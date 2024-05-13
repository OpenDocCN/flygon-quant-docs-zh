# 经纪人 - 订单管理类

> 原文：[`gbeced.github.io/pyalgotrade/docs/v0.20/html/broker.html`](https://gbeced.github.io/pyalgotrade/docs/v0.20/html/broker.html)

## 基础模块和类

*类* `pyalgotrade.broker.``Order`（*type_*，*action*，*instrument*，*quantity*，*instrumentTraits*）

基类：`object`

订单的基类。

| 参数： |
| --- |

+   **type**（`Order.Type`）– 订单类型

+   **action**（`Order.Action`）– 订单操作。

+   **instrument** (*string.*) – 工具标识符。

+   **quantity**（*int/float.*）– 订单数量。

|

注

这是一个基类，不应直接使用。

有效的**type**参数值为：

> +   Order.Type.MARKET
> +   
> +   Order.Type.LIMIT
> +   
> +   Order.Type.STOP
> +   
> +   Order.Type.STOP_LIMIT

有效的**action**参数值为：

> +   Order.Action.BUY
> +   
> +   Order.Action.BUY_TO_COVER
> +   
> +   Order.Action.SELL
> +   
> +   Order.Action.SELL_SHORT

`getId`()

返回订单 ID。

注

如果订单未提交，则此值将为 None。

`getType`()

返回订单类型。有效的订单类型为：

+   Order.Type.MARKET

+   Order.Type.LIMIT

+   Order.Type.STOP

+   Order.Type.STOP_LIMIT

`getSubmitDateTime`()

返回订单提交的日期时间。

`getAction`()

返回订单操作。有效的订单操作包括：

+   Order.Action.BUY

+   Order.Action.BUY_TO_COVER

+   Order.Action.SELL

+   Order.Action.SELL_SHORT

`getState`()

返回订单状态。有效的订单状态为：

+   Order.State.INITIAL（初始状态）。

+   Order.State.SUBMITTED

+   Order.State.ACCEPTED

+   Order.State.CANCELED

+   Order.State.PARTIALLY_FILLED

+   Order.State.FILLED

`isActive`()

如果订单处于活动状态，则返回 True。

`isInitial`()

如果订单状态为 Order.State.INITIAL，则返回 True。

`isSubmitted`()

如果订单状态为 Order.State.SUBMITTED，则返回 True。

`isAccepted`()

如果订单状态为 Order.State.ACCEPTED，则返回 True。

`isCanceled`()

如果订单状态为 Order.State.CANCELED，则返回 True。

`isPartiallyFilled`()

如果订单状态为 Order.State.PARTIALLY_FILLED，则返回 True。

`isFilled`()

如果订单状态为 Order.State.FILLED，则返回 True。

`getInstrument`()

返回工具标识符。

`getQuantity`()

返回数量。

`getFilled`()

返回已执行的股票数量。

`getRemaining`()

返回仍未完成的股票数量。

`getAvgFillPrice`()

返回已执行的股票的平均价格，如果没有填充，则返回 None。

`getGoodTillCanceled`()

如果订单有效直到取消，则返回 True。

`setGoodTillCanceled`(*goodTillCanceled*)

设置订单是否应保持有效直到取消。如果在会话关闭时订单尚未填充，则未填充的订单将自动取消，如果它们未设置为有效直到取消

| 参数： | **goodTillCanceled** (*boolean.*) – 如果订单应保持有效直到取消，则为 True。 |
| --- | --- |

注

一旦订单提交，就无法更改。

`getAllOrNone`()

如果订单应完全执行，则返回 True，否则取消。

`setAllOrNone`(*allOrNone*)

设置此订单的全部或无属性。

| 参数： | **allOrNone** (*boolean.*) – 如果订单应完全填充，则为 True。 |
| --- | --- |

注意

一旦提交订单，就无法更改。

`getExecutionInfo`()

返回此订单的最后执行信息，如果到目前为止没有填充任何内容，则返回 None。每次订单或其部分被填充时，这将不同。

| 返回类型： | `OrderExecutionInfo`. |
| --- | --- |

*class* `pyalgotrade.broker.``MarketOrder`(*action*, *instrument*, *quantity*, *onClose*, *instrumentTraits*)

基类：`pyalgotrade.broker.Order`

市价订单的基类。

注意

这是一个基类，不应直接使用。

`getFillOnClose`()

如果订单应尽可能接近收盘价填充（市价收盘订单），则返回 True。

*class* `pyalgotrade.broker.``LimitOrder`(*action*, *instrument*, *limitPrice*, *quantity*, *instrumentTraits*)

基类：`pyalgotrade.broker.Order`

限价单的基类。

注意

这是一个基类，不应直接使用。

`getLimitPrice`()

返回限价。

*class* `pyalgotrade.broker.``StopOrder`(*action*, *instrument*, *stopPrice*, *quantity*, *instrumentTraits*)

基类：`pyalgotrade.broker.Order`

停止订单的基类。

注意

这是一个基类，不应直接使用。

`getStopPrice`()

返回止损价格。

*class* `pyalgotrade.broker.``StopLimitOrder`(*action*, *instrument*, *stopPrice*, *limitPrice*, *quantity*, *instrumentTraits*)

基类：`pyalgotrade.broker.Order`

停止限价订单的基类。

注意

这是一个基类，不应直接使用。

`getStopPrice`()

返回止损价格。

`getLimitPrice`()

返回限价。

*class* `pyalgotrade.broker.``OrderExecutionInfo`(*price*, *quantity*, *commission*, *dateTime*)

基类：`object`

订单的执行信息。

`getPrice`()

返回填充价格。

`getQuantity`()

返回数量。

`getCommission`()

返回应用的佣金。

`getDateTime`()

返回订单执行的`datatime.datetime`。

*class* `pyalgotrade.broker.``Broker`

基类：`pyalgotrade.observer.Subject`

经纪人的基类。

注意

这是一个基类，不应直接使用。

`getCash`(*includeShort=True*)

返回可用现金。

| 参数： | **includeShort** (*boolean.*) – 包括空头头寸的现金。 |
| --- | --- |

`getShares`(*instrument*)

返回工具的股票数量。

`getPositions`()

返回一个将工具映射到股票的字典。

`getActiveOrders`(*instrument=None*)

返回仍然活跃的订单序列。

| 参数： | **instrument** (*string.*) – 一个可选的工具标识符，用于仅返回给定工具的活动订单。 |
| --- | --- |

`submitOrder`(*order*)

提交一个订单。

| 参数： | **order** (`Order`.) – 要提交的订单。 |
| --- | --- |

注意

+   调用此后，订单处于已提交状态，并且不会为此转换触发事件。

+   对同一订单调用两次将引发异常。

`createMarketOrder`(*action*, *instrument*, *quantity*, *onClose=False*)

创建一个市价订单。市价订单是以最佳可用价格买入或卖出股票的订单。通常，这种类型的订单将立即执行。但是，市价订单的执行价格不受保证。

| 参数： |
| --- |

+   **action** (*Order.Action.BUY，或 Order.Action.BUY_TO_COVER，或 Order.Action.SELL 或 Order.Action.SELL_SHORT.*) – 订单操作。

+   **instrument** (*字符串.*) – 工具标识符。

+   **quantity** (*int/float.*) – 订单数量。

+   **onClose** (*布尔值.*) – 如果订单应尽可能接近收盘价成交（市价收盘单）。默认为 False。

|

| 返回类型： | 一个`MarketOrder`子类。 |
| --- | --- |

`createLimitOrder`(*action*, *instrument*, *limitPrice*, *quantity*)

创建一个限价订单。限价订单是以特定价格或更好的价格买入或卖出股票的订单。买入限价订单只能以限价或更低价格执行，卖出限价订单只能以限价或更高价格执行。

| 参数： |
| --- |

+   **action** (*Order.Action.BUY，或 Order.Action.BUY_TO_COVER，或 Order.Action.SELL 或 Order.Action.SELL_SHORT.*) – 订单操作。

+   **instrument** (*字符串.*) – 工具标识符。

+   **limitPrice** (*float*) – 订单价格。

+   **quantity** (*int/float.*) – 订单数量。

|

| 返回类型： | 一个`LimitOrder`子类。 |
| --- | --- |

`createStopOrder`(*action*, *instrument*, *stopPrice*, *quantity*)

创建一个止损订单。止损订单，也称为止损单，是一种在股价达到指定价格（止损价格）时买入或卖出股票的订单。当止损价格达到时，止损订单变成市价订单。买入止损订单以高于当前市场价格的止损价格输入。投资者通常使用买入止损订单来限制亏损或保护已卖空的股票的利润。卖出止损订单以低于当前市场价格的止损价格输入。投资者通常使用卖出止损订单来限制亏损或保护他们拥有的股票的利润。

| 参数： |
| --- |

+   **action** (*Order.Action.BUY，或 Order.Action.BUY_TO_COVER，或 Order.Action.SELL 或 Order.Action.SELL_SHORT.*) – 订单操作。

+   **instrument** (*字符串.*) – 工具标识符。

+   **stopPrice** (*float*) – 触发价格。

+   **quantity** (*int/float.*) – 订单数量。

|

| 返回类型： | 一个`StopOrder`子类。 |
| --- | --- |

`createStopLimitOrder`(*action*, *instrument*, *stopPrice*, *limitPrice*, *quantity*)

创建一个停止限价订单。 停止限价订单是一种买入或卖出股票的订单，结合了停止订单和限价订单的特点。 一旦触发了停止价格，停止限价订单就变成了一个限价订单，以指定的价格（或更好）执行。 停止限价订单的好处在于投资者可以控制订单执行的价格。

| 参数： |
| --- |

+   **action** (*Order.Action.BUY, or Order.Action.BUY_TO_COVER, or Order.Action.SELL or Order.Action.SELL_SHORT.*) – 订单动作。

+   **instrument** (*string.*) – 工具标识符。

+   **stopPrice** (*float*) – 触发价格。

+   **limitPrice** (*float*) – 限价订单的价格。

+   **quantity** (*int/float.*) – 订单数量。

|

| 返回类型： | 一个 `StopLimitOrder` 的子类。 |
| --- | --- |

`cancelOrder`(*order*)

请求取消订单。 如果订单已成交，则会引发异常。

| 参数： | **order** (`Order`.) – 要取消的订单。 |  ## 回测模块和类

*类* `pyalgotrade.broker.backtesting.``Commission`

基类：`object`

实现不同佣金方案的基类。

注意

这是一个基类，不应直接使用。

`calculate`(*order*, *price*, *quantity*)

计算订单执行的佣金。

| 参数： |
| --- |

+   **order** (`pyalgotrade.broker.Order`.) – 执行的订单。

+   **price** (*float.*) – 每股价格。

+   **quantity** (*float.*) – 订单数量。

|

| 返回类型： | float。 |
| --- | --- |

*类* `pyalgotrade.broker.backtesting.``NoCommission`

基类：`pyalgotrade.broker.backtesting.Commission`

一个 `Commission` 类，始终返回 0。

*类* `pyalgotrade.broker.backtesting.``FixedPerTrade`(*amount*)

基类：`pyalgotrade.broker.backtesting.Commission`

一个 `Commission` 类，针对整个交易收取固定金额的手续费。

| 参数： | **amount** (*float.*) – 一笔订单的手续费。 |
| --- | --- |

*类* `pyalgotrade.broker.backtesting.``TradePercentage`(*percentage*)

基类：`pyalgotrade.broker.backtesting.Commission`

一个 `Commission` 类，按整个交易的百分比收取手续费。

| 参数: | **percentage** (*float.*) – 要收取的百分比。0.01 表示 1%，以此类推。必须小于 1。 |
| --- | --- |

*class* `pyalgotrade.broker.backtesting.``Broker`(*cash*, *barFeed*, *commission=None*)

基类: `pyalgotrade.broker.Broker`

回测经纪人。

| 参数: |
| --- |

+   **cash** (*int/float.*) – 初始现金金额。

+   **barFeed** (`pyalgotrade.barfeed.BarFeed`) – 将提供条形的 bar feed。

+   **commission** (`Commission`) – 负责计算订单佣金的对象。

|

`getCommission`()

返回用于计算订单佣金的策略。

| 返回类型: | `Commission`. |
| --- | --- |

`getEquity`()

返回组合价值（现金 + 股票 * 价格）。

`getFillStrategy`()

返回当前设置的 `pyalgotrade.broker.fillstrategy.FillStrategy`。

`setCommission`(*commission*)

设置用于计算订单佣金的策略。

| 参数: | **commission** (`Commission`.) – 负责计算订单佣金的对象。 |
| --- | --- |

`setFillStrategy`(*strategy*)

设置要使用的 `pyalgotrade.broker.fillstrategy.FillStrategy`。

`setShares`(*instrument*, *quantity*, *price*)

在策略开始执行之前设置现有股份。

| 参数: |
| --- |

+   **instrument** – 仪器标识符。

+   **quantity** – 给定仪器的股票数量。

+   **price** – 每股的价格。

|

*class* `pyalgotrade.broker.slippage.``SlippageModel`

基类: `object`

滑点模型的基类。

注意

这是一个基类，不应直接使用。

`calculatePrice`(*order*, *price*, *quantity*, *bar*, *volumeUsed*)

返回订单的每股滑点价格。

| 参数: |
| --- |

+   **order** (`pyalgotrade.broker.Order`.) – 被填充的订单。

+   **price** (*float.*) – 滑点之前每股的价格。

+   **quantity** (*float.*) – 此次订单将填充的股票数量。

+   **bar** (`pyalgotrade.bar.Bar`.) – 当前的 bar。

+   **volumeUsed** (*float.*) – 到目前为止从当前 bar 获取的体积大小。

|

| 返回类型: | float. |
| --- | --- |

*class* `pyalgotrade.broker.slippage.``NoSlippage`

基类: `pyalgotrade.broker.slippage.SlippageModel`

无滑点模型。

*class* `pyalgotrade.broker.slippage.``VolumeShareSlippage`(*priceImpact=0.1*)

基类：`pyalgotrade.broker.slippage.SlippageModel`

定义在 Zipline 的 VolumeShareSlippage 模型中的成交量份额滑点模型。滑点是通过将价格影响常数乘以订单与总成交量比率的平方来计算的。

查看 [`www.quantopian.com/help#ide-slippage`](https://www.quantopian.com/help#ide-slippage) 获取更多详细信息。

| 参数： | **priceImpact** (*float.*) – 定义您的订单对回测价格计算的影响程度。 |
| --- | --- |

*class* `pyalgotrade.broker.fillstrategy.``FillStrategy`

基类：`object`

用于回测的订单填充策略的基类。

`fillLimitOrder`(*broker_*, *order*, *bar*)

覆盖以返回限价订单的成交价格和数量，如果在给定时间无法成交订单，则返回 None。

| 参数： |
| --- |

+   **broker** (`Broker`) – 经纪人。

+   **order** (`pyalgotrade.broker.LimitOrder`) – 订单。

+   **bar** (`pyalgotrade.bar.Bar`) – 当前柱形图。

|

| 返回类型： | 一个 `FillInfo` 或者如果订单不应该被填充则返回 None。 |
| --- | --- |

`fillMarketOrder`(*broker_*, *order*, *bar*)

覆盖以返回市价订单的成交价格和数量，如果在给定时间无法成交订单，则返回 None。

| 参数： |
| --- |

+   **broker** (`Broker`) – 经纪人。

+   **order** (`pyalgotrade.broker.MarketOrder`) – 订单。

+   **bar** (`pyalgotrade.bar.Bar`) – 当前柱形图。

|

| 返回类型： | 一个 `FillInfo` 或者如果订单不应该被填充则返回 None。 |
| --- | --- |

`fillStopLimitOrder`(*broker_*, *order*, *bar*)

覆盖以返回停止限价订单的成交价格和数量，如果在给定时间无法成交订单，则返回 None。

| 参数： |
| --- |

+   **broker** (`Broker`) – 经纪人。

+   **order** (`pyalgotrade.broker.StopLimitOrder`) – 订单。

+   **bar** (`pyalgotrade.bar.Bar`) – 当前柱形图。

|

| 返回类型： | 一个 `FillInfo` 或者如果订单不应该被填充则返回 None。 |
| --- | --- |

`fillStopOrder`(*broker_*, *order*, *bar*)

覆盖以返回停止订单的成交价格和数量，如果在给定时间无法成交订单，则返回 None。

| 参数： |
| --- |

+   **broker** (`Broker`) – 经纪人。

+   **order** (`pyalgotrade.broker.StopOrder`) – 订单。

+   **bar** (`pyalgotrade.bar.Bar`) – 当前柱形图。

|

| 返回类型： | 一个 `FillInfo` 或者如果订单不应该被填充则返回 None。 |
| --- | --- |

`onBars`(*broker_*, *bars*)

覆盖（可选）以在经纪人处理新柱形图时收到通知。

| 参数： |
| --- |

+   **broker**（`Broker`）- 经纪人。

+   **bars**（`pyalgotrade.bar.Bars`）- 当前的条形图。

|

`onOrderFilled`（*broker_*, *order*）

重写（可选）以在订单被填充或部分填充时收到通知。

| 参数： |
| --- |

+   **broker**（`Broker`）- 经纪人。

+   **order**（`pyalgotrade.broker.Order`）- 填充的订单。

|

*class* `pyalgotrade.broker.fillstrategy.``DefaultStrategy`（*volumeLimit=0.25*）

基类：`pyalgotrade.broker.fillstrategy.FillStrategy`

默认填充策略。

| 参数： | **volumeLimit**（*float*）- 订单在一根条中可以占用的交易量的比例。必须> 0 且<= 1。如果为 None，则不检查交易量限制。 |
| --- | --- |

此策略的工作方式如下：

+   一个`pyalgotrade.broker.MarketOrder`始终使用开盘/收盘价填充。

+   一个`pyalgotrade.broker.LimitOrder`将会被如下方式填充：

    +   如果限价已与开盘价突破，则使用开盘价。

    +   如果该条包含限价，则使用限价。

    +   请注意，在买入时，如果价格达到或低于限价，则价格会被突破，而在卖出时，如果价格达到或超过限价，则价格会被突破。

+   一个`pyalgotrade.broker.StopOrder`将会被如下方式填充：

    +   如果止损价已与开盘价突破，则使用开盘价。

    +   如果该条包含止损价，则使用止损价。

    +   请注意，在买入时，如果价格达到或超过止损价，则价格会被突破，而在卖出时，如果价格达到或低于止损价，则价格会被突破。

+   一个`pyalgotrade.broker.StopLimitOrder`将会被如下方式填充：

    +   如果止损价已与开盘价突破，或者如果该条包含止损价，则限价单将变为活动状态。

    +   如果限价单处于活动状态：

        +   如果在同一根条中激活了限价单，并且限价也被突破，则使用止损价和限价填充价格中较好的那个（如前所述）。

        +   如果限价单在先前的条中激活，则使用限价填充价格（如前所述）。

注意

+   这是经纪人使用的默认策略。

+   默认情况下，它使用`pyalgotrade.broker.slippage.NoSlippage`滑点模型。

+   如果 volumeLimit 为 0.25，而某个条的交易量为 100，则在该条处理的所有订单中最多只能使用 25 股。

+   如果使用交易条形图，则该条的所有交易量都可以使用。

`setSlippageModel`（*slippageModel*）

设置滑点模型以使用。

| 参数: | **slippageModel** (`pyalgotrade.broker.slippage.SlippageModel`) – 滑点模型。 |
| --- | --- |

`setVolumeLimit`(*volumeLimit*)

设定体积限制。

| 参数: | **volumeLimit** (*float*) – 订单在柱状图中可以占用的体积比例。必须 > 0 且 <= 1。如果为 None，则不检查体积限制。 |
| --- | --- |

### 目录

+   经纪人 – 订单管理类

    +   基础模块和类

    +   回测模块和类

