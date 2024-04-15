# 括号订单

> 原文：[`www.backtrader.com/blog/posts/2017-04-01-bracket/bracket/`](https://www.backtrader.com/blog/posts/2017-04-01-bracket/bracket/)

发行 `1.9.37.116` 版本添加了 `bracket` 订单，提供了由回测经纪人支持的非常广泛的订单范围（`Market`，`Limit`，`Close`，`Stop`，`StopLimit`，`StopTrail`，`StopTrailLimit`，`OCO`）

注意

这是为了 *回测* 和 *交互经纪人* 商店而实现的

`bracket` 订单不是单个订单，而实际上是由 *3* 个订单组成的。让我们考虑长边

+   主边 `buy` 订单，通常设置为 `Limit` 或 `StopLimit` 订单

+   低边 `sell` 订单，通常设置为 `Stop` 订单以限制损失

+   高边 `sell` 订单，通常设置为 `Limit` 订单以获利

对于短边，相应的 `sell` 和 2 x `buy` 订单。

低/高边订单实际上确实围绕主边订单创建了一个括号。

为了加入一些逻辑，以下规则适用：

+   为了避免它们中的任何一个被独立触发，三个订单一起提交

+   低/高边订单标记为主边的子订单

+   直到主边执行完毕，子订单都不活跃

+   主边取消则低边和高边都取消

+   主边的执行激活了低边和高边

+   一旦活跃

    +   任何低/高边订单的执行或取消都会自动取消另一个

## 使用模式

创建括号订单集的两种可能性

+   单次发行 3 个订单

+   手动发行 3 个订单

## 单次发行一个括号

*backtrader* 在 `Strategy` 中提供了两种控制 *bracket* 订单的新方法。

+   `buy_bracket` 和 `sell_bracket`

注意

签名和信息在下方或在 `Strategy` 参考部分。

通过一条语句完整发行 3 个订单集。例如：

```py
brackets = self.buy_bracket(limitprice=14.00, price=13.50, stopprice=13.00)
```

注意 `stopprice` 和 `limitprice` 如何包裹主 `price`

这应该足够了。实际的目标 `data` 将是 `data0`，而 `size` 将由默认大小器自动确定。当然，可以指定许多其他参数以对执行进行精细控制。

返回值是：

+   包含 3 个订单的列表，顺序为 `[main, stop, limit]`

因为当发出 `sell_bracket` 订单时，低边和高边将被转向，参数按照惯例命名为 `stop` 和 `limit`

+   `stop` 旨在停止损失（在长操作中是低边，在短操作中是高边）

+   `limit` 旨在获利（在长操作中是高边，在短操作中是低边）

## 手动发行一个括号

这涉及生成 3 个订单并调整 `transmit` 和 `parent` 参数。规则如下：

+   主边订单必须首先创建，且 `transmit=False`

+   低/高边订单必须具有 `parent=main_side_order`

+   要创建的第一个低/高边订单必须具有 `transmit=False`

+   要创建的最后一个订单（低端或高端）设置`transmit=True`

一个实际示例，执行上述单个命令所做的事情：

```py
mainside = self.buy(price=13.50, exectype=bt.Order.Limit, transmit=False)
lowside  = self.sell(price=13.00, size=mainsize.size, exectype=bt.Order.Stop,
                     transmit=False, parent=mainside)
highside = self.sell(price=14.00, size=mainsize.size, exectype=bt.Order.Limit,
                     transmit=True, parent=mainside)
```

还有很多事情要做：

+   跟踪`mainside`的顺序以指示它是其他订单的父订单

+   控制`transmit`以确保只有最后一个订单触发联合传输

+   指定执行类型

+   为低端和高端指定`size`

    因为`size` **必须**相同。如果参数没有手动指定，并且最终用户引入了一个 sizer，那么 sizer 实际上可能为订单指示一个不同的值。这就是为什么在为`mainside`订单设置后，必须手动添加到调用中的原因。

## 它的一个示例

运行下面的示例会产生以下输出（为简洁起见）

```py
$ ./bracket.py --plot

2005-01-28: Oref 1 / Buy at 2941.11055
2005-01-28: Oref 2 / Sell Stop at 2881.99275
2005-01-28: Oref 3 / Sell Limit at 3000.22835
2005-01-31: Order ref: 1 / Type Buy / Status Submitted
2005-01-31: Order ref: 2 / Type Sell / Status Submitted
2005-01-31: Order ref: 3 / Type Sell / Status Submitted
2005-01-31: Order ref: 1 / Type Buy / Status Accepted
2005-01-31: Order ref: 2 / Type Sell / Status Accepted
2005-01-31: Order ref: 3 / Type Sell / Status Accepted
2005-02-01: Order ref: 1 / Type Buy / Status Expired
2005-02-01: Order ref: 2 / Type Sell / Status Canceled
2005-02-01: Order ref: 3 / Type Sell / Status Canceled
...
2005-08-11: Oref 16 / Buy at 3337.3892
2005-08-11: Oref 17 / Sell Stop at 3270.306
2005-08-11: Oref 18 / Sell Limit at 3404.4724
2005-08-12: Order ref: 16 / Type Buy / Status Submitted
2005-08-12: Order ref: 17 / Type Sell / Status Submitted
2005-08-12: Order ref: 18 / Type Sell / Status Submitted
2005-08-12: Order ref: 16 / Type Buy / Status Accepted
2005-08-12: Order ref: 17 / Type Sell / Status Accepted
2005-08-12: Order ref: 18 / Type Sell / Status Accepted
2005-08-12: Order ref: 16 / Type Buy / Status Completed
2005-08-18: Order ref: 17 / Type Sell / Status Completed
2005-08-18: Order ref: 18 / Type Sell / Status Canceled
...
2005-09-26: Oref 22 / Buy at 3383.92535
2005-09-26: Oref 23 / Sell Stop at 3315.90675
2005-09-26: Oref 24 / Sell Limit at 3451.94395
2005-09-27: Order ref: 22 / Type Buy / Status Submitted
2005-09-27: Order ref: 23 / Type Sell / Status Submitted
2005-09-27: Order ref: 24 / Type Sell / Status Submitted
2005-09-27: Order ref: 22 / Type Buy / Status Accepted
2005-09-27: Order ref: 23 / Type Sell / Status Accepted
2005-09-27: Order ref: 24 / Type Sell / Status Accepted
2005-09-27: Order ref: 22 / Type Buy / Status Completed
2005-10-04: Order ref: 24 / Type Sell / Status Completed
2005-10-04: Order ref: 23 / Type Sell / Status Canceled
...
```

显示了 3 种不同的结果：

+   在第 1 种情况下，主订单已过期，这自动取消了其他两个订单

+   在第 2 种情况下，主订单已完成，低端（在买入情况下为止损）已执行，限制损失

+   在第 3 种情况下，主订单已完成，高端（限价）已执行

    这可以注意到，因为*Completed*的 id 分别为`22`和`24`，而**high**方订单是最后发出的，这意味着未执行的 low side 订单的 id 为 23。

视觉上

![图片](img/2c1b300adfad7c6033a468207df452cb.png)

可以立即看到，亏损交易和盈利交易都围绕着相同的数值对齐，这就是 bracketing 的目的。控制双方。

运行示例手动发出 3 个订单，但可以告诉它使用`buy_bracket`。让我们看看输出：

```py
$ ./bracket.py --strat usebracket=True
```

产生相同的结果

![图片](img/e3941ba3d7614308c56035faaf6e7c82.png)

## 一些参考

查看新的`buy_bracket`和`sell_bracket`方法

```py
def buy_bracket(self, data=None, size=None, price=None, plimit=None,
                exectype=bt.Order.Limit, valid=None, tradeid=0,
                trailamount=None, trailpercent=None, oargs={},
                stopprice=None, stopexec=bt.Order.Stop, stopargs={},
                limitprice=None, limitexec=bt.Order.Limit, limitargs={},
                **kwargs):
    '''
    Create a bracket order group (low side - buy order - high side). The
    default behavior is as follows:

      - Issue a **buy** order with execution ``Limit``

      - Issue a *low side* bracket **sell** order with execution ``Stop``

      - Issue a *high side* bracket **sell** order with execution
        ``Limit``.

    See below for the different parameters

      - ``data`` (default: ``None``)

        For which data the order has to be created. If ``None`` then the
        first data in the system, ``self.datas[0] or self.data0`` (aka
        ``self.data``) will be used

      - ``size`` (default: ``None``)

        Size to use (positive) of units of data to use for the order.

        If ``None`` the ``sizer`` instance retrieved via ``getsizer`` will
        be used to determine the size.

        **Note**: The same size is applied to all 3 orders of the bracket

      - ``price`` (default: ``None``)

        Price to use (live brokers may place restrictions on the actual
        format if it does not comply to minimum tick size requirements)

        ``None`` is valid for ``Market`` and ``Close`` orders (the market
        determines the price)

        For ``Limit``, ``Stop`` and ``StopLimit`` orders this value
        determines the trigger point (in the case of ``Limit`` the trigger
        is obviously at which price the order should be matched)

      - ``plimit`` (default: ``None``)

        Only applicable to ``StopLimit`` orders. This is the price at which
        to set the implicit *Limit* order, once the *Stop* has been
        triggered (for which ``price`` has been used)

      - ``trailamount`` (default: ``None``)

        If the order type is StopTrail or StopTrailLimit, this is an
        absolute amount which determines the distance to the price (below
        for a Sell order and above for a buy order) to keep the trailing
        stop

      - ``trailpercent`` (default: ``None``)

        If the order type is StopTrail or StopTrailLimit, this is a
        percentage amount which determines the distance to the price (below
        for a Sell order and above for a buy order) to keep the trailing
        stop (if ``trailamount`` is also specified it will be used)

      - ``exectype`` (default: ``bt.Order.Limit``)

        Possible values: (see the documentation for the method ``buy``

      - ``valid`` (default: ``None``)

        Possible values: (see the documentation for the method ``buy``

      - ``tradeid`` (default: ``0``)

        Possible values: (see the documentation for the method ``buy``

      - ``oargs`` (default: ``{}``)

        Specific keyword arguments (in a ``dict``) to pass to the main side
        order. Arguments from the default ``**kwargs`` will be applied on
        top of this.

      - ``**kwargs``: additional broker implementations may support extra
        parameters. ``backtrader`` will pass the *kwargs* down to the
        created order objects

        Possible values: (see the documentation for the method ``buy``

        **Note**: this ``kwargs`` will be applied to the 3 orders of a
        bracket. See below for specific keyword arguments for the low and
        high side orders

      - ``stopprice`` (default: ``None``)

        Specific price for the *low side* stop order

      - ``stopexec`` (default: ``bt.Order.Stop``)

        Specific execution type for the *low side* order

      - ``stopargs`` (default: ``{}``)

        Specific keyword arguments (in a ``dict``) to pass to the low side
        order. Arguments from the default ``**kwargs`` will be applied on
        top of this.

      - ``limitprice`` (default: ``None``)

        Specific price for the *high side* stop order

      - ``stopexec`` (default: ``bt.Order.Limit``)

        Specific execution type for the *high side* order

      - ``limitargs`` (default: ``{}``)

        Specific keyword arguments (in a ``dict``) to pass to the high side
        order. Arguments from the default ``**kwargs`` will be applied on
        top of this.

    Returns:

      - A list containing the 3 orders [order, stop side, limit side]

    '''

def sell_bracket(self, data=None,
                 size=None, price=None, plimit=None,
                 exectype=bt.Order.Limit, valid=None, tradeid=0,
                 trailamount=None, trailpercent=None,
                 oargs={},
                 stopprice=None, stopexec=bt.Order.Stop, stopargs={},
                 limitprice=None, limitexec=bt.Order.Limit, limitargs={},
                 **kwargs):
    '''
    Create a bracket order group (low side - buy order - high side). The
    default behavior is as follows:

      - Issue a **sell** order with execution ``Limit``

      - Issue a *high side* bracket **buy** order with execution ``Stop``

      - Issue a *low side* bracket **buy** order with execution ``Limit``.

    See ``bracket_buy`` for the meaning of the parameters

    Returns:

      - A list containing the 3 orders [order, stop side, limit side]

    '''
```

## 示例用法

```py
$ ./bracket.py --help
usage: bracket.py [-h] [--data0 DATA0] [--fromdate FROMDATE] [--todate TODATE]
                  [--cerebro kwargs] [--broker kwargs] [--sizer kwargs]
                  [--strat kwargs] [--plot [kwargs]]

Sample Skeleton

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

## 示例代码

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
import datetime

import backtrader as bt

class St(bt.Strategy):
    params = dict(
        ma=bt.ind.SMA,
        p1=5,
        p2=15,
        limit=0.005,
        limdays=3,
        limdays2=1000,
        hold=10,
        usebracket=False,  # use order_target_size
        switchp1p2=False,  # switch prices of order1 and order2
    )

    def notify_order(self, order):
        print('{}: Order ref: {} / Type {} / Status {}'.format(
            self.data.datetime.date(0),
            order.ref, 'Buy' * order.isbuy() or 'Sell',
            order.getstatusname()))

        if order.status == order.Completed:
            self.holdstart = len(self)

        if not order.alive() and order.ref in self.orefs:
            self.orefs.remove(order.ref)

    def __init__(self):
        ma1, ma2 = self.p.ma(period=self.p.p1), self.p.ma(period=self.p.p2)
        self.cross = bt.ind.CrossOver(ma1, ma2)

        self.orefs = list()

        if self.p.usebracket:
            print('-' * 5, 'Using buy_bracket')

    def next(self):
        if self.orefs:
            return  # pending orders do nothing

        if not self.position:
            if self.cross > 0.0:  # crossing up

                close = self.data.close[0]
                p1 = close * (1.0 - self.p.limit)
                p2 = p1 - 0.02 * close
                p3 = p1 + 0.02 * close

                valid1 = datetime.timedelta(self.p.limdays)
                valid2 = valid3 = datetime.timedelta(self.p.limdays2)

                if self.p.switchp1p2:
                    p1, p2 = p2, p1
                    valid1, valid2 = valid2, valid1

                if not self.p.usebracket:
                    o1 = self.buy(exectype=bt.Order.Limit,
                                  price=p1,
                                  valid=valid1,
                                  transmit=False)

                    print('{}: Oref {} / Buy at {}'.format(
                        self.datetime.date(), o1.ref, p1))

                    o2 = self.sell(exectype=bt.Order.Stop,
                                   price=p2,
                                   valid=valid2,
                                   parent=o1,
                                   transmit=False)

                    print('{}: Oref {} / Sell Stop at {}'.format(
                        self.datetime.date(), o2.ref, p2))

                    o3 = self.sell(exectype=bt.Order.Limit,
                                   price=p3,
                                   valid=valid3,
                                   parent=o1,
                                   transmit=True)

                    print('{}: Oref {} / Sell Limit at {}'.format(
                        self.datetime.date(), o3.ref, p3))

                    self.orefs = [o1.ref, o2.ref, o3.ref]

                else:
                    os = self.buy_bracket(
                        price=p1, valid=valid1,
                        stopprice=p2, stopargs=dict(valid=valid2),
                        limitprice=p3, limitargs=dict(valid=valid3),)

                    self.orefs = [o.ref for o in os]

        else:  # in the market
            if (len(self) - self.holdstart) >= self.p.hold:
                pass  # do nothing in this case

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
            'Sample Skeleton'
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
