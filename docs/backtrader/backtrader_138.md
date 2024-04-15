# 黄金 vs. 标普 500

> 原文：[`www.backtrader.com/blog/posts/2016-12-13-gold-vs-sp500/gold-vs-sp500/`](https://www.backtrader.com/blog/posts/2016-12-13-gold-vs-sp500/gold-vs-sp500/)

有时候了解*backtrader*的使用情况可以帮助理解人们可能正在寻找和使用平台的内容。

参考：

+   [`estrategiastrading.com/oro-bolsa-estadistica-con-python/`](https://estrategiastrading.com/oro-bolsa-estadistica-con-python/)

这是一篇（西班牙语）分析两个 ETF：`GLD` vs `SPY`（实际上是*黄金* vs *标普 500*）的文章。

不去深入翻译，让我们集中精力在*backtrader*的重要内容上：

+   添加一个*相关性*指标。出于这个目的，选择了 `PearsonR`。

    为了完成这一壮举，而不是从头开始编写代码，我们展示了如何使用`scipy`函数来完成。代码如下：

    ```py
    `class PearsonR(bt.ind.PeriodN):
        _mindatas = 2  # hint to the platform

        lines = ('correlation',)
        params = (('period', 20),)

        def next(self):
            c, p, = scipy.stats.pearsonr(self.data0.get(size=self.p.period),
                                         self.data1.get(size=self.p.period))

            self.lines.correlation[0] = c` 
    ```

+   添加滚动对数收益

    该平台已经有了一个带有对数收益的分析器，但没有*滚动*。

    添加了分析器 `LogReturnsRolling`，它接受一个 `timeframe` 参数（和 `compression`）来使用与数据不同的时间框架（如果需要的话）。

    与此同时，为了可视化（内部使用*分析器*），添加了一个 `LogReturns` 观察者。

+   允许在数据绘图中加入数据（轻松）。就像这样：

    ```py
     `# Data feeds
        data0 = YahooData(dataname=args.data0, **kwargs)
        # cerebro.adddata(data0)
        cerebro.resampledata(data0, timeframe=bt.TimeFrame.Weeks)

        data1 = YahooData(dataname=args.data1, **kwargs)
        # cerebro.adddata(data1)
        cerebro.resampledata(data1, timeframe=bt.TimeFrame.Weeks)
        data1.plotinfo.plotmaster = data0` 
    ```

    只需使用 `plotmaster=data0` 就可以在 `data0` 上绘制 `data1`。

从平台的最开始就支持在自己的轴上和彼此之间绘制移动平均线。

*分析器*和*观察者*也已添加到平台上，并且提供了与博客文章中相同默认设置的示例。

运行示例：

```py
$ ./gold-vs-sp500.py --cerebro stdstats=False --plot volume=False
```

注意

`stdstats=False` 和 `volume=False` 用于通过删除一些常见的内容，如`CashValue`观察者和*volume*子图，来减少图表中的混乱。

生成一个图表，几乎模拟了文章中的大部分输出。

![image](img/07fd7b9cbdb70d8e35cf1d9bf6e40005.png)

不包括在内：

+   创建收益分布图表。

    它们无法适应基于*datetime*的 x 轴的图表。

但是拥有这些分布可能是有益的。

## 示例用法

```py
$ ./gold-vs-sp500.py --help
usage: gold-vs-sp500.py [-h] [--data0 TICKER] [--data1 TICKER] [--offline]
                        [--fromdate FROMDATE] [--todate TODATE]
                        [--cerebro kwargs] [--broker kwargs] [--sizer kwargs]
                        [--strat kwargs] [--plot [kwargs]] [--myobserver]

Gold vs SP500 from https://estrategiastrading.com/oro-bolsa-estadistica-con-
python/

optional arguments:
  -h, --help           show this help message and exit
  --data0 TICKER       Yahoo ticker to download (default: SPY)
  --data1 TICKER       Yahoo ticker to download (default: GLD)
  --offline            Use the offline files (default: False)
  --fromdate FROMDATE  Date[time] in YYYY-MM-DD[THH:MM:SS] format (default:
                       2005-01-01)
  --todate TODATE      Date[time] in YYYY-MM-DD[THH:MM:SS] format (default:
                       2016-01-01)
  --cerebro kwargs     kwargs in key=value format (default: )
  --broker kwargs      kwargs in key=value format (default: )
  --sizer kwargs       kwargs in key=value format (default: )
  --strat kwargs       kwargs in key=value format (default: )
  --plot [kwargs]      kwargs in key=value format (default: )
```

## 示例代码

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

# Reference
# https://estrategiastrading.com/oro-bolsa-estadistica-con-python/

import argparse
import datetime

import scipy.stats

import backtrader as bt

class PearsonR(bt.ind.PeriodN):
    _mindatas = 2  # hint to the platform

    lines = ('correlation',)
    params = (('period', 20),)

    def next(self):
        c, p, = scipy.stats.pearsonr(self.data0.get(size=self.p.period),
                                     self.data1.get(size=self.p.period))

        self.lines.correlation[0] = c

class MACrossOver(bt.Strategy):
    params = (
        ('ma', bt.ind.MovAv.SMA),
        ('pd1', 20),
        ('pd2', 20),
    )

    def __init__(self):
        ma1 = self.p.ma(self.data0, period=self.p.pd1, subplot=True)
        self.p.ma(self.data1, period=self.p.pd2, plotmaster=ma1)
        PearsonR(self.data0, self.data1)

def runstrat(args=None):
    args = parse_args(args)

    cerebro = bt.Cerebro()

    # Data feed kwargs
    kwargs = dict()

    # Parse from/to-date
    dtfmt, tmfmt = '%Y-%m-%d', 'T%H:%M:%S'
    for a, d in ((getattr(args, x), x) for x in ['fromdate', 'todate']):
        if a:
            strpfmt = dtfmt + tmfmt * ('T' in a)
            kwargs[d] = datetime.datetime.strptime(a, strpfmt)

    if not args.offline:
        YahooData = bt.feeds.YahooFinanceData
    else:
        YahooData = bt.feeds.YahooFinanceCSVData

    # Data feeds
    data0 = YahooData(dataname=args.data0, **kwargs)
    # cerebro.adddata(data0)
    cerebro.resampledata(data0, timeframe=bt.TimeFrame.Weeks)

    data1 = YahooData(dataname=args.data1, **kwargs)
    # cerebro.adddata(data1)
    cerebro.resampledata(data1, timeframe=bt.TimeFrame.Weeks)
    data1.plotinfo.plotmaster = data0

    # Broker
    kwargs = eval('dict(' + args.broker + ')')
    cerebro.broker = bt.brokers.BackBroker(**kwargs)

    # Sizer
    kwargs = eval('dict(' + args.sizer + ')')
    cerebro.addsizer(bt.sizers.FixedSize, **kwargs)

    # Strategy
    if True:
        kwargs = eval('dict(' + args.strat + ')')
        cerebro.addstrategy(MACrossOver, **kwargs)

    cerebro.addobserver(bt.observers.LogReturns2,
                        timeframe=bt.TimeFrame.Weeks,
                        compression=20)

    # Execute
    cerebro.run(**(eval('dict(' + args.cerebro + ')')))

    if args.plot:  # Plot if requested to
        cerebro.plot(**(eval('dict(' + args.plot + ')')))

def parse_args(pargs=None):

    parser = argparse.ArgumentParser(
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
        description=(
            'Gold vs SP500 from '
            'https://estrategiastrading.com/oro-bolsa-estadistica-con-python/')
    )

    parser.add_argument('--data0', required=False, default='SPY',
                        metavar='TICKER', help='Yahoo ticker to download')

    parser.add_argument('--data1', required=False, default='GLD',
                        metavar='TICKER', help='Yahoo ticker to download')

    parser.add_argument('--offline', required=False, action='store_true',
                        help='Use the offline files')

    # Defaults for dates
    parser.add_argument('--fromdate', required=False, default='2005-01-01',
                        help='Date[time] in YYYY-MM-DD[THH:MM:SS] format')

    parser.add_argument('--todate', required=False, default='2016-01-01',
                        help='Date[time] in YYYY-MM-DD[THH:MM:SS] format')

    parser.add_argument('--cerebro', required=False, default='',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--broker', required=False, default='',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--sizer', required=False, default='',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--strat', required=False, default='',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--plot', required=False, default='',
                        nargs='?', const='{}',
                        metavar='kwargs', help='kwargs in key=value format')

    return parser.parse_args(pargs)

if __name__ == '__main__':
    runstrat()
```
