# strategy – 基本策略类

> 原文：[`gbeced.github.io/pyalgotrade/docs/v0.20/html/strategy.html`](https://gbeced.github.io/pyalgotrade/docs/v0.20/html/strategy.html)

策略是您定义的类，实现交易逻辑，何时买入，何时卖出等。

买卖可以通过两种方式进行：

> +   使用以下任何方法进行个别订单的下单：
> +   
> > +   `pyalgotrade.strategy.BaseStrategy.marketOrder()`
> > +   
> > +   `pyalgotrade.strategy.BaseStrategy.limitOrder()`
> > +   
> > +   `pyalgotrade.strategy.BaseStrategy.stopOrder()`
> > +   
> > +   `pyalgotrade.strategy.BaseStrategy.stopLimitOrder()`
> > +   
> +   使用包装一对进入/退出订单的更高级别接口：
> +   
> > +   `pyalgotrade.strategy.BaseStrategy.enterLong()`
> > +   
> > +   `pyalgotrade.strategy.BaseStrategy.enterShort()`
> > +   
> > +   `pyalgotrade.strategy.BaseStrategy.enterLongLimit()`
> > +   
> > +   `pyalgotrade.strategy.BaseStrategy.enterShortLimit()`

位置是用于下单的高级抽象。它们本质上是一对进入-退出订单，并提供比使用个别订单更容易跟踪回报和损益的方式。

## 策略

*class* `pyalgotrade.strategy.``BaseStrategy`(*barFeed*, *broker*)

基类： `object`

策略的基类。

| 参数： |
| --- |

+   **barFeed** (`pyalgotrade.barfeed.BaseBarFeed`.) – 将提供 bars 的 bar feed。

+   **broker** (`pyalgotrade.broker.Broker`.) – 处理订单的经纪人。

|

注意

这是一个基类，不应直接使用。

`getFeed`()

返回此策略正在使用的`pyalgotrade.barfeed.BaseBarFeed`。

`getBroker`()

返回用于处理订单执行的`pyalgotrade.broker.Broker`。

`getCurrentDateTime`()

返回当前`pyalgotrade.bar.Bars`的`datetime.datetime`。

`marketOrder`(*instrument*, *quantity*, *onClose=False*, *goodTillCanceled=False*, *allOrNone=False*)

提交市价订单。

| 参数： |
| --- |

+   **instrument**（*字符串.*） – 工具标识符。

+   **quantity**（*int/float.*） – 股票数量。正数表示买入，负数表示卖出。

+   **onClose**（*布尔值.*） – 如果订单应尽可能接近收盘价成交（市价收盘订单），则为 True。默认值为 False。

+   **goodTillCanceled**（*布尔值.*） – 如果订单有效直至取消，则为 True。如果为 False，则订单在会话关闭时自动取消。

+   **allOrNone**（*布尔值.*） – 如果订单应完全成交或根本不成交，则为 True。

|

| 返回类型： | 提交的`pyalgotrade.broker.MarketOrder`。 |
| --- | --- |

`limitOrder`（*instrument*, *limitPrice*, *quantity*, *goodTillCanceled=False*, *allOrNone=False*）

提交一个限价订单。

| 参数： |
| --- |

+   **instrument**（*字符串.*） – 工具标识符。

+   **limitPrice**（*浮点数.*） – 限价。

+   **quantity**（*int/float.*） – 股票数量。正数表示买入，负数表示卖出。

+   **goodTillCanceled**（*布尔值.*） – 如果订单有效直至取消，则为 True。如果为 False，则订单在会话关闭时自动取消。

+   **allOrNone**（*布尔值.*） – 如果订单应完全成交或根本不成交，则为 True。

|

| 返回类型： | 提交的`pyalgotrade.broker.LimitOrder`。 |
| --- | --- |

`stopOrder`（*instrument*, *stopPrice*, *quantity*, *goodTillCanceled=False*, *allOrNone=False*）

提交一个止损订单。

| 参数： |
| --- |

+   **instrument**（*字符串.*） – 工具标识符。

+   **stopPrice**（*浮点数.*） – 止损价格。

+   **quantity**（*int/float.*） – 股票数量。正数表示买入，负数表示卖出。

+   **goodTillCanceled**（*布尔值.*） – 如果订单有效直至取消，则为 True。如果为 False，则订单在会话关闭时自动取消。

+   **allOrNone**（*布尔值.*） – 如果订单应完全成交或根本不成交，则为 True。

|

| 返回类型： | 提交的`pyalgotrade.broker.StopOrder`。 |
| --- | --- |

`stopLimitOrder`（*instrument*, *stopPrice*, *limitPrice*, *quantity*, *goodTillCanceled=False*, *allOrNone=False*）

提交一个市价订单。

| 参数： |
| --- |

+   **instrument**（*字符串.*） – 工具标识符。

+   **stopPrice**（*浮点数.*） – 止损价格。

+   **limitPrice**（*浮点数.*） – 限价。

+   **quantity**（*int/float.*） – 股票数量。正数表示买入，负数表示卖出。

+   **goodTillCanceled**（*布尔值.*） – 如果订单有效直至取消，则为 True。如果为 False，则订单在会话关闭时自动取消。

+   **allOrNone**（*布尔值.*） – 如果订单应完全成交或根本不成交，则为 True。

|

| 返回类型： | 提交的`pyalgotrade.broker.StopLimitOrder`。 |
| --- | --- |

`enterLong`（*instrument*, *quantity*, *goodTillCanceled=False*, *allOrNone=False*）

生成一个购买`pyalgotrade.broker.MarketOrder`以进入多头头寸。

| 参数： |
| --- |

+   **instrument** (*字符串。*) – 工具标识符。

+   **quantity** (*整数。*) – 入场订单数量。

+   **goodTillCanceled** (*布尔值。*) – 如果入场订单有效期直到取消，则为 True。如果为 False，则当会话关闭时订单会自动取消。

+   **allOrNone** (*布尔值。*) – 如果订单应完全成交或根本不成交，则为 True。

|

| 返回类型： | 进入`pyalgotrade.strategy.position.Position`。 |
| --- | --- |

`enterShort`(*instrument*, *quantity*, *goodTillCanceled=False*, *allOrNone=False*)

生成一个卖空`pyalgotrade.broker.MarketOrder`以进入空头头寸。

| 参数： |
| --- |

+   **instrument** (*字符串。*) – 工具标识符。

+   **quantity** (*整数。*) – 入场订单数量。

+   **goodTillCanceled** (*布尔值。*) – 如果入场订单有效期直到取消，则为 True。如果为 False，则当会话关闭时订单会自动取消。

+   **allOrNone** (*布尔值。*) – 如果订单应完全成交或根本不成交，则为 True。

|

| 返回类型： | 进入`pyalgotrade.strategy.position.Position`。 |
| --- | --- |

`enterLongLimit`(*instrument*, *limitPrice*, *quantity*, *goodTillCanceled=False*, *allOrNone=False*)

生成一个购买`pyalgotrade.broker.LimitOrder`以进入多头头寸。

| 参数： |
| --- |

+   **instrument** (*字符串。*) – 工具标识符。

+   **limitPrice** (*浮点数。*) – 限价。

+   **quantity** (*整数。*) – 入场订单数量。

+   **goodTillCanceled** (*布尔值。*) – 如果入场订单有效期直到取消，则为 True。如果为 False，则当会话关闭时订单会自动取消。

+   **allOrNone** (*布尔值。*) – 如果订单应完全成交或根本不成交，则为 True。

|

| 返回类型： | 进入`pyalgotrade.strategy.position.Position`。 |
| --- | --- |

`enterShortLimit`(*instrument*, *limitPrice*, *quantity*, *goodTillCanceled=False*, *allOrNone=False*)

生成一个卖空`pyalgotrade.broker.LimitOrder`以进入空头头寸。

| 参数： |
| --- |

+   **instrument** (*字符串。*) – 工具标识符。

+   **limitPrice** (*浮点数。*) – 限价。

+   **quantity** (*整数。*) – 入场订单数量。

+   **goodTillCanceled** (*布尔值。*) – 如果入场订单有效期直到取消，则为 True。如果为 False，则当会话关闭时订单会自动取消。

+   **allOrNone** (*布尔值。*) – 如果订单应完全成交或根本不成交，则为 True。

|

| 返回类型： | 输入的`pyalgotrade.strategy.position.Position`。 |
| --- | --- |

`enterLongStop`（*instrument*，*stopPrice*，*quantity*，*goodTillCanceled=False*，*allOrNone=False*）

生成一个购买`pyalgotrade.broker.StopOrder`以进入多头头寸。

| 参数： |
| --- |

+   **instrument**（*字符串.*） - 工具标识符。

+   **stopPrice**（*float.*） - 停止价格。

+   **quantity**（*整数.*） - 进入订单数量。

+   **goodTillCanceled**（*布尔值.*） - 如果进入订单有效直至取消，则为 True。如果为 False，则订单在交易会话结束时自动取消。

+   **allOrNone**（*布尔值.*） - 如果订单应完全填充或根本不填充，则为 True。

|

| 返回类型： | 输入的`pyalgotrade.strategy.position.Position`。 |
| --- | --- |

`enterShortStop`（*instrument*，*stopPrice*，*quantity*，*goodTillCanceled=False*，*allOrNone=False*）

生成一个卖空`pyalgotrade.broker.StopOrder`以进入空头头寸。

| 参数： |
| --- |

+   **instrument**（*字符串.*） - 工具标识符。

+   **stopPrice**（*float.*） - 停止价格。

+   **quantity**（*整数.*） - 进入订单数量。

+   **goodTillCanceled**（*布尔值.*） - 如果进入订单有效直至取消，则为 True。如果为 False，则订单在交易会话结束时自动取消。

+   **allOrNone**（*布尔值.*） - 如果订单应完全填充或根本不填充，则为 True。

|

| 返回类型： | 输入的`pyalgotrade.strategy.position.Position`。 |
| --- | --- |

`enterLongStopLimit`（*instrument*，*stopPrice*，*limitPrice*，*quantity*，*goodTillCanceled=False*，*allOrNone=False*）

生成一个购买`pyalgotrade.broker.StopLimitOrder`订单以进入多头头寸。

| 参数： |
| --- |

+   **instrument**（*字符串.*） - 工具标识符。

+   **stopPrice**（*float.*） - 停止价格。

+   **limitPrice**（*float.*） - 限价。

+   **quantity**（*整数.*） - 进入订单数量。

+   **goodTillCanceled**（*布尔值.*） - 如果进入订单有效直至取消，则为 True。如果为 False，则订单在交易会话结束时自动取消。

+   **allOrNone**（*布尔值.*） - 如果订单应完全填充或根本不填充，则为 True。

|

| 返回类型： | 输入的`pyalgotrade.strategy.position.Position`。 |
| --- | --- |

`enterShortStopLimit`（*instrument*，*stopPrice*，*limitPrice*，*quantity*，*goodTillCanceled=False*，*allOrNone=False*）

生成一个卖空`pyalgotrade.broker.StopLimitOrder`订单以进入空头头寸。

| 参数： |
| --- |

+   **instrument**（*字符串*） - 仪器标识符。

+   **stopPrice**（*浮点数*） - 停止价格。

+   **limitPrice**（*浮点数*） - 限价。

+   **quantity**（*整数*） - 入市订单数量。

+   **goodTillCanceled**（*布尔值*） - 如果入市订单有效直到取消，则为 True。如果为 False，则订单在交易日结束时自动取消。

+   **allOrNone**（*布尔值*） - 如果订单应完全成交或根本不成交，则为 True。

|

| 返回类型: | 输入的`pyalgotrade.strategy.position.Position`。 |
| --- | --- |

`onEnterOk`（*position*）

覆盖（可选）以在提交用于进入持仓的订单被成交时收到通知。默认实现为空。

| 参数: | **position**（`pyalgotrade.strategy.position.Position`.） - 由任何 enterLongXXX 或 enterShortXXX 方法返回的持仓。 |
| --- | --- |

`onEnterCanceled`（*position*）

覆盖（可选）以在提交用于进入持仓的订单被取消时收到通知。默认实现为空。

| 参数: | **position**（`pyalgotrade.strategy.position.Position`.） - 由任何 enterLongXXX 或 enterShortXXX 方法返回的持仓。 |
| --- | --- |

`onExitOk`（*position*）

覆盖（可选）以在提交用于退出持仓的订单被成交时收到通知。默认实现为空。

| 参数: | **position**（`pyalgotrade.strategy.position.Position`.） - 由任何 enterLongXXX 或 enterShortXXX 方法返回的持仓。 |
| --- | --- |

`onExitCanceled`（*position*）

覆盖（可选）以在提交用于退出持仓的订单被取消时收到通知。默认实现为空。

| 参数: | **position**（`pyalgotrade.strategy.position.Position`.） - 由任何 enterLongXXX 或 enterShortXXX 方法返回的持仓。 |
| --- | --- |

`onStart`（）

覆盖（可选）以在策略开始执行时收到通知。默认实现为空。

`onFinish`（*bars*）

覆盖（可选）以在策略完成执行时收到通知。默认实现为空。

| 参数: | **bars**（`pyalgotrade.bar.Bars`.） - 最后处理的 Bars。 |
| --- | --- |

`onIdle`（）

覆盖（可选）以在没有事件时收到通知。

注意

在纯回测情景中，此方法不会被调用。

`onBars`（*bars*）

覆盖（**强制**）以在有新 Bars 可用时收到通知。默认实现引发异常。

**这是要覆盖的方法，以输入您的交易逻辑并输入/退出持仓**。

| 参数： | **bars** (`pyalgotrade.bar.Bars`.) – 当前的 K 线数据。 |
| --- | --- |

`onOrderUpdated`(*order*)

覆盖（可选）以在订单更新时收到通知。

| 参数： | **order** (`pyalgotrade.broker.Order`.) – 更新的订单。 |
| --- | --- |

`run`()

仅调用一次（**仅一次**）以运行策略。

`stop`()

停止正在运行的策略。

`attachAnalyzer`(*strategyAnalyzer*)

添加一个 `pyalgotrade.stratanalyzer.StrategyAnalyzer`。

`debug`(*msg*)

在策略记录器上以 DEBUG 级别记录消息。

`info`(*msg*)

在策略记录器上以 INFO 级别记录消息。

`warning`(*msg*)

在策略记录器上以 WARNING 级别记录消息。

`error`(*msg*)

在策略记录器上以 ERROR 级别记录消息。

`critical`(*msg*)

在策略记录器上以 CRITICAL 级别记录消息。

`resampleBarFeed`(*frequency*, *callback*)

构建一个通过特定频率分组的重采样 K 线数据源。

| 参数： |
| --- |

+   **frequency** – 分组频率（以秒为单位）。必须大于 0。

+   **callback** – 一个类似于 onBars 的函数，当有新的 K 线数据可用时将被调用。

|

| 返回类型： | `pyalgotrade.barfeed.BaseBarFeed`. |
| --- | --- |

*class* `pyalgotrade.strategy.``BacktestingStrategy`(*barFeed*, *cash_or_brk=1000000*)

基类：`pyalgotrade.strategy.BaseStrategy`

策略回测的基类。

| 参数： |
| --- |

+   **barFeed** (`pyalgotrade.barfeed.BaseBarFeed`.) – 用于策略回测的 K 线数据源。

+   **cash_or_brk**（int/float 或 `pyalgotrade.broker.Broker`.） – 起始资本或经纪人实例。

|

注意

这是一个基类，不应该直接使用。

`setDebugMode`(*debugOn*)

启用/禁用策略和回测经纪人中的调试级别消息。默认情况下启用。  ## 头寸

*class* `pyalgotrade.strategy.position.``Position`(*strategy*, *entryOrder*, *goodTillCanceled*, *allOrNone*)

基类： `object`

头寸的基类。

头寸是更高级别的抽象，用于下订单。它们基本上是一对进出订单，并且允许跟踪回报和利润更容易地比手动下订单。

| 参数： |
| --- |

+   **strategy** (`pyalgotrade.strategy.BaseStrategy`.) – 属于该头寸的策略。

+   **entryOrder** (`pyalgotrade.broker.Order`) – 用于进入头寸的订单。

+   **goodTillCanceled** (*boolean.*) – 如果应将进入订单设置为有效直至取消，则为 True。

+   **allOrNone** (*boolean.*) – 如果订单应完全成交或根本不成交，则为 True。

|

注意

这是一个基类，不应直接使用。

`getShares`()

返回股票数量。 对于多头头寸，这将是正数，对于空头头寸，这将是负数。

注意

如果进入订单未成交，或者如果头寸已关闭，则股票数量将为 0。

`entryActive`()

如果进入订单有效，则返回 True。

`entryFilled`()

如果进入订单已成交，则返回 True。

`exitActive`()

如果退出订单有效，则返回 True。

`exitFilled`()

如果退出订单已成交，则返回 True。

`getEntryOrder`()

返回用于进入该头寸的`pyalgotrade.broker.Order`。

`getExitOrder`()

返回用于退出该头寸的`pyalgotrade.broker.Order`。 如果此头寸尚未关闭，则返回 None。

`getInstrument`()

返回用于此头寸的工具。

`getReturn`(*includeCommissions=True*)

计算到目前为止的累积百分比收益。 如果头寸未关闭，则这些将是未实现的收益。

`getPnL`(*includeCommissions=True*)

计算到目前为止的损益。 如果头寸未关闭，则这些将是未实现的损益。

`cancelEntry`()

如果进入订单有效，则会请求取消。

`cancelExit`()

如果退出订单有效，则会请求取消。

`exitMarket`(*goodTillCanceled=None*)

提交市价订单以关闭此头寸。

| 参数: | **goodTillCanceled** (*boolean.*) – 如果退出订单有效直到取消，则为 True。 如果为 False，则当会话关闭时订单会自动取消。 如果为 None，则与进入订单匹配。 |
| --- | --- |

注意

+   如果头寸已关闭（进入取消或退出成交），则此操作不会产生任何影响。

+   如果此头寸的退出订单处于挂起状态，则会引发异常。 应先取消退出订单。

+   如果进入订单有效，则会请求取消。

`exitLimit`(*limitPrice*, *goodTillCanceled=None*)

提交限价订单以关闭此头寸。

| 参数: |
| --- |

+   **limitPrice** (*float.*) – 限价。

+   **goodTillCanceled** (*boolean.*) – 如果退出订单有效直到取消，则为 True。 如果为 False，则当会话关闭时订单会自动取消。 如果为 None，则与进入订单匹配。

|

注意

+   如果头寸已关闭（进入取消或退出成交），则此操作不会产生任何影响。

+   如果此头寸的退出订单处于挂起状态，则会引发异常。 应先取消退出订单。

+   如果进入订单有效，则会请求取消。

`exitStop`(*stopPrice*, *goodTillCanceled=None*)

提交停止订单以关闭此头寸。

| 参数: |
| --- |

+   **stopPrice** (*float.*) – 停止价格。

+   **goodTillCanceled**（*boolean.*）– 如果退出订单有效期长达取消，则为 True。如果为 False，则在会话关闭时订单会自动取消。如果为 None，则会与进入订单匹配。

|

注意

+   如果持仓已关闭（进入取消或退出已填充），则不会产生任何影响。

+   如果此持仓的退出订单仍在等待中，则会引发异常。必须首先取消退出订单。

+   如果进入订单仍然有效，则将请求取消。

`exitStopLimit`（*stopPrice*, *limitPrice*, *goodTillCanceled=None*）

提交一个停止限价订单以关闭此持仓。

| 参数： |
| --- |

+   **stopPrice**（*float.*）– 止损价。

+   **limitPrice**（*float.*）– 限价。

+   **goodTillCanceled**（*boolean.*）– 如果退出订单有效期长达取消，则为 True。如果为 False，则在会话关闭时订单会自动取消。如果为 None，则会与进入订单匹配。

|

注意

+   如果持仓已关闭（进入取消或退出已填充），则不会产生任何影响。

+   如果此持仓的退出订单仍在等待中，则会引发异常。必须首先取消退出订单。

+   如果进入订单仍然有效，则将请求取消。

`isOpen`()

如果持仓是打开的，则返回 True。

`getAge`()

返回处于打开状态的持续时间。

| 返回类型： | datetime.timedelta。 |
| --- | --- |

注意

+   如果持仓是打开的，则返回进入日期和最后一根 K 线日期之间的差异。

+   如果持仓已关闭，则返回进入日期和退出日期之间的差异。

### 目录

+   strategy – 基本策略类

    +   策略

    +   持仓

