# 信用利息

> 原文：[`www.backtrader.com/blog/posts/2016-08-22-credit-interest/credit-interest/`](https://www.backtrader.com/blog/posts/2016-08-22-credit-interest/credit-interest/)

在一些情况下，真实经纪人的现金金额可能会减少，因为资产操作包括利率。例如：

+   股票的空头卖出

+   ETFs 长和短

这意味着不仅交易构成了系统的盈利，因为信用的利息也在消耗账户。

为了涵盖这种情况，*backtrader*在`1.8.8.96`版本开始（功能）可以考虑到这一点。

## 扩展 `CommissionInfo`

即使与任何*order/trade*无关，从账户中扣除现金的折扣可以被建模为经纪人收取的佣金。因此，鉴于*backtrader*已经提供了灵活和可扩展的佣金系统，该系统已稍微扩展以支持*credit interest*。

现在可以使用两个新参数实例化 `CommissionInfo`：

+   `interest`（默认：`0.0`）

    如果这个值不为零，那么这就是持有空头寸时收取的年利率。这主要是为了股票空头卖出

    公式：`days \* price \* abs(size) \* (interest / 365)`

    必须以绝对值指定：0.05 -> 5%

    注意

    通过覆盖方法 `_get_credit_interest` 可以改变行为

+   `interest_long`（默认：`False`）

    一些产品（如 ETFs）无论是空头还是多头都会收取利息。如果这是 `True` 并且 `interest` 不为零，则会对两个方向收取利息。

参数也可以通过*broker*使用以下方法设置：

```py
def setcommission(self,
                  commission=0.0, margin=None, mult=1.0,
                  commtype=None, percabs=True, stocklike=False,
                  interest=0.0, interest_long=False,
                  name=None)
```

其中 `interest` 和 `interest_long` 显然与上文的含义相同。

## 应用一个 `CommissionInfo`

对于佣金百分比的股票，带有*credit interest*的典型使用场景如下

```py
import backtrader as bt

cerebro = bt.Cerebro()
comminfo = bt.CommissionInfo(commtype=bt.CommissionInfo.COMM_PERC,  # % commission
                             commission=0.005,  # 0.5%
                             percabs=True,  # perc expressed in abs terms
                             stocklike=True,
                             interest=0.05,  # 5% anual credit interest rate
                            )

cerebro.broker.addcommissioninfo(comminfo)
...
```

如果最终用户有自己的佣金方案，这将非常有用。

一个更简单的案例与`setcommission`：

```py
import backtrader as bt

cerebro = bt.Cerebro()
cerebro.broker.setcommission(commtype=bt.CommissionInfo.COMM_PERC,  # % commission
                             commission=0.005,  # 0.5%
                             percabs=True,  # perc expressed in abs terms
                             stocklike=True,
                             interest=0.05,  # 5% anual credit interest rate
                            )

...
```

其余的和任何其他常规的*backtrader*脚本一样。

## 一些样本场景

### 仅限`long`，无退出，无利息

为了建立一个最低基线，让我们先不要利息，只让脚本进入市场并避免退出。

```py
$ ./credit-interest.py --plot --stocklike --long --no-exit
01 2005-04-11 23:59:59 BUY  Size: +10 / Price: 3088.47
```

![image](img/aab6804c68219829704d595573a5bd8d.png)

现在应该很清楚了。从总投资组合价值中有一条平直的现金线，不显示任何扣除。

### 仅限`long`，无退出和利息

让我们尝试添加利息，看看会发生什么（我们将添加一个巨大的`15%`利息来尝试注意到这些动向）

```py
$ ./credit-interest.py --plot --stocklike --long --no-exit --interest 0.15
01 2005-04-11 23:59:59 BUY  Size: +10 / Price: 3088.47
```

![image](img/d0ce920dc1c754919fb336946b4d9e85.png)

什么都没变！这是意料之中的。在大多数情况下，利息只适用于`short`头寸（使用信用卖出）而这是一个`long-only`头寸。

让我们告诉脚本也要为`long`头寸做这个

```py
$ ./credit-interest.py --plot --stocklike --long --no-exit --interest 0.15 --interest_long
01 2005-04-11 23:59:59 BUY  Size: +10 / Price: 3088.47
```

![image](img/7947fc049a3af4a9caf33d2419b0db7f.png)

改变已经存在。有一个减少，并且很大（鉴于正在收取的利息很大）

### 多空头场景

这将模拟类似于具有年度利息的`ETF`，可以是常规的或反向的。首先让我们建立基线。

```py
$ ./credit-interest.py --plot --stocklike
01 2005-03-22 23:59:59 SELL Size: -10 / Price: 3040.55
02 2005-04-11 23:59:59 BUY  Size: +10 / Price: 3088.47
...
...
34 2006-12-19 23:59:59 BUY  Size: +10 / Price: 4121.01
35 2006-12-19 23:59:59 BUY  Size: +10 / Price: 4121.01
```

还有更多操作，系统始终*在市场中*。

![图片](img/0927d8c10dd53795660d83b38793082c.png)

由于`ETF`将对多头和空头操作收取利息，现在利息将同时增加：

```py
$ ./credit-interest.py --plot --stocklike --interest 0.15 --interest_long
01 2005-03-22 23:59:59 SELL Size: -10 / Price: 3040.55
02 2005-04-11 23:59:59 BUY  Size: +10 / Price: 3088.47
...
...
34 2006-12-19 23:59:59 BUY  Size: +10 / Price: 4121.01
```

![图片](img/b7ac7979c5ba9cd44a9c9b23ce9247fc.png)

*注意*：`34`次操作而不是`35`。似乎有些地方可能出了问题，但是……没有……

收取的*利息*会稍微减少现金储备，最终导致最后一个订单无法执行，因为*现金不足*

从*多头*操作中去除利息费用（即使对于*ETF*来说并非真实）将使系统能够结束：

```py
$ ./credit-interest.py --plot --stocklike --interest 0.15
01 2005-03-22 23:59:59 SELL Size: -10 / Price: 3040.55
02 2005-04-11 23:59:59 BUY  Size: +10 / Price: 3088.47
...
...
34 2006-12-19 23:59:59 BUY  Size: +10 / Price: 4121.01
35 2006-12-19 23:59:59 BUY  Size: +10 / Price: 4121.01
```

回到业务，直到第`35`次操作。

![图片](img/740588603e4b742d38921d599e96364c.png)

与原始数据快速比较显示，最终现金从`7490`（不计利息）变为`5418`（仅对空头操作应用利息）

## 结论

这种新功能允许更真实地模拟回测场景，以尝试实现梦想：一个盈利系统

## 示例用法

```py
$ ./credit-interest.py --help
usage: credit-interest.py [-h] [--data DATA] [--fromdate FROMDATE]
                          [--todate TODATE] [--cash CASH] [--period1 PERIOD1]
                          [--period2 PERIOD2] [--interest INTEREST]
                          [--interest_long] [--long | --short] [--no-exit]
                          [--stocklike] [--margin MARGIN] [--mult MULT]
                          [--stake STAKE] [--plot [kwargs]]

Sample for Slippage

optional arguments:
  -h, --help            show this help message and exit
  --data DATA           Specific data to be read in (default:
                        ../../datas/2005-2006-day-001.txt)
  --fromdate FROMDATE   Starting date in YYYY-MM-DD format (default: None)
  --todate TODATE       Ending date in YYYY-MM-DD format (default: None)
  --cash CASH           Cash to start with (default: 50000)
  --period1 PERIOD1     Fast moving average period (default: 10)
  --period2 PERIOD2     Slow moving average period (default: 30)
  --interest INTEREST   Activate credit interest rate (default: 0.0)
  --interest_long       Credit interest rate for long positions (default:
                        False)
  --long                Do a long only strategy (default: False)
  --short               Do a long only strategy (default: False)
  --no-exit             The 1st taken position will not be exited (default:
                        False)
  --stocklike           Consider the asset to be stocklike (default: False)
  --margin MARGIN       Margin for future like instruments (default: 0.0)
  --mult MULT           Multiplier for future like instruments (default: 1.0)
  --stake STAKE         Stake to apply (default: 10)
  --plot [kwargs], -p [kwargs]
                        Plot the read data applying any kwargs passed For
                        example: --plot style="candle" (to plot candles)
                        (default: None)
```

## 示例代码

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
import collections
import datetime
import itertools

import backtrader as bt

class SMACrossOver(bt.Signal):
    params = (('p1', 10), ('p2', 30),)

    def __init__(self):
        sma1 = bt.indicators.SMA(period=self.p.p1)
        sma2 = bt.indicators.SMA(period=self.p.p2)
        self.lines.signal = bt.indicators.CrossOver(sma1, sma2)

class NoExit(bt.Signal):
    def next(self):
        self.lines.signal[0] = 0.0

class St(bt.SignalStrategy):
    opcounter = itertools.count(1)

    def notify_order(self, order):
        if order.status == bt.Order.Completed:
            t = ''
            t += '{:02d}'.format(next(self.opcounter))
            t += ' {}'.format(order.data.datetime.datetime())
            t += ' BUY ' * order.isbuy() or ' SELL'
            t += ' Size: {:+d} / Price: {:.2f}'
            print(t.format(order.executed.size, order.executed.price))

def runstrat(args=None):
    args = parse_args(args)

    cerebro = bt.Cerebro()
    cerebro.broker.set_cash(args.cash)

    dkwargs = dict()
    if args.fromdate is not None:
        fromdate = datetime.datetime.strptime(args.fromdate, '%Y-%m-%d')
        dkwargs['fromdate'] = fromdate

    if args.todate is not None:
        todate = datetime.datetime.strptime(args.todate, '%Y-%m-%d')
        dkwargs['todate'] = todate

    # if dataset is None, args.data has been given
    data = bt.feeds.BacktraderCSVData(dataname=args.data, **dkwargs)
    cerebro.adddata(data)

    cerebro.signal_strategy(St)
    cerebro.addsizer(bt.sizers.FixedSize, stake=args.stake)

    sigtype = bt.signal.SIGNAL_LONGSHORT
    if args.long:
        sigtype = bt.signal.SIGNAL_LONG
    elif args.short:
        sigtype = bt.signal.SIGNAL_SHORT

    cerebro.add_signal(sigtype,
                       SMACrossOver, p1=args.period1, p2=args.period2)

    if args.no_exit:
        if args.long:
            cerebro.add_signal(bt.signal.SIGNAL_LONGEXIT, NoExit)
        elif args.short:
            cerebro.add_signal(bt.signal.SIGNAL_SHORTEXIT, NoExit)

    comminfo = bt.CommissionInfo(
        mult=args.mult,
        margin=args.margin,
        stocklike=args.stocklike,
        interest=args.interest,
        interest_long=args.interest_long)

    if True:
        cerebro.broker.addcommissioninfo(comminfo)

    cerebro.run()
    if args.plot:
        pkwargs = dict(style='bar')
        if args.plot is not True:  # evals to True but is not True
            npkwargs = eval('dict(' + args.plot + ')')  # args were passed
            pkwargs.update(npkwargs)

        cerebro.plot(**pkwargs)

def parse_args(pargs=None):

    parser = argparse.ArgumentParser(
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
        description='Sample for Slippage')

    parser.add_argument('--data', required=False,
                        default='../../datas/2005-2006-day-001.txt',
                        help='Specific data to be read in')

    parser.add_argument('--fromdate', required=False, default=None,
                        help='Starting date in YYYY-MM-DD format')

    parser.add_argument('--todate', required=False, default=None,
                        help='Ending date in YYYY-MM-DD format')

    parser.add_argument('--cash', required=False, action='store',
                        type=float, default=50000,
                        help=('Cash to start with'))

    parser.add_argument('--period1', required=False, action='store',
                        type=int, default=10,
                        help=('Fast moving average period'))

    parser.add_argument('--period2', required=False, action='store',
                        type=int, default=30,
                        help=('Slow moving average period'))

    parser.add_argument('--interest', required=False, action='store',
                        default=0.0, type=float,
                        help=('Activate credit interest rate'))

    parser.add_argument('--interest_long', required=False, action='store_true',
                        help=('Credit interest rate for long positions'))

    pgroup = parser.add_mutually_exclusive_group()
    pgroup.add_argument('--long', required=False, action='store_true',
                        help=('Do a long only strategy'))

    pgroup.add_argument('--short', required=False, action='store_true',
                        help=('Do a long only strategy'))

    parser.add_argument('--no-exit', required=False, action='store_true',
                        help=('The 1st taken position will not be exited'))

    parser.add_argument('--stocklike', required=False, action='store_true',
                        help=('Consider the asset to be stocklike'))

    parser.add_argument('--margin', required=False, action='store',
                        default=0.0, type=float,
                        help=('Margin for future like instruments'))

    parser.add_argument('--mult', required=False, action='store',
                        default=1.0, type=float,
                        help=('Multiplier for future like instruments'))

    parser.add_argument('--stake', required=False, action='store',
                        default=10, type=int,
                        help=('Stake to apply'))

    # Plot options
    parser.add_argument('--plot', '-p', nargs='?', required=False,
                        metavar='kwargs', const=True,
                        help=('Plot the read data applying any kwargs passed\n'
                              '\n'
                              'For example:\n'
                              '\n'
                              '  --plot style="candle" (to plot candles)\n'))

    if pargs is not None:
        return parser.parse_args(pargs)

    return parser.parse_args()

if __name__ == '__main__':
    runstrat()
```
