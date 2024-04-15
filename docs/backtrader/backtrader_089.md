# Visual Chart

> 原文：[`www.backtrader.com/docu/live/vc/vc/`](https://www.backtrader.com/docu/live/vc/vc/)

与 Visual Chart 的集成支持两者： 

+   *实时数据*提供

+   *实时交易*

*Visual Chart*是完整的交易解决方案：

+   在单个平台上集成图表、数据源和经纪功能

    更多信息，请访问：[www.visualchart.com](http://www.visualchart.com)

## 需求

+   *VisualChart 6*

+   *Windows* - VisualChart 正在运行的平台

+   `comtypes`分支：[`github.com/mementum/comtypes`](https://github.com/mementum/comtypes)

    使用以下命令安装：`pip install https://github.com/mementum/comtypes/archive/master.zip`

    *Visual Chart* API 基于*COM*。

    当前的`comtypes`主分支不支持对`VT_RECORD`的`VT_ARRAYS`的解包。这是由*Visual Chart*使用的

    [Pull Request #104](https://github.com/enthought/comtypes/pull/104)已提交但尚未集成。一旦集成，就可以使用主分支。

+   `pytz`（可选，但真的很推荐）

    确保每个数据都在市场时间内返回。

    对大多数市场而言这是真实的，但有些市场确实是例外情况（`全球指数`是一个很好的例子）

    *Visual Chart*内部的时间管理及其与*COM*传递的时间的关系是复杂的，并且使用`pytz`倾向于简化事情。

## 示例代码

源代码包含一个完整的示例：

+   `samples/vctest/vctest.py`

示例无法涵盖每种可能的用例，但它试图提供广泛的见解，并应强调在使用回测模块或实时数据模块时没有真正的区别。

可以指出一件事：

+   在进行任何交易活动之前，示例等待`data.LIVE`数据状态通知。

    这可能是任何实时策略中需要考虑的事情。

## VCStore - 存储库

存储库是实时数据源/交易支持的关键，为*COM* API 和数据源以及经纪人代理的需求之间提供一层适应性。

+   提供获取*经纪人*实例的方法：

    +   `VCStore.getbroker(*args, **kwargs)`

+   提供对获取器*数据*源实例的访问

    +   `VCStore.getedata(*args, **kwargs)`

    在这种情况下，许多`**kwargs`对于数据源都是常见的，如`dataname`，`fromdate`，`todate`，`sessionstart`，`sessionend`，`timeframe`，`compression`

    数据可能提供其他参数。请查看下面的参考资料。

`VCStore`将尝试：

+   使用*Windows Registry*自动定位*VisualChart*在系统中的位置

    +   如果找到，将扫描安装目录以查找*COM* DLL 以创建*COM* *typelibs*，并能够实例化适当的对象。

    +   如果未找到，则将尝试使用已知的和硬编码的*CLSIDs*执行相同操作。

注意

即使可以通过扫描文件系统找到 DLL，*Visual Chart*本身也必须正在运行。backtrader 不会启动*Visual Chart*

`VCStore`的其他职责：

+   保持对 *Visual Chart* 与服务器的连接状态的一般跟踪

## VCData feeds

### 通用

*Visual Chart* 提供的数据源具有一些有趣的特性：

+   **重新采样**由平台完成

    并非所有情况：*秒*不受支持，仍需由 *backtrader* 完成

    因此，只有在处理秒数时，最终用户才需要执行：

    ```py
    `vcstore = bt.stores.VCStore()
    vcstore.getdata(dataname='015ES', timeframe=bt.TimeFrame.Ticks)
    cerebro.resampledata(data, timeframe=bt.TimeFrame.Seconds, compression=5)` 
    ```

    在所有其他情况下，仅需要：

    ```py
    `vcstore = bt.stores.VCStore()
    data = vcstore.getdata(dataname='015ES', timeframe=bt.TimeFrame.Minutes, compression=2)
    cerebro.addata(data)` 
    ```

数据将通过比较内部设备时钟和平台提供的`ticks`来内部计算`timeoffset`，以便在没有新的`ticks`到达时尽快提供*自动*重新采样的柱状图。

实例化数据：

+   将在 *VisualChart* 左上方看到的符号传递，不包括空格。 例如：

    +   *ES-Mini* 显示为 `001 ES`。 实例化它为：

    ```py
    `data = vcstore.getdata(dataname='001ES', ...)` 
    ```

    +   *EuroStoxx 50* 显示为 `015 ES`。 实例化它为：

    ```py
    `data = vcstore.getdata(dataname='015ES', ...)` 
    ```

注意

*backtrader* 将尽力清除位于名称直接从 *Visual Chart* 粘贴的第四个位置的空格。

### 时间管理

时间管理遵循 *backtrader* 的一般规则

+   给出*市场*时间，以确保代码不依赖于在不同时间发生 DST 转换，并使本地时间对时间比较不可靠。

这适用于 *Visual Chart* 中的大多数市场，但对于某些市场进行了特定的管理：

+   交换 `096` 中的数据被命名为 `International Indices`。

    理论上这些被报告为位于`Europe/London`时区，但测试表明这似乎只是部分正确，某些内部管理措施已经覆盖了它。

可以通过传递参数 `usetimezones=True` 来启用实际*时区*的时间管理。 如果可用，会尝试使用 `pytz`。 并不需要，因为对于大多数市场，*Visual Chart* 提供的内部时间偏移量允许无缝转换到市场时间。

在任何情况下，报告`096.DJI`位于`Europe/London`时间似乎是毫无意义的，当实际上它位于`US/Eastern`时。 因此，`backtrader` 将在后者中报告它。 在这种情况下，强烈推荐使用 `pytz`。

注意

*道琼斯工业指数*（不是全球版本）位于 `099I-DJI`

注意

所有这些时间管理都在 DST 转换期间进行真正的测试，期间本地和远程市场发生了与 DST 相关的不同步现象。

`VCDATA`中定义了输出*时区*的`International Indices`列表：

```py
`'096.FTSE': 'Europe/London',
'096.FTEU3': 'Europe/London',
'096.MIB30': 'Europe/Berlin',
'096.SSMI': 'Europe/Berlin',
'096.HSI': 'Asia/Hong_Kong',
'096.BVSP': 'America/Sao_Paulo',
'096.MERVAL': 'America/Argentina/Buenos_Aires',
'096.DJI': 'US/Eastern',
'096.IXIC': 'US/Eastern',
'096.NDX': 'US/Eastern',` 
```

#### 小时间问题

使用给定的**时间**而不是默认的 `00:00:00` 传递 `fromdate` 或 `todate` 似乎会在 *COM* API 中创建一个过滤器，并且任何日期的柱状图只会在给定时间之后交付。

因此：

+   请仅向 `VCData` 传递**完整日期**，如下所示：

    ```py
    `data = vcstore.getdata(dataname='001ES', fromdate=datetime(2016, 5, 15))` 
    ```

    并非::

    ```py
    `data = vcstore.getdata(dataname=‘001ES’, fromdate=datetime(2016, 5, 15, 8, 30))` 
    ```

#### 补偿时间长度

如果最终用户未指定`fromdate`，平台将自动尝试回填，然后继续实时数据。回填是时间框架相关的，如下：

+   `Ticks`，`MicroSeconds`，`Seconds`：**1 天**

    对于给定的 3 个时间框架，*秒*和*微秒*不受*Visual Chart*直接支持，通过*Ticks*的重新采样完成

+   `分钟`：**2 天**

+   `天数`：**1 年**

+   `周`：**2 年**

+   `月份`：**5 年**

+   `月份`：**20 年**

定义的回填期将乘以请求的`压缩`，即：如果*时间框架*是`分钟`，*压缩*是 5，则最终*回填期*将是：`2 天 * 5 -> 10 天`

### 交易数据

*Visual Chart*提供**连续未来**。无需手动管理，您可以跟踪所选的未来而无需中断。这是一个优势，也提出了一个小挑战：

+   `ES-Mini`是`001ES`，但实际交易资产（例如：Sep-2016）是`ESU16`。

为了克服这一点，并允许跟踪*连续未来*并交易*真实资产*的策略，在数据实例化期间可以指定以下内容：

```py
`data = vcstore.getdata(dataname='001ES', tradename='ESU16')` 
```

交易将在`ESU16`上进行，但数据源将来自`001ES`（数据相同，为期 3 个月）

### 其他参数

+   `qcheck`（默认：`0.5`秒）控制唤醒与内部重新采样器/重播器交流的频率，以避免延迟传递条形图。

    将应用以下逻辑来使用此参数：

    +   如果检测到内部*重新采样/重播*，则将使用该值。

    +   如果未检测到内部*重新采样/重播*，数据源将不会唤醒，因为没有要报告的内容。

    数据源仍将唤醒以检查*Visual Chart*内置的重新采样器，但这是自动控制的。

### 数据通知

数据源将通过以下一种或多种方式报告当前状态（检查*Cerebro*和*Strategy*参考）

+   `Cerebro.notify_data`（如果被覆盖）

+   使用`Cerebro.adddatacb`添加的回调

+   `Strategy.notify_data`（如果被覆盖）

*策略*内的一个示例：

```py
`class VCStrategy(bt.Strategy):

    def notify_data(self, data, status, *args, **kwargs):

        if status == data.LIVE:  # the data has switched to live data
           # do something
           pass` 
```

系统发生变化后将发送以下通知：

+   `CONNECTED`

    成功初始连接时发送

+   `DISCONNECTED`

    在这种情况下，不再可能检索数据，数据将指示系统无法执行任何操作。可能的情况：

    +   指定了错误的合同

    +   在历史下载期间中断

    +   尝试重新连接到 TWS 的次数超过了

+   `CONNBROKEN`

    连接已丢失，无论是到 TWS 还是到数据中心。数据源将尝试（通过存储）重新连接和回填，必要时，并恢复操作

+   `NOTSUBSCRIBED`

    合同和连接正常，但由于权限不足，无法检索数据。

    数据将指示系统无法检索数据

+   `DELAYED`

    表示*历史*/*回填*操作正在进行中，并且策略处理的数据不是实时数据

+   `LIVE`

    表示从这一点开始要处理的数据是实时数据

*策略*的开发人员应考虑在发生断开连接或接收**延迟**数据时采取哪些行动。

## VCBroker - 实时交易

### 使用经纪人

要使用*VCBroker*，必须替换由*cerebro*创建的标准经纪人模拟实例。

使用*Store*模型（首选）：

```py
`import backtrader as bt

cerebro = bt.Cerebro()
vcstore = bt.stores.VCStore()
cerebro.broker = vcstore.getbroker()  # or cerebro.setbroker(...)` 
```

### 经纪人参数

无论是直接还是通过`getbroker`，`VCBroker`经纪人都不支持参数。 这是因为经纪人只是对真实*经纪人*的代理。 真正的经纪人给出的，不应被拿走。

### 限制

#### 仓位

*Visual Chart*报告**持仓**。 这在大多数情况下可以用来控制实际仓位，但缺少指示*仓位*已关闭的最终事件。

这使得*backtrader*必须对*Position*进行完整的会计核算，并与您帐户中任何先前存在的仓位分开

#### 佣金

*COM*交易界面不报告佣金。 *backtrader*没有机会做出合理猜测，除非：

+   *经纪人*与指示实际发生的佣金的*佣金*实例一起实例化。

### 与其进行交易

#### 账户

*Visual Chart*在一个经纪人上同时支持多个帐户。 可以使用以下参数控制选择的帐户：

+   `account`（默认值：`None`）

    VisualChart 支持同时在经纪人上使用多个帐户。 如果使用默认值`None`，则将使用 ComTrader `Accounts`集合中的第一个帐户。

    如果提供了帐户名称，则将检查并使用`Accounts`集合（如果存在）

#### 操作

关于标准用法没有变化。 只需使用策略中可用的方法（有关详细说明，请参阅`Strategy`参考）

+   `buy`

+   `sell`

+   `close`

+   `cancel`

### 返回的订单对象

+   标准*backtrader* `Order`对象

### 订单执行类型

*Visual Chart*支持*backtrader*所需的最小订单执行类型，因此，任何经过回测的内容都可以实时执行。

因此，订单执行类型仅限于*经纪人模拟*中可用的类型：

+   `Order.Market`

+   `Order.Close`

+   `Order.Limit`

+   `Order.Stop`（当*Stop*触发时，*Market*订单随后进行）

+   `Order.StopLimit`（当*Stop*触发时，*Limit*订单随后进行）

### 订单有效性

在回测期间可用的相同有效性概念（用于`valid`到`buy`和`sell`）可用，并具有相同含义。 因此，对于以下值，`valid`参数转换如下以用于*Visual Chart Orders*：

+   `None`翻译为*Good Til Cancelled*

    因为未指定有效期，因此理解订单必须有效直至取消

+   `datetime/date`翻译为*Good Til Date*

    注意

    注意：*Visual Chart*仅支持“完整日期”，并且忽略了*时间*部分。

+   `timedelta(x)` 翻译为 *有效期至*（这里 `timedelta(x) != timedelta()`）

    注释

    注意：*Visual Chart* 仅支持**完整日期**，并且会将*时间*部分丢弃。

    这被解释为指示订单从 `now` + `timedelta(x)` 开始有效

+   `timedelta() or 0` 翻译为 *会话*

    已传递一个值（而不是 `None`），但是值为 *Null*，并被解释为仅在当前 *日期*（会话）有效的订单

### 通知

标准 `Order` 状态将通过方法 `notify_order`（如果被覆盖）通知到一个 *策略* 上

+   `已提交` - 订单已发送至 TWS

+   `已接受` - 订单已下达

+   `已拒绝` - 订单放置失败或在其生命周期内被系统取消

+   `部分完成` - 部分执行已经发生

+   `已完成` - 订单已完全执行

+   `已取消`（或 `取消`）

+   `已过期` - 目前尚未报告。需要一种启发式方法来区分此状态与 `取消` 的区别

## 参考

### VCStore

#### 类 backtrader.stores.VCStore()

包装 ibpy ibConnection 实例的单例类。

这些参数也可以在使用该存储的类中指定，例如 `VCData` 和 `VCBroker`

### VCBroker

#### 类 backtrader.brokers.VCBroker(**kwargs)

VisualChart 的经纪商实现。

此类将 VisualChart 的订单/持仓映射到 `backtrader` 的内部 API。

参数：

+   `账户`（默认值：None）

    VisualChart 支持在经纪商上同时使用多个账户。如果默认值为 `None`，则将使用 ComTrader `Accounts` 集合中的第 1 个账户。

    如果提供了账户名称，则将检查并使用 `Accounts` 集合（如果存在）

+   `佣金`（默认值：None）

    如果未传递佣金方案，则将自动生成对象

    有关更多解释，请参阅下面的注释

### 注释

+   持仓

VisualChart 通过 ComTrader 接口报告“OpenPositions”更新，但仅当持仓具有“大小”时。指示持仓已移至零的更新通过不存在此类持仓来报告。这强制通过观察执行事件来保持持仓的会计，就像模拟经纪商一样。

+   佣金

VisualChart 的 ComTrader 接口不报告佣金，因此自动生成的 CommissionInfo 对象不能使用不存在的佣金来正确计算它们。为了支持佣金，必须传递带有适当佣金方案的 `commission` 参数。

佣金方案的文档详细介绍了如何实现这一点

+   过期时间

ComTrader 接口（或者是 comtypes 模块？）从 `datetime` 对象中丢弃了 `time` 信息，并且到期日期始终是完整日期。

+   过期报告

目前还没有启发式方法来确定取消的订单何时因过期而取消。因此，过期订单被报告为已取消。

### VCData

#### 类 backtrader.feeds.VCData(**kwargs)

VisualChart 数据源。

参数：

+   `qcheck`（默认值：`0.5`）唤醒以便让重采样器/重播器检查当前柱是否可以进行交付的默认超时时间

    该值仅在数据中插入了重采样/重播过滤器时才会使用

+   `historical`（默认值：`False`）如果没有提供`todate`参数（在基类中定义），则将强制仅进行历史下载（如果设置为`True`）

    如果提供了`todate`，则可以实现相同的效果

+   `milliseconds`（默认值：`True`）由*Visual Chart*构建的柱状图具有如下外观：HH:MM:59.999000

    如果该参数设置为`True`，将会在时间上增加一毫秒，使其看起来像是：HH::MM + 1:00.000000

+   `tradename`（默认值：`None`）连续期货无法交易，但非常适合数据跟踪。如果提供了该参数，它将是当前期货的名称，该名称将成为交易资产。示例：

    +   001ES -> ES-Mini 连续提供作为`dataname`

    +   ESU16 -> ES-Mini 2016-09\. 如果在`tradename`中提供了此信息，它将成为交易资产。

+   `usetimezones`（默认值：`True`）对于大多数市场，*Visual Chart*提供的时间偏移信息允许将日期时间转换为市场时间（*backtrader*选择的表示方式）

    一些市场是特殊的（`096`），需要特殊的内部覆盖和时区支持以显示用户预期的市场时间。

    如果该参数设置为`True`，将尝试导入`pytz`以使用时区（默认值）

    禁用它将取消时区使用（可能有助于减轻负载过重的情况）
