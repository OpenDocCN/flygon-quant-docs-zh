# 条形图同步

> [`www.backtrader.com/blog/posts/2015-10-04-bar-synchronization/bar-synchronization/`](https://www.backtrader.com/blog/posts/2015-10-04-bar-synchronization/bar-synchronization/)

文献和/或行业中缺乏标准公式并不是问题，因为问题实际上可以总结为：

+   条形图同步

[工单 #23](https://github.com/mementum/backtrader/issues/23) 提出了一些关于`backtrader`是否可以计算**相对成交量**指标的疑问。

请求者需要比较特定时刻的成交量与前一个交易日相同时刻的成交量。包括：

+   一些未知长度的预市数据

有这样的要求会使大多数指标构建的基本原则无效：

+   有一个固定的用于向后查看的周期

此外，考虑到比较是在盘内进行的，还必须考虑其他因素：

+   一些“盘内”瞬间可能会缺失（无论是分钟还是秒）

    数据源缺失每日条形图的可能性很小，但缺失分钟或秒条形图并不罕见。

    主要原因是可能根本没有进行任何交易。或者在交易所谈判中可能出现问题，实际上阻止了条形图被记录。

考虑到前述所有要点，对指标开发得出一些结论：

+   这里的**周期**不是指一个周期，而是一个缓冲区，以确保有足够的条形图使指标尽快生效

+   一些条形图可能会缺失

+   主要问题是同步

幸运的是，有一个关键可以帮助解决同步问题：

+   比较的条形图是“盘内”的，因此计算已经看到的天数和给定时刻已经看到的“条形图”数量可以实现同步

前一天的值保存在字典中，因为如前所述的“向后查看”期限是未知的。

一些早期的想法可以被抛弃，比如实现一个`DataFilter`数据源，因为这实际上会使数据源与系统的其他部分不同步，通过删除预市数据。同步问题也会存在。

探索的一个想法是创建一个`DataFiller`，通过使用最后的收盘价填补缺失的分钟/秒，并将成交量设置为 0。

通过实践发现，有必要在`backtrader`中识别一些额外需求，比如一个**time2num**函数（日期 2 数字和数字 2 日期系列的补充），以及将成为**lines**的额外方法：

+   从浮点表示的日期中提取“日”和“时间”部分

    被称为“dt”和“tm”

与此同时，`RelativeVolumeByBar` 指标的代码如下所示。在指标内部进行“period”/“buffer” 计算不是首选模式，但在这种情况下它能够达到目的。

```py
`from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import collections
import datetime
import math

import backtrader as bt

def time2num(tm):
    """
    Convert :mod:`time` to the to the preserving hours, minutes, seconds
    and microseconds.  Return value is a :func:`float`.
    """
    HOURS_PER_DAY = 24.0
    MINUTES_PER_HOUR = 60.0
    SECONDS_PER_MINUTE = 60.0
    MUSECONDS_PER_SECOND = 1e6
    MINUTES_PER_DAY = MINUTES_PER_HOUR * HOURS_PER_DAY
    SECONDS_PER_DAY = SECONDS_PER_MINUTE * MINUTES_PER_DAY
    MUSECONDS_PER_DAY = MUSECONDS_PER_SECOND * SECONDS_PER_DAY

    tm_num = (tm.hour / HOURS_PER_DAY +
              tm.minute / MINUTES_PER_DAY +
              tm.second / SECONDS_PER_DAY +
              tm.microsecond / MUSECONDS_PER_DAY)

    return tm_num

def dtime_dt(dt):
    return math.trunc(dt)

def dtime_tm(dt):
    return math.modf(dt)[0]

class RelativeVolumeByBar(bt.Indicator):
    alias = ('RVBB',)
    lines = ('rvbb',)

    params = (
        ('prestart', datetime.time(8, 00)),
        ('start', datetime.time(9, 10)),
        ('end', datetime.time(17, 15)),
    )

    def _plotlabel(self):
        plabels = []
        for name, value in self.params._getitems():
            plabels.append('%s: %s' % (name, value.strftime('%H:%M')))

        return plabels

    def __init__(self):
        # Inform the platform about the minimum period needs
        minbuffer = self._calcbuffer()
        self.addminperiod(minbuffer)

        # Structures/variable to keep synchronization
        self.pvol = dict()
        self.vcount = collections.defaultdict(int)

        self.days = 0
        self.dtlast = 0

        # Keep the start/end times in numeric format for comparison
        self.start = time2num(self.p.start)
        self.end = time2num(self.p.end)

        # Done after calc to ensure coop inheritance and composition work
        super(RelativeVolumeByBar, self).__init__()

    def _barisvalid(self, tm):
        return self.start <= tm <= self.end

    def _daycount(self):
        dt = dtime_dt(self.data.datetime[0])
        if dt > self.dtlast:
            self.days += 1
            self.dtlast = dt

    def prenext(self):
        self._daycount()

        tm = dtime_tm(self.data.datetime[0])
        if self._barisvalid(tm):
            self.pvol[tm] = self.data.volume[0]
            self.vcount[tm] += 1

    def next(self):
        self._daycount()

        tm = dtime_tm(self.data.datetime[0])
        if not self._barisvalid(tm):
            return

        # Record the "minute/second" of this day has been seen
        self.vcount[tm] += 1

        # Get the bar's volume
        vol = self.data.volume[0]

        # If number of days is right, we saw the same "minute/second" last day
        if self.vcount[tm] == self.days:
            self.lines.rvbb[0] = vol / self.pvol[tm]

        # Synchronize the days and volume count for next cycle
        self.vcount[tm] = self.days

        # Record the volume for this bar for next cycle
        self.pvol[tm] = vol

    def _calcbuffer(self):
        # Period calculation
        minend = self.p.end.hour * 60 + self.p.end.minute
        # minstart = session_start.hour * 60 + session_start.minute
        # use prestart to account for market_data
        minstart = self.p.prestart.hour * 60 + self.p.prestart.minute

        minbuffer = minend - minstart

        tframe = self.data._timeframe
        tcomp = self.data._compression

        if tframe == bt.TimeFrame.Seconds:
            minbuffer = (minperiod * 60)

        minbuffer = (minbuffer // tcomp) + tcomp

        return minbuffer` 
```

通过脚本调用，可以如下使用：

```py
`$ ./relative-volume.py --help
usage: relative-volume.py [-h] [--data DATA] [--prestart PRESTART]
                          [--start START] [--end END] [--fromdate FROMDATE]
                          [--todate TODATE] [--writer] [--wrcsv] [--plot]
                          [--numfigs NUMFIGS]

MultiData Strategy

optional arguments:
  -h, --help            show this help message and exit
  --data DATA, -d DATA  data to add to the system
  --prestart PRESTART   Start time for the Session Filter
  --start START         Start time for the Session Filter
  --end END, -te END    End time for the Session Filter
  --fromdate FROMDATE, -f FROMDATE
                        Starting date in YYYY-MM-DD format
  --todate TODATE, -t TODATE
                        Starting date in YYYY-MM-DD format
  --writer, -w          Add a writer to cerebro
  --wrcsv, -wc          Enable CSV Output in the writer
  --plot, -p            Plot the read data
  --numfigs NUMFIGS, -n NUMFIGS
                        Plot using numfigs figures` 
```

测试调用：

```py
`$ ./relative-volume.py --plot` 
```

生成此图表：

![图片](img/1ec92237467276024a1fc10e8e31664c.png)

脚本代码。

```py
`from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
import datetime

# The above could be sent to an independent module
import backtrader as bt
import backtrader.feeds as btfeeds

from relvolbybar import RelativeVolumeByBar

def runstrategy():
    args = parse_args()

    # Create a cerebro
    cerebro = bt.Cerebro()

    # Get the dates from the args
    fromdate = datetime.datetime.strptime(args.fromdate, '%Y-%m-%d')
    todate = datetime.datetime.strptime(args.todate, '%Y-%m-%d')

    # Create the 1st data
    data = btfeeds.VChartCSVData(
        dataname=args.data,
        fromdate=fromdate,
        todate=todate,
        )

    # Add the 1st data to cerebro
    cerebro.adddata(data)

    # Add an empty strategy
    cerebro.addstrategy(bt.Strategy)

    # Get the session times to pass them to the indicator
    prestart = datetime.datetime.strptime(args.prestart, '%H:%M')
    start = datetime.datetime.strptime(args.start, '%H:%M')
    end = datetime.datetime.strptime(args.end, '%H:%M')

    # Add the Relative volume indicator
    cerebro.addindicator(RelativeVolumeByBar,
                         prestart=prestart, start=start, end=end)

    # Add a writer with CSV
    if args.writer:
        cerebro.addwriter(bt.WriterFile, csv=args.wrcsv)

    # And run it
    cerebro.run(stdstats=False)

    # Plot if requested
    if args.plot:
        cerebro.plot(numfigs=args.numfigs, volume=True)

def parse_args():
    parser = argparse.ArgumentParser(description='MultiData Strategy')

    parser.add_argument('--data', '-d',
                        default='../../datas/2006-01-02-volume-min-001.txt',
                        help='data to add to the system')

    parser.add_argument('--prestart',
                        default='08:00',
                        help='Start time for the Session Filter')

    parser.add_argument('--start',
                        default='09:15',
                        help='Start time for the Session Filter')

    parser.add_argument('--end', '-te',
                        default='17:15',
                        help='End time for the Session Filter')

    parser.add_argument('--fromdate', '-f',
                        default='2006-01-01',
                        help='Starting date in YYYY-MM-DD format')

    parser.add_argument('--todate', '-t',
                        default='2006-12-31',
                        help='Starting date in YYYY-MM-DD format')

    parser.add_argument('--writer', '-w', action='store_true',
                        help='Add a writer to cerebro')

    parser.add_argument('--wrcsv', '-wc', action='store_true',
                        help='Enable CSV Output in the writer')

    parser.add_argument('--plot', '-p', action='store_true',
                        help='Plot the read data')

    parser.add_argument('--numfigs', '-n', default=1,
                        help='Plot using numfigs figures')

    return parser.parse_args()

if __name__ == '__main__':
    runstrategy()` 
```
