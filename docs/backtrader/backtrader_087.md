# 交互式经纪人

> 原文：[`www.backtrader.com/docu/live/ib/ib/`](https://www.backtrader.com/docu/live/ib/ib/)

与交互式经纪人的集成支持两种：

+   *实时数据*提供

+   *实时交易*

注意

尽管尝试测试尽可能多的错误条件和情况，但代码可能（像任何其他软件一样）包含错误。

在进入生产之前，彻底测试任何策略都使用**模拟交易**帐户或 TWS **演示**。

注意

使用`IbPy`模块进行与交互式经纪人的交互必须事先安装。在写作时，Pypi 中没有包，但可以使用以下命令使用`pip`进行安装：

```py
pip install git+https://github.com/blampe/IbPy.git
```

如果您的系统中没有`git`可用（Windows 安装？），则以下内容也应该有效：

```py
pip install https://github.com/blampe/IbPy/archive/master.zip
```

## 示例代码

源代码包含完整的示例：

+   samples/ibtest/ibtest.py

示例不能涵盖每种可能的用例，但它试图提供广泛的见解，并应该突出显示在使用回测模块或实时数据模块时没有真正的区别

有一件事可以确定：

+   示例在进行任何交易活动之前等待`data.LIVE`数据状态通知。

    这可能是在任何实时策略中考虑的事情

## 商店模型 vs 直接模型

与交互式经纪人的交互支持通过 2 种模式：

1.  商店模型（*首选*）

1.  与数据源类和经纪人类的直接交互

商店模型在创建*经纪人*和*数据源*时提供了清晰的分离模式。两个代码片段应该更好地作为示例。

首先是**Store**模型：

```py
import backtrader as bt

ibstore = bt.stores.IBStore(host='127.0.0.1', port=7496, clientId=35)
data = ibstore.getdata(dataname='EUR.USD-CASH-IDEALPRO')
```

这里是参数：

+   `host`、`port`和`clientId`被传递到它们所属的地方`IBStore`，该地方使用这些参数打开连接。

然后使用`getdata`创建一个与*backtrader*中所有数据源共用的**数据**源。

+   `dataname`请求*EUR*/*USD*外汇对。

现在可以直接使用了：

```py
import backtrader as bt

data = bt.feeds.IBData(dataname='EUR.USD-CASH-IDEALPRO',
                       host='127.0.0.1', port=7496, clientId=35)
```

这里：

+   用于商店的参数被传递给数据。

+   这些将用于在后台创建`IBStore`实例

缺点：

+   清晰度大大降低，因为不清楚什么属于数据，什么属于商店。

## IBStore - 商店

商店是实时数据源/交易支持的关键，提供了一个在`IbPy`模块和数据源和经纪人代理的需求之间的适应层。

*商店*是一个涵盖以下功能的概念：

+   作为一个实体的中心商店：在这种情况下，实体是 IB。

    这可能需要或不需要参数

+   提供通过该方法获取*经纪人*实例的访问权限：

    +   `IBStore.getbroker(*args, **kwargs)`

+   提供获取*数据*源实例的访问权限

    +   `IBStore.getdata(*args, **kwargs)`

    在这种情况下，许多`**kwargs`与数据源相同，如`dataname`、`fromdate`、`todate`、`sessionstart`、`sessionend`、`timeframe`、`compression`

    数据可能提供其他参数。请查看下面的参考资料。

`IBStore` 提供：

+   连接目标（`host` 和 `port` 参数）

+   标识（`clientId` 参数）

+   重新连接控制（`reconnect` 和 `timeout` 参数）

+   时间偏移检查（`timeoffset` 参数，请参见下文）

+   通知和调试

    `notifyall`（默认：`False`）：在这种情况下，IB 发送的任何*错误*消息（许多只是信息性的）都将被转发给*Cerebro*/*Strategy*

    `_debug`（默认：`False`）：在这种情况下，从 TWS 收到的每条消息都将打印到标准输出

## IBData 数据源

### 数据选项

无论是直接还是通过 `getdata`，`IBData` 数据源支持以下数据选项：

+   历史下载请求

    如果持续时间超过 IB 对于给定*时间框架/压缩*组合施加的限制，这些将分成多个请求

+   3 种实时数据

    +   `tickPrice` 事件（通过 IB `reqMktData`）

    用于*CASH*产品（至少 TWS API 9.70 的实验表明不支持其他类型）

    通过查看`BID`价格接收*tick*价格事件，根据非官方互联网文献，这似乎是跟踪`CASH`市场价格的方法。

    时间戳是在系统中本地生成的。如果用户希望，可以使用与 IB 服务器时间的偏移量（从 IB `reqCurrentTime` 计算）

    +   `tickString` 事件（又名 `RTVolume`（通过 IB `reqMktData`））

    大约每 250 毫秒从 IB 接收一个*OHLC/Volume*快照（如果没有发生交易，则间隔可能更长）

    +   `RealTimeBars` 事件（通过 IB `reqRealTimeBars`）

    每 5 秒接收历史数据条（由 IB 固定持续时间）

    如果所选的*时间框架/组合*低于*秒/5*级别，此功能将自动禁用。

    !!! 注意

    ```py
     ``RealTimeBars` do not work with the TWS Demo` 
    ```

    默认行为是在大多数情况下使用：`tickString`，除非用户明确希望使用`RealTimeBars`

+   `Backfilling`

    除非用户要求只进行*历史*下载，否则数据源将自动进行回填：

    +   **在开始时**：使用最大可能的持续时间。例如：对于*天/1*（*时间框架/压缩*）组合，IB 的最大默认持续时间是*1 年*，这是将进行回填的时间量

    +   **在数据断开连接后**：在这种情况下，用于回填操作的数据量将通过查看断开连接前接收的最新数据来减少。

注意

请注意，最终考虑的*时间框架/压缩*组合可能不是在*数据源创建*期间指定的，而是在系统中*插入*期间指定的。请参见以下示例：

```py
data = ibstore.getdata(dataname='EUR.USD-CASH-IDEALPRO',
                       timeframe=bt.TimeFrame.Seconds, compression=5)

cerebro.resampledata(data, timeframe=bt.TimeFrame.Minutes, compression=2)
```

如现在应该清楚的是，最终考虑的*时间框架/压缩*组合是*分钟/2*

### 数据合同检查

在启动阶段，*数据*源将尝试下载指定合同的详细信息（查看如何指定的参考资料）。如果找不到这样的合同或找到多个匹配项，则数据将拒绝继续并将其通知系统。一些例子。

简单但明确的合同规范：

```py
data = ibstore.getdata(dataname='TWTR')  # Twitter
```

只会找到一个实例（2016-06），因为对于默认类型`STK`、交易所`SMART`和货币（默认为空）的单一合同交易将被找到。

使用`AAPL`的类似方法会失败：

```py
data = ibstore.getdata(dataname='AAPL')  # Error -> multiple contracts
```

因为`SMART`可以在几个真实交易所找到合同，并且`AAPL`在其中一些交易所以不同的货币交易。以下是可以的：

```py
data = ibstore.getdata(dataname='AAPL-STK-SMART-USD')  # 1 contract found
```

### 数据通知

数据源将通过以下一种或多种方式报告当前状态（查看*Cerebro*和*Strategy*参考资料）

+   `Cerebro.notify_data`（如果重写）

+   使用`Cerebro.adddatacb`添加回调

+   `Strategy.notify_data`（如果重写）

在*strategy*内部的一个例子：

```py
class IBStrategy(bt.Strategy):

    def notify_data(self, data, status, *args, **kwargs):

        if status == data.LIVE:  # the data has switched to live data
           # do something
           pass
```

系统发生更改后，将发送以下通知：

+   `CONNECTED`

    成功初始连接时发送

+   `DISCONNECTED`

    在这种情况下，不再能够检索数据，并且数据将指示系统无法执行任何操作。可能的条件：

    +   指定的合同有误

    +   在历史下载期间中断

    +   尝试重新连接到 TWS 的次数已超过限制

+   `CONNBROKEN`

    与 TWS 或数据中心的连接已断开。数据源将尝试（通过存储）重新连接和回溯填充，必要时，并恢复操作

+   `NOTSUBSCRIBED`

    合同和连接都正常，但由于权限不足，无法检索数据。

    数据将向系统指示无法检索数据

+   `DELAYED`

    表示正在进行*历史*/*回溯*操作，并且由策略处理的数据不是实时数据

+   `LIVE`

    表示从此处开始由*strategy*处理的数据是实时数据

*策略*的开发者应该考虑在发生断开连接或接收**延迟**数据等情况时要采取哪些行动。

### 数据时间框架和压缩

*backtrader*生态系统中的数据源，在创建时支持`timeframe`和`compression`参数。这些参数也可以通过`data._timeframe`和`data._compression`属性访问

*timeframe/compression*组合的重要性在将数据通过`resampledata`或`replaydata`传递给`cerebro`实例时具有特定目的，以便内部重新采样器/重播器对象了解预期目标是什么。当重新采样/重播时，`._timeframe`和`._compression`将在数据中被覆盖。

但在另一方面，对于实时数据源，这些信息可能起重要作用。请参阅以下示例：

```py
data = ibstore.getdata(dataname='EUR.USD-CASH-IDEALPRO',
                       timeframe=bt.TimeFrame.Ticks,
                       compression=1,  # 1 is the default
                       rtbar=True,  # use RealTimeBars
                      )
cerebro.adddata(data)
```

用户正在请求**tick**数据，这很重要，因为：

+   不会进行回溯填充（IB 支持的最小单位是*Seconds/1*）

+   即使请求和支持`dataname`的`RealTimeBars`，也不会使用，因为`RealTimeBar`的最小分辨率是*Seconds/5*

无论如何，除非使用*Ticks/1*的分辨率，否则数据必须进行*重新采样/重播*。上述情况下与实时条和工作：

```py
data = ibstore.getdata(dataname='TWTR-STK-SMART', rtbar=True)
cerebro.resampledata(data, timeframe=bt.TimeFrame.Seconds, compression=20)
```

在这种情况下，并且如上所述，在`resampledata`期间将覆盖数据的`._timeframe`和`._compression`属性。这是会发生的事情：

+   将会发生回溯填充，请求分辨率为*Seconds/20*

+   `RealTimeBars`将用于实时数据，因为分辨率等于/大于*Seconds/5*且数据支持（不是*CASH*产品）

+   TWS 发送给系统的事件最多每 5 秒发生一次。这可能不重要，因为系统每 20 秒只向策略发送一个条。

没有`RealTimeBars`的情况下相同：

```py
data = ibstore.getdata(dataname='TWTR-STK-SMART')
cerebro.resampledata(data, timeframe=bt.TimeFrame.Seconds, compression=20)
```

在这种情况下：

+   将会发生回溯填充，请求分辨率为*Seconds/20*

+   `tickString`将用于实时数据，因为（不是*CASH*产品）

+   TWS 发送给系统的事件最多每 250 毫秒发生一次。这可能不重要，因为系统每 20 秒只向策略发送一个条。

最后，对于*CASH*产品和最多 20 秒：

```py
data = ibstore.getdata(dataname='EUR.USD-CASH-IDEALPRO')
cerebro.resampledata(data, timeframe=bt.TimeFrame.Seconds, compression=20)
```

在这种情况下：

+   将会发生回溯填充，请求分辨率为*Seconds/20*

+   `tickPrice`将用于实时数据，因为这是现金产品

    即使添加了`rtbar=True`

+   TWS 发送给系统的事件最多每 250 毫秒发生一次。这可能不重要，因为系统每 20 秒只向策略发送一个条。

### 时间管理

数据源将自动从*TWS*报告的`ContractDetails`对象中确定时区。

注意

这要求安装`pytz`。如果未安装，则用户应为数据源的`tz`参数提供与所需输出时区兼容的`tzinfo`实例

注意

如果安装了`pytz`并且用户认为自动时区确定不起作用，则`tz`参数可以包含一个时区名称的字符串。`backtrader`将尝试使用给定名称实例化一个`pytz.timezone`

报告的`datetime`将是与产品相关的时区的时间。一些示例：

+   *产品*: 欧洲证券交易所的 EuroStoxxx 50（股票代码：*ESTX50-YYYYMM-DTB*)

    时区将是`CET`（中欧时间），又名`Europe/Berlin`

+   *产品*: ES-Mini（股票代码：*ES-YYYYMM-GLOBEX*)

    时区将是`EST5EDT`，又名`EST`，又名`US/Eastern`

+   *产品*: EUR.JPY 外汇对（股票代码*EUR.JPY-CASH-IDEALPRO*)

    时区将是`EST5EDT`，又名`EST`，又名`US/Eastern`

    实际上这是一个交互式经纪商的设置，因为外汇交易几乎连续 24 小时进行，因此对于它们不会有真正的时区。

这种行为确保交易保持一致，无论交易者的实际位置如何，因为计算机很可能具有实际位置的时区，而不是交易场所的时区。

请阅读手册的**时间管理**部分。

注意

TWS Demo 在没有数据下载权限的资产的时区报告方面并不准确（欧洲斯托克 50 期货就是这种情况的一个例子）

### 实时数据源和重采样/重播

关于何时为实时数据源交付条的设计决策是：

+   尽可能实时地交付它们

这可能看起来很明显，对于`Ticks`的时间框架来说是这样，但如果*重采样/重播*起作用，延迟可能会发生。用例：

+   重采样配置为*Seconds/5*，具有：

    ```py
    `cerebro.resampledata(data, timeframe=bt.TimeFrame.Seconds, compression=5)` 
    ```

+   一个时间为`23:05:27.325000`的 tick 被交付

+   在市场上交易速度很慢，下一个 tick 将在`23:05:59.025000`时到达

也许并不明显，但*backtrader*并不知道交易速度非常慢，下一个 tick 大约会在`32`秒后到来。如果没有适当的措施，一个时间为`23:05:30.000000`的重采样条可能会被延迟约`29 秒`。

这就是为什么实时数据源每隔`x`秒（*float*值）唤醒一次*重采样器/重播器*并让它知道没有新数据输入。这是在创建实时数据源时通过参数`qcheck`（默认值：`0.5`秒）来控制的。

这意味着重采样器每隔`qcheck`秒就有机会交付一个条，如果本地时钟显示，重采样周期已经结束。这样一来，上述情景（`23:05:30.000000`）的重采样条最多会在报告时间后`qcheck`秒交付。

因为默认值是`0.5`，所以最晚时间是：`23:05:30.500000`。几乎比以前早了 29 秒。

缺点：

+   *有些 tick 可能会对已经交付的重采样/重播的条造成延迟*

如果在交付后，TWS 从服务器收到一个时间戳为`23:05:29.995\`000`的延迟消息，这对于已经向系统报告的时间`23:05.30.000000`来说就太晚了

这通常发生在以下情况下：

+   `timeoffset`在`IBStore\`中被禁用（设置为`False`），*IB*报告的时间与本地时钟的时间差很大。

避免大部分延迟样本的最佳方法是：

+   增加`qcheck`值，以考虑延迟消息：

    ```py
    `data = ibstore.getdata('TWTR', qcheck=2.0, ...)` 
    ```

这应该增加额外的空间，即使延迟了*重采样/重播*条的交付

注意

当然，对于*Seconds/5*的重采样来说，2.0 秒的延迟意义不同于*Minutes/10*的重采样

如果由于某种原因，最终用户希望禁用`timeoffset`并且不通过`qcheck`进行管理，则仍然可以接受延迟样本：

+   在`getdata` / `IBData`的参数中设置`_latethrough`为`True`：

    ```py
    `data = ibstore.getdata('TWTR', _latethrough=True, ...)` 
    ```

+   在*重采样/重播*时设置`takelate`为`True`：

    ```py
    `cerebro.resampledata(data, takelate=True)` 
    ```

## IBBroker - 实时交易

注意

在*backtrader*中的*经纪人模拟*中实现了`tradeid`功能。这允许正确地跟踪在同一资产上并行执行的交易，并将佣金正确地分配给适当的`tradeid`

此概念在此实时经纪人中不受支持，因为佣金是由经纪人报告的，而在某些情况下，不可能将其分离为不同的`tradeid`值。

`tradeid`仍然可以指定，但不再有意义。

### 使用经纪人

要使用*IB Broker*，必须替换由*cerebro*创建的标准经纪人模拟实例。

使用*Store*模型（首选）：

```py
import backtrader as bt

cerebro = bt.Cerebro()
ibstore = bt.stores.IBStore(host='127.0.0.1', port=7496, clientId=35)
cerebro.broker = ibstore.getbroker()  # or cerebro.setbroker(...)
```

使用直接方法：

```py
import backtrader as bt

cerebro = bt.Cerebro()
cerebro.broker = bt.brokers.IBBroker(host='127.0.0.1', port=7496, clientId=35)
```

### 经纪人参数

不管是直接还是通过`getbroker`，`IBBroker`经纪人都不支持任何参数。这是因为经纪人只是一个真实*经纪人*的代理。真实经纪人给予的，不应被剥夺。

### 一些限制

#### 现金和价值报告

当内部的*backtrader*经纪人模拟在调用策略的`next`方法之前对`value`（净清算价值）和`cash`进行计算时，无法保证与实时经纪人相同。

+   如果请求了值，则可能会延迟`next`的执行，直到回答到达

+   经纪人可能尚未计算出这些值

*backtrader*告诉 TWS 在它们更改时提供更新的值（*backtrader*订阅`accounUpdate`消息），但它不知道消息何时到达。

`IBBroker`的`getcash`和`getvalue`方法报告的值始终是从 IB 接收到的最新值。

注意

进一步的限制是，即使有更多货币的值可用，这些值也以帐户的基本货币报告。这是一个设计选择。

#### 位置

*backtrader*使用 TWS 报告的资产的`Position`（价格和大小）。在*order execution*和*order status*消息后，内部计算可以被使用，但是如果其中一些消息被错过（套接字有时会丢失数据包），则计算将无法进行。

当然，如果连接到 TWS 时将执行交易的资产已经有一个开放的头寸，由于初始偏移，策略所做的`Trades`的计算将不会像通常一样工作

### 与其进行交易

就标准用法而言，没有变化。只需使用策略中可用的方法（有关完整说明，请参阅`Strategy`参考资料）

+   `buy`

+   `sell`

+   `close`

+   `cancel`

### 返回的订单对象

+   与 backtrader 的`Order`对象兼容（在同一层次结构中的子类）

### 订单执行类型

IB 支持各种执行类型，其中一些由 IB 模拟，一些由交易所本身支持。最初支持哪些订单执行类型的决定有一个动机：

+   与*backtrader*中可用的*经纪人模拟*兼容性

    这是因为经过回测的内容将会投入生产。

因此，订单执行类型仅限于*broker simulation*中可用的类型：

+   `Order.Market`

+   `Order.Close`

+   `Order.Limit`

+   `Order.Stop`（当*Stop*触发时，*Market*订单跟随）

+   `Order.StopLimit`（当*Stop*触发时，*Limit*订单跟随）

注意

停止触发是根据不同的策略由 IB 执行的。*backtrader*不修改默认设置，即为`0`：

```py
0 - the default value. The "double bid/ask" method will be used for
orders for OTC stocks and US options. All other orders will use the
"last" method.
```

如果用户希望修改此项，可以根据 IB 文档提供的额外`**kwargs`向`buy`和`sell`提供。例如，在策略的`next`方法中：

```py
def next(self):
    # some logic before
    self.buy(data, m_triggerMethod=2)
```

这已更改策略为`2`（*“last”方法，其中停止订单基于最后价格触发*）

请参阅 IB API 文档以获取有关停止触发的进一步澄清

### 订单有效期

在回测期间可用的相同有效性概念（使用`valid`来`buy`和`sell`）也可用，并具有相同的含义。因此，对于以下*IB 订单*的`valid`参数翻译如下：

+   `None -> GTC`（Good Til Cancelled）

    因为没有指定有效期，所以理解为订单必须有效直到取消

+   `datetime/date`翻译为`GTD`（Good Til Date）

    传递`datetime.datetime/datetime.date`实例表示订单必须有效直到给定时间点。

+   `timedelta(x)`翻译为`GTD`（这里`timedelta(x) != timedelta()`）

    这被解释为指示订单从`now` + `timedelta(x)`开始有效

+   `float`翻译为`GTD`

    如果该值来自*backtrader*使用的原始*float*日期时间存储，则订单必须有效直到由该*float*指示的日期时间

+   `timedelta() or 0`翻译为`DAY`

    已有一个值（而不是`None`），但是为空，被解释为当前*day*（session）有效的订单

### 通知

标准的`Order`状态将通过方法`notify_order`（如果已重写）通知给*strategy*

+   `Submitted` - 订单已发送到 TWS

+   `Accepted` - 订单已下达

+   `Rejected` - 订单放置失败或在其生命周期内被系统取消

+   `Partial` - 已经部分执行

+   `Completed` - 订单已完全执行

+   `Canceled`（或`Cancelled`）

    这在 IB 下有几个意思：

    +   手动用户取消

    +   服务器/交易所取消了订单

    +   订单有效期已过期

        将应用启发式方法，如果已从 TWS 接收到带有`orderState`指示为`PendingCancel`或`Canceled`的`openOrder`消息，则订单将被标记为`已过期`

+   `已过期` - 请参阅上文的解释

## 参考

### IBStore

#### 类 backtrader.stores.IBStore()

封装一个 ibpy ibConnection 实例的单例类。

参数也可以在使用此存储的类中指定，如`IBData`和`IBBroker`

参数：

+   `host`（默认：`127.0.0.1`）：IB TWS 或 IB Gateway 实际运行的位置。尽管这通常是本地主机，但不应该是

+   `port`（默认值：`7496`）：连接的端口。演示系统使用`7497`

+   `clientId`（默认值：`None`）：要用于连接到 TWS 的客户端 ID。

    `None`：生成 1 到 65535 之间的随机 ID。一个`整数`：将作为要使用的值传递。

+   `notifyall`（默认值：`False`）

    如果为`False`，则只会将`error`消息发送到`Cerebro`和`Strategy`的`notify_store`方法。

    如果为`True`，则会通知从 TWS 接收到的每条消息

+   `_debug`（默认值：`False`）

    打印从 TWS 接收到的所有消息到标准输出

+   `reconnect`（默认值：`3`）

    在第 1 次连接尝试失败后，尝试重新连接的次数。

    将其设置为`-1`值以永远保持重新连接

+   `timeout`（默认值：`3.0`）

    重新连接尝试之间的秒数

+   `timeoffset`（默认值：`True`）

    如果为 True，则将从`reqCurrentTime`（IB 服务器时间）获得的时间用于计算到本地时间的偏移量，并且此偏移量将用于价格通知（例如用于 CASH 市场的 tickPrice 事件）以修改本地计算的时间戳。

    时间偏移将传播到`backtrader`生态系统的其他部分，例如**重新采样**，以使用计算出的偏移量对齐重新采样时间戳。

+   `timerefresh`（默认值：`60.0`）

    秒数：时间偏移量必须刷新的频率

+   `indcash`（默认值：`True`）

    将 IND 代码视为现金进行价格检索

### IBBroker

#### 类`backtrader.brokers.IBBroker(**kwargs)`

用于 Interactive Brokers 的经纪实现。

此类将 Interactive Brokers 的订单/持仓映射到`backtrader`的内部 API。

### 注意

+   实际上不支持`tradeid`，因为利润和损失直接来自 IB。因为（如预期的那样）以 FIFO 方式计算，所以对于`tradeid`，利润和损失并不准确。

+   仓位

如果在操作开始时有资产的持仓或通过其他方式给出的订单改变了持仓，那么在`cerebro`中计算的交易将不反映现实。

为了避免这种情况，该经纪商将不得不进行自己的持仓管理，这也将允许使用多个 ID 进行交易（利润和损失也将在本地计算），但可能被认为是与实时经纪商合作的目的相悖。

### IBData

#### 类`backtrader.feeds.IBData(**kwargs)`

Interactive Brokers 数据源。

支持参数`dataname`中的以下合同规格：

+   TICKER # 股票类型和 SMART 交易所

+   TICKER-STK # 股票和 SMART 交易所

+   TICKER-STK-EXCHANGE # 股票

+   TICKER-STK-EXCHANGE-CURRENCY # 股票

+   TICKER-CFD # 差价合约和 SMART 交易所

+   TICKER-CFD-EXCHANGE # 差价合约

+   TICKER-CDF-EXCHANGE-CURRENCY # 股票

+   TICKER-IND-EXCHANGE # 指数

+   TICKER-IND-EXCHANGE-CURRENCY # 指数

+   TICKER-YYYYMM-EXCHANGE # 期货

+   TICKER-YYYYMM-EXCHANGE-CURRENCY # 期货

+   TICKER-YYYYMM-EXCHANGE-CURRENCY-MULT # 期货

+   TICKER-FUT-EXCHANGE-CURRENCY-YYYYMM-MULT # 期货

+   TICKER-YYYYMM-EXCHANGE-CURRENCY-STRIKE-RIGHT # 期权

+   TICKER-YYYYMM-EXCHANGE-CURRENCY-STRIKE-RIGHT-MULT # 期权

+   TICKER-FOP-EXCHANGE-CURRENCY-YYYYMM-STRIKE-RIGHT # 期权组合

+   TICKER-FOP-EXCHANGE-CURRENCY-YYYYMM-STRIKE-RIGHT-MULT # 期权组合

+   CUR1.CUR2-CASH-IDEALPRO # 外汇

+   TICKER-YYYYMMDD-EXCHANGE-CURRENCY-STRIKE-RIGHT # 期权

+   TICKER-YYYYMMDD-EXCHANGE-CURRENCY-STRIKE-RIGHT-MULT # 期权

+   TICKER-OPT-EXCHANGE-CURRENCY-YYYYMMDD-STRIKE-RIGHT # 期权

+   TICKER-OPT-EXCHANGE-CURRENCY-YYYYMMDD-STRIKE-RIGHT-MULT # 期权

Params：

+   `sectype`（默认：`STK`）

    如果在`dataname`规范中未提供*证券类型*，则应用的默认值

+   `exchange`（默认：`SMART`）

    如果在`dataname`规范中未提供*交易所*，则应用的默认值

+   `currency`（默认：`''`）

    如果在`dataname`规范中未提供*货币*，则应用的默认值

+   `historical`（默认：`False`）

    如果设置为`True`，数据源将在第一次下载数据后停止。

    将使用标准数据源参数`fromdate`和`todate`作为参考。

    如果请求的持续时间大于由 IB 给定的允许的数据时间段/压缩，则数据源将发出多个请求。

+   `what`（默认：`None`）

    如果为`None`，则历史数据请求将使用不同资产类型的默认值：

    +   对于 CASH 资产，为‘BID’

    +   对于任何其他交易

    如果希望使用另一个值，请查看 IB API 文档

+   `rtbar`（默认：`False`）

    如果为`True`，则将使用由 Interactive Brokers 提供的`5 秒实时数据条`作为最小刻度。根据文档，它们对应于实时值（一旦被 IB 整理和筛选）

    如果为`False`，则将使用基于接收到的刻度的`RTVolume`价格。对于`CASH`资产（例如 EUR.JPY），将始终使用`RTVolume`，并从中获取`bid`价格（根据互联网上零散的文献，这是 IB 的行业事实标准）

    即使设置为`True`，如果数据被重新采样/保留到低于秒/5 的时间段/压缩，也不会使用实时数据，因为 IB 不会在该级别以下提供它们

+   `qcheck`（默认：`0.5`）

    如果未收到数据，等待的时间（秒）以便适当地对数据包进行重新采样/重播并将通知传递给链上

+   `backfill_start`（默认：`True`）

    在开始时执行回填。将在单个请求中获取尽可能多的历史数据。

+   `backfill`（默认：`True`）

    在断开连接/重新连接周期后执行回填。间隙持续时间将用于下载尽可能少的数据

+   `backfill_from`（默认：`None`）

    可以传递附加数据源来进行初始回填。一旦数据源用尽，并且如果需要，将从 IB 进行回填。理想情况下，这意味着从已存储的源（如磁盘上的文件）进行回填，但不限于此。

+   `latethrough`（默认：`False`）

    如果数据源被重采样/重播，一些 ticks 可能来得太晚，已经交付的重采样/重播 bar 了。如果设置为 `True`，那些 ticks 将无论如何通过。

    检查重采样文档以了解如何考虑这些 ticks。

    这种情况可能特别发生在 `IBStore` 实例中 `timeoffset` 设置为 `False`，且 TWS 服务器时间与本地计算机时间不同步时

+   `tradename`（默认：`None`）对于某些特定情况很有用，比如 `CFD`，其中价格由一种资产提供，交易发生在另一种资产上。

    +   SPY-STK-SMART-USD -> 标普 500 ETF（将被指定为 `dataname`）

    +   SPY-CFD-SMART-USD -> 对应的 CFD 提供的不是价格跟踪，而是交易资产（指定为 `tradename`）

参数中的默认值是允许类似 `\`TICKER` 这样的东西，其中参数 `sectype`（默认：`STK`）和 `exchange`（默认：`SMART`）被应用。

一些资产如 `AAPL` 需要完整的规范，包括 `currency`（默认：‘’），而其他资产如 `TWTR` 可以直接传递。

+   `AAPL-STK-SMART-USD` 将是 dataname 的完整规范

    或者：`IBData` 作为 `IBData(dataname='AAPL', currency='USD')`，它使用默认值（`STK` 和 `SMART`），并覆盖货币为 `USD`
