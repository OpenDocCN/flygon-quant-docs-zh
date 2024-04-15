# StopTrail(Limit)

> 原文：[`www.backtrader.com/docu/order-creation-execution/trail/stoptrail/`](https://www.backtrader.com/docu/order-creation-execution/trail/stoptrail/)

版本`1.9.35.116`将`StopTrail`和`StopTrailLimit`订单执行类型添加到回测武器库。

注意

这仅在回测中实现，尚未针对实时经纪人实现

注意

用版本`1.9.36.116`更新。交互式经纪人支持`StopTrail`、`StopTrailLimit`和`OCO`。

+   `OCO`总是将第 1 个订单作为参数`oco`指定为一组

+   `StopTrailLimit`：经纪人模拟和`IB`经纪人具有相同的行为。指定：`price`作为初始触发价格（也指定`trailamount`），然后`plimi`作为初始限价。两者之间的差异将确定`limitoffset`（限价与停止触发价格之间的距离）

用法模式完全集成到策略实例的标准`buy`、`sell`和`close`市场操作方法中。注意：

+   指示希望的执行类型，如`exectype=bt.Order.StopTrail`

+   以及是否必须使用固定距离或百分比距离计算跟踪价格

    +   固定距离：`trailamount=10`

    +   百分比距离：`trailpercent=0.02`（即：`2%`）

如果通过发出`buy`进入市场，这是`sell`与`StopTrail`和`trailamount`一起的操作：

+   如果未指定`price`，则使用最新的`close`价格

+   `trailamount`从价格中减去以找到`stop`（或触发）价格

+   下一个迭代的*经纪人*检查触发价格是否已达到

    +   如果**是**：订单以`Market`执行类型的方法执行

    +   如果**否**，则使用最新的`close`价格减去`trailamount`距离重新计算`stop`价格

    +   如果新价格上涨，则更新

    +   如果新价格将下跌（或根本不变），则将其丢弃

也就是说：跟踪止损价格随价格上涨而上涨，但如果价格开始下跌，则保持不变，以潜在地获利。

如果通过`sell`进入市场，然后通过`StopTrail`发出`buy`订单只是做相反的操作，即：价格向下跟随。

一些用法模式

```py
# For a StopTrail going downwards
# last price will be used as reference
self.buy(size=1, exectype=bt.Order.StopTrail, trailamount=0.25)
# or
self.buy(size=1, exectype=bt.Order.StopTrail, price=10.50, trailamount=0.25)

# For a StopTrail going upwards
# last price will be used as reference
self.sell(size=1, exectype=bt.Order.StopTrail, trailamount=0.25)
# or
self.sell(size=1, exectype=bt.Order.StopTrail, price=10.50, trailamount=0.25)
```

也可以指定`trailpercent`而不是`trailamount`，价格与价格的距离将被计算为价格的百分比

```py
# For a StopTrail going downwards with 2% distance
# last price will be used as reference
self.buy(size=1, exectype=bt.Order.StopTrail, trailpercent=0.02)
# or
self.buy(size=1, exectype=bt.Order.StopTrail, price=10.50, trailpercent=0.0.02)

# For a StopTrail going upwards with 2% difference
# last price will be used as reference
self.sell(size=1, exectype=bt.Order.StopTrail, trailpercent=0.02)
# or
self.sell(size=1, exectype=bt.Order.StopTrail, price=10.50, trailpercent=0.02)
```

对于`StopTrailLimit`

+   唯一的区别在于当触发跟踪止损价格时会发生什么。

+   在这种情况下，订单以`Limit`订单的形式执行（与`StopLimit`订单的行为相同，但在这种情况下，触发价格是动态的）

    **注意**：必须指定`plimit=x.x`给`buy`或`sell`，这将是*限价*

    **注意**：*限价*不像停止/触发价格那样动态更改

例如总是值得一看，因此通常的*backtrader*示例，其中

+   使用移动平均线上穿进入市场多头

+   使用跟踪止损退出市场

使用`50`点固定价格距离的执行

```py
$ ./trail.py --plot --strat trailamount=50.0
```

这产生了以下图表

![image](img/551fb950e5b5964370cf4a2c884e6a9f.png)

和以下输出：

```py
**************************************************
2005-02-14,3075.76,3025.76,3025.76
----------
2005-02-15,3086.95,3036.95,3036.95
2005-02-16,3068.55,3036.95,3018.55
2005-02-17,3067.34,3036.95,3017.34
2005-02-18,3072.04,3036.95,3022.04
2005-02-21,3063.64,3036.95,3013.64
...
...
**************************************************
2005-05-19,3051.79,3001.79,3001.79
----------
2005-05-20,3050.45,3001.79,3000.45
2005-05-23,3070.98,3020.98,3020.98
2005-05-24,3066.55,3020.98,3016.55
2005-05-25,3059.84,3020.98,3009.84
2005-05-26,3086.08,3036.08,3036.08
2005-05-27,3084.0,3036.08,3034.0
2005-05-30,3096.54,3046.54,3046.54
2005-05-31,3076.75,3046.54,3026.75
2005-06-01,3125.88,3075.88,3075.88
2005-06-02,3131.03,3081.03,3081.03
2005-06-03,3114.27,3081.03,3064.27
2005-06-06,3099.2,3081.03,3049.2
2005-06-07,3134.82,3084.82,3084.82
2005-06-08,3125.59,3084.82,3075.59
2005-06-09,3122.93,3084.82,3072.93
2005-06-10,3143.85,3093.85,3093.85
2005-06-13,3159.83,3109.83,3109.83
2005-06-14,3162.86,3112.86,3112.86
2005-06-15,3147.55,3112.86,3097.55
2005-06-16,3160.09,3112.86,3110.09
2005-06-17,3178.48,3128.48,3128.48
2005-06-20,3162.14,3128.48,3112.14
2005-06-21,3179.62,3129.62,3129.62
2005-06-22,3182.08,3132.08,3132.08
2005-06-23,3190.8,3140.8,3140.8
2005-06-24,3161.0,3140.8,3111.0
...
...
...
**************************************************
2006-12-19,4100.48,4050.48,4050.48
----------
2006-12-20,4118.54,4068.54,4068.54
2006-12-21,4112.1,4068.54,4062.1
2006-12-22,4073.5,4068.54,4023.5
2006-12-27,4134.86,4084.86,4084.86
2006-12-28,4130.66,4084.86,4080.66
2006-12-29,4119.94,4084.86,4069.94
```

而不是等待通常的下穿模式，系统使用跟踪止损退出市场。例如，让我们看看第一次操作。

+   进入多头时的收盘价：`3075.76`

+   系统计算的跟踪止损价：`3025.76`（相距`50`个单位）

+   样本计算的跟踪止损价：`3025.76`（每行显示的最后价格）

在第一次计算之后：

+   收盘价上涨至`3086.95`，止损价调整为`3036.95`

+   以下收盘价不超过`3086.95`，触发价格不变

在其他两次操作中也可以看到相同的模式。

为了比较，仅使用`30`点固定距离的执行（仅图表）

```py
$ ./trail.py --plot --strat trailamount=30.0
```

和图表

![image](img/700eceb9bc1fb34867831b2eee88bc64.png)

最后一次执行后跟随`trailpercent=0.02`

```py
$ ./trail.py --plot --strat trailpercent=0.02
```

相应的图表。

![image](img/1d0cd296d8c7430931eacd19a3b2e23e.png)

示例用法

```py
$ ./trail.py --help
usage: trail.py [-h] [--data0 DATA0] [--fromdate FROMDATE] [--todate TODATE]
                [--cerebro kwargs] [--broker kwargs] [--sizer kwargs]
                [--strat kwargs] [--plot [kwargs]]

StopTrail Sample

optional arguments:
  -h, --help           show this help message and exit
  --data0 DATA0        Data to read in (default:
                       ../../datas/2005-2006-day-001.txt)
  --fromdate FROMDATE  Date[time] in YYYY-MM-DD[THH:MM:SS] format (default: )
  --todate TODATE      Date[time] in YYYY-MM-DD[THH:MM:SS] format (default: )
  --cerebro kwargs     kwargs in key=value format (default: )
  --broker kwargs      kwargs in key=value format (default: )
  --sizer kwargs       kwargs in key=value format (default: )
  --strat kwargs       kwargs in key=value format (default: )
  --plot [kwargs]      kwargs in key=value format (default: )
```

示例代码

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
import datetime

import backtrader as bt

class St(bt.Strategy):
    params = dict(
        ma=bt.ind.SMA,
        p1=10,
        p2=30,
        stoptype=bt.Order.StopTrail,
        trailamount=0.0,
        trailpercent=0.0,
    )

    def __init__(self):
        ma1, ma2 = self.p.ma(period=self.p.p1), self.p.ma(period=self.p.p2)
        self.crup = bt.ind.CrossUp(ma1, ma2)
        self.order = None

    def next(self):
        if not self.position:
            if self.crup:
                o = self.buy()
                self.order = None
                print('*' * 50)

        elif self.order is None:
            self.order = self.sell(exectype=self.p.stoptype,
                                   trailamount=self.p.trailamount,
                                   trailpercent=self.p.trailpercent)

            if self.p.trailamount:
                tcheck = self.data.close - self.p.trailamount
            else:
                tcheck = self.data.close * (1.0 - self.p.trailpercent)
            print(','.join(
                map(str, [self.datetime.date(), self.data.close[0],
                          self.order.created.price, tcheck])
                )
            )
            print('-' * 10)
        else:
            if self.p.trailamount:
                tcheck = self.data.close - self.p.trailamount
            else:
                tcheck = self.data.close * (1.0 - self.p.trailpercent)
            print(','.join(
                map(str, [self.datetime.date(), self.data.close[0],
                          self.order.created.price, tcheck])
                )
            )

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

    # Data feed
    data0 = bt.feeds.BacktraderCSVData(dataname=args.data0, **kwargs)
    cerebro.adddata(data0)

    # Broker
    cerebro.broker = bt.brokers.BackBroker(**eval('dict(' + args.broker + ')'))

    # Sizer
    cerebro.addsizer(bt.sizers.FixedSize, **eval('dict(' + args.sizer + ')'))

    # Strategy
    cerebro.addstrategy(St, **eval('dict(' + args.strat + ')'))

    # Execute
    cerebro.run(**eval('dict(' + args.cerebro + ')'))

    if args.plot:  # Plot if requested to
        cerebro.plot(**eval('dict(' + args.plot + ')'))

def parse_args(pargs=None):
    parser = argparse.ArgumentParser(
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
        description=(
            'StopTrail Sample'
        )
    )

    parser.add_argument('--data0', default='../../datas/2005-2006-day-001.txt',
                        required=False, help='Data to read in')

    # Defaults for dates
    parser.add_argument('--fromdate', required=False, default='',
                        help='Date[time] in YYYY-MM-DD[THH:MM:SS] format')

    parser.add_argument('--todate', required=False, default='',
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
