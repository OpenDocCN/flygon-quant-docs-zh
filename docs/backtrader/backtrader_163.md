# Visual Chart 实时数据/交易

> 原文：[`www.backtrader.com/blog/posts/2016-07-12-visualchart-feed/visualchart-feed/`](https://www.backtrader.com/blog/posts/2016-07-12-visualchart-feed/visualchart-feed/)

从版本 *1.5.1.93* 开始，backtrader 支持 Visual Chart 实时数据和实时交易。

需要的东西：

+   *Visual Chart 6*（这个版本运行在 Windows 上）

+   `comtypes`，具体来说是这个分支：[`github.com/mementum/comtypes`](https://github.com/mementum/comtypes)

    使用以下命令安装：`pip install https://github.com/mementum/comtypes/archive/master.zip`

    *Visual Chart* 的 API 基于 *COM*，当前的 `comtypes` 主分支不支持对 `VT_RECORD` 的 `VT_ARRAYS` 进行解包。而 *Visual Chart* 正是使用了这个。

    [Pull Request #104](https://github.com/enthought/comtypes/pull/104) 已经提交但尚未集成。一旦集成，就可以使用主分支了。

+   `pytz`（可选但强烈建议）

    在许多情况下，数据提供的内部 `SymbolInfo.TimeOffset` 就足以返回市场时间的数据流（即使默认配置是 *Visual Chart* 中的 `LocalTime`）

如果您不知道什么是 *Visual Chart* 和/或其当前关联的经纪商 *Esfera Capital*，请访问以下网站：

+   [Visual Chart](https://www.visualchart.com)

+   [Esfera Capital](https://www.esferacapital.es)

初始声明：

+   如往常一样，在冒险之前测试，**测试**，**测试** 和 **再次测试** 一千次。

    从这个软件中的 *bugs*，到您自己的软件中的 bug 以及处理意外情况的管理：**任何事情都可能出错**

关于此的一些说明：

+   数据提供非常好，并支持内置重采样。好处在于无需进行重采样。

+   数据流不支持 *Seconds* 分辨率。这不太好，但可以通过 *backtrader* 的内置重采样解决。

+   内置了回填功能

+   一些国际指数市场（在交易所 `096`）具有奇怪的时区和市场偏移。

    对此进行了一些工作，例如以预期的 `US/Eastern` 时区提供 `096.DJI`。

+   数据提供了 *continuous futures*，非常方便拥有大量历史数据。

    因此，可以向数据传递第二个参数，指示实际的交易资产。

+   *Good Til Date* 订单的日期时间只能指定为 *日期*。*时间* 部分会被忽略。

+   没有直接的方法可以找到本地设备到数据服务器的偏移量，需要通过会话开始时的 *实时数据点* 进行启发式分析来找出这一点。

+   传递带有 *时间* 组件的 *datetime*（而不是默认的 *00:00:00*）似乎会在 *COM* API 中创建一个 *时间过滤器*。例如，如果您想要 *Minute* 数据，从 3 天前开始到 *14:30*，您可以这样做：

    ```py
    `dt = datetime.now() - timedelta(days=3)
    dt.replace(hour=14, minute=30)

    vcstore.getdata(dataname='001ES', fromdate=dt)` 
    ```

    数据会一直跳过直到 *14:30* 不仅是 3 天前，而是*以后每一天*

    因此，请只传递*完整日期*，即默认的*时间*部分不受影响。

+   *经纪人* 支持 *Positions* 的概念，但仅在它们是 *open* 时。 关于 *Position* 的最后事件（其 **size 为 0**）不会发送。

    因此，*Position* 记账完全由 *backtrader* 完成。

+   *经纪人* 不报告佣金。

    解决方法是在实例化经纪人时提供自己的 `CommissionInfo` 派生类。 请参阅 *backtader* 文档以创建自己的类。 这相当容易。

+   `Cancelled` 与 `Expired` 订单。 此区别不存在，需要启发式方法来尝试清除这种区别。

    因此，只有`Cancelled`将被报告。

一些额外的说明：

+   *实时* ticks 大部分情况下不被使用。 它们为 *backtrader* 目的产生大量不需要的信息。 在被 *backtrader* 完全断开连接之前，它们有两个主要目的。

    1.  查找符号是否存在。

    1.  计算到数据服务器的偏移量。

    当然，价格信息是实时收集的，但是来自 *DataSource* 对象，它们同时提供历史数据。

尽可能多地记录并在通常的文档链接处提供。

+   [阅读文档](http://backtrader.readthedocs.io/en/latest/)

从样本 `vctest.pye` 对 *Visual Chart* 和 *Demo Broker* 进行了一些运行。

首先：`015ES`（*EuroStoxx50* 连续）重新采样为 1 分钟，并具有断开连接和重新连接：

```py
$ ./vctest.py --data0 015ES --timeframe Minutes --compression 1 --fromdate 2016-07-12
```

输出：

```py
--------------------------------------------------
Strategy Created
--------------------------------------------------
Datetime, Open, High, Low, Close, Volume, OpenInterest, SMA
***** DATA NOTIF: CONNECTED
***** DATA NOTIF: DELAYED
0001, 2016-07-12T08:01:00.000000, 2871.0, 2872.0, 2869.0, 2872.0, 1915.0, 0.0, nan
0002, 2016-07-12T08:02:00.000000, 2872.0, 2872.0, 2870.0, 2871.0, 479.0, 0.0, nan
0003, 2016-07-12T08:03:00.000000, 2871.0, 2871.0, 2869.0, 2870.0, 518.0, 0.0, nan
0004, 2016-07-12T08:04:00.000000, 2870.0, 2871.0, 2870.0, 2871.0, 248.0, 0.0, nan
0005, 2016-07-12T08:05:00.000000, 2870.0, 2871.0, 2870.0, 2871.0, 234.0, 0.0, 2871.0
...
...
0639, 2016-07-12T18:39:00.000000, 2932.0, 2933.0, 2932.0, 2932.0, 1108.0, 0.0, 2932.8
0640, 2016-07-12T18:40:00.000000, 2931.0, 2932.0, 2931.0, 2931.0, 65.0, 0.0, 2932.6
***** DATA NOTIF: LIVE
0641, 2016-07-12T18:41:00.000000, 2932.0, 2932.0, 2930.0, 2930.0, 2093.0, 0.0, 2931.8
***** STORE NOTIF: (u'VisualChart is Disconnected', -65520)
***** DATA NOTIF: CONNBROKEN
***** STORE NOTIF: (u'VisualChart is Connected', -65521)
***** DATA NOTIF: CONNECTED
***** DATA NOTIF: DELAYED
0642, 2016-07-12T18:42:00.000000, 2931.0, 2931.0, 2931.0, 2931.0, 137.0, 0.0, 2931.2
0643, 2016-07-12T18:43:00.000000, 2931.0, 2931.0, 2931.0, 2931.0, 432.0, 0.0, 2931.0
...
0658, 2016-07-12T18:58:00.000000, 2929.0, 2929.0, 2929.0, 2929.0, 4.0, 0.0, 2930.0
0659, 2016-07-12T18:59:00.000000, 2929.0, 2930.0, 2929.0, 2930.0, 353.0, 0.0, 2930.0
***** DATA NOTIF: LIVE
0660, 2016-07-12T19:00:00.000000, 2930.0, 2930.0, 2930.0, 2930.0, 376.0, 0.0, 2930.0
0661, 2016-07-12T19:01:00.000000, 2929.0, 2930.0, 2929.0, 2930.0, 35.0, 0.0, 2929.8
```

注意

执行环境安装了 `pytz`。

注意

注意没有 `--resample`：对于 `Minutes`，重新采样是内置于 *Visual Chart* 中的。

最后一些交易，购买 `015ES` 的 *2* 个合约，单个 `Market` 订单，并将它们卖出为 *1* 个合约的 2 个订单。

执行：

```py
$ ./vctest.py --data0 015ES --timeframe Minutes --compression 1 --fromdate 2016-07-12 2>&1 --broker --account accname --trade --stake 2
```

输出相当冗长，显示了订单执行的所有部分。 简要总结一下：

```py
--------------------------------------------------
Strategy Created
--------------------------------------------------
Datetime, Open, High, Low, Close, Volume, OpenInterest, SMA
***** DATA NOTIF: CONNECTED
***** DATA NOTIF: DELAYED
0001, 2016-07-12T08:01:00.000000, 2871.0, 2872.0, 2869.0, 2872.0, 1915.0, 0.0, nan
...
0709, 2016-07-12T19:50:00.000000, 2929.0, 2930.0, 2929.0, 2930.0, 11.0, 0.0, 2930.4
***** DATA NOTIF: LIVE
0710, 2016-07-12T19:51:00.000000, 2930.0, 2930.0, 2929.0, 2929.0, 134.0, 0.0, 2930.0
-------------------------------------------------- ORDER BEGIN 2016-07-12 19:52:01.629000
Ref: 1
OrdType: 0
OrdType: Buy
Status: 1
Status: Submitted
Size: 2
Price: None
Price Limit: None
ExecType: 0
ExecType: Market
CommInfo: <backtrader.brokers.vcbroker.VCCommInfo object at 0x000000001100CE10>
End of Session: 736157.916655
Info: AutoOrderedDict()
Broker: <backtrader.brokers.vcbroker.VCBroker object at 0x000000000475D400>
Alive: True
-------------------------------------------------- ORDER END
-------------------------------------------------- ORDER BEGIN 2016-07-12 19:52:01.629000
Ref: 1
OrdType: 0
OrdType: Buy
Status: 2
Status: Accepted
Size: 2
Price: None
Price Limit: None
ExecType: 0
ExecType: Market
CommInfo: <backtrader.brokers.vcbroker.VCCommInfo object at 0x000000001100CE10>
End of Session: 736157.916655
Info: AutoOrderedDict()
Broker: None
Alive: True
-------------------------------------------------- ORDER END
-------------------------------------------------- ORDER BEGIN 2016-07-12 19:52:01.629000
Ref: 1
OrdType: 0
OrdType: Buy
Status: 4
Status: Completed
Size: 2
Price: None
Price Limit: None
ExecType: 0
ExecType: Market
CommInfo: <backtrader.brokers.vcbroker.VCCommInfo object at 0x000000001100CE10>
End of Session: 736157.916655
Info: AutoOrderedDict()
Broker: None
Alive: False
-------------------------------------------------- ORDER END
-------------------------------------------------- TRADE BEGIN 2016-07-12 19:52:01.629000
ref:1
data:<backtrader.feeds.vcdata.VCData object at 0x000000000475D9E8>
tradeid:0
size:2.0
price:2930.0
value:5860.0
commission:0.0
pnl:0.0
pnlcomm:0.0
justopened:True
isopen:True
isclosed:0
baropen:710
dtopen:736157.74375
barclose:0
dtclose:0.0
barlen:0
historyon:False
history:[]
status:1
-------------------------------------------------- TRADE END
...
```

以下发生了：

+   数据正常接收。

+   发出了一个执行类型为 `Market` 的 `BUY`，数量为 `2`。

    +   收到 `Submitted` 和 `Accepted` 通知（仅显示了 `Submitted`）。

    +   一连串的 `Partial` 执行（仅显示了 1 个）直到收到 `Completed`。

    实际执行没有显示，但在 `order.executed` 下收到的 `order` 实例中可用。

+   虽然没有显示，但发出了 2 x `Market` `SELL` 订单来撤销操作。

    屏幕截图显示了在一个晚上使用 `015ES`（*EuroStoxx 50*）和 `034EURUS`（*EUR.USD 外汇对*）进行两次不同运行后，在 *Visual Chart* 中的日志。

![image](img/0e2daec1d73f0e96f42ee5945056bca1.png)

示例可以做得更多，旨在彻底测试设施，如果可能的话，揭示任何粗糙的边缘。

使用方式：

```py
$ ./vctest.py --help
usage: vctest.py [-h] [--exactbars EXACTBARS] [--plot] [--stopafter STOPAFTER]
                 [--nostore] [--qcheck QCHECK] [--no-timeoffset] --data0 DATA0
                 [--tradename TRADENAME] [--data1 DATA1] [--timezone TIMEZONE]
                 [--no-backfill_start] [--latethrough] [--historical]
                 [--fromdate FROMDATE] [--todate TODATE]
                 [--smaperiod SMAPERIOD] [--replay | --resample]
                 [--timeframe {Ticks,MicroSeconds,Seconds,Minutes,Days,Weeks,Months,Years}]
                 [--compression COMPRESSION] [--no-bar2edge] [--no-adjbartime]
                 [--no-rightedge] [--broker] [--account ACCOUNT] [--trade]
                 [--donotsell]
                 [--exectype {Market,Close,Limit,Stop,StopLimit}]
                 [--price PRICE] [--pstoplimit PSTOPLIMIT] [--stake STAKE]
                 [--valid VALID] [--cancel CANCEL]

Test Visual Chart 6 integration

optional arguments:
  -h, --help            show this help message and exit
  --exactbars EXACTBARS
                        exactbars level, use 0/-1/-2 to enable plotting
                        (default: 1)
  --plot                Plot if possible (default: False)
  --stopafter STOPAFTER
                        Stop after x lines of LIVE data (default: 0)
  --nostore             Do not Use the store pattern (default: False)
  --qcheck QCHECK       Timeout for periodic notification/resampling/replaying
                        check (default: 0.5)
  --no-timeoffset       Do not Use TWS/System time offset for non timestamped
                        prices and to align resampling (default: False)
  --data0 DATA0         data 0 into the system (default: None)
  --tradename TRADENAME
                        Actual Trading Name of the asset (default: None)
  --data1 DATA1         data 1 into the system (default: None)
  --timezone TIMEZONE   timezone to get time output into (pytz names)
                        (default: None)
  --historical          do only historical download (default: False)
  --fromdate FROMDATE   Starting date for historical download with format:
                        YYYY-MM-DD[THH:MM:SS] (default: None)
  --todate TODATE       End date for historical download with format: YYYY-MM-
                        DD[THH:MM:SS] (default: None)
  --smaperiod SMAPERIOD
                        Period to apply to the Simple Moving Average (default:
                        5)
  --replay              replay to chosen timeframe (default: False)
  --resample            resample to chosen timeframe (default: False)
  --timeframe {Ticks,MicroSeconds,Seconds,Minutes,Days,Weeks,Months,Years}
                        TimeFrame for Resample/Replay (default: Ticks)
  --compression COMPRESSION
                        Compression for Resample/Replay (default: 1)
  --no-bar2edge         no bar2edge for resample/replay (default: False)
  --no-adjbartime       no adjbartime for resample/replay (default: False)
  --no-rightedge        no rightedge for resample/replay (default: False)
  --broker              Use VisualChart as broker (default: False)
  --account ACCOUNT     Choose broker account (else first) (default: None)
  --trade               Do Sample Buy/Sell operations (default: False)
  --donotsell           Do not sell after a buy (default: False)
  --exectype {Market,Close,Limit,Stop,StopLimit}
                        Execution to Use when opening position (default:
                        Market)
  --price PRICE         Price in Limit orders or Stop Trigger Price (default:
                        None)
  --pstoplimit PSTOPLIMIT
                        Price for the limit in StopLimit (default: None)
  --stake STAKE         Stake to use in buy operations (default: 10)
  --valid VALID         Seconds or YYYY-MM-DD (default: None)
  --cancel CANCEL       Cancel a buy order after n bars in operation, to be
                        combined with orders like Limit (default: 0)
```

代码：

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
import datetime

# The above could be sent to an independent module
import backtrader as bt
from backtrader.utils import flushfile  # win32 quick stdout flushing
from backtrader.utils.py3 import string_types

class TestStrategy(bt.Strategy):
    params = dict(
        smaperiod=5,
        trade=False,
        stake=10,
        exectype=bt.Order.Market,
        stopafter=0,
        valid=None,
        cancel=0,
        donotsell=False,
        price=None,
        pstoplimit=None,
    )

    def __init__(self):
        # To control operation entries
        self.orderid = list()
        self.order = None

        self.counttostop = 0
        self.datastatus = 0

        # Create SMA on 2nd data
        self.sma = bt.indicators.MovAv.SMA(self.data, period=self.p.smaperiod)

        print('--------------------------------------------------')
        print('Strategy Created')
        print('--------------------------------------------------')

    def notify_data(self, data, status, *args, **kwargs):
        print('*' * 5, 'DATA NOTIF:', data._getstatusname(status), *args)
        if status == data.LIVE:
            self.counttostop = self.p.stopafter
            self.datastatus = 1

    def notify_store(self, msg, *args, **kwargs):
        print('*' * 5, 'STORE NOTIF:', msg)

    def notify_order(self, order):
        if order.status in [order.Completed, order.Cancelled, order.Rejected]:
            self.order = None

        print('-' * 50, 'ORDER BEGIN', datetime.datetime.now())
        print(order)
        print('-' * 50, 'ORDER END')

    def notify_trade(self, trade):
        print('-' * 50, 'TRADE BEGIN', datetime.datetime.now())
        print(trade)
        print('-' * 50, 'TRADE END')

    def prenext(self):
        self.next(frompre=True)

    def next(self, frompre=False):
        txt = list()
        txt.append('%04d' % len(self))
        dtfmt = '%Y-%m-%dT%H:%M:%S.%f'
        txt.append('%s' % self.data.datetime.datetime(0).strftime(dtfmt))
        txt.append('{}'.format(self.data.open[0]))
        txt.append('{}'.format(self.data.high[0]))
        txt.append('{}'.format(self.data.low[0]))
        txt.append('{}'.format(self.data.close[0]))
        txt.append('{}'.format(self.data.volume[0]))
        txt.append('{}'.format(self.data.openinterest[0]))
        txt.append('{}'.format(self.sma[0]))
        print(', '.join(txt))

        if len(self.datas) > 1:
            txt = list()
            txt.append('%04d' % len(self))
            dtfmt = '%Y-%m-%dT%H:%M:%S.%f'
            txt.append('%s' % self.data1.datetime.datetime(0).strftime(dtfmt))
            txt.append('{}'.format(self.data1.open[0]))
            txt.append('{}'.format(self.data1.high[0]))
            txt.append('{}'.format(self.data1.low[0]))
            txt.append('{}'.format(self.data1.close[0]))
            txt.append('{}'.format(self.data1.volume[0]))
            txt.append('{}'.format(self.data1.openinterest[0]))
            txt.append('{}'.format(float('NaN')))
            print(', '.join(txt))

        if self.counttostop:  # stop after x live lines
            self.counttostop -= 1
            if not self.counttostop:
                self.env.runstop()
                return

        if not self.p.trade:
            return

        # if True and len(self.orderid) < 1:
        if self.datastatus and not self.position and len(self.orderid) < 1:
            self.order = self.buy(size=self.p.stake,
                                  exectype=self.p.exectype,
                                  price=self.p.price,
                                  plimit=self.p.pstoplimit,
                                  valid=self.p.valid)

            self.orderid.append(self.order)
        elif self.position.size > 0 and not self.p.donotsell:
            if self.order is None:
                size = self.p.stake // 2
                if not size:
                    size = self.position.size  # use the remaining
                self.order = self.sell(size=size, exectype=bt.Order.Market)

        elif self.order is not None and self.p.cancel:
            if self.datastatus > self.p.cancel:
                self.cancel(self.order)

        if self.datastatus:
            self.datastatus += 1

    def start(self):
        header = ['Datetime', 'Open', 'High', 'Low', 'Close', 'Volume',
                  'OpenInterest', 'SMA']
        print(', '.join(header))

        self.done = False

def runstrategy():
    args = parse_args()

    # Create a cerebro
    cerebro = bt.Cerebro()

    storekwargs = dict()

    if not args.nostore:
        vcstore = bt.stores.VCStore(**storekwargs)

    if args.broker:
        brokerargs = dict(account=args.account, **storekwargs)
        if not args.nostore:
            broker = vcstore.getbroker(**brokerargs)
        else:
            broker = bt.brokers.VCBroker(**brokerargs)

        cerebro.setbroker(broker)

    timeframe = bt.TimeFrame.TFrame(args.timeframe)
    if args.resample or args.replay:
        datatf = bt.TimeFrame.Ticks
        datacomp = 1
    else:
        datatf = timeframe
        datacomp = args.compression

    fromdate = None
    if args.fromdate:
        dtformat = '%Y-%m-%d' + ('T%H:%M:%S' * ('T' in args.fromdate))
        fromdate = datetime.datetime.strptime(args.fromdate, dtformat)

    todate = None
    if args.todate:
        dtformat = '%Y-%m-%d' + ('T%H:%M:%S' * ('T' in args.todate))
        todate = datetime.datetime.strptime(args.todate, dtformat)

    VCDataFactory = vcstore.getdata if not args.nostore else bt.feeds.VCData

    datakwargs = dict(
        timeframe=datatf, compression=datacomp,
        fromdate=fromdate, todate=todate,
        historical=args.historical,
        qcheck=args.qcheck,
        tz=args.timezone
    )

    if args.nostore and not args.broker:   # neither store nor broker
        datakwargs.update(storekwargs)  # pass the store args over the data

    data0 = VCDataFactory(dataname=args.data0, tradename=args.tradename,
                          **datakwargs)

    data1 = None
    if args.data1 is not None:
        data1 = VCDataFactory(dataname=args.data1, **datakwargs)

    rekwargs = dict(
        timeframe=timeframe, compression=args.compression,
        bar2edge=not args.no_bar2edge,
        adjbartime=not args.no_adjbartime,
        rightedge=not args.no_rightedge,
    )

    if args.replay:
        cerebro.replaydata(dataname=data0, **rekwargs)

        if data1 is not None:
            cerebro.replaydata(dataname=data1, **rekwargs)

    elif args.resample:
        cerebro.resampledata(dataname=data0, **rekwargs)

        if data1 is not None:
            cerebro.resampledata(dataname=data1, **rekwargs)

    else:
        cerebro.adddata(data0)
        if data1 is not None:
            cerebro.adddata(data1)

    if args.valid is None:
        valid = None
    else:
        try:
            valid = float(args.valid)
        except:
            dtformat = '%Y-%m-%d' + ('T%H:%M:%S' * ('T' in args.valid))
            valid = datetime.datetime.strptime(args.valid, dtformat)
        else:
            valid = datetime.timedelta(seconds=args.valid)

    # Add the strategy
    cerebro.addstrategy(TestStrategy,
                        smaperiod=args.smaperiod,
                        trade=args.trade,
                        exectype=bt.Order.ExecType(args.exectype),
                        stake=args.stake,
                        stopafter=args.stopafter,
                        valid=valid,
                        cancel=args.cancel,
                        donotsell=args.donotsell,
                        price=args.price,
                        pstoplimit=args.pstoplimit)

    # Live data ... avoid long data accumulation by switching to "exactbars"
    cerebro.run(exactbars=args.exactbars)

    if args.plot and args.exactbars < 1:  # plot if possible
        cerebro.plot()

def parse_args():
    parser = argparse.ArgumentParser(
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
        description='Test Visual Chart 6 integration')

    parser.add_argument('--exactbars', default=1, type=int,
                        required=False, action='store',
                        help='exactbars level, use 0/-1/-2 to enable plotting')

    parser.add_argument('--plot',
                        required=False, action='store_true',
                        help='Plot if possible')

    parser.add_argument('--stopafter', default=0, type=int,
                        required=False, action='store',
                        help='Stop after x lines of LIVE data')

    parser.add_argument('--nostore',
                        required=False, action='store_true',
                        help='Do not Use the store pattern')

    parser.add_argument('--qcheck', default=0.5, type=float,
                        required=False, action='store',
                        help=('Timeout for periodic '
                              'notification/resampling/replaying check'))

    parser.add_argument('--no-timeoffset',
                        required=False, action='store_true',
                        help=('Do not Use TWS/System time offset for non '
                              'timestamped prices and to align resampling'))

    parser.add_argument('--data0', default=None,
                        required=True, action='store',
                        help='data 0 into the system')

    parser.add_argument('--tradename', default=None,
                        required=False, action='store',
                        help='Actual Trading Name of the asset')

    parser.add_argument('--data1', default=None,
                        required=False, action='store',
                        help='data 1 into the system')

    parser.add_argument('--timezone', default=None,
                        required=False, action='store',
                        help='timezone to get time output into (pytz names)')

    parser.add_argument('--historical',
                        required=False, action='store_true',
                        help='do only historical download')

    parser.add_argument('--fromdate',
                        required=False, action='store',
                        help=('Starting date for historical download '
                              'with format: YYYY-MM-DD[THH:MM:SS]'))

    parser.add_argument('--todate',
                        required=False, action='store',
                        help=('End date for historical download '
                              'with format: YYYY-MM-DD[THH:MM:SS]'))

    parser.add_argument('--smaperiod', default=5, type=int,
                        required=False, action='store',
                        help='Period to apply to the Simple Moving Average')

    pgroup = parser.add_mutually_exclusive_group(required=False)

    pgroup.add_argument('--replay',
                        required=False, action='store_true',
                        help='replay to chosen timeframe')

    pgroup.add_argument('--resample',
                        required=False, action='store_true',
                        help='resample to chosen timeframe')

    parser.add_argument('--timeframe', default=bt.TimeFrame.Names[0],
                        choices=bt.TimeFrame.Names,
                        required=False, action='store',
                        help='TimeFrame for Resample/Replay')

    parser.add_argument('--compression', default=1, type=int,
                        required=False, action='store',
                        help='Compression for Resample/Replay')

    parser.add_argument('--no-bar2edge',
                        required=False, action='store_true',
                        help='no bar2edge for resample/replay')

    parser.add_argument('--no-adjbartime',
                        required=False, action='store_true',
                        help='no adjbartime for resample/replay')

    parser.add_argument('--no-rightedge',
                        required=False, action='store_true',
                        help='no rightedge for resample/replay')

    parser.add_argument('--broker',
                        required=False, action='store_true',
                        help='Use VisualChart as broker')

    parser.add_argument('--account', default=None,
                        required=False, action='store',
                        help='Choose broker account (else first)')

    parser.add_argument('--trade',
                        required=False, action='store_true',
                        help='Do Sample Buy/Sell operations')

    parser.add_argument('--donotsell',
                        required=False, action='store_true',
                        help='Do not sell after a buy')

    parser.add_argument('--exectype', default=bt.Order.ExecTypes[0],
                        choices=bt.Order.ExecTypes,
                        required=False, action='store',
                        help='Execution to Use when opening position')

    parser.add_argument('--price', default=None, type=float,
                        required=False, action='store',
                        help='Price in Limit orders or Stop Trigger Price')

    parser.add_argument('--pstoplimit', default=None, type=float,
                        required=False, action='store',
                        help='Price for the limit in StopLimit')

    parser.add_argument('--stake', default=10, type=int,
                        required=False, action='store',
                        help='Stake to use in buy operations')

    parser.add_argument('--valid', default=None,
                        required=False, action='store',
                        help='Seconds or YYYY-MM-DD')

    parser.add_argument('--cancel', default=0, type=int,
                        required=False, action='store',
                        help=('Cancel a buy order after n bars in operation,'
                              ' to be combined with orders like Limit'))

    return parser.parse_args()

if __name__ == '__main__':
    runstrategy()
```
