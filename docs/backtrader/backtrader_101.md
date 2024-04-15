# 关于回测性能和核心内存执行

> 原文：[`www.backtrader.com/blog/2019-10-25-on-backtesting-performance-and-out-of-memory/on-backtesting-performance-and-out-of-memory/`](https://www.backtrader.com/blog/2019-10-25-on-backtesting-performance-and-out-of-memory/on-backtesting-performance-and-out-of-memory/)

最近有两个[`redit.com/r/algotrading`](https://redit.com/r/algotrading)帖子启发了本文。

+   一个声称*backtrader*无法处理 1.6M 根蜡烛的帖子：[reddit/r/algotrading - 一个高性能的回测系统？](https://www.reddit.com/r/algotrading/comments/dlfujr/a_performant_backtesting_system/)

+   还有一个要求能够回测 8000 支股票的东西：[reddit/r/algotrading - 支持 1000+ 支股票的回测库？](https://www.reddit.com/r/algotrading/comments/dmv51t/backtesting_libs_that_supports_1000_stocks/)

    作者询问关于一个可以进行“核心/内存外”回测的框架，*“因为显然无法将所有这些数据加载到内存中”*

我们当然将使用*backtrader*来解决这些概念

## 2M 根蜡烛

为了做到这一点，首先要生成那么多根蜡烛。考虑到第一个帖子提到了 77 支股票和 1.6M 根蜡烛，这将导致每支股票有 20,779 根蜡烛，因此我们将采取以下措施以获得良好的数字

+   为 100 支股票生成蜡烛

+   每支股票生成 20,000 根蜡烛

即：共计 2M 根蜡烛的 100 个文件。

脚本

```py
import numpy as np
import pandas as pd

COLUMNS = ['open', 'high', 'low', 'close', 'volume', 'openinterest']
CANDLES = 20000
STOCKS

dateindex = pd.date_range(start='2010-01-01', periods=CANDLES, freq='15min')

for i in range(STOCKS):

    data = np.random.randint(10, 20, size=(CANDLES, len(COLUMNS)))
    df = pd.DataFrame(data * 1.01, dateindex, columns=COLUMNS)
    df = df.rename_axis('datetime')
    df.to_csv('candles{:02d}.csv'.format(i))
```

这将生成 100 个文件，从`candles00.csv`开始一直到`candles99.csv`。实际值并不重要。重要的是具有标准的`datetime`、`OHLCV`组件（和`OpenInterest`）。

## 测试系统

+   *硬件/操作系统*：将使用一台配备 Intel i7 和 32 G 字节内存的 Windows 10 15.6 英寸笔记本电脑。

+   *Python*：CPython `3.6.1` 和 `pypy3 6.0.0`

+   *其他*：一个持续运行并占用大约 20% CPU 的应用程序。常见的嫌疑人如 Chrome（102 个进程）、Edge、Word、Powerpoint、Excel 和一些次要应用程序正在运行

## *backtrader* 默认配置

让我们回想一下*backtrader*的默认运行时配置是什么：

+   如有可能，预加载所有数据源

+   如果所有数据源都可以预加载，请在批处理模式（命名为`runonce`）中运行

+   首先预先计算所有指标

+   逐步通过策略逻辑和经纪人

## 在默认批处理`runonce`模式下执行挑战

我们的测试脚本（请查看底部获取完整源代码）将打开这 100 个文件，并使用默认的*backtrader*配置处理它们。

```py
$ ./two-million-candles.py
Cerebro Start Time:          2019-10-26 08:33:15.563088
Strat Init Time:             2019-10-26 08:34:31.845349
Time Loading Data Feeds:     76.28
Number of data feeds:        100
Strat Start Time:            2019-10-26 08:34:31.864349
Pre-Next Start Time:         2019-10-26 08:34:32.670352
Time Calculating Indicators: 0.81
Next Start Time:             2019-10-26 08:34:32.671351
Strat warm-up period Time:   0.00
Time to Strat Next Logic:    77.11
End Time:                    2019-10-26 08:35:31.493349
Time in Strategy Next Logic: 58.82
Total Time in Strategy:      58.82
Total Time:                  135.93
Length of data feeds:        20000
```

**内存使用**：观察到峰值为 348 M 字节

大部分时间实际上是用于预加载数据（`98.63` 秒），其余时间用于策略，其中包括在每次迭代中通过经纪人（`73.63` 秒）。总时间为`173.26`秒。

根据您想如何计算，性能为：

+   考虑整个运行时间为每秒`14,713`根蜡烛

**底线**：在上面两个 Reddit 帖子中声称*backtrader*无法处理 1.6M 根蜡烛的说法是*错误的*。

### 使用`pypy`进行操作

既然该帖子声称使用`pypy`没有帮助，那么我们来看看使用它会发生什么。

```py
$ ./two-million-candles.py
Cerebro Start Time:          2019-10-26 08:39:42.958689
Strat Init Time:             2019-10-26 08:40:31.260691
Time Loading Data Feeds:     48.30
Number of data feeds:        100
Strat Start Time:            2019-10-26 08:40:31.338692
Pre-Next Start Time:         2019-10-26 08:40:31.612688
Time Calculating Indicators: 0.27
Next Start Time:             2019-10-26 08:40:31.612688
Strat warm-up period Time:   0.00
Time to Strat Next Logic:    48.65
End Time:                    2019-10-26 08:40:40.150689
Time in Strategy Next Logic: 8.54
Total Time in Strategy:      8.54
Total Time:                  57.19
Length of data feeds:        20000
```

天啊！总时间从`135.93`秒降至`57.19`秒。性能**翻了一番**。

性能：每秒`34,971`根蜡烛

**内存使用**：峰值为 269 兆字节。

这也是与标准 CPython 解释器相比的重要改进。

## 处理 2 百万根蜡烛超出核心内存

所有这些都可以改进，如果考虑到*backtrader*有几个配置选项用于执行回测会话，包括优化缓冲区并仅使用最少需要的数据集（理想情况下仅使用大小为`1`的缓冲区，这仅在理想情况下发生）

要使用的选项将是`exactbars=True`。从`exactbars`的文档中（这是在实例化`Cerebro`时或在调用`run`时传递给`Cerebro`的参数）

```py
 `True` or `1`: all “lines” objects reduce memory usage to the
  automatically calculated minimum period.

  If a Simple Moving Average has a period of 30, the underlying data
  will have always a running buffer of 30 bars to allow the
  calculation of the Simple Moving Average

  * This setting will deactivate `preload` and `runonce`

  * Using this setting also deactivates **plotting**
```

为了最大限度地优化，并且因为绘图将被禁用，以下内容也将被使用：`stdstats=False`，它禁用了用于现金、价值和交易的标准*观察者*（对绘图有用，但不再在范围内）

```py
$ ./two-million-candles.py --cerebro exactbars=False,stdstats=False
Cerebro Start Time:          2019-10-26 08:37:08.014348
Strat Init Time:             2019-10-26 08:38:21.850392
Time Loading Data Feeds:     73.84
Number of data feeds:        100
Strat Start Time:            2019-10-26 08:38:21.851394
Pre-Next Start Time:         2019-10-26 08:38:21.857393
Time Calculating Indicators: 0.01
Next Start Time:             2019-10-26 08:38:21.857393
Strat warm-up period Time:   0.00
Time to Strat Next Logic:    73.84
End Time:                    2019-10-26 08:39:02.334936
Time in Strategy Next Logic: 40.48
Total Time in Strategy:      40.48
Total Time:                  114.32
Length of data feeds:        20000
```

性能：每秒`17,494`根蜡烛

**内存使用**：75 兆字节（在回测会话的整个过程中保持稳定）

让我们与之前未经优化的运行进行比较

+   而不是花费超过`76`秒预加载数据，因为数据没有预加载，回测立即开始

+   总时间为`114.32`秒，比`135.93`秒快了`15.90%`。

+   内存使用改进了`68.5%`。

注意

实际上，我们可以向脚本输入 1 亿根蜡烛，内存消耗量仍将保持在`75 兆字节`不变

### 再次使用`pypy`进行操作

现在我们知道如何优化，让我们按照`pypy`的方式来做。

```py
$ ./two-million-candles.py --cerebro exactbars=True,stdstats=False
Cerebro Start Time:          2019-10-26 08:44:32.309689
Strat Init Time:             2019-10-26 08:44:32.406689
Time Loading Data Feeds:     0.10
Number of data feeds:        100
Strat Start Time:            2019-10-26 08:44:32.409689
Pre-Next Start Time:         2019-10-26 08:44:32.451689
Time Calculating Indicators: 0.04
Next Start Time:             2019-10-26 08:44:32.451689
Strat warm-up period Time:   0.00
Time to Strat Next Logic:    0.14
End Time:                    2019-10-26 08:45:38.918693
Time in Strategy Next Logic: 66.47
Total Time in Strategy:      66.47
Total Time:                  66.61
Length of data feeds:        20000
```

性能：每秒`30,025`根蜡烛

**内存使用**：恒定为`49 兆字节`

与之前的等效运行相比：

+   运行时间为`66.61`秒，比之前的`114.32`秒快了`41.73%`

+   `49 兆字节`与`75 兆字节`相比，内存使用改善了`34.6%`。

注意

在这种情况下，`pypy`无法击败其批处理(`runonce`)模式的时间，即`57.19`秒。这是可以预料的，因为在预加载时，计算器指示是以**向量化**模式进行的，而这正是`pypy`的 JIT 擅长的地方。

无论如何，它仍然表现出色，并且在内存消耗方面有重要的**改进**

## 运行完整的交易

该脚本可以创建指标（移动平均线）并在 100 个数据源上执行*多空*策略，使用移动平均线的交叉。让我们用`pypy`来做，并且知道它与批处理模式更好，就这么办。

```py
$ ./two-million-candles.py --strat indicators=True,trade=True
Cerebro Start Time:          2019-10-26 08:57:36.114415
Strat Init Time:             2019-10-26 08:58:25.569448
Time Loading Data Feeds:     49.46
Number of data feeds:        100
Total indicators:            300
Moving Average to be used:   SMA
Indicators period 1:         10
Indicators period 2:         50
Strat Start Time:            2019-10-26 08:58:26.230445
Pre-Next Start Time:         2019-10-26 08:58:40.850447
Time Calculating Indicators: 14.62
Next Start Time:             2019-10-26 08:58:41.005446
Strat warm-up period Time:   0.15
Time to Strat Next Logic:    64.89
End Time:                    2019-10-26 09:00:13.057955
Time in Strategy Next Logic: 92.05
Total Time in Strategy:      92.21
Total Time:                  156.94
Length of data feeds:        20000
```

性能：每秒`12,743`根蜡烛

**内存使用**：观察到峰值为`1300 Mbytes`。

执行时间显然增加了（指标 + 交易），但为什么内存使用量增加了呢？

在得出任何结论之前，让我们运行它创建指标，但不进行交易

```py
$ ./two-million-candles.py --strat indicators=True
Cerebro Start Time:          2019-10-26 09:05:55.967969
Strat Init Time:             2019-10-26 09:06:44.072969
Time Loading Data Feeds:     48.10
Number of data feeds:        100
Total indicators:            300
Moving Average to be used:   SMA
Indicators period 1:         10
Indicators period 2:         50
Strat Start Time:            2019-10-26 09:06:44.779971
Pre-Next Start Time:         2019-10-26 09:06:59.208969
Time Calculating Indicators: 14.43
Next Start Time:             2019-10-26 09:06:59.360969
Strat warm-up period Time:   0.15
Time to Strat Next Logic:    63.39
End Time:                    2019-10-26 09:07:09.151838
Time in Strategy Next Logic: 9.79
Total Time in Strategy:      9.94
Total Time:                  73.18
Length of data feeds:        20000
```

性能：`27,329`根蜡烛/秒

**内存使用**：`600 Mbytes`（在优化的`exactbars`模式下进行相同操作仅消耗`60 Mbytes`，但执行时间增加，因为`pypy`本身不能进行如此大的优化）

有了这个：**交易时内存使用量确实增加了**。原因是`Order`和`Trade`对象被创建、传递并由经纪人保留。

注意

要考虑到数据集包含随机值，这会产生大量的交叉，因此会产生大量的订单和交易。不应期望常规数据集有类似的行为。

## 结论

### 无效声明

如上所证明的那样是*虚假的*，因为*backtrader* **能够**处理 160 万根蜡烛以上。

### 一般情况

1.  *backtrader*可以轻松处理`2M`根蜡烛，使用默认配置（内存数据预加载）

1.  *backtrader*可以在非预加载优化模式下运行，将缓冲区减少到最小，以进行核心外存内存回测

1.  当在优化的非预加载模式下进行*回测*时，内存消耗的增加来自经纪人生成的行政开销。

1.  即使在交易时，使用指标并且经纪人不断介入，性能也是`12,473`根蜡烛/秒

1.  在可能的情况下使用`pypy`（例如，如果你不需要绘图）

### 对于这些情况使用 Python 和/或*backtrader*

使用`pypy`，启用交易，并且使用随机数据集（比平常更多的交易），整个 2M 根蜡烛的处理时间为：

+   `156.94`秒，即：几乎`2 分钟 37 秒`

考虑到这是在一台同时运行多个其他任务的笔记本电脑上完成的，可以得出结论，可以处理`2M`个条形图。

### `8000`支股票的情况呢？

执行时间必须乘以 80，因此：

+   需要运行这个随机集场景的时间为`12,560`秒（或几乎`210 分钟`或`3 小时 30 分钟`）。

即使假设标准数据集会生成远少于操作，也仍然需要谈论**几小时**（`3 或 4`）的回测时间

内存使用量也会增加，当**交易**时由于经纪人的操作，并且可能需要**一些**吉字节。

注意

这里不能简单地再次乘以 80，因为示例脚本使用随机数据进行交易，并尽可能频繁。无论如何，所需的 RAM 量都将是**重要的**

因此，仅使用*backtrader*作为研究和回测工具的工作流似乎有些牵强。

## 关于工作流的讨论

使用*backtrader*时需要考虑两种标准工作流程

+   一切都用`backtrader`完成，即：研究和回测都在一个工具中完成

+   使用 `pandas` 进行研究，获取想法是否良好的概念，然后使用 `backtrader` 进行回测，尽可能准确地验证，可能已将大型数据集缩减为对于常规 RAM 场景更易处理的内容。

提示

人们可以想象使用类似 `dask` 进行外存内存执行来替换 `pandas`

## 测试脚本

这里是源代码

```py
#!/usr/bin/env python
# -*- coding: utf-8; py-indent-offset:4 -*-
###############################################################################
import argparse
import datetime

import backtrader as bt

class St(bt.Strategy):
    params = dict(
        indicators=False,
        indperiod1=10,
        indperiod2=50,
        indicator=bt.ind.SMA,
        trade=False,
    )

    def __init__(self):
        self.dtinit = datetime.datetime.now()
        print('Strat Init Time: {}'.format(self.dtinit))
        loaddata = (self.dtinit - self.env.dtcerebro).total_seconds()
        print('Time Loading Data Feeds: {:.2f}'.format(loaddata))

        print('Number of data feeds: {}'.format(len(self.datas)))
        if self.p.indicators:
            total_ind = self.p.indicators * 3 * len(self.datas)
            print('Total indicators: {}'.format(total_ind))
            indname = self.p.indicator.__name__
            print('Moving Average to be used: {}'.format(indname))
            print('Indicators period 1: {}'.format(self.p.indperiod1))
            print('Indicators period 2: {}'.format(self.p.indperiod2))

            self.macross = {}
            for d in self.datas:
                ma1 = self.p.indicator(d, period=self.p.indperiod1)
                ma2 = self.p.indicator(d, period=self.p.indperiod2)
                self.macross[d] = bt.ind.CrossOver(ma1, ma2)

    def start(self):
        self.dtstart = datetime.datetime.now()
        print('Strat Start Time: {}'.format(self.dtstart))

    def prenext(self):
        if len(self.data0) == 1:  # only 1st time
            self.dtprenext = datetime.datetime.now()
            print('Pre-Next Start Time: {}'.format(self.dtprenext))
            indcalc = (self.dtprenext - self.dtstart).total_seconds()
            print('Time Calculating Indicators: {:.2f}'.format(indcalc))

    def nextstart(self):
        if len(self.data0) == 1:  # there was no prenext
            self.dtprenext = datetime.datetime.now()
            print('Pre-Next Start Time: {}'.format(self.dtprenext))
            indcalc = (self.dtprenext - self.dtstart).total_seconds()
            print('Time Calculating Indicators: {:.2f}'.format(indcalc))

        self.dtnextstart = datetime.datetime.now()
        print('Next Start Time: {}'.format(self.dtnextstart))
        warmup = (self.dtnextstart - self.dtprenext).total_seconds()
        print('Strat warm-up period Time: {:.2f}'.format(warmup))
        nextstart = (self.dtnextstart - self.env.dtcerebro).total_seconds()
        print('Time to Strat Next Logic: {:.2f}'.format(nextstart))
        self.next()

    def next(self):
        if not self.p.trade:
            return

        for d, macross in self.macross.items():
            if macross > 0:
                self.order_target_size(data=d, target=1)
            elif macross < 0:
                self.order_target_size(data=d, target=-1)

    def stop(self):
        dtstop = datetime.datetime.now()
        print('End Time: {}'.format(dtstop))
        nexttime = (dtstop - self.dtnextstart).total_seconds()
        print('Time in Strategy Next Logic: {:.2f}'.format(nexttime))
        strattime = (dtstop - self.dtprenext).total_seconds()
        print('Total Time in Strategy: {:.2f}'.format(strattime))
        totaltime = (dtstop - self.env.dtcerebro).total_seconds()
        print('Total Time: {:.2f}'.format(totaltime))
        print('Length of data feeds: {}'.format(len(self.data)))

def run(args=None):
    args = parse_args(args)

    cerebro = bt.Cerebro()

    datakwargs = dict(timeframe=bt.TimeFrame.Minutes, compression=15)
    for i in range(args.numfiles):
        dataname = 'candles{:02d}.csv'.format(i)
        data = bt.feeds.GenericCSVData(dataname=dataname, **datakwargs)
        cerebro.adddata(data)

    cerebro.addstrategy(St, **eval('dict(' + args.strat + ')'))
    cerebro.dtcerebro = dt0 = datetime.datetime.now()
    print('Cerebro Start Time: {}'.format(dt0))
    cerebro.run(**eval('dict(' + args.cerebro + ')'))

def parse_args(pargs=None):
    parser = argparse.ArgumentParser(
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
        description=(
            'Backtrader Basic Script'
        )
    )

    parser.add_argument('--numfiles', required=False, default=100, type=int,
                        help='Number of files to rea')

    parser.add_argument('--cerebro', required=False, default='',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--strat', '--strategy', required=False, default='',
                        metavar='kwargs', help='kwargs in key=value format')

    return parser.parse_args(pargs)

if __name__ == '__main__':
    run()
```
