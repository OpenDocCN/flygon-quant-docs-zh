# 数据过滤器

> 原文：[`www.backtrader.com/blog/posts/2015-11-21-data-filters/data-filling-filtering/`](https://www.backtrader.com/blog/posts/2015-11-21-data-filters/data-filling-filtering/)

一段时间前，票证＃23 让我考虑在该票证的上下文中进行的讨论的潜在改进。

+   对票证＃23：[`github.com/mementum/backtrader/issues/23`](https://github.com/mementum/backtrader/issues/23)

在票证中，我添加了一个`DataFilter`类，但这太过复杂。实际上，这让人想起了内置了相同功能的`DataResampler`和`DataReplayer`中构建的复杂性。

因此，自几个版本以来，`backtrader`支持向数据源添加一个`filter`（如果愿意，可以称之为`processor`）。重新采样和重播使用该功能进行了内部重新实现，一切似乎变得不那么复杂（尽管仍然是）

## 过滤器在起作用

鉴于现有的数据源，您可以使用数据源的`addfilter`方法：

```py
data = MyDataFeed(name=myname)

data.addfilter(filter, *args, **kwargs)
```

显然，`filter`必须符合给定的接口，即：

+   一个接受此签名的可调用对象：

    ```py
    callable(data, *args, **kwargs)` 
    ```

或者

+   一个可以实例化和调用的类

    +   在实例化期间，**init**方法必须支持以下签名：

    ```py
    def __init__(self, data, *args, **kwargs)` 
    ```

    +   这个对象的**call**和 last 方法如下：

    ```py
    def __call__(self, data)

    def last(self, data)` 
    ```

可调用/实例将被调用以处理数据源产生的每个数据。

## 对票证＃23 的更好解决方案

那张票想要：

+   一个基于白天的相对成交量指标

+   白天数据可能丢失

+   预/后市场数据可能到达

实施几个过滤器可以缓解回测环境的情况。

### 过滤预/后市场数据

以下过滤器（已经在`backtrader`中可用）挺身而出：

```py
class SessionFilter(with_metaclass(metabase.MetaParams, object)):
    '''
    This class can be applied to a data source as a filter and will filter out
    intraday bars which fall outside of the regular session times (ie: pre/post
    market data)

    This is a "non-simple" filter and must manage the stack of the data (passed
    during init and __call__)

    It needs no "last" method because it has nothing to deliver
    '''
    def __init__(self, data):
        pass

    def __call__(self, data):
        '''
        Return Values:

          - False: data stream was not touched
          - True: data stream was manipulated (bar outside of session times and
          - removed)
        '''
        if data.sessionstart <= data.datetime.tm(0) <= data.sessionend:
            # Both ends of the comparison are in the session
            return False  # say the stream is untouched

        # bar outside of the regular session times
        data.backwards()  # remove bar from data stack
        return True  # signal the data was manipulated
```

该过滤器使用数据中嵌入的会话开始/结束时间来过滤条形图

+   如果新数据的日期时间在会话时间内，则返回`False`以指示数据未受影响

+   如果日期时间超出范围，则数据源将向后发送，有效地擦除最后生成的数据。并返回`True`以指示数据流已被操作。

注意

调用`data.backwards()`可能/可能是低级的，过滤器应该具有处理数据流内部的 API

脚本末尾的示例代码可以在有或无过滤的情况下运行。第一次运行是 100%未经过滤且未指定会话时间：

```py
$ ./data-filler.py --writer --wrcsv
```

查看第 1 天的开始和结束：

```py
===============================================================================
Id,2006-01-02-volume-min-001,len,datetime,open,high,low,close,volume,openinterest,Strategy,len
1,2006-01-02-volume-min-001,1,2006-01-02 09:01:00,3602.0,3603.0,3597.0,3599.0,5699.0,0.0,Strategy,1
2,2006-01-02-volume-min-001,2,2006-01-02 09:02:00,3600.0,3601.0,3598.0,3599.0,894.0,0.0,Strategy,2
...
...
581,2006-01-02-volume-min-001,581,2006-01-02 19:59:00,3619.0,3619.0,3619.0,3619.0,1.0,0.0,Strategy,581
582,2006-01-02-volume-min-001,582,2006-01-02 20:00:00,3618.0,3618.0,3617.0,3618.0,242.0,0.0,Strategy,582
583,2006-01-02-volume-min-001,583,2006-01-02 20:01:00,3618.0,3618.0,3617.0,3617.0,15.0,0.0,Strategy,583
584,2006-01-02-volume-min-001,584,2006-01-02 20:04:00,3617.0,3617.0,3617.0,3617.0,107.0,0.0,Strategy,584
585,2006-01-02-volume-min-001,585,2006-01-03 09:01:00,3623.0,3625.0,3622.0,3624.0,4026.0,0.0,Strategy,585
...
```

2006 年 1 月 2 日从 09:01:00 到 20:04:00 进行会话运行。

现在使用`SessionFilter`运行，并告诉脚本使用 09:30 和 17:30 作为会话的开始/结束时间：

```py
$ ./data-filler.py --writer --wrcsv --tstart 09:30 --tend 17:30 --filter

===============================================================================
Id,2006-01-02-volume-min-001,len,datetime,open,high,low,close,volume,openinterest,Strategy,len
1,2006-01-02-volume-min-001,1,2006-01-02 09:30:00,3604.0,3605.0,3603.0,3604.0,546.0,0.0,Strategy,1
2,2006-01-02-volume-min-001,2,2006-01-02 09:31:00,3604.0,3606.0,3604.0,3606.0,438.0,0.0,Strategy,2
...
...
445,2006-01-02-volume-min-001,445,2006-01-02 17:29:00,3621.0,3621.0,3620.0,3620.0,866.0,0.0,Strategy,445
446,2006-01-02-volume-min-001,446,2006-01-02 17:30:00,3620.0,3621.0,3619.0,3621.0,1670.0,0.0,Strategy,446
447,2006-01-02-volume-min-001,447,2006-01-03 09:30:00,3637.0,3638.0,3635.0,3636.0,1458.0,0.0,Strategy,447
...
```

数据输出现在从 09:30 开始，到 17:30 结束。已经过滤掉预/后市场数据。

### 填充缺失数据

对输出的深入检查显示如下：

```py
...
61,2006-01-02-volume-min-001,61,2006-01-02 10:30:00,3613.0,3614.0,3613.0,3614.0,112.0,0.0,Strategy,61
62,2006-01-02-volume-min-001,62,2006-01-02 10:31:00,3614.0,3614.0,3614.0,3614.0,183.0,0.0,Strategy,62
63,2006-01-02-volume-min-001,63,2006-01-02 10:34:00,3614.0,3614.0,3614.0,3614.0,841.0,0.0,Strategy,63
64,2006-01-02-volume-min-001,64,2006-01-02 10:35:00,3614.0,3614.0,3614.0,3614.0,17.0,0.0,Strategy,64
...
```

缺少分钟 10:32 和 10:33 的数据。作为一年中的第 1 个交易日，可能根本没有进行交易。或者数据源可能未能捕获到这些数据。

为了解决票号 #23 的问题，并能够将给定分钟的成交量与前一天的同一分钟进行比较，我们将填补缺失的数据。

`backtrader`中已经存在一个`SessionFiller`，按预期填补了缺失的数据。代码很长，比过滤器的复杂性更多（请参阅末尾的完整实现），但让我们看看类/参数定义：

```py
class SessionFiller(with_metaclass(metabase.MetaParams, object)):
    '''
    Bar Filler for a Data Source inside the declared session start/end times.

    The fill bars are constructed using the declared Data Source ``timeframe``
    and ``compression`` (used to calculate the intervening missing times)

    Params:

      - fill_price (def: None):

        If None is passed, the closing price of the previous bar will be
        used. To end up with a bar which for example takes time but it is not
        displayed in a plot ... use float('Nan')

      - fill_vol (def: float('NaN')):

        Value to use to fill the missing volume

      - fill_oi (def: float('NaN')):

        Value to use to fill the missing Open Interest

      - skip_first_fill (def: True):

        Upon seeing the 1st valid bar do not fill from the sessionstart up to
        that bar
    '''
    params = (('fill_price', None),
              ('fill_vol', float('NaN')),
              ('fill_oi', float('NaN')),
              ('skip_first_fill', True))
```

示例脚本现在可以过滤和填充数据了：

```py
./data-filler.py --writer --wrcsv --tstart 09:30 --tend 17:30 --filter --filler

...
62,2006-01-02-volume-min-001,62,2006-01-02 10:31:00,3614.0,3614.0,3614.0,3614.0,183.0,0.0,Strategy,62
63,2006-01-02-volume-min-001,63,2006-01-02 10:32:00,3614.0,3614.0,3614.0,3614.0,0.0,,Strategy,63
64,2006-01-02-volume-min-001,64,2006-01-02 10:33:00,3614.0,3614.0,3614.0,3614.0,0.0,,Strategy,64
65,2006-01-02-volume-min-001,65,2006-01-02 10:34:00,3614.0,3614.0,3614.0,3614.0,841.0,0.0,Strategy,65
...
```

分钟 10:32 和 10:33 已存在。脚本使用最后已知的“收盘”价格填充价格值，并将成交量和持仓量字段设置为 0。脚本接受一个`--fvol`参数，将成交量设置为任何值（包括'NaN'）

### 完成票号 #23

使用`SessionFilter`和`SessionFiller`完成了以下工作：

+   未提供盘前/盘后市数据

+   不存在数据（在给定时间范围内）丢失

现在，在实施`RelativeVolume`指标的 Ticket 23 中讨论的“同步”不再需要，因为所有日期的条形图数量完全相同（在示例中从 09:30 到 17:30 的所有分钟都包括在内）

请记住，默认情况下将缺失的成交量设置为`0`，可以开发一个简单的`RelativeVolume`指标：

```py
class RelativeVolume(bt.Indicator):
    csv = True  # show up in csv output (default for indicators is False)

    lines = ('relvol',)
    params = (
        ('period', 20),
        ('volisnan', True),
    )

    def __init__(self):
        if self.p.volisnan:
            # if missing volume will be NaN, do a simple division
            # the end result for missing volumes will also be NaN
            relvol = self.data.volume(-self.p.period) / self.data.volume
        else:
            # Else do a controlled Div with a built-in function
            relvol = bt.DivByZero(
                self.data.volume(-self.p.period),
                self.data.volume,
                zero=0.0)

        self.lines.relvol = relvol
```

使用`backtrader`中的内置辅助功能，足够聪明，可以避免除零错误。

在下次脚本调用中将所有部分组合起来：

```py
./data-filler.py --writer --wrcsv --tstart 09:30 --tend 17:30 --filter --filler --relvol

===============================================================================
Id,2006-01-02-volume-min-001,len,datetime,open,high,low,close,volume,openinterest,Strategy,len,RelativeVolume,len,relvol
1,2006-01-02-volume-min-001,1,2006-01-02 09:30:00,3604.0,3605.0,3603.0,3604.0,546.0,0.0,Strategy,1,RelativeVolume,1,
2,2006-01-02-volume-min-001,2,2006-01-02 09:31:00,3604.0,3606.0,3604.0,3606.0,438.0,0.0,Strategy,2,RelativeVolume,2,
...
```

`RelativeVolume`指标在第 1 个条形图期间产生了预期外的输出。周期在脚本中计算为：(17:30 - 09:30 * 60) + 1。让我们直接看看相对成交量在第二天的 10:32 和 10:33 是如何看待的，考虑到第 1 天，成交量值被填充为`0`：

```py
...
543,2006-01-02-volume-min-001,543,2006-01-03 10:31:00,3648.0,3648.0,3647.0,3648.0,56.0,0.0,Strategy,543,RelativeVolume,543,3.26785714286
544,2006-01-02-volume-min-001,544,2006-01-03 10:32:00,3647.0,3648.0,3647.0,3647.0,313.0,0.0,Strategy,544,RelativeVolume,544,0.0
545,2006-01-02-volume-min-001,545,2006-01-03 10:33:00,3647.0,3647.0,3647.0,3647.0,135.0,0.0,Strategy,545,RelativeVolume,545,0.0
546,2006-01-02-volume-min-001,546,2006-01-03 10:34:00,3648.0,3648.0,3647.0,3648.0,171.0,0.0,Strategy,546,RelativeVolume,546,4.91812865497
...
```

如预期那样，它被设置为`0`。

### 结论

数据源中的`filter`机制打开了完全操纵数据流的可能性。请谨慎使用。

### 脚本代码和用法

在`backtrader`的源码中提供了示例：

```py
usage: data-filler.py [-h] [--data DATA] [--filter] [--filler] [--fvol FVOL]
                      [--tstart TSTART] [--tend TEND] [--relvol]
                      [--fromdate FROMDATE] [--todate TODATE] [--writer]
                      [--wrcsv] [--plot] [--numfigs NUMFIGS]

DataFilter/DataFiller Sample

optional arguments:
  -h, --help            show this help message and exit
  --data DATA, -d DATA  data to add to the system
  --filter, -ft         Filter using session start/end times
  --filler, -fl         Fill missing bars inside start/end times
  --fvol FVOL           Use as fill volume for missing bar (def: 0.0)
  --tstart TSTART, -ts TSTART
                        Start time for the Session Filter (HH:MM)
  --tend TEND, -te TEND
                        End time for the Session Filter (HH:MM)
  --relvol, -rv         Add relative volume indicator
  --fromdate FROMDATE, -f FROMDATE
                        Starting date in YYYY-MM-DD format
  --todate TODATE, -t TODATE
                        Starting date in YYYY-MM-DD format
  --writer, -w          Add a writer to cerebro
  --wrcsv, -wc          Enable CSV Output in the writer
  --plot, -p            Plot the read data
  --numfigs NUMFIGS, -n NUMFIGS
                        Plot using numfigs figures
```

代码：

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
import datetime
import math

# The above could be sent to an independent module
import backtrader as bt
import backtrader.feeds as btfeeds
import backtrader.utils.flushfile
import backtrader.filters as btfilters

from relativevolume import RelativeVolume

def runstrategy():
    args = parse_args()

    # Create a cerebro
    cerebro = bt.Cerebro()

    # Get the dates from the args
    fromdate = datetime.datetime.strptime(args.fromdate, '%Y-%m-%d')
    todate = datetime.datetime.strptime(args.todate, '%Y-%m-%d')

    # Get the session times to pass them to the indicator
    # datetime.time has no strptime ...
    dtstart = datetime.datetime.strptime(args.tstart, '%H:%M')
    dtend = datetime.datetime.strptime(args.tend, '%H:%M')

    # Create the 1st data
    data = btfeeds.BacktraderCSVData(
        dataname=args.data,
        fromdate=fromdate,
        todate=todate,
        timeframe=bt.TimeFrame.Minutes,
        compression=1,
        sessionstart=dtstart,  # internally just the "time" part will be used
        sessionend=dtend,  # internally just the "time" part will be used
    )

    if args.filter:
        data.addfilter(btfilters.SessionFilter)

    if args.filler:
        data.addfilter(btfilters.SessionFiller, fill_vol=args.fvol)

    # Add the data to cerebro
    cerebro.adddata(data)

    if args.relvol:
        # Calculate backward period - tend tstart are in same day
        # + 1 to include last moment of the interval dstart <-> dtend
        td = ((dtend - dtstart).seconds // 60) + 1
        cerebro.addindicator(RelativeVolume,
                             period=td,
                             volisnan=math.isnan(args.fvol))

    # Add an empty strategy
    cerebro.addstrategy(bt.Strategy)

    # Add a writer with CSV
    if args.writer:
        cerebro.addwriter(bt.WriterFile, csv=args.wrcsv)

    # And run it - no trading - disable stdstats
    cerebro.run(stdstats=False)

    # Plot if requested
    if args.plot:
        cerebro.plot(numfigs=args.numfigs, volume=True)

def parse_args():
    parser = argparse.ArgumentParser(
        description='DataFilter/DataFiller Sample')

    parser.add_argument('--data', '-d',
                        default='../../datas/2006-01-02-volume-min-001.txt',
                        help='data to add to the system')

    parser.add_argument('--filter', '-ft', action='store_true',
                        help='Filter using session start/end times')

    parser.add_argument('--filler', '-fl', action='store_true',
                        help='Fill missing bars inside start/end times')

    parser.add_argument('--fvol', required=False, default=0.0,
                        type=float,
                        help='Use as fill volume for missing bar (def: 0.0)')

    parser.add_argument('--tstart', '-ts',
                        # default='09:14:59',
                        # help='Start time for the Session Filter (%H:%M:%S)')
                        default='09:15',
                        help='Start time for the Session Filter (HH:MM)')

    parser.add_argument('--tend', '-te',
                        # default='17:15:59',
                        # help='End time for the Session Filter (%H:%M:%S)')
                        default='17:15',
                        help='End time for the Session Filter (HH:MM)')

    parser.add_argument('--relvol', '-rv', action='store_true',
                        help='Add relative volume indicator')

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
    runstrategy()
```

### SessionFiller

来自`backtrader`源码：

```py
class SessionFiller(with_metaclass(metabase.MetaParams, object)):
    '''
    Bar Filler for a Data Source inside the declared session start/end times.

    The fill bars are constructed using the declared Data Source ``timeframe``
    and ``compression`` (used to calculate the intervening missing times)

    Params:

      - fill_price (def: None):

        If None is passed, the closing price of the previous bar will be
        used. To end up with a bar which for example takes time but it is not
        displayed in a plot ... use float('Nan')

      - fill_vol (def: float('NaN')):

        Value to use to fill the missing volume

      - fill_oi (def: float('NaN')):

        Value to use to fill the missing Open Interest

      - skip_first_fill (def: True):

        Upon seeing the 1st valid bar do not fill from the sessionstart up to
        that bar
    '''
    params = (('fill_price', None),
              ('fill_vol', float('NaN')),
              ('fill_oi', float('NaN')),
              ('skip_first_fill', True))

    # Minimum delta unit in between bars
    _tdeltas = {
        TimeFrame.Minutes: datetime.timedelta(seconds=60),
        TimeFrame.Seconds: datetime.timedelta(seconds=1),
        TimeFrame.MicroSeconds: datetime.timedelta(microseconds=1),
    }

    def __init__(self, data):
        # Calculate and save timedelta for timeframe
        self._tdunit = self._tdeltas[data._timeframe] * data._compression

        self.seenbar = False  # control if at least one bar has been seen
        self.sessend = MAXDATE  # maxdate is the control for bar in session

    def __call__(self, data):
        '''
        Params:
          - data: the data source to filter/process

        Returns:
          - False (always) because this filter does not remove bars from the
        stream

        The logic (starting with a session end control flag of MAXDATE)

          - If new bar is over session end (never true for 1st bar)

            Fill up to session end. Reset sessionend to MAXDATE & fall through

          - If session end is flagged as MAXDATE

            Recalculate session limits and check whether the bar is within them

            if so, fill up and record the last seen tim

          - Else ... the incoming bar is in the session, fill up to it
        '''
        # Get time of current (from data source) bar
        dtime_cur = data.datetime.datetime()

        if dtime_cur > self.sessend:
            # bar over session end - fill up and invalidate
            self._fillbars(data, self.dtime_prev, self.sessend + self._tdunit)
            self.sessend = MAXDATE

        # Fall through from previous check ... the bar which is over the
        # session could already be in a new session and within the limits
        if self.sessend == MAXDATE:
            # No bar seen yet or one went over previous session limit
            sessstart = data.datetime.tm2datetime(data.sessionstart)
            self.sessend = sessend = data.datetime.tm2datetime(data.sessionend)

            if sessstart <= dtime_cur <= sessend:
                # 1st bar from session in the session - fill from session start
                if self.seenbar or not self.p.skip_first_fill:
                    self._fillbars(data, sessstart - self._tdunit, dtime_cur)

            self.seenbar = True
            self.dtime_prev = dtime_cur

        else:
            # Seen a previous bar and this is in the session - fill up to it
            self._fillbars(data, self.dtime_prev, dtime_cur)
            self.dtime_prev = dtime_cur

        return False

    def _fillbars(self, data, time_start, time_end, forcedirty=False):
        '''
        Fills one by one bars as needed from time_start to time_end

        Invalidates the control dtime_prev if requested
        '''
        # Control flag - bars added to the stack
        dirty = False

        time_start += self._tdunit
        while time_start < time_end:
            dirty = self._fillbar(data, time_start)
            time_start += self._tdunit

        if dirty or forcedirty:
            data._save2stack(erase=True)

    def _fillbar(self, data, dtime):
        # Prepare an array of the needed size
        bar = [float('Nan')] * data.size()

        # Fill datetime
        bar[data.DateTime] = date2num(dtime)

        # Fill the prices
        price = self.p.fill_price or data.close[-1]
        for pricetype in [data.Open, data.High, data.Low, data.Close]:
            bar[pricetype] = price

        # Fill volume and open interest
        bar[data.Volume] = self.p.fill_vol
        bar[data.OpenInterest] = self.p.fill_oi

        # Fill extra lines the data feed may have defined beyond DateTime
        for i in range(data.DateTime + 1, data.size()):
            bar[i] = data.lines[i][0]

        # Add tot he stack of bars to save
        data._add2stack(bar)

        return True
```
