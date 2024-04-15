# BTFD - 现实的打击

> 原文：[`www.backtrader.com/blog/posts/2016-12-28-btfd-bites/btfd-bites/`](https://www.backtrader.com/blog/posts/2016-12-28-btfd-bites/btfd-bites/)

之前的帖子设法复制了`BTFD`策略，发现真正的收益是`16x`而不是`31x`。

但正如在复制过程中指出的那样：

+   没有收取任何手续费

+   使用`2x`杠杆不收取利息

这引起了一个显而易见的问题：

+   *当收取佣金和利息时，16 倍的收益将有多少？*

幸运的是，之前的样本足够灵活，可以进行实验。 为了得到一些视觉反馈和验证，以下代码将添加到策略中

```py
def start(self):
    print(','.join(['TRADE', 'STATUS', 'Value', 'PNL', 'COMMISSION']))

def notify_order(self, order):
    if order.status in [order.Margin]:
        print('ORDER FAILED with status:', order.getstatusname())

def notify_trade(self, trade):
    if trade.isclosed:
        print(','.join(map(str, [
            'TRADE', 'CLOSE',
            self.data.num2date(trade.dtclose).date().isoformat(),
            trade.value,
            trade.pnl,
            trade.commission,
        ]
        )))
    elif trade.justopened:
        print(','.join(map(str, [
            'TRADE', 'OPEN',
            self.data.num2date(trade.dtopen).date().isoformat(),
            trade.value,
            trade.pnl,
            trade.commission,
        ]
        )))
```

关键在于以下内容：

+   查看交易如何开启和关闭（价值，利润和损失，价值和佣金）

+   如果订单因资金不足而被拒绝，则提供反馈`Margin`

    注意

    因为将有一个调整要投资的金额，以留出佣金的空间，所以一些订单可能无法被经纪人接受。 这种视觉反馈可以帮助识别情况

## 验证

首先快速测试以查看某些订单是否被接受。

```py
$ ./btfd.py --comminfo commission=0.001,leverage=2.0 --strat target=1.0

TRADE,STATUS,Value,PNL,COMMISSION
ORDER FAILED with status: Margin
ORDER FAILED with status: Margin
TRADE,OPEN,1990-01-08,199345.2,0.0,199.3452
TRADE,CLOSE,1990-01-10,0.0,-1460.28,397.23012
```

注意：

+   我们应用`target=1.0`，意味着：*尝试投资 100%的资本*。 这是默认值，但它在这里作为参考。

+   `commission=0.001`或`0.1%`，以确保我们有时会满足保证金

+   前两个订单被拒绝了，显示`Margin`。

+   第三个订单被接受了。 这不是错误。 系统试图投资`100%`的资本，但资产有一个*价格*，这是用来计算股份大小的。 *大小*是从实际可用的*现金*计算出的潜在*大小*的结果进行向下舍入。 这次向下舍入为这个第三个订单留下了足够的空间。

+   交易通知（`OPEN`和`CLOSE`）显示了开仓手续费和最终总手续费，接近`200k`的价值，展示了`2x`杠杆的实际效果。

    开仓手续费为`199.3452`，这是杠杆价值的`0.1%`，即`199,345.2`

剩余的测试将使用`target=0.99x`进行，其中`x`将确保有足够的空间用于选定的手续费。

## 现实的打击

让我们举一些真实的例子

### 目标 99.8% - 佣金 0.1%

```py
./btfd.py --comminfo commission=0.001,leverage=2.0 --strat target=0.998 --plot
```

![image](img/c1469fcb970e66b1e6308b288bad3330.png)

霹雳般的！ 不仅`BTFD`策略远未接近`16x`的收益：**它损失了大部分资金**。

+   从`100,000`下降到大约`4,027`

注意

*下降到*值是非杠杆值，因为这是在平仓时将返回系统的大约值

### 目标 99.9% - 佣金 0.05%

很可能是佣金过于激进。 让我们去一半

```py
./btfd.py --comminfo commission=0.0005,leverage=2.0 --strat target=0.999 --plot
```

![image](img/53135e6fc1bb42bf8182488fc8fedefc.png)

**NO, NO**。佣金并非如此激进，因为系统仍然亏损，从`100,000`下降到约`69,000`（非杠杆价值）

### 目标 99.95% - 佣金 0.025%

佣金再次被除以二

```py
./btfd.py --comminfo commission=0.00025,leverage=2.0 --strat target=0.9995 --plot
```

![image](img/9f8d0eb059dc30ae2845e066e3953597.png)

最终系统赚钱了：

+   最初的`100,000`被用于`3x`增益，增加到`331,459`。

+   但这并不符合资产的表现，该资产已经涨到了超过`600k`

注意

该示例接受`--fromdate YYYY-MM-DD`和`--todate YYYY-MM-DD`来选择应用策略的时间段。这将允许对不同日期范围进行类似的场景测试。

## 结论

当面对佣金时，`16x`的收益无法持续。对于一些经纪人提供的佣金（无上限且按百分比计算），需要非常好的交易才能确保系统赚钱。

在这种情况下，策略应用于`标普 500`，`BTFD`策略与指数的表现不相匹配。

没有应用*利率*。使用佣金已足以看出`16x`距离任何潜在利润有多远。无论如何，以`2%`的利率运行将执行如下

```py
./btfd.py --comminfo commission=0.00025,leverage=2.0,interest=0.02,interest_long=True --strat target=0.9995 --plot
```

`interest_long=True` 是必须的，因为默认情况下收取利息的行为只针对*多头*仓位进行。

## 示例用法

```py
$ ./btfd.py --help
usage: btfd.py [-h] [--offline] [--data TICKER]
               [--fromdate YYYY-MM-DD[THH:MM:SS]]
               [--todate YYYY-MM-DD[THH:MM:SS]] [--cerebro kwargs]
               [--broker kwargs] [--valobserver kwargs] [--strat kwargs]
               [--comminfo kwargs] [--plot [kwargs]]

BTFD - http://dark-bid.com/BTFD-only-strategy-that-matters.html - https://www.
reddit.com/r/algotrading/comments/5jez2b/can_anyone_replicate_this_strategy/

optional arguments:
  -h, --help            show this help message and exit
  --offline             Use offline file with ticker name (default: False)
  --data TICKER         Yahoo ticker to download (default: ^GSPC)
  --fromdate YYYY-MM-DD[THH:MM:SS]
                        Starting date[time] (default: 1990-01-01)
  --todate YYYY-MM-DD[THH:MM:SS]
                        Ending date[time] (default: 2016-10-01)
  --cerebro kwargs      kwargs in key=value format (default: stdstats=False)
  --broker kwargs       kwargs in key=value format (default: cash=100000.0,
                        coc=True)
  --valobserver kwargs  kwargs in key=value format (default:
                        assetstart=100000.0)
  --strat kwargs        kwargs in key=value format (default:
                        approach="highlow")
  --comminfo kwargs     kwargs in key=value format (default: leverage=2.0)
  --plot [kwargs]       kwargs in key=value format (default: )
```

## 示例代码

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

# References:
#  - https://www.reddit.com/r/algotrading/comments/5jez2b/can_anyone_replicate_this_strategy/
#  - http://dark-bid.com/BTFD-only-strategy-that-matters.html

import argparse
import datetime

import backtrader as bt

class ValueUnlever(bt.observers.Value):
    '''Extension of regular Value observer to add leveraged view'''
    lines = ('value_lever', 'asset')
    params = (('assetstart', 100000.0), ('lever', True),)

    def next(self):
        super(ValueUnlever, self).next()
        if self.p.lever:
            self.lines.value_lever[0] = self._owner.broker._valuelever

        if len(self) == 1:
            self.lines.asset[0] = self.p.assetstart
        else:
            change = self.data[0] / self.data[-1]
            self.lines.asset[0] = change * self.lines.asset[-1]

class St(bt.Strategy):
    params = (
        ('fall', -0.01),
        ('hold', 2),
        ('approach', 'highlow'),
        ('target', 1.0)
    )

    def __init__(self):
        if self.p.approach == 'closeclose':
            self.pctdown = self.data.close / self.data.close(-1) - 1.0
        elif self.p.approach == 'openclose':
            self.pctdown = self.data.close / self.data.open - 1.0
        elif self.p.approach == 'highclose':
            self.pctdown = self.data.close / self.data.high - 1.0
        elif self.p.approach == 'highlow':
            self.pctdown = self.data.low / self.data.high - 1.0

    def next(self):
        if self.position:
            if len(self) == self.barexit:
                self.close()
        else:
            if self.pctdown <= self.p.fall:
                self.order_target_percent(target=self.p.target)
                self.barexit = len(self) + self.p.hold

    def start(self):
        print(','.join(['TRADE', 'STATUS', 'Value', 'PNL', 'COMMISSION']))

    def notify_order(self, order):
        if order.status in [order.Margin, order.Rejected, order.Canceled]:
            print('ORDER FAILED with status:', order.getstatusname())

    def notify_trade(self, trade):
        if trade.isclosed:
            print(','.join(map(str, [
                'TRADE', 'CLOSE',
                self.data.num2date(trade.dtclose).date().isoformat(),
                trade.value,
                trade.pnl,
                trade.commission,
            ]
            )))
        elif trade.justopened:
            print(','.join(map(str, [
                'TRADE', 'OPEN',
                self.data.num2date(trade.dtopen).date().isoformat(),
                trade.value,
                trade.pnl,
                trade.commission,
            ]
            )))

def runstrat(args=None):
    args = parse_args(args)

    cerebro = bt.Cerebro()

    # Data feed kwargs
    kwargs = dict()

    # Parse from/to-date
    dtfmt, tmfmt = '%Y-%m-%d', 'T%H:%M:%S'
    for a, d in ((getattr(args, x), x) for x in ['fromdate', 'todate']):
        kwargs[d] = datetime.datetime.strptime(a, dtfmt + tmfmt * ('T' in a))

    if not args.offline:
        YahooData = bt.feeds.YahooFinanceData
    else:
        YahooData = bt.feeds.YahooFinanceCSVData

    # Data feed - no plot - observer will do the job
    data = YahooData(dataname=args.data, plot=False, **kwargs)
    cerebro.adddata(data)

    # Broker
    cerebro.broker = bt.brokers.BackBroker(**eval('dict(' + args.broker + ')'))

    # Add a commission
    cerebro.broker.setcommission(**eval('dict(' + args.comminfo + ')'))

    # Strategy
    cerebro.addstrategy(St, **eval('dict(' + args.strat + ')'))

    # Add specific observer
    cerebro.addobserver(ValueUnlever, **eval('dict(' + args.valobserver + ')'))

    # Execute
    cerebro.run(**eval('dict(' + args.cerebro + ')'))

    if args.plot:  # Plot if requested to
        cerebro.plot(**eval('dict(' + args.plot + ')'))

def parse_args(pargs=None):
    parser = argparse.ArgumentParser(
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
        description=(' - '.join([
            'BTFD',
            'http://dark-bid.com/BTFD-only-strategy-that-matters.html',
            ('https://www.reddit.com/r/algotrading/comments/5jez2b/'
             'can_anyone_replicate_this_strategy/')]))
        )

    parser.add_argument('--offline', required=False, action='store_true',
                        help='Use offline file with ticker name')

    parser.add_argument('--data', required=False, default='^GSPC',
                        metavar='TICKER', help='Yahoo ticker to download')

    parser.add_argument('--fromdate', required=False, default='1990-01-01',
                        metavar='YYYY-MM-DD[THH:MM:SS]',
                        help='Starting date[time]')

    parser.add_argument('--todate', required=False, default='2016-10-01',
                        metavar='YYYY-MM-DD[THH:MM:SS]',
                        help='Ending date[time]')

    parser.add_argument('--cerebro', required=False, default='stdstats=False',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--broker', required=False,
                        default='cash=100000.0, coc=True',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--valobserver', required=False,
                        default='assetstart=100000.0',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--strat', required=False,
                        default='approach="highlow"',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--comminfo', required=False, default='leverage=2.0',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--plot', required=False, default='',
                        nargs='?', const='volume=False',
                        metavar='kwargs', help='kwargs in key=value format')

    return parser.parse_args(pargs)

if __name__ == '__main__':
    runstrat()
```
