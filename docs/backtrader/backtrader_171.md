# 逃离 OHLC 之地

> 原文：[`www.backtrader.com/blog/posts/2016-03-08-escape-from-ohlc-land/escape-from-ohlc-land/`](https://www.backtrader.com/blog/posts/2016-03-08-escape-from-ohlc-land/escape-from-ohlc-land/)

在 backtrader 的构思和开发过程中应用的一个关键概念是**灵活性**。Python 的*元编程*和*内省*能力是保持许多灵活性的基础，同时仍能够交付成果。

一篇旧文章展示了扩展概念。

+   [扩展数据源](http://blog.backtrader.com/posts/2015-08-07/extending-a-datafeed/)

基础知识：

```py
`from backtrader.feeds import GenericCSVData

class GenericCSV_PE(GenericCSVData):
    lines = ('pe',)  # Add 'pe' to already defined lines` 
```

完成。`backtrader`在后台定义了最常见的线：OHLC。

如果我们深入研究`GenericCSV_PE`的最终方面，继承加上新定义的线的总和将产生以下线：

```py
`('close', 'open', 'high', 'low', 'volume', 'openinterest', 'datetime', 'pe',)` 
```

这可以随时通过`getlinealiases`方法检查（适用于*DataFeeds*、*Indicators*、*Strategies*和*Observers*）

机制是灵活的，通过对内部进行一些探索，你实际上可以得到任何东西，但已经证明这还不够。

[Ticket #60](https://github.com/mementum/backtrader/issues/60)询问是否支持*高频数据*，即：Bid/Ask 数据。这意味着以*OHLC*形式预定义的*lines*层次结构不够用。*Bid*和*Ask*价格、成交量和交易数量可以适应现有的*OHLC*字段，但这不会感觉自然。如果只关注*Bid*和*Ask*价格，会有太多未触及的字段。

这需要一个解决方案，已经在[发布 1.2.1.88](http://blog.backtrader.com/posts/2016-03-07-release-1.2.1.88/release-1.2.1.88/)中实施。这个想法可以总结为：

+   现在不仅可以*扩展*现有的层次结构，还可以用新的层次结构*替换*原有的层次结构

只有一个约束条件：

+   必须存在一个`datetime`字段（希望其中包含有意义的`datetime`信息）

    这是因为`backtrader`需要一些用于同步的东西（多个数据源、多个时间框架、重新采样、重播），就像阿基米德需要杠杆一样。

这是它的工作原理：

```py
`from backtrader.feeds import GenericCSVData

class GenericCSV_BidAsk(GenericCSVData):
    linesoverride = True
    lines = ('bid', 'ask', 'datetime')  # Replace hierarchy with this one` 
```

完成。

好的，不完全是因为我们正在查看从*csv*源加载的行。层次结构实际上已经被**替换**为*bid, ask datetime*定义，这要归功于`linesoverride=True`设置。

原始的`GenericCSVData`类解析一个*csv*文件，并需要提示哪里是对应于*lines*的*fields*。原始定义如下：

```py
`class GenericCSVData(feed.CSVDataBase):
    params = (
        ('nullvalue', float('NaN')),
        ('dtformat', '%Y-%m-%d %H:%M:%S'),
        ('tmformat', '%H:%M:%S'),

        ('datetime', 0),
        ('time', -1),  # -1 means not present
        ('open', 1),
        ('high', 2),
        ('low', 3),
        ('close', 4),
        ('volume', 5),
        ('openinterest', 6),
    )` 
```

新的*重新定义层次结构类*可以轻松完成：

```py
`from backtrader.feeds import GenericCSVData

class GenericCSV_BidAsk(GenericCSVData):
    linesoverride = True
    lines = ('bid', 'ask', 'datetime')  # Replace hierarchy with this one

    params = (('bid', 1), ('ask', 2))` 
```

表明*Bid*价格是 csv 流中的字段#1，*Ask*价格是字段#2。我们保留了基类中的*datetime* #0 定义不变。

为此制作一个小数据文件有所帮助：

```py
`TIMESTAMP,BID,ASK
02/03/2010 16:53:50,0.5346,0.5347
02/03/2010 16:53:51,0.5343,0.5347
02/03/2010 16:53:52,0.5543,0.5545
02/03/2010 16:53:53,0.5342,0.5344
02/03/2010 16:53:54,0.5245,0.5464
02/03/2010 16:53:54,0.5460,0.5470
02/03/2010 16:53:56,0.5824,0.5826
02/03/2010 16:53:57,0.5371,0.5374
02/03/2010 16:53:58,0.5793,0.5794
02/03/2010 16:53:59,0.5684,0.5688` 
```

将一个小测试脚本添加到等式中（对于那些直接转到源代码中的示例的人来说，增加一些内容）（见最后的完整代码）：

```py
`$ ./bidask.py` 
```

输出说明一切：

```py
 `1: 2010-02-03T16:53:50 - Bid 0.5346 - 0.5347 Ask
 2: 2010-02-03T16:53:51 - Bid 0.5343 - 0.5347 Ask
 3: 2010-02-03T16:53:52 - Bid 0.5543 - 0.5545 Ask
 4: 2010-02-03T16:53:53 - Bid 0.5342 - 0.5344 Ask
 5: 2010-02-03T16:53:54 - Bid 0.5245 - 0.5464 Ask
 6: 2010-02-03T16:53:54 - Bid 0.5460 - 0.5470 Ask
 7: 2010-02-03T16:53:56 - Bid 0.5824 - 0.5826 Ask
 8: 2010-02-03T16:53:57 - Bid 0.5371 - 0.5374 Ask
 9: 2010-02-03T16:53:58 - Bid 0.5793 - 0.5794 Ask
10: 2010-02-03T16:53:59 - Bid 0.5684 - 0.5688 Ask` 
```

瞧！*Bid*/*Ask*价格已经被正确读取、解析和解释，并且策略已能通过*self.data*访问数据源中的*.bid*和*.ask*行。

重新定义*lines*层次结构却带来一个广泛的问题，即已预定义的*Indicators*的用法。

+   例如：*Stochastic*是一种依赖于*close*、*high*和*low*价格来计算其输出的指标。

    即使我们把*Bid*视为*close*（因为它是第一个），仅有另一个*price*元素（*Ask*），而不是另外两个。而概念上，*Ask*与*high*和*low*没有任何关系。

    有可能，与这些字段一起工作并在*高频交易*领域操作（或研究）的人并不关心*Stochastic*作为首选指标

+   其他指标如*移动平均*完全正常。它们对字段的含义或暗示不做任何假设，且乐意接受任何东西。因此，可以这样做：

    ```py
    `mysma = backtrader.indicators.SMA(self.data.bid, period=5)` 
    ```

    并且将提供最后 5 个*bid*价格的移动平均线

测试脚本已经支持添加*SMA*。让我们执行：

```py
`$ ./bidask.py --sma --period=3` 
```

输出：

```py
 `3: 2010-02-03T16:53:52 - Bid 0.5543 - 0.5545 Ask - SMA: 0.5411
 4: 2010-02-03T16:53:53 - Bid 0.5342 - 0.5344 Ask - SMA: 0.5409
 5: 2010-02-03T16:53:54 - Bid 0.5245 - 0.5464 Ask - SMA: 0.5377
 6: 2010-02-03T16:53:54 - Bid 0.5460 - 0.5470 Ask - SMA: 0.5349
 7: 2010-02-03T16:53:56 - Bid 0.5824 - 0.5826 Ask - SMA: 0.5510
 8: 2010-02-03T16:53:57 - Bid 0.5371 - 0.5374 Ask - SMA: 0.5552
 9: 2010-02-03T16:53:58 - Bid 0.5793 - 0.5794 Ask - SMA: 0.5663
10: 2010-02-03T16:53:59 - Bid 0.5684 - 0.5688 Ask - SMA: 0.5616` 
```

注意

绘图仍然依赖于`open`、`high`、`low`、`close`和`volume`存在于数据源中。

一些情况可以通过简单地用*Line on Close*绘图并仅取对象中的第 1 个定义行来直接覆盖。但是必须开发一个合理的模型。针对即将推出的`backtrader`版本

测试脚本用法：

```py
`$ ./bidask.py --help
usage: bidask.py [-h] [--data DATA] [--dtformat DTFORMAT] [--sma]
                 [--period PERIOD]

Bid/Ask Line Hierarchy

optional arguments:
  -h, --help            show this help message and exit
  --data DATA, -d DATA  data to add to the system (default:
                        ../../datas/bidask.csv)
  --dtformat DTFORMAT, -dt DTFORMAT
                        Format of datetime in input (default: %m/%d/%Y
                        %H:%M:%S)
  --sma, -s             Add an SMA to the mix (default: False)
  --period PERIOD, -p PERIOD
                        Period for the sma (default: 5)` 
```

而测试脚本本身（包含在`backtrader`源代码中）

```py
`from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse

import backtrader as bt
import backtrader.feeds as btfeeds
import backtrader.indicators as btind

class BidAskCSV(btfeeds.GenericCSVData):
    linesoverride = True  # discard usual OHLC structure
    # datetime must be present and last
    lines = ('bid', 'ask', 'datetime')
    # datetime (always 1st) and then the desired order for
    params = (
        # (datetime, 0), # inherited from parent class
        ('bid', 1),  # default field pos 1
        ('ask', 2),  # default field pos 2
    )

class St(bt.Strategy):
    params = (('sma', False), ('period', 3))

    def __init__(self):
        if self.p.sma:
            self.sma = btind.SMA(self.data, period=self.p.period)

    def next(self):
        dtstr = self.data.datetime.datetime().isoformat()
        txt = '%4d: %s - Bid %.4f - %.4f Ask' % (
            (len(self), dtstr, self.data.bid[0], self.data.ask[0]))

        if self.p.sma:
            txt += ' - SMA: %.4f' % self.sma[0]
        print(txt)

def parse_args():
    parser = argparse.ArgumentParser(
        description='Bid/Ask Line Hierarchy',
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
    )

    parser.add_argument('--data', '-d', action='store',
                        required=False, default='../../datas/bidask.csv',
                        help='data to add to the system')

    parser.add_argument('--dtformat', '-dt',
                        required=False, default='%m/%d/%Y %H:%M:%S',
                        help='Format of datetime in input')

    parser.add_argument('--sma', '-s', action='store_true',
                        required=False,
                        help='Add an SMA to the mix')

    parser.add_argument('--period', '-p', action='store',
                        required=False, default=5, type=int,
                        help='Period for the sma')

    return parser.parse_args()

def runstrategy():
    args = parse_args()

    cerebro = bt.Cerebro()  # Create a cerebro

    data = BidAskCSV(dataname=args.data, dtformat=args.dtformat)
    cerebro.adddata(data)  # Add the 1st data to cerebro
    # Add the strategy to cerebro
    cerebro.addstrategy(St, sma=args.sma, period=args.period)
    cerebro.run()

if __name__ == '__main__':
    runstrategy()` 
```
