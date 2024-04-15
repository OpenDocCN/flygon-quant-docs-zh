# 策略

> 原文：[`www.backtrader.com/docu/strategy/`](https://www.backtrader.com/docu/strategy/)

`Cerebro` 实例是 `backtrader` 的泵心和控制大脑。对于平台用户来说，`Strategy` 是相同的。

*策略* 在方法中表达的生命周期

注意

策略可以通过从模块 `backtrader.errors` 中引发 `StrategySkipError` 异常来在*出生*期间中断

这将避免在回测过程中执行策略。请参阅`Exceptions` 部分

1.  概念：`__init__`

    显然这是在实例化期间调用的：`indicators` 将在此处创建以及其他所需的属性。例如：

    ```py
    def __init__(self):
        self.sma = btind.SimpleMovingAverage(period=15)` 
    ```

1.  出生：`start`

    世界（`cerebro`）告诉策略是时候开始行动了。存在一个默认的空方法。

1.  童年期：`prenext`

    在概念阶段声明的 `indicators` 将对策略需要成熟的时间施加限制：这称为`最小周期`。在 `__init__` 中创建了一个带有 `period=15` 的 *SimpleMovingAverage*。

    只要系统看到的条数少于 `15` 条，将调用 `prenext`（默认实现是空操作）

1.  成年期：`next`

    一旦系统看到了 `15` 条数据，并且 `SimpleMovingAverage` 有足够大的缓冲区开始产生值，策略就足够成熟以实际执行。

    有一个 `nextstart` 方法，它被调用*一次*，标记从 `prenext` 到 `next` 的切换。`nextstart` 的默认实现只是调用 `next`

1.  繁殖：`None`

    好的，策略实际上并没有繁殖。但从某种意义上来说，它们确实如此，因为系统将会多次实例化它们（使用不同的参数）如果*优化*

1.  死亡：`stop`

    系统告诉策略，已经到了整理事务并做好准备的时间。存在一个默认的空方法。

对于大多数情况和常规使用模式，这将是这样的：

```py
class MyStrategy(bt.Strategy):

    def __init__(self):
        self.sma = btind.SimpleMovingAverage(period=15)

    def next(self):
        if self.sma > self.data.close:
            # Do something
            pass

        elif self.sma < self.data.close:
            # Do something else
            pass
```

在这个片段中：

+   在 `__init__` 期间，一个属性被分配了一个指标

+   默认的空 `start` 方法没有被覆盖

+   `prenext` 和 `nexstart` 没有被覆盖

+   在 `next` 中，指标的值与收盘价进行比较以执行某些操作

+   默认的空 `stop` 方法没有被覆盖

策略，就像现实世界中的交易员一样，会在事件发生时收到通知。实际上，每个`next`周期在回测过程中都会发生一次。策略将：

+   通过 `notify_order(order)` 通知订单状态变化

+   通过 `notify_trade(trade)` 通知任何开仓/更新/关闭交易

+   通过 `notify_cashvalue(cash, value)` 通知经纪人当前现金和投资组合

+   通过 `notify_fund(cash, value, fundvalue, shares)` 通知经纪人当前现金和投资组合以及基金价值和份额的交易跟踪

+   通过 `notify_store(msg, *args, **kwargs)` 通知事件（具体实现）

    请参阅 Cerebro 以了解*store*通知的说明。即使它们也已经传递给了一个`cerebro`实例（通过覆盖`notify_store`方法或通过*回调*传递），这些通知也将传递给策略

而且*策略*也像交易员一样在`next`方法期间有机会在市场中操作，以尝试获得利润

+   `buy`方法，用于做多或减少/平仓空头头寸

+   `sell`方法，用于做空或减少/平仓多头头寸

+   `close`方法，显然关闭现有头寸

+   `cancel`方法，用于取消尚未执行的订单

## 如何购买/出售/平仓

`Buy`和`Sell`方法生成订单。调用时，它们返回一个`Order`（或子类）实例，可用作引用。此订单具有唯一的`ref`标识符，可用于比较

注意

特定经纪人实现的`Order`子类可能携带经纪人提供的其他*唯一标识符*。

要创建订单，请使用以下参数：

+   `data`（默认值：`None`）

    必须为其创建订单的数据。如果是`None`，那么系统中的第一个数据，`self.datas[0] or self.data0`（又名`self.data`）将被使用

+   `size`（默认值：`None`）

    要使用的数据单位（正数）的大小，用于订单

    如果是`None`，则将使用通过`getsizer`检索的`sizer`实例来确定大小。

+   `price`（默认值：`None`）

    要使用的价格（现实经纪人可能对实际格式施加限制，如果不符合最低跳动大小要求）

    对于`Market`和`Close`订单，`None`是有效的（市场确定价格）

    对于`Limit`、`Stop`和`StopLimit`订单，此值确定触发点（在`Limit`的情况下，触发显然是订单应该匹配的价格）

+   `plimit`（默认值：`None`）

    仅适用于`StopLimit`订单。这是触发*Limit*订单的价格，一旦*Stop*被触发（使用了`price`）

+   `exectype`（默认值：`None`）

    可能的值：

    +   `Order.Market`或`None`。市价单将以下一个可用价格执行。在回测中，它将是下一个柱的开盘价

    +   `Order.Limit`。只能以给定的`price`或更好的价格执行的订单

    +   `Order.Stop`。当价格达到`price`时触发的订单，并像`Order.Market`订单一样执行

    +   `Order.StopLimit`。当价格达到`price`时触发的订单，并作为隐式*Limit*订单以由`pricelimit`给出的价格执行

+   `valid`（默认值：`None`）

    可能的值：

    +   `None`：这将生成一个不会过期的订单（又称*Good til cancel*），并保持在市场上直到匹配或取消。实际上，经纪人倾向于强加一个时间限制，但通常是在很远的时间内，可以考虑它不会过期

    +   `datetime.datetime`或`datetime.date`实例：将使用该日期生成订单，有效期直到给定的日期时间（又称*截止日期*）

    +   `Order.DAY` 或 `0` 或 `timedelta()`: 生成到 *Session End*（又名 *day* 订单）为止的一天有效订单

    +   `numeric value`: 假设这是对应于 `matplotlib` 编码中的日期时间的值（`backtrader` 使用的日期时间编码），将用于生成直到该时间有效的订单（*good til date*）

+   `tradeid`（默认值： `0`）

    这是 `backtrader` 应用的内部值，用于跟踪同一资产上的重叠交易。当通知订单状态变化时，此 `tradeid` 将发送回 *strategy*。

+   `**kwargs`: 其他经纪人实现可能支持额外的参数。 `backtrader` 将这些 *kwargs* 传递给创建的订单对象

    例如: 如果 `backtrader` 直接支持的 4 种订单执行类型不够用，例如 *Interactive Brokers* 的情况下可以将以下内容作为 *kwargs* 传递:

    ```py
    orderType='LIT', lmtPrice=10.0, auxPrice=9.8` 
    ```

    这将覆盖 `backtrader` 创建的设置，并生成一个 *touched* 价格为 9.8， *limit* 价格为 10.0 的 `LIMIT IF TOUCHED` 订单。

## 信息位:

+   策略具有与主数据（`datas[0]`）长度相等的 *length*，当然可以用 `len(self)` 获得

    如果正在重播数据或传递了实时数据，并且新的数据到达相同时间点（长度）的话，可以调用 `next` 而不更改 *长度*

## 成员属性:

+   `env`: 此策略所在的 cerebro 实体

+   `datas`: 已传递给 cerebro 的数据源数组

    +   `data/data0` 是 datas[0] 的别名

    +   `dataX` 是 datas[X] 的别名

    *数据源* 也可以通过名称访问（参见参考资料），如果已为其分配了名称

+   `dnames`: 通过名称访问数据源的另一种方式（可以使用 `[name]` 或 `.name` 符号）

    例如如果对数据进行重采样如下:

    ```py
    ...
    data0 = bt.feeds.YahooFinanceData(datname='YHOO', fromdate=..., name='days')
    cerebro.adddata(data0)
    cerebro.resampledata(data0, timeframe=bt.TimeFrame.Weeks, name='weeks')
    ...` 
    ```

    策略中可以像这样为每个创建指标:

    ```py
    ...
    smadays = bt.ind.SMA(self.dnames.days, period=30)  # or self.dnames['days']
    smaweeks = bt.ind.SMA(self.dnames.weeks, period=10)  # or self.dnames['weeks']
    ...` 
    ```

+   `broker`: 引用与此策略相关联的经纪人（从 cerebro 接收）

+   `stats`: 保存由 cerebro 为该策略创建的观察者的列表/类似命名元组序列

+   `analyzers`: 保存由 cerebro 为该策略创建的分析器的列表/类似命名元组序列

+   `position`: 实际上是一个属性，用于给出 `data0` 的当前持仓。

    有用于检索所有仓位的方法（参见参考资料）

## 成员属性（用于统计/观察者/分析器）:

+   `_orderspending`: 将在调用 `next` 之前通知给策略的订单列表

+   `_tradespending`: 将在调用 `next` 之前通知给策略的交易列表

+   `_orders`: 已经通知的订单列表。订单可以以不同的状态和不同的执行位多次出现在列表中。此列表用于保留历史记录。

+   `_trades`: 已通知的订单列表。交易可以像订单一样多次出现在列表中。

注意

请记住，`prenext`、`nextstart` 和 `next` 可能会针对同一时间点被多次调用（当使用日线时，每次 ticks 更新价格时）

## 参考：Strategy

#### 类 backtrader.Strategy(*args, **kwargs)

用于用户定义策略的基类。

#### next()

此方法将在满足所有数据/指标的最小期限后，为所有剩余数据点调用。

#### nextstart()

此方法将被调用一次，确切地在所有数据/指标的最小期限已满足时。默认行为是调用 next

#### prenext()

此方法将在策略开始执行之前，所有数据/指标的最小期限已满足时调用

#### start()

在回测即将开始之前调用。

#### stop()

在回测即将停止之前调用

#### notify_order(order)

每当有变化时接收订单

#### notify_trade(trade)

每当有变化时接收交易

#### notify_cashvalue(cash, value)

接收策略经纪人的当前基金价值、价值状态

#### notify_fund(cash, value, fundvalue, shares)

接收当前现金、价值、基金价值和基金份额

#### notify_store(msg, *args, **kwargs)

接收来自存储提供程序的通知

#### buy(data=None, size=None, price=None, plimit=None, exectype=None, valid=None, tradeid=0, oco=None, trailamount=None, trailpercent=None, parent=None, transmit=True, **kwargs)

创建买入（做多）订单并发送到经纪人

+   `data`（默认为 `None`）

    为哪个数据创建订单。如果为 `None`，则系统中的第一个数据，`self.datas[0] 或 self.data0`（又名 `self.data`）将被使用

+   `size`（默认为 `None`）

    要使用的数据单位（正数）来用于订单。

    如果为 `None`，则将使用通过 `getsizer` 获取的 `sizer` 实例来确定大小。

+   `price`（默认为 `None`）

    用于价格（实时经纪人可能对实际格式施加限制，如果不符合最小 tick 大小要求）

    对于 `Market` 和 `Close` 订单，`None` 是有效的（市场决定价格）

    对于 `Limit`、`Stop` 和 `StopLimit` 订单，此值确定触发点（在 `Limit` 的情况下，触发显然是订单应匹配的价格）

+   `plimit`（默认为 `None`）

    仅适用于 `StopLimit` 订单。这是触发隐含 *Limit* 订单的价格，一旦 *Stop* 被触发（`price` 已被使用）

+   `trailamount`（默认为 `None`）

    如果订单类型是 `StopTrail` 或 `StopTrailLimit`，这是一个绝对数值，用于确定距离价格的距离（对于卖单是下方，对于买单是上方）以保持跟踪止损

+   `trailpercent`（默认为 `None`）

    如果订单类型是 `StopTrail` 或 `StopTrailLimit`，这是一个百分比数值，用于确定距离价格的距离（对于卖单是下方，对于买单是上方）以保持跟踪止损（如果还指定了 `trailamount`，它将被使用）

+   `exectype` (默认值: `None`)

    可能的值:

    +   `Order.Market` 或 `None`. 一个市价订单将以下一个可用价格执行。 在回测中，它将是下一个 bar 的开盘价

    +   `Order.Limit`. 一个只能以给定的`price`或更好的价格执行的订单

    +   `Order.Stop`. 一个在`price`处触发并像`Order.Market`订单一样执行的订单

    +   `Order.StopLimit`. 一个在`price`处触发并以隐式*Limit*订单形式执行的订单，价格由`pricelimit`给出

    +   `Order.Close`. 一个只能在会话结束时（通常在收盘竞价时）执行的订单

    +   `Order.StopTrail`. 一个在`price`减去`trailamount`（或`trailpercent`）处触发并在价格远离停止时更新的订单

    +   `Order.StopTrailLimit`. 一个在`price`减去`trailamount`（或`trailpercent`）处触发并在价格远离停止时更新的订单

+   `valid` (默认值: `None`)

    可能的值:

    +   `None`: 这将生成一个不会过期的订单（又称*Good till cancel*），并保持在市场上直到匹配或取消。 实际上，经纪人往往会强加一个时间限制，但这通常是如此遥远的时间，以至于可以视为不会过期。

    +   `datetime.datetime` 或 `datetime.date` 实例：日期将用于生成一个直到给定日期时间（又称*good till date*）的有效订单

    +   `Order.DAY` 或 `0` 或 `timedelta()`: 一个有效期至*会话结束*（又称*day*订单）的订单将被生成

    +   `numeric value`: 这被假设为与`matplotlib`编码中的日期时间对应的值（`backtrader`使用的编码方式），并将用于生成一个有效订单，直到该时间（*good till date*）

+   `tradeid` (默认值: `0`)

    这是`backtrader`应用的内部值，用于跟踪同一资产上的重叠交易。 当通知订单状态变化时，此`tradeid`将发送回*strategy*。

+   `oco` (默认值: `None`)

    另一个`order`实例。 这个订单将成为 OCO（Order Cancel Others）组的一部分。 一个订单的执行会立即取消同一组中的所有其他订单

+   `parent` (默认值: `None`)

    控制一组订单的关系，例如一个由高限价卖出和低止损卖出所包围的买入订单。高/低侧订单保持不活跃，直到父订单被执行（它们变为活动状态）或取消/过期（子订单也取消）bracket 订单具有相同的大小

+   `transmit` (默认值: `True`)

    指示订单是否必须**发送**，即不仅在经纪人处放置，而且发出。 例如，这用于控制 bracket 订单，其中一个禁用父订单和第 1 组子订单的传输，并激活最后一组子订单的传输，这会触发所有 bracket 订单的完全放置。

+   `**kwargs`：其他经纪人实现可能支持额外的参数。`backtrader`将*kwargs*传递给创建的订单对象

    示例：如果直接由`backtrader`支持的 4 种订单执行类型不够，例如*Interactive Brokers*的情况下，可以将以下内容作为*kwargs*传递：

    ```py
    orderType='LIT', lmtPrice=10.0, auxPrice=9.8` 
    ```

    这将覆盖`backtrader`创建的设置，并生成一个*touched*价格为 9.8，*limit*价格为 10.0 的`LIMIT IF TOUCHED`订单。

+   **返回**

    +   提交的订单

#### sell(data=None, size=None, price=None, plimit=None, exectype=None, valid=None, tradeid=0, oco=None, trailamount=None, trailpercent=None, parent=None, transmit=True, **kwargs)

创建一个卖出（做空）订单并将其发送给经纪人

查看`buy`的文档以了解参数的解释

返回：提交的订单

#### close(data=None, size=None, **kwargs)

抵消长/短持仓

查看`buy`的文档以了解参数的解释

注意

+   `size`: 如果调用者没有提供，则自动从现有位置计算（默认值：`None`）

返回：提交的订单

#### cancel(order)

在经纪人处取消订单

#### buy_bracket(data=None, size=None, price=None, plimit=None, exectype=2, valid=None, tradeid=0, trailamount=None, trailpercent=None, oargs={}, stopprice=None, stopexec=3, stopargs={}, limitprice=None, limitexec=2, limitargs={}, **kwargs)

创建一个支架订单组（低端 - 买单 - 高端）。默认行为如下：

+   发出**buy**带有执行`Limit`的订单

+   发出一个*low side*支架**sell**带有执行`Stop`的订单

+   发出一个*high side*支架**sell**带有执行`Limit`的订单。

有关不同参数的详细信息，请参见下文

+   `data`（默认值：`None`）

    要为哪些数据创建订单。如果为`None`，则将使用系统中的第一个数据，`self.datas[0]或 self.data0`（又名`self.data`）

+   `size`（默认值：`None`）

    要使用的大小（正）以用于订单的数据单位。

    如果为`None`，则将使用通过`getsizer`检索到的`sizer`实例确定大小。

    注意

    同样的大小适用于支架的所有 3 个订单

+   `price`（默认值：`None`）

    使用价格（实时经纪人可能会对实际格式施加限制，如果不符合最小跳动大小要求）

    `None`适用于`Market`和`Close`订单（市场确定价格）

    对于`Limit`、`Stop`和`StopLimit`订单，此值确定触发点（在`Limit`情况下，触发显然是订单应匹配的价格）

+   `plimit`（默认值：`None`）

    仅适用于`StopLimit`订单。这是设置隐含*Limit*订单的价格，一旦*Stop*已被触发（已使用`price`）

+   `trailamount`（默认值：`None`）

    如果订单类型为 StopTrail 或 StopTrailLimit，则此值是一个绝对金额，用于确定保持跟踪止损的价格距离（对于卖出订单为下方，对于买入订单为上方）

+   `trailpercent`（默认值：`None`）

    如果订单类型为 StopTrail 或 StopTrailLimit，则此处为一个百分比值，确定保持跟踪止损的距离（对于卖出订单下方，对于买入订单上方）（如果还指定了`trailamount`，则将使用它）

+   `exectype`（默认值：`bt.Order.Limit`）

    可能的取值：（查看方法`buy`的文档）

+   `valid`（默认值：`None`）

    可能的取值：（查看方法`buy`的文档）

+   `tradeid`（默认值：`0`）

    可能的取值：（查看方法`buy`的文档）

+   `oargs`（默认值：`{}`）

    将特定的关键字参数（在`dict`中）传递给主侧订单。默认的`**kwargs`中的参数将在此基础上应用。

+   `**kwargs`：其他经纪人实现可能支持额外的参数。`backtrader`将把*kwargs*传递给创建的订单对象

    可能的取值：（查看方法`buy`的文档）

    注意

    这个`kwargs`将应用于一个括号的 3 个订单。有关低侧和高侧订单的特定关键字参数，请参见下文

+   `stopprice`（默认值：`None`）

    低侧停止订单的具体价格

+   `stopexec`（默认值：`bt.Order.Stop`）

    低侧订单的具体执行类型

+   `stopargs`（默认值：`{}`）

    将特定的关键字参数（在`dict`中）传递给低侧订单。默认的`**kwargs`中的参数将在此基础上应用。

+   `limitprice`（默认值：`None`）

    高侧停止订单的具体价格

+   `stopexec`（默认值：`bt.Order.Limit`）

    高侧订单的具体执行类型

+   `limitargs`（默认值：`{}`）

    将特定的关键字参数（在`dict`中）传递给高侧订单。默认的`**kwargs`中的参数将在此基础上应用。

可以通过以下方式抑制高/低侧订单：

+   `limitexec=None`以抑制*高侧*

+   `stopexec=None`以抑制*低侧*

+   **返回**

    +   包含 3 个订单的列表[订单，停止侧，限价侧]

    +   如果高/低订单已被抑制，则返回值仍将包含 3 个订单，但被抑制的订单将值为`None`

#### sell_bracket(data=None, size=None, price=None, plimit=None, exectype=2, valid=None, tradeid=0, trailamount=None, trailpercent=None, oargs={}, stopprice=None, stopexec=3, stopargs={}, limitprice=None, limitexec=2, limitargs={}, **kwargs)

创建一个括号订单组（低侧 - 买入订单 - 高侧）。默认行为如下：

+   发出一个**卖出**订单，执行`限价`

+   发出一个*高侧*括号**买入**订单，执行`停止`

+   发出一个*低侧*括号**买入**订单，执行`限价`。

查看`bracket_buy`以了解参数的含义

可以通过以下方式抑制高/低侧订单：

+   `stopexec=None`以抑制*高侧*

+   `limitexec=None`以抑制*低侧*

+   **返回**

    +   包含 3 个订单的列表[订单，停止侧，限价侧]

    +   如果高/低订单已被抑制，则返回值仍将包含 3 个订单，但被抑制的订单将值为`None`

#### order_target_size(data=None, target=0, **kwargs)

下达订单以将头寸重新平衡为`target`的最终大小

当前`position`大小被视为实现`target`的起始点

+   如果`target` > `pos.size` -> 买入 `target - pos.size`

+   如果`target` < `pos.size` -> 卖出 `pos.size - target`

它返回以下之一：

+   生成的订单

或

+   如果没有下达订单则为`None`（`target == position.size`)

#### order_target_value(data=None, target=0.0, price=None, **kwargs)

下达订单以将头寸重新平衡为`target`的最终价值

当前`value`被视为实现`target`的起始点

+   如果没有`target`，则在数据上关闭头寸

+   如果`target` > `value`则在数据上买入

+   如果`target` < `value`则在数据上卖出

它返回以下之一：

+   生成的订单

或

+   如果没有下达订单则为`None`

#### order_target_percent(data=None, target=0.0, **kwargs)

下达订单以将头寸重新平衡为当前投资组合`value`的`target`百分比的最终价值

`target`以小数表示：`0.05` -> `5%`

它使用`order_target_value`来执行订单。

*示例*

+   `target=0.05`且投资组合价值为`100`

+   要达到的`value`为`0.05 * 100 = 5`

+   `5`作为`target`值传递给`order_target_value`

当前`value`被视为实现`target`的起始点

使用`position.size`来确定头寸是`多头`/`空头`

+   如果`target` > `value`

    +   如果`pos.size >= 0`则买入（增加多头头寸）

    +   如果`pos.size < 0`则卖出（增加空头头寸）

+   如果`target` < `value`

    +   如果`pos.size >= 0`则卖出（减少多头头寸）

    +   如果`pos.size < 0`则买入（减少空头头寸）

它返回以下之一：

+   生成的订单

或

+   如果没有下达订单，则为`None`（`target == position.size`）

#### getsizer()

返回正在使用的 sizer，如果使用自动��注计算

也可用作`sizer`

#### setsizer(sizer)

替换默认（固定赌注）sizer

#### getsizing(data=None, isbuy=True)

返回由 sizer 实例计算的当前情况下的赌注

#### getposition(data=None, broker=None)

返回给定经纪人中给定数据的当前头寸。

如果两者都为 None，则将使用主要数据和默认经纪人

也可用的属性`position`

#### getpositionbyname(name=None, broker=None)

返回给定经纪人中给定名称的当前头寸。

如果两者都为 None，则将使用主要数据和默认经纪人

也可用的属性`positionbyname`

#### getpositionsbyname(broker=None)

直接从经纪人那里返回当前名称的头寸

如果给定的`broker`为 None，则将使用默认经纪人

也可用的属性`positionsbyname`

#### getdatanames()

返回现有数据名称的列表

#### getdatabyname(name)

通过名称使用环境（cerebro）返回给定数据

#### `add_timer(when, offset=datetime.timedelta(0), repeat=datetime.timedelta(0), weekdays=[], weekcarry=False, monthdays=[], monthcarry=True, allow=None, tzdata=None, cheat=False, *args, **kwargs)`

注意

可以在 `__init__` 或 `start` 期间调用

安排一个计时器来调用一个或多个策略的指定回调函数或 `notify_timer`。

+   **参数**

    **when** (*-*) – 可以是

    +   `datetime.time` 实例（参见下面的 `tzdata`）

    +   `bt.timer.SESSION_START` 用于引用会话开始

    +   `bt.timer.SESSION_END` 用于引用会话结束

    +   `offset` 必须是一个 `datetime.timedelta` 实例

    用于偏移值 `when`。与 `SESSION_START` 和 `SESSION_END` 结合使用具有有意义的用途，以指示会话开始后 `15 分钟` 调用计时器的情况。

    +   `repeat` 必须是一个 `datetime.timedelta` 实例

        指示在第一次调用后，进一步的调用是否会在同一会话中按计划的 `repeat` 间隔进行安排

        一旦计时器超过会话结束，它将被重置为 `when` 的原始值

    +   `weekdays`：一个**排序的**可迭代对象，其中的整数表示可以实际调用计时器的日期（ISO 代码，星期一为 1，星期日为 7）

        如果未指定，则计时器将在所有日期都处于活动状态

    +   `weekcarry`（默认：`False`）。如果为 `True` 并且没有看到周几（例如：交易假期），则计时器将在下一天执行（即使是在新的一周）

    +   `monthdays`：一个**排序的**可迭代对象，其中的整数表示要执行计时器的月份中的哪些日期。例如，每月的第 15 天

        如果未指定，则计时器将在所有日期都处于活动状态

    +   `monthcarry`（默认：`True`）。如果未看到该天（周末，交易假期），则计时器将在下一个可用日期执行。

    +   `allow`（默认：`None`）。一个回调函数，接收一个 `datetime.date` 实例，如果日期允许计时器，则返回`True`，否则返回`False`

    +   `tzdata` 可以是 `None`（默认值），一个 `pytz` 实例或一个数据源实例。

        `None`：`when`按照面值解释（即使它不是 UTC 时间也会被处理为 UTC 时间）

        `pytz` 实例：`when` 将被解释为由时区实例指定的本地时间。

        `data feed` 实例：`when` 将被解释为数据源实例参数 `tz` 指定的本地时间。

        注意

        如果 `when` 是 `SESSION_START` 或 `SESSION_END`，并且 `tzdata` 是 `None`，则系统中的第一个数据源（即 `self.data0`）将被用作查找会话时间的参考。

    +   `cheat`（默认为 `False`）如果为 `True`，则在经纪人有机会评估订单之前将调用计时器。这打开了在会话开始之前根据开盘价发出订单的可能性

    +   `*args`：任何额外的参数将被传递给 `notify_timer`

    +   `**kwargs`：任何额外的 kwargs 将被传递给 `notify_timer`

返回值：

+   创建的计时器

#### notify_timer(timer, when, *args, **kwargs)

接收定时器通知，其中`timer`是由`add_timer`返回的定时器，`when`是调用时间。`args`和`kwargs`是传递给`add_timer`的任何额外参数。

实际的`when`时间可能会晚一些，但系统可能在之前无法调用定时器。这个值是定时器值，而不是系统时间。
