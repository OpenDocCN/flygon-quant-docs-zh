# 扩展佣金

> 原文：[`www.backtrader.com/docu/extending-commissions/commission-schemes-extended/`](https://www.backtrader.com/docu/extending-commissions/commission-schemes-extended/)

佣金和相关功能由一个名为`CommissionInfo`的单个类管理，该类大部分通过调用`broker.setcommission`实例化。

该概念仅限于具有保证金和按合同固定佣金的期货，以及具有基于价格/数量百分比的股票。即使已实现其目的，也不是最灵活的方案。

GitHub 上的增强请求[#29](https://github.com/mementum/backtrader/issues/29)导致一些重写，以便：

+   保持`CommissionInfo`和`broker.setcommission`与原始行为兼容。

+   对代码进行一些清理

+   使佣金方案灵活以支持增强请求和进一步的可能性

在进入示例之前的实际工作

```py
class CommInfoBase(with_metaclass(MetaParams)):
    COMM_PERC, COMM_FIXED = range(2)

    params = (
        ('commission', 0.0), ('mult', 1.0), ('margin', None),
        ('commtype', None),
        ('stocklike', False),
        ('percabs', False),
    )
```

引入了`CommissionInfo`的基类，它将新参数添加到混合中：

+   `commtype`（默认：`None`）

    这是兼容性的关键。如果值为`None`，则`CommissionInfo`对象和`broker.setcommission`的行为将与以前相同。即：

    +   如果设置了`margin`，则佣金方案适用于具有按合同固定佣金的期货

    +   如果未设置`margin`，则佣金方案适用于采用百分比方法的股票。

    如果值为`COMM_PERC`或`COMM_FIXED`（或从派生类中的任何其他值），这显然决定了佣金是否是固定的还是基于百分比的。

+   `stocklike`（默认：`False`）

    如上所述，旧的`CommissionInfo`对象中的实际行为由参数`margin`确定

    如果`commtype`设置为`None`之外的其他内容，则此值指示资产是否为类似期货的资产（将使用保证金并进行基于条形的现金调整），否则这是类似股票的资产。

+   `percabs`（默认：`False`）

    如果为`False`，则百分比必须以相对形式传递（xx%）。

    如果为`True`，则百分比必须作为绝对值传递（0.xx）

    `CommissionInfo`是从`CommInfoBase`继承的，将此参数的默认值更改为`True`以保持兼容的行为。

所有这些参数也可以在`broker.setcommission`中使用，现在看起来像这样：

```py
def setcommission(self,
                  commission=0.0, margin=None, mult=1.0,
                  commtype=None, percabs=True, stocklike=False,
                  name=None):
```

请注意以下内容：

+   `percabs`为`True`，以保持与旧调用的兼容行为，如上所述对于`CommissionInfo`对象

用于测试`commissions-schemes`的旧示例已重写以支持命令行参数和新行为。用法帮助：

```py
$ ./commission-schemes.py --help
usage: commission-schemes.py [-h] [--data DATA] [--fromdate FROMDATE]
                             [--todate TODATE] [--stake STAKE]
                             [--period PERIOD] [--cash CASH] [--comm COMM]
                             [--mult MULT] [--margin MARGIN]
                             [--commtype {none,perc,fixed}] [--stocklike]
                             [--percrel] [--plot] [--numfigs NUMFIGS]

Commission schemes

optional arguments:
  -h, --help            show this help message and exit
  --data DATA, -d DATA  data to add to the system (default:
                        ../../datas/2006-day-001.txt)
  --fromdate FROMDATE, -f FROMDATE
                        Starting date in YYYY-MM-DD format (default:
                        2006-01-01)
  --todate TODATE, -t TODATE
                        Starting date in YYYY-MM-DD format (default:
                        2006-12-31)
  --stake STAKE         Stake to apply in each operation (default: 1)
  --period PERIOD       Period to apply to the Simple Moving Average (default:
                        30)
  --cash CASH           Starting Cash (default: 10000.0)
  --comm COMM           Commission factor for operation, either apercentage or
                        a per stake unit absolute value (default: 2.0)
  --mult MULT           Multiplier for operations calculation (default: 10)
  --margin MARGIN       Margin for futures-like operations (default: 2000.0)
  --commtype {none,perc,fixed}
                        Commission - choose none for the old CommissionInfo
                        behavior (default: none)
  --stocklike           If the operation is for stock-like assets orfuture-
                        like assets (default: False)
  --percrel             If perc is expressed in relative xx{'const': True,
                        'help': u'If perc is expressed in relative xx%
                        ratherthan absolute value 0.xx', 'option_strings': [u'
                        --percrel'], 'dest': u'percrel', 'required': False,
                        'nargs': 0, 'choices': None, 'default': False, 'prog':
                        'commission-schemes.py', 'container':
                        <argparse._ArgumentGroup object at
                        0x0000000007EC9828>, 'type': None, 'metavar':
                        None}atherthan absolute value 0.xx (default: False)
  --plot, -p            Plot the read data (default: False)
  --numfigs NUMFIGS, -n NUMFIGS
                        Plot using numfigs figures (default: 1)
```

让我们进行一些运行以重现原始佣金方案贴文的原始行为。

## 期货佣金（固定和有保证金）

执行和图表：

```py
$ ./commission-schemes.py --comm 2.0 --margin 2000.0 --mult 10 --plot
```

![image](img/3b34821f141f4b6846b890b1429d0309.png)

并且输出显示固定佣金为 2.0 货币单位（默认的投注是 1）：

```py
2006-03-09, BUY CREATE, 3757.59
2006-03-10, BUY EXECUTED, Price: 3754.13, Cost: 2000.00, Comm 2.00
2006-04-11, SELL CREATE, 3788.81
2006-04-12, SELL EXECUTED, Price: 3786.93, Cost: 2000.00, Comm 2.00
2006-04-12, TRADE PROFIT, GROSS 328.00, NET 324.00
...
```

## 股票佣金（带和不带保证金的百分比）

执行和图表：

```py
$ ./commission-schemes.py --comm 0.005 --margin 0 --mult 1 --plot
```

![图片](img/f71981f061c32c988b10695da5e75c81.png)

为了提高可读性，可以使用相对百分比值：

```py
$ ./commission-schemes.py --percrel --comm 0.5 --margin 0 --mult 1 --plot
```

现在`0.5`直接意味着`0.5％`

在两种情况下输出为：

```py
2006-03-09, BUY CREATE, 3757.59
2006-03-10, BUY EXECUTED, Price: 3754.13, Cost: 3754.13, Comm 18.77
2006-04-11, SELL CREATE, 3788.81
2006-04-12, SELL EXECUTED, Price: 3786.93, Cost: 3754.13, Comm 18.93
2006-04-12, TRADE PROFIT, GROSS 32.80, NET -4.91
...
```

## 期货的佣金（百分比和有保证金）

使用新参数，期货的基于百分比的方案：

```py
$ ./commission-schemes.py --commtype perc --percrel --comm 0.5 --margin 2000 --mult 10 --plot
```

![图片](img/f71981f061c32c988b10695da5e75c81.png)

改变佣金……最终结果改变也就不足为奇了。

输出显示佣金现在是可变的：

```py
2006-03-09, BUY CREATE, 3757.59
2006-03-10, BUY EXECUTED, Price: 3754.13, Cost: 2000.00, Comm 18.77
2006-04-11, SELL CREATE, 3788.81
2006-04-12, SELL EXECUTED, Price: 3786.93, Cost: 2000.00, Comm 18.93
2006-04-12, TRADE PROFIT, GROSS 328.00, NET 290.29
...
```

在先前的运行中设置了 2.0 货币单位（默认投注为 1）

另一篇文章将详细介绍新类别和一种自制佣金方案的实现。

## 示例的代码

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
import datetime

import backtrader as bt
import backtrader.feeds as btfeeds
import backtrader.indicators as btind

class SMACrossOver(bt.Strategy):
    params = (
        ('stake', 1),
        ('period', 30),
    )

    def log(self, txt, dt=None):
  ''' Logging function fot this strategy'''
        dt = dt or self.datas[0].datetime.date(0)
        print('%s, %s' % (dt.isoformat(), txt))

    def notify_order(self, order):
        if order.status in [order.Submitted, order.Accepted]:
            # Buy/Sell order submitted/accepted to/by broker - Nothing to do
            return

        # Check if an order has been completed
        # Attention: broker could reject order if not enougth cash
        if order.status in [order.Completed, order.Canceled, order.Margin]:
            if order.isbuy():
                self.log(
                    'BUY EXECUTED, Price: %.2f, Cost: %.2f, Comm %.2f' %
                    (order.executed.price,
                     order.executed.value,
                     order.executed.comm))
            else:  # Sell
                self.log('SELL EXECUTED, Price: %.2f, Cost: %.2f, Comm %.2f' %
                         (order.executed.price,
                          order.executed.value,
                          order.executed.comm))

    def notify_trade(self, trade):
        if trade.isclosed:
            self.log('TRADE PROFIT, GROSS %.2f, NET %.2f' %
                     (trade.pnl, trade.pnlcomm))

    def __init__(self):
        sma = btind.SMA(self.data, period=self.p.period)
        # > 0 crossing up / < 0 crossing down
        self.buysell_sig = btind.CrossOver(self.data, sma)

    def next(self):
        if self.buysell_sig > 0:
            self.log('BUY CREATE, %.2f' % self.data.close[0])
            self.buy(size=self.p.stake)  # keep order ref to avoid 2nd orders

        elif self.position and self.buysell_sig < 0:
            self.log('SELL CREATE, %.2f' % self.data.close[0])
            self.sell(size=self.p.stake)

def runstrategy():
    args = parse_args()

    # Create a cerebro
    cerebro = bt.Cerebro()

    # Get the dates from the args
    fromdate = datetime.datetime.strptime(args.fromdate, '%Y-%m-%d')
    todate = datetime.datetime.strptime(args.todate, '%Y-%m-%d')

    # Create the 1st data
    data = btfeeds.BacktraderCSVData(
        dataname=args.data,
        fromdate=fromdate,
        todate=todate)

    # Add the 1st data to cerebro
    cerebro.adddata(data)

    # Add a strategy
    cerebro.addstrategy(SMACrossOver, period=args.period, stake=args.stake)

    # Add the commission - only stocks like a for each operation
    cerebro.broker.setcash(args.cash)

    commtypes = dict(
        none=None,
        perc=bt.CommInfoBase.COMM_PERC,
        fixed=bt.CommInfoBase.COMM_FIXED)

    # Add the commission - only stocks like a for each operation
    cerebro.broker.setcommission(commission=args.comm,
                                 mult=args.mult,
                                 margin=args.margin,
                                 percabs=not args.percrel,
                                 commtype=commtypes[args.commtype],
                                 stocklike=args.stocklike)

    # And run it
    cerebro.run()

    # Plot if requested
    if args.plot:
        cerebro.plot(numfigs=args.numfigs, volume=False)

def parse_args():
    parser = argparse.ArgumentParser(
        description='Commission schemes',
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,)

    parser.add_argument('--data', '-d',
                        default='../../datas/2006-day-001.txt',
                        help='data to add to the system')

    parser.add_argument('--fromdate', '-f',
                        default='2006-01-01',
                        help='Starting date in YYYY-MM-DD format')

    parser.add_argument('--todate', '-t',
                        default='2006-12-31',
                        help='Starting date in YYYY-MM-DD format')

    parser.add_argument('--stake', default=1, type=int,
                        help='Stake to apply in each operation')

    parser.add_argument('--period', default=30, type=int,
                        help='Period to apply to the Simple Moving Average')

    parser.add_argument('--cash', default=10000.0, type=float,
                        help='Starting Cash')

    parser.add_argument('--comm', default=2.0, type=float,
                        help=('Commission factor for operation, either a'
                              'percentage or a per stake unit absolute value'))

    parser.add_argument('--mult', default=10, type=int,
                        help='Multiplier for operations calculation')

    parser.add_argument('--margin', default=2000.0, type=float,
                        help='Margin for futures-like operations')

    parser.add_argument('--commtype', required=False, default='none',
                        choices=['none', 'perc', 'fixed'],
                        help=('Commission - choose none for the old'
                              ' CommissionInfo behavior'))

    parser.add_argument('--stocklike', required=False, action='store_true',
                        help=('If the operation is for stock-like assets or'
                              'future-like assets'))

    parser.add_argument('--percrel', required=False, action='store_true',
                        help=('If perc is expressed in relative xx% rather'
                              'than absolute value 0.xx'))

    parser.add_argument('--plot', '-p', action='store_true',
                        help='Plot the read data')

    parser.add_argument('--numfigs', '-n', default=1,
                        help='Plot using numfigs figures')

    return parser.parse_args()

if __name__ == '__main__':
    runstrategy()
```
