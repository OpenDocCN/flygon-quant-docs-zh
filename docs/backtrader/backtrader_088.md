# Oanda

> 原文：[`www.backtrader.com/docu/live/oanda/oanda/`](https://www.backtrader.com/docu/live/oanda/oanda/)

与 Oanda 的集成支持：

+   *实时数据*馈送

+   *实时交易*

## 要求

+   `oandapy`

    使用以下命令安装：`pip install git+https://github.com/oanda/oandapy.git`

+   `pytz`（可选且不推荐）

    鉴于外汇市场的全球性和 24 小时运作的特点，选择使用`UTC`时间。如果愿意，您仍然可以使用您期望的输出时区。

## 示例代码

源代码包含完整示例：

+   `samples/oandatest/oandatest.py`

## Oanda - 存储

存储是实时数据提要/交易支持的关键，提供了*Oanda* API 与数据提要和经纪人代理的需求之间的适配层。

+   提供访问使用方法获取*经纪人*实例：

    +   `OandaStore.getbroker(*args, **kwargs)`

+   提供访问*数据*提要实例的方法

    +   `OandaStore.getedata(\*args, **kwargs)`

    在这种情况下，许多`**kwargs`与数据提要（例如`dataname`、`fromdate`、`todate`、`sessionstart`、`sessionend`、`timeframe`、`compression`）是共同的

    数据可能提供其他参数。请查看下面的参考资料。

### 强制性参数

为了成功连接到*Oanda*，以下参数是强制性的：

+   `token`（默认值：`None`）：API 访问令牌

+   `account`（默认值：`None`）：账户 ID

这些由*Oanda*提供

是否连接到*测试*服务器或真实服务器，请使用：

+   `practice`（默认值：`False`）：使用测试环境

必须定期检查账户以获取*现金*和*价值*。刷新周期可以通过以下方式控制：

+   `account_tmout`（默认值：`10.0`）：帐户价值/现金刷新周期

## Oanda 提要

实例化数据：

+   根据 Oanda 的指南传递符号

    +   根据 Oanda 的指南，*EUR/USDD*必须指定为`EUR_USD`。实例化如下：

    ```py
    `data = oandastore.getdata(dataname='EUR_USD', ...)` 
    ```

### 时间管理

除非将`tz`参数（*pytz 兼容*对象）传递给数据提要，否则所有时间输出均为`UTC`格式，如上所述。

#### 回填

*backtrader*对*Oanda*没有特殊要求。对于小时间框架，在*测试*服务器上由*Oanda*返回的回填长度为`500`条

## OandaBroker - 实时交易

### 使用经纪人

要使用*OandaBroker*，必须替换由*cerebro*创建的标准经纪人模拟实例。

使用*Store*模型（首选）：

```py
`import backtrader as bt

cerebro = bt.Cerebro()
oandastore = bt.stores.OandaStore()
cerebro.broker = oandastore.getbroker()  # or cerebro.setbroker(...)` 
```

### 经纪人 - 初始持仓

经纪人支持一个参数：

+   `use_positions`（默认值：`True`）：连接到经纪人提供商时，使用现有持仓来启动经纪人。

    在实例化时设置为`False`，以忽略任何现有持仓

#### 操作

关于标准用法没有变化。只需使用策略中可用的方法（详见`Strategy`参考资料以获取完整解释）

+   `buy`

+   `sell`

+   `close`

+   `cancel`

### 订单执行类型

*Oanda*几乎支持*backtrader*所需的所有订单执行类型，但不包括*Close*。

因此，订单执行类型受到限制：

+   `Order.Market`

+   `Order.Limit`

+   `Order.Stop`

+   `Order.StopLimit`（使用 *Stop* 和 *upperBound* / *lowerBound* 价格）

+   `Order.StopTrail`

+   *Bracket* 订单受到支持，使用 `takeprofit` 和 `stoploss` 订单成员并在内部创建模拟订单。

### 订单有效性

在回测期间（使用 `valid` 为 `buy` 和 `sell`）可用的相同的有效性概念可用，并且具有相同的含义。因此，对于以下值，*Oanda Orders* 的 `valid` 参数将如下翻译：

+   `None` 转换为 *Good Til Cancelled*

    因为未指定有效性，所以理解为订单必须有效直至取消

+   `datetime/date` 转换为 *Good Til Date*

+   `timedelta(x)` 转换为 *Good Til Date*（这里 `timedelta(x) != timedelta()`）

    这被解释为信号，要求订单从 `now` + `timedelta(x)` 开始有效。

+   `timedelta() 或 0` 转换为 *Session*

    已传递一个值（而不是 `None`）但为 *Null*，并被解释为当前 *day*（会话）有效的订单

### 通知

标准的 `Order` 状态将通过 `notify_order` 方法（如果已重写）通知到*策略*。

+   `Submitted` - 订单已发送到 TWS

+   `Accepted` - 订单已下达

+   `Rejected` - 用于实际拒绝和在订单创建期间未知其他状态时使用

+   `Partial` - 部分执行已经发生

+   `Completed` - 订单已完全执行

+   `Canceled`（或 `Cancelled`）

+   `Expired` - 当订单因到期而取消时

## 参考

### OandaStore

#### class backtrader.stores.OandaStore()

单例类，用于控制与 Oanda 的连接。

参数：

+   `token`（默认值：`None`）：API 访问令牌

+   `account`（默认值：`None`）：账户 ID

+   `practice`（默认值：`False`）：使用测试环境

+   `account_tmout`（默认值：`10.0`）：账户价值/现金刷新的刷新周期

### OandaBroker

#### class backtrader.brokers.OandaBroker(**kwargs)

Oanda 的经纪人实现。

此类将来自 Oanda 的订单/持仓映射到 `backtrader` 的内部 API。

参数：

+   `use_positions`（默认值：`True`）：连接到经纪人提供者时，使用现有仓位启动经纪人。

    在实例化期间设置为 `False` 以忽略任何现有仓位

### OandaData

#### class backtrader.feeds.OandaData(**kwargs)

Oanda 数据源。

参数：

+   `qcheck`（默认值：`0.5`）

    如果未收到数据，则在苏醒的秒数内将给出重新采样/重播数据包的机会，并将通知传递给链上

+   `historical`（默认值：`False`）

    如果设置为 `True`，数据源将在第一次下载数据后停止。

    将使用标准数据源参数 `fromdate` 和 `todate` 作为参考。

    如果请求的持续时间大于 IB 允许的时间跨度/压缩所选择的数据的持续时间，数据源将进行多次请求。

+   `backfill_start`（默认值：`True`）

    在开始时执行回填。将通过单个请求获取最大可能的历史数据。

+   `backfill`（默认：`True`）

    在断开/重新连接周期后执行回填。间隙持续时间将用于下载可能的最小数据量

+   `backfill_from`（默认：`None`）

    可以传递额外的数据源来进行初始的回填层。一旦数据源用尽并且如果请求，将从 IB 进行回填。理想情况下，这是为了从已存储的源（如磁盘上的文件）进行回填，但不限于此。

+   `bidask`（默认：`True`）

    如果为`True`，则历史/回填请求将从服务器请求*bid/ask*价格

    如果为`False`，则将请求*midpoint*

+   `useask`（默认：`False`）

    如果为`True`，则将使用*bidask*价格的*ask*部分，而不是默认的*bid*使用方式

+   `includeFirst`（默认：`True`）

    通过直接设置参数到 Oanda API 调用来影响历史/回填请求的第一个柱条的交付

+   `reconnect`（默认：`True`）

    当网络连接断开时重新连接

+   `reconnections`（默认：`-1`）

    重新连接尝试的次数：`-1`表示永远

+   `reconntimeout`（默认：`5.0`）

    在重新连接尝试之间等待的时间（秒）

此数据源仅支持`timeframe`和`compression`的以下映射，这些映射符合 OANDA API 开发人员指南中的定义：

```py
`(TimeFrame.Seconds, 5): 'S5',
(TimeFrame.Seconds, 10): 'S10',
(TimeFrame.Seconds, 15): 'S15',
(TimeFrame.Seconds, 30): 'S30',
(TimeFrame.Minutes, 1): 'M1',
(TimeFrame.Minutes, 2): 'M3',
(TimeFrame.Minutes, 3): 'M3',
(TimeFrame.Minutes, 4): 'M4',
(TimeFrame.Minutes, 5): 'M5',
(TimeFrame.Minutes, 10): 'M10',
(TimeFrame.Minutes, 15): 'M15',
(TimeFrame.Minutes, 30): 'M30',
(TimeFrame.Minutes, 60): 'H1',
(TimeFrame.Minutes, 120): 'H2',
(TimeFrame.Minutes, 180): 'H3',
(TimeFrame.Minutes, 240): 'H4',
(TimeFrame.Minutes, 360): 'H6',
(TimeFrame.Minutes, 480): 'H8',
(TimeFrame.Days, 1): 'D',
(TimeFrame.Weeks, 1): 'W',
(TimeFrame.Months, 1): 'M',` 
```

任何其他组合都将被拒绝
