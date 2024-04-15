# 扩展佣金

> 原文：[`www.backtrader.com/blog/posts/2015-11-05-commission-schemes-extended/commission-schemes-extended/`](https://www.backtrader.com/blog/posts/2015-11-05-commission-schemes-extended/commission-schemes-extended/)

佣金和相关功能由一个单独的类`CommissionInfo`管理，该类主要通过调用`broker.setcommission`来实例化。

有一些帖子讨论了这种行为。

+   佣金：股票 vs 期货

+   改进佣金：股票 vs 期货

这个概念仅限于具有保证金的期货和具有基于价格/大小百分比的佣金的股票。 即使它已经达到了它的目的，它也不是最灵活的方案。

我自己的实现中只有一件事我不喜欢，那就是`CommissionInfo`将百分比值以绝对值（0.xx）而不是相对值（xx%）的方式传递

GitHub 的增强请求[#29](https://github.com/mementum/backtrader/issues/29)导致一些重新设计，以：

+   保持`CommissionInfo`和`broker.setcommission`与原始行为兼容

+   对代码进行清理

+   使佣金方案灵活以支持增强请求和进一步可能性

进入示例之前的实际工作：

```py
`class CommInfoBase(with_metaclass(MetaParams)):
    COMM_PERC, COMM_FIXED = range(2)

    params = (
        ('commission', 0.0), ('mult', 1.0), ('margin', None),
        ('commtype', None),
        ('stocklike', False),
        ('percabs', False),
    )` 
```

引入了一个`CommissionInfo`的基类，将新参数添加到混合中：

+   `commtype`（默认：None）

    这是兼容性的关键。 如果值为`None`，则`CommissionInfo`对象和`broker.setcommission`的行为将与以前相同。 即：

    +   如果设置了`margin`，则佣金方案适用于具有固定合约佣金的期货

    +   如果未设置`margin`，则佣金方案适用于具有基于百分比的股票方法

    如果值为`COMM_PERC`或`COMM_FIXED`（或派生类中的任何其他值），则这显然决定了佣金是固定的还是基于百分比的

+   `stocklike`（默认：False）

    如上所述，旧的`CommissionInfo`对象中的实际行为由参数`margin`决定

    如上所述，如果将`commtype`设置为除`None`之外的其他值，则此值指示资产是否为类似期货的资产（将使用保证金，并执行基于条的现金调整）或者这是类似股票的资产

+   `percabs`（默认：False）

    如果为`False`，则百分比必须以相对术语传递（xx%）

    如果为`True`，则百分比必须以绝对值（0.xx）传递

    `CommissionInfo`是从`CommInfoBase`派生的，将此参数的默认值更改为`True`以保持兼容的行为

所有这些参数也可以在`broker.setcommission`中使用，现在看起来像这样：

```py
`def setcommission(self,
                  commission=0.0, margin=None, mult=1.0,
                  commtype=None, percabs=True, stocklike=False,
                  name=None):` 
```

请注意以下内容：

+   `percabs`设置为`True`，以保持与旧调用的`CommissionInfo`对象上述行为的兼容

重新设计了用于测试`commissions-schemes`的旧样本，以支持命令行参数和新行为。 使用帮助：

```py
`$ ./commission-schemes.py --help
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
                        Plot using numfigs figures (default: 1)` 
```

让我们进行一些运行，以重新创建原始佣金方案帖子的原始行为。

## 期货的佣金（固定和带保证金）

执行和图表：

```py
`$ ./commission-schemes.py --comm 2.0 --margin 2000.0 --mult 10 --plot` 
```

![图片](img/fbd827df0660d893b0e63f45f90f0a9b.png)

并且输出显示固定佣金为 2.0 货币单位（默认押注为 1）：

```py
`2006-03-09, BUY CREATE, 3757.59
2006-03-10, BUY EXECUTED, Price: 3754.13, Cost: 2000.00, Comm 2.00
2006-04-11, SELL CREATE, 3788.81
2006-04-12, SELL EXECUTED, Price: 3786.93, Cost: 2000.00, Comm 2.00
2006-04-12, TRADE PROFIT, GROSS 328.00, NET 324.00
...` 
```

## 股票的佣金（百分比和无保证金）

执行和图表：

```py
`$ ./commission-schemes.py --comm 0.005 --margin 0 --mult 1 --plot` 
```

![图片](img/ec6b394b2b30dc1b4cab16d7ab6b36dd.png)

为了提高可读性，可以使用相对%值：

```py
`$ ./commission-schemes.py --percrel --comm 0.5 --margin 0 --mult 1 --plot` 
```

现在`0.5`直接表示`0.5%`

输出在两种情况下都是：

```py
`2006-03-09, BUY CREATE, 3757.59
2006-03-10, BUY EXECUTED, Price: 3754.13, Cost: 3754.13, Comm 18.77
2006-04-11, SELL CREATE, 3788.81
2006-04-12, SELL EXECUTED, Price: 3786.93, Cost: 3754.13, Comm 18.93
2006-04-12, TRADE PROFIT, GROSS 32.80, NET -4.91
...` 
```

## 期货的佣金（百分比和带保证金）

使用新参数，基于百分比的期货：

```py
`$ ./commission-schemes.py --commtype perc --percrel --comm 0.5 --margin 2000 --mult 10 --plot` 
```

![图片](img/ec6b394b2b30dc1b4cab16d7ab6b36dd.png)

改变佣金...最终结果发生变化并不奇怪

输出显示佣金现在是可变的：

```py
`2006-03-09, BUY CREATE, 3757.59
2006-03-10, BUY EXECUTED, Price: 3754.13, Cost: 2000.00, Comm 18.77
2006-04-11, SELL CREATE, 3788.81
2006-04-12, SELL EXECUTED, Price: 3786.93, Cost: 2000.00, Comm 18.93
2006-04-12, TRADE PROFIT, GROSS 328.00, NET 290.29
...` 
```

在上一次运行中设置了 2.0 货币单位（默认押注为 1）

另一篇文章将详细介绍新的类别和自制佣金方案的实施。

## 示例代码

```py
`from __future__ import (absolute_import, division, print_function,
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
    runstrategy()` 
```
