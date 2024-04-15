# 使用保守公式重新平衡

> 原文：[`www.backtrader.com/blog/2019-07-19-rebalancing-conservative/rebalancing-conservative/`](https://www.backtrader.com/blog/2019-07-19-rebalancing-conservative/rebalancing-conservative/)

*保守公式*方法在本文中提出：[The Conservative Formula in Python: Quantitative Investing made Easy](https://papers.ssrn.com/sol3/papers.cfm?abstract_id=3145152)

这是许多可能的重新平衡方法之一，但是易于理解。方法概要：

+   从 `Y`（1000 中的 100）的宇宙中选取了 `x` 支股票。

+   选择标准是：

    +   低波动率

    +   高净支付收益

    +   高动量

    +   每月重新平衡

有了这个想法，让我们去提出*backtrader*中的一种可能实现吧。

## 数据

即使有一个获胜的策略，如果没有为该策略提供数据，也不会真正获胜。这意味着必须考虑数据的外观以及如何加载数据。

假定一组*CSV*（“逗号分隔值”）文件可用，包含以下功能

+   `ohlcv` 月度数据

+   在 `v` 后增加了一个额外的字段，包含*净支付收益*（`npy`），以获取 `ohlcvn` 数据集。

因此，*CSV*数据的格式如下

```py
date, open, high, low, close, volume, npy
2001-12-31, 1.0, 1.0, 1.0, 1.0, 0.5, 3.0
2002-01-31, 2.0, 2.5, 1.1, 1.2, 3.0, 5.0
...
```

即：每月一行。现在数据加载器引擎可以准备好创建一个简单的扩展，与*backtrader*一起提供的通用内置 CSV 加载器。

```py
class NetPayOutData(bt.feeds.GenericCSVData):
    lines = ('npy',)  # add a line containing the net payout yield
    params = dict(
        npy=6,  # npy field is in the 6th column (0 based index)
        dtformat='%Y-%m-%d',  # fix date format a yyyy-mm-dd
        timeframe=bt.TimeFrame.Months,  # fixed the timeframe
        openinterest=-1,  # -1 indicates there is no openinterest field
    )
```

那就是。注意添加基本数据到`ohlcv`数据流是多么容易。

1.  通过使用表达式 `lines=('npy',)`。其他常规字段（`open`、`high`等）已经包含在 `GenericCSVData` 中

1.  通过使用`params = dict(npy=6)`指示加载位置。其他字段具有预定义的位置。

*时间框架*也已在参数中更新，以反映数据的每月性质。

* * *

**注意**

请参阅[文档 - 数据源参考 - GenericCSVData](https://www.backtrader.com/docu/dataautoref/#genericcsvdata)以获取实际字段和加载位置（所有这些都可以自定义）

* * *

数据加载器将必须用文件名正确实例化，但这是稍后的事情，当下面提供标准样板以获得完整的脚本时。

## 策略

让我们将逻辑放入标准的*backtrader*策略中。为了尽可能使其通用和可定制化，将使用与数据相同的`params`方法，就像之前用数据一样。

在深入研究策略之前，让我们考虑快速摘要中的一个要点

+   从 `Y` 的宇宙中选择了 `x` 支股票。

策略本身并不负责向宇宙中添加股票，但负责选择。如果在代码中固定了`x`和`Y`，但宇宙中只添加了 50 支股票，仍然尝试选择 100 支股票，就会出现这样的情况。为了应对这种情况，将执行以下操作：

+   具有值为`0.10`（即：`10%`）的`selperc`参数，表示从宇宙中选择的股票数量。

    这意味着如果有 1000 只股票，只会选择 100 只，如果宇宙由 50 只股票组成，则只会选择 5 只。

至于排名股票的公式，看起来像这样：

+   `(动量 * 净支付) / 波动率`

    这意味着具有较高动量、较高支付和较低波动率的股票将获得较高的分数。

对于`momentum`，将使用`RateOfChange`指标（又名`ROC`），它*测量一段时间内价格的变化比率*。

`净支付`已经是数据源的一部分。

要计算`波动率`，将使用股票的`n-periods`回报的`标准差`（`n-periods`，因为事物将保持为参数）。

有了这些信息，策略就可以用正确的参数进行*初始化*，设置指标和计算，这些将在每月迭代中使用。

首先是声明和参数。

```py
class St(bt.Strategy):
    params = dict(
        selcperc=0.10,  # percentage of stocks to select from the universe
        rperiod=1,  # period for the returns calculation, default 1 period
        vperiod=36,  # lookback period for volatility - default 36 periods
        mperiod=12,  # lookback period for momentum - default 12 periods
        reserve=0.05  # 5% reserve capital
    )
```

注意，上面未提及的内容已添加，即参数`reserve=0.05`（即*5%*），用于计算每只股票的百分比分配，保留一定资金在银行中。虽然对于模拟，人们可能想要使用 100%的资本，但这样做可能会遇到一些问题，如价格差距、浮点精度等，最终可能会错过一些市场入场机会。

在任何其他事情之前，创建一个小的日志记录方法，它将允许记录组合如何重新平衡。

```py
 `def log(self, arg):
        print('{}  {}'.format(self.datetime.date(), arg))
```

在`__init__`方法的开头，计算要排名的股票数量，并应用保留资本参数以确定银行的每只股票百分比。

```py
 `def __init__(self):
        # calculate 1st the amount of stocks that will be selected
        self.selnum = int(len(self.datas) * self.p.selcperc)

        # allocation perc per stock
        # reserve kept to make sure orders are not rejected due to
        # margin. Prices are calculated when known (close), but orders can only
        # be executed next day (opening price). Price can gap upwards
        self.perctarget = (1.0 - self.p.reserve) % self.selnum
```

最后，初始化完成，计算每只股票的波动率和动量指标，然后将其应用于每只股票的排名公式计算中。

```py
 `# returns, volatilities and momentums
        rs = [bt.ind.PctChange(d, period=self.p.rperiod) for d in self.datas]
        vs = [bt.ind.StdDev(ret, period=self.p.vperiod) for ret in rs]
        ms = [bt.ind.ROC(d, period=self.p.mperiod) for d in self.datas]

        # simple rank formula: (momentum * net payout) / volatility
        # the highest ranked: low vol, large momentum, large payout
        self.ranks = {d: d.npy * m / v for d, v, m in zip(self.datas, vs, ms)}
```

现在是每个月迭代的时候了。排名在`self.ranks`字典中可用。每次迭代都必须对键/值对进行排序，以确定哪些项必须离开，哪些项必须成为组合的一部分（保留或添加）。

```py
 `def next(self):
        # sort data and current rank
        ranks = sorted(
            self.ranks.items(),  # get the (d, rank), pair
            key=lambda x: x[1][0],  # use rank (elem 1) and current time "0"
            reverse=True,  # highest ranked 1st ... please
        )
```

可迭代物按照相反顺序排序，因为排名公式为排名靠前的股票提供更高的分数。

现在是重新平衡的时候了。

### 重新平衡 1：获取排名靠前和具有开仓头寸的股票。

```py
 `# put top ranked in dict with data as key to test for presence
        rtop = dict(ranks[:self.selnum])

        # For logging purposes of stocks leaving the portfolio
        rbot = dict(ranks[self.selnum:])
```

这里发生了一些 Python 的诡计，因为使用了一个`dict`。原因是，如果将排名靠前的股票放入一个`list`中，*Python*会在内部使用运算符`==`来检查运算符`in`的存在。尽管不太可能，但两只股票可能在同一天具有相同的值。使用`dict`时，检查项存在性时会使用哈希值作为键的一部分。

**注意**：出于日志记录目的，还创建了`rbot`（*排名底部*），其中包含未在`rtop`中出现的股票。

为了后续区分必须离开投资组合的股票、那些只需重新平衡的股票以及新的排名靠前的股票，准备了投资组合中当前的股票列表。

```py
 `# prepare quick lookup list of stocks currently holding a position
        posdata = [d for d, pos in self.getpositions().items() if pos]
```

### 重新平衡 2：卖出不再排名靠前的股票

就像在现实世界中一样，在*backtrader*生态系统中，在买入之前卖出是必须的，以确保有足够的现金。

```py
 `# remove those no longer top ranked
        # do this first to issue sell orders and free cash
        for d in (d for d in posdata if d not in rtop):
            self.log('Exit {} - Rank {:.2f}'.format(d._name, rbot[d][0]))
            self.order_target_percent(d, target=0.0)
```

当前拥有仓位但不再排名靠前的股票被出售（即`target=0.0`）。

**注意**

这里一个简单的`self.close(data)`就足够了，而不是明确说明目标百分比。

### 重新平衡 3：为所有排名靠前的股票发布目标订单

总投资组合价值随时间变化，已经在投资组合中的股票可能必须略微增加/减少当前仓位以匹配预期的百分比。`order_target_percent`是进入市场的理想方法，因为它会自动计算是否需要`buy`或`sell`订单。

```py
 `# rebalance those already top ranked and still there
        for d in (d for d in posdata if d in rtop):
            self.log('Rebal {} - Rank {:.2f}'.format(d._name, rtop[d][0]))
            self.order_target_percent(d, target=self.perctarget)
            del rtop[d]  # remove it, to simplify next iteration
```

在将新股票添加到投资组合之前，重新平衡已有仓位的股票，因为新股票只会发布`buy`订单并消耗现金。在重新平衡后从`rtop[data].pop()`中移除现有股票后，`rtop`中剩余的股票是将新添加到投资组合中的股票。

```py
 `# issue a target order for the newly top ranked stocks
        # do this last, as this will generate buy orders consuming cash
        for d in rtop:
            self.log('Enter {} - Rank {:.2f}'.format(d._name, rtop[d][0]))
            self.order_target_percent(d, target=self.perctarget)
```

## 运行所有并评估它！

拥有数据加载器类和策略是不够的。就像任何其他框架一样，需要一些样板。以下代码使其成为可能。

```py
def run(args=None):
    args = parse_args(args)

    cerebro = bt.Cerebro()

    # Data feed kwargs
    dkwargs = dict(**eval('dict(' + args.dargs + ')'))

    # Parse from/to-date
    dtfmt, tmfmt = '%Y-%m-%d', 'T%H:%M:%S'
    if args.fromdate:
        fmt = dtfmt + tmfmt * ('T' in args.fromdate)
        dkwargs['fromdate'] = datetime.datetime.strptime(args.fromdate, fmt)

    if args.todate:
        fmt = dtfmt + tmfmt * ('T' in args.todate)
        dkwargs['todate'] = datetime.datetime.strptime(args.todate, fmt)

    # add all the data files available in the directory datadir
    for fname in glob.glob(os.path.join(args.datadir, '*')):
        data = NetPayOutData(dataname=fname, **dkwargs)
        cerebro.adddata(data)

    # add strategy
    cerebro.addstrategy(St, **eval('dict(' + args.strat + ')'))

    # set the cash
    cerebro.broker.setcash(args.cash)

    cerebro.run()  # execute it all

    # Basic performance evaluation ... final value ... minus starting cash
    pnl = cerebro.broker.get_value() - args.cash
    print('Profit ... or Loss: {:.2f}'.format(pnl))
```

在以下情况下完成：

+   解析参数并使其可用（这显然是可选的，因为一切都可以硬编码，但是良好的实践是好的实践）

+   创建一个`cerebro`引擎实例。是的，这是西班牙语中的“大脑”，是框架的一部分，负责在黑暗中协调编排的操作。虽然它可以接受几个选项，但默认值对于大多数用例来说应该足够了。

+   加载数据文件，使用`args.datadir`的简单目录扫描完成，并使用`NetPayOutData`加载所有文件，并将其添加到`cerebro`实例中

+   添加策略

+   设置现金，默认为`1,000,000`。考虑到使用情况是`100`支股票在`500`支股票的宇宙中，似乎有些现金是可以用的。这也是一个可以更改的参数。

+   并调用`cerebro.run()`

+   最后评估性能

为了能够直接从命令行运行具有不同参数的事务，下面提供了一个启用了`argparse`的样板，其中包含了整个代码

**性能评估**

通过最终结果值的形式添加了一个简单的性能评估，即：最终净资产价值减去起始现金。

*backtrader*生态系统提供了一组内置性能分析器，也可以使用，如：`SharpeRatio`、`Variability-Weighted Return`、`SQN`等。参见[文档 - 分析器参考](https://www.backtrader.com/docu/analyzers-reference/)

## 完整的脚本

最后，作品的大部分呈现为整体。享受吧！

```py
import argparse
import datetime
import glob
import os.path

import backtrader as bt

class NetPayOutData(bt.feeds.GenericCSVData):
    lines = ('npy',)  # add a line containing the net payout yield
    params = dict(
        npy=6,  # npy field is in the 6th column (0 based index)
        dtformat='%Y-%m-%d',  # fix date format a yyyy-mm-dd
        timeframe=bt.TimeFrame.Months,  # fixed the timeframe
        openinterest=-1,  # -1 indicates there is no openinterest field
    )

class St(bt.Strategy):
    params = dict(
        selcperc=0.10,  # percentage of stocks to select from the universe
        rperiod=1,  # period for the returns calculation, default 1 period
        vperiod=36,  # lookback period for volatility - default 36 periods
        mperiod=12,  # lookback period for momentum - default 12 periods
        reserve=0.05  # 5% reserve capital
    )

    def log(self, arg):
        print('{}  {}'.format(self.datetime.date(), arg))

    def __init__(self):
        # calculate 1st the amount of stocks that will be selected
        self.selnum = int(len(self.datas) * self.p.selcperc)

        # allocation perc per stock
        # reserve kept to make sure orders are not rejected due to
        # margin. Prices are calculated when known (close), but orders can only
        # be executed next day (opening price). Price can gap upwards
        self.perctarget = (1.0 - self.p.reserve) / self.selnum

        # returns, volatilities and momentums
        rs = [bt.ind.PctChange(d, period=self.p.rperiod) for d in self.datas]
        vs = [bt.ind.StdDev(ret, period=self.p.vperiod) for ret in rs]
        ms = [bt.ind.ROC(d, period=self.p.mperiod) for d in self.datas]

        # simple rank formula: (momentum * net payout) / volatility
        # the highest ranked: low vol, large momentum, large payout
        self.ranks = {d: d.npy * m / v for d, v, m in zip(self.datas, vs, ms)}

    def next(self):
        # sort data and current rank
        ranks = sorted(
            self.ranks.items(),  # get the (d, rank), pair
            key=lambda x: x[1][0],  # use rank (elem 1) and current time "0"
            reverse=True,  # highest ranked 1st ... please
        )

        # put top ranked in dict with data as key to test for presence
        rtop = dict(ranks[:self.selnum])

        # For logging purposes of stocks leaving the portfolio
        rbot = dict(ranks[self.selnum:])

        # prepare quick lookup list of stocks currently holding a position
        posdata = [d for d, pos in self.getpositions().items() if pos]

        # remove those no longer top ranked
        # do this first to issue sell orders and free cash
        for d in (d for d in posdata if d not in rtop):
            self.log('Leave {} - Rank {:.2f}'.format(d._name, rbot[d][0]))
            self.order_target_percent(d, target=0.0)

        # rebalance those already top ranked and still there
        for d in (d for d in posdata if d in rtop):
            self.log('Rebal {} - Rank {:.2f}'.format(d._name, rtop[d][0]))
            self.order_target_percent(d, target=self.perctarget)
            del rtop[d]  # remove it, to simplify next iteration

        # issue a target order for the newly top ranked stocks
        # do this last, as this will generate buy orders consuming cash
        for d in rtop:
            self.log('Enter {} - Rank {:.2f}'.format(d._name, rtop[d][0]))
            self.order_target_percent(d, target=self.perctarget)

def run(args=None):
    args = parse_args(args)

    cerebro = bt.Cerebro()

    # Data feed kwargs
    dkwargs = dict(**eval('dict(' + args.dargs + ')'))

    # Parse from/to-date
    dtfmt, tmfmt = '%Y-%m-%d', 'T%H:%M:%S'
    if args.fromdate:
        fmt = dtfmt + tmfmt * ('T' in args.fromdate)
        dkwargs['fromdate'] = datetime.datetime.strptime(args.fromdate, fmt)

    if args.todate:
        fmt = dtfmt + tmfmt * ('T' in args.todate)
        dkwargs['todate'] = datetime.datetime.strptime(args.todate, fmt)

    # add all the data files available in the directory datadir
    for fname in glob.glob(os.path.join(args.datadir, '*')):
        data = NetPayOutData(dataname=fname, **dkwargs)
        cerebro.adddata(data)

    # add strategy
    cerebro.addstrategy(St, **eval('dict(' + args.strat + ')'))

    # set the cash
    cerebro.broker.setcash(args.cash)

    cerebro.run()  # execute it all

    # Basic performance evaluation ... final value ... minus starting cash
    pnl = cerebro.broker.get_value() - args.cash
    print('Profit ... or Loss: {:.2f}'.format(pnl))

def parse_args(pargs=None):
    parser = argparse.ArgumentParser(
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
        description=('Rebalancing with the Conservative Formula'),
    )

    parser.add_argument('--datadir', required=True,
                        help='Directory with data files')

    parser.add_argument('--dargs', default='',
                        metavar='kwargs', help='kwargs in k1=v1,k2=v2 format')

    # Defaults for dates
    parser.add_argument('--fromdate', required=False, default='',
                        help='Date[time] in YYYY-MM-DD[THH:MM:SS] format')

    parser.add_argument('--todate', required=False, default='',
                        help='Date[time] in YYYY-MM-DD[THH:MM:SS] format')

    parser.add_argument('--cerebro', required=False, default='',
                        metavar='kwargs', help='kwargs in k1=v1,k2=v2 format')

    parser.add_argument('--cash', default=1000000.0, type=float,
                        metavar='kwargs', help='kwargs in k1=v1,k2=v2 format')

    parser.add_argument('--strat', required=False, default='',
                        metavar='kwargs', help='kwargs in k1=v1,k2=v2 format')

    return parser.parse_args(pargs)

if __name__ == '__main__':
    run()
```
