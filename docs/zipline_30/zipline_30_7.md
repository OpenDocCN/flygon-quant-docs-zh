# API

> 原文：[`zipline.ml4trading.io/api-reference.html`](https://zipline.ml4trading.io/api-reference.html)

## 运行回测

`run_algorithm()`函数创建一个`TradingAlgorithm`实例，该实例代表一个交易策略和执行该策略的参数。

```py
zipline.run_algorithm(...)
```

运行交易算法。

参数：

+   **开始** (*datetime*) – 回测的开始日期。

+   **结束** (*datetime*) – 回测的结束日期。

+   **初始化** (*可调用**[**上下文 -> None**]*) – 用于算法的初始化函数。在回测开始时调用一次，用于设置算法所需的状态。

+   **资本基础** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 回测的起始资本。

+   **处理数据** (*可调用****(**上下文**,* [*BarData**)* *-> None**]**,* *可选*) – 用于算法的处理数据函数。当`data_frequency == 'minute'`时，每分钟调用一次；当`data_frequency == 'daily'`时，每天调用一次。

+   **交易开始前** (*可调用****(**上下文**,* [*BarData**)* *-> None**]**,* *可选*) – 算法的交易开始前函数。在每个交易日开始前调用一次（在初始化的第一天之后）。

+   **分析** (*可调用**[**(**上下文**,* *pd.DataFrame**)* *-> None**]**,* *可选*) – 用于算法的分析函数。该函数在回测结束时被调用一次，并传入上下文和性能数据。

+   **数据频率** (*{'daily'**,* *'minute'}**,* *可选*) – 算法运行的数据频率。

+   **捆绑包** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 用于加载回测数据的捆绑包名称。默认为‘quantopian-quandl’。

+   **捆绑时间戳** (*datetime**,* *可选*) – 查找捆绑数据的日期时间。默认为当前时间。

+   **交易日历** (*TradingCalendar**,* *可选*) – 用于回测的交易日历。

+   **指标集** (*可迭代**[**Metric**]或* [*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 模拟中要计算的指标集。如果传递了字符串，则使用`zipline.finance.metrics.load()`解析集合。

+   **基准收益** (*pd.Series**,* *可选*) – 用作基准的收益序列。

+   **默认扩展** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 是否加载默认的 zipline 扩展。该扩展位于`$ZIPLINE_ROOT/extension.py`。

+   **extensions** (*iterable**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*]**,* *optional*) – 要加载的任何其他扩展的名称。每个元素可以是像`a.b.c`这样的点分隔模块路径，也可以是像`a/b/c.py`这样的 python 文件路径，以`.py`结尾。

+   **strict_extensions** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *optional*) – 如果任何扩展加载失败，运行是否应该失败。如果此参数为 False，则会发出警告。

+   **environ** (*mapping**[**str -> str**]**,* *optional*) – 要使用的操作系统环境。许多扩展使用此参数来获取参数。默认值为`os.environ`。

+   **blotter** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *or* *zipline.finance.blotter.Blotter**,* *optional*) – 要与此算法一起使用的 blotter。如果作为字符串传递，我们会在`zipline.extensions.register`中查找 blotter 构造函数并调用它，不带任何参数。默认值是一个永远不会取消订单的`zipline.finance.blotter.SimulationBlotter`。

返回：

**perf** – 算法的日表现。

返回类型：

pd.DataFrame

另请参阅

`zipline.data.bundles.bundles`

可用的数据包。

## 交易算法 API

在`initialize`、`handle_data`和`before_trading_start` API 函数中可用的方法如下。

在所有列出的函数中，`self`参数指的是当前执行的`TradingAlgorithm`实例。

### 数据对象

```py
class zipline.protocol.BarData
```

提供从算法 API 函数访问每分钟和每日价格/成交量数据的方法。

还提供实用方法来确定资产是否存活，以及它是否有最近的成交数据。

此对象的实例作为`data`传递给`handle_data()`和`before_trading_start()`。

参数：

+   **data_portal** (*DataPortal*) – 提供条形价格数据的提供者。

+   **simulation_dt_func** (*callable*) – 返回当前模拟时间的函数。这通常绑定到 TradingSimulation 的方法。

+   **data_frequency** (*{'minute'**,* *'daily'}*) – 条形数据的频率；即数据是每日还是分钟条形图

+   **restrictions** (*zipline.finance.asset_restrictions.Restrictions*) – 结合并返回来自多个来源的受限列表信息的对象

```py
can_trade()
```

对于给定的资产或资产迭代器，如果满足以下所有条件，则返回 True：

1.  资产在当前模拟时间的会话中存活（如果当前模拟时间不是市场分钟，我们使用下一个会话）。

1.  资产的交易所当前模拟时间或模拟日历的下一个市场分钟是开放的。

1.  资产有一个已知的最后价格。

参数：

**assets** (*zipline.assets.Asset* *或* *iterable* *of* *zipline.assets.Asset*) – 确定可交易性的资产。

注意

上述第二个条件需要进一步解释：

+   如果资产的交易所日历与模拟日历相同，则此条件始终返回 True。

+   如果在模拟日历中有市场分钟不在该资产交易所的交易时间内（例如，如果模拟运行在 CMES 日历上，但资产是 MSFT，它在 NYSE 交易），在这些分钟内，这个条件将返回 False（例如，东部时间工作日早上 3:15，此时 CMES 开放但 NYSE 关闭）。

返回：

**can_trade** – 布尔值或布尔序列，指示在当前分钟内请求的资产是否可以交易。

返回类型：

[bool](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)") 或 pd.Series[[bool](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")]

```py
current()
```

返回给定资产在当前模拟时间下给定字段的“当前”值。

参数：

+   **assets** (*zipline.assets.Asset* *或* *iterable* *of* *zipline.assets.Asset*) – 请求数据的资产。

+   **fields** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* *iterable*[*[*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*]**) – 请求的数据字段。有效的字段名称包括：“价格”、“最后交易”、“开盘”、“最高”、“最低”、“收盘”和“成交量”。

返回：

**current_value** – 见下文注释。

返回类型：

标量、pandas Series 或 pandas DataFrame。

注意

此函数的返回类型取决于其输入的类型：

+   如果请求的是单个资产和一个字段，返回的值是一个标量（根据字段不同，可能是浮点数或`pd.Timestamp`）。

+   如果请求的是单个资产和一组字段，返回的值是一个`pd.Series`，其索引是请求的字段。

+   如果请求的是一组资产和一个字段，返回的值是一个`pd.Series`，其索引是资产。

+   如果请求的是一组资产和一组字段，返回的值是一个`pd.DataFrame`。返回的框架的列将是请求的字段，索引将是请求的资产。

对于`fields`产生的值如下：

+   请求“价格”将产生该资产的最新收盘价格，如果该分钟没有交易，则从更早的一分钟前向填充。如果没有最新已知值（可能是因为该资产从未交易过，或者已经退市），则返回 NaN。如果找到值，并且我们必须跨越调整边界（拆分、股息等）才能获得它，则在返回之前将该值调整为当前模拟时间。

+   请求“开盘”、“最高”、“最低”或“收盘”将产生当前分钟的开盘、最高、最低或收盘价。如果该分钟没有交易发生，则返回`NaN`。

+   请求“成交量”将产生当前分钟的成交量。如果该分钟没有交易发生，则返回 0。

+   请求“最后交易”将产生该资产最后一次交易的分钟时间，即使该资产已经停止交易。如果没有最后一次已知值，则返回`pd.NaT`。

如果当前模拟时间对于某个资产不是有效的市场时间，我们将使用最近的市场收盘价代替。

```py
history()
```

返回一个长度为`bar_count`的尾随窗口，其中包含给定资产、字段和频率的数据，并根据当前模拟时间调整了拆分、股息和合并。

缺失数据的行为与`current()`的注释中描述的行为相同。

参数：

+   **assets**（*zipline.assets.Asset* *或* *可迭代* *的* *zipline.assets.Asset*）——请求数据的资产。

+   **fields**（*字符串* *或* *可迭代* *的* *字符串*）——请求的数据字段。有效的字段名称包括：“价格”、“最后交易”、“开盘”、“最高”、“最低”、“收盘”和“成交量”。

+   **bar_count**（[*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")）——请求的数据观测值数量。

+   **频率**（[*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")）——指示是否加载每日或每分钟数据观测值的字符串。传递'1m'表示每分钟数据，'1d'表示每日数据。

返回：

**历史** – 请参见下面的注释。

返回类型：

pd.Series 或 pd.DataFrame 或 pd.Panel

笔记

此函数的返回类型取决于`assets`和`fields`的类型：

+   如果请求了一个资产和一个字段，返回的值是一个长度为`bar_count`的`pd.Series`，其索引是`pd.DatetimeIndex`。

+   如果请求了一个资产和多个字段，返回的值是一个具有形状`(bar_count, len(fields))`的`pd.DataFrame`。该数据框的索引将是一个`pd.DatetimeIndex`，其列将是`fields`。

+   如果请求了多个资产和一个字段，返回的值是一个具有形状`(bar_count, len(assets))`的`pd.DataFrame`。该数据框的索引将是一个`pd.DatetimeIndex`，其列将是`assets`。

+   如果请求了多个资产和多个字段，则返回值是具有 pd.MultiIndex 的 `pd.DataFrame`，其中包含 `pd.DatetimeIndex` 和 `assets` 的对，而列将包含字段（s）。它具有形状 `(bar_count * len(assets), len(fields))`。pd.MultiIndex 的名称是

    > +   `date` 如果频率 == ‘1d’`` 或 `date_time` 如果频率 == ‘1m``, 和
    > +   
    > +   `asset`

如果当前模拟时间不是有效的市场时间，我们将使用上次市场收盘时间代替。

```py
is_stale()
```

对于给定的资产或资产迭代器，如果资产存活且当前模拟时间没有交易数据，则返回 True。

如果资产从未交易，则返回 False。

如果当前模拟时间不是有效的市场时间，我们使用当前时间检查资产是否存活，但我们使用上次市场分钟/日进行交易数据检查。

参数：

**assets** (*zipline.assets.Asset* *或* *iterable* *of* *zipline.assets.Asset*) – 应确定其过时性的资产。

返回：

**is_stale** – 布尔值或布尔序列，指示请求的资产是否过时。

返回类型：

[bool](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)") 或 pd.Series[[bool](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")]

### 调度函数

```py
zipline.api.schedule_function(self, func, date_rule=None, time_rule=None, half_days=True, calendar=None)
```

安排一个函数在未来重复调用。

参数：

+   **func** (*callable*) – 当规则触发时要执行的函数。`func` 应该与 `handle_data` 具有相同的签名。

+   **date_rule** (*zipline.utils.events.EventRule**,* *optional*) – 用于执行 `func` 的日期规则。如果未传递，则函数将在每个交易日运行。

+   **time_rule** (*zipline.utils.events.EventRule**,* *optional*) – 用于执行 `func` 的时间规则。如果未传递，则函数将在一天的第一个市场分钟的末尾执行。

+   **half_days** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *optional*) – 此规则是否应在半天内触发？默认为 True。

+   **calendar** (*Sentinel**,* *optional*) – 用于计算依赖于交易日的规则的日历。

另请参阅

`zipline.api.date_rules`, `zipline.api.time_rules`

```py
class zipline.api.date_rules
```

基于日期的工厂 `schedule_function()` 规则。

另请参阅

`schedule_function()`

```py
static every_day()
```

创建一个每天触发的规则。

返回：

**rule**

返回类型：

zipline.utils.events.EventRule

```py
static month_end(days_offset=0)
```

创建一个规则，该规则在每个月末之前的固定数量的交易日触发。

参数：

**days_offset** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *optional*) – 触发前距离月末的交易天数。默认值为 0，即在月末最后一天触发。

返回：

**规则**

返回类型：

zipline.utils.events.EventRule

```py
static month_start(days_offset=0)
```

创建一个在每月开始后固定交易天数触发的规则。

参数：

**days_offset** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *optional*) – 每月触发前等待的交易天数。默认值为 0，即在每月第一个交易日触发。

返回：

**规则**

返回类型：

zipline.utils.events.EventRule

```py
static week_end(days_offset=0)
```

创建一个在每周结束前固定交易天数触发的规则。

参数：

**days_offset** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *optional*) – 触发前距离周末的交易天数。默认值为 0，即在周末最后一个交易日触发。

```py
static week_start(days_offset=0)
```

创建一个在每周开始后固定交易天数触发的规则。

参数：

**days_offset** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *optional*) – 每周触发前等待的交易天数。默认值为 0，即在每周第一个交易日触发。

```py
class zipline.api.time_rules
```

基于时间的 `schedule_function()` 规则的工厂。

另请参阅

`schedule_function()`

```py
every_minute
```

别名：`Always`

```py
static market_close(offset=None, hours=None, minutes=None)
```

创建一个在市场收盘后固定时间触发的规则。

偏移量可以指定为 [`datetime.timedelta`](https://docs.python.org/3/library/datetime.html#datetime.timedelta "(in Python v3.11)")，或者指定为小时和分钟数。

参数：

+   **offset** ([*datetime.timedelta*](https://docs.python.org/3/library/datetime.html#datetime.timedelta "(in Python v3.11)")*,* *optional*) – 如果传递，触发的时间距离收盘的偏移量。必须至少为 1 分钟。

+   **hours** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *optional*) – 如果传递，则在收盘前等待的小时数。

+   **minutes** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *optional*) – 如果传递，则在收盘前等待的分钟数。

返回：

**规则**

返回类型：

zipline.utils.events.EventRule

注意

如果没有传递参数，默认偏移量是收盘前一分钟。

如果传递了`offset`，则不能传递`hours`和`minutes`。相反，如果传递了`hours`或`minutes`，则不能传递`offset`。

```py
static market_open(offset=None, hours=None, minutes=None)
```

创建一个在市场开盘后固定时间触发的规则。

偏移量可以指定为 [`datetime.timedelta`](https://docs.python.org/3/library/datetime.html#datetime.timedelta "(in Python v3.11)")，或者指定为小时和分钟数。

参数：

+   **偏移量** ([*datetime.timedelta*](https://docs.python.org/3/library/datetime.html#datetime.timedelta "(in Python v3.11)")*,* *可选*) – 如果传递，触发时的开盘市场偏移量。必须至少为 1 分钟。

+   **小时数** ([*整数*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 如果传递，市场开盘后等待的小时数。

+   **分钟数** ([*整数*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 如果传递，市场开盘后等待的分钟数。

返回：

**规则**

返回类型：

zipline.utils.events.EventRule

注意

如果没有参数传递，默认偏移量为市场开盘后一分钟。

如果传递了`offset`，则不得传递`hours`和`minutes`。相反，如果传递了`hours`或`minutes`，则不得传递`offset`。

### 订单

```py
zipline.api.order(self, asset, amount, limit_price=None, stop_price=None, style=None)
```

下固定数量的股票订单。

参数：

+   **资产** (*资产*) – 要下单的资产。

+   **数量** ([*整数*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 要下单的股票数量。如果`amount`为正数，这是要购买或平仓的股票数量。如果`amount`为负数，这是要卖出或做空的股票数量。

+   **限价** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 订单的限价。

+   **止损价** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 订单的止损价。

+   **风格** (*执行风格**,* *可选*) – 订单的执行风格。

返回：

**订单 ID** – 此订单的唯一标识符，如果没有下单则为 None。

返回类型：

[字符串](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") 或 None

注意

`limit_price`和`stop_price`参数提供了传递常见执行风格的简写方式。传递`limit_price=N`等同于`style=LimitOrder(N)`。类似地，传递`stop_price=M`等同于`style=StopOrder(M)`，传递`limit_price=N`和`stop_price=M`等同于`style=StopLimitOrder(N, M)`。同时传递`style`和`limit_price`或`stop_price`是错误的。

另请参阅

`zipline.finance.execution.ExecutionStyle`, `zipline.api.order_value()`, `zipline.api.order_percent()`

```py
zipline.api.order_value(self, asset, value, limit_price=None, stop_price=None, style=None)
```

下固定金额的订单。

等同于`order(asset, value / data.current(asset, 'price'))`。

参数：

+   **资产** (*资产*) – 要下单的资产。

+   **价值** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")) – 要交易的`资产`的价值量。买入或卖出的股票数量将等于`价值 / 当前价格`。

+   **限价** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")*,* *可选*) – 订单的限价。

+   **止损价** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")*,* *可选*) – 订单的止损价。

+   **类型** (*ExecutionStyle*) – 订单的执行类型。

返回：

**订单 ID** – 此订单的唯一标识符。

返回类型：

[字符串](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中)")

注意

有关`限价`、`止损价`和`类型`的更多信息，请参阅`zipline.api.order()`

参见

`zipline.finance.execution.ExecutionStyle`, `zipline.api.order()`, `zipline.api.order_percent()`

```py
zipline.api.order_percent(self, asset, percent, limit_price=None, stop_price=None, style=None)
```

在指定的资产中下订单，对应于当前投资组合价值的给定百分比。

参数：

+   **资产** (*Asset*) – 此订单所针对的资产。

+   **百分比** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")) – 分配给`资产`的投资组合价值的百分比。以小数形式指定，例如：0.50 表示 50%。

+   **限价** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")*,* *可选*) – 订单的限价。

+   **止损价** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")*,* *可选*) – 订单的止损价。

+   **类型** (*ExecutionStyle*) – 订单的执行类型。

返回：

**订单 ID** – 此订单的唯一标识符。

返回类型：

[字符串](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中)")

注意

有关`限价`、`止损价`和`类型`的更多信息，请参阅`zipline.api.order()`

参见

`zipline.finance.execution.ExecutionStyle`, `zipline.api.order()`, `zipline.api.order_value()`

```py
zipline.api.order_target(self, asset, target, limit_price=None, stop_price=None, style=None)
```

下达订单以调整持仓至目标股数。如果持仓不存在，这等同于下达新订单。如果持仓已存在，这等同于为当前股数与目标股数之差下达订单。

参数：

+   **asset** (*Asset*) – 此订单所针对的资产。

+   **target** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – `asset`的期望股数。

+   **limit_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 订单的限价。

+   **stop_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 订单的止损价格。

+   **style** (*ExecutionStyle*) – 订单的执行风格。

返回：

**order_id** – 此订单的唯一标识符。

返回类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

注释

`order_target`不考虑任何未完成订单。例如：

```py
order_target(sid(0), 10)
order_target(sid(0), 10) 
```

这段代码将导致`sid(0)`的 20 股，因为第一次调用`order_target`时，第二次`order_target`调用尚未完成。

有关`limit_price`、`stop_price`和`style`的更多信息，请参阅`zipline.api.order()`。

另请参阅

`zipline.finance.execution.ExecutionStyle`，`zipline.api.order()`，`zipline.api.order_target_percent()`，`zipline.api.order_target_value()`

```py
zipline.api.order_target_value(self, asset, target, limit_price=None, stop_price=None, style=None)
```

下达订单以调整持仓至目标价值。如果持仓不存在，这等同于下达新订单。如果持仓已存在，这等同于为当前价值与目标价值之差下达订单。如果所订购的资产是期货，则计算的“目标价值”实际上是目标敞口，因为期货没有“价值”。

参数：

+   **asset** (*Asset*) – 此订单所针对的资产。

+   **target** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – `asset`的期望总价值。

+   **limit_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 订单的限价。

+   **stop_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 订单的止损价格。

+   **风格** (*执行风格*) – 订单的执行风格。

返回：

**订单 ID** – 该订单的唯一标识符。

返回类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

注意

`order_target_value` 不考虑任何未完成订单。例如：

```py
order_target_value(sid(0), 10)
order_target_value(sid(0), 10) 
```

这段代码将导致`sid(0)`的 20 美元，因为第一次调用`order_target_value`时，第二次`order_target_value`调用尚未完成。

有关`limit_price`、`stop_price`和`style`的更多信息，请参阅 `zipline.api.order()`

另请参阅

`zipline.finance.execution.ExecutionStyle`, `zipline.api.order()`, `zipline.api.order_target()`, `zipline.api.order_target_percent()`

```py
zipline.api.order_target_percent(self, asset, target, limit_price=None, stop_price=None, style=None)
```

下订单以调整持仓至当前投资组合价值的预定百分比。如果持仓不存在，则等同于下新订单。如果持仓已存在，则等同于下订单以调整目标百分比与当前百分比之间的差额。

参数：

+   **资产** (*资产*) – 该订单所针对的资产。

+   **目标** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 希望分配给`资产`的投资组合价值的百分比。以小数形式指定，例如：0.50 表示 50%。

+   **限价** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 订单的限价。

+   **止损价** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 订单的止损价。

+   **风格** (*执行风格*) – 订单的执行风格。

返回：

**订单 ID** – 该订单的唯一标识符。

返回类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

注意

`order_target_value` 不考虑任何未完成订单。例如：

```py
order_target_percent(sid(0), 10)
order_target_percent(sid(0), 10) 
```

这段代码将导致投资组合的 20%分配给`sid(0)`，因为第一次调用`order_target_percent`时，第二次`order_target_percent`调用尚未完成。

有关`limit_price`、`stop_price`和`style`的更多信息，请参阅 `zipline.api.order()`

另请参阅

`zipline.finance.execution.ExecutionStyle`, `zipline.api.order()`, `zipline.api.order_target()`, `zipline.api.order_target_value()`

```py
class zipline.finance.execution.ExecutionStyle
```

订单执行风格的基类。

```py
property exchange
```

此订单应被路由到的交易所。

```py
abstract get_limit_price(is_buy)
```

获取此订单的限价。返回值为 None 或一个大于等于 0 的数值。

```py
abstract get_stop_price(is_buy)
```

获取此订单的止损价格。返回值为 None 或一个大于等于 0 的数值。

```py
class zipline.finance.execution.MarketOrder(exchange=None)
```

以当前市场价格成交的订单的执行风格。

这是使用`order()`下达的订单的默认设置。

```py
class zipline.finance.execution.LimitOrder(limit_price, asset=None, exchange=None)
```

以等于或优于指定限价的价格成交的订单的执行风格。

参数：

**限价** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 买入的最高价格，或卖出的最低价格，订单应在该价格成交。

```py
class zipline.finance.execution.StopOrder(stop_price, asset=None, exchange=None)
```

以市场价格达到阈值时下达的市场订单的执行风格。

参数：

**止损价格** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 订单应被下达的价格阈值。对于卖出，如果市场价格跌至该值以下，则下达订单。对于买入，如果市场价格升至该值以上，则下达订单。

```py
class zipline.finance.execution.StopLimitOrder(limit_price, stop_price, asset=None, exchange=None)
```

执行风格，表示在市场价格达到阈值时下达的限价订单。

参数：

+   **限价** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 买入的最高价格，或卖出的最低价格，订单应在该价格或更好的价格成交。

+   **止损价格** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 订单应被下达的价格阈值。对于卖出，如果市场价格跌至该值以下，则下达订单。对于买入，如果市场价格升至该值以上，则下达订单。

```py
zipline.api.get_order(self, order_id)
```

根据订单函数返回的订单 ID 查找订单。

参数：

**订单 ID** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 订单的唯一标识符。

返回：

**订单** – 订单对象。

返回类型：

订单

```py
zipline.api.get_open_orders(self, asset=None)
```

检索所有当前的未结订单。

参数：

**资产** (*Asset*) – 如果传递且不为 None，则仅返回给定资产的未结订单，而不是所有未结订单。

返回：

**未结订单** – 如果没有传递资产，这将返回一个字典，将资产映射到包含该资产所有未结订单的列表。如果传递了资产，则这将返回该资产的未结订单列表。

返回类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[[列表](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[Order]] 或 [列表](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[Order]

```py
zipline.api.cancel_order(self, order_param)
```

取消一个未完成的订单。

参数：

**order_param** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* *Order*) – 要取消的订单 ID 或订单对象。

#### 订单取消政策

```py
zipline.api.set_cancel_policy(self, cancel_policy)
```

设置模拟的订单取消政策。

参数：

**cancel_policy** (*CancelPolicy*) – 要使用的取消政策。

另请参阅

`zipline.api.EODCancel`, `zipline.api.NeverCancel`

```py
class zipline.finance.cancel_policy.CancelPolicy
```

抽象的取消政策接口。

```py
abstract should_cancel(event)
```

是否应取消所有未完成的订单？

参数：

**event** (*枚举值*) –

事件类型之一：

+   `zipline.gens.sim_engine.BAR`

+   `zipline.gens.sim_engine.DAY_START`

+   `zipline.gens.sim_engine.DAY_END`

+   `zipline.gens.sim_engine.MINUTE_END`

返回：

**should_cancel** – 是否应取消所有未完成的订单？

返回类型：

[布尔值](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")

```py
zipline.api.EODCancel(warn_on_cancel=True)
```

该政策在一天结束时取消未完成的订单。目前，Zipline 仅将此政策应用于每分钟模拟。

参数：

**warn_on_cancel** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 如果这导致订单被取消，是否应发出警告？

```py
zipline.api.NeverCancel()
```

订单永远不会自动取消。

### 资产

```py
zipline.api.symbol(self, symbol_str, country_code=None)
```

通过股票代码查找股票。

参数：

+   **symbol_str** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要查找的股票的股票代码。

+   **国家代码** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* *None**,* *可选*) – 限制符号搜索的国家。

返回：

**股票** – 在当前符号查找日期持有股票代码的股票。

返回类型：

zipline.assets.Equity

引发：

**SymbolNotFound** – 当符号在当前查找日期未被持有时引发。

另请参阅

`zipline.api.set_symbol_lookup_date()`

```py
zipline.api.symbols(self, *args, **kwargs)
```

查找多个股票作为一个列表。

参数：

+   ***args** (*iterable**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*]*) – 要查找的股票代码。

+   **国家代码** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* *None**,* *可选*) – 限制符号搜索的国家。

返回：

**股票** – 在当前符号查找日期持有给定股票代码的股票。

返回类型：

[list](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[zipline.assets.Equity]

引发：

**SymbolNotFound** – 当在当前查找日期未持有其中一个符号时引发。

另请参阅

`zipline.api.set_symbol_lookup_date()`

```py
zipline.api.future_symbol(self, symbol)
```

查找具有给定符号的期货合约。

参数：

**symbol** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 所需合约的符号。

返回：

**future** – 以`symbol`名称交易的期货。

返回类型：

zipline.assets.Future

引发：

**SymbolNotFound** – 当未找到名为‘symbol’的合约时引发。

```py
zipline.api.set_symbol_lookup_date(self, dt)
```

设置符号将被解析为其资产的日期（符号可能在不同时间映射到不同的公司或底层资产）

参数：

**dt** (*datetime*) – 新的符号查找日期。

```py
zipline.api.sid(self, sid)
```

通过其唯一资产标识符查找资产。

参数：

**sid** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 标识资产的唯一整数。

返回：

**asset** – 具有给定`sid`的资产。

返回类型：

zipline.assets.Asset

引发：

**SidsNotFound** – 当请求的`sid`未映射到任何资产时。

### 交易控制

Zipline 提供交易控制以确保算法按预期执行。这些函数有助于保护算法免受意外行为的不良后果，尤其是在使用真实资金进行交易时。

```py
zipline.api.set_do_not_order_list(self, restricted_list, on_error='fail')
```

设置对哪些资产可以下单的限制。

参数：

**restricted_list** (*container***[*Asset**]**,* *SecurityList*) – 不能下单的资产。

```py
zipline.api.set_long_only(self, on_error='fail')
```

设置规则，指定此算法不能持有空头头寸。

```py
zipline.api.set_max_leverage(self, max_leverage)
```

设置算法最大杠杆的限制。

参数：

**max_leverage** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 算法的最大杠杆。如果未提供，则不会有最大值。

```py
zipline.api.set_max_order_count(self, max_count, on_error='fail')
```

设置单日内可以下达的订单数量的限制。

参数：

**max_count** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 任何单日内可以下达的最大订单数量。

```py
zipline.api.set_max_order_size(self, asset=None, max_shares=None, max_notional=None, on_error='fail')
```

对为 sid 下达的任何单个订单的股票数量和/或美元价值设置限制。限制被视为绝对值，并在算法尝试为 sid 下达订单时执行。

如果算法尝试下达的订单将导致超过这些限制之一，则引发 TradingControlException。

参数：

+   **asset** (*Asset**,* *optional*) – 如果提供，这仅对给定资产的持仓设置守卫。

+   **max_shares** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *optional*) – 一次可以订购的最大股票数量。

+   **max_notional** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 一次可以订购的最大价值。

```py
zipline.api.set_max_position_size(self, asset=None, max_shares=None, max_notional=None, on_error='fail')
```

为给定的 sid 设置持有的股票数量和/或美元价值的限制。这些限制被视为绝对值，并在算法尝试为 sid 下达订单时执行。这意味着由于拆分/股息，可能会持有超过最大数量的股票，并且由于价格改善，可能会持有超过最大名义价值的股票。

如果算法尝试下达的订单会导致持有的股票/美元价值绝对值超过这些限制之一，则会引发 TradingControlException。

参数：

+   **asset** (*Asset**,* *optional*) – 如果提供，则仅对给定资产的持仓设置警卫。

+   **max_shares** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *optional*) – 对于资产持有的最大股票数量。

+   **max_notional** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 对于资产持有的最大价值。

### 模拟参数

```py
zipline.api.set_benchmark(self, benchmark)
```

设置基准资产。

参数：

**benchmark** (*zipline.assets.Asset*) – 设置为新基准的资产。

注释

对于新的基准资产，任何支付的股息都将自动再投资。

#### 佣金模型

```py
zipline.api.set_commission(self, us_equities=None, us_futures=None)
```

设置模拟的佣金模型。

参数：

+   **us_equities** (*EquityCommissionModel*) – 用于交易美国股票的佣金模型。

+   **us_futures** (*FutureCommissionModel*) – 用于交易美国期货的佣金模型。

注释

此函数只能在`initialize()`期间调用。

参见

`zipline.finance.commission.PerShare`，`zipline.finance.commission.PerTrade`，`zipline.finance.commission.PerDollar`

```py
class zipline.finance.commission.CommissionModel
```

佣金模型的抽象基类。

佣金模型负责接受订单/交易对，并计算应向算法的账户收取的每笔交易的佣金金额。

要实现新的佣金模型，请创建`CommissionModel`的子类并实现`calculate()`。

```py
abstract calculate(order, transaction)
```

计算由于`transaction`而对`order`收取的佣金金额。

参数：

+   **order** (*zipline.finance.order.Order*) –

    正在处理的订单。

    `订单` 的 `佣金` 字段是一个浮点数，表示该订单已收取的佣金金额。

+   **transaction** (*zipline.finance.transaction.Transaction*) – 正在处理的交易所。如果单个订单在给定条形图中的交易量不足以填充所请求的全部金额，则可能会产生多个交易所。

返回：

**已收取金额** – 我们应该归因于该订单的额外佣金，以美元计。

返回类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")

```py
class zipline.finance.commission.PerShare(cost=0.001, min_trade_cost=0.0)
```

根据每股的成本计算佣金，可选择每笔交易的最低成本。

参数：

+   **成本** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 每笔股票交易支付的佣金金额。默认是每股十分之一美分。

+   **min_trade_cost** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 每笔交易支付的最低佣金金额。默认没有最低限制。

注意

这是 zipline 对股票的默认佣金模型。

```py
class zipline.finance.commission.PerTrade(cost=0.0)
```

根据每笔交易的成本计算佣金。

对于需要多次填充的订单，全额佣金将收取给第一次填充。

参数：

**成本** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 每笔股票交易支付的佣金固定金额。

```py
class zipline.finance.commission.PerDollar(cost=0.0015)
```

通过应用每美元交易固定成本来计算模型佣金。

参数：

**成本** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 每笔股票交易支付的佣金固定金额。默认是每美元交易收取 $0.0015 的佣金。

#### 滑点模型

```py
zipline.api.set_slippage(self, us_equities=None, us_futures=None)
```

设置模拟的滑点模型。

参数：

+   **us_equities** (*EquitySlippageModel*) – 用于交易美国股票的滑点模型。

+   **us_futures** (*FutureSlippageModel*) – 用于交易美国期货的滑点模型。

注意

此函数只能在 `initialize()` 期间调用。

另请参阅

`zipline.finance.slippage.SlippageModel`

```py
class zipline.finance.slippage.SlippageModel
```

滑点模型的抽象基类。

滑点模型负责模拟期间订单填充的费率和价格。

要实现一个新的滑点模型，创建一个 `SlippageModel` 的子类并实现 `process_order()`。

```py
process_order(data, order)
```

```py
volume_for_bar
```

当前分钟内，对于正在填充的资产，已经完成填充的股票数量。该属性由基类自动维护。如果单个资产有多个开放订单，子类可以使用它来跟踪总填充量。

类型：

[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")

笔记

定义自己的构造函数的子类应在执行其他初始化之前调用`super(<子类名称>, self).__init__()`。

```py
abstract process_order(data, order)
```

计算当前分钟内为 `订单` 成交的股份数量和价格。

参数：

+   **数据** (*zipline.protocol.BarData*) – 给定条形图的数据。

+   **订单** (*zipline.finance.order.Order*) – 要模拟的订单。

返回：

+   **执行价格** (*float*) – 成交的价格。

+   **执行成交量** (*int*) – 应成交的股份数量。必须在`0`和`订单.金额 - 订单.已成交`之间。如果成交的数量少于剩余的数量，`订单`将保持开放状态，并在下一分钟再次传递给此方法。

引发：

**zipline.finance.slippage.LiquidityExceeded** – 如果在当前条形图期间不应再处理当前资产的更多订单，则可能会引发。

笔记

在调用此方法之前，`volume_for_bar` 将设置为当前分钟内已为 `订单.资产` 成交的股份数量。

`process_order()` 在基础类中不会为没有历史成交量的条形图调用。

```py
class zipline.finance.slippage.FixedSlippage(spread=0.0)
```

简单模型假设所有资产的价差固定。

参数：

**价差** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 假设所有资产的价差大小。买入订单将以 `收盘价 + (价差 / 2)` 成交。卖出订单将以 `收盘价 - (价差 / 2)` 成交。

笔记

该模型不对成交规模设置限制。只要在订单资产中发生任何交易活动，无论订单规模是否大于历史成交量，订单都将立即成交。

```py
class zipline.finance.slippage.VolumeShareSlippage(volume_limit=0.025, price_impact=0.1)
```

将滑点建模为历史成交量百分比的二次函数。

买入订单将以以下价格成交：

```py
price * (1 + price_impact * (volume_share ** 2)) 
```

卖出订单将以以下价格成交：

```py
price * (1 - price_impact * (volume_share ** 2)) 
```

其中`价格`是条形图的收盘价，`成交量份额`是每分钟成交量填充的百分比，最多可达`成交量限制`。

参数：

+   **成交量限制** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 每个条形图中可以成交的历史成交量的最大百分比。0.5 表示历史成交量的 50%。1.0 表示 100%。默认值为 0.025（即，2.5%）。

+   **价格影响** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 价格影响的缩放系数。较大的值将导致更多的模拟价格影响。较小的值将导致较少的模拟价格影响。默认值为 0.1。

### 管道

更多信息，请参阅 管道 API

```py
zipline.api.attach_pipeline(self, pipeline, name, chunks=None, eager=True)
```

注册一个管道，以便在每天开始时进行计算。

参数：

+   **pipeline** (*Pipeline*) – 要计算的管道。

+   **name** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 管道的名称。

+   **chunks** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)") *or* *iterator**,* *optional*) – 要计算管道结果的天数。增加此数字将使获取第一个结果的时间更长，但可能会改善模拟的总运行时间。如果传递了迭代器，我们将根据迭代器的值运行分块。默认值为 True。

+   **eager** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *optional*) – 是否在 before_trading_start 之前计算此管道。

返回：

**pipeline** – 返回未更改的附加管道。

返回类型：

Pipeline

另请参阅

`zipline.api.pipeline_output()`

```py
zipline.api.pipeline_output(self, name)
```

获取由名称`name`附加的管道的结果。

参数：

**name** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要从其中获取结果的管道的名称。

返回：

**results** – 包含当前模拟日期请求的管道的结果的数据框。

返回类型：

pd.DataFrame

引发：

**NoSuchPipeline** – 当未注册具有名称 name 的管道时引发。

另请参阅

`zipline.api.attach_pipeline()`, `zipline.pipeline.engine.PipelineEngine.run_pipeline()`

### 杂项

```py
zipline.api.record(self, *args, **kwargs)
```

每天跟踪和记录值。

参数：

****kwargs** – 要记录的名称和值。

注意

这些值将出现在性能数据包和传递给`analyze`的性能数据框中，以及从`run_algorithm()`返回的性能数据框中。

```py
zipline.api.get_environment(self, field='platform')
```

查询执行环境。

参数：

+   **field** (*{'platform'**,* *'arena'**,* *'data_frequency'**,* *'start'**,* *'end'**,*) –

+   **'capital_base'** –

+   **'platform'** –

+   **'*'}** –

+   **meanings** (*要查询的字段。选项包括以下内容*) –

+   **arena** (*-*) – 模拟参数的竞技场。这通常将是`'backtest'`，但某些系统可能使用它来区分实时交易和回测。

+   **data_frequency** (*-*) – data_frequency 告诉算法它是使用每日数据还是分钟数据运行。

+   **start** (*-*) – 模拟的开始日期。

+   **end** (*-*) – 模拟的结束日期。

+   **capital_base** (*-*) – 模拟的起始资本。

+   **-platform** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 代码运行的平台。默认情况下，这将是字符串‘zipline’。这可以让算法知道它们是否在 Quantopian 平台上运行。

+   ***** (*-*) – 返回字典中的所有字段。

返回:

**val** – 查询字段的值。有关更多信息，请参见上文。

返回类型:

任何

引发:

[**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError "(in Python v3.11)") – 当`field`不是有效选项时引发。

```py
zipline.api.fetch_csv(self, url, pre_func=None, post_func=None, date_column='date', date_format=None, timezone='UTC', symbol=None, mask=True, symbol_column=None, special_params_checker=None, country_code=None, **kwargs)
```

从远程 URL 获取 CSV 文件并注册数据，以便可以从`data`对象查询数据。

参数:

+   **url** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要加载的 CSV 文件的 URL。

+   **pre_func** (*callable**[**pd.DataFrame -> pd.DataFrame**]**,* *optional*) – 一个回调函数，允许在日期解析或符号映射之前对从 fetch_csv 返回的原始数据进行预处理。

+   **post_func** (*callable**[**pd.DataFrame -> pd.DataFrame**]**,* *optional*) – 一个回调函数，允许在日期和符号映射后对数据进行后处理。

+   **date_column** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *optional*) – 预处理数据框中包含日期时间信息以映射数据的列的名称。

+   **date_format** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *optional*) – `date_column`中日期的格式。如果未提供，`fetch_csv`将尝试推断格式。有关此字符串格式的信息，请参阅[`pandas.read_csv()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html#pandas.read_csv "(in pandas v2.0.3)")。

+   **timezone** (*tzinfo* *or* [*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *optional*) – `date_column`中日期时间的时区。

+   **symbol** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *optional*) – 如果数据是关于新资产或指数的，则此字符串将用于在`data`中标识值的名称。例如，可以使用`fetch_csv`加载 VIX 的数据，然后此字段可以是字符串`'VIX'`。

+   **mask** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *optional*) – 丢弃任何无法进行符号映射的行。

+   **symbol_column** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 如果数据正在为每个资产附加一些新属性，则此参数是包含符号的预处理数据框中的列的名称。这将连同日期信息一起用于映射资产查找器中的 sids。

+   **country_code** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *optional*) – 用于消除符号查找歧义的国家代码。

+   ****kwargs** – 转发给 [`pandas.read_csv()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html#pandas.read_csv "(在 pandas v2.0.3 中)")。

返回值：

**csv_data_source** – 将从指定 url 拉取数据的请求源。

返回类型：

zipline.sources.requests_csv.PandasRequestsCSV

## Blotters

[blotter](https://www.investopedia.com/terms/b/blotter.asp) 记录了一段时间内的交易及其细节，通常是一个交易日。交易细节包括时间、价格、订单大小以及是买入还是卖出订单等信息。它通常由记录通过数据源进行的交易的贸易软件创建。

```py
class zipline.finance.blotter.blotter.Blotter(cancel_policy=None)
```

```py
batch_order(order_arg_lists)
```

批量下单。

参数：

**order_arg_lists** (*iterable**[*[*tuple*](https://docs.python.org/3/library/stdtypes.html#tuple "(在 Python v3.11 中)")*]*) – 订单期望的参数元组。

返回值：

**order_ids** – 每个已下（或未下）订单的唯一标识符（或 None）。

返回类型：

[列表](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11 中)")[[字符串](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中)") 或 None]

注意

这对于 Blotter 子类来说是必需的，以便能够批量下单，而不是一次只传递一个订单请求。

```py
abstract cancel(order_id, relay_status=True)
```

取消单个订单

参数：

+   **order_id** ([*int*](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11 中)")) – 订单的 id

+   **relay_status** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(在 Python v3.11 中)")) – 是否记录订单状态

```py
abstract cancel_all_orders_for_asset(asset, warn=False, relay_status=True)
```

取消给定资产的所有未结订单。

```py
abstract get_transactions(bar_data)
```

根据当前未结订单、滑点模型和佣金模型创建交易列表。

参数：

**bar_data** (*zipline._protocol.BarData*) –

注意

该方法记录了 blotter 的 open_orders 字典，以便

在我们处理完所有未结订单后，它能够准确无误。

返回值：

+   **transactions_list** (*List*) – transactions_list: 由当前未结订单产生的交易列表。如果没有未结订单，则返回空列表。

+   **commissions_list** (*List*) – commissions_list: 由填充未结订单产生的佣金列表。佣金是一个具有“资产”和“成本”参数的对象。

+   **closed_orders** (*List*) – closed_orders: 已填充的所有订单列表。

```py
abstract hold(order_id, reason='')
```

将具有 order_id 的订单标记为‘held’。Held 在功能上类似于‘open’。当填充（全部或部分）到达时，状态将自动变回 open/filled，视情况而定。

```py
abstract order(asset, amount, style, order_id=None)
```

下单。

参数：

+   **asset** (*zipline.assets.Asset*) – 该订单对应的资产。

+   **金额** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 要订购的股票数量。如果 `金额` 为正数，这是要购买或平仓的股票数量。如果 `金额` 为负数，这是要卖出或做空的股票数量。

+   **样式** (*zipline.finance.execution.ExecutionStyle*) – 订单的执行样式。

+   **订单 ID** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 此订单的唯一标识符。

返回：

**订单 ID** – 此订单的唯一标识符，如果没有下订单，则为 None。

返回类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") 或 None

注意

金额 > 0：买入/平仓 金额 < 0：卖出/做空 市价单：订单（资产，金额） 限价单：订单（资产，金额，样式=限价订单（限价）） 止损单：订单（资产，金额，样式=止损订单（止损价）） 止损限价单：订单（资产，金额，样式=止损限价订单（限价，止损价））

```py
abstract process_splits(splits)
```

通过修改任何未结订单来处理拆分列表。

参数：

**拆分** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")) – 拆分列表。每个拆分都是一个包含（资产，比率）的元组。

返回类型：

None

```py
abstract prune_orders(closed_orders)
```

从交易记录的未结订单列表中删除所有给定订单。

参数：

**已关闭订单** (*已关闭订单的可迭代对象*) –

返回类型：

None

```py
abstract reject(order_id, reason='')
```

将给定订单标记为‘拒绝’，其功能类似于取消。区别在于拒绝是强制性的（通常包括经纪人指示订单被拒绝原因的消息），而取消通常是用户驱动的。

```py
class zipline.finance.blotter.SimulationBlotter(equity_slippage=None, future_slippage=None, equity_commission=None, future_commission=None, cancel_policy=None)
```

```py
cancel(order_id, relay_status=True)
```

取消单个订单

参数：

+   **订单 ID** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 订单的 ID

+   **relay_status** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 是否记录订单状态

```py
cancel_all_orders_for_asset(asset, warn=False, relay_status=True)
```

取消给定资产的所有未结订单。

```py
get_transactions(bar_data)
```

根据当前的未结订单、滑点模型和佣金模型创建交易列表。

参数：

**bar_data** (*zipline._protocol.BarData*) –

注意

此方法记录交易记录的未结订单字典，以便

在我们处理完未结订单时，它是准确的。

返回：

+   **交易列表** (*List*) – 交易列表：由当前未结订单产生的交易列表。如果没有未结订单，则返回空列表。

+   **佣金列表** (*List*) – 佣金列表：由填充未结订单产生的佣金列表。佣金是一个具有“资产”和“成本”参数的对象。

+   **已关闭订单** (*List*) – 已关闭订单：已填充的所有订单的列表。

```py
hold(order_id, reason='')
```

将具有 order_id 的订单标记为‘held’。Held 功能上类似于‘open’。当填充（全部或部分）到达时，状态将自动变回 open/filled，必要时。

```py
order(asset, amount, style, order_id=None)
```

下订单。

参数：

+   **asset** (*zipline.assets.Asset*) – 该订单对应的资产。

+   **amount** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 要订购的股票数量。如果`amount`为正数，这是要购买或覆盖的股票数量。如果`amount`为负数，这是要出售或做空的股票数量。

+   **style** (*zipline.finance.execution.ExecutionStyle*) – 订单的执行风格。

+   **order_id** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *optional*) – 该订单的唯一标识符。

返回：

**order_id** – 该订单的唯一标识符，如果没有下订单，则为 None。

返回类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")或 None

笔记

amount > 0 :: 买入/覆盖 金额 < 0 :: 卖出/做空 市价单：order(asset, amount) 限价单：order(asset, amount, style=LimitOrder(limit_price)) 止损单：order(asset, amount, style=StopOrder(stop_price)) 止损限价单：order(asset, amount, style=StopLimitOrder(limit_price, stop_price))

```py
process_splits(splits)
```

处理一系列拆分，根据需要修改任何未完成订单。

参数：

**splits** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")) – 拆分列表。每个拆分是一个(资产, 比率)的元组。

返回类型：

无

```py
prune_orders(closed_orders)
```

从 blotter 的 open_orders 列表中删除所有给定订单。

参数：

**closed_orders** (*iterable* *of* *已关闭的订单*) –

返回类型：

无

```py
reject(order_id, reason='')
```

将给定订单标记为‘rejected’，其功能类似于取消。区别在于，拒绝是非自愿的（通常包含经纪人指示订单被拒绝原因的消息），而取消通常是用户驱动的。

## 管道 API

`Pipeline`通过在回测期间优化因子的计算，实现了更快速和更节省内存的执行。

```py
class zipline.pipeline.Pipeline(columns=None, screen=None, domain=GENERIC)
```

管道对象表示一组要由管道引擎编译和执行的命名表达式。

管道有两个重要属性：‘columns’，一个命名`Term`实例的字典，和‘screen’，一个`Filter`，表示将资产包含在管道结果中的标准。

要在 TradingAlgorithm 的上下文中计算管道，用户必须在`initialize`函数中调用`attach_pipeline`来注册该管道应在每个交易日进行计算。可以通过从`handle_data`、`before_trading_start`或计划函数调用`pipeline_output`来检索附加管道的最新输出。

参数：

+   **列**（[*dict*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")*,* 可选） – 初始列。

+   **屏幕**（*zipline.pipeline.Filter**,* 可选） – 初始屏幕。

```py
add(term, name, overwrite=False)
```

添加一列。

计算`term`的结果将作为一列显示在运行此管道生成的 DataFrame 中。

参数：

+   **列**（*zipline.pipeline.Term*） – 要添加到管道中的过滤器、因子或分类器。

+   **名称**（[*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要添加的列的名称。

+   **覆盖**（[*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 如果已经有一个名为 name 的列，是否覆盖现有条目。

```py
domain(default)
```

获取此管道的域。

+   如果在构造时提供了显式域，则使用它。

+   否则，从已注册的列中推断出一个域。

+   如果无法推断出域，则返回`默认`。

参数：

**默认**（*zipline.pipeline.domain.Domain*） – 如果无法通过此管道本身推断出域，则使用的域。

返回：

**域** – 管道的域。

返回类型：

zipline.pipeline.domain.Domain

引发：

+   **AmbiguousDomain** –

+   [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError "(in Python v3.11)") – 如果`self`中的项与 self._domain 冲突。

```py
remove(name)
```

移除一列。

参数：

**名称**（[*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要移除的列的名称。

引发：

[**KeyError**](https://docs.python.org/3/library/exceptions.html#KeyError "(in Python v3.11)") – 如果名称不在 self.columns 中。

返回：

**已移除** – 已移除的项。

返回类型：

zipline.pipeline.Term

```py
set_screen(screen, overwrite=False)
```

在此 Pipeline 上设置一个屏幕。

参数：

+   **过滤器**（*zipline.pipeline.Filter*） – 要作为屏幕应用的过滤器。

+   **覆盖**（[*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 是否覆盖任何现有的屏幕。如果覆盖为 False 且 self.screen 不为 None，我们将引发错误。

```py
show_graph(format='svg')
```

将此 Pipeline 渲染为 DAG。

参数：

**格式**（{'svg'**,* 'png'**,* 'jpeg'}） – 要渲染的图像格式。默认值为‘svg’。

```py
to_execution_plan(domain, default_screen, start_date, end_date)
```

编译为 ExecutionPlan。

参数：

+   **域**（*zipline.pipeline.domain.Domain*） – 管道将在其上执行的域。

+   **default_screen** (*zipline.pipeline.Term*) – 如果 self.screen 为 None，则使用作为筛选条件的项。

+   **all_dates** (*pd.DatetimeIndex*) – 用于计算每个项的起始和结束的日期日历。

+   **start_date** (*pd.Timestamp*) – 所需输出的第一个日期。

+   **end_date** (*pd.Timestamp*) – 所需输出的最后一个日期。

返回：

**graph** – 编码项依赖关系的图，包括有关额外行要求的元数据。

返回类型：

zipline.pipeline.graph.ExecutionPlan

```py
to_simple_graph(default_screen)
```

编译成一个没有额外行元数据的简单 TermGraph。

参数：

**default_screen** (*zipline.pipeline.Term*) – 如果 self.screen 为 None，则使用作为筛选条件的项。

返回：

**graph** – 编码项依赖关系的图。

返回类型：

zipline.pipeline.graph.TermGraph

```py
property columns
```

此管道的输出列。

返回：

**columns** – 从列名到计算该列输出的表达式的映射。

返回类型：

[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)"), zipline.pipeline.ComputableTerm]

```py
property screen
```

此管道的筛选条件。

返回：

**screen** – 定义此管道筛选条件的项。如果 `screen` 是一个筛选器，则不通过筛选器的行（即，对于该行，筛选器计算结果为 `False`）将从该管道的输出中删除，然后再返回结果。

返回类型：

zipline.pipeline.Filter 或 None

注意

在 Pipeline 上设置筛选条件不会改变任何行的值：它只影响是否返回给定行。使用筛选条件计算管道的逻辑等效于不使用筛选条件计算管道，然后作为后处理步骤，过滤掉任何计算结果为 `False` 的行。

```py
class zipline.pipeline.CustomFactor(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

用户定义因子的基类。

参数：

+   **inputs** (*iterable**,* *可选*) – BoundColumn 实例的可迭代对象（例如 USEquityPricing.close），描述要加载并传递给 self.compute 的数据。如果未将此参数传递给 CustomFactor 构造函数，我们将查找名为 inputs 的类级属性。

+   **outputs** (*iterable**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*]**,* *可选*) – 表示此因子应计算并返回的每个输出的名称的字符串的可迭代对象。如果未将此参数传递给 CustomFactor 构造函数，我们将查找名为 outputs 的类级属性。

+   **window_length** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 每个输入要传递的行数。如果未将此参数传递给 CustomFactor 构造函数，我们将查找名为 window_length 的类级属性。

+   **mask**（*zipline.pipeline.Filter*，*可选*）– 一个过滤器，描述我们应该在哪些资产上每天进行计算。每次调用`CustomFactor.compute`将只接收在调用`compute`的日期上`mask`产生 True 的资产。

笔记

实现自己的因子的用户应该继承 CustomFactor 并实现一个名为 compute 的方法，其签名如下：

```py
def compute(self, today, assets, out, *inputs):
   ... 
```

在每个模拟日期，`compute`将被调用，传递当前日期、一个 sid 数组、一个输出数组以及一个输入数组，每个表达式作为输入传递给 CustomFactor 构造函数。

传递给`compute`的值的具体类型如下：

```py
today : np.datetime64[ns]
    Row label for the last row of all arrays passed as `inputs`.
assets : np.array[int64, ndim=1]
    Column labels for `out` and`inputs`.
out : np.array[self.dtype, ndim=1]
    Output array of the same shape as `assets`.  `compute` should write
    its desired return values into `out`. If multiple outputs are
    specified, `compute` should write its desired return values into
    `out.<output_name>` for each output name in `self.outputs`.
*inputs : tuple of np.array
    Raw data arrays corresponding to the values of `self.inputs`. 
```

`compute`函数应该预期会传递 NaN 值，这些值代表在某个资产没有可用数据的日期。这可能包括资产尚未存在的日期。

例如，如果一个 CustomFactor 需要 10 行收盘价数据，而资产 A 从 2014 年 6 月 2 日星期一开始交易，那么在 2014 年 6 月 3 日星期二，资产 A 的输入数据列将会有 9 个领先的 NaN 值，因为这些日期的数据尚未可用。

示例

具有预先声明默认值的 CustomFactor：

```py
class TenDayRange(CustomFactor):
  """
 Computes the difference between the highest high in the last 10
 days and the lowest low.

 Pre-declares high and low as default inputs and `window_length` as
 10.
 """

    inputs = [USEquityPricing.high, USEquityPricing.low]
    window_length = 10

    def compute(self, today, assets, out, highs, lows):
        from numpy import nanmin, nanmax

        highest_highs = nanmax(highs, axis=0)
        lowest_lows = nanmin(lows, axis=0)
        out[:] = highest_highs - lowest_lows

# Doesn't require passing inputs or window_length because they're
# pre-declared as defaults for the TenDayRange class.
ten_day_range = TenDayRange() 
```

没有默认值的 CustomFactor：

```py
class MedianValue(CustomFactor):
  """
 Computes the median value of an arbitrary single input over an
 arbitrary window..

 Does not declare any defaults, so values for `window_length` and
 `inputs` must be passed explicitly on every construction.
 """

    def compute(self, today, assets, out, data):
        from numpy import nanmedian
        out[:] = data.nanmedian(data, axis=0)

# Values for `inputs` and `window_length` must be passed explicitly to
# MedianValue.
median_close10 = MedianValue([USEquityPricing.close], window_length=10)
median_low15 = MedianValue([USEquityPricing.low], window_length=15) 
```

具有多个输出的 CustomFactor：

```py
class MultipleOutputs(CustomFactor):
    inputs = [USEquityPricing.close]
    outputs = ['alpha', 'beta']
    window_length = N

    def compute(self, today, assets, out, close):
        computed_alpha, computed_beta = some_function(close)
        out.alpha[:] = computed_alpha
        out.beta[:] = computed_beta

# Each output is returned as its own Factor upon instantiation.
alpha, beta = MultipleOutputs()

# Equivalently, we can create a single factor instance and access each
# output as an attribute of that instance.
multiple_outputs = MultipleOutputs()
alpha = multiple_outputs.alpha
beta = multiple_outputs.beta 
```

注意：如果一个 CustomFactor 有多个输出，所有输出必须具有相同的 dtype。例如，在上面的例子中，如果 alpha 是浮点数，那么 beta 也必须是浮点数。

```py
dtype = dtype('float64')
```

```py
class zipline.pipeline.Filter(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), domain=sentinel('NotSpecified'), *args, **kwargs)
```

计算布尔输出的管道表达式。

过滤器最常用于描述要包含或排除的资产集合，以用于特定目的。许多 Pipeline API 函数接受一个`mask`参数，该参数可以提供一个过滤器，指示只有通过过滤器的值才应被考虑用于请求的计算。例如，`zipline.pipeline.Factor.top()`接受一个掩码，指示只应在通过指定过滤器的资产上计算排名。

构建过滤器最常见的方法之一是通过比较运算符（`<`，`<=`，`!=`，`eq`，`>`，`>=`）之一。例如，一个自然的方式来构建一个过滤器，对于 10 天加权平均价格小于$20.0 的股票，首先构建一个计算 10 天加权平均价格的因子，然后将其与标量值 20.0 进行比较：

```py
>>> from zipline.pipeline.factors import VWAP
>>> vwap_10 = VWAP(window_length=10)
>>> vwaps_under_20 = (vwap_10 <= 20) 
```

过滤器也可以通过两个因子之间的比较来构造。例如，要构造一个过滤器，对于资产/日期对，其中资产的 10 天加权平均价格大于其 30 天加权平均价格，则产生 True：

```py
>>> short_vwap = VWAP(window_length=10)
>>> long_vwap = VWAP(window_length=30)
>>> higher_short_vwap = (short_vwap > long_vwap) 
```

过滤器可以通过`&`（与）和`|`（或）运算符组合。

`&`两个过滤器组合产生一个新的过滤器，如果**两个**输入都产生 True，则新过滤器产生 True。

`|`两个过滤器组合产生一个新的过滤器，如果**任何一个**输入产生 True，则新过滤器产生 True。

`~`运算符可用于反转过滤器，将所有 True 值与 Falses 互换。

过滤器可以作为`screen`属性设置在管道中，指示应排除过滤器产生 False 的资产/日期对。这既有助于减少管道输出的噪声，也有助于减少管道结果的内存消耗。

```py
__and__(other)
```

二进制运算符：‘&’

```py
__or__(other)
```

二进制运算符：‘|’

```py
if_else(if_true, if_false)
```

创建一个从两个选择中选择值的项。

参数：

+   **if_true** (*zipline.pipeline.term.ComputableTerm*) – 在过滤器输出 True 的位置应使用的表达式的值。

+   **if_false** (*zipline.pipeline.term.ComputableTerm*) – 在过滤器输出 False 的位置应使用的表达式的值。

返回值：

**merged** – 一个项，根据`self`产生的值从`if_true`或`if_false`中取值进行计算。

返回的项在`self`产生 True 的位置从``if_true``取值，在`self`产生 False 的位置从`if_false`取值。

返回类型：

zipline.pipeline.term.ComputableTerm

示例

设`f`为产生以下输出的因子：

```py
 AAPL   MSFT    MCD     BK
2017-03-13    1.0    2.0    3.0    4.0
2017-03-14    5.0    6.0    7.0    8.0 
```

设`g`为另一个产生以下输出的因子：

```py
 AAPL   MSFT    MCD     BK
2017-03-13   10.0   20.0   30.0   40.0
2017-03-14   50.0   60.0   70.0   80.0 
```

最后，设`condition`为产生以下输出的过滤器：

```py
 AAPL   MSFT    MCD     BK
2017-03-13   True  False   True  False
2017-03-14   True   True  False  False 
```

那么，表达式`condition.if_else(f, g)`产生以下输出：

```py
 AAPL   MSFT    MCD     BK
2017-03-13    1.0   20.0    3.0   40.0
2017-03-14    5.0    6.0   70.0   80.0 
```

另请参阅

[`numpy.where`](https://numpy.org/doc/stable/reference/generated/numpy.where.html#numpy.where "(in NumPy v1.25)")， `Factor.fillna`

```py
class zipline.pipeline.Factor(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), domain=sentinel('NotSpecified'), *args, **kwargs)
```

管道 API 表达式，产生数值或日期值输出。

因子是最常用的管道项，代表任何产生数值结果的计算结果。

因子可以通过任何内置数学运算符（`+`，`-`，`*`等）与其他因子以及标量值组合。

这使得编写结合多个因子的复杂表达式变得容易。例如，构建一个计算两个其他因子平均值的因子非常简单：

```py
>>> f1 = SomeFactor(...)  
>>> f2 = SomeOtherFactor(...)  
>>> average = (f1 + f2) / 2.0 
```

因子还可以通过比较运算符转换为`zipline.pipeline.Filter`对象：（`<`，`<=`，`!=`，`eq`，`>`，`>=`）。

除了基本的数值运算符外，因子还定义了许多自然运算符。这些包括识别缺失或极端值输出的方法（`isnull()`，`notnull()`，`isnan()`，`notnan()`），输出归一化的方法（`rank()`，`demean()`，`zscore()`），以及基于结果的秩次序属性构建过滤器的方法（`top()`，`bottom()`，`percentile_between()`）。

```py
eq(other)
```

构建一个`Filter`，计算`self == other`。

参数：

**其他**（*zipline.pipeline.Factor*，[*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")） – 表达式的右侧。

返回：

**filter** – 过滤器，计算`self == other`，使用`self`和`other`的输出。

返回类型：

zipline.pipeline.Filter

```py
demean(mask=sentinel('NotSpecified'), groupby=sentinel('NotSpecified'))
```

构建一个因子，计算`self`并从结果的每一行中减去均值。

如果提供了`mask`，则在计算行均值时忽略`mask`返回 False 的值，并在`mask`为 False 的任何地方输出 NaN。

如果提供了`groupby`，则根据`groupby`产生的值对每一行进行分区，去均值分区数组，并将子结果重新组合。

参数：

+   **mask**（*zipline.pipeline.Filter*，*可选*） – 一个过滤器，定义了计算均值时忽略的值。

+   **groupby**（*zipline.pipeline.Classifier*，*可选*） – 一个分类器，定义了计算均值的分区。

示例

设`f`为一个因子，将产生以下输出：

```py
 AAPL   MSFT    MCD     BK
2017-03-13    1.0    2.0    3.0    4.0
2017-03-14    1.5    2.5    3.5    1.0
2017-03-15    2.0    3.0    4.0    1.5
2017-03-16    2.5    3.5    1.0    2.0 
```

设`c`为一个分类器，产生以下输出：

```py
 AAPL   MSFT    MCD     BK
2017-03-13      1      1      2      2
2017-03-14      1      1      2      2
2017-03-15      1      1      2      2
2017-03-16      1      1      2      2 
```

设`m`为一个过滤器，产生以下输出：

```py
 AAPL   MSFT    MCD     BK
2017-03-13  False   True   True   True
2017-03-14   True  False   True   True
2017-03-15   True   True  False   True
2017-03-16   True   True   True  False 
```

那么`f.demean()`将从`f`产生的每一行中减去均值。

```py
 AAPL   MSFT    MCD     BK
2017-03-13 -1.500 -0.500  0.500  1.500
2017-03-14 -0.625  0.375  1.375 -1.125
2017-03-15 -0.625  0.375  1.375 -1.125
2017-03-16  0.250  1.250 -1.250 -0.250 
```

`f.demean(mask=m)`将从每一行中减去均值，但均值计算将忽略对角线上的值，并在输出中将对角线上的值写为 NaN。对角线上的值被忽略，因为它们是`m`产生 False 的位置。

```py
 AAPL   MSFT    MCD     BK
2017-03-13    NaN -1.000  0.000  1.000
2017-03-14 -0.500    NaN  1.500 -1.000
2017-03-15 -0.166  0.833    NaN -0.666
2017-03-16  0.166  1.166 -1.333    NaN 
```

`f.demean(groupby=c)`将从 AAPL/MSFT 和 MCD/BK 的相应条目中减去它们的组均值。AAPL/MSFT 被分组在一起，因为这两个资产在分类器`c`的输出中总是产生 1。同样，MCD/BK 被分组在一起，因为它们总是产生 2。

```py
 AAPL   MSFT    MCD     BK
2017-03-13 -0.500  0.500 -0.500  0.500
2017-03-14 -0.500  0.500  1.250 -1.250
2017-03-15 -0.500  0.500  1.250 -1.250
2017-03-16 -0.500  0.500 -0.500  0.500 
```

`f.demean(mask=m, groupby=c)` 也会减去 AAPL/MSFT 和 MCD/BK 的组均值，但计算均值时会忽略对角线上的值，并在输出中将对角线上的值写为 NaN。

```py
 AAPL   MSFT    MCD     BK
2017-03-13    NaN  0.000 -0.500  0.500
2017-03-14  0.000    NaN  1.250 -1.250
2017-03-15 -0.500  0.500    NaN  0.000
2017-03-16 -0.500  0.500  0.000    NaN 
```

注意

均值对异常值的大小很敏感。在处理可能产生较大异常值的因素时，使用`mask`参数来排除分布极端的值通常很有用：

```py
>>> base = MyFactor(...)  
>>> normalized = base.demean(
...     mask=base.percentile_between(1, 99),
... ) 
```

`demean()` 仅支持 dtype 为 float64 的因素。

另请参阅

[`pandas.DataFrame.groupby()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.groupby.html#pandas.DataFrame.groupby "(in pandas v2.0.3)")

```py
zscore(mask=sentinel('NotSpecified'), groupby=sentinel('NotSpecified'))
```

构建一个对每天的结果进行 Z 分数标准化的因素。

行的 Z 分数定义为：

```py
(row - row.mean()) / row.stddev() 
```

如果提供了`mask`，则在计算行均值和标准差时忽略`mask`返回 False 的值，并在`mask`为 False 的任何地方输出 NaN。

如果提供了`groupby`，则根据`groupby`生成的值对每行进行分区，对分区数组进行 z 分数标准化，并将子结果重新组合起来。

参数：

+   **mask**（*zipline.pipeline.Filter**,* *可选*) – 定义在计算 Z 分数时要忽略的值的过滤器。

+   **groupby**（*zipline.pipeline.Classifier**,* *可选*) – 定义用于计算 Z 分数的分区的分类器。

返回：

**zscored** – 一个对自身输出进行 Z 分数标准化的因素。

返回类型：

zipline.pipeline.Factor

注意

均值和标准差对异常值的大小很敏感。在处理可能产生较大异常值的因素时，使用`mask`参数来排除分布极端的值通常很有用：

```py
>>> base = MyFactor(...)  
>>> normalized = base.zscore(
...    mask=base.percentile_between(1, 99),
... ) 
```

`zscore()` 仅支持 dtype 为 float64 的因素。

示例

请参阅`demean()` 以获取关于`mask`和`groupby`的语义的深入示例。

另请参阅

[`pandas.DataFrame.groupby()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.groupby.html#pandas.DataFrame.groupby "(in pandas v2.0.3)")

```py
rank(method='ordinal', ascending=True, mask=sentinel('NotSpecified'), groupby=sentinel('NotSpecified'))
```

构建一个新的因素，表示每行内各列的排序排名。

参数：

+   **方法**（[*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *{'ordinal'**,* *'min'**,* *'max'**,* *'dense'**,* *'average'}*) – 用于给相同元素分配排名的方法。请参阅 scipy.stats.rankdata 以获取每种排名方法的完整描述。默认值为‘ordinal’。

+   **升序**（[*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 是否以升序或降序返回排序后的排名。默认值为 True。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 表示在计算排名时要考虑的资产的过滤器。如果提供了掩码，则在计算排名时忽略掩码产生 False 值的任何资产/日期对。

+   **groupby** (*zipline.pipeline.Classifier**,* *可选*) – 定义排序的分类器。

返回：

**排名** – 将计算由 self 生成的数据排名的新的因子。

返回类型：

zipline.pipeline.Factor

注意

方法的默认值与 scipy.stats.rankdata 的默认值不同。请参阅该函数的文档以获取方法的有效输入的完整描述。

在给定日期的缺失或不存在数据将导致资产在该日获得 NaN 排名。

另请参阅

[`scipy.stats.rankdata()`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.rankdata.html#scipy.stats.rankdata "(in SciPy v1.11.1)")

```py
pearsonr(target, correlation_length, mask=sentinel('NotSpecified'))
```

构造一个新的因子，计算`目标`与`self`的列之间的滚动皮尔逊相关系数。

参数：

+   **目标** (*zipline.pipeline.Term*) – 用于计算与 self 生成的每个数据列的相关性的术语。这可以是因子、BoundColumn 或切片。如果目标为二维，则按资产计算相关性。

+   **相关长度** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 计算每个相关系数的回溯窗口的长度。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 描述每天应计算与目标切片相关性的资产的过滤器。

返回：

**相关性** – 将计算`目标`与`self`的列之间的相关性的新因子。

返回类型：

zipline.pipeline.Factor

注意

此方法只能在对作为窗口化`Factor`对象输入安全的表达式上调用。此类表达式的示例包括`BoundColumn` `Returns`以及从`rank()`或`zscore()`创建的任何因子。

示例

假设我们想要创建一个因子，计算 AAPL 的 10 天回报与所有其他资产的 10 天回报之间的相关性，每个相关性计算超过 30 天。这可以通过以下方式实现：

```py
returns = Returns(window_length=10)
returns_slice = returns[sid(24)]
aapl_correlations = returns.pearsonr(
    target=returns_slice, correlation_length=30,
) 
```

这等效于执行：

```py
aapl_correlations = RollingPearsonOfReturns(
    target=sid(24), returns_length=10, correlation_length=30,
) 
```

另请参阅

[`scipy.stats.pearsonr()`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.pearsonr.html#scipy.stats.pearsonr "(in SciPy v1.11.1)"), `zipline.pipeline.factors.RollingPearsonOfReturns`, `Factor.spearmanr()`

```py
spearmanr(target, correlation_length, mask=sentinel('NotSpecified'))
```

构建一个新的因子，计算`target`与`self`列之间的滚动 spearman 等级相关系数。

参数：

+   **target** (*zipline.pipeline.Term*) – 用于计算与`self`产生的每个数据列相关性的术语。这可能是一个因子、一个 BoundColumn 或一个切片。如果目标是一个二维的，相关性是按资产计算的。

+   **correlation_length** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 计算每个相关系数的回溯窗口长度。

+   **mask** (*zipline.pipeline.Filter**,* *optional*) – 一个 Filter，描述了哪些资产应该每天计算其与目标切片的相关性。

返回：

**correlations** – 一个新的因子，将计算`target`与`self`列之间的相关性。

返回类型：

zipline.pipeline.Factor

注意

此方法仅能用于被认为是安全的、可作为窗口化`Factor`对象输入的表达式。此类表达式的例子包括`BoundColumn` `Returns`以及由`rank()`或`zscore()`创建的任何因子。

示例

假设我们想要创建一个因子，计算 AAPL 的 10 天回报率与所有其他资产的 10 天回报率之间的相关性，每个相关性计算周期为 30 天。这可以通过以下步骤实现：

```py
returns = Returns(window_length=10)
returns_slice = returns[sid(24)]
aapl_correlations = returns.spearmanr(
    target=returns_slice, correlation_length=30,
) 
```

这相当于执行以下操作：

```py
aapl_correlations = RollingSpearmanOfReturns(
    target=sid(24), returns_length=10, correlation_length=30,
) 
```

另请参阅

[`scipy.stats.spearmanr()`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.spearmanr.html#scipy.stats.spearmanr "(in SciPy v1.11.1)"), `Factor.pearsonr()`

```py
linear_regression(target, regression_length, mask=sentinel('NotSpecified'))
```

构建一个新的因子，执行从目标预测`self`列的普通最小二乘回归。

参数：

+   **target** (*zipline.pipeline.Term*) – 在每个回归中用作预测器/自变量的术语。这可能是一个因子、一个 BoundColumn 或一个切片。如果目标是一个二维的，回归是按资产计算的。

+   **regression_length** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 用于计算每个回归的回溯窗口长度。

+   **mask** (*zipline.pipeline.Filter**,* *可选*) – 描述每天应与目标切片进行回归的资产的过滤器。

返回：

**regressions** – 一个新因子，将计算目标与自身列的线性回归。

返回类型：

zipline.pipeline.Factor

注意

此方法只能在对作为窗口化`Factor`对象输入使用的表达式被认为是安全的情况下调用。此类表达式的例子包括`BoundColumn` `Returns`以及任何由`rank()`或`zscore()`创建的因子。

示例

假设我们想要创建一个因子，该因子将 AAPL 的 10 天回报率与所有其他资产的 10 天回报率进行回归，每个回归计算周期为 30 天。这可以通过以下步骤实现：

```py
returns = Returns(window_length=10)
returns_slice = returns[sid(24)]
aapl_regressions = returns.linear_regression(
    target=returns_slice, regression_length=30,
) 
```

这等效于执行以下操作：

```py
aapl_regressions = RollingLinearRegressionOfReturns(
    target=sid(24), returns_length=10, regression_length=30,
) 
```

另请参阅

[`scipy.stats.linregress()`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.linregress.html#scipy.stats.linregress "(in SciPy v1.11.1)")

```py
winsorize(min_percentile, max_percentile, mask=sentinel('NotSpecified'), groupby=sentinel('NotSpecified'))
```

构建一个新的因子，该因子对由此因子得到的结果进行截尾。

截尾改变排名低于最小百分位的值为最小百分位的值。同样，排名高于最大百分位的值被改变为最大百分位的值。

截尾对于限制极端数据点的影响而不完全移除这些点是有用的。

如果提供了`mask`，则在计算百分位数截止点时忽略`mask`返回 False 的值，并在`mask`为 False 的任何地方输出 NaN。

如果提供了`groupby`，则将截尾分别应用于由`groupby`定义的每个组。

参数：

+   **min_percentile** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* [*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 值位于或低于此百分位的条目将被替换为第(len(input) * min_percentile)个最低值。如果不应剪辑低值，请使用 0。

+   **max_percentile** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* [*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 值位于或高于此百分位的条目将被替换为第(len(input) * max_percentile)个最低值。如果不应剪辑高值，请使用 1。

+   **mask** (*zipline.pipeline.Filter**,* *optional*) – 定义在截尾时要忽略的值的过滤器。

+   **groupby** (*zipline.pipeline.Classifier**,* *optional*) – 定义截尾分区的分类器。

返回：

**winsorized** – 一个因子，产生一个经过截尾处理的自我版本。

返回类型：

zipline.pipeline.Factor

示例

```py
price = USEquityPricing.close.latest
columns={
    'PRICE': price,
    'WINSOR_1: price.winsorize(
        min_percentile=0.25, max_percentile=0.75
    ),
    'WINSOR_2': price.winsorize(
        min_percentile=0.50, max_percentile=1.0
    ),
    'WINSOR_3': price.winsorize(
        min_percentile=0.0, max_percentile=0.5
    ),

} 
```

给定一个具有上述定义的列的管道，对于给定的一天，结果可能看起来像：

```py
 'PRICE' 'WINSOR_1' 'WINSOR_2' 'WINSOR_3'
Asset_1    1        2          4          3
Asset_2    2        2          4          3
Asset_3    3        3          4          3
Asset_4    4        4          4          4
Asset_5    5        5          5          4
Asset_6    6        5          5          4 
```

另请参阅

[`scipy.stats.mstats.winsorize()`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.mstats.winsorize.html#scipy.stats.mstats.winsorize "(in SciPy v1.11.1)"), [`pandas.DataFrame.groupby()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.groupby.html#pandas.DataFrame.groupby "(in pandas v2.0.3)")

```py
quantiles(bins, mask=sentinel('NotSpecified'))
```

构建一个计算`self`输出分位数的分类器。

对于每个非 NaN 数据点，输出都标有一个从 0 到（bins - 1）的整数值。NaN 数据点标有-1。

如果提供了`mask`，则在`mask`产生 False 的位置忽略数据点，并在这些位置发出-1 的标签。

参数：

+   **bins** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 要计算的标签的箱数。

+   **mask** (*zipline.pipeline.Filter**,* *optional*) – 计算分位数时忽略的值的掩码。

返回：

**分位数** – 一个分类器，产生从 0 到（bins - 1）的整数标签。

返回类型：

zipline.pipeline.Classifier

```py
quartiles(mask=sentinel('NotSpecified'))
```

构建一个在`self`输出上计算四分位数的分类器。

对于每个非 NaN 数据点，输出都标有一个值，分别为 0、1、2 或 3，对应于每行中的第一、第二、第三或第四四分位数。NaN 数据点标有-1。

如果提供了`mask`，则在`mask`产生 False 的位置忽略数据点，并在这些位置发出-1 的标签。

参数：

**mask** (*zipline.pipeline.Filter**,* *optional*) – 计算四分位数时忽略的值的掩码。

返回：

**四分位数** – 一个分类器，产生从 0 到 3 的整数标签。

返回类型：

zipline.pipeline.Classifier

```py
quintiles(mask=sentinel('NotSpecified'))
```

构建一个在`self`上计算五分位数标签的分类器。

对于每个非 NaN 数据点，输出都标有一个值，分别为 0、1、2 或 3、4，对应于每行中的五分位数。NaN 数据点标有-1。

如果提供了`mask`，则在`mask`产生 False 的位置忽略数据点，并在这些位置发出-1 的标签。

参数：

**mask** (*zipline.pipeline.Filter**,* *optional*) – 计算五分位数时忽略的值的掩码。

返回：

**五分位数** – 一个分类器，产生从 0 到 4 的整数标签。

返回类型：

zipline.pipeline.Classifier

```py
deciles(mask=sentinel('NotSpecified'))
```

构造一个分类器，计算`self`的十分位标签。

输出中的每个非 NaN 数据点都标有一个从 0 到 9 的值，对应于每行的十分位数。NaN 数据点标记为-1。

如果提供了`mask`，则在`mask`产生 False 的位置忽略数据点，并在这些位置发出-1 的标签。

参数：

**mask** (*zipline.pipeline.Filter**,* *可选*) – 计算十分位数时要忽略的值的掩码。

返回：

**deciles** – 产生从 0 到 9 的整数标签的分类器。

返回类型：

zipline.pipeline.Classifier

```py
top(N, mask=sentinel('NotSpecified'), groupby=sentinel('NotSpecified'))
```

构造一个过滤器，匹配每天自身资产值的最高 N 个。

如果提供了`groupby`，则返回一个过滤器，匹配每个组的最高 N 个资产值。

参数：

+   **N** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 每天通过返回的过滤器的资产数量。

+   **mask** (*zipline.pipeline.Filter**,* *可选*) – 表示计算排名时要考虑的资产的过滤器。如果提供了 mask，则在计算最高值时忽略 mask 产生 False 的任何资产/日期对。

+   **groupby** (*zipline.pipeline.Classifier*, *可选*) – 定义排序分区的一个分类器。

返回：

**filter**

返回类型：

zipline.pipeline.Filter

```py
bottom(N, mask=sentinel('NotSpecified'), groupby=sentinel('NotSpecified'))
```

构造一个过滤器，匹配每天自身资产值的最低 N 个。

如果提供了`groupby`，则返回一个过滤器，匹配`groupby`定义的每个组的最低 N 个资产值。

参数：

+   **N** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 每天通过返回的过滤器的资产数量。

+   **mask** (*zipline.pipeline.Filter**,* *可选*) – 表示计算排名时要考虑的资产的过滤器。如果提供了 mask，则在计算最低值时忽略 mask 产生 False 的任何资产/日期对。

+   **groupby** (*zipline.pipeline.Classifier*, *可选*) – 定义排序分区的一个分类器。

返回：

**filter**

返回类型：

zipline.pipeline.Filter

```py
percentile_between(min_percentile, max_percentile, mask=sentinel('NotSpecified'))
```

构造一个过滤器，匹配自身值落在`min_percentile`和`max_percentile`定义范围内的值。

参数：

+   **min_percentile** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)") *[**0.0**,* *100.0**]*) – 对于数据中高于此百分位的资产返回 True。

+   **max_percentile** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)") *[**0.0**,* *100.0**]*) – 对于数据中低于此百分位的资产返回 True。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 表示在计算百分位阈值时要考虑的资产的过滤器。如果提供了掩码，则每天仅使用`掩码`返回 True 的资产来计算百分位截止点。对于`掩码`产生 False 的资产，此因子的输出也将产生 False。

返回：

**out** – 将计算指定百分位范围掩码的新过滤器。

返回类型：

zipline.pipeline.Filter

```py
isnan()
```

对于此因子中所有 NaN 值，产生 True 的过滤器。

返回：

**nanfilter**

返回类型：

zipline.pipeline.Filter

```py
notnan()
```

对于此因子中非 NaN 的值，产生 True 的过滤器。

返回：

**nanfilter**

返回类型：

zipline.pipeline.Filter

```py
isfinite()
```

对于此因子中除 NaN、inf 或-inf 之外的任何值，产生 True 的过滤器。

```py
clip(min_bound, max_bound, mask=sentinel('NotSpecified'))
```

剪裁（限制）因子中的值。

给定一个区间，区间外的值被剪裁到区间边缘。例如，如果指定了`[0, 1]`的区间，小于 0 的值变为 0，大于 1 的值变为 1。

参数：

+   **最小边界** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11)")) – 使用的最小值。

+   **最大边界** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11)")) – 使用的最大值。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 表示在剪裁时要考虑的资产的过滤器。

注意

若只想在一侧剪裁值，可以传递`-np.inf`和`np.inf`。例如，只想剪裁最大值而不剪裁最小值：

```py
factor.clip(min_bound=-np.inf, max_bound=user_provided_max) 
```

另请参阅

[`numpy.clip`](https://numpy.org/doc/stable/reference/generated/numpy.clip.html#numpy.clip "(在 NumPy v1.25)")

```py
clip(min_bound, max_bound, mask=sentinel('NotSpecified'))
```

剪裁（限制）因子中的值。

给定一个区间，区间外的值被剪裁到区间边缘。例如，如果指定了`[0, 1]`的区间，小于 0 的值变为 0，大于 1 的值变为 1。

参数：

+   **最小边界** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11)")) – 使用的最小值。

+   **最大边界** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11)")) – 使用的最大值。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 表示在剪裁时要考虑的资产的过滤器。

注意

若只想在一侧剪裁值，可以传递`-np.inf`和`np.inf`。例如，只想剪裁最大值而不剪裁最小值：

```py
factor.clip(min_bound=-np.inf, max_bound=user_provided_max) 
```

另请参阅

[`numpy.clip`](https://numpy.org/doc/stable/reference/generated/numpy.clip.html#numpy.clip "(在 NumPy v1.25)")

```py
__add__(other)
```

构建一个`因子`，计算`self + other`。

参数：

**其他** (*zipline.pipeline.Factor**,* [*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

返回：

**因子** – 计算`self + other`的因子，输出`self`和`other`的结果。

返回类型：

zipline.pipeline.Factor

```py
__sub__(other)
```

构建一个`因子`，计算`self - other`。

参数：

**其他** (*zipline.pipeline.Factor**,* [*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

返回：

**因子** – 计算`self - other`的因子，输出`self`和`other`的结果。

返回类型：

zipline.pipeline.Factor

```py
__mul__(other)
```

构建一个`因子`，计算`self * other`。

参数：

**其他** (*zipline.pipeline.Factor**,* [*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

返回：

**因子** – 计算`self * other`的因子，输出`self`和`other`的结果。

返回类型：

zipline.pipeline.Factor

```py
__div__(other)
```

构建一个`因子`，计算`self / other`。

参数：

**其他** (*zipline.pipeline.Factor**,* [*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

返回：

**因子** – 计算`self / other`的因子，输出`self`和`other`的结果。

返回类型：

zipline.pipeline.Factor

```py
__mod__(other)
```

构建一个`因子`，计算`self % other`。

参数：

**其他** (*zipline.pipeline.Factor**,* [*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

返回：

**因子** – 计算`self % other`的因子，输出`self`和`other`的结果。

返回类型：

zipline.pipeline.Factor

```py
__pow__(other)
```

构建一个`因子`，计算`self ** other`。

参数：

**其他** (*zipline.pipeline.Factor**,* [*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

返回：

**因子** – 计算`self ** other`的因子，输出`self`和`other`的结果。

返回类型：

zipline.pipeline.Factor

```py
__lt__(other)
```

构建一个计算`self < other`的`Filter`。

**参数**：

**other** (*zipline.pipeline.Factor**,* [*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

**返回**：

**过滤器** – 计算`self < other`的过滤器，使用`self`和`other`的输出结果。

**返回类型**：

zipline.pipeline.Filter

```py
__le__(other)
```

构建一个计算`self <= other`的`Filter`。

**参数**：

**other** (*zipline.pipeline.Factor**,* [*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

**返回**：

**过滤器** – 计算`self <= other`的过滤器，使用`self`和`other`的输出结果。

**返回类型**：

zipline.pipeline.Filter

```py
__ne__(other)
```

构建一个计算`self != other`的`Filter`。

**参数**：

**other** (*zipline.pipeline.Factor**,* [*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

**返回**：

**过滤器** – 计算`self != other`的过滤器，使用`self`和`other`的输出结果。

**返回类型**：

zipline.pipeline.Filter

```py
__ge__(other)
```

构建一个计算`self >= other`的`Filter`。

**参数**：

**other** (*zipline.pipeline.Factor**,* [*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

**返回**：

**过滤器** – 计算`self >= other`的过滤器，使用`self`和`other`的输出结果。

**返回类型**：

zipline.pipeline.Filter

```py
__gt__(other)
```

构建一个计算`self > other`的`Filter`。

**参数**：

**other** (*zipline.pipeline.Factor**,* [*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

**返回**：

**过滤器** – 计算`self > other`的过滤器，使用`self`和`other`的输出结果。

**返回类型**：

zipline.pipeline.Filter

```py
fillna(fill_value)
```

创建一个新项，该项用`fill_value`填充此项输出的缺失值。

**参数**：

**fill_value** (*zipline.pipeline.ComputableTerm**, or* *object.*) –

用于替换缺失值的对象。

如果传入的是可计算项（例如因子），则将使用该项的结果作为填充值。

如果传递了一个标量（例如一个数字），该标量将用作填充值。

示例

**用标量填充：**

设`f`是一个因子，它将产生以下输出：

```py
 AAPL   MSFT    MCD     BK
2017-03-13    1.0    NaN    3.0    4.0
2017-03-14    1.5    2.5    NaN    NaN 
```

那么`f.fillna(0)`产生以下输出：

```py
 AAPL   MSFT    MCD     BK
2017-03-13    1.0    0.0    3.0    4.0
2017-03-14    1.5    2.5    0.0    0.0 
```

**用术语填充：**

设`f`如上所述，设`g`是另一个将产生以下输出的因子：

```py
 AAPL   MSFT    MCD     BK
2017-03-13   10.0   20.0   30.0   40.0
2017-03-14   15.0   25.0   35.0   45.0 
```

那么，`f.fillna(g)`产生以下输出：

```py
 AAPL   MSFT    MCD     BK
2017-03-13    1.0   20.0    3.0    4.0
2017-03-14    1.5    2.5   35.0   45.0 
```

返回值：

**填充的** – 一个计算与`self`相同结果的术语，但使用`fill_value`的值填充缺失值。

返回类型：

zipline.pipeline.ComputableTerm

```py
mean(mask=sentinel('NotSpecified'))
```

创建一个 1 维因子，每天计算自身平均值。

参数：

**掩码** (*zipline.pipeline.Filter**,* *可选*) – 一个表示在计算结果时要考虑的资产的 Filter。如果提供，我们将忽略`mask`产生`False`的资产/日期对。

返回值：

**结果**

返回类型：

zipline.pipeline.Factor

```py
stddev(mask=sentinel('NotSpecified'))
```

创建一个 1 维因子，每天计算自身标准差。

参数：

**掩码** (*zipline.pipeline.Filter**,* *可选*) – 一个表示在计算结果时要考虑的资产的 Filter。如果提供，我们将忽略`mask`产生`False`的资产/日期对。

返回值：

**结果**

返回类型：

zipline.pipeline.Factor

```py
max(mask=sentinel('NotSpecified'))
```

创建一个 1 维因子，每天计算自身最大值。

参数：

**掩码** (*zipline.pipeline.Filter**,* *可选*) – 一个表示在计算结果时要考虑的资产的 Filter。如果提供，我们将忽略`mask`产生`False`的资产/日期对。

返回值：

**结果**

返回类型：

zipline.pipeline.Factor

```py
min(mask=sentinel('NotSpecified'))
```

创建一个 1 维因子，每天计算自身最小值。

参数：

**掩码** (*zipline.pipeline.Filter**,* *可选*) – 一个表示在计算结果时要考虑的资产的 Filter。如果提供，我们将忽略`mask`产生`False`的资产/日期对。

返回值：

**结果**

返回类型：

zipline.pipeline.Factor

```py
median(mask=sentinel('NotSpecified'))
```

创建一个 1 维因子，每天计算自身中位数。

参数：

**掩码** (*zipline.pipeline.Filter**,* *可选*) – 一个表示在计算结果时要考虑的资产的 Filter。如果提供，我们将忽略`mask`产生`False`的资产/日期对。

返回值：

**结果**

返回类型：

zipline.pipeline.Factor

```py
sum(mask=sentinel('NotSpecified'))
```

创建一个 1 维因子，每天计算自身总和。

参数：

**掩码**（*zipline.pipeline.Filter**,* *可选*) – 一个表示在计算结果时要考虑的资产的过滤器。如果提供，我们忽略`mask`产生`False`的资产/日期对。

返回：

**结果**

返回类型：

zipline.pipeline.Factor

```py
class zipline.pipeline.Term(domain=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), window_safe=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), *args, **kwargs)
```

可以出现在`zipline.pipeline.Pipeline`的计算图中的对象的基类。

注意

大多数管道 API 用户只通过子类与`Term`交互：

+   `BoundColumn`

+   `Factor`

+   `Filter`

+   `分类器`

`Term`的实例是**记忆化的**。如果您使用相同的参数两次调用一个 Term 的构造函数，那么两次调用都将返回相同的对象：

**示例：**

```py
>>> from zipline.pipeline.data import EquityPricing
>>> from zipline.pipeline.factors import SimpleMovingAverage
>>> x = SimpleMovingAverage(inputs=[EquityPricing.close], window_length=5)
>>> y = SimpleMovingAverage(inputs=[EquityPricing.close], window_length=5)
>>> x is y
True 
```

警告

术语的记忆化意味着在构造后修改术语的属性通常是不安全的。

```py
graph_repr()
```

在渲染 GraphViz 图形时使用的简短 repr。

```py
recursive_repr()
```

在递归渲染具有输入的术语时使用的简短 repr。

```py
class zipline.pipeline.data.DataSet
```

管道数据集的基类。

一个`DataSet`由两部分定义：

1.  描述数据集可查询属性的`Column`对象集合。

1.  描述由`DataSet`表示的数据的资产和日历的`Domain`。

要创建新的管道数据集，请定义`DataSet`的子类，并将一个或多个`Column`对象设置为类级属性。每个列都需要一个`np.dtype`，它描述了数据集的加载器应该生成的数据类型。整数列还必须提供一个“缺失值”，用于在给定的资产/日期组合中没有可用值时使用。

默认情况下，数据集的领域是特殊的单例值`GENERIC`，这意味着它们可以在运行于**任何**领域的管道中使用。

在某些情况下，可能更希望将数据集限制为仅支持单个领域。例如，数据集可能描述仅覆盖美国的供应商的数据。要将数据集限制为特定领域，请在类作用域中定义一个领域属性。

您还可以通过调用通用数据集的`specialize`方法并指定感兴趣的领域，来定义特定领域的数据集版本。

示例

内置的 EquityPricing 数据集定义如下：

```py
class EquityPricing(DataSet):
    open = Column(float)
    high = Column(float)
    low = Column(float)
    close = Column(float)
    volume = Column(float) 
```

内置的 USEquityPricing 数据集是 EquityPricing 的一个特化。它定义为：

```py
from zipline.pipeline.domain import US_EQUITIES
USEquityPricing = EquityPricing.specialize(US_EQUITIES) 
```

列可以具有除浮点数之外的其他类型。包含各种公司元数据的数据集可能这样定义：

```py
class CompanyMetadata(DataSet):
    # Use float for semantically-numeric data, even if it's always
    # integral valued (see Notes section below). The default missing
    # value for floats is NaN.
    shares_outstanding = Column(float)

    # Use object for string columns. The default missing value for
    # object-dtype columns is None.
    ticker = Column(object)

    # Use integers for integer-valued categorical data like sector or
    # industry codes. Integer-dtype columns require an explicit missing
    # value.
    sector_code = Column(int, missing_value=-1)

    # Use bool for boolean-valued flags. Note that the default missing
    # value for bool-dtype columns is False.
    is_primary_share = Column(bool) 
```

注释

由于 numpy 没有原生支持带有缺失值的整数，强烈建议用户对任何语义上为数值的数据使用浮点数。这样做可以使用 NaN 作为自然的缺失值，具有有用的传播语义。

```py
classmethod get_column(name)
```

按名称查找列。

参数：

**名称** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要查找的列的名称。

返回：

**列** – 具有给定名称的列。

返回类型：

zipline.pipeline.data.BoundColumn

引发：

[**AttributeError**](https://docs.python.org/3/library/exceptions.html#AttributeError "(in Python v3.11)") – 如果给定名称的列不存在。

```py
class zipline.pipeline.data.Column(dtype, missing_value=sentinel('NotSpecified'), doc=None, metadata=None, currency_aware=False)
```

一个抽象的数据列，尚未与数据集关联。

```py
bind(name)
```

将列对象绑定到其名称。

```py
class zipline.pipeline.data.BoundColumn(dtype, missing_value, dataset, name, doc, metadata, currency_conversion, currency_aware)
```

一个具体绑定到特定数据集的数据列。

```py
dtype
```

加载此列时生成的数据的 dtype。

类型：

[numpy.dtype](https://numpy.org/doc/stable/reference/generated/numpy.dtype.html#numpy.dtype "(in NumPy v1.25)")

```py
latest
```

一个`Filter`、`Factor`或`Classifier`，计算该列在每个日期的最近已知值。有关更多详细信息，请参阅`zipline.pipeline.mixins.LatestMixin`。

类型：

zipline.pipeline.LoadableTerm

```py
dataset
```

该列所属的数据集。

类型：

zipline.pipeline.data.DataSet

```py
name
```

该列的名称。

类型：

[字符串](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
metadata
```

与该列相关的额外元数据。

类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")

```py
currency_aware
```

该列是否生成以货币计价的数据。

类型：

[布尔](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")

注释

此类实例在访问`DataSet`的属性时动态创建。例如，`close`是此类的一个实例。管道 API 用户永远不应该直接构造此类实例。

```py
property currency_aware
```

该列是否生成以货币计价的数据。

```py
property currency_conversion
```

应用于该项的货币转换规范。

```py
property dataset
```

该列所属的数据集。

```py
fx(currency)
```

构造此列的货币转换版本。

参数：

**货币** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* *zipline.currency.Currency*) – 要将此列的数据转换成的货币。

返回：

**列** – 生成与`self`相同数据的列，但货币转换为`currency`。

返回类型：

BoundColumn

```py
graph_repr()
```

用于渲染管道图的简短表示。

```py
property metadata
```

此列的元数据的副本。

```py
property name
```

此列的名称。

```py
property qualname
```

此列的全限定名称。

```py
recursive_repr()
```

用于在递归上下文中渲染的简短表示。

```py
specialize(domain)
```

将`self`特化为具体域。

```py
unspecialize()
```

将列未特化为通用形式。

这等效于`column.specialize(GENERIC)`。

```py
class zipline.pipeline.data.DataSetFamily
```

管道数据集家族的基类。

数据集家族用于表示行唯一标识符需要超过资产和日期坐标的场景。`DataSetFamily`也可以被视为`DataSet`对象的集合，每个对象都有相同的列、域和维度。

`DataSetFamily`对象通过一个或多个`Column`对象以及一个额外的字段`extra_dims`来定义。

`extra_dims`字段定义了除资产和日期之外必须固定的坐标，以生成逻辑时间序列。列对象决定了家族切片将共享的列。

`extra_dims`表示为有序字典，其中键是维度名称，值是沿该维度的唯一值集合。

要在管道表达式中使用`DataSetFamily`，必须使用`slice()`方法为每个额外维度选择特定值。例如，给定一个`DataSetFamily`：

```py
class SomeDataSet(DataSetFamily):
    extra_dims = [
        ('dimension_0', {'a', 'b', 'c'}),
        ('dimension_1', {'d', 'e', 'f'}),
    ]

    column_0 = Column(float)
    column_1 = Column(bool) 
```

此数据集可能代表具有以下列的表：

```py
sid :: int64
asof_date :: datetime64[ns]
timestamp :: datetime64[ns]
dimension_0 :: str
dimension_1 :: str
column_0 :: float64
column_1 :: bool 
```

在这里，我们可以看到隐含的`sid`、`asof_date`和`timestamp`列，以及额外的维度列。

这个`DataSetFamily`可以转换为常规的`DataSet`：

```py
DataSetSlice = SomeDataSet.slice(dimension_0='a', dimension_1='e') 
```

这个切片数据集代表了在高维数据集中满足`(dimension_0 == 'a') & (dimension_1 == 'e')`条件的行。

```py
classmethod slice(*args, **kwargs)
```

对 DataSetFamily 进行切片以生成按资产和日期索引的数据集。

参数：

+   ***args** –

+   ****kwargs** – 沿每个额外维度固定的坐标。

返回：

**数据集** – 一个按资产和日期索引的常规管道数据集。

返回类型：

DataSet

注意

用于生成结果的额外维度坐标可在`extra_coords`属性下获得。

```py
class zipline.pipeline.data.EquityPricing
```

`DataSet`包含每日交易价格和成交量。

```py
close = EquityPricing.close::float64
```

```py
high = EquityPricing.high::float64
```

```py
low = EquityPricing.low::float64
```

```py
open = EquityPricing.open::float64
```

```py
volume = EquityPricing.volume::float64
```

### 内置因子

因子旨在以一种提取算法可交易信号的方式转换输入数据。

```py
class zipline.pipeline.factors.AverageDollarVolume(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

平均每日美元交易量

**默认输入**：[EquityPricing.close, EquityPricing.volume]

**默认窗口长度**：无

```py
compute(today, assets, out, close, volume)
```

通过编写一个将值写入 out 的函数来覆盖此方法。

```py
class zipline.pipeline.factors.BollingerBands(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

布林带技术指标。[`en.wikipedia.org/wiki/Bollinger_Bands`](https://en.wikipedia.org/wiki/Bollinger_Bands)

**默认输入**：`zipline.pipeline.data.EquityPricing.close`

参数：

+   **inputs** (*长度为 1 的可迭代对象***[*BoundColumn**]*) – 用于计算布林带表达式。

+   **window_length** (*int > 0*) – 用于计算布林带的回溯窗口长度。

+   **k** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 用于创建上下带的添加或减去的标准差数量。

```py
compute(today, assets, out, close, k)
```

通过编写一个将值写入 out 的函数来覆盖此方法。

```py
class zipline.pipeline.factors.BusinessDaysSincePreviousEvent(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), domain=sentinel('NotSpecified'), *args, **kwargs)
```

业务日自上一个事件的抽象类。返回每个资产自最近事件日期的**业务日**（非交易日！）数量。

这与 BusinessDaysUntilNextEarnings 保持对称，不使用交易日。

今天宣布或即将宣布事件的资产将产生 0.0 的值。在前一个工作日宣布事件的资产将产生 1.0 的值。

事件日期为 NaT 的资产将产生 NaN 值。

示例

`BusinessDaysSincePreviousEvent`可用于创建事件驱动的因子。例如，你可能只想交易最近 5 个工作日内有 asof_date 数据点的资产。为此，你可以创建一个`BusinessDaysSincePreviousEvent`因子，将数据集中相关的 asof_date 列作为输入，如下所示：

```py
# Factor computing number of days since most recent asof_date
# per asset.
days_since_event = BusinessDaysSincePreviousEvent(
    inputs=[MyDataset.asof_date]
)

# Filter returning True for each asset whose most recent asof_date
# was in the last 5 business days.
recency_filter = (days_since_event <= 5) 
```

```py
dtype = dtype('float64')
```

```py
class zipline.pipeline.factors.BusinessDaysUntilNextEvent(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), domain=sentinel('NotSpecified'), *args, **kwargs)
```

业务日直到下一个事件的抽象类。返回每个资产到下一个已知事件日期的**业务日**（非交易日！）数量。

这并不使用交易日，因为交易日历包含的信息可能在当时计算时对算法不可用。

例如，2001 年 9 月 11 日的 NYSE 收盘价，在 9 月 10 日时算法是无法知晓的。

今天宣布或即将宣布事件的资产将产生 0.0 的值。将在下一个工作日宣布事件的资产将产生 1.0 的值。

事件日期为 NaT 的资产将产生 NaN 值。

```py
dtype = dtype('float64')
```

```py
class zipline.pipeline.factors.DailyReturns(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

计算收盘价的日百分比变化。

**默认输入**：[EquityPricing.close]

```py
class zipline.pipeline.factors.ExponentialWeightedMovingAverage(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

指数加权移动平均

**默认输入**：无

**默认窗口长度**：无

参数：

+   **输入**（*长度为 1 的列表/元组* *的* *绑定列*） – 用于计算平均值的表达式。

+   **窗口长度**（*int > 0*） – 用于计算平均值的回溯窗口的长度。

+   **衰减率**（[*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *0 < decay_rate <= 1*） –

    用于折扣过去观测值的权重因子。

    在计算历史平均值时，行乘以序列：

    ```py
    decay_rate, decay_rate ** 2, decay_rate ** 3, ... 
    ```

注意

+   此类也可以通过名称`EWMA`导入。

另请参阅

[`pandas.DataFrame.ewm()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.ewm.html#pandas.DataFrame.ewm "(in pandas v2.0.3)")

```py
compute(today, assets, out, data, decay_rate)
```

通过一个函数重写此方法，该函数将一个值写入输出。

```py
class zipline.pipeline.factors.ExponentialWeightedMovingStdDev(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

指数加权移动标准差

**默认输入**：无

**默认窗口长度**：无

参数：

+   **输入**（*长度为 1 的列表/元组* *的* *绑定列*） – 用于计算平均值的表达式。

+   **窗口长度**（*int > 0*） – 用于计算平均值的回溯窗口的长度。

+   **衰减率**（[*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *0 < decay_rate <= 1*） –

    用于折扣过去观测值的权重因子。

    在计算历史平均值时，行乘以序列：

    ```py
    decay_rate, decay_rate ** 2, decay_rate ** 3, ... 
    ```

注意

+   此类也可以通过名称`EWMSTD`导入。

另请参阅

`pandas.DataFrame.ewm()`

```py
compute(today, assets, out, data, decay_rate)
```

通过一个函数重写此方法，该函数将一个值写入输出。

```py
class zipline.pipeline.factors.Latest(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

每天产生输入[0]的最新已知值的因素。

数据集列的.latest 属性返回此因素的实例。

```py
compute(today, assets, out, data)
```

通过一个函数重写此方法，该函数将一个值写入输出。

```py
zipline.pipeline.factors.MACDSignal
```

别名为`MovingAverageConvergenceDivergenceSignal`

```py
class zipline.pipeline.factors.MaxDrawdown(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

最大回撤

**默认输入**：无

**默认窗口长度**：无

```py
compute(today, assets, out, data)
```

通过一个函数重写此方法，该函数将一个值写入输出。

```py
class zipline.pipeline.factors.Returns(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

计算给定窗口长度内收盘价的变化百分比。

**默认输入**：[EquityPricing.close]

```py
compute(today, assets, out, close)
```

通过一个函数重写此方法，该函数将一个值写入输出。

```py
class zipline.pipeline.factors.RollingPearson(base_factor, target, correlation_length, mask=sentinel('NotSpecified'))
```

计算给定因素的列与另一因素/绑定列的列或数据切片/单列之间的皮尔逊相关系数的因素。

参数：

+   **基础因素**（*zipline.pipeline.Factor*） – 用于计算其每个列与目标的相关性的因素。

+   **目标**（*zipline.pipeline.Term with a numeric dtype*） – 与基础因素产生的每个数据列计算相关性的项。该项可以是因素、绑定列或切片。如果目标为二维，则按资产计算相关性。

+   **相关长度** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 用于计算每个相关系数的回溯窗口长度。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 描述每天应计算与目标相关性的基础因子资产（列）的过滤器。

参见

[`scipy.stats.pearsonr()`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.pearsonr.html#scipy.stats.pearsonr "(in SciPy v1.11.1)"), `Factor.pearsonr()`, `zipline.pipeline.factors.RollingPearsonOfReturns`

注意

大多数用户应该调用 Factor.pearsonr 而不是直接构造此类的一个实例。

```py
compute(today, assets, out, base_data, target_data)
```

使用一个函数重写此方法，该函数将值写入输出。

```py
class zipline.pipeline.factors.RollingSpearman(base_factor, target, correlation_length, mask=sentinel('NotSpecified'))
```

一个因子，用于计算给定因子各列与另一因子/绑定列或切片/单列数据的斯皮尔曼等级相关系数。

参数：

+   **基础因子** (*zipline.pipeline.Factor*) – 用于计算其各列与目标相关性的因子。

+   **目标** (*zipline.pipeline.Term with a numeric dtype*) – 与基础因子产生的数据每一列计算相关性的项。该项可以是因子、绑定列或切片。如果目标为二维，则按资产计算相关性。

+   **相关长度** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 用于计算每个相关系数的回溯窗口长度。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 描述每天应计算与目标相关性的基础因子资产（列）的过滤器。

参见

[`scipy.stats.spearmanr()`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.spearmanr.html#scipy.stats.spearmanr "(in SciPy v1.11.1)"), `Factor.spearmanr()`, `zipline.pipeline.factors.RollingSpearmanOfReturns`

注意

大多数用户应该调用 Factor.spearmanr 而不是直接构造此类的一个实例。

```py
compute(today, assets, out, base_data, target_data)
```

使用一个函数重写此方法，该函数将值写入输出。

```py
class zipline.pipeline.factors.RollingLinearRegressionOfReturns(target, returns_length, regression_length, mask=sentinel('NotSpecified'))
```

执行普通最小二乘回归，预测给定资产的所有其他资产的回报。

参数：

+   **目标** (*zipline.assets.Asset*) – 用于回归所有其他资产的资产。

+   **回报长度** (*int >= 2*) – 用于计算回报的回溯窗口长度。日回报需要长度为 2 的窗口。

+   **regression_length**（*int >= 1*）– 计算每个回归的回顾窗口长度。

+   **mask** (*zipline.pipeline.Filter**,* *optional*) – 描述每天应将哪些资产与目标资产进行回归的过滤器。

笔记

在许多资产上计算此因子可能耗时。建议使用掩码来限制计算回归的资产数量。

此因子旨在返回五个输出：

+   alpha，一个计算每个回归截距的因子。

+   beta，一个计算每个回归斜率的因子。

+   r_value，一个计算每个回归相关系数的因子。

+   p_value，一个计算每个回归的双侧 p 值的因子，用于假设检验的零假设是斜率为零。

+   stderr，一个计算每个回归估计标准误差的因子。

有关具有多个输出的因子的更多帮助，请参阅`zipline.pipeline.CustomFactor`。

示例

让以下成为三个不同资产的 10 天回报示例：

```py
 SPY    MSFT     FB
2017-03-13    -.03     .03    .04
2017-03-14    -.02    -.03    .02
2017-03-15    -.01     .02    .01
2017-03-16       0    -.02    .01
2017-03-17     .01     .04   -.01
2017-03-20     .02    -.03   -.02
2017-03-21     .03     .01   -.02
2017-03-22     .04    -.02   -.02 
```

假设我们感兴趣的是预测每个股票在滚动 5 天回顾窗口内相对于 SPY 的回报。我们可以通过以下方式计算 2017-03-17 至 2017-03-22 的滚动回归系数（alpha 和 beta）：

```py
regression_factor = RollingRegressionOfReturns(
    target=sid(8554),
    returns_length=10,
    regression_length=5,
)
alpha = regression_factor.alpha
beta = regression_factor.beta 
```

计算 2017-03-17 至 2017-03-22 的`alpha`的结果为：

```py
 SPY    MSFT     FB
2017-03-17       0    .011   .003
2017-03-20       0   -.004   .004
2017-03-21       0    .007   .006
2017-03-22       0    .002   .008 
```

计算 2017-03-17 至 2017-03-22 的`beta`的结果为：

```py
 SPY    MSFT     FB
2017-03-17       1      .3   -1.1
2017-03-20       1      .2     -1
2017-03-21       1     -.3     -1
2017-03-22       1     -.3    -.9 
```

注意，SPY 的 alpha 列全为 0，beta 列全为 1，因为 SPY 与其自身的回归线仅仅是函数 y = x。

要了解其他每个值是如何计算的，以 2017-03-17 MSFT 的`alpha`和`beta`值（分别为.011 和.3）为例。这些值是通过运行线性回归预测 MSFT 的回报来自 SPY 的回报，使用从 2017-03-17 开始并回顾 5 天的值。也就是说，回归是在 x = [-.03, -.02, -.01, 0, .01]和 y = [.03, -.03, .02, -.02, .04]上运行的，并产生了一个斜率.3 和一个截距.011。

另请参阅

`zipline.pipeline.factors.RollingPearsonOfReturns`, `zipline.pipeline.factors.RollingSpearmanOfReturns`

```py
class zipline.pipeline.factors.RollingPearsonOfReturns(target, returns_length, correlation_length, mask=sentinel('NotSpecified'))
```

计算给定资产的回报与所有其他资产的回报之间的皮尔逊积矩相关系数。

皮尔逊相关系数是大多数人所说的“相关系数”或“R 值”。

参数：

+   **target** (*zipline.assets.Asset*) – 与所有其他资产相关的资产。

+   **回报长度** (*int >= 2*) – 计算回报的回溯窗口长度。每日回报需要 2 的窗口长度。

+   **相关性长度** (*int >= 1*) – 计算每个相关系数的回溯窗口长度。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 描述每天应计算哪些资产与目标资产的相关性的过滤器。

注意

计算许多资产的这一因子可能耗时。建议使用掩码以限制计算相关性的资产数量。

示例

让以下成为三个不同资产的 10 天回报示例：

```py
 SPY    MSFT     FB
2017-03-13    -.03     .03    .04
2017-03-14    -.02    -.03    .02
2017-03-15    -.01     .02    .01
2017-03-16       0    -.02    .01
2017-03-17     .01     .04   -.01
2017-03-20     .02    -.03   -.02
2017-03-21     .03     .01   -.02
2017-03-22     .04    -.02   -.02 
```

假设我们感兴趣的是 2017-03-17 至 2017-03-22 期间 SPY 的滚动回报与每只股票的相关性，使用 5 天的回溯窗口（即，我们计算每个相关系数的数据跨越 5 天）。我们可以通过以下方式实现：

```py
rolling_correlations = RollingPearsonOfReturns(
    target=sid(8554),
    returns_length=10,
    correlation_length=5,
) 
```

从 2017-03-17 到 2017-03-22 计算`rolling_correlations`的结果给出：

```py
 SPY   MSFT     FB
2017-03-17       1    .15   -.96
2017-03-20       1    .10   -.96
2017-03-21       1   -.16   -.94
2017-03-22       1   -.16   -.85 
```

请注意，SPY 的列全为 1，因为任何数据系列与其自身的相关性始终为 1。要了解其他每个值是如何计算的，以 MSFT 列中的.15 为例。这是从 2017-03-17 回溯的 SPY 回报（-.03, -.02, -.01, 0, .01）与 MSFT 回报（.03, -.03, .02, -.02, .04）之间的相关系数。

另请参阅

`zipline.pipeline.factors.RollingSpearmanOfReturns`, `zipline.pipeline.factors.RollingLinearRegressionOfReturns`

```py
class zipline.pipeline.factors.RollingSpearmanOfReturns(target, returns_length, correlation_length, mask=sentinel('NotSpecified'))
```

计算给定资产的回报与所有其他资产的回报之间的斯皮尔曼等级相关系数。

参数：

+   **目标** (*zipline.assets.Asset*) – 与所有其他资产进行相关的资产。

+   **回报长度** (*int >= 2*) – 计算回报的回溯窗口长度。每日回报需要 2 的窗口长度。

+   **相关性长度** (*int >= 1*) – 计算每个相关系数的回溯窗口长度。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 描述每天应计算哪些资产与目标资产的相关性的过滤器。

注意

计算许多资产的这一因子可能耗时。建议使用掩码以限制计算相关性的资产数量。

另请参阅

`zipline.pipeline.factors.RollingPearsonOfReturns`, `zipline.pipeline.factors.RollingLinearRegressionOfReturns`

```py
class zipline.pipeline.factors.SimpleBeta(target, regression_length, allowed_missing_percentage=0.25)
```

**产生斜率的因子**，即每个资产的日回报率与单一“目标”资产的日回报率之间的回归线斜率。

**参数**：

+   **目标**（*zipline.Asset*）- 其他资产应与之回归的资产。

+   **回归长度**（[*整数*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")）- 用于回归的日回报天数。

+   **允许缺失百分比**（[*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")，可选）- 在计算贝塔值时允许缺失的回报观察值的百分比（介于 0 和 1 之间）。具有超过此百分比的回报观察值缺失的资产将产生 NaN 值。默认行为是允许 25%的输入缺失。

```py
compute(today, assets, out, all_returns, target_returns, allowed_missing_count)
```

**重写此方法**，使用一个函数将值写入输出。

```py
dtype = dtype('float64')
```

```py
graph_repr()
```

**简短的表示形式**，用于渲染管道图。

```py
property target
```

**获取贝塔计算的目标**

```py
class zipline.pipeline.factors.RSI(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

**相对强弱指数**

**默认输入**：`zipline.pipeline.data.EquityPricing.close`

**默认窗口长度**：15

```py
compute(today, assets, out, closes)
```

**重写此方法**，使用一个函数将值写入输出。

```py
class zipline.pipeline.factors.SimpleMovingAverage(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

**任意列的平均值**

**默认输入**：无

**默认窗口长度**：无

```py
compute(today, assets, out, data)
```

**重写此方法**，使用一个函数将值写入输出。

```py
class zipline.pipeline.factors.VWAP(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

**成交量加权平均价格**

**默认输入**：[EquityPricing.close, EquityPricing.volume]

**默认窗口长度**：无

```py
class zipline.pipeline.factors.WeightedAverageValue(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

**VWAP 类计算的辅助工具**

**默认输入**：无

**默认窗口长度**：无

```py
compute(today, assets, out, base, weight)
```

**重写此方法**，使用一个函数将值写入输出。

```py
class zipline.pipeline.factors.PercentChange(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

**计算百分比变化**，超过给定的`window_length`。

**默认输入**：无

**默认窗口长度**：无

**注释**

**百分比变化**计算为`(new - old) / abs(old)`。

```py
compute(today, assets, out, values)
```

**重写此方法**，使用一个函数将值写入输出。

```py
class zipline.pipeline.factors.PeerCount(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

**同类计数**，即给定分类器中不同类别的数量。此因子由分类器的实例方法 peer_count()返回。

**默认输入**：无

**默认窗口长度**：1

```py
compute(today, assets, out, classifier_values)
```

**重写此方法**，使用一个函数将值写入输出。

### **内置过滤器**

```py
class zipline.pipeline.filters.All(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

**过滤器**，要求资产在`window_length`连续天内产生 True。

**默认输入**：无

**默认窗口长度**：无

```py
compute(today, assets, out, arg)
```

**重写此方法**，使用一个函数将值写入输出。

```py
class zipline.pipeline.filters.AllPresent(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

**管道过滤器**，指示输入项在给定窗口内具有数据。

```py
compute(today, assets, out, value)
```

**重写此方法**，使用一个函数将值写入输出。

```py
class zipline.pipeline.filters.Any(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

**过滤器**，要求资产在最近`window_length`天内至少有一天产生 True。

**默认输入**：无

**默认窗口长度：** 无

```py
compute(today, assets, out, arg)
```

通过一个函数重写此方法，该函数将值写入 out。

```py
class zipline.pipeline.filters.AtLeastN(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

一个过滤器，要求资产在过去`window_length`天内至少连续 N 天为真。

**默认输入：** 无

**默认窗口长度：** 无

```py
compute(today, assets, out, arg, N)
```

通过一个函数重写此方法，该函数将值写入 out。

```py
class zipline.pipeline.filters.SingleAsset(asset)
```

一个仅对给定资产计算为真的过滤器。

```py
graph_repr()
```

用于渲染 GraphViz 图表时的简短表示。

```py
class zipline.pipeline.filters.StaticAssets(assets)
```

一个仅对预先确定的一组资产计算为真的过滤器。

`StaticAssets` 主要用于调试或在已知一组固定资产的情况下交互式计算管道术语。

参数：

**assets** (*iterable***[*Asset**]*) – 要过滤的资产的可迭代对象。

```py
class zipline.pipeline.filters.StaticSids(sids)
```

一个仅对预先确定的一组 sids 计算为真的过滤器。

`StaticSids` 主要用于调试或在已知一组固定 sids 的情况下交互式计算管道术语。

参数：

**sids** (*iterable**[*[*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*]*) – 要过滤的 sids 的可迭代对象。

### 管道引擎

执行`Pipeline`的计算引擎定义了核心计算算法。

主要入口点是 SimplePipelineEngine.run_pipeline，它实现了以下执行管道的算法：

1.  确定管道的域。

1.  构建管道中所有术语的依赖关系图，并提供每个术语从其输入中需要额外行数的信息。

1.  将（2）中计算的域与我们的 AssetFinder 结合，生成一个“生命周期矩阵”。生命周期矩阵是一个布尔值的 DataFrame，其标签为日期 x 资产。每个条目对应于一个（日期，资产）对，并指示在给定日期该资产是否可交易。

1.  生成一个包含缓存或预先计算术语的“工作区”字典。

1.  对（1）中构建的图进行拓扑排序，以生成未预填充的任何术语的执行顺序。

1.  按照（5）中计算的顺序遍历术语。对于每个术语：

    1.  从工作区获取术语的输入。

    1.  计算每个术语并将结果存储在工作区中。

    1.  如果结果不再需要，则从工作区中移除以减少执行期间的内存使用。

1.  从工作区提取管道的输出，并将其转换为“窄”格式，输出标签由管道的屏幕决定。

```py
class zipline.pipeline.engine.PipelineEngine
```

```py
abstract run_pipeline(pipeline, start_date, end_date, hooks=None)
```

从`start_date`到`end_date`计算`pipeline`的值。

参数：

+   **pipeline** (*zipline.pipeline.Pipeline*) – 要运行的管道。

+   **start_date** (*pd.Timestamp*) – 计算矩阵的起始日期。

+   **end_date** (*pd.Timestamp*) – 计算矩阵的结束日期。

+   **hooks** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*[**实现**(**PipelineHooks**)**]**,* *可选*) – 用于对管道执行进行检测的钩子。

返回：

**result** – 计算结果的框架。

`result`列对应于 pipeline.columns 的条目，它应该是一个将字符串映射到`zipline.pipeline.Term`实例的字典。

对于`start_date`和`end_date`之间的每一天，`result`将包含通过管道筛选的每个资产的行。筛选条件为`None`表示应该为每一天存在的每个资产返回一行。

返回类型：

pd.DataFrame

```py
abstract run_chunked_pipeline(pipeline, start_date, end_date, chunksize, hooks=None)
```

计算从`start_date`到`end_date`的`pipeline`值，以`chunksize`大小的日期块执行。

分块执行减少了内存消耗，并且根据您的管道内容，可能会减少计算时间。

参数：

+   **pipeline** (*Pipeline*) – 要运行的管道。

+   **start_date** (*pd.Timestamp*) – 运行管道的开始日期。

+   **end_date** (*pd.Timestamp*) – 运行管道的结束日期。

+   **chunksize** ([*整数*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 每次执行的天数。

+   **hooks** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*[**实现**(**PipelineHooks**)**]**,* *可选*) – 用于对管道执行进行检测的钩子。

返回：

**result** – 计算结果的框架。

`result`列对应于 pipeline.columns 的条目，它应该是一个将字符串映射到`zipline.pipeline.Term`实例的字典。

对于`start_date`和`end_date`之间的每一天，`result`将包含通过管道筛选的每个资产的行。筛选条件为`None`表示应该为每一天存在的每个资产返回一行。

返回类型：

pd.DataFrame

另请参阅

`zipline.pipeline.engine.PipelineEngine.run_pipeline()`

```py
class zipline.pipeline.engine.SimplePipelineEngine(get_loader, asset_finder, default_domain=GENERIC, populate_initial_workspace=None, default_hooks=None)
```

计算每个术语独立的 PipelineEngine 类。

参数：

+   **get_loader** (*可调用*) – 一个函数，它接收一个可加载的术语并返回一个 PipelineLoader，用于检索该术语的原始数据。

+   **asset_finder** (*zipline.assets.AssetFinder*) – 一个 AssetFinder 实例。我们依赖 AssetFinder 来确定在任何时间点哪些资产在顶级宇宙中。

+   **populate_initial_workspace** (*callable**,* *optional*) – 用于在计算管道时填充初始工作区的函数。有关更多信息，请参阅 `zipline.pipeline.engine.default_populate_initial_workspace()`。

+   **default_hooks** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*,* *optional*) – 应该用于检测此引擎执行的所有管道的钩子列表。

（另请参阅）

`zipline.pipeline.engine.default_populate_initial_workspace()`

```py
__init__(get_loader, asset_finder, default_domain=GENERIC, populate_initial_workspace=None, default_hooks=None)
```

```py
run_chunked_pipeline(pipeline, start_date, end_date, chunksize, hooks=None)
```

从 `开始日期` 到 `结束日期` 计算 `pipeline` 的值，每次计算 `chunksize` 大小的日期块。

分块执行减少了内存消耗，并且可能会根据管道内容减少计算时间。

（参数：）

+   **pipeline** (*Pipeline*) – 要运行的管道。

+   **开始日期** (*pd.Timestamp*) – 运行管道的开始日期。

+   **结束日期** (*pd.Timestamp*) – 运行管道的结束日期。

+   **chunksize** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 每次执行的天数。

+   **hooks** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*[**implements**(**PipelineHooks**)**]**,* *optional*) – 用于检测管道执行的钩子。

（返回：）

**结果** – 计算结果的框架。

`结果` 列对应于 pipeline.columns 的条目，它应该是将字符串映射到 `zipline.pipeline.Term` 实例的字典。

对于 `开始日期` 和 `结束日期` 之间的每个日期，`结果` 将包含每个通过 pipeline.screen 的资产的行。`None` 的屏幕表示应该为每天存在的每个资产返回一行。

（返回类型：）

pd.DataFrame

（另请参阅）

`zipline.pipeline.engine.PipelineEngine.run_pipeline()`

```py
run_pipeline(pipeline, start_date, end_date, hooks=None)
```

从 `开始日期` 到 `结束日期` 计算 `pipeline` 的值。

（参数：）

+   **pipeline** (*zipline.pipeline.Pipeline*) – 要运行的管道。

+   **开始日期** (*pd.Timestamp*) – 计算矩阵的开始日期。

+   **结束日期** (*pd.Timestamp*) – 计算矩阵的结束日期。

+   **hooks** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*[**implements**(**PipelineHooks**)**]**,* *optional*) – 用于检测管道执行的钩子。

（返回：）

**结果** – 计算结果的框架。

`result`列对应于 pipeline.columns 的条目，它应该是一个字典，将字符串映射到`zipline.pipeline.Term`的实例。

对于`start_date`和`end_date`之间的每个日期，`result`将包含每个通过 pipeline.screen 的资产的行。`None`的屏幕表示应该为每天存在的每个资产返回一行。

返回类型：

pd.DataFrame

```py
zipline.pipeline.engine.default_populate_initial_workspace(initial_workspace, root_mask_term, execution_plan, dates, assets)
```

`populate_initial_workspace`的默认实现。此函数返回`initial_workspace`参数，不做任何修改。

参数：

+   **初始工作区** ([*字典*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")*[**类似数组**]*) – 在我们填充任何缓存项之前的初始工作区。

+   **根掩码术语** (*Term*) – 根掩码术语，通常是`AssetExists()`。这是为了计算各个术语的日期所必需的。

+   **执行计划** (*ExecutionPlan*) – 正在运行的管道的执行计划。

+   **日期** (*pd.DatetimeIndex*) – 此管道运行中请求的所有日期，包括用于回溯窗口的额外日期。

+   **资产** (*pd.Int64Index*) – 计算窗口中存在的所有资产。

返回：

**填充的初始工作区** – 开始计算的工作区。

返回类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[术语, 类似数组]

### 数据加载器

有几种加载器需要向`Pipeline`提供数据，这些加载器需要实现由`PipelineLoader`定义的接口。

```py
class zipline.pipeline.loaders.base.PipelineLoader(*args, **kwargs)
```

管道加载器的接口。

```py
load_adjusted_array(domain, columns, dates, sids, mask)
```

加载`columns`的数据作为 AdjustedArrays。

参数：

+   **域** (*zipline.pipeline.domain.Domain*) – 必须加载请求数据的管道的域。

+   **列** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")**[*zipline.pipeline.data.dataset.BoundColumn**]*) – 请求数据的列。

+   **日期** (*pd.DatetimeIndex*) – 请求数据的日期。

+   **sid** (*pd.Int64Index*) – 请求数据的资产标识符。

+   **掩码** (*np.array**[**ndim=2**,* *dtype=bool**]*) – 形状为(len(dates), len(sids))的布尔数组，指示我们认为请求的资产在哪些日期是活动的/可交易的。某些加载器使用此进行优化。

返回：

**数组** – 从列到表示请求日期和请求 sid 的点在时间滚动视图的 AdjustedArray 的映射。

返回类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[BoundColumn -> zipline.lib.adjusted_array.AdjustedArray]

```py
__init__()
```

```py
class zipline.pipeline.loaders.frame.DataFrameLoader(column, baseline, adjustments=None)
```

从 DataFrame 读取输入的 PipelineLoader。

主要用于测试，但如果数据适合内存，也可用于实际工作。

参数：

+   **列**（*zipline.pipeline.data.BoundColumn*） – 该列的数据可由该加载器加载。

+   **基线**（[*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)")） – 具有 DatetimeIndex 类型索引和 Int64Index 类型列的 DataFrame。日期应标记为算法可**获得**值的第一个日期。这意味着 OHLCV 数据在提供给此类之前通常应向后移动一个交易日。

+   **调整**（[*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)")*,* *默认=None*） –

    具有以下列的 DataFrame：

    sid : int value : any kind : int (zipline.pipeline.loaders.frame.ADJUSTMENT_TYPES) start_date : datetime64 (可以为 NaT) end_date : datetime64 (必须设置) apply_date : datetime64 (必须设置)

    默认的 None 被解释为“不对基线进行调整”。

```py
__init__(column, baseline, adjustments=None)
```

```py
format_adjustments(dates, assets)
```

构建 AdjustedArray 期望格式的 Adjustment 对象字典。

返回一个字典，形式如下：{ # 我们应应用调整列表的日期在日期中的整数索引。 1 : [ Float64Multiply(first_row=2, last_row=4, col=3, value=0.5), Float64Overwrite(first_row=3, last_row=5, col=1, value=2.0), … ], … }

```py
load_adjusted_array(domain, columns, dates, sids, mask)
```

从我们存储的基线加载数据。

```py
class zipline.pipeline.loaders.equity_pricing_loader.EquityPricingLoader(raw_price_reader, adjustments_reader, fx_reader)
```

加载每日 OHLCV 数据的 PipelineLoader。

参数：

+   **原始价格读取器**（*zipline.data.session_bars.SessionBarReader*） – 提供原始价格的读取器。

+   **调整读取器**（*zipline.data.adjustments.SQLiteAdjustmentReader*） – 提供价格/成交量调整的读取器。

+   **外汇读取器**（*zipline.data.fx.FXRateReader*） – 提供货币转换的读取器。

```py
__init__(raw_price_reader, adjustments_reader, fx_reader)
```

```py
zipline.pipeline.loaders.equity_pricing_loader.USEquityPricingLoader
```

别名为 `EquityPricingLoader`

```py
class zipline.pipeline.loaders.events.EventsLoader(events, next_value_columns, previous_value_columns)
```

支持加载事件字段下一个和上一个值的 PipelineLoaders 的基类。

目前不支持调整。

参数：

+   **事件**（*pd.DataFrame*） –

    表示与特定公司相关的事件（例如股票回购或盈利公告）的 DataFrame。

    `事件` 必须至少包含三列：

    sidint64

    与每个事件关联的资产 ID。

    事件日期 datetime64[ns]

    事件发生的日期。

    时间戳 datetime64[ns]

    我们得知该事件的日期。

+   **next_value_columns** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")*[**BoundColumn -> str**]*) – 从数据集列到原始字段名称的映射，用于在搜索下一个事件值时应使用的字段名称。

+   **previous_value_columns** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")*[**BoundColumn -> str**]*) – 从数据集列到原始字段名称的映射，用于在搜索上一个事件值时应使用的字段名称。

```py
__init__(events, next_value_columns, previous_value_columns)
```

```py
class zipline.pipeline.loaders.earnings_estimates.EarningsEstimatesLoader(estimates, name_map)
```

一个抽象的估计数据管道加载器，可以根据列数据集的 num_announcements 属性，从日历日期向前/向后加载可变数量的季度数据。如果需要应用拆分调整，必须提供加载器、拆分调整后的列和拆分调整后的 asof 日期。

参数：

+   **estimates** (*pd.DataFrame*) –

    原始估计数据；必须至少包含 5 列：

    sidint64

    与每个估计关联的资产 ID。

    event_datedatetime64[ns]

    估计所针对的事件将/已经发生的日期。

    timestampdatetime64[ns]

    我们得知估计值的日期时间。

    fiscal_quarterint64

    事件发生/将发生的季度。

    fiscal_yearint64

    事件发生/将发生的年份。

+   **name_map** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")*[**str -> str**]*) – 此加载器将加载的 BoundColumns 的名称映射到事件中相应列的名称。

```py
__init__(estimates, name_map)
```

## 交易所和资产元数据

```py
class zipline.assets.ExchangeInfo(name, canonical_name, country_code)
```

资产交易的交易所。

参数：

+   **name** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *or* *None*) – 交易所的全名，例如‘NEW YORK STOCK EXCHANGE’或‘NASDAQ GLOBAL MARKET’。

+   **canonical_name** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 交易所的标准名称，例如‘NYSE’或‘NASDAQ’。如果为 None，则将与名称相同。

+   **country_code** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 交易所所在的国家代码。

```py
name
```

交易所的全名，例如‘NEW YORK STOCK EXCHANGE’或‘NASDAQ GLOBAL MARKET’。

类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") 或 None

```py
canonical_name
```

交易所的标准名称，例如‘NYSE’或‘NASDAQ’。如果为 None，则将与名称相同。

类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
country_code
```

交易所所在的国家代码。

类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
calendar
```

交易所使用的交易日历。

类型：

TradingCalendar

```py
property calendar
```

该交易所使用的交易日历。

```py
class zipline.assets.Asset
```

可以被交易算法拥有的实体的基类。

```py
sid
```

分配给资产的持久唯一标识符。

类型：

[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")

```py
symbol
```

资产最近交易的最新股票代码。如果资产更改股票代码，此字段可能会在没有警告的情况下更改。如果需要持久标识符，请使用`sid`。

类型：

[字符串](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
asset_name
```

资产的全名。

类型：

[字符串](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
exchange
```

资产交易的交易所的规范简称（例如，‘NYSE’）。

类型：

[字符串](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
exchange_full
```

资产交易的交易所的全名（例如，‘纽约证券交易所’）。

类型：

[字符串](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
exchange_info
```

有关该资产上市的交易所的信息。

类型：

zipline.assets.ExchangeInfo

```py
country_code
```

表示资产交易国家的两个字符代码。

类型：

[字符串](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
start_date
```

资产首次交易日期。

类型：

pd.Timestamp

```py
end_date
```

资产交易的最后日期。在 Quantopian 上，对于仍在交易的资产，此值设置为当前（实时）日期。

类型：

pd.Timestamp

```py
tick_size
```

该资产价格变动的最小金额。

类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")

```py
auto_close_date
```

在模拟中，此资产的仓位将在该日期自动清算为现金。默认情况下，这是`end_date`之后的三天。

类型：

pd.Timestamp

```py
from_dict()
```

从字典构建资产实例。

```py
is_alive_for_session()
```

返回资产在给定时间点是否存活。

参数：

**会话标签** (*pd.Timestamp*) – 要检查的期望会话标签。（UTC 午夜）

返回：

**布尔值**

返回类型：

检查资产在给定时间点是否存活。

```py
is_exchange_open()
```

参数：

**dt_ 分钟** (*pd.Timestamp* *(**UTC**,* *时区感知**)*) – 要检查的分钟。

返回：

**布尔值**

返回类型：

检查资产的交易所是否在给定分钟内开放。

```py
to_dict()
```

转换为包含资产所有属性的 python 字典。

这在调试时通常很有用。

返回：

**as_dict**

返回类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")

```py
class zipline.assets.Equity
```

资产子类，代表公司、信托或合伙企业的部分所有权。

```py
class zipline.assets.Future
```

资产子类，代表期货合约的所有权。

```py
to_dict()
```

转换为 python 字典。

```py
class zipline.assets.AssetConvertible
```

ABC，用于可转换为资产整数表示的类型。

包括资产、字符串和整数

## 交易日历 API

算法执行的时间线事件遵循特定的`TradingCalendar`。

## 数据 API

### 写入器

```py
class zipline.data.bcolz_daily_bars.BcolzDailyBarWriter(filename, calendar, start_session, end_session)
```

能够将每日 OHLCV 数据写入磁盘的类，以便可以高效地由 BcolzDailyOHLCVReader 读取。

参数：

+   **文件名** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 我们应该写入输出的位置。

+   **日历** (*zipline.utils.calendar.trading_calendar*) – 用于计算资产日历偏移的日历。

+   **start_session** (*pd.Timestamp*) – 午夜 UTC 会话标签。

+   **end_session** (*pd.Timestamp*) – 午夜 UTC 会话标签。

另请参阅

`zipline.data.bcolz_daily_bars.BcolzDailyBarReader`

```py
write(data, assets=None, show_progress=False, invalid_data_behavior='warn')
```

参数：

+   **data** (*iterable**[*[*tuple*](https://docs.python.org/3/library/stdtypes.html#tuple "(in Python v3.11)")*[*[*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* [*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)") *or* *bcolz.ctable**]**]*) – 要写入的数据块。每个块应为 sid 和该资产数据的元组。

+   **assets** ([*set*](https://docs.python.org/3/library/stdtypes.html#set "(in Python v3.11)")*[*[*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*]**,* *optional*) – 应包含在`data`中的资产。如果提供了这个，我们将检查`data`与资产，并提供更好的进度信息。

+   **show_progress** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *optional*) – 是否在写入时显示进度条。

+   **invalid_data_behavior** (*{'warn'**,* *'raise'**,* *'ignore'}**,* *optional*) – 当遇到超出 uint32 范围的数据时，应采取的措施。

返回：

**table** – 新写入的表。

返回类型：

bcolz.ctable

```py
write_csvs(asset_map, show_progress=False, invalid_data_behavior='warn')
```

从我们的资产映射中读取 CSV 作为 DataFrame。

参数：

+   **asset_map** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")*[**int -> str**]*) – 资产 id 到包含该资产 CSV 数据的文件路径的映射

+   **show_progress** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 是否在写入时显示进度条。

+   **invalid_data_behavior** (*{'warn'**,* *'raise'**,* *'ignore'}*) – 当遇到超出 uint32 范围的数据时，应采取的措施。

```py
class zipline.data.adjustments.SQLiteAdjustmentWriter(conn_or_path, equity_daily_bar_reader, overwrite=False)
```

用于由 SQLiteAdjustmentReader 读取的数据的写入器

参数：

+   **conn_or_path** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *or* [*sqlite3.Connection*](https://docs.python.org/3/library/sqlite3.html#sqlite3.Connection "(in Python v3.11)")) – 目标 sqlite 数据库的句柄。

+   **equity_daily_bar_reader** (*SessionBarReader*) – 用于股息写入的日柱读取器。

+   **overwrite** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *optional**,* *default=False*) – 如果为 True 且 conn_or_path 是字符串，则在连接之前删除给定路径上的任何现有文件。

另请参阅

`zipline.data.adjustments.SQLiteAdjustmentReader`

```py
calc_dividend_ratios(dividends)
```

计算应用于股票的比率，以便在查看定价历史时平滑价格，在 ex_date 时，市场调整股票价值的变化，因为即将到来的股息。

返回：

与拆分和合并相同的格式的框架，键包括 - sid，股票的 ID - effective_date，应用比率的日期（以秒为单位）。 - ratio，应用于向后查看定价数据的比率。

返回类型：

DataFrame

```py
write(splits=None, mergers=None, dividends=None, stock_dividends=None)
```

将数据写入 SQLite 文件，供 SQLiteAdjustmentReader 读取。

参数：

+   **splits** ([*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)")*,* *optional*) –

    包含拆分数据的 DataFrame。该 DataFrame 的格式为：

    effective_dateint

    调整应应用的日期，表示为自 Unix 纪元以来的秒数。

    ratiofloat

    对于有效日期之前的所有数据应用的值。对于开盘价、最高价、最低价和收盘价，这些值乘以比率。成交量除以该值。

    sidint

    与此调整相关的资产 ID。

+   **mergers** ([*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)")*,* *optional*) –

    包含合并数据的 DataFrame。该 DataFrame 的格式为：

    effective_dateint

    调整应应用的日期，表示为自 Unix 纪元以来的秒数。

    ratiofloat

    对于有效日期之前的所有数据应用的值。对于开盘价、最高价、最低价和收盘价，这些值乘以比率。成交量不受影响。

    sidint

    与此调整相关的资产 ID。

+   **dividends** ([*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)")*,* *optional*) –

    包含股息数据的 DataFrame。DataFrame 的格式为：

    sidint

    与此调整相关的资产 ID。

    ex_datedatetime64

    必须持有股票以有资格接收付款的日期。

    declared_datedatetime64

    股息向公众宣布的日期。

    pay_datedatetime64

    股息分配的日期。

    record_datedatetime64

    检查股票所有权以确定股息分配的日期。

    amountfloat

    每股支付的现金金额。

    股息比率计算方式为：`1.0 - (dividend_value / "ex_date 前一天的收盘价")`

+   **stock_dividends** ([*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)")*,* *optional*) –

    包含股票股息数据的 DataFrame。DataFrame 的格式为：

    > sidint
    > 
    > 与此调整相关的资产 ID。
    > 
    > ex_datedatetime64
    > 
    > 必须持有股票以有资格接收付款的日期。
    > 
    > declared_datedatetime64
    > 
    > 股息向公众宣布的日期。
    > 
    > pay_datedatetime64
    > 
    > 股息分配的日期。
    > 
    > 记录日期时间 64
    > 
    > 检查股票所有权以确定股息分配的日期。
    > 
    > 支付 sid 整数
    > 
    > 应支付的股份的资产 id。
    > 
    > 比率浮点数
    > 
    > 当前持有的 sid 中应使用 payment_sid 的新股份支付的股份比率。

另请参阅

`zipline.data.adjustments.SQLiteAdjustmentReader`

```py
write_dividend_data(dividends, stock_dividends=None)
```

同时写入股息支付和派生价格调整比率。

```py
write_dividend_payouts(frame)
```

将股息支付数据写入 SQLite 表 dividend_payouts。

```py
class zipline.assets.AssetDBWriter(engine)
```

用于向资产数据库写入数据的类。

参数：

**engine** (*Engine* *或* [*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – SQLAlchemy 引擎或 SQL 数据库的路径。

```py
init_db(txn=None)
```

连接到数据库并创建表。

参数：

**txn** (*sa.engine.Connection*, *可选*) – 要执行的事务块。如果未提供，将使用提供的引擎启动新事务。

返回：

**metadata** – 描述新资产数据库的元数据。

返回类型：

sa.MetaData

```py
write(equities=None, futures=None, exchanges=None, root_symbols=None, equity_supplementary_mappings=None, chunk_size=999)
```

将资产元数据写入 sqlite 数据库。

参数：

+   **equities** (*pd.DataFrame*, *可选*) –

    股票元数据。该数据框的列包括：

    > 符号字符串
    > 
    > 该股票的代码。
    > 
    > 资产名称字符串
    > 
    > 该资产的全称。
    > 
    > 开始日期时间
    > 
    > 该资产创建的日期。
    > 
    > 结束日期时间，可选
    > 
    > 我们拥有该资产的最后交易数据的日期。
    > 
    > 首次交易日期时间，可选
    > 
    > 我们拥有该资产的首个交易数据的日期。
    > 
    > 自动关闭日期时间，可选
    > 
    > 关闭该资产中任何持仓的日期。
    > 
    > 交易所字符串
    > 
    > 该资产交易的交易所。

    该数据框的索引应包含 sids。

+   **期货** (*pd.DataFrame*, *可选*) –

    期货合约元数据。该数据框的列包括：

    > 符号字符串
    > 
    > 该期货合约的代码。
    > 
    > 根符号字符串
    > 
    > 根符号，或去除到期日的符号。
    > 
    > 资产名称字符串
    > 
    > 该资产的全称。
    > 
    > 开始日期时间，可选
    > 
    > 该资产创建的日期。
    > 
    > 结束日期时间，可选
    > 
    > 我们拥有该资产的最后交易数据的日期。
    > 
    > 首次交易日期时间，可选
    > 
    > 我们拥有该资产的首个交易数据的日期。
    > 
    > 交易所字符串
    > 
    > 该资产交易的交易所。
    > 
    > 通知日期时间
    > 
    > 合约所有者可能被迫接受合约资产实物交割的日期。
    > 
    > 到期日期时间
    > 
    > 合约到期日期。
    > 
    > 自动关闭日期时间
    > 
    > 经纪商将自动关闭该合约中任何持仓的日期。
    > 
    > 最小价格变动浮点数
    > 
    > 合约的最小价格变动。
    > 
    > 乘数：浮点数
    > 
    > 该合约代表的底层资产的数量。

+   **交易所** (*pd.DataFrame*, *可选*) –

    资产可交易的交易所。该数据框的列包括：

    > 交易所字符串
    > 
    > 交易所的全称。
    > 
    > 规范名称字符串
    > 
    > 交易所的规范名称。
    > 
    > 国家代码字符串
    > 
    > 交易所的 ISO 3166 alpha-2 国家代码。

+   **根符号**（*pd.DataFrame**，*可选*）-

    期货合约的根符号。该数据框的列包括：

    > 根符号字符串
    > 
    > 根符号名称。
    > 
    > 根符号 ID 整数
    > 
    > 该根符号的唯一 ID。
    > 
    > 部门字符串，可选
    > 
    > 该根符号的部门。
    > 
    > 描述字符串，可选
    > 
    > 该根符号的简短描述。
    > 
    > 交易所字符串
    > 
    > 该根符号交易的交易所。

+   **股票补充映射**（*pd.DataFrame**，*可选*）- 将任意类型的值映射到资产的额外映射。

+   **块大小**（[*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*，*可选*）- 一次写入 SQLite 表的行数。这默认为 sqlite 中绑定参数的默认数量。如果您使用更多或更少的参数编译 sqlite3，您可能希望在此传递该值。

另请参阅

`zipline.assets.asset_finder`

```py
write_direct(equities=None, equity_symbol_mappings=None, equity_supplementary_mappings=None, futures=None, exchanges=None, root_symbols=None, chunk_size=999)
```

以资产数据库中存储的格式将资产元数据写入 sqlite 数据库。

参数：

+   **股票**（*pd.DataFrame**，*可选*）-

    股票元数据。该数据框的列包括：

    > 代码字符串
    > 
    > 该股票的代码。
    > 
    > 资产名称字符串
    > 
    > 该资产的全名。
    > 
    > 开始日期时间
    > 
    > 该资产创建的日期。
    > 
    > 结束日期时间，可选
    > 
    > 我们拥有该资产交易数据的最后一个日期。
    > 
    > 首次交易日期时间，可选
    > 
    > 我们拥有该资产交易数据的第一个日期。
    > 
    > 自动关闭日期时间，可选
    > 
    > 关闭该资产中任何头寸的日期。
    > 
    > 交易所字符串
    > 
    > 该资产交易的交易所。

    该数据框的索引应包含 sids。

+   **期货**（*pd.DataFrame**，*可选*）-

    期货合约元数据。该数据框的列包括：

    > 代码字符串
    > 
    > 该期货合约的代码。
    > 
    > 根符号字符串
    > 
    > 根符号，或去除到期日的符号。
    > 
    > 资产名称字符串
    > 
    > 该资产的全名。
    > 
    > 开始日期时间，可选
    > 
    > 该资产创建的日期。
    > 
    > 结束日期时间，可选
    > 
    > 我们拥有该资产交易数据的最后一个日期。
    > 
    > 首次交易日期时间，可选
    > 
    > 我们拥有该资产交易数据的第一个日期。
    > 
    > 交易所字符串
    > 
    > 该资产交易的交易所。
    > 
    > 通知日期时间
    > 
    > 合约持有人可能被迫接受合约资产实物交割的日期。
    > 
    > 到期日期时间
    > 
    > 合约到期日期。
    > 
    > 自动关闭日期时间
    > 
    > 经纪人将自动关闭该合约中任何头寸的日期。
    > 
    > 最小价格变动浮点数
    > 
    > 合约的最小价格变动。
    > 
    > 乘数：浮点数
    > 
    > 该合约代表的底层资产数量。

+   **交易所**（*pd.DataFrame**，*可选*）-

    资产可以交易的交易所。该数据框的列包括：

    > 交易所字符串
    > 
    > 交易所的全名。
    > 
    > 规范名称字符串
    > 
    > 交易所的规范名称。
    > 
    > 国家代码字符串
    > 
    > 交易所的 ISO 3166 alpha-2 国家代码。

+   **根符号** (*pd.DataFrame*, *可选*) –

    期货合约的根符号。这个数据框的列包括：

    > 根符号字符串
    > 
    > 根符号名称。
    > 
    > 根符号标识符整数
    > 
    > 这个根符号的唯一标识符。
    > 
    > 部门字符串，可选
    > 
    > 这个根符号的部门。
    > 
    > 描述字符串，可选
    > 
    > 这个根符号的简短描述。
    > 
    > 交易所字符串
    > 
    > 这个根符号交易的交易所。

+   **股票补充映射** (*pd.DataFrame*, *可选*) – 从任意类型的值到资产的额外映射。

+   **块大小** ([*整数*](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11 中)")*,* *可选*) – 一次写入 SQLite 表的行数。这默认为 sqlite 中默认的绑定参数数量。如果你编译的 sqlite3 有更多或更少的绑定参数，你可能想在这里传递那个值。

### 阅读器

```py
class zipline.data.bcolz_daily_bars.BcolzDailyBarReader(table, read_all_threshold=3000)
```

用于读取由 BcolzDailyOHLCVWriter 编写的原始定价数据的阅读器。

参数：

+   **表** (*bcolz.ctable*) – 包含定价数据的 ctable，其属性对应于下面的属性列表。

+   **读取所有阈值** ([*整数*](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11)")) – 股票数量；低于此数量，数据通过从 carray 中读取每个资产的切片来读取。高于此数量，数据通过将所有资产的数据拉入内存，然后为每个日期和资产对索引到该数组来读取。用于调整使用少量或大量股票时的读取性能。

```py
The table with which this loader interacts contains the following
```

```py
attributes
```

```py
first_row
```

从资产标识符到具有该标识符的数据集中第一行的索引的映射。

类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(在 Python v3.11 中)")

```py
last_row
```

从资产标识符到具有该标识符的数据集中最后一行的索引的映射。

类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(在 Python v3.11 中)")

```py
calendar_offset
```

从资产标识符到数据集中第一行的日历索引的映射。

类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(在 Python v3.11 中)")

```py
start_session_ns
```

这个数据集中使用的第一个会话的纪元纳秒。

类型：

[整数](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11 中)")

```py
end_session_ns
```

这个数据集中使用的最后一个会话的纪元纳秒。

类型：

[整数](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11 中)")

```py
calendar_name
```

使用的交易日历的字符串标识符（例如，“NYSE”）。

类型：

[字符串](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中)")

```py
We use first_row and last_row together to quickly find ranges of rows to
```

```py
load when reading an asset's data into memory.
```

```py
We use calendar_offset and calendar to orient loaded blocks within a
```

```py
range of queried dates.
```

注释

Bcolz CTable 由列和属性组成。这个加载器与之交互的表包含以下列：

[‘开盘’, ‘最高’, ‘最低’, ‘收盘’, ‘成交量’, ‘日期’, ‘标识符’]。

这些列中的数据被解释如下：

+   价格列（‘开盘’, ‘最高’, ‘最低’, ‘收盘’）被解释为 1000 * 交易美元价值。

+   成交量被解释为交易成交量。

+   日期被解释为自 1970 年 1 月 1 日 UTC 午夜以来的秒数。

+   标识符是行的资产标识符。

每个列中的数据按资产分组，然后在每个资产块内按日期排序。

该表旨在表示长时间范围的数据，例如十年的股票数据，因此每个资产块的长度并不相等。这些块被剪辑到每个资产的已知开始和结束日期，以减少需要包含的空值数量，以便制作常规/立方数据集。

当在同一索引上读取开盘、最高、最低、收盘和成交量时，应表示相同的资产和日期。

另请参阅

`zipline.data.bcolz_daily_bars.BcolzDailyBarWriter`

```py
currency_codes(sids)
```

获取请求的 sids 的价格报价货币。

假设 sid 的价格始终以单一货币报价。

参数：

**sids** (*np.array**[**int64**]*) – 需要货币的 sids 数组。

返回值：

**currency_codes** – 用于列出`sids`货币的货币代码数组。实现应为货币未知的 sids 返回 None。

返回类型：

np.array[[object](https://docs.python.org/3/library/functions.html#object "(in Python v3.11)")]

```py
get_last_traded_dt(asset, day)
```

获取`asset`在或之前交易的最新分钟`dt`。

如果在或之前没有交易`dt`，则返回`pd.NaT`。

参数：

+   **asset** (*zipline.asset.Asset*) – 获取最后交易分钟的资产。

+   **dt** (*pd.Timestamp*) – 开始搜索最后交易分钟的时间。

返回值：

**last_traded** – 使用输入 dt 作为视点的给定资产的最后交易 dt。

返回类型：

pd.Timestamp

```py
get_value(sid, dt, field)
```

参数：

+   **sid** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 资产标识符。

+   **day** (*datetime64-like*) – 请求数据的日期的午夜。

+   **colname** (*string*) – 价格字段。例如：(‘open’, ‘high’, ‘low’, ‘close’, ‘volume’)

返回值：

给定 sid 在给定日期的 colname 的现货价格。如果给定的日期和 sid 在股票的日期范围之前或之后，则引发 NoDataOnDate 异常。如果日期在日期范围内，但价格为 0，则返回-1。

返回类型：

[float](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")

```py
property last_available_dt
```

返回值：**dt** – 读者可以提供数据的最后一个会话。 :rtype: pd.Timestamp

```py
load_raw_arrays(columns, start_date, end_date, assets)
```

参数：

+   **columns** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)") *of* [*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – ‘open’, ‘high’, ‘low’, ‘close’, 或 ‘volume’

+   **start_date** (*Timestamp*) – 窗口范围的开始。

+   **end_date** (*Timestamp*) – 窗口范围的结束。

+   **assets** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)") *of* [*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 窗口中的资产标识符。

返回值：

一个列表，每个字段都有一个 ndarrays 条目，形状为(范围内的分钟数, sids)，dtype 为 float64，包含开始和结束 dt 范围内各自字段的值。

返回类型：

[list](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)") of np.ndarray

```py
sid_day_index(sid, day)
```

参数：

+   **sid** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 资产标识符。

+   **日期** (*datetime64-like*) – 请求数据的日期的午夜。

返回：

为给定的 sid 和日期索引数据磁带。如果在给定日期和 sid 之前或之后，则引发 NoDataOnDate 异常。

返回类型：

[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")

```py
class zipline.data.adjustments.SQLiteAdjustmentReader(conn)
```

根据公司行为从 SQLite 数据库加载调整。

期望以 SQLiteAdjustmentWriter 输出的格式编写的数据。

参数：

**连接** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* [*sqlite3.Connection*](https://docs.python.org/3/library/sqlite3.html#sqlite3.Connection "(in Python v3.11)")) – 用于加载数据的连接。

另请参阅

`zipline.data.adjustments.SQLiteAdjustmentWriter`

```py
load_adjustments(dates, assets, should_include_splits, should_include_mergers, should_include_dividends, adjustment_type)
```

从底层调整数据库加载调整对象集合。

参数：

+   **日期** (*pd.DatetimeIndex*) – 需要调整的日期。

+   **资产** (*pd.Int64Index*) – 需要调整的资产。

+   **应包含拆分** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 是否应包含拆分调整。

+   **应包含合并** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 是否应包含合并调整。

+   **应包含股息** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 是否应包含股息调整。

+   **调整类型** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 是否应在输出中包含价格调整、数量调整或两者。

返回：

**调整** – 一个字典，包含从索引到调整对象的价格和/或数量调整映射，以在该索引处应用。

返回类型：

[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[str -> dict[int -> Adjustment]]

```py
unpack_db_to_component_dfs(convert_dates=False)
```

返回调整文件中已知表的集合，以 DataFrame 形式。

参数：

**转换日期** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 默认情况下，日期以自 EPOCH 以来的秒数返回。如果 convert_dates 为 True，则日期列中的所有整数都将转换为日期时间。

返回：

**dfs** – 一个字典，将表名映射到相应表的 DataFrame 版本，其中所有日期列都已从 int 强制转换回 datetime。

返回类型：

dict{str->DataFrame}

```py
class zipline.assets.AssetFinder(engine, future_chain_predicates={'AD': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'BP': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'CD': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'EL': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'GC': functools.partial(<built-in function delivery_predicate>, {'M', 'Z', 'Q', 'V', 'G', 'J'}), 'JY': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'ME': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'PA': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'PL': functools.partial(<built-in function delivery_predicate>, {'J', 'F', 'V', 'N'}), 'SV': functools.partial(<built-in function delivery_predicate>, {'H', 'N', 'Z', 'U', 'K'}), 'XG': functools.partial(<built-in function delivery_predicate>, {'M', 'Z', 'Q', 'V', 'G', 'J'}), 'YS': functools.partial(<built-in function delivery_predicate>, {'H', 'N', 'Z', 'U', 'K'})})
```

AssetFinder 是一个接口，用于访问由`AssetDBWriter`编写的资产元数据数据库。

该类提供了通过唯一整数 id 或符号查找资产的方法。出于历史原因，我们称这些唯一 id 为“sids”。

参数：

+   **engine** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *or* *SQLAlchemy.engine*) – 一个连接到资产数据库的引擎，或者可以被 SQLAlchemy 解析为 URI 的字符串。

+   **future_chain_predicates** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")) – 一个字典，将未来根符号映射到接受参数的谓词函数

+   **be** (*作为参数的合约并返回是否* *或* *不合约应该*) –

+   **链。** (*包含在*) –

另请参阅

`zipline.assets.AssetDBWriter`

```py
property equities_sids
```

资产查找器中所有股票的 sids。

```py
equities_sids_for_country_code(country_code)
```

返回给定国家的所有 sids。

参数：

**country_code** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 一个 ISO 3166 alpha-2 国家代码。

返回：

在这个国家进行交易的 sids。

返回类型：

[tuple](https://docs.python.org/3/library/stdtypes.html#tuple "(in Python v3.11)")[[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")]

```py
equities_sids_for_exchange_name(exchange_name)
```

返回给定 exchange_name 的所有 sids。

参数：

**exchange_name** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) –

返回：

其交易所位于这个国家的 sids。

返回类型：

[tuple](https://docs.python.org/3/library/stdtypes.html#tuple "(in Python v3.11)")[[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")]

```py
property futures_sids
```

资产查找器中所有期货合约的 sids。

```py
get_supplementary_field(sid, field_name, as_of_date)
```

获取资产的补充字段的值。

参数：

+   **sid** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 要查询的资产的 sid。

+   **field_name** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 补充字段的名称。

+   **as_of_date** (*pd.Timestamp**,* *None*) – 返回该日期上的最后一个已知值。如果为 None，则仅当我们只为这个 sid 提供一个值时才返回值。如果为 None 且我们有多个值，则引发 MultipleValuesFoundForSid。

引发：

+   **NoValueForSid** – 如果我们没有这个资产的值，或者在 as_of_date 时不知道值。

+   **MultipleValuesFoundForSid** – 如果我们有这个资产的多个值，并且为 as_of_date 传递了 None。

```py
group_by_type(sids)
```

按资产类型对 sids 列表进行分组。

参数：

**sids** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*[*[*整数*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*]*) –

返回：

**types** – 一个字典，将唯一的资产类型映射到从 sids 中提取的 sids 列表。如果我们无法查找资产，则为其分配一个 None 键。

返回类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[[字符串](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")或无 -> 列表[[整数](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")]]

```py
lifetimes(dates, include_start_date, country_codes)
```

计算指定日期范围内资产 lifetimes 的 DataFrame。

参数：

+   **dates** (*pd.DatetimeIndex*) – 计算 lifetimes 的日期。

+   **include_start_date** ([*布尔值*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) –

    是否将资产在其 start_date 当天视为存活。

    这在回测环境中很有用，其中 lifetimes 用于表示“我是否有该资产在当天早晨的数据？”对于许多金融指标（例如每日收盘价），直到资产的第一天结束时才可获得该资产的数据。

+   **country_codes** (*可迭代*[*[*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*]*) – 要获取 lifetimes 的国家代码。

返回：

**lifetimes** – 一个布尔类型的框架，日期作为索引，资产作为 Int64Index 的列。在 lifetimes.loc[date, asset]处的值为 True，当且仅当资产在 date 存在。如果 include_start_date 为 False，则当 date == asset.start_date 时，lifetimes.loc[date, asset]将为 false。

返回类型：

pd.DataFrame

另请参阅

[`numpy.putmask`](https://numpy.org/doc/stable/reference/generated/numpy.putmask.html#numpy.putmask "(in NumPy v1.25)"), `zipline.pipeline.engine.SimplePipelineEngine._compute_root_mask`

```py
lookup_asset_types(sids)
```

检索一组 sids 的资产类型。

参数：

**sids** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*[*[*整数*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*]*) –

返回：

**types** – 提供的 sids 的资产类型。

返回类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[sid -> 字符串或无]

```py
lookup_future_symbol(symbol)
```

通过符号查找未来合约。

参数：

**symbol** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 所需合约的符号。

返回：

**future** – 由`symbol`引用的未来合约。

返回类型：

未来

引发：

**SymbolNotFound** – 当找不到名为‘symbol’的合约时引发。

```py
lookup_generic(obj, as_of_date, country_code)
```

将对象转换为资产或资产序列。

此方法主要作为实现用户界面 API 的便利工具，该 API 可以处理多种输入类型。在内部代码中，如果我们已经知道输入的预期类型，则不应使用它。

参数：

+   **obj** ([*整数*](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11)")*,* [*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11)")*,* *Asset**,* *ContinuousFuture**, 或* *iterable*) – 要转换为一个或多个资产的对象。整数被解释为 sids。字符串被解释为股票代码。资产和 ContinuousFutures 保持不变。

+   **as_of_date** (*pd.Timestamp* *或* *None*) – 用于消除股票查找歧义的时间戳。与 lookup_symbol 中的语义相同。

+   **国家代码** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中)") *或* *None*) – 用于消除股票查找歧义的 ISO-3166 国家代码。与 lookup_symbol 中的语义相同。

返回值：

**matches, missing** –

`matches`是转换的结果。`missing`是一个列表

包含任何无法解析的值。如果`obj`不是可迭代的，则`missing`将是一个空列表。

返回类型：

[元组](https://docs.python.org/3/library/stdtypes.html#tuple "(在 Python v3.11 中)")

```py
lookup_symbol(symbol, as_of_date, fuzzy=False, country_code=None)
```

通过符号查找股票。

参数：

+   **symbol** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11)")) – 要解析的股票代码。

+   **as_of_date** ([*datetime.datetime*](https://docs.python.org/3/library/datetime.html#datetime.datetime "(在 Python v3.11)") *或* *None*) – 查找此符号的最后所有者，直到这个日期时间。如果`as_of_date`为 None，则只能解析股票，如果只有一个股票曾经拥有该股票代码。

+   **模糊** ([*布尔*](https://docs.python.org/3/library/functions.html#bool "(在 Python v3.11 中)")*,* *可选*) – 是否应使用模糊符号匹配？模糊符号匹配尝试解决股份类别表示的差异。例如，有些人可能将`BRK`的`A`股份类别表示为`BRK.A`，而其他人可能写成`BRK_A`。

+   **country_code** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11)") *或* *None**, *可选*) – 要限制搜索的国家。如果未提供，搜索将跨越所有国家，这增加了查找含糊不清的可能性。

返回：

**股票** – 在给定的`as_of_date`上持有`symbol`的股票，或者如果`as_of_date`为 None，则只有持有`symbol`的股票。

返回类型：

Equity

引发：

+   **SymbolNotFound** – 当没有任何股票持有给定的符号时引发。

+   **MultipleSymbolsFound** – 当没有给出`as_of_date`且超过一个股票持有`symbol`时引发。当`fuzzy=True`且在`as_of_date`上有多个给定`symbol`的候选时也会引发。当没有给出`country_code`且符号在多个国家之间含糊不清时也会引发。

```py
lookup_symbols(symbols, as_of_date, fuzzy=False, country_code=None)
```

通过符号查找股票列表。

等效于：

```py
[finder.lookup_symbol(s, as_of, fuzzy) for s in symbols] 
```

但可能更快，因为重复查找被缓存了。

参数：

+   **symbols** (*sequence**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*]*) – 要解析的 ticker 符号序列。

+   **as_of_date** (*pd.Timestamp*) – 转发到`lookup_symbol`。

+   **fuzzy** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 转发到`lookup_symbol`。

+   **country_code** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* *None**,* *可选*) – 限制搜索的国家。如果不提供，搜索将跨越所有国家，这增加了模糊查找的可能性。

返回：

**equities**

返回类型：

[list](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[Equity]

```py
retrieve_all(sids, default_none=False)
```

检索 sids 中的所有资产。

参数：

+   **sids** (*iterable* *of* [*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 要检索的资产。

+   **default_none** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 如果为 True，对于失败的查找返回 None。如果为 False，引发 SidsNotFound。

返回：

**assets** – 与 sids 长度相同的列表，包含与请求的 sids 对应的 Assets（或 Nones）。

返回类型：

[list](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[Asset or None]

引发：

**SidsNotFound** – 当请求的 sid 未找到且 default_none=False 时。

```py
retrieve_asset(sid, default_none=False)
```

检索给定 sid 的资产。

```py
retrieve_equities(sids)
```

为 sid 列表检索 Equity 对象。

用户通常不需要使用此方法（相反，他们应该更喜欢更通用/友好的 retrieve_assets），但由于它在上游使用，因此它具有文档化的接口和测试。

参数：

**sids** (*iterable**[*[*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*]*) –

返回：

**equities**

返回类型：

[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[int -> Equity]

引发：

**EquitiesNotFound** – 当请求的任何资产未找到时。

```py
retrieve_futures_contracts(sids)
```

为 sid 的可迭代对象检索 Future 对象。

用户通常不需要使用此方法（相反，他们应该更喜欢更通用/友好的 retrieve_assets），但由于它在上游使用，因此它具有文档化的接口和测试。

参数：

**sids** (*iterable**[*[*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*]*) –

返回：

**equities**

返回类型：

[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[int -> Equity]

引发：

**EquitiesNotFound** – 当请求的任何资产未找到时。

```py
property sids
```

资产查找器中的所有 sid。

```py
class zipline.data.data_portal.DataPortal(asset_finder, trading_calendar, first_trading_day, equity_daily_reader=None, equity_minute_reader=None, future_daily_reader=None, future_minute_reader=None, adjustment_reader=None, last_available_session=None, last_available_minute=None, minute_history_prefetch_length=1560, daily_history_prefetch_length=40)
```

接口到 zipline 模拟所需的所有数据。

这由模拟运行器用于回答有关数据的问题，例如获取给定日期的资产价格或服务历史调用。

参数：

+   **资产查找器** (*zipline.assets.assets.AssetFinder*) – 用于解析资产的 AssetFinder 实例。

+   **交易日历** (*zipline.utils.calendar.exchange_calendar.TradingCalendar*) – 提供分钟到会话信息的日历实例。

+   **首个交易日** (*pd.Timestamp*) – 模拟的首个交易日。

+   **股票日读取器** (*BcolzDailyBarReader**,* *可选*) – 股票的日条形图读取器。用于服务日数据回测或分钟回测中的日历史调用。如果没有提供日条形图读取器但提供了分钟条形图读取器，则分钟将汇总以服务日请求。

+   **股票分钟读取器** (*BcolzMinuteBarReader*, *可选*) – 股票的分钟条形图读取器。用于服务分钟数据回测或分钟历史调用。如果没有提供日条形图读取器，则可用于服务日调用。

+   **期货日读取器** (*BcolzDailyBarReader**,* *可选*) – 期货的日条形图读取器。用于服务日数据回测或分钟回测中的日历史调用。如果没有提供日条形图读取器但提供了分钟条形图读取器，则分钟将汇总以服务日请求。

+   **期货分钟读取器** (*BcolzFutureMinuteBarReader*, *可选*) – 期货的分钟条形图读取器。用于服务分钟数据回测或分钟历史调用。如果没有提供日条形图读取器，则可用于服务日调用。

+   **调整读取器** (*SQLiteAdjustmentWriter**,* *可选*) – 调整读取器。用于将拆分、股息和其他调整数据应用于读取器提供的原始数据。

+   **最后可用会话** (*pd.Timestamp*, *可选*) – 会话级数据中可用的最后一个会话。

+   **最后可用分钟** (*pd.Timestamp*, *可选*) – 分钟级数据中可用的最后一分钟。

```py
get_adjusted_value(asset, field, dt, perspective_dt, data_frequency, spot_value=None)
```

返回一个标量值，表示在给定 dt 时所需资产字段的值，已应用调整。

参数：

+   **资产** (*Asset*) – 所需数据的资产。

+   **字段** (*{'开盘'*,*'最高'*,*'最低'*,*'收盘'*,*'成交量'*,*'价格'*,*'最后交易'}*) – 资产的所需字段。

+   **dt** (*pd.Timestamp*) – 所需值的时间戳。

+   **视角时间戳** (*pd.Timestamp*) – 从该时间戳回看数据。

+   **数据频率** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11)")) – 要查询的数据频率；即数据是“每日”还是“分钟”条形图

返回：

**值** – 在 `dt` 时对 `资产` 的给定 `字段` 的值，已知由 `perspective_dt` 应用的任何调整。返回类型基于所请求的 `字段`。如果字段是“开盘”、“最高”、“最低”、“收盘”或“价格”之一，则值将为浮点数。如果 `字段` 是“成交量”，则值将为整数。如果 `字段` 是“最后交易”，则值将为时间戳。

返回类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11)")、[整数](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11)") 或 pd.Timestamp

```py
get_adjustments(assets, field, dt, perspective_dt)
```

返回给定字段和资产列表在 dt 和 perspective_dt 之间的调整列表。

参数：

+   **资产** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11)") *的* *类型资产**，或* *资产*) – 所需调整的资产或资产。

+   **字段** (*{'开盘'**,* *'最高'**,* *'最低'**,* *'收盘'**,* *'成交量'**,* *'价格'**,* *'最后交易'}*) – 资产的所需字段。

+   **dt** (*pd.Timestamp*) – 所需值的时间戳。

+   **perspective_dt** (*pd.Timestamp*) – 从哪个时间戳查看数据。

返回：

**调整** – 对该字段的调整。

返回类型：

[列表](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11)") [调整]

```py
get_current_future_chain(continuous_future, dt)
```

根据连续期货规范，检索给定 dt 的合约的期货链。

返回：

**期货链** – 活跃期货的列表，其中第一个索引是由连续期货定义指定的当前合约，第二个是下一个即将到来的合约，依此类推。

返回类型：

[列表](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11)")[期货]

```py
get_fetcher_assets(dt)
```

返回当前日期定义的资产列表，由提取器数据定义。

返回：

**列表**

返回类型：

资产对象的列表。

```py
get_history_window(assets, end_dt, bar_count, frequency, field, data_frequency, ffill=True)
```

公共 API 方法，返回包含所请求历史窗口的数据框。数据完全调整。

参数：

+   **资产** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11)") *的* *zipline.data.Asset 对象*) – 所需数据的资产。

+   **bar_count** ([*整数*](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11)")) – 所需条形图的数量。

+   **频率** (*字符串*) – “1d” 或 “1m”

+   **字段** (*字符串*) – 资产的所需字段。

+   **数据频率** (*字符串*) – 要查询的数据频率；即数据是“每日”还是“分钟”条形图。

+   **ffill** (*布尔值*) – 前向填充缺失值。仅在字段为“价格”时有效。

返回类型：

包含所请求数据的数据框。

```py
get_last_traded_dt(asset, dt, data_frequency)
```

给定资产和 dt，返回从给定 dt 视角的最后交易 dt。

如果在 dt 上有交易，答案是提供的 dt。

```py
get_scalar_asset_spot_value(asset, field, dt, data_frequency)
```

公共 API 方法，返回一个标量值，表示在给定 dt 时所需资产字段的值。

参数：

+   **assets** (*Asset*) – 所需数据的资产或资产。这不能是任意的 AssetConvertible。

+   **field** (*{'open'**,* *'high'**,* *'low'**,* *'close'**,* *'volume'**,*) – ‘price’, ‘last_traded’} 资产的所需字段。

+   **dt** (*pd.Timestamp*) – 所需值的时间戳。

+   **data_frequency** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要查询的数据频率；即数据是‘每日’还是‘分钟’条形图

返回：

**value** – `field`对于`asset`的即期价值。返回类型基于所请求的`field`。如果字段是‘open’, ‘high’, ‘low’, ‘close’, 或‘price’之一，值将是一个浮点数。如果`field`是‘volume’，值将是一个整数。如果`field`是‘last_traded’，值将是一个时间戳。

返回类型：

[float](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)"), [int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)"), 或 pd.Timestamp

```py
get_splits(assets, dt)
```

返回给定 sids 和给定 dt 的任何分割。

参数：

+   **assets** (*容器*) – 我们想要分割的资产。

+   **dt** (*pd.Timestamp*) – 我们正在检查分割的日期。注意：这预计是 UTC 午夜。

返回：

**splits** – 分割列表，其中每个分割是(资产, 比率)元组。

返回类型：

[list](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[(asset, [float](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)"))]

```py
get_spot_value(assets, field, dt, data_frequency)
```

公共 API 方法，返回一个标量值，表示在给定 dt 时所需资产字段的值。

参数：

+   **assets** (*Asset**,* *ContinuousFuture**, 或* *iterable* *of* *same.*) – 所需数据的资产或资产。

+   **field** (*{'open'**,* *'high'**,* *'low'**,* *'close'**,* *'volume'**,*) – ‘price’, ‘last_traded’} 资产的所需字段。

+   **dt** (*pd.Timestamp*) – 所需值的时间戳。

+   **data_frequency** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要查询的数据频率；即数据是‘每日’还是‘分钟’条形图

返回：

**value** – `field`对于`asset`的即期价值。返回类型基于所请求的`field`。如果字段是‘open’, ‘high’, ‘low’, ‘close’, 或‘price’之一，值将是一个浮点数。如果`field`是‘volume’，值将是一个整数。如果`field`是‘last_traded’，值将是一个时间戳。

返回类型：

[float](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)"), [int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)"), 或 pd.Timestamp

```py
get_stock_dividends(sid, trading_days)
```

返回给定交易范围内特定 sid 的所有股票红利。

参数：

+   **sid** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 应返回其股票红利的资产。

+   **trading_days** (*pd.DatetimeIndex*) – 交易范围。

返回：

+   **list** (*一个包含所有相关属性填充的对象列表。*)

+   *所有时间戳字段都转换为 pd.Timestamps。*

```py
handle_extra_source(source_df, sim_params)
```

额外来源总是有一个 sid 列。

我们将给定数据（通过前向填充）扩展到模拟日期的完整范围，以便在模拟期间快速查找。

```py
class zipline.sources.benchmark_source.BenchmarkSource(benchmark_asset, trading_calendar, sessions, data_portal, emission_rate='daily', benchmark_returns=None)
```

```py
daily_returns(start, end=None)
```

返回给定期间的每日回报。

参数：

+   **start** (*datetime*) – 包含的起始会话标签。

+   **end** (*datetime**,* *可选*) – 包含的结束会话标签。如果不提供，将`start`视为标量键。

返回：

**returns** – 给定期间的回报。索引将是[start, end]范围内的交易日历。如果只提供`start`，则返回那天的标量值。

返回类型：

pd.Series 或 [float](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")

```py
get_range(start_dt, end_dt)
```

查找给定期间的回报。

参数：

+   **start_dt** (*datetime*) – 包含的起始标签。

+   **end_dt** (*datetime*) – 包含的结束标签。

返回：

**returns** – 返回的序列。

返回类型：

pd.Series

另请参阅

`zipline.sources.benchmark_source.BenchmarkSource.daily_returns`

`如果`emission_rate == 'minute'`，此方法期望分钟输入，当`emission_rate == 'daily'`时，期望会话标签。`

```py`
get_value(dt)
```

查找给定 dt 的回报。

参数：

**dt** (*datetime*) – 要查找的标签。

返回：

**returns** – 给定 dt 或会话的回报。

返回类型：

[float](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")

另请参阅

`zipline.sources.benchmark_source.BenchmarkSource.daily_returns`

`如果`emission_rate == 'minute'`，此方法期望分钟输入，当`emission_rate == 'daily'`时，期望会话标签。``

``### 捆绑包

```py
zipline.data.bundles.register(name='__no__default__', f='__no__default__', calendar_name='NYSE', start_session=None, end_session=None, minutes_per_day=390, create_writers=True)
```

注册数据捆绑包摄取函数。

参数：

+   **name** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 捆绑包的名称。

+   **f** (*callable*) –

    摄取函数。此函数将被传递：

    > environmapping
    > 
    > 此操作的环境。
    > 
    > asset_db_writerAssetDBWriter
    > 
    > 用于写入的资产数据库写入器。
    > 
    > minute_bar_writerBcolzMinuteBarWriter
    > 
    > 用于写入的分钟条形写入器。
    > 
    > daily_bar_writerBcolzDailyBarWriter
    > 
    > 用于写入的每日条形写入器。
    > 
    > adjustment_writerSQLiteAdjustmentWriter
    > 
    > 用于写入的调整数据库写入器。
    > 
    > calendartrading_calendars.TradingCalendar
    > 
    > 要摄取的交易日历。
    > 
    > start_sessionpd.Timestamp
    > 
    > 要摄取的数据的第一个会话。
    > 
    > end_sessionpd.Timestamp
    > 
    > 要摄取的数据的最后一个会话。
    > 
    > cacheDataFrameCache
    > 
    > 用于临时存储数据帧的映射对象。在加载失败的情况下，应使用此对象来缓存中间结果。在成功加载后，这将自动清理。
    > 
    > 显示进度布尔值
    > 
    > 尽可能显示当前加载的进度。

+   **日历名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*, *可选*) – 用于对齐包数据的日历的名称。默认为 ‘NYSE’。

+   **开始会话** (*pd.Timestamp*, *可选*) – 我们想要获取数据的第一个会话。如果没有提供，或者日期超出了日历支持的范围，则使用日历的第一个会话。

+   **结束会话** (*pd.Timestamp*, *可选*) – 我们想要获取数据的最后一个会话。如果没有提供，或者日期超出了日历支持的范围，则使用日历的最后一个会话。

+   **每天分钟数** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*, *可选*) – 每个正常交易日的分钟数。

+   **创建写入器** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*, *可选*) – 是否应该为摄取函数创建写入器。在不需要它们的情况下，例如 `quantopian-quandl` 包，可以禁用此功能以进行优化。

注释

此函数可以用作装饰器，例如：

```py
@register('quandl')
def quandl_ingest_function(...):
    ... 
```

另请参阅

`zipline.data.bundles.bundles`

```py
zipline.data.bundles.ingest(name, environ=os.environ, date=None, show_progress=True)
```

为给定的包摄取数据。

参数：

+   **名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 包的名称。

+   **环境** (*映射*, *可选*) – 环境变量。默认为 os.environ。

+   **时间戳** (*datetime*, *可选*) – 用于加载的时间戳。默认情况下，这是当前时间。

+   **资产版本** (*可迭代*[*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*]*, *可选*) – 要降级的资产数据库的版本。

+   **显示进度** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*, *可选*) – 告诉摄取函数在可能的情况下显示进度。

```py
zipline.data.bundles.load(name, environ=os.environ, date=None)
```

加载以前摄取的包。

参数：

+   **名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 包的名称。

+   **环境** (*映射*, *可选*) – 环境变量。默认为 os.environ。

+   **时间戳** (*datetime*, *可选*) – 要查找的数据的时间戳。默认为当前时间。

返回：

**包数据** – 此包的原始数据读取器。

返回类型：

BundleData

```py
zipline.data.bundles.unregister(name)
```

注销一个捆绑包。

参数：

**name** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11)")) – 要注销的捆绑包的名称。

引发：

**UnknownBundle** – 当没有使用给定名称注册的捆绑包时引发。

另请参阅

`zipline.data.bundles.bundles`

```py
zipline.data.bundles.bundles
```

已注册的捆绑包，作为从捆绑包名称到捆绑包数据的映射。此映射是不可变的，只能通过`register()`或`unregister()`更新。``  ``## 风险指标

### 算法状态

```py
class zipline.finance.ledger.Ledger(trading_sessions, capital_base, data_frequency)
```

账本跟踪所有订单和交易，以及投资组合和持仓的当前状态。

```py
portfolio
```

正在管理中的更新后的投资组合。

类型：

zipline.protocol.Portfolio

```py
account
```

正在管理中的更新后的账户。

类型：

zipline.protocol.Account

```py
position_tracker
```

当前持仓集合。

类型：

PositionTracker

```py
todays_returns
```

当天的回报。在分钟排放模式下，这是部分日的回报。在每日排放模式下，这是`daily_returns[session]`。

类型：

[float](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11)")

```py
daily_returns_series
```

每日回报系列。尚未完成的交易日将持有`np.nan`值。

类型：

pd.Series

```py
daily_returns_array
```

作为 ndarray 的每日回报。尚未完成的交易日将持有`np.nan`值。

类型：

np.ndarray

```py
orders(dt=None)
```

检索给定条形图或整个模拟中所有订单的字典形式。

参数：

**dt** (*pd.Timestamp* 或 *None*, *可选*) – 用于查询订单的特定时间戳。如果未传入或明确传入 None，则将返回所有订单。

返回：

**orders** – 订单信息。

返回类型：

[列表](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11)")[[字典](https://docs.python.org/3/library/stdtypes.html#dict "(在 Python v3.11)")]

```py
override_account_fields(settled_cash=sentinel('not_overridden'), accrued_interest=sentinel('not_overridden'), buying_power=sentinel('not_overridden'), equity_with_loan=sentinel('not_overridden'), total_positions_value=sentinel('not_overridden'), total_positions_exposure=sentinel('not_overridden'), regt_equity=sentinel('not_overridden'), regt_margin=sentinel('not_overridden'), initial_margin_requirement=sentinel('not_overridden'), maintenance_margin_requirement=sentinel('not_overridden'), available_funds=sentinel('not_overridden'), excess_liquidity=sentinel('not_overridden'), cushion=sentinel('not_overridden'), day_trades_remaining=sentinel('not_overridden'), leverage=sentinel('not_overridden'), net_leverage=sentinel('not_overridden'), net_liquidation=sentinel('not_overridden'))
```

覆盖`self.account`上的字段。

```py
property portfolio
```

计算当前投资组合。

笔记

这是缓存的，重复访问不会重新计算投资组合，除非投资组合可能已更改。

```py
process_commission(commission)
```

处理佣金。

参数：

**commission** (*zp.Event*) – 正在支付的佣金。

```py
process_dividends(next_session, asset_finder, adjustment_reader)
```

处理下一交易日的股息。

这将为我们赚取下一个交易日的除息日股息，以及支付下一个交易日的支付日股息。

```py
process_order(order)
```

跟踪已下的订单。

参数：

**order** (*zp.Order*) – 要记录的订单。

```py
process_splits(splits)
```

处理一系列拆分，根据需要修改任何持仓。

参数：

**splits** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")***(*[*资产**,* [*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*)**]*) – 一组拆分。每个拆分是一个元组（资产，比率）。

```py
process_transaction(transaction)
```

向账本添加一笔交易，并根据需要更新当前状态。

参数：

**transaction** (*zp.Transaction*) – 要执行的交易。

```py
transactions(dt=None)
```

检索给定时间段内或整个模拟中的所有交易的字典形式。

参数：

**dt** (*pd.Timestamp* *or* *None**,* *optional*) – 要查找交易的具体时间。如果未传递或明确传递 None，则将返回所有交易。

返回：

**transactions** – 交易信息。

返回类型：

[列表](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")]

```py
update_portfolio()
```

强制计算当前投资组合状态。

```py
class zipline.protocol.Portfolio(start_date=None, capital_base=0.0)
```

提供对当前投资组合状态只读访问的对象。

参数：

+   **start_date** (*pd.Timestamp*) – 记录周期的开始日期。

+   **capital_base** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 投资组合的起始价值。这将用作起始现金、当前现金和投资组合价值。

```py
positions
```

包含当前持有仓位信息的类似字典的对象。

类型：

zipline.protocol.Positions

```py
cash
```

投资组合中当前持有的现金金额。

类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")

```py
portfolio_value
```

投资组合持仓的当前清算价值。等于`现金 + 总和(份额 * 价格)`

类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")

```py
starting_cash
```

回测开始时投资组合中的现金金额。

类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")

```py
property current_portfolio_weights
```

通过计算其持有价值除以所有仓位的总价值来计算投资组合中每项资产的权重。

每项股票的价值是其价格乘以持有的股份数量。每份期货合约的价值是其单价乘以持有的股份数量乘以乘数。

```py
class zipline.protocol.Account
```

账户对象跟踪有关交易账户的信息。随着算法运行，这些值会更新，而其键保持不变。如果连接到经纪人，可以使用经纪人报告的交易账户值来更新这些值。

```py
class zipline.finance.ledger.PositionTracker(data_frequency)
```

持有的仓位当前状态。

参数：

**data_frequency** (*{'daily'**,* *'minute'}*) – 模拟的数据频率。

```py
earn_dividends(cash_dividends, stock_dividends)
```

给定一组除息日均为下一个交易日，计算并存储每个股息的现金和/或股票支付金额。

参数：

+   **cash_dividends** (*iterable* *of* *(**资产**,* *金额**,* *支付日期**)* *namedtuples*) –

+   **stock_dividends** (*iterable* *of* *(**asset**,* *payment_asset**,* *ratio**,* *pay_date**)*) – namedtuples。

```py
handle_splits(splits)
```

处理一系列拆分，根据需要修改任何持仓。

参数：

**splits** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")) – 拆分列表。每个拆分是一个 (资产, 比率) 的元组。

返回：

**int** – 持仓。

返回类型：

修改每个持仓后，剩余的现金来自分数股。

```py
pay_dividends(next_trading_day)
```

根据累积的账本记录，返回基于应支付的股息的现金支付。

```py
property stats
```

持仓的当前状态。

返回：

**stats** – 当前的持仓统计数据。

返回类型：

PositionStats

注意

这是缓存的，重复访问不会重新计算统计数据，除非统计数据可能已更改。

```py
class zipline.finance._finance_ext.PositionStats
```

从当前持仓计算出的值。

```py
gross_exposure
```

总持仓暴露度。

类型：

浮点数 64 位

```py
gross_value
```

总持仓价值。

类型：

浮点数 64 位

```py
long_exposure
```

仅多头持仓的暴露度。

类型：

浮点数 64 位

```py
long_value
```

仅多头持仓的价值。

类型：

浮点数 64 位

```py
net_exposure
```

净持仓暴露度。

类型：

浮点数 64 位

```py
net_value
```

净持仓价值。

类型：

浮点数 64 位

```py
short_exposure
```

仅空头持仓的暴露度。

类型：

浮点数 64 位

```py
short_value
```

仅空头持仓的价值。

类型：

浮点数 64 位

```py
longs_count
```

多头持仓的数量。

类型：

整数 64 位

```py
shorts_count
```

空头持仓的数量。

类型：

整数 64 位

```py
position_exposure_array
```

每个持仓的暴露度，顺序与 `position_tracker.positions` 相同。

类型：

np.ndarray[float64]

```py
position_exposure_series
```

每个持仓的暴露度，顺序与 `position_tracker.positions` 相同。索引是每个资产的数字 sid。

类型：

pd.Series[float64]

注意

`position_exposure_array` 和 `position_exposure_series` 共享相同的底层内存。如果你每分钟都在访问，应该优先使用数组接口以获得更好的性能。

`position_exposure_array` 和 `position_exposure_series` 在位置跟踪器下一次更新统计数据时可能会被修改。不要依赖这些对象在访问 `stats` 时保持不变。如果需要冻结这些值，必须进行复制。

### 内置指标

```py
class zipline.finance.metrics.metric.SimpleLedgerField(ledger_field, packet_field=None)
```

每栏或每节发出账本字段的当前值。

参数：

+   **ledger_field** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要读取的账本字段。

+   **packet_field** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *optional*) – 要在数据包中填充的字段名称。如果未提供，将使用 `ledger_field`。

```py
class zipline.finance.metrics.metric.DailyLedgerField(ledger_field, packet_field=None)
```

类似于 `SimpleLedgerField`，但也将当前值放入 `cumulative_perf` 部分。

参数：

+   **ledger_field** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要读取的账本字段。

+   **数据包字段** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 要在数据包中填充的字段名称。如果未提供，将使用`账本字段`。

```py
class zipline.finance.metrics.metric.StartOfPeriodLedgerField(ledger_field, packet_field=None)
```

记录周期开始时账本字段的价值。

参数：

+   **账本字段** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要读取的账本字段。

+   **数据包字段** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 要在数据包中填充的字段名称。如果未提供，将使用`账本字段`。

```py
class zipline.finance.metrics.metric.StartOfPeriodLedgerField(ledger_field, packet_field=None)
```

记录周期开始时账本字段的价值。

参数：

+   **账本字段** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要读取的账本字段。

+   **数据包字段** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 要在数据包中填充的字段名称。如果未提供，将使用`账本字段`。

```py
class zipline.finance.metrics.metric.Returns
```

跟踪算法的每日和累积回报。

```py
class zipline.finance.metrics.metric.BenchmarkReturnsAndVolatility
```

跟踪基准的每日和累积回报以及基准回报的波动性。

```py
class zipline.finance.metrics.metric.CashFlow
```

跟踪每日和累积现金流。

注释

由于历史原因，此字段在数据包中名为‘capital_used’。

```py
class zipline.finance.metrics.metric.Orders
```

跟踪每日订单。

```py
class zipline.finance.metrics.metric.Transactions
```

跟踪每日交易。

```py
class zipline.finance.metrics.metric.Positions
```

跟踪每日持仓。

```py
class zipline.finance.metrics.metric.ReturnsStatistic(function, field_name=None)
```

报告模拟结束标量或从算法返回计算的时间序列的指标。

参数：

+   **函数** (*可调用*) – 对每日回报调用的函数。

+   **字段名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 字段的名称。如果未提供，它将是`function.__name__`。

```py
class zipline.finance.metrics.metric.AlphaBeta
```

将模拟结束的 alpha 和 beta 添加到基准。

```py
class zipline.finance.metrics.metric.MaxLeverage
```

跟踪最大账户杠杆。

### 指标集

```py
zipline.finance.metrics.register(name, function=None)
```

注册新的指标集。

参数：

+   **名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 指标集的名称

+   **函数** (*可调用*) – 产生指标集的可调用对象。

注释

如果只传递`名称`，则可以用作装饰器。

另请参阅

`zipline.finance.metrics.get_metrics_set`, `zipline.finance.metrics.unregister_metrics_set`

```py
zipline.finance.metrics.load(name)
```

返回与给定名称注册的指标集的实例。

返回：

**指标** – 指标集的新实例。

返回类型：

[集合](https://docs.python.org/3/library/stdtypes.html#set "(in Python v3.11)")[指标]

引发：

[**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError "(in Python v3.11)") – 当没有指标集注册到`名称`时引发。

```py
zipline.finance.metrics.unregister(name)
```

注销现有的指标集。

参数：

**名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 指标集的名称

另请参阅

`zipline.finance.metrics.register_metrics_set`

```py
zipline.data.finance.metrics.metrics_sets
```

已注册的指标集作为指标集名称到加载函数的映射。这个映射是不可变的，只能通过 `register()` 或 `unregister()` 更新。

## 实用工具

### 缓存

```py
class zipline.utils.cache.CachedObject(value, expires)
```

一个简单的结构，用于维护带有过期日期的缓存对象。

参数：

+   **值**（[*对象*](https://docs.python.org/3/library/functions.html#object "(在 Python v3.11)")) ——要缓存的对象。

+   **过期**（*类似 datetime*）——值的过期日期。对于过期日期严格大于过期日期的日期，缓存被认为无效。

示例

```py
>>> from pandas import Timestamp, Timedelta
>>> expires = Timestamp('2014', tz='UTC')
>>> obj = CachedObject(1, expires)
>>> obj.unwrap(expires - Timedelta('1 minute'))
1
>>> obj.unwrap(expires)
1
>>> obj.unwrap(expires + Timedelta('1 minute'))
... 
Traceback (most recent call last):
  ...
Expired: 2014-01-01 00:00:00+00:00 
```

```py
class zipline.utils.cache.ExpiringCache(cache=None, cleanup=<function ExpiringCache.<lambda>>)
```

多个 CachedObjects 的缓存，它返回包装的值，或者在值过期时引发并删除 CachedObject。

参数：

+   **缓存**（*类似字典*，*可选*）——一个类似字典的对象实例，至少需要支持：__del__、__getitem__、__setitem__。如果为 None，则默认使用字典。

+   **清理**（*可调用*，*可选*）——一个接受单个参数（缓存对象）的方法，在缓存对象过期时被调用，在删除对象之前。如果不提供，默认为无操作。

示例

```py
>>> from pandas import Timestamp, Timedelta
>>> expires = Timestamp('2014', tz='UTC')
>>> value = 1
>>> cache = ExpiringCache()
>>> cache.set('foo', value, expires)
>>> cache.get('foo', expires - Timedelta('1 minute'))
1
>>> cache.get('foo', expires + Timedelta('1 minute'))
Traceback (most recent call last):
  ...
KeyError: 'foo' 
```

```py
class zipline.utils.cache.dataframe_cache(path=None, lock=None, clean_on_failure=True, serialization='pickle')
```

数据帧的磁盘缓存。

`dataframe_cache` 是一个可变的字符串名称到 pandas DataFrame 对象的映射。这个对象可以用作上下文管理器，在退出时删除缓存目录。

参数：

+   **路径**（[*str*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11)")*，*可选*）——缓存的目录路径。文件将写为 `path/<keyname>`。

+   **锁**（*Lock*，*可选*）——用于多线程/多进程访问缓存的线程锁。如果不提供，将不会使用锁。

+   **在失败时清理**（[*bool*](https://docs.python.org/3/library/functions.html#bool "(在 Python v3.11)")*，*可选*）——如果在上下文管理器中引发异常，是否应该清理目录。

+   **序列化**（*{'msgpack'*，*'pickle:<n>'}*，*可选*）——数据应该如何被序列化。如果传递了 `'pickle'`，可以传递一个可选的 pickle 协议，例如：`'pickle:3'`，表示使用 pickle 协议 3。

注意

`cache[:]` 语法将所有键值对加载到内存中作为一个字典。缓存使用的是临时文件格式，该格式可能会在 zipline 的不同版本之间发生变化。

```py
class zipline.utils.cache.working_file(final_path, *args, **kwargs)
```

一个用于管理临时文件的上下文管理器，如果上下文中没有引发异常，则将文件移动到非临时位置。

参数：

+   **最终路径**（[*str*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11)")) ——提交时移动文件的位置。

+   ***args** ——转发到 NamedTemporaryFile。

+   ****kwargs** ——转发到 NamedTemporaryFile。

注意

如果没有异常，文件在 __exit__ 时被移动。`working_file` 使用 [`shutil.move()`](https://docs.python.org/3/library/shutil.html#shutil.move "(in Python v3.11)") 移动实际文件，这意味着它具有与 [`shutil.move()`](https://docs.python.org/3/library/shutil.html#shutil.move "(in Python v3.11)") 一样强的保证。

```py
class zipline.utils.cache.working_dir(final_path, *args, **kwargs)
```

用于管理临时目录的上下文管理器，如果在上下文中没有引发异常，则将其移动到非临时位置。

参数：

+   **最终路径**（[*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")）- 提交时移动文件的位置。

+   ***args** – 转发到 tmp_dir。

+   ****kwargs** – 转发到 tmp_dir。

笔记

如果没有异常，文件在 __exit__ 时被移动。`working_dir` 使用 `dir_util.copy_tree()` 移动实际文件，这意味着它具有与 `dir_util.copy_tree()` 一样强的保证。

### 命令行

```py
zipline.utils.cli.maybe_show_progress(it, show_progress, **kwargs)
```

可选择为给定的迭代器显示进度条。

参数：

+   **it**（*可迭代*）- 底层迭代器。

+   **show_progress**（[*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")）- 是否显示进度。

+   ****kwargs** – 转发到 click 进度条。

返回：

**itercontext** – 一个上下文管理器，其 enter 是实际使用的迭代器。

返回类型：

上下文管理器

示例

```py
with maybe_show_progress([1, 2, 3], True) as ns:
     for n in ns:
         ... 
```

## 运行回测

函数 `run_algorithm()` 创建一个代表交易策略和执行策略参数的 `TradingAlgorithm` 实例。

```py
zipline.run_algorithm(...)
```

运行交易算法。

参数：

+   **开始**（*datetime*）- 回测的开始日期。

+   **结束**（*datetime*）- 回测的结束日期。

+   **初始化**（*可调用[上下文 -> 无]*）- 用于算法的初始化函数。该函数在回测开始时被调用一次，应被用于设置算法所需的任何状态。

+   **资本基础**（[*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")）- 回测的起始资本。

+   **handle_data**（*可调用（上下文，[BarData）-> 无]*，*可选*）- 用于算法的 handle_data 函数。当 `data_frequency == 'minute'` 时，每分钟被调用一次，或者当 `data_frequency == 'daily'` 时，每天被调用一次。

+   **before_trading_start**（*可调用（上下文，[BarData）-> 无]*，*可选*）- 算法的 before_trading_start 函数。在每个交易日开始前被调用一次（在第一天的 initialize 之后）。

+   **分析**（*可调用[（上下文，pd.DataFrame）-> 无]*，*可选*）- 用于算法的分析函数。该函数在回测结束时被调用一次，并传递上下文和性能数据。

+   **数据频率** (*{'daily', 'minute'}*, *可选*) – 算法运行的数据频率。

+   **包** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*, *可选*) – 用于加载运行回测所需数据的包的名称。默认值为‘quantopian-quandl’。

+   **包时间戳** (*datetime*, *可选*) – 查找包数据的时间戳。默认值为当前时间。

+   **交易日历** (*TradingCalendar*, *可选*) – 用于回测的交易日历。

+   **指标集** (*可迭代[Metric]或[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*, *可选*) – 模拟中要计算的指标集。如果传递的是字符串，则使用`zipline.finance.metrics.load()`解析该集。

+   **基准回报** (*pd.Series*, *可选*) – 用于作为基准的回报序列。

+   **默认扩展** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*, *可选*) – 是否加载默认的 zipline 扩展。该扩展位于`$ZIPLINE_ROOT/extension.py`。

+   **扩展** (*可迭代[[*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*]*, *可选*) – 要加载的任何其他扩展的名称。每个元素可以是像`a.b.c`这样的点分模块路径，也可以是像`a/b/c.py`这样的以`.py`结尾的 python 文件路径。

+   **严格扩展** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*, *可选*) – 如果加载任何扩展失败，是否应使运行失败。如果此项为假，则会发出警告而不是失败。

+   **环境** (*映射[str -> str]*, *可选*) – 要使用的操作系统环境。许多扩展使用此参数来获取参数。默认值为`os.environ`。

+   **交易记录** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") 或 *zipline.finance.blotter.Blotter*, *可选*) – 与该算法一起使用的交易记录。如果作为字符串传递，我们会在`zipline.extensions.register`注册的交易记录构造函数中查找，并调用它，不带任何参数。默认值是一个永远不会取消订单的`zipline.finance.blotter.SimulationBlotter`。

返回值：

**perf** – 算法的日绩效。

返回类型：

pd.DataFrame

另请参阅

`zipline.data.bundles.bundles`

可用的数据包。

## 交易算法 API

以下方法可在`initialize`、`handle_data`和`before_trading_start` API 函数中使用。

在所有列出的函数中，`self`参数指的是当前执行的`TradingAlgorithm`实例。

### 数据对象

```py
class zipline.protocol.BarData
```

提供从算法 API 函数访问每分钟和每日价格/成交量数据的方法。

还提供实用方法来确定资产是否存活，以及它是否有最近的交易数据。

该对象的一个实例作为`data`传递给`handle_data()`和`before_trading_start()`。

参数：

+   **data_portal** (*DataPortal*) – 条形价格数据的提供者。

+   **simulation_dt_func** (*可调用*) – 返回当前模拟时间的函数。这通常绑定到 TradingSimulation 的方法。

+   **数据频率** (*{'分钟'**,* *'每日'}*) – 条形数据的频率；即数据是每日还是分钟条形图

+   **限制条件** (*zipline.finance.asset_restrictions.Restrictions*) – 结合并返回来自多个来源的限制列表信息的对象

```py
can_trade()
```

对于给定的资产或资产的可迭代对象，如果以下所有条件都为真，则返回 True：

1.  资产在当前模拟时间的会话期间存活（如果当前模拟时间不是市场分钟，我们使用下一个会话）。

1.  资产的交易所当前模拟时间或模拟日历的下一个市场分钟是开放的。

1.  资产有已知的最后价格。

参数：

**资产** (*zipline.assets.Asset* *或* *可迭代* *的* *zipline.assets.Asset*) – 应确定可交易性的资产。

注释

上述第二个条件需要进一步解释：

+   如果资产的交易所日历与模拟日历相同，则此条件始终返回 True。

+   如果模拟日历中有市场分钟超出该资产的交易所交易时间（例如，如果模拟运行在 CMES 日历上，但资产是 MSFT，它在 NYSE 交易），在这些分钟内，此条件将返回 False（例如，东部时间工作日早上 3:15，此时 CMES 开放但 NYSE 关闭）。

返回：

**可交易** – 布尔值或布尔序列，指示在当前分钟内请求的资产是否可以交易。

返回类型：

[bool](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)") 或 pd.Series[[bool](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")]

```py
current()
```

返回给定资产在当前模拟时间的给定字段的“当前”值。

参数：

+   **资产** (*zipline.assets.Asset* *或* *可迭代* *的* *zipline.assets.Asset*) – 请求数据的资产。

+   **字段** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* *可迭代*[*[*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*]**) – 请求的数据字段。有效字段名包括：“价格”、“最后交易价”、“开盘价”、“最高价”、“最低价”、“收盘价”和“成交量”。

返回：

**当前值** – 请参见下面的注释。

返回类型：

标量、pandas 系列或 pandas 数据框。

注释

此函数的返回类型取决于其输入的类型：

+   如果请求了单个资产和单个字段，返回值是一个标量（根据字段的不同，可能是浮点数或`pd.Timestamp`）。

+   如果请求了单个资产和字段列表，返回值是一个`pd.Series`，其索引是请求的字段。

+   如果请求了资产列表和单个字段，返回值是一个`pd.Series`，其索引是资产。

+   如果请求了资产列表和字段列表，返回值是一个`pd.DataFrame`。返回的数据框的列将是请求的字段，数据框的索引将是请求的资产。

对于`字段`产生的值如下：

+   请求“价格”将返回资产的最新收盘价，如果本分钟没有交易，则从较早的时间点前向填充。如果没有最新已知值（可能是因为资产从未交易过，或者已经退市），则返回 NaN。如果找到值，并且为了获取该值我们不得不跨越调整边界（如拆分、股息等），则在返回之前将该值调整为当前模拟时间。

+   请求“开盘”、“最高”、“最低”或“收盘”将返回当前分钟的开盘、最高、最低或收盘价。如果本分钟没有交易发生，则返回`NaN`。

+   请求“成交量”将返回当前分钟的成交总量。如果本分钟没有交易发生，则返回 0。

+   请求“最后交易”将返回资产最后一次交易的分钟时间，即使资产已经停止交易。如果没有最后一次已知值，则返回`pd.NaT`。

如果当前模拟时间对于某个资产不是有效的市场时间，我们将使用最近的市场收盘价。

```py
history()
```

返回一个长度为`bar_count`的尾随窗口，包含给定资产、字段和频率的数据，并根据当前模拟时间调整了拆分、股息和合并。

缺失数据的行为与`current()`的注释中描述的行为相同。

参数：

+   **资产** (*zipline.assets.Asset* *或* *zipline.assets.Asset*的可迭代对象) – 请求数据的资产。

+   **字段** (*字符串* *或* *字符串的可迭代对象*) – 请求的数据字段。有效字段名称包括：“价格”、“最后交易”、“开盘”、“最高”、“最低”、“收盘”和“成交量”。

+   **bar_count** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 请求的数据观测值数量。

+   **频率** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 字符串，指示是加载每日数据还是每分钟数据。传递‘1m’表示每分钟数据，‘1d’表示每日数据。

返回值：

**历史记录** – 请参阅下面的注释。

返回类型：

pd.Series 或 pd.DataFrame 或 pd.Panel

注意

此函数的返回类型取决于`assets`和`fields`的类型：

+   如果请求单个资产和单个字段，返回的值是一个长度为`bar_count`的`pd.Series`，其索引为`pd.DatetimeIndex`。

+   如果请求单个资产和多个字段，返回的值是一个具有形状`(bar_count, len(fields))`的`pd.DataFrame`。该数据框的索引将是一个`pd.DatetimeIndex`，其列将是`fields`。

+   如果请求多个资产和单个字段，返回的值是一个具有形状`(bar_count, len(assets))`的`pd.DataFrame`。该数据框的索引将是一个`pd.DatetimeIndex`，其列将是`assets`。

+   如果请求多个资产和多个字段，返回的值是一个具有 pd.MultiIndex 的`pd.DataFrame`，包含`pd.DatetimeIndex`和`assets`的对，而列将包含字段(s)。它具有形状`(bar_count * len(assets), len(fields))`。pd.MultiIndex 的名称是

    > +   `date` 如果频率 == ‘1d’`` 或 `date_time` 如果频率 == ‘1m``, 和
    > +   
    > +   `asset`

如果当前模拟时间不是有效的市场时间，我们使用最后一个市场收盘价代替。

```py
is_stale()
```

对于给定的资产或资产的可迭代对象，如果资产存活且当前模拟时间没有交易数据，则返回 True。

如果资产从未交易过，返回 False。

如果当前模拟时间不是有效的市场时间，我们使用当前时间检查资产是否存活，但我们使用最后一个市场分钟/天进行交易数据检查。

参数：

**assets** (*zipline.assets.Asset* *或* *iterable* *of* *zipline.assets.Asset*) – 应确定其过时性的资产(s)。

返回值：

**is_stale** – 布尔值或布尔序列，指示所请求的资产(s)是否过时。

返回类型：

[bool](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")或 pd.Series[[bool](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")]

### 调度函数

```py
zipline.api.schedule_function(self, func, date_rule=None, time_rule=None, half_days=True, calendar=None)
```

安排一个函数在未来重复调用。

参数：

+   **func** (*可调用*) – 规则触发时要执行的函数。`func`应该与`handle_data`具有相同的签名。

+   **date_rule** (*zipline.utils.events.EventRule**,* *可选*) – 用于确定执行`func`的日期的规则。如果未传递，则函数将在每个交易日运行。

+   **time_rule** (*zipline.utils.events.EventRule**,* *可选*) – 用于确定执行`func`的时间的规则。如果未传递，则函数将在一天的第一个市场分钟的末尾执行。

+   **half_days** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 此规则是否应在半天内触发？默认为 True。

+   **calendar** (*Sentinel**,* *可选*) – 用于计算依赖交易日历的规则的日历。

另请参阅

`zipline.api.date_rules`, `zipline.api.time_rules`

```py
class zipline.api.date_rules
```

用于基于日期的`schedule_function()`规则的工厂。

另请参阅

`schedule_function()`

```py
static every_day()
```

创建一个每天触发的规则。

返回：

**rule**

返回类型：

zipline.utils.events.EventRule

```py
static month_end(days_offset=0)
```

创建一个规则，该规则在每月结束前固定数量的交易天数触发。

参数：

**days_offset** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *optional*) – 触发前距离月末的交易天数。默认值为 0，即在每月最后一天触发。

返回：

**rule**

返回类型：

zipline.utils.events.EventRule

```py
static month_start(days_offset=0)
```

创建一个规则，该规则在每月开始后固定数量的交易天数触发。

参数：

**days_offset** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *optional*) – 每个月触发前等待的交易天数。默认值为 0，即在每月第一个交易日触发。

返回：

**rule**

返回类型：

zipline.utils.events.EventRule

```py
static week_end(days_offset=0)
```

创建一个规则，该规则在每周结束前固定数量的交易天数触发。

参数：

**days_offset** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *optional*) – 触发前距离周末的交易天数。默认值为 0，即在每周最后一个交易日触发。

```py
static week_start(days_offset=0)
```

创建一个规则，该规则在每周开始后固定数量的交易天数触发。

参数：

**days_offset** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *optional*) – 每周触发前等待的交易天数。默认值为 0，即在每周第一个交易日触发。

```py
class zipline.api.time_rules
```

用于基于时间的`schedule_function()`规则的工厂。

另请参阅

`schedule_function()`

```py
every_minute
```

别名为`Always`

```py
static market_close(offset=None, hours=None, minutes=None)
```

创建一个规则，该规则在收盘时固定偏移量触发。

偏移量可以指定为[`datetime.timedelta`](https://docs.python.org/3/library/datetime.html#datetime.timedelta "(in Python v3.11)")，或者指定为小时和分钟数。

参数：

+   **offset** ([*datetime.timedelta*](https://docs.python.org/3/library/datetime.html#datetime.timedelta "(in Python v3.11)")*,* *optional*) – 如果传递，则在收盘时触发的偏移量。必须至少为 1 分钟。

+   **hours** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *optional*) – 如果传递，则在收盘前等待的小时数。

+   **minutes** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 如果传入，这是市场收盘前等待的分钟数。

返回：

**rule**

返回类型：

zipline.utils.events.EventRule

注意

如果没有传入参数，默认的偏移量是市场收盘前一分钟。

如果传入`offset`，则不得传入`hours`和`minutes`。相反，如果传入了`hours`或`minutes`，则不得传入`offset`。

```py
static market_open(offset=None, hours=None, minutes=None)
```

创建一个在市场开盘后固定偏移量触发的规则。

偏移量可以指定为[`datetime.timedelta`](https://docs.python.org/3/library/datetime.html#datetime.timedelta "(in Python v3.11)")，或者指定为小时和分钟数。

参数：

+   **offset** ([*datetime.timedelta*](https://docs.python.org/3/library/datetime.html#datetime.timedelta "(in Python v3.11)")*,* *可选*) – 如果传入，这是触发时距离市场开盘的偏移量。必须至少为 1 分钟。

+   **hours** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 如果传入，这是市场开盘后等待的小时数。

+   **minutes** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 如果传入，这是市场开盘后等待的分钟数。

返回：

**rule**

返回类型：

zipline.utils.events.EventRule

注意

如果没有传入参数，默认的偏移量是市场开盘后一分钟。

如果传入`offset`，则不得传入`hours`和`minutes`。相反，如果传入了`hours`或`minutes`，则不得传入`offset`。

### 订单

```py
zipline.api.order(self, asset, amount, limit_price=None, stop_price=None, style=None)
```

下订单购买固定数量的股票。

参数：

+   **asset** (*Asset*) – 要订购的资产。

+   **amount** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 要订购的股票数量。如果`amount`为正数，这是要购买或平仓的股票数量。如果`amount`为负数，这是要出售或做空的股票数量。

+   **limit_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 订单的限价。

+   **stop_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 订单的止损价。

+   **style** (*ExecutionStyle**,* *可选*) – 订单的执行风格。

返回：

**order_id** – 此订单的唯一标识符，如果没有下订单，则为 None。

返回类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") 或 None

注意

`limit_price` 和 `stop_price` 参数提供了传递常见执行风格的简写方式。传递 `limit_price=N` 等同于 `style=LimitOrder(N)`。类似地，传递 `stop_price=M` 等同于 `style=StopOrder(M)`，而传递 `limit_price=N` 和 `stop_price=M` 等同于 `style=StopLimitOrder(N, M)`。同时传递 `style` 和 `limit_price` 或 `stop_price` 是错误的。

另请参阅

`zipline.finance.execution.ExecutionStyle`, `zipline.api.order_value()`, `zipline.api.order_percent()`

```py
zipline.api.order_value(self, asset, value, limit_price=None, stop_price=None, style=None)
```

下固定金额的订单。

等同于 `order(asset, value / data.current(asset, 'price'))`。

参数：

+   **asset** (*Asset*) – 要订购的资产。

+   **value** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 要交易的`asset`的价值量。买入或卖出的股票数量将等于 `value / current_price`。

+   **limit_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 订单的限价。

+   **stop_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 订单的止损价。

+   **style** (*ExecutionStyle*) – 订单的执行风格。

返回：

**order_id** – 该订单的唯一标识符。

返回类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

注意

有关 `limit_price`、`stop_price` 和 `style` 的更多信息，请参阅 `zipline.api.order()`

另请参阅

`zipline.finance.execution.ExecutionStyle`, `zipline.api.order()`, `zipline.api.order_percent()`

```py
zipline.api.order_percent(self, asset, percent, limit_price=None, stop_price=None, style=None)
```

根据当前投资组合价值的指定百分比，在特定资产中下订单。

参数：

+   **asset** (*Asset*) – 该订单所针对的资产。

+   **percent** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 将投资组合价值的百分比分配给 `asset`。这以小数形式指定，例如：0.50 表示 50%。

+   **limit_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 订单的限价。

+   **stop_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 订单的止损价。

+   **风格** (*ExecutionStyle*) – 订单的执行风格。

Returns:

**订单 ID** – 此订单的唯一标识符。

Return type:

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

Notes

“有关`limit_price`、`stop_price`和`style`的更多信息，请参阅`zipline.api.order()`”

See also

`zipline.finance.execution.ExecutionStyle`, `zipline.api.order()`, `zipline.api.order_value()`

```py
zipline.api.order_target(self, asset, target, limit_price=None, stop_price=None, style=None)
```

“下达订单以调整持仓至目标股数。如果持仓不存在，这相当于下达新订单。如果持仓存在，这相当于下达订单以实现目标股数与当前股数之间的差额。”

Parameters:

+   **资产** (*Asset*) – 此订单所涉及的资产。

+   **目标** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – `asset`的期望股数。

+   **限价** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 订单的限价。

+   **止损价** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 订单的止损价。

+   **风格** (*ExecutionStyle*) – 订单的执行风格。

Returns:

**订单 ID** – 此订单的唯一标识符。

Return type:

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

Notes

`order_target`不考虑任何未完成的订单。例如：

```py
order_target(sid(0), 10)
order_target(sid(0), 10) 
```

“此代码将导致`sid(0)`的 20 股，因为第一个`order_target`调用尚未执行时，第二个`order_target`调用已经执行。”

“有关`limit_price`、`stop_price`和`style`的更多信息，请参阅`zipline.api.order()`”

See also

`zipline.finance.execution.ExecutionStyle`, `zipline.api.order()`, `zipline.api.order_target_percent()`, `zipline.api.order_target_value()`

```py
zipline.api.order_target_value(self, asset, target, limit_price=None, stop_price=None, style=None)
```

下订单以调整持仓至目标价值。如果持仓不存在，这相当于下新订单。如果持仓已存在，这相当于下订单以弥补目标价值与当前价值之间的差额。如果所订购的资产是期货，则计算的“目标价值”实际上是目标风险敞口，因为期货没有“价值”。

参数：

+   **资产** (*资产*) – 此订单所针对的资产。

+   **目标** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – `资产`的期望总价值。

+   **限价** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 订单的限价。

+   **止损价** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 订单的止损价。

+   **风格** (*执行风格*) – 订单的执行风格。

返回：

**订单 ID** – 此订单的唯一标识符。

返回类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

注意

`order_target_value` 不考虑任何未完成订单。例如：

```py
order_target_value(sid(0), 10)
order_target_value(sid(0), 10) 
```

这段代码将导致`sid(0)`的 20 美元，因为第一次调用`order_target_value`时，第二次`order_target_value`调用尚未完成。

有关`limit_price`、`stop_price`和`style`的更多信息，请参阅 `zipline.api.order()`

另请参阅

`zipline.finance.execution.ExecutionStyle`, `zipline.api.order()`, `zipline.api.order_target()`, `zipline.api.order_target_percent()`

```py
zipline.api.order_target_percent(self, asset, target, limit_price=None, stop_price=None, style=None)
```

下订单以调整持仓至当前资产组合价值的目标百分比。如果持仓不存在，这相当于下新订单。如果持仓已存在，这相当于下订单以弥补目标百分比与当前百分比之间的差额。

参数：

+   **资产** (*资产*) – 此订单所针对的资产。

+   **目标** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 希望分配给`资产`的资产组合价值的百分比。这以小数形式指定，例如：0.50 表示 50%。

+   **限价** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 订单的限价。

+   **止损价** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 订单的止损价。

+   **样式** (*执行样式*) – 订单的执行样式。

返回：

**订单 ID** – 此订单的唯一标识符。

返回类型：

[字符串](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

注意

`order_target_value` 不考虑任何未成交的订单。例如：

```py
order_target_percent(sid(0), 10)
order_target_percent(sid(0), 10) 
```

这段代码将导致 20%的资产分配给 sid(0)，因为在第二次调用`order_target_percent`时，第一次调用`order_target_percent`尚未成交。

有关`限价`、`止损价`和`样式`的更多信息，请参阅`zipline.api.order()`

另请参阅

`zipline.finance.execution.ExecutionStyle`, `zipline.api.order()`, `zipline.api.order_target()`, `zipline.api.order_target_value()`

```py
class zipline.finance.execution.ExecutionStyle
```

订单执行样式的基类。

```py
property exchange
```

此订单应发送至的交易所。

```py
abstract get_limit_price(is_buy)
```

获取此订单的限价。返回值为 None 或一个大于等于 0 的数值。

```py
abstract get_stop_price(is_buy)
```

获取此订单的止损价。返回值为 None 或一个大于等于 0 的数值。

```py
class zipline.finance.execution.MarketOrder(exchange=None)
```

执行样式，订单应以当前市场价格成交。

这是使用`order()`下达的订单的默认设置。

```py
class zipline.finance.execution.LimitOrder(limit_price, asset=None, exchange=None)
```

执行样式，订单应以等于或优于指定限价的价格成交。

参数：

**限价** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 买入的最高价格，或卖出的最低价格，订单应在此价格成交。

```py
class zipline.finance.execution.StopOrder(stop_price, asset=None, exchange=None)
```

执行样式，表示如果市场价格达到阈值，则应下达市价订单。

参数：

**止损价** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 应下达订单的价格阈值。对于卖出，如果市场价格低于此值，则下达订单。对于买入，如果市场价格高于此值，则下达订单。

```py
class zipline.finance.execution.StopLimitOrder(limit_price, stop_price, asset=None, exchange=None)
```

执行样式，表示如果市场价格达到阈值，则应下达限价订单。

参数：

+   **限价** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 买入的最高价格，或卖出的最低价格，订单应在此价格或更优价格成交。

+   **stop_price** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 应下达订单的价格阈值。对于卖出，如果市场价格低于此值，将下达订单。对于买入，如果市场价格高于此值，将下达订单。

```py
zipline.api.get_order(self, order_id)
```

根据订单函数返回的订单 id 查找订单。

参数：

**order_id** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 订单的唯一标识符。

返回：

**order** – 订单对象。

返回类型：

订单

```py
zipline.api.get_open_orders(self, asset=None)
```

检索所有当前的未结订单。

参数：

**asset** (*Asset*) – 如果传递且不为 None，则仅返回给定资产的未结订单，而不是所有未结订单。

返回：

**open_orders** – 如果没有传递资产，这将返回一个字典，将资产映射到包含该资产所有未结订单的列表。如果传递了资产，则这将返回该资产的未结订单列表。

返回类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[[列表](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[Order]] 或 [列表](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[Order]

```py
zipline.api.cancel_order(self, order_param)
```

取消一个未结订单。

参数：

**order_param** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* *Order*) – 要取消的 order_id 或订单对象。

#### 订单取消政策

```py
zipline.api.set_cancel_policy(self, cancel_policy)
```

为模拟设置订单取消政策。

参数：

**cancel_policy** (*CancelPolicy*) – 要使用的取消政策。

另请参阅

`zipline.api.EODCancel`, `zipline.api.NeverCancel`

```py
class zipline.finance.cancel_policy.CancelPolicy
```

抽象取消政策接口。

```py
abstract should_cancel(event)
```

是否应该取消所有未结订单？

参数：

**event** (*枚举值*) –

事件类型之一：

+   `zipline.gens.sim_engine.BAR`

+   `zipline.gens.sim_engine.DAY_START`

+   `zipline.gens.sim_engine.DAY_END`

+   `zipline.gens.sim_engine.MINUTE_END`

返回：

**should_cancel** – 是否应取消所有未结订单？

返回类型：

[布尔值](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")

```py
zipline.api.EODCancel(warn_on_cancel=True)
```

该政策在一天结束时取消未结订单。目前，Zipline 仅将此政策应用于每分钟模拟。

参数：

**warn_on_cancel** ([*布尔值*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 如果这导致订单被取消，是否应发出警告？

```py
zipline.api.NeverCancel()
```

订单不会自动取消。

### 资产

```py
zipline.api.symbol(self, symbol_str, country_code=None)
```

通过其股票代码查找股票。

参数：

+   **symbol_str** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要查找的股票的代码。

+   **country_code** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *or* *None**,* *optional*) – 限制符号搜索的国家代码。

返回：

**equity** – 在当前符号查找日期持有股票代码的股票。

返回类型：

zipline.assets.Equity

引发：

**SymbolNotFound** – 当符号在当前查找日期未被持有时引发。

另请参阅

`zipline.api.set_symbol_lookup_date()`

```py
zipline.api.symbols(self, *args, **kwargs)
```

查找多个股票作为列表。

参数：

+   ***args** (*iterable**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*]*) – 要查询的股票代码。

+   **country_code** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *or* *None**,* *optional*) – 限制符号搜索的国家代码。

返回：

**equities** – 在当前符号查找日期持有给定股票代码的股票。

返回类型：

[list](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[zipline.assets.Equity]

引发：

**SymbolNotFound** – 当其中一个符号在当前查找日期未被持有时引发。

另请参阅

`zipline.api.set_symbol_lookup_date()`

```py
zipline.api.future_symbol(self, symbol)
```

使用给定符号查找期货合约。

参数：

**symbol** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 所需合约的符号。

返回：

**future** – 以`symbol`名称交易的期货。

返回类型：

zipline.assets.Future

引发：

**SymbolNotFound** – 当未找到名为‘symbol’的合约时引发。

```py
zipline.api.set_symbol_lookup_date(self, dt)
```

设置符号解析为其资产的日期（符号可能在不同时间映射到不同的公司或底层资产）

参数：

**dt** (*datetime*) – 新的符号查找日期。

```py
zipline.api.sid(self, sid)
```

通过其唯一资产标识符查找资产。

参数：

**sid** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 标识资产的唯一整数。

返回：

**asset** – 具有给定`sid`的资产。

返回类型：

zipline.assets.Asset

引发：

**SidsNotFound** – 当请求的`sid`未映射到任何资产时。

### 交易控制

Zipline 提供交易控制以确保算法按预期执行。这些函数有助于保护算法免受意外行为的不良后果，尤其是在使用真实资金进行交易时。

```py
zipline.api.set_do_not_order_list(self, restricted_list, on_error='fail')
```

对可以下单的资产设置限制。

参数：

**restricted_list** (*container***[*Asset**]**,* *SecurityList*) – 不能下单的资产。

```py
zipline.api.set_long_only(self, on_error='fail')
```

设置规则，指定此算法不能采取空头头寸。

```py
zipline.api.set_max_leverage(self, max_leverage)
```

为算法的最大杠杆设置限制。

参数：

**最大杠杆** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 算法的最大杠杆。如果未提供，则没有最大值。

```py
zipline.api.set_max_order_count(self, max_count, on_error='fail')
```

为一天内可以下订单的数量设置限制。

参数：

**最大订单数** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 任何单个交易日可以下订单的最大数量。

```py
zipline.api.set_max_order_size(self, asset=None, max_shares=None, max_notional=None, on_error='fail')
```

为 sid 的任何单个订单设置股份数量和/或美元价值的限制。这些限制被视为绝对值，并在算法尝试为 sid 下订单时执行。

如果算法尝试下订单，这将导致超过这些限制之一，则引发 TradingControlException。

参数：

+   **资产** (*Asset**,* *可选*) – 如果提供，这仅在给定资产的持仓上设置守卫。

+   **最大股份** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 一次可以下订单的最大股份数量。

+   **最大名义价值** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 一次可以下订单的最大价值。

```py
zipline.api.set_max_position_size(self, asset=None, max_shares=None, max_notional=None, on_error='fail')
```

为给定 sid 设置持有的股份数量和/或美元价值的限制。这些限制被视为绝对值，并在算法尝试为 sid 下订单时执行。这意味着由于拆分/股息，可能会持有超过最大股份数量，由于价格改善，可能会持有超过最大名义价值。

如果算法尝试下订单，这将导致股份/美元价值的绝对值增加超过这些限制之一，则引发 TradingControlException。

参数：

+   **资产** (*Asset**,* *可选*) – 如果提供，这仅在给定资产的持仓上设置守卫。

+   **最大股份** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 持有资产的最大股份数量。

+   **最大名义价值** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 持有资产的最大价值。

### 模拟参数

```py
zipline.api.set_benchmark(self, benchmark)
```

设置基准资产。

参数：

**基准** (*zipline.assets.Asset*) – 设置为新基准的资产。

注意

对于新基准资产支付的任何股息将自动再投资。

#### 佣金模型

```py
zipline.api.set_commission(self, us_equities=None, us_futures=None)
```

设置模拟的佣金模型。

参数：

+   **美国股票** (*EquityCommissionModel*) – 用于交易美国股票的佣金模型。

+   **美国期货** (*FutureCommissionModel*) – 用于交易美国期货的佣金模型。

注意

此函数只能在`initialize()`期间调用。

另请参阅

`zipline.finance.commission.PerShare`, `zipline.finance.commission.PerTrade`, `zipline.finance.commission.PerDollar`

```py
class zipline.finance.commission.CommissionModel
```

佣金模型的抽象基类。

佣金模型负责接受订单/交易对，并计算在每次交易中应向算法账户收取多少佣金。

要实现一个新的佣金模型，创建一个`CommissionModel`的子类，并实现`calculate()`方法。

```py
abstract calculate(order, transaction)
```

计算由于`transaction`而对`order`收取的佣金金额。

参数：

+   **订单** (*zipline.finance.order.Order*) –

    正在处理中的订单。

    `order`的`commission`字段是一个浮点数，表示该订单上已收取的佣金金额。

+   **交易** (*zipline.finance.transaction.Transaction*) – 正在处理的交易。如果某个 bar 中没有足够的成交量来满足订单中请求的全部数量，单个订单可能会产生多次交易。

返回：

**已收取金额** – 我们应该归因于该订单的额外佣金，以美元计。

返回类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")

```py
class zipline.finance.commission.PerShare(cost=0.001, min_trade_cost=0.0)
```

根据每股的成本计算交易佣金，可选择每笔交易的最小成本。

参数：

+   **成本** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")*,* *可选*) – 每笔交易的股票佣金金额。默认是每股十分之一美分。

+   **最小交易成本** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")*,* *可选*) – 每笔交易支付的佣金最小金额。默认没有最小值。

注意

这是 zipline 默认的股票佣金模型。

```py
class zipline.finance.commission.PerTrade(cost=0.0)
```

根据每笔交易的成本计算交易佣金。

对于需要多次成交的订单，全额佣金将计入首次成交。

参数：

**成本** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")*,* *可选*) – 每笔股票交易支付的佣金固定金额。

```py
class zipline.finance.commission.PerDollar(cost=0.0015)
```

通过应用每笔交易固定成本来模拟佣金。

参数：

**成本** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")*,* *可选*) – 每笔股票交易支付的佣金固定金额。默认是每笔交易 0.0015 美元的佣金。

#### 滑点模型

```py
zipline.api.set_slippage(self, us_equities=None, us_futures=None)
```

设置模拟的滑点模型。

参数：

+   **us_equities** (*EquitySlippageModel*) – 用于交易美国股票的滑点模型。

+   **us_futures** (*FutureSlippageModel*) – 用于交易美国期货的滑点模型。

注意

该函数只能在`initialize()`期间调用。

另请参阅

`zipline.finance.slippage.SlippageModel`

```py
class zipline.finance.slippage.SlippageModel
```

滑点模型的抽象基础类。

滑点模型负责模拟中订单成交的比率和价格。

要实现一个新的滑点模型，创建一个`SlippageModel`的子类并实现`process_order()`。

```py
process_order(data, order)
```

```py
volume_for_bar
```

当前分钟内已经为正在成交的资产成交的股数。该属性由基础类自动维护。如果一个资产有多个开放订单，子类可以使用它来跟踪总成交数量。

类型：

[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")

注意

定义自己构造函数的子类应该在执行其他初始化之前调用`super(<subclass name>, self).__init__()`。

```py
abstract process_order(data, order)
```

计算当前分钟内为`order`成交的股数和价格。

参数：

+   **data** (*zipline.protocol.BarData*) – 给定 bar 的数据。

+   **order** (*zipline.finance.order.Order*) – 要模拟的订单。

返回：

+   **execution_price** (*float*) – 成交价格。

+   **execution_volume** (*int*) – 应该成交的股数。必须在`0`和`order.amount - order.filled`之间。如果成交的数量少于剩余数量，`order`将保持开放状态，并在下一分钟再次传递给此方法。

引发：

**zipline.finance.slippage.LiquidityExceeded** – 如果在当前 bar 期间不应该再处理当前资产的订单，可能会引发此异常。

注意

在调用此方法之前，`volume_for_bar`将被设置为当前分钟内已经为`order.asset`成交的股数。

`process_order()` 基础类不会在历史成交量为零的 bar 上调用。

```py
class zipline.finance.slippage.FixedSlippage(spread=0.0)
```

假设所有资产具有固定大小价差的简单模型。

参数：

**spread** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 所有资产假设的价差大小。买单将在`close + (spread / 2)`时成交。卖单将在`close - (spread / 2)`时成交。

注意

该模型不对填充的大小设置限制。对于资产的订单，只要在订单的资产中发生任何交易活动，订单就会立即被填充，即使订单的大小大于历史交易量。

```py
class zipline.finance.slippage.VolumeShareSlippage(volume_limit=0.025, price_impact=0.1)
```

将模型滑点视为历史交易量百分比的二次函数。

买入的订单将以以下价格填充：

```py
price * (1 + price_impact * (volume_share ** 2)) 
```

卖出的订单将以以下价格填充：

```py
price * (1 - price_impact * (volume_share ** 2)) 
```

其中`价格`是条形的收盘价，`volume_share`是填充的每分钟交易量的百分比，最多不超过`volume_limit`。

参数：

+   **volume_limit** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 每个条形中可以填充的历史交易量的最大百分比。0.5 表示历史交易量的 50%。1.0 表示 100%。默认值为 0.025（即，2.5%）。

+   **price_impact** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 价格影响的缩放系数。较大的值将导致更多的模拟价格影响。较小的值将导致较少的模拟价格影响。默认值为 0.1。

### Pipeline

有关更多信息，请参阅 Pipeline API

```py
zipline.api.attach_pipeline(self, pipeline, name, chunks=None, eager=True)
```

注册一个管道，以便在每天开始时进行计算。

参数：

+   **pipeline** (*Pipeline*) – 要计算的管道。

+   **名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 管道的名称。

+   **chunks** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)") *或* *迭代器**,* *可选*) – 要计算管道结果的天数。增加此数字将使获取第一个结果的时间更长，但可能会改善模拟的总运行时间。如果传递了迭代器，我们将根据迭代器的值运行分块。默认值为 True。

+   **eager** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 是否在 before_trading_start 之前计算此管道。

返回：

**pipeline** – 返回未更改的附加管道。

返回类型：

Pipeline

另请参阅

`zipline.api.pipeline_output()`

```py
zipline.api.pipeline_output(self, name)
```

获取通过名称`名称`附加的管道的结果。

参数：

**名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 用于获取结果的管道的名称。

返回：

**结果** – 包含当前模拟日期请求的管道结果的 DataFrame。

返回类型：

pd.DataFrame

引发：

**NoSuchPipeline** – 当未注册具有名称名称的管道时引发。

另请参阅

`zipline.api.attach_pipeline()`, `zipline.pipeline.engine.PipelineEngine.run_pipeline()`

### 杂项

```py
zipline.api.record(self, *args, **kwargs)
```

每天跟踪和记录值。

参数：

****kwargs** – 要记录的名称和值。

注释

这些值将出现在性能数据包和传递给`analyze`并从`run_algorithm()`返回的性能数据框中。

```py
zipline.api.get_environment(self, field='platform')
```

查询执行环境。

参数：

+   **field** (*{'platform'**,* *'arena'**,* *'data_frequency'**,* *'start'**,* *'end'**,*) –

+   **'capital_base'** –

+   **'platform'** –

+   **'*'** –

+   **meanings** (*要查询的字段。选项具有以下*) –

+   **arena** (*-*) – 模拟参数中的竞技场。这通常将是`'backtest'`，但某些系统可能使用它来区分回测和实时交易。

+   **数据频率** (*-*) – 数据频率告诉算法它是使用日数据还是分钟数据运行。

+   **开始** (*-*) – 模拟的开始日期。

+   **结束** (*-*) – 模拟的结束日期。

+   **capital_base** (*-*) – 模拟的起始资本。

+   **-平台** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 代码运行的平台。默认情况下，这将是字符串‘zipline’。这可以让算法知道它们是否在 Quantopian 平台上运行。

+   ***** (*-*) – 返回字典中的所有字段。

返回：

**val** – 查询字段的值。有关更多信息，请参见上文。

返回类型：

任何

引发：

[**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError "(in Python v3.11)") – 当`field`不是有效选项时引发。

```py
zipline.api.fetch_csv(self, url, pre_func=None, post_func=None, date_column='date', date_format=None, timezone='UTC', symbol=None, mask=True, symbol_column=None, special_params_checker=None, country_code=None, **kwargs)
```

从远程 URL 获取 CSV 文件并注册数据，以便可以从`data`对象查询数据。

参数：

+   **url** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要加载的 CSV 文件的 URL。

+   **pre_func** (*callable**[**pd.DataFrame -> pd.DataFrame**]**,* *可选*) – 允许在日期解析或符号映射之前对从 fetch_csv 返回的原始数据进行预处理的回调。

+   **post_func** (*callable**[**pd.DataFrame -> pd.DataFrame**]**,* *可选*) – 在日期和符号映射后允许对数据进行后处理的回调。

+   **date_column** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 预处理数据框中包含日期时间信息的列的名称，用于映射数据。

+   **日期格式** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – `date_column`中日期的格式。如果未提供，`fetch_csv`将尝试推断格式。有关此字符串格式的信息，请参阅[`pandas.read_csv()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html#pandas.read_csv "(in pandas v2.0.3)")。

+   **时区** (*tzinfo* 或 [*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 用于`date_column`中日期时间的时区。

+   **符号** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 如果数据是关于新资产或指数的，则此字符串将用于在`data`中标识值的名称。例如，可以使用`fetch_csv`加载 VIX 的数据，那么这个字段可以是字符串`'VIX'`。

+   **掩码** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 删除无法进行符号映射的任何行。

+   **符号列** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 如果数据正在为每个资产附加一些新属性，则此参数是预处理数据框中包含符号的列的名称。这将连同日期信息一起用于在资产查找器中映射 sid。

+   **国家代码** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 用于消除符号查找歧义的国家代码。

+   ****kwargs** – 转发到[`pandas.read_csv()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html#pandas.read_csv "(in pandas v2.0.3)")。

返回值：

**csv_ 数据源** – 将从指定 URL 拉取数据的请求源。

返回类型：

zipline.sources.requests_csv.PandasRequestsCSV

### 数据对象

```py
class zipline.protocol.BarData
```

提供从算法 API 函数访问每分钟和每日价格/成交量数据的方法。

还提供了实用方法来确定资产是否存活，以及它是否有最近的交易数据。

此对象的实例作为`data`传递给`handle_data()`和`before_trading_start()`。

参数：

+   **数据门户** (*DataPortal*) – 提供条形定价数据。

+   **simulation_dt_func** (*可调用*) – 返回当前模拟时间的函数。这通常绑定到 TradingSimulation 的方法。

+   **数据频率** (*{'minute'**,* *'daily'}*) – 条形数据的频率；即数据是每日还是分钟条形

+   **限制** (*zipline.finance.asset_restrictions.Restrictions*) – 结合并返回来自多个来源的限制列表信息的对象

```py
can_trade()
```

对于给定资产或资产的可迭代对象，如果以下所有条件都为真，则返回 True：

1.  资产在当前模拟时间的会话期间存活（如果当前模拟时间不是市场分钟，我们使用下一个会话）。

1.  资产的交易所当前模拟时间或模拟日历的下一个市场分钟是开放的。

1.  该资产有一个已知的最后价格。

参数：

**资产** (*zipline.assets.Asset* *或* *可迭代* *的* *zipline.assets.Asset*) – 应确定可交易性的资产。

注释

上述第二个条件需要进一步解释：

+   如果资产的交易所日历与模拟日历相同，则此条件始终返回 True。

+   如果模拟日历中有市场分钟不在该资产的交易所交易时间内（例如，如果模拟运行在 CMES 日历上，但资产是 MSFT，它在 NYSE 交易），在这些分钟内，此条件将返回 False（例如，东部时间工作日早上 3:15，此时 CMES 开放但 NYSE 关闭）。

返回值：

**可交易** – 布尔值或布尔序列，指示在当前分钟内请求的资产是否可交易。

返回类型：

[bool](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)") 或 pd.Series[[bool](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")]

```py
current()
```

返回在当前模拟时间给定资产的给定字段的“当前”值。

参数：

+   **资产** (*zipline.assets.Asset* *或* *可迭代* *的* *zipline.assets.Asset*) – 请求数据的资产。

+   **字段** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* *可迭代**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*]**.*) – 请求的数据字段。有效字段名称包括：“价格”、“最后交易”、“开盘”、“最高”、“最低”、“收盘”和“成交量”。

返回值：

**当前值** – 参见下面的注释。

返回类型：

标量、pandas Series 或 pandas DataFrame。

注释

此函数的返回类型取决于其输入的类型：

+   如果请求单个资产和一个字段，返回值是一个标量（根据字段不同，可能是浮点数或`pd.Timestamp`）。

+   如果请求单个资产和一组字段，返回值是一个`pd.Series`，其索引是请求的字段。

+   如果请求一组资产和一个字段，返回值是一个`pd.Series`，其索引是资产。

+   如果请求一组资产和一组字段，返回值是一个`pd.DataFrame`。返回的框架的列将是请求的字段，框架的索引将是请求的资产。

为`fields`生成的值如下：

+   请求“价格”会得到该资产的最新收盘价，如果该分钟没有交易，则向前填充更早一分钟的价格。如果没有最新值（可能是因为该资产从未交易过，或者已经退市），则返回 NaN。如果找到值，并且我们必须跨越调整边界（拆分、股息等）才能得到它，则在返回之前将值调整为当前模拟时间。

+   请求“开盘”、“最高”、“最低”或“收盘”会得到当前分钟的开盘、最高、最低或收盘价。如果该分钟没有交易发生，则返回`NaN`。

+   请求“成交量”会得到当前分钟的成交量。如果该分钟没有交易发生，则返回 0。

+   请求“last_traded”会得到该资产最后一次交易的分钟时间，即使该资产已经停止交易。如果没有最新值，则返回`pd.NaT`。

如果当前模拟时间对于某个资产不是有效的市场时间，我们将使用最近的市场收盘价代替。

```py
history()
```

返回给定资产、字段和频率的长度为`bar_count`的尾随窗口数据，根据当前模拟时间调整拆分、股息和合并。

缺失数据的语义与`current()`的注释中描述的相同。

参数：

+   **assets** (*zipline.assets.Asset* *或* *iterable* *的* *zipline.assets.Asset*) – 请求数据的资产。

+   **fields** (*字符串* *或* *iterable* *的* *字符串*) – 请求的数据字段。有效字段名称包括：“价格”、“last_traded”、“开盘”、“最高”、“最低”、“收盘”和“成交量”。

+   **bar_count** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 请求的数据观测值数量。

+   **frequency** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 字符串，指示是否加载每日或每分钟数据观测值。传递‘1m’表示每分钟数据，‘1d’表示每日数据。

返回：

**history** – 请参阅下面的注释。

返回类型：

pd.Series 或 pd.DataFrame 或 pd.Panel

注意

此函数的返回类型取决于`assets`和`fields`的类型：

+   如果请求了单个资产和一个字段，返回值是一个长度为`bar_count`的`pd.Series`，其索引是`pd.DatetimeIndex`。

+   如果请求了单个资产和多个字段，返回值是一个具有形状`(bar_count, len(fields))`的`pd.DataFrame`。该数据框的索引将是一个`pd.DatetimeIndex`，其列将是`fields`。

+   如果请求了多个资产和一个字段，返回值是一个具有形状`(bar_count, len(assets))`的`pd.DataFrame`。该数据框的索引将是一个`pd.DatetimeIndex`，其列将是`assets`。

+   如果请求了多个资产和多个字段，则返回值是一个 `pd.DataFrame`，其中包含一个包含 `pd.DatetimeIndex` 和 `assets` 对的 pd.MultiIndex，而列将包含字段(s)。它具有形状 `(bar_count * len(assets), len(fields))`。pd.MultiIndex 的名称是

    > +   `date` 如果频率 == ‘1d’`` 或 `date_time` 如果频率 == ‘1m``, 和
    > +   
    > +   `资产`

如果当前模拟时间不是有效的市场时间，我们使用上次市场收盘价代替。

```py
is_stale()
```

对于给定的资产或资产的可迭代对象，如果资产存活且当前模拟时间没有交易数据，则返回 True。

如果资产从未交易过，则返回 False。

如果当前模拟时间不是有效的市场时间，我们使用当前时间来检查资产是否存活，但我们使用最后一个市场分钟/日来进行交易数据检查。

参数：

**assets** (*zipline.assets.Asset* *或* *iterable* *of* *zipline.assets.Asset*) – 应确定其陈旧性的资产(s)。

返回：

**is_stale** – 布尔值或布尔序列，指示请求的资产(s)是否陈旧。

返回类型：

[bool](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)") 或 pd.Series[[bool](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")]

### 调度函数

```py
zipline.api.schedule_function(self, func, date_rule=None, time_rule=None, half_days=True, calendar=None)
```

安排一个函数在未来重复调用。

参数：

+   **func** (*可调用*) – 当规则触发时要执行的函数。`func` 应该与 `handle_data` 具有相同的签名。

+   **date_rule** (*zipline.utils.events.EventRule*, *可选*) – 执行 `func` 的日期规则。如果未传递，则该函数将每天运行。

+   **time_rule** (*zipline.utils.events.EventRule*, *可选*) – 执行 `func` 的时间规则。如果未传递，则该函数将在一天的第一个市场分钟的末尾执行。

+   **半天交易日** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 此规则是否应在半天交易日触发？默认值为 True。

+   **日历** (*Sentinel*, *可选*) – 用于计算依赖于交易日历的规则的日历。

另请参阅

`zipline.api.date_rules`, `zipline.api.time_rules`

```py
class zipline.api.date_rules
```

基于日期的 `schedule_function()` 规则的工厂。

另请参阅

`schedule_function()`

```py
static every_day()
```

创建一个每天触发的规则。

返回：

**rule**

返回类型：

zipline.utils.events.EventRule

```py
static month_end(days_offset=0)
```

创建一个规则，该规则在每个月底之前固定数量的交易日触发。

参数：

**days_offset** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 在月结束前触发的交易天数。默认值为 0，即在月的最后一天触发。

返回：

**规则**

返回类型：

zipline.utils.events.EventRule

```py
static month_start(days_offset=0)
```

创建一条规则，该规则在每月开始后的固定交易日内触发。

参数：

**days_offset** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 在每月触发前等待的交易天数。默认值为 0，即在每月的第一个交易日触发。

返回：

**规则**

返回类型：

zipline.utils.events.EventRule

```py
static week_end(days_offset=0)
```

创建一条规则，该规则在每周结束前的固定交易日内触发。

参数：

**days_offset** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 在周结束前触发的交易天数。默认值为 0，即在周的最后一个交易日触发。

```py
static week_start(days_offset=0)
```

创建一条规则，该规则在每周开始后的固定交易日内触发。

参数：

**days_offset** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 在每周触发前等待的交易天数。默认值为 0，即在每周的第一个交易日触发。

```py
class zipline.api.time_rules
```

用于时间基础的`schedule_function()`规则的工厂。

另请参阅

`schedule_function()`

```py
every_minute
```

alias of `Always`

```py
static market_close(offset=None, hours=None, minutes=None)
```

创建一条规则，该规则在市场收盘后的固定偏移量触发。

偏移量可以指定为[`datetime.timedelta`](https://docs.python.org/3/library/datetime.html#datetime.timedelta "(in Python v3.11)")，或者指定为小时和分钟数。

参数：

+   **偏移量** ([*datetime.timedelta*](https://docs.python.org/3/library/datetime.html#datetime.timedelta "(in Python v3.11)")*,* *可选*) – 如果传递，则从市场收盘时触发的时间偏移。必须至少为 1 分钟。

+   **小时** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 如果传递，则在收盘前等待的小时数。

+   **分钟** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 如果传递，则在收盘前等待的分钟数。

返回：

**规则**

返回类型：

zipline.utils.events.EventRule

注释

如果没有传递参数，则默认偏移量为市场收盘前一分钟。

如果传递了`offset`，则不能传递`hours`和`minutes`。相反，如果传递了`hours`或`minutes`，则不能传递`offset`。

```py
static market_open(offset=None, hours=None, minutes=None)
```

创建一条规则，该规则在市场开盘后的固定偏移量触发。

偏移量可以指定为[`datetime.timedelta`](https://docs.python.org/3/library/datetime.html#datetime.timedelta "(in Python v3.11)")，或者指定为小时和分钟数。

参数：

+   **偏移量** ([*datetime.timedelta*](https://docs.python.org/3/library/datetime.html#datetime.timedelta "(in Python v3.11)")*,* *可选*) – 如果传递，表示触发时的市场开盘偏移量。必须至少为 1 分钟。

+   **小时数** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 如果传递，表示市场开盘后等待的小时数。

+   **分钟数** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 如果传递，表示市场开盘后等待的分钟数。

返回：

**规则**

返回类型：

zipline.utils.events.EventRule

注意

如果没有参数传递，默认的偏移量是市场开盘后一分钟。

如果传递了`偏移量`，则不能传递`小时数`和`分钟数`。相反，如果传递了`小时数`或`分钟数`，则不能传递`偏移量`。

### 订单

```py
zipline.api.order(self, asset, amount, limit_price=None, stop_price=None, style=None)
```

下固定数量的股票订单。

参数：

+   **资产** (*Asset*) – 要订购的资产。

+   **数量** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 要订购的股票数量。如果`数量`为正数，则表示要购买或平仓的股票数量。如果`数量`为负数，则表示要出售或做空的股票数量。

+   **限价** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 订单的限价。

+   **止损价** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 订单的止损价。

+   **风格** (*ExecutionStyle**,* *可选*) – 订单的执行风格。

返回：

**订单 ID** – 此订单的唯一标识符，如果没有下订单，则为 None。

返回类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") 或 None

注意

`限价`和`止损价`参数提供了传递常见执行风格的简写方式。传递`限价=N`等同于`风格=限价订单(N)`。类似地，传递`止损价=M`等同于`风格=止损订单(M)`，传递`限价=N`和`止损价=M`等同于`风格=止损限价订单(N, M)`。同时传递`风格`和`限价`或`止损价`是错误的。

另请参阅

`zipline.finance.execution.ExecutionStyle`, `zipline.api.order_value()`, `zipline.api.order_percent()`

```py
zipline.api.order_value(self, asset, value, limit_price=None, stop_price=None, style=None)
```

下固定金额的订单。

等同于`order(asset, value / data.current(asset, 'price'))`。

参数：

+   **资产** (*Asset*) – 要订购的资产。

+   **value** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 要交易的`asset`的价值量。买入或卖出的股数将等于`value / current_price`。

+   **limit_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 订单的限价。

+   **stop_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 订单的止损价。

+   **style** (*ExecutionStyle*) – 订单的执行风格。

返回：

**order_id** – 该订单的唯一标识符。

返回类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

注释

参见`zipline.api.order()`以获取关于`limit_price`、`stop_price`和`style`的更多信息

另请参阅

`zipline.finance.execution.ExecutionStyle`, `zipline.api.order()`, `zipline.api.order_percent()`

```py
zipline.api.order_percent(self, asset, percent, limit_price=None, stop_price=None, style=None)
```

在指定的资产中下订单，对应于当前资产组合价值的给定百分比。

参数：

+   **asset** (*Asset*) – 该订单所针对的资产。

+   **percent** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 分配给`asset`的资产组合价值的百分比。以小数形式指定，例如：0.50 表示 50%。

+   **limit_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 订单的限价。

+   **stop_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 订单的止损价。

+   **style** (*ExecutionStyle*) – 订单的执行风格。

返回：

**order_id** – 该订单的唯一标识符。

返回类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

注释

参见`zipline.api.order()`以获取关于`limit_price`、`stop_price`和`style`的更多信息

另请参阅

`zipline.finance.execution.ExecutionStyle`, `zipline.api.order()`, `zipline.api.order_value()`

```py
zipline.api.order_target(self, asset, target, limit_price=None, stop_price=None, style=None)
```

下订单以调整仓位至目标股数。如果仓位不存在，这相当于下新订单。如果仓位已存在，这相当于为当前股数与目标股数之间的差额下订单。

参数：

+   **资产** (*资产*) – 该订单所针对的资产。

+   **目标** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – `资产`的期望股数。

+   **限价** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 订单的限价。

+   **止损价** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 订单的止损价。

+   **风格** (*ExecutionStyle*) – 订单的执行风格。

返回：

**订单 ID** – 该订单的唯一标识符。

返回类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

注意

`order_target`不考虑任何未完成订单。例如：

```py
order_target(sid(0), 10)
order_target(sid(0), 10) 
```

这段代码将导致`sid(0)`的 20 股，因为在第二次`order_target`调用时，第一次`order_target`调用尚未完成填充。

有关`limit_price`、`stop_price`和`style`的更多信息，请参阅`zipline.api.order()`

另请参阅

`zipline.finance.execution.ExecutionStyle`, `zipline.api.order()`, `zipline.api.order_target_percent()`, `zipline.api.order_target_value()`

```py
zipline.api.order_target_value(self, asset, target, limit_price=None, stop_price=None, style=None)
```

下订单以调整仓位至目标值。如果仓位不存在，这相当于下新订单。如果仓位已存在，这相当于为当前值与目标值之间的差额下订单。如果所订购的资产是期货，则计算的“目标值”实际上是目标风险敞口，因为期货没有“价值”。

参数：

+   **资产** (*资产*) – 该订单所针对的资产。

+   **目标** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – `资产`的期望总价值。

+   **限价** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 订单的限价。

+   **止损价** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 订单的止损价。

+   **style** (*ExecutionStyle*) – 订单的执行风格。

返回：

**order_id** – 此订单的唯一标识符。

返回类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中)")

笔记

`order_target_value`不考虑任何未完成订单。例如：

```py
order_target_value(sid(0), 10)
order_target_value(sid(0), 10) 
```

这段代码将导致`sid(0)`的 20 美元，因为在第二次调用`order_target_value`时，第一次调用`order_target_value`尚未完成。

有关`limit_price`、`stop_price`和`style`的更多信息，请参阅`zipline.api.order()`

另请参阅

`zipline.finance.execution.ExecutionStyle`, `zipline.api.order()`, `zipline.api.order_target()`, `zipline.api.order_target_percent()`

```py
zipline.api.order_target_percent(self, asset, target, limit_price=None, stop_price=None, style=None)
```

下订单以调整持仓至当前投资组合价值的预定百分比。如果持仓不存在，这相当于下新订单。如果持仓已存在，这相当于下订单以达到目标百分比与当前百分比之间的差额。

参数：

+   **asset** (*Asset*) – 此订单所针对的资产。

+   **target** ([*float*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")) – 希望分配给`asset`的投资组合价值的百分比。这以小数形式指定，例如：0.50 表示 50%。

+   **limit_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")*,* *可选*) – 订单的限价。

+   **stop_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")*,* *可选*) – 订单的止损价。

+   **style** (*ExecutionStyle*) – 订单的执行风格。

返回：

**order_id** – 此订单的唯一标识符。

返回类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中)")

笔记

`order_target_value`不考虑任何未完成订单。例如：

```py
order_target_percent(sid(0), 10)
order_target_percent(sid(0), 10) 
```

这段代码将导致 20%的投资组合被分配给 sid(0)，因为在第二次调用`order_target_percent`时，第一次调用`order_target_percent`尚未完成。

有关`limit_price`、`stop_price`和`style`的更多信息，请参阅`zipline.api.order()`

另请参阅

`zipline.finance.execution.ExecutionStyle`, `zipline.api.order()`, `zipline.api.order_target()`, `zipline.api.order_target_value()`

```py
class zipline.finance.execution.ExecutionStyle
```

订单执行风格的基类。

```py
property exchange
```

此订单应路由到的交易所。

```py
abstract get_limit_price(is_buy)
```

获取此订单的限价。返回值为 None 或一个数值，该数值大于等于 0。

```py
abstract get_stop_price(is_buy)
```

获取此订单的止损价。返回值为 None 或一个数值，该数值大于等于 0。

```py
class zipline.finance.execution.MarketOrder(exchange=None)
```

以当前市场价格成交的订单执行风格。

这是使用`order()`放置订单时的默认设置。

```py
class zipline.finance.execution.LimitOrder(limit_price, asset=None, exchange=None)
```

以等于或优于指定限价的价格成交的订单执行风格。

参数：

**limit_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 买入时的最高价格，或卖出时的最低价格，订单应在此价格成交。

```py
class zipline.finance.execution.StopOrder(stop_price, asset=None, exchange=None)
```

当市场价格达到某一阈值时，代表应放置市价单的执行风格。

参数：

**stop_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 订单应放置的价格阈值。对于卖出，如果市场价格低于此值，则放置订单。对于买入，如果市场价格高于此值，则放置订单。

```py
class zipline.finance.execution.StopLimitOrder(limit_price, stop_price, asset=None, exchange=None)
```

当市场价格达到某一阈值时，代表应放置限价单的执行风格。

参数：

+   **limit_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 买入时的最高价格，或卖出时的最低价格，订单应在此价格或更优价格成交。

+   **stop_price** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 订单应放置的价格阈值。对于卖出，如果市场价格低于此值，则放置订单。对于买入，如果市场价格高于此值，则放置订单。

```py
zipline.api.get_order(self, order_id)
```

根据订单函数返回的订单 id 查找订单。

参数：

**order_id** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 订单的唯一标识符。

返回：

**订单** – 订单对象。

返回类型：

订单

```py
zipline.api.get_open_orders(self, asset=None)
```

检索所有当前未成交的订单。

参数：

**asset** (*Asset*) – 如果传递且不为 None，则仅返回给定资产的未成交订单，而不是所有未成交订单。

返回：

**open_orders** – 如果没有传递资产，这将返回一个字典，将资产映射到资产的所有未成交订单列表。如果传递了资产，则这将返回该资产的未成交订单列表。

返回类型：

[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[[list](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[Order]] 或 [list](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[Order]

```py
zipline.api.cancel_order(self, order_param)
```

取消未成交的订单。

参数：

**order_param** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *or* *Order*) – 要取消的订单 ID 或订单对象。

#### 订单取消策略

```py
zipline.api.set_cancel_policy(self, cancel_policy)
```

设置模拟的订单取消策略。

参数：

**cancel_policy** (*CancelPolicy*) – 要使用的取消策略。

另请参阅

`zipline.api.EODCancel`, `zipline.api.NeverCancel`

```py
class zipline.finance.cancel_policy.CancelPolicy
```

抽象取消策略接口。

```py
abstract should_cancel(event)
```

是否应取消所有未成交的订单？

参数：

**event** (*enum-value*) –

事件类型之一：

+   `zipline.gens.sim_engine.BAR`

+   `zipline.gens.sim_engine.DAY_START`

+   `zipline.gens.sim_engine.DAY_END`

+   `zipline.gens.sim_engine.MINUTE_END`

返回：

**should_cancel** – 是否应取消所有未成交的订单？

返回类型：

[bool](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")

```py
zipline.api.EODCancel(warn_on_cancel=True)
```

该策略在交易日结束时取消未成交的订单。目前，Zipline 仅将此策略应用于分钟级别的模拟。

参数：

**warn_on_cancel** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *optional*) – 如果这导致订单被取消，是否应发出警告？

```py
zipline.api.NeverCancel()
```

订单永远不会自动取消。

#### 订单取消策略

```py
zipline.api.set_cancel_policy(self, cancel_policy)
```

设置模拟的订单取消策略。

参数：

**cancel_policy** (*CancelPolicy*) – 要使用的取消策略。

另请参阅

`zipline.api.EODCancel`, `zipline.api.NeverCancel`

```py
class zipline.finance.cancel_policy.CancelPolicy
```

抽象取消策略接口。

```py
abstract should_cancel(event)
```

是否应取消所有未成交的订单？

参数：

**event** (*enum-value*) –

事件类型之一：

+   `zipline.gens.sim_engine.BAR`

+   `zipline.gens.sim_engine.DAY_START`

+   `zipline.gens.sim_engine.DAY_END`

+   `zipline.gens.sim_engine.MINUTE_END`

返回：

**should_cancel** – 是否应取消所有未成交的订单？

返回类型：

[bool](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")

```py
zipline.api.EODCancel(warn_on_cancel=True)
```

该策略在交易日结束时取消未成交的订单。目前，Zipline 仅将此策略应用于分钟级别的模拟。

参数：

**warn_on_cancel** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *optional*) – 如果这导致订单被取消，是否应发出警告？

```py
zipline.api.NeverCancel()
```

订单永远不会自动取消。

### 资产

```py
zipline.api.symbol(self, symbol_str, country_code=None)
```

通过股票代码查找股票。

参数：

+   **symbol_str** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要查找的股票的股票代码。

+   **country_code** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *or* *None**,* *optional*) – 限制符号搜索的国家。

返回值：

**equity** – 在当前符号查找日期持有该股票代码的股票。

返回类型：

zipline.assets.Equity

引发：

**SymbolNotFound** – 当在当前查找日期未持有该符号时引发。

另请参阅

`zipline.api.set_symbol_lookup_date()`

```py
zipline.api.symbols(self, *args, **kwargs)
```

查找多个股票作为列表。

参数：

+   ***args** (*iterable**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*]*) – 要查找的股票代码。

+   **country_code** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *or* *None**,* *optional*) – 限制符号搜索的国家。

返回值：

**equities** – 在当前符号查找日期持有给定股票代码的股票。

返回类型：

[list](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[zipline.assets.Equity]

引发：

**SymbolNotFound** – 当在当前查找日期未持有其中一个符号时引发。

另请参阅

`zipline.api.set_symbol_lookup_date()`

```py
zipline.api.future_symbol(self, symbol)
```

通过给定的符号查找期货合约。

参数：

**symbol** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 所需合约的符号。

返回值：

**future** – 以名称`symbol`交易的期货。

返回类型：

zipline.assets.Future

引发：

**SymbolNotFound** – 当未找到名为‘symbol’的合约时引发。

```py
zipline.api.set_symbol_lookup_date(self, dt)
```

设置符号解析为其资产的日期（符号可能在不同时间映射到不同的公司或底层资产）

参数：

**dt** (*datetime*) – 新的符号查找日期。

```py
zipline.api.sid(self, sid)
```

通过其唯一的资产标识符查找资产。

参数：

**sid** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 标识资产的唯一整数。

返回值：

**asset** – 具有给定`sid`的资产。

返回类型：

zipline.assets.Asset

引发：

**SidsNotFound** – 当请求的`sid`未映射到任何资产时。

### 交易控制

Zipline 提供交易控制以确保算法按预期执行。这些函数有助于保护算法免受意外行为的不良后果，尤其是在使用真实资金进行交易时。

```py
zipline.api.set_do_not_order_list(self, restricted_list, on_error='fail')
```

设置对可以下单的资产的限制。

参数：

**restricted_list** (`container[Asset]`, `SecurityList`) – 不能订购的资产。

```py
zipline.api.set_long_only(self, on_error='fail')
```

设置规则，指定此算法不能持有空头仓位。

```py
zipline.api.set_max_leverage(self, max_leverage)
```

设置算法最大杠杆的限制。

参数：

**max_leverage** (`float`) – 算法的最大杠杆。如果不提供，则没有最大值。

```py
zipline.api.set_max_order_count(self, max_count, on_error='fail')
```

对单日内可以下达的订单数量设置限制。

参数：

**max_count** (`int`) – 任何单日内可以下达的最大订单数量。

```py
zipline.api.set_max_order_size(self, asset=None, max_shares=None, max_notional=None, on_error='fail')
```

对为 sid 下达的单个订单的股份数和/或美元价值设置限制。限制被视为绝对值，并在算法尝试为 sid 下订单时执行。

如果算法尝试下达的订单会导致超过这些限制之一，则引发 TradingControlException。

参数：

+   **asset** (`Asset`, 可选) – 如果提供，则仅对给定资产的仓位设置保护。

+   **max_shares** (`int`, 可选) – 一次可以订购的最大股份数。

+   **max_notional** (`float`, 可选) – 一次可以订购的最大价值。

```py
zipline.api.set_max_position_size(self, asset=None, max_shares=None, max_notional=None, on_error='fail')
```

对给定 sid 的股份数和/或美元价值设置限制。限制被视为绝对值，并在算法尝试为 sid 下订单时执行。这意味着由于拆分/股息，可能会持有超过最大股份数，由于价格改善，可能会持有超过最大名义价值。

如果算法尝试下达的订单会导致股份/美元价值的绝对值超过这些限制之一，则引发 TradingControlException。

参数：

+   **asset** (`Asset`, 可选) – 如果提供，则仅对给定资产的仓位设置保护。

+   **max_shares** (`int`, 可选) – 对某资产持有的最大股份数。

+   **max_notional** (`float`, 可选) – 对某资产持有的最大价值。

### 模拟参数

```py
zipline.api.set_benchmark(self, benchmark)
```

设置基准资产。

参数：

**benchmark** (`zipline.assets.Asset`) – 设置为新基准的资产。

注意

对于新的基准资产，任何支付的股息将自动再投资。

#### 佣金模型

```py
zipline.api.set_commission(self, us_equities=None, us_futures=None)
```

为模拟设置佣金模型。

参数：

+   **us_equities** (*EquityCommissionModel*) – 用于交易美国股票的佣金模型。

+   **us_futures** (*FutureCommissionModel*) – 用于交易美国期货的佣金模型。

注释

此函数只能在 `initialize()` 期间调用。

另请参见

`zipline.finance.commission.PerShare`, `zipline.finance.commission.PerTrade`, `zipline.finance.commission.PerDollar`

```py
class zipline.finance.commission.CommissionModel
```

佣金模型的抽象基类。

佣金模型负责接受订单/交易对，并计算每笔交易应向算法账户收取多少佣金。

要实现新的佣金模型，请创建 `CommissionModel` 的子类并实现 `calculate()`。

```py
abstract calculate(order, transaction)
```

根据 `transaction` 计算 `order` 上应收取的佣金金额。

参数：

+   **order** (*zipline.finance.order.Order*) –

    （订单被处理，省略）

    `order` 的 `commission` 字段是一个浮点数，表示该订单已收取的佣金金额。

+   **transaction** (*zipline.finance.transaction.Transaction*) – 正在处理的交易所。如果某个订单在给定的条形图中没有足够的成交量来填充所请求的全部数量，则单个订单可能会产生多个交易。

返回：

**amount_charged** – 我们应该归因于该订单的额外佣金，以美元计。

返回类型：

[float](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")

```py
class zipline.finance.commission.PerShare(cost=0.001, min_trade_cost=0.0)
```

根据每股的成本计算交易佣金，可选择每笔交易的最小成本。

（参数重复，省略）

+   **cost** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 每交易一股支付的佣金金额。默认值为每股一美分的一成。

+   **min_trade_cost** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 每笔交易支付的佣金最小金额。默认值为无最小值。

注释

这是 zipline 默认的股票佣金模型。

```py
class zipline.finance.commission.PerTrade(cost=0.0)
```

根据每笔交易的成本计算交易佣金。

对于需要多次成交的订单，全额佣金将收取给首次成交。

（参数重复，省略）

**cost** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 每笔股票交易支付的佣金固定金额。

```py
class zipline.finance.commission.PerDollar(cost=0.0015)
```

通过应用每美元交易的固定成本来计算佣金。

参数：

**成本** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 每美元股票交易支付的固定佣金金额。默认是每美元交易 0.0015 美元的佣金。

#### 滑点模型

```py
zipline.api.set_slippage(self, us_equities=None, us_futures=None)
```

设置模拟的滑点模型。

参数：

+   **us_equities** (*EquitySlippageModel*) – 用于交易美国股票的滑点模型。

+   **us_futures** (*FutureSlippageModel*) – 用于交易美国期货的滑点模型。

注释

该函数只能在`initialize()`期间调用。

另请参阅

`zipline.finance.slippage.SlippageModel`

```py
class zipline.finance.slippage.SlippageModel
```

滑点模型的抽象基类。

滑点模型负责模拟中订单成交的费率和价格。

要实现一个新的滑点模型，请创建一个`SlippageModel`的子类，并实现`process_order()`方法。

```py
process_order(data, order)
```

```py
volume_for_bar
```

在当前分钟内，对于当前正在成交的资产，已经成交的股数。该属性由基类自动维护。子类可以使用它来跟踪单个资产的多个开放订单的总成交数量。

类型：

[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")

注释

定义自己构造函数的子类应在执行其他初始化之前调用`super(<subclass name>, self).__init__()`。

```py
abstract process_order(data, order)
```

计算在当前分钟内为`order`成交的股数和价格。

参数：

+   **data** (*zipline.protocol.BarData*) – 给定 bar 的数据。

+   **order** (*zipline.finance.order.Order*) – 要模拟的订单。

返回：

+   **执行价格** (*float*) – 成交价格。

+   **执行量** (*int*) – 应成交的股数。必须在`0`和`order.amount - order.filled`之间。如果成交的数量少于剩余数量，`order`将保持开放状态，并在下一分钟再次传递给此方法。

引发：

**zipline.finance.slippage.LiquidityExceeded** – 如果在当前 bar 期间不应再处理当前资产的更多订单，则可能会引发此异常。

注释

在调用此方法之前，`volume_for_bar`将被设置为`order.asset`在当前分钟内已经成交的股数。

`process_order()`方法在历史成交量为零的 bar 上不会被基类调用。

```py
class zipline.finance.slippage.FixedSlippage(spread=0.0)
```

假设所有资产的固定大小价差的简单模型。

参数：

**价差** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 所有资产假设的价差大小。买入订单将以`close + (spread / 2)`成交。卖出订单将以`close - (spread / 2)`成交。

注意

该模型不对成交大小设置限制。只要在订单资产中发生任何交易活动，资产的订单就会立即成交，即使订单的大小超过了历史成交量。

```py
class zipline.finance.slippage.VolumeShareSlippage(volume_limit=0.025, price_impact=0.1)
```

将模型滑点作为历史成交量的百分比的二次函数。

买入订单将以以下价格成交：

```py
price * (1 + price_impact * (volume_share ** 2)) 
```

卖出订单将以以下价格成交：

```py
price * (1 - price_impact * (volume_share ** 2)) 
```

其中`price`是该时段的收盘价，`volume_share`是填充的每分钟成交量的百分比，最多可达`volume_limit`。

参数：

+   **成交量限制** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 每个时段可以成交的历史成交量的最大百分比。0.5 表示历史成交量的 50%。1.0 表示 100%。默认值为 0.025（即，2.5%）。

+   **价格影响** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 价格影响的缩放系数。较大的值将导致更多的模拟价格影响。较小的值将导致较少的模拟价格影响。默认值为 0.1。

#### 佣金模型

```py
zipline.api.set_commission(self, us_equities=None, us_futures=None)
```

设置模拟的佣金模型。

参数：

+   **美国股票** (*EquityCommissionModel*) – 用于交易美国股票的佣金模型。

+   **美国期货** (*FutureCommissionModel*) – 用于交易美国期货的佣金模型。

注意

此函数只能在`initialize()`期间调用。

另请参见

`zipline.finance.commission.PerShare`，`zipline.finance.commission.PerTrade`，`zipline.finance.commission.PerDollar`

```py
class zipline.finance.commission.CommissionModel
```

佣金模型的抽象基类。

佣金模型负责接受订单/交易对，并计算在每次交易中应向算法账户收取多少佣金。

要实现新的佣金模型，请创建一个`CommissionModel`的子类，并实现`calculate()`。

```py
abstract calculate(order, transaction)
```

计算由于`transaction`而对`order`收取的佣金金额。

参数：

+   **订单** (*zipline.finance.order.Order*) –

    正在处理的订单。

    `order`的`commission`字段是一个浮点数，表示已经对该订单收取的佣金金额。

+   **transaction** (*zipline.finance.transaction.Transaction*) – 正在处理的交易所。如果某个时段内没有足够的交易量来满足订单中请求的全部数量，则单个订单可能会产生多个交易所。

返回：

**amount_charged** – 我们应该归因于该订单的额外佣金，以美元计。

返回类型：

[float](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")

```py
class zipline.finance.commission.PerShare(cost=0.001, min_trade_cost=0.0)
```

根据每股成本计算交易佣金，并可选择每笔交易的最小成本。

参数：

+   **cost** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 每交易一股支付的佣金金额。默认是每股一美分的十分之一。

+   **min_trade_cost** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 每笔交易支付的最低佣金金额。默认没有最低限制。

注意

这是 zipline 默认的股票佣金模型。

```py
class zipline.finance.commission.PerTrade(cost=0.0)
```

根据每笔交易的成本计算交易佣金。

对于需要多次成交的订单，全额佣金将计入首次成交。

参数：

**cost** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 每笔股票交易支付的固定佣金金额。

```py
class zipline.finance.commission.PerDollar(cost=0.0015)
```

通过应用每美元交易固定成本来模拟佣金。

参数：

**cost** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *optional*) – 每交易一美元股票支付的固定佣金金额。默认是每交易一美元支付 0.0015 美元的佣金。

#### 滑点模型

```py
zipline.api.set_slippage(self, us_equities=None, us_futures=None)
```

设置模拟的滑点模型。

参数：

+   **us_equities** (*EquitySlippageModel*) – 用于交易美国股票的滑点模型。

+   **us_futures** (*FutureSlippageModel*) – 用于交易美国期货的滑点模型。

注意

此函数只能在 `initialize()` 期间调用。

另请参阅

`zipline.finance.slippage.SlippageModel`

```py
class zipline.finance.slippage.SlippageModel
```

滑点模型的抽象基类。

滑点模型负责模拟中订单成交的费率和价格。

要实现一个新的滑点模型，创建一个 `SlippageModel` 的子类，并实现 `process_order()`。

```py
process_order(data, order)
```

```py
volume_for_bar
```

当前分钟内已为当前填充资产填充的股份数量。此属性由基类自动维护。如果单个资产有多个未完成订单，子类可以使用它来跟踪已填充的总量。

类型：

[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")

注意

定义自己的构造函数的子类应在执行其他初始化之前调用`super(<subclass name>, self).__init__()`。

```py
abstract process_order(data, order)
```

计算当前分钟内`order`的成交股数和价格。

参数：

+   **数据** (*zipline.protocol.BarData*) – 给定柱的数据。

+   **订单** (*zipline.finance.order.Order*) – 要模拟的订单。

返回：

+   **执行价格** (*float*) – 成交价格。

+   **执行量** (*int*) – 应成交的股数。必须在`0`和`order.amount - order.filled`之间。如果已成交的数量少于剩余数量，`order`将保持开放状态，并在下一分钟再次传递给此方法。

引发：

**zipline.finance.slippage.LiquidityExceeded** – 如果在当前资产的当前柱上不应再处理更多订单，则可能会引发此异常。

注释

在调用此方法之前，`volume_for_bar` 将设置为当前分钟内已为 `order.asset` 成交的股数。

`process_order()` 在历史成交量为零的柱上不会被基类调用。

```py
class zipline.finance.slippage.FixedSlippage(spread=0.0)
```

假设所有资产具有固定大小的点差简单模型。

参数：

**点差** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 假设所有资产的点差大小。买单将按`close + (spread / 2)`成交。卖单将按`close - (spread / 2)`成交。

注释

该模型不对成交大小设置限制。只要在订单资产中发生任何交易活动，资产的订单总是会立即成交，即使订单的大小大于历史成交量。

```py
class zipline.finance.slippage.VolumeShareSlippage(volume_limit=0.025, price_impact=0.1)
```

将点差建模为历史成交量百分比的二次函数。

买单将按以下方式成交：

```py
price * (1 + price_impact * (volume_share ** 2)) 
```

卖单将按以下方式成交：

```py
price * (1 - price_impact * (volume_share ** 2)) 
```

其中 `price` 是柱的收盘价，`volume_share` 是已成交的每分钟成交量的百分比，最多可达 `volume_limit` 的最大值。

参数：

+   **成交量限制** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 每个柱上可以成交的历史成交量的最大百分比。0.5 表示 50% 的历史成交量。1.0 表示 100%。默认值为 0.025（即，2.5%）。

+   **价格影响** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 价格影响的缩放系数。较大的值将导致更多的模拟价格影响。较小的值将导致较少的模拟价格影响。默认值为 0.1。

### 管道

有关更多信息，请参阅 Pipeline API

```py
zipline.api.attach_pipeline(self, pipeline, name, chunks=None, eager=True)
```

注册一个管道，以便在每天开始时计算。

参数：

+   **pipeline** (*Pipeline*) – 要计算的管道。

+   **name** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 管道的名称。

+   **chunks** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)") *或* *迭代器**,* *可选*) – 要计算管道结果的天数。增加此数字将使获取第一个结果的时间更长，但可能会提高模拟的总运行时间。如果传递了迭代器，我们将根据迭代器的值以块的形式运行。默认值为 True。

+   **eager** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 是否在 before_trading_start 之前计算此管道。

返回：

**pipeline** – 返回未更改的附加管道。

返回类型：

Pipeline

（另请参见）

`zipline.api.pipeline_output()`

```py
zipline.api.pipeline_output(self, name)
```

（获取由名称`name`附加的管道的结果）

参数：

**name** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要获取结果的管道的名称。

返回：

**结果** – 包含当前模拟日期请求的管道的结果的数据框。

返回类型：

pd.DataFrame

（引发）

**NoSuchPipeline** – 当没有注册名为 name 的管道时引发。

（另请参见）

`zipline.api.attach_pipeline()`, `zipline.pipeline.engine.PipelineEngine.run_pipeline()`

### （杂项）

```py
zipline.api.record(self, *args, **kwargs)
```

（跟踪和记录每天的值）

参数：

****kwargs** – 要记录的名称和值。

（注释）

（这些值将出现在性能数据包和传递给`analyze`以及从`run_algorithm()`返回的性能数据框中。）

```py
zipline.api.get_environment(self, field='platform')
```

（查询执行环境）

参数：

+   **field** (*{'platform'**,* *'arena'**,* *'data_frequency'**,* *'start'**,* *'end'**,*) –

+   **'capital_base'** –

+   **'platform'** –

+   **'*'** –

+   **meanings** (*查询的字段。选项具有以下*) –

+   **arena** (*-*) – 模拟参数中的竞技场。这通常将是`'backtest'`，但某些系统可能使用此来区分回测和实时交易。

+   **data_frequency** (*-*) – data_frequency 告诉算法它是否正在使用每日数据或分钟数据运行。

+   **start** (*-*) – 模拟的开始日期。

+   **end** (*-*) – 模拟的结束日期。

+   **资本基础** (*-*) – 模拟的起始资本。

+   **-platform** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 代码运行的平台。默认情况下，这将是字符串‘zipline’。这可以让算法知道它们是否在 Quantopian 平台上运行。

+   ***** (*-*) – 返回字典中的所有字段。

返回：

**val** – 查询字段的值。有关更多信息，请参见上文。

返回类型：

any

引发：

[**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError "(in Python v3.11)") – 当`field`不是有效选项时引发。

```py
zipline.api.fetch_csv(self, url, pre_func=None, post_func=None, date_column='date', date_format=None, timezone='UTC', symbol=None, mask=True, symbol_column=None, special_params_checker=None, country_code=None, **kwargs)
```

从远程 URL 获取 CSV 并注册数据，以便可以从`data`对象查询数据。

参数：

+   **url** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要加载的 CSV 文件的 URL。

+   **pre_func** (*callable**[**pd.DataFrame -> pd.DataFrame**]**,* *optional*) – 在日期解析或符号映射之前允许预处理从 fetch_csv 返回的原始数据的回调。

+   **post_func** (*callable**[**pd.DataFrame -> pd.DataFrame**]**,* *optional*) – 允许在日期和符号映射后处理数据的回调。

+   **date_column** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *optional*) – 包含日期时间信息的列的名称，用于映射预处理数据帧中的数据。

+   **date_format** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *optional*) – `date_column`中日期的格式。如果未提供，`fetch_csv`将尝试推断格式。有关此字符串格式的信息，请参阅[`pandas.read_csv()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html#pandas.read_csv "(in pandas v2.0.3)")。

+   **timezone** (*tzinfo* *or* [*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *optional*) – `date_column`中日期时间的时区。

+   **symbol** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *optional*) – 如果数据是关于新资产或指数的，则此字符串将用于在`data`中标识值的名称。例如，可以使用`fetch_csv`加载 VIX 的数据，然后此字段可以是字符串`'VIX'`。

+   **mask** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *optional*) – 删除无法进行符号映射的任何行。

+   **symbol_column** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 如果数据正在附加每个资产的某些新属性，则此参数是包含符号的列的名称。这将与日期信息一起用于映射资产查找器中的 sids。

+   **country_code** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *optional*) – 用于消除歧义的符号查找的国家代码。

+   ****kwargs** – 转发至 [`pandas.read_csv()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.read_csv.html#pandas.read_csv "(in pandas v2.0.3)")。

返回：

**csv_data_source** – 将从指定 url 拉取数据的请求源。

返回类型：

zipline.sources.requests_csv.PandasRequestsCSV

## Blotters

一个[blotter](https://www.investopedia.com/terms/b/blotter.asp)记录了一段时间内的交易及其细节，通常是一个交易日。交易细节包括时间、价格、订单大小以及是买入还是卖出订单。它通常由记录通过数据源进行的交易的贸易软件创建。

```py
class zipline.finance.blotter.blotter.Blotter(cancel_policy=None)
```

```py
batch_order(order_arg_lists)
```

批量下单。

参数：

**order_arg_lists** (*iterable**[*[*tuple*](https://docs.python.org/3/library/stdtypes.html#tuple "(in Python v3.11)")*]*) – 订单期望的参数元组。

返回：

**order_ids** – 每个已下订单（或未下订单）的唯一标识符（或 None）。

返回类型：

[列表](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[[字符串](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") 或 None]

注释

这对于 Blotter 子类能够批量下单是必需的，而不是一次传递一个订单请求。

```py
abstract cancel(order_id, relay_status=True)
```

取消单个订单

参数：

+   **order_id** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 订单的 id

+   **relay_status** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 是否记录订单状态

```py
abstract cancel_all_orders_for_asset(asset, warn=False, relay_status=True)
```

取消给定资产的所有未结订单。

```py
abstract get_transactions(bar_data)
```

根据当前未结订单、滑点模型和佣金模型创建交易列表。

参数：

**bar_data** (*zipline._protocol.BarData*) –

注释

此方法记录 blotter 的 open_orders 字典，以便

在我们处理完未结订单后，信息是准确的。

返回：

+   **交易列表** (*列表*) – 交易列表：由当前未结订单产生的交易列表。如果没有未结订单，则返回空列表。

+   **佣金列表** (*列表*) – 佣金列表：由未结订单成交产生的佣金列表。佣金是一个具有“资产”和“成本”参数的对象。

+   **已成交订单** (*列表*) – 已成交订单：已成交的所有订单列表。

```py
abstract hold(order_id, reason='')
```

将订单标记为“held”，其功能与“open”类似。当成交（全部或部分）到达时，状态将自动变更为 open/filled。

```py
abstract order(asset, amount, style, order_id=None)
```

下订单。

参数：

+   **资产** (*zipline.assets.Asset*) – 该订单对应的资产。

+   **数量** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 要订购的股票数量。如果`数量`为正数，则表示要购买或平仓的股票数量。如果`数量`为负数，则表示要出售或做空的股票数量。

+   **样式** (*zipline.finance.execution.ExecutionStyle*) – 订单的执行样式。

+   **订单 ID** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 此订单的唯一标识符。

返回：

**订单 ID** – 此订单的唯一标识符，如果没有下订单，则为 None。

返回类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") 或 None

注意

数量 > 0：买入/平仓 数量 < 0：卖出/做空 市价单：order(资产, 数量) 限价单：order(资产, 数量, style=LimitOrder(限价)) 止损单：order(资产, 数量, style=StopOrder(止损价)) 止损限价单：order(资产, 数量, style=StopLimitOrder(限价, 止损价))

```py
abstract process_splits(splits)
```

通过修改任何必要的未结订单来处理拆分列表。

参数：

**拆分** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")) – 拆分列表。每个拆分是一个元组（资产，比率）。

返回类型：

无

```py
abstract prune_orders(closed_orders)
```

从 blotter 的 open_orders 列表中删除所有给定订单。

参数：

**已关闭订单** (*已关闭订单的可迭代对象*) –

返回类型：

无

```py
abstract reject(order_id, reason='')
```

将给定订单标记为‘已拒绝’，其功能类似于已取消。区别在于拒绝是强制性的（通常包括经纪人指示订单被拒绝的原因的消息），而取消通常是用户驱动的。

```py
class zipline.finance.blotter.SimulationBlotter(equity_slippage=None, future_slippage=None, equity_commission=None, future_commission=None, cancel_policy=None)
```

```py
cancel(order_id, relay_status=True)
```

取消单个订单

参数：

+   **订单 ID** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 订单的 ID

+   **relay_status** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 是否记录订单状态

```py
cancel_all_orders_for_asset(asset, warn=False, relay_status=True)
```

取消给定资产的所有未结订单。

```py
get_transactions(bar_data)
```

根据当前的未结订单、滑点模型和佣金模型创建交易列表。

参数：

**bar_data** (*zipline._protocol.BarData*) –

注意

此方法记录了 blotter 的 open_orders 字典，以便

在我们处理完未结订单时，它是准确的。

返回：

+   **交易列表** (*列表*) – 交易列表：由当前未结订单产生的交易列表。如果没有未结订单，则返回空列表。

+   **佣金列表** (*列表*) – 佣金列表：填充未结订单产生的佣金列表。佣金是一个具有“资产”和“成本”参数的对象。

+   **已关闭订单** (*列表*) – 已关闭订单：已填充的所有订单列表。

```py
hold(order_id, reason='')
```

将订单 ID 标记为‘持有’。持有功能上类似于‘开放’。当填充（全部或部分）到达时，状态将自动变回开放/填充，视情况而定。

```py
order(asset, amount, style, order_id=None)
```

下订单。

参数：

+   **资产** (*zipline.assets.Asset*) – 此订单的资产。

+   **金额** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 要订购的股票数量。如果`金额`为正，这是要购买或平仓的股票数量。如果`金额`为负，这是要出售或做空的股票数量。

+   **风格** (*zipline.finance.execution.ExecutionStyle*) – 订单的执行风格。

+   **订单 ID** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 此订单的唯一标识符。

返回值：

**订单 ID** – 此订单的唯一标识符，如果未下订单，则为 None。

返回类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") 或 None

注意

金额 > 0 :: 买入/平仓 金额 < 0 :: 卖出/做空 市价单：order(资产, 金额) 限价单：order(资产, 金额, style=LimitOrder(限价)) 止损单：order(资产, 金额, style=StopOrder(止损价)) 止损限价单：order(资产, 金额, style=StopLimitOrder(限价, 止损价))

```py
process_splits(splits)
```

处理一系列拆分，根据需要修改任何开放订单。

参数：

**拆分** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")) – 拆分列表。每个拆分都是一个(资产, 比率)的元组。

返回类型：

无

```py
prune_orders(closed_orders)
```

从 blotter 的 open_orders 列表中移除所有给定订单。

参数：

**已关闭订单** (*已关闭订单的可迭代对象*) –

返回类型：

无

```py
reject(order_id, reason='')
```

将给定订单标记为‘拒绝’，其功能类似于取消。区别在于拒绝是非自愿的（通常包括经纪人指示为何拒绝订单的消息），而取消通常是用户驱动的。

## Pipeline API

`Pipeline`通过优化回测期间因子的计算，实现了更快、更内存高效的执行。

```py
class zipline.pipeline.Pipeline(columns=None, screen=None, domain=GENERIC)
```

Pipeline 对象表示一组要由 PipelineEngine 编译和执行的命名表达式。

Pipeline 有两个重要属性：‘columns’，一个命名`Term`实例的字典，和‘screen’，一个`Filter`，表示将资产包含在 Pipeline 结果中的标准。

要在 TradingAlgorithm 的上下文中计算管道，用户必须在`initialize`函数中调用`attach_pipeline`来注册管道，以便每个交易日都计算管道。可以通过从`handle_data`、`before_trading_start`或计划函数调用`pipeline_output`来检索附加管道的最新输出。

参数：

+   **列** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")*,* *可选*) – 初始列。

+   **筛选** (*zipline.pipeline.Filter**,* *可选*) – 初始筛选条件。

```py
add(term, name, overwrite=False)
```

添加一列。

计算`term`的结果将显示为运行此管道产生的 DataFrame 中的一列。

参数：

+   **列** (*zipline.pipeline.Term*) – 要添加到管道中的筛选器、因子或分类器。

+   **名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要添加的列的名称。

+   **覆盖** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 如果已经有一个名为 name 的列，是否覆盖现有条目。

```py
domain(default)
```

获取此管道的域。

+   如果在构造时提供了显式域，则使用它。

+   否则，从注册的列中推断域。

+   如果没有域可以推断，则返回`默认`。

参数：

**默认** (*zipline.pipeline.domain.Domain*) – 如果没有域可以从这个管道本身推断出来，则使用的域。

返回：

**域** – 管道的域。

返回类型：

zipline.pipeline.domain.Domain

引发：

+   **AmbiguousDomain** –

+   [**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError "(in Python v3.11)") – 如果`self`中的项与 self._domain 冲突。

```py
remove(name)
```

移除一列。

参数：

**名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要移除的列的名称。

引发：

[**KeyError**](https://docs.python.org/3/library/exceptions.html#KeyError "(in Python v3.11)") – 如果名称不在 self.columns 中。

返回：

**removed** – 移除的项。

返回类型：

zipline.pipeline.Term

```py
set_screen(screen, overwrite=False)
```

在此管道上设置筛选条件。

参数：

+   **筛选器** (*zipline.pipeline.Filter*) – 作为筛选条件应用的筛选器。

+   **覆盖** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 是否覆盖任何现有筛选条件。如果覆盖为 False 且 self.screen 不为 None，则引发错误。

```py
show_graph(format='svg')
```

将此管道渲染为 DAG。

参数：

**格式** (*{'svg'**,* *'png'**,* *'jpeg'}*) – 渲染图像的格式。默认是‘svg’。

```py
to_execution_plan(domain, default_screen, start_date, end_date)
```

编译成执行计划。

参数：

+   **域** (*zipline.pipeline.domain.Domain*) – 管道将在其上执行的域。

+   **默认筛选条件**（*zipline.pipeline.Term*）——如果 self.screen 为 None，则使用作为筛选条件的术语。

+   **所有日期**（*pd.DatetimeIndex*）——用于计算每个术语的起始和结束的日期日历。

+   **开始日期**（*pd.Timestamp*）——请求输出的第一个日期。

+   **结束日期**（*pd.Timestamp*）——请求输出的最后日期。

返回值：

**图**——编码术语依赖关系的图，包括关于额外行需求的元数据。

返回类型：

zipline.pipeline.graph.ExecutionPlan

```py
to_simple_graph(default_screen)
```

编译成一个没有额外行元数据的简单 TermGraph。

参数：

**默认筛选条件**（*zipline.pipeline.Term*）——如果 self.screen 为 None，则使用作为筛选条件的术语。

返回值：

**图**——编码术语依赖关系的图。

返回类型：

zipline.pipeline.graph.TermGraph

```py
property columns
```

此管道的输出列。

返回值：

**列**——从列名到计算该列输出的表达式的映射。

返回类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[[字符串](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)"), zipline.pipeline.ComputableTerm]

```py
property screen
```

此管道的筛选条件。

返回值：

**筛选条件**——定义此管道筛选条件的术语。如果`筛选条件`是一个过滤器，未通过过滤器的行（即，过滤器计算为`False`的行）将在返回结果之前从该管道的输出中删除。

返回类型：

zipline.pipeline.Filter 或 None

注意

在 Pipeline 上设置筛选条件不会改变任何行的值：它只影响是否返回给定行。使用筛选条件计算管道在逻辑上等同于不使用筛选条件计算管道，然后作为后处理步骤，过滤掉筛选条件计算为`False`的任何行。

```py
class zipline.pipeline.CustomFactor(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

用户定义因子的基类。

参数：

+   **输入**（*可迭代对象*，*可选*）——一个 BoundColumn 实例的可迭代对象（例如 USEquityPricing.close），描述了要加载并传递给 self.compute 的数据。如果在 CustomFactor 构造函数中未传递此参数，我们将查找名为 inputs 的类级属性。

+   **输出**（*可迭代对象*[*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")，*可选*）——一个字符串的可迭代对象，表示此因子应计算并返回的每个输出的名称。如果在 CustomFactor 构造函数中未传递此参数，我们将查找名为 outputs 的类级属性。

+   **窗口长度**（[*整数*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")，*可选*）——每个输入要传递的行数。如果在 CustomFactor 构造函数中未传递此参数，我们将查找名为 window_length 的类级属性。

+   **mask** (*zipline.pipeline.Filter**,* *可选*) – 一个过滤器，描述我们应该在哪一天计算的资产。每次调用`CustomFactor.compute`只会收到在`mask`在该日产生 True 的资产。

笔记

实现自己的因子的用户应该继承 CustomFactor 并实现一个名为 compute 的方法，其签名如下：

```py
def compute(self, today, assets, out, *inputs):
   ... 
```

在每个模拟日期，`compute`将被调用，传递当前日期、一个 sid 数组、一个输出数组，以及一个输入数组，用于每个表达式作为输入传递给 CustomFactor 构造函数。

传递给 compute 的值的具体类型如下：

```py
today : np.datetime64[ns]
    Row label for the last row of all arrays passed as `inputs`.
assets : np.array[int64, ndim=1]
    Column labels for `out` and`inputs`.
out : np.array[self.dtype, ndim=1]
    Output array of the same shape as `assets`.  `compute` should write
    its desired return values into `out`. If multiple outputs are
    specified, `compute` should write its desired return values into
    `out.<output_name>` for each output name in `self.outputs`.
*inputs : tuple of np.array
    Raw data arrays corresponding to the values of `self.inputs`. 
```

`compute`函数应该期望传递 NaN 值用于在某个资产没有可用数据的日期。这可能包括某个资产尚未存在的日期。

例如，如果一个 CustomFactor 需要 10 行收盘价数据，并且资产 A 从 2014 年 6 月 2 日星期一开始交易，那么在 2014 年 6 月 3 日星期二，资产 A 的输入数据列将会有 9 个领先的 NaN 值，用于之前没有可用数据的日子。

示例

具有预先声明默认值的 CustomFactor：

```py
class TenDayRange(CustomFactor):
  """
 Computes the difference between the highest high in the last 10
 days and the lowest low.

 Pre-declares high and low as default inputs and `window_length` as
 10.
 """

    inputs = [USEquityPricing.high, USEquityPricing.low]
    window_length = 10

    def compute(self, today, assets, out, highs, lows):
        from numpy import nanmin, nanmax

        highest_highs = nanmax(highs, axis=0)
        lowest_lows = nanmin(lows, axis=0)
        out[:] = highest_highs - lowest_lows

# Doesn't require passing inputs or window_length because they're
# pre-declared as defaults for the TenDayRange class.
ten_day_range = TenDayRange() 
```

一个没有默认值的 CustomFactor：

```py
class MedianValue(CustomFactor):
  """
 Computes the median value of an arbitrary single input over an
 arbitrary window..

 Does not declare any defaults, so values for `window_length` and
 `inputs` must be passed explicitly on every construction.
 """

    def compute(self, today, assets, out, data):
        from numpy import nanmedian
        out[:] = data.nanmedian(data, axis=0)

# Values for `inputs` and `window_length` must be passed explicitly to
# MedianValue.
median_close10 = MedianValue([USEquityPricing.close], window_length=10)
median_low15 = MedianValue([USEquityPricing.low], window_length=15) 
```

具有多个输出的 CustomFactor：

```py
class MultipleOutputs(CustomFactor):
    inputs = [USEquityPricing.close]
    outputs = ['alpha', 'beta']
    window_length = N

    def compute(self, today, assets, out, close):
        computed_alpha, computed_beta = some_function(close)
        out.alpha[:] = computed_alpha
        out.beta[:] = computed_beta

# Each output is returned as its own Factor upon instantiation.
alpha, beta = MultipleOutputs()

# Equivalently, we can create a single factor instance and access each
# output as an attribute of that instance.
multiple_outputs = MultipleOutputs()
alpha = multiple_outputs.alpha
beta = multiple_outputs.beta 
```

注意：如果一个 CustomFactor 有多个输出，所有输出必须具有相同的 dtype。例如，在上面的例子中，如果 alpha 是浮点数，那么 beta 也必须是浮点数。

```py
dtype = dtype('float64')
```

```py
class zipline.pipeline.Filter(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), domain=sentinel('NotSpecified'), *args, **kwargs)
```

计算布尔输出的管道表达式。

过滤器最常用于描述一组资产，用于包括或排除某些特定目的。许多 Pipeline API 函数接受一个`mask`参数，可以提供一个过滤器，指示只有在过滤器通过的值才应该在执行请求的计算时考虑。例如，`zipline.pipeline.Factor.top()`接受一个掩码，指示排名应该只在通过指定过滤器的资产上计算。

构建过滤器最常见的方法是通过比较运算符（`<`，`<=`，`!=`，`eq`，`>`，`>=`）之一`Factor`。例如，一种自然的方式来构建一个过滤器，用于 10 天加权平均价格小于$20.0 的股票，首先构建一个计算 10 天加权平均价格的因子，并将其与标量值 20.0 进行比较：

```py
>>> from zipline.pipeline.factors import VWAP
>>> vwap_10 = VWAP(window_length=10)
>>> vwaps_under_20 = (vwap_10 <= 20) 
```

过滤器也可以通过两个因子之间的比较来构建。例如，构建一个过滤器，用于资产/日期对，其中资产的 10 天加权平均价格大于其 30 天加权平均价格：

```py
>>> short_vwap = VWAP(window_length=10)
>>> long_vwap = VWAP(window_length=30)
>>> higher_short_vwap = (short_vwap > long_vwap) 
```

过滤器可以通过`&`（与）和`|`（或）运算符组合。

`&`两个过滤器产生一个新的过滤器，如果**两个**输入都产生 True，则产生 True。

`|`两个过滤器产生一个新的过滤器，如果**任何一个**输入产生 True，则产生 True。

`~`运算符可用于反转过滤器，将所有 True 值与 Falses 交换，反之亦然。

过滤器可以设置为 Pipeline 的`screen`属性，指示过滤器产生 False 的资产/日期对应从 Pipeline 的输出中排除。这对于减少 Pipeline 输出的噪声和减少 Pipeline 结果的内存消耗都很有用。

```py
__and__(other)
```

二元运算符：‘&’

```py
__or__(other)
```

二元运算符：‘|’

```py
if_else(if_true, if_false)
```

创建一个从两个选项中选择值的术语。

参数：

+   **if_true** (*zipline.pipeline.term.ComputableTerm*) – 在过滤器输出为 True 的位置应使用此表达式的值。

+   **if_false** (*zipline.pipeline.term.ComputableTerm*) – 在过滤器输出为 False 的位置应使用此表达式的值。

返回：

**merged** – 一个根据`self`产生的值从`if_true`或`if_false`中取值计算的术语。

返回的术语在`self`产生 True 的位置从``if_true``抽取，在`self`产生 False 的位置从`if_false`抽取。

返回类型：

zipline.pipeline.term.ComputableTerm

示例

设`f`是一个产生以下输出的因子：

```py
 AAPL   MSFT    MCD     BK
2017-03-13    1.0    2.0    3.0    4.0
2017-03-14    5.0    6.0    7.0    8.0 
```

设`g`是另一个产生以下输出的因子：

```py
 AAPL   MSFT    MCD     BK
2017-03-13   10.0   20.0   30.0   40.0
2017-03-14   50.0   60.0   70.0   80.0 
```

最后，设`condition`是一个产生以下输出的过滤器：

```py
 AAPL   MSFT    MCD     BK
2017-03-13   True  False   True  False
2017-03-14   True   True  False  False 
```

那么，表达式`condition.if_else(f, g)`产生以下输出：

```py
 AAPL   MSFT    MCD     BK
2017-03-13    1.0   20.0    3.0   40.0
2017-03-14    5.0    6.0   70.0   80.0 
```

另请参阅

[`numpy.where`](https://numpy.org/doc/stable/reference/generated/numpy.where.html#numpy.where "(in NumPy v1.25)")，`Factor.fillna`

```py
class zipline.pipeline.Factor(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), domain=sentinel('NotSpecified'), *args, **kwargs)
```

Pipeline API 表达式，产生数值或日期值输出。

因子是最常用的 Pipeline 术语，代表任何产生数值结果的计算结果。

因子可以通过任何内置的数学运算符（`+`，`-`，`*`等）与其他因子或标量值组合。

这使得编写结合多个因子的复杂表达式变得容易。例如，构造一个计算两个其他因子平均值的因子非常简单：

```py
>>> f1 = SomeFactor(...)  
>>> f2 = SomeOtherFactor(...)  
>>> average = (f1 + f2) / 2.0 
```

因子也可以通过比较运算符（`<`，`<=`，`!=`，`eq`，`>`，`>=`）转换为`zipline.pipeline.Filter`对象。

除了基本的数值运算符之外，还有许多定义在因子上的自然运算符。这些包括识别缺失或极值输出的方法（`isnull()`，`notnull()`，`isnan()`，`notnan()`），规范化输出的方法（`rank()`，`demean()`，`zscore()`），以及基于结果的秩次属性的构建过滤器的方法（`top()`，`bottom()`，`percentile_between()`）。

```py
eq(other)
```

构建一个`Filter`，计算`self == other`。

参数：

**other** (*zipline.pipeline.Factor*，[*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*) – 表达式的右侧。

返回：

**过滤** – 使用`self`和`other`的输出计算`self == other`的过滤器。

返回类型：

zipline.pipeline.Filter

```py
demean(mask=sentinel('NotSpecified'), groupby=sentinel('NotSpecified'))
```

构建一个因子，该因子计算`self`并从结果的每一行中减去均值。

如果提供了`mask`，则在计算行均值时忽略`mask`返回 False 的值，并在掩码为 False 的任何地方输出 NaN。

如果提供了`groupby`，则根据`groupby`产生的值对每一行进行分区，对分区数组去均值，并将子结果重新组合起来。

参数：

+   **mask** (*zipline.pipeline.Filter*，*可选*) – 一个定义了计算均值时忽略的值的过滤器。

+   **groupby** (*zipline.pipeline.Classifier*，*可选*) – 一个定义了计算均值的分区的分类器。

示例

设`f`是一个将产生以下输出的因子：

```py
 AAPL   MSFT    MCD     BK
2017-03-13    1.0    2.0    3.0    4.0
2017-03-14    1.5    2.5    3.5    1.0
2017-03-15    2.0    3.0    4.0    1.5
2017-03-16    2.5    3.5    1.0    2.0 
```

设`c`是一个产生以下输出的分类器：

```py
 AAPL   MSFT    MCD     BK
2017-03-13      1      1      2      2
2017-03-14      1      1      2      2
2017-03-15      1      1      2      2
2017-03-16      1      1      2      2 
```

设`m`是一个产生以下输出的过滤器：

```py
 AAPL   MSFT    MCD     BK
2017-03-13  False   True   True   True
2017-03-14   True  False   True   True
2017-03-15   True   True  False   True
2017-03-16   True   True   True  False 
```

然后`f.demean()`将从`f`产生的每一行中减去均值。

```py
 AAPL   MSFT    MCD     BK
2017-03-13 -1.500 -0.500  0.500  1.500
2017-03-14 -0.625  0.375  1.375 -1.125
2017-03-15 -0.625  0.375  1.375 -1.125
2017-03-16  0.250  1.250 -1.250 -0.250 
```

`f.demean(mask=m)`将从每一行中减去均值，但在计算均值时会忽略对角线上的值，并在输出中将对角线上的值替换为 NaN。忽略对角线上的值是因为这些位置是掩码`m`返回 False 的地方。

```py
 AAPL   MSFT    MCD     BK
2017-03-13    NaN -1.000  0.000  1.000
2017-03-14 -0.500    NaN  1.500 -1.000
2017-03-15 -0.166  0.833    NaN -0.666
2017-03-16  0.166  1.166 -1.333    NaN 
```

`f.demean(groupby=c)`将从 AAPL/MSFT 和 MCD/BK 的各自条目中减去组均值。AAPL/MSFT 被分组在一起，因为这两个资产在分类器`c`的输出中总是产生 1。同样，MCD/BK 被分组在一起，因为它们总是产生 2。

```py
 AAPL   MSFT    MCD     BK
2017-03-13 -0.500  0.500 -0.500  0.500
2017-03-14 -0.500  0.500  1.250 -1.250
2017-03-15 -0.500  0.500  1.250 -1.250
2017-03-16 -0.500  0.500 -0.500  0.500 
```

`f.demean(mask=m, groupby=c)` 也会减去 AAPL/MSFT 和 MCD/BK 的组均值，但计算均值时会忽略对角线上的值，并在输出中将对角线上的值写为 NaN。

```py
 AAPL   MSFT    MCD     BK
2017-03-13    NaN  0.000 -0.500  0.500
2017-03-14  0.000    NaN  1.250 -1.250
2017-03-15 -0.500  0.500    NaN  0.000
2017-03-16 -0.500  0.500  0.000    NaN 
```

`demean()` 仅支持 dtype 为 float64 的因子。

均值对异常值的大小敏感。当处理可能产生大异常值的因子时，使用`mask`参数丢弃分布极端的值通常很有用：

```py
>>> base = MyFactor(...)  
>>> normalized = base.demean(
...     mask=base.percentile_between(1, 99),
... ) 
```

注释

另请参阅

[`pandas.DataFrame.groupby()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.groupby.html#pandas.DataFrame.groupby "(in pandas v2.0.3)")

```py
zscore(mask=sentinel('NotSpecified'), groupby=sentinel('NotSpecified'))
```

构建一个因子，对每天的结果进行 Z-Score 计算。

行的 Z-Score 定义为：

```py
(row - row.mean()) / row.stddev() 
```

如果提供了`mask`，则在计算行均值和标准差时忽略`mask`返回 False 的值，并在掩码为 False 的任何地方输出 NaN。

如果提供了`groupby`，则根据`groupby`产生的值对每一行进行分区，对分区数组进行 Z-Score 计算，并将子结果重新组合。

参数：

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 当进行 Z-Score 计算时，定义要忽略的值的过滤器。

+   **groupby** (*zipline.pipeline.Classifier**,* *可选*) – 定义用于计算 Z-Score 的分区的分类器。

返回：

**zscored** – 一个因子，其输出为自身的 Z-Score。

返回类型：

zipline.pipeline.Factor

注释

均值和标准差对异常值的大小敏感。当处理可能产生大异常值的因子时，使用`mask`参数丢弃分布极端的值通常很有用：

```py
>>> base = MyFactor(...)  
>>> normalized = base.zscore(
...    mask=base.percentile_between(1, 99),
... ) 
```

`zscore()` 仅支持 dtype 为 float64 的因子。

示例

参见`demean()`，了解关于`mask`和`groupby`的语义的深入示例。

另请参阅

[`pandas.DataFrame.groupby()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.groupby.html#pandas.DataFrame.groupby "(in pandas v2.0.3)")

```py
rank(method='ordinal', ascending=True, mask=sentinel('NotSpecified'), groupby=sentinel('NotSpecified'))
```

构建一个新的因子，表示每一行内各列的排序等级。

参数：

+   **方法** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *{'ordinal'**,* *'min'**,* *'max'**,* *'dense'**,* *'average'}*) – 用于为绑定元素分配等级的方法。有关每种排名方法的语义的完整描述，请参阅 scipy.stats.rankdata。默认值为‘ordinal’。

+   **升序** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 是否返回升序或降序的排序等级。默认值为 True。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 表示在计算排名时要考虑的资产的过滤器。如果提供了掩码，则在计算排名时忽略掩码产生 False 值的任何资产/日期对。

+   **groupby** (*zipline.pipeline.Classifier**,* *可选*) – 定义排序时要执行的分区的分类器。

返回：

**排名** – 一个新的因子，将计算`self`产生的数据的排名。

返回类型：

zipline.pipeline.Factor

注释

方法的默认值与 scipy.stats.rankdata 的默认值不同。有关方法的有效输入的完整描述，请参阅该函数的文档。

在给定日期的缺失或不存在的数据将导致资产在该日期获得 NaN 的排名。

参见

[`scipy.stats.rankdata()`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.rankdata.html#scipy.stats.rankdata "(in SciPy v1.11.1)")

```py
pearsonr(target, correlation_length, mask=sentinel('NotSpecified'))
```

构建一个新的因子，该因子计算`target`与`self`列之间的滚动皮尔逊相关系数。

参数：

+   **目标** (*zipline.pipeline.Term*) – 用于与`self`产生的每个数据列计算相关性的术语。这可以是因子、BoundColumn 或切片。如果目标为二维，则按资产计算相关性。

+   **相关长度** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 计算每个相关系数的回溯窗口长度。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 描述每天应计算与目标切片相关性的资产的过滤器。

返回：

**相关性** – 一个新的因子，将计算`target`与`self`的列之间的相关性。

返回类型：

zipline.pipeline.Factor

注释

此方法只能用于被认为是安全的表达式，这些表达式可以用作窗口化`Factor`对象的输入。此类表达式的示例包括`BoundColumn` `Returns`以及由`rank()`或`zscore()`创建的任何因子。

示例

假设我们想要创建一个因子，该因子计算 AAPL 的 10 天回报与所有其他资产的 10 天回报之间的相关性，每个相关性计算 30 天。这可以通过以下方式实现：

```py
returns = Returns(window_length=10)
returns_slice = returns[sid(24)]
aapl_correlations = returns.pearsonr(
    target=returns_slice, correlation_length=30,
) 
```

这等效于执行：

```py
aapl_correlations = RollingPearsonOfReturns(
    target=sid(24), returns_length=10, correlation_length=30,
) 
```

参见

[`scipy.stats.pearsonr()`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.pearsonr.html#scipy.stats.pearsonr "(in SciPy v1.11.1)"), `zipline.pipeline.factors.RollingPearsonOfReturns`, `Factor.spearmanr()`

```py
spearmanr(target, correlation_length, mask=sentinel('NotSpecified'))
```

构建一个新的因子，计算`目标`与`自身`列之间的滚动斯皮尔曼等级相关系数。

参数：

+   **目标** (*zipline.pipeline.Term*) – 用于与自身产生的每个数据列计算相关性的项。这可以是因子、绑定列或切片。如果目标为二维，则按资产计算相关性。

+   **相关性长度** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 计算每个相关系数所用的回溯窗口长度。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 描述每天应计算与目标切片相关性的资产的过滤器。

返回：

**相关性** – 一个新的因子，用于计算`目标`与`自身`列之间的相关性。

返回类型：

zipline.pipeline.Factor

注意

此方法只能在对用作窗口化`Factor`对象输入安全的表达式上调用。此类表达式的示例包括`BoundColumn` `Returns`以及由`rank()`或`zscore()`创建的任何因子。

示例

假设我们想要创建一个因子，计算苹果公司 10 天回报率与其他所有资产 10 天回报率之间的相关性，每个相关性计算周期为 30 天。这可以通过以下步骤实现：

```py
returns = Returns(window_length=10)
returns_slice = returns[sid(24)]
aapl_correlations = returns.spearmanr(
    target=returns_slice, correlation_length=30,
) 
```

这相当于执行：

```py
aapl_correlations = RollingSpearmanOfReturns(
    target=sid(24), returns_length=10, correlation_length=30,
) 
```

参见

[`scipy.stats.spearmanr()`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.spearmanr.html#scipy.stats.spearmanr "(in SciPy v1.11.1)"), `Factor.pearsonr()`

```py
linear_regression(target, regression_length, mask=sentinel('NotSpecified'))
```

构建一个新的因子，执行最小二乘回归，预测自身列从目标。

参数：

+   **目标** (*zipline.pipeline.Term*) – 在每个回归中用作预测变量/自变量的项。这可以是因子、绑定列或切片。如果目标为二维，则按资产计算回归。

+   **回归长度** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 用于计算每个回归的回溯窗口的长度。

+   **mask** (*zipline.pipeline.Filter**,* *可选*) – 描述每天应与目标切片进行回归的资产的 Filter。

返回：

**回归** – 一个新的 Factor，将计算目标与自身列之间的线性回归。

返回类型：

zipline.pipeline.Factor

注意

此方法只能用于被认为是安全的，可以作为窗口化`Factor`对象输入的表达式。此类表达式的例子包括`BoundColumn` `Returns`以及任何由`rank()`或`zscore()`创建的因素。

示例

假设我们想要创建一个因子，该因子将 AAPL 的 10 天回报与所有其他资产的 10 天回报进行回归，每个回归计算超过 30 天。这可以通过执行以下操作来实现：

```py
returns = Returns(window_length=10)
returns_slice = returns[sid(24)]
aapl_regressions = returns.linear_regression(
    target=returns_slice, regression_length=30,
) 
```

这相当于执行：

```py
aapl_regressions = RollingLinearRegressionOfReturns(
    target=sid(24), returns_length=10, regression_length=30,
) 
```

另请参阅

[`scipy.stats.linregress()`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.linregress.html#scipy.stats.linregress "(in SciPy v1.11.1)")

```py
winsorize(min_percentile, max_percentile, mask=sentinel('NotSpecified'), groupby=sentinel('NotSpecified'))
```

构建一个新的因子，该因子对由此因子得到的结果进行 winsorize。

Winsorization 将排名低于最小百分位的值更改为最小百分位的值。类似地，排名高于最大百分位的值被更改为最大百分位的值。

Winsorizing 对于限制极端数据点的影响而不完全删除这些点非常有用。

如果提供了`mask`，则在计算百分位数截止点时忽略`mask`返回 False 的值，并在`mask`为 False 的任何地方输出 NaN。

如果提供了`groupby`，则分别对由`groupby`定义的每个组应用 winsorization。

参数：

+   **最小百分位数** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* [*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 值等于或低于此百分位的条目将被替换为第(len(input) * min_percentile)个最低值。如果低值不应被剪裁，请使用 0。

+   **最大百分位数** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* [*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 值等于或高于此百分位的条目将被替换为第(len(input) * max_percentile)个最低值。如果高值不应被剪裁，请使用 1。

+   **mask** (*zipline.pipeline.Filter**,* *optional*) – 在 winsorizing 时忽略的值的过滤器。

+   **groupby** (*zipline.pipeline.Classifier**,* *optional*) – 定义了要进行 winsorize 的分区的分类器。

返回：

**winsorized** – 一个因子，产生一个 winsorized 版本的 self。

返回类型：

zipline.pipeline.Factor

示例

```py
price = USEquityPricing.close.latest
columns={
    'PRICE': price,
    'WINSOR_1: price.winsorize(
        min_percentile=0.25, max_percentile=0.75
    ),
    'WINSOR_2': price.winsorize(
        min_percentile=0.50, max_percentile=1.0
    ),
    'WINSOR_3': price.winsorize(
        min_percentile=0.0, max_percentile=0.5
    ),

} 
```

给定一个如上定义的列的管道，对于某一天的结果可能看起来像：

```py
 'PRICE' 'WINSOR_1' 'WINSOR_2' 'WINSOR_3'
Asset_1    1        2          4          3
Asset_2    2        2          4          3
Asset_3    3        3          4          3
Asset_4    4        4          4          4
Asset_5    5        5          5          4
Asset_6    6        5          5          4 
```

另请参阅

[`scipy.stats.mstats.winsorize()`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.mstats.winsorize.html#scipy.stats.mstats.winsorize "(in SciPy v1.11.1)"), [`pandas.DataFrame.groupby()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.groupby.html#pandas.DataFrame.groupby "(in pandas v2.0.3)")

```py
quantiles(bins, mask=sentinel('NotSpecified'))
```

构建一个分类器，计算`self`输出的分位数。

对于每个非 NaN 的数据点，输出都会被标记为一个从 0 到（bins - 1）的整数值。NaN 值被标记为-1。

如果提供了`mask`，则在`mask`产生 False 的位置忽略数据点，并在这些位置发出-1 的标签。

参数：

+   **bins** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 要计算的标签箱数。

+   **mask** (*zipline.pipeline.Filter**,* *optional*) – 计算分位数时忽略的值的掩码。

返回：

**分位数** – 一个分类器，产生从 0 到（bins - 1）的整数标签。

返回类型：

zipline.pipeline.Classifier

```py
quartiles(mask=sentinel('NotSpecified'))
```

构建一个分类器，计算`self`输出的四分位数。

对于每个非 NaN 的数据点，输出都会被标记为 0、1、2 或 3 中的一个值，对应于每行中的第一、第二、第三或第四四分位数。NaN 数据点被标记为-1。

如果提供了`mask`，则在`mask`产生 False 的位置忽略数据点，并在这些位置发出-1 的标签。

参数：

**mask** (*zipline.pipeline.Filter**,* *optional*) – 计算四分位数时忽略的值的掩码。

返回：

**四分位数** – 一个分类器，产生从 0 到 3 的整数标签。

返回类型：

zipline.pipeline.Classifier

```py
quintiles(mask=sentinel('NotSpecified'))
```

构建一个分类器，在`self`上计算五分位数标签。

对于每个非 NaN 的数据点，输出都会被标记为 0、1、2、3 或 4 中的一个值，对应于每行中的五分位数。NaN 数据点被标记为-1。

如果提供了`mask`，则在`mask`产生 False 的位置忽略数据点，并在这些位置发出-1 的标签。

参数：

**mask** (*zipline.pipeline.Filter**,* *optional*) – 计算五分位数时忽略的值的掩码。

返回：

**五分位数** – 一个分类器，产生从 0 到 4 的整数标签。

返回类型：

zipline.pipeline.Classifier

```py
deciles(mask=sentinel('NotSpecified'))
```

构造一个分类器，在`self`上计算十分位数标签。

对于每一行，每个非 NaN 数据点都会被标记为 0 到 9 的值，对应于十分位数。NaN 数据点被标记为-1。

如果提供了`mask`，则在`mask`产生 False 的位置忽略数据点，并在这些位置发出-1 的标签。

参数：

**掩码** (*zipline.pipeline.Filter**,* *可选*) – 计算十分位数时要忽略的值的掩码。

返回：

**十分位数** – 产生从 0 到 9 的整数标签的分类器。

返回类型：

zipline.pipeline.Classifier

```py
top(N, mask=sentinel('NotSpecified'), groupby=sentinel('NotSpecified'))
```

构造一个过滤器，匹配每天自身顶部 N 个资产值。

如果提供了`groupby`，则返回一个过滤器，该过滤器匹配每个组中顶部 N 个资产值。

参数：

+   **N** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 每天通过返回的过滤器的资产数量。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 表示在计算排名时要考虑的资产的过滤器。如果提供了掩码，则在计算最高值时忽略掩码产生 False 值的任何资产/日期对。

+   **groupby** (*zipline.pipeline.Classifier**,* *可选*) – 定义排序时要考虑的分区的分类器。

返回：

**过滤器**

返回类型：

zipline.pipeline.Filter

```py
bottom(N, mask=sentinel('NotSpecified'), groupby=sentinel('NotSpecified'))
```

构造一个过滤器，匹配每天自身底部 N 个资产值。

如果提供了`groupby`，则返回一个过滤器，该过滤器匹配`groupby`定义的每个组中底部 N 个资产值。

参数：

+   **N** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 每天通过返回的过滤器的资产数量。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 表示在计算排名时要考虑的资产的过滤器。如果提供了掩码，则在计算底部值时忽略掩码产生 False 值的任何资产/日期对。

+   **groupby** (*zipline.pipeline.Classifier**,* *可选*) – 定义排序时要考虑的分区的分类器。

返回：

**过滤器**

返回类型：

zipline.pipeline.Filter

```py
percentile_between(min_percentile, max_percentile, mask=sentinel('NotSpecified'))
```

构造一个过滤器，匹配自身值在`min_percentile`和`max_percentile`定义的范围内。

参数：

+   **min_percentile** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)") *[**0.0**,* *100.0**]*) – 对于数据中高于此百分位的资产返回 True。

+   **max_percentile** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)") *[**0.0**,* *100.0**]*) – 对于数据中低于此百分位的资产返回 True。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 一个表示在计算百分位数阈值时要考虑的资产的过滤器。如果提供了掩码，则每天仅使用`mask`返回 True 的资产来计算百分位数截止点。对于`mask`产生 False 的资产，此因子输出中也将产生 False。

返回：

**out** – 将计算指定百分位数范围掩码的新过滤器。

返回类型：

zipline.pipeline.Filter

```py
isnan()
```

对于此因子中所有 NaN 的值，产生 True 的过滤器。

返回：

**nanfilter**

返回类型：

zipline.pipeline.Filter

```py
notnan()
```

对于此因子中非 NaN 的值，产生 True 的过滤器。

返回：

**nanfilter**

返回类型：

zipline.pipeline.Filter

```py
isfinite()
```

对于此因子中非 NaN、inf 或-inf 的值，产生 True 的过滤器。

```py
clip(min_bound, max_bound, mask=sentinel('NotSpecified'))
```

剪辑（限制）因子中的值。

给定一个区间，区间外的值将被剪辑到区间边缘。例如，如果指定了`[0, 1]`的区间，小于 0 的值变为 0，大于 1 的值变为 1。

参数：

+   **min_bound** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 要使用的最小值。

+   **max_bound** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 要使用的最大值。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 一个表示在剪辑时要考虑的资产的过滤器。

注释

要仅在一侧剪辑值，可以传递`-np.inf`和`np.inf`。例如，仅剪辑最大值而不剪辑最小值：

```py
factor.clip(min_bound=-np.inf, max_bound=user_provided_max) 
```

另请参阅

[`numpy.clip`](https://numpy.org/doc/stable/reference/generated/numpy.clip.html#numpy.clip "(in NumPy v1.25)")

```py
clip(min_bound, max_bound, mask=sentinel('NotSpecified'))
```

剪辑（限制）因子中的值。

给定一个区间，区间外的值将被剪辑到区间边缘。例如，如果指定了`[0, 1]`的区间，小于 0 的值变为 0，大于 1 的值变为 1。

参数：

+   **min_bound** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 要使用的最小值。

+   **max_bound** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 要使用的最大值。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 一个表示在剪辑时要考虑的资产的过滤器。

注释

要仅在一侧剪辑值，可以传递`-np.inf`和`np.inf`。例如，仅剪辑最大值而不剪辑最小值：

```py
factor.clip(min_bound=-np.inf, max_bound=user_provided_max) 
```

另请参阅

[`numpy.clip`](https://numpy.org/doc/stable/reference/generated/numpy.clip.html#numpy.clip "(in NumPy v1.25)")

```py
__add__(other)
```

构造一个`Factor`，计算`self + other`。

参数：

**其他** (*zipline.pipeline.Factor**,* [*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

返回：

**因子** – 因子计算`self + other`，输出`self`和`other`的结果。

返回类型：

zipline.pipeline.Factor

```py
__sub__(other)
```

构造一个`Factor`，计算`self - other`。

参数：

**其他** (*zipline.pipeline.Factor**,* [*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

返回：

**因子** – 因子计算`self - other`，输出`self`和`other`的结果。

返回类型：

zipline.pipeline.Factor

```py
__mul__(other)
```

构造一个`Factor`，计算`self * other`。

参数：

**其他** (*zipline.pipeline.Factor**,* [*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

返回：

**因子** – 因子计算`self * other`，输出`self`和`other`的结果。

返回类型：

zipline.pipeline.Factor

```py
__div__(other)
```

构造一个`Factor`，计算`self / other`。

参数：

**其他** (*zipline.pipeline.Factor**,* [*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

返回：

**因子** – 因子计算`self / other`，输出`self`和`other`的结果。

返回类型：

zipline.pipeline.Factor

```py
__mod__(other)
```

构造一个`Factor`，计算`self % other`。

参数：

**其他** (*zipline.pipeline.Factor**,* [*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

返回：

**因子** – 因子计算`self % other`，输出`self`和`other`的结果。

返回类型：

zipline.pipeline.Factor

```py
__pow__(other)
```

构造一个`Factor`，计算`self ** other`。

参数：

**其他** (*zipline.pipeline.Factor**,* [*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

返回：

**因子** – 因子计算`self ** other`，输出`self`和`other`的结果。

返回类型：

zipline.pipeline.Factor

```py
__lt__(other)
```

构造一个`Filter`，计算`self < other`。

参数：

**其他** (*zipline.pipeline.Factor**,* [*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

返回：

**过滤器** – 使用`self < other`计算过滤器，输出`self`和`other`的结果。

返回类型：

zipline.pipeline.Filter

```py
__le__(other)
```

构造一个`Filter`，计算`self <= other`。

参数：

**其他** (*zipline.pipeline.Factor**,* [*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

返回：

**过滤器** – 使用`self <= other`计算过滤器，输出`self`和`other`的结果。

返回类型：

zipline.pipeline.Filter

```py
__ne__(other)
```

构造一个`Filter`，计算`self != other`。

参数：

**其他** (*zipline.pipeline.Factor**,* [*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

返回：

**过滤器** – 使用`self != other`计算过滤器，输出`self`和`other`的结果。

返回类型：

zipline.pipeline.Filter

```py
__ge__(other)
```

构造一个`Filter`，计算`self >= other`。

参数：

**其他** (*zipline.pipeline.Factor**,* [*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

返回：

**过滤器** – 使用`self >= other`计算过滤器，输出`self`和`other`的结果。

返回类型：

zipline.pipeline.Filter

```py
__gt__(other)
```

构造一个`Filter`，计算`self > other`。

参数：

**其他** (*zipline.pipeline.Factor**,* [*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 表达式的右侧。

返回：

**过滤器** – 使用`self > other`计算过滤器，输出`self`和`other`的结果。

返回类型：

zipline.pipeline.Filter

```py
fillna(fill_value)
```

创建一个新项，该项使用`fill_value`填充此项输出的缺失值。

参数：

**填充值** (*zipline.pipeline.ComputableTerm**, or* *object.*) –

用于替换缺失值的对象。

如果传递了一个可计算项（例如因子），则将使用该项的结果作为填充值。

如果传递的是标量（例如一个数字），则该标量将用作填充值。

示例

**使用标量填充：**

设`f`是一个将产生以下输出的因子：

```py
 AAPL   MSFT    MCD     BK
2017-03-13    1.0    NaN    3.0    4.0
2017-03-14    1.5    2.5    NaN    NaN 
```

然后`f.fillna(0)`产生以下输出：

```py
 AAPL   MSFT    MCD     BK
2017-03-13    1.0    0.0    3.0    4.0
2017-03-14    1.5    2.5    0.0    0.0 
```

**使用项填充：**

设`f`如上所述，设`g`是另一个将产生以下输出的因子：

```py
 AAPL   MSFT    MCD     BK
2017-03-13   10.0   20.0   30.0   40.0
2017-03-14   15.0   25.0   35.0   45.0 
```

然后，`f.fillna(g)`产生以下输出：

```py
 AAPL   MSFT    MCD     BK
2017-03-13    1.0   20.0    3.0    4.0
2017-03-14    1.5    2.5   35.0   45.0 
```

返回值：

**填充的** – 一个计算与`self`相同结果的项，但是使用`fill_value`中的值填充缺失值。

返回类型：

zipline.pipeline.ComputableTerm

```py
mean(mask=sentinel('NotSpecified'))
```

创建一个每天计算自身平均值的 1 维因子。

参数：

**掩码** (*zipline.pipeline.Filter**,* *可选*) – 一个表示在计算结果时要考虑的资产的 Filter。如果提供，我们将忽略`mask`产生`False`的资产/日期对。

返回值：

**结果**

返回类型：

zipline.pipeline.Factor

```py
stddev(mask=sentinel('NotSpecified'))
```

创建一个每天计算自身标准差的 1 维因子。

参数：

**掩码** (*zipline.pipeline.Filter**,* *可选*) – 一个表示在计算结果时要考虑的资产的 Filter。如果提供，我们将忽略`mask`产生`False`的资产/日期对。

返回值：

**结果**

返回类型：

zipline.pipeline.Factor

```py
max(mask=sentinel('NotSpecified'))
```

创建一个每天计算自身最大值的 1 维因子。

参数：

**掩码** (*zipline.pipeline.Filter**,* *可选*) – 一个表示在计算结果时要考虑的资产的 Filter。如果提供，我们将忽略`mask`产生`False`的资产/日期对。

返回值：

**结果**

返回类型：

zipline.pipeline.Factor

```py
min(mask=sentinel('NotSpecified'))
```

创建一个每天计算自身最小值的 1 维因子。

参数：

**掩码** (*zipline.pipeline.Filter**,* *可选*) – 一个表示在计算结果时要考虑的资产的 Filter。如果提供，我们将忽略`mask`产生`False`的资产/日期对。

返回值：

**结果**

返回类型：

zipline.pipeline.Factor

```py
median(mask=sentinel('NotSpecified'))
```

创建一个每天计算自身中位数的 1 维因子。

参数：

**掩码** (*zipline.pipeline.Filter**,* *可选*) – 一个表示在计算结果时要考虑的资产的 Filter。如果提供，我们将忽略`mask`产生`False`的资产/日期对。

返回值：

**结果**

返回类型：

zipline.pipeline.Factor

```py
sum(mask=sentinel('NotSpecified'))
```

创建一个每天计算自身总和的 1 维因子。

参数：

**掩码** (*zipline.pipeline.过滤器**,* *可选*) – 一个表示在计算结果时要考虑的资产的过滤器。如果提供，我们将忽略`掩码`产生`False`的资产/日期对。

返回：

**结果**

返回类型：

zipline.pipeline.因子

```py
class zipline.pipeline.Term(domain=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), window_safe=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), *args, **kwargs)
```

可以出现在`zipline.pipeline.管道`的计算图中的对象的基础类。

注意

大多数管道 API 用户只通过子类与`术语`交互：

+   `绑定列`

+   `因子`

+   `过滤器`

+   `分类器`

`术语`的实例是**记忆化**的。如果你用相同的参数两次调用一个术语的构造函数，那么两次调用都将返回相同的对象：

**示例：**

```py
>>> from zipline.pipeline.data import EquityPricing
>>> from zipline.pipeline.factors import SimpleMovingAverage
>>> x = SimpleMovingAverage(inputs=[EquityPricing.close], window_length=5)
>>> y = SimpleMovingAverage(inputs=[EquityPricing.close], window_length=5)
>>> x is y
True 
```

警告

术语的记忆化意味着通常在构造后修改术语的属性是不安全的。

```py
graph_repr()
```

用于渲染 GraphViz 图形的简短 repr。

```py
recursive_repr()
```

用于递归渲染具有输入的术语的简短 repr。

```py
class zipline.pipeline.data.DataSet
```

管道数据集的基础类。

`数据集`由两部分定义：

1.  一组`列`对象，描述数据集的可查询属性。

1.  描述由`数据集`表示的数据的资产和日历的`领域`。

要创建一个新的管道数据集，请定义一个`数据集`的子类，并将一个或多个`列`对象设置为类级属性。每个列都需要一个`np.dtype`，它描述了数据集的加载器应该生成的数据类型。整数列还必须提供一个“缺失值”，用于在给定的资产/日期组合中没有可用值时使用。

默认情况下，数据集的领域是特殊的单例值`通用`，这意味着它们可以在运行在**任何**领域的管道中使用。

在某些情况下，可能更希望将数据集限制为只支持单个领域。例如，一个数据集可能描述仅覆盖美国的供应商的数据。要将数据集限制为特定领域，请在类作用域中定义一个领域属性。

你还可以通过调用其`特化`方法并指定感兴趣的领域，来定义一个特定于领域的通用数据集版本。

示例

内置的 EquityPricing 数据集定义如下：

```py
class EquityPricing(DataSet):
    open = Column(float)
    high = Column(float)
    low = Column(float)
    close = Column(float)
    volume = Column(float) 
```

内置的 USEquityPricing 数据集是 EquityPricing 的特化。它定义为：

```py
from zipline.pipeline.domain import US_EQUITIES
USEquityPricing = EquityPricing.specialize(US_EQUITIES) 
```

列可以具有除浮点数之外的其他类型。包含各种公司元数据的数据集可能定义如下：

```py
class CompanyMetadata(DataSet):
    # Use float for semantically-numeric data, even if it's always
    # integral valued (see Notes section below). The default missing
    # value for floats is NaN.
    shares_outstanding = Column(float)

    # Use object for string columns. The default missing value for
    # object-dtype columns is None.
    ticker = Column(object)

    # Use integers for integer-valued categorical data like sector or
    # industry codes. Integer-dtype columns require an explicit missing
    # value.
    sector_code = Column(int, missing_value=-1)

    # Use bool for boolean-valued flags. Note that the default missing
    # value for bool-dtype columns is False.
    is_primary_share = Column(bool) 
```

注意

由于 numpy 没有原生支持带有缺失值的整数，强烈建议用户对任何语义上为数值的数据使用浮点数。这样做可以使用 NaN 作为自然的缺失值，这具有有用的传播语义。

```py
classmethod get_column(name)
```

通过名称查找列。

参数：

**名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要查找的列的名称。

返回：

**列** – 具有给定名称的列。

返回类型：

zipline.pipeline.data.BoundColumn

引发：

[**AttributeError**](https://docs.python.org/3/library/exceptions.html#AttributeError "(in Python v3.11)") – 如果指定的列名不存在。

```py
class zipline.pipeline.data.Column(dtype, missing_value=sentinel('NotSpecified'), doc=None, metadata=None, currency_aware=False)
```

一个抽象的数据列，尚未与数据集关联。

```py
bind(name)
```

将列对象与其名称绑定。

```py
class zipline.pipeline.data.BoundColumn(dtype, missing_value, dataset, name, doc, metadata, currency_conversion, currency_aware)
```

一个已经具体绑定到特定数据集的数据列。

```py
dtype
```

加载此列时生成的数据的 dtype。

类型：

[numpy.dtype](https://numpy.org/doc/stable/reference/generated/numpy.dtype.html#numpy.dtype "(in NumPy v1.25)")

```py
latest
```

一个`Filter`、`Factor`或`Classifier`，计算此列在每个日期的最近已知值。有关更多详细信息，请参阅`zipline.pipeline.mixins.LatestMixin`。

类型：

zipline.pipeline.LoadableTerm

```py
dataset
```

此列绑定的数据集。

类型：

zipline.pipeline.data.DataSet

```py
name
```

此列的名称。

类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
metadata
```

与此列关联的额外元数据。

类型：

[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")

```py
currency_aware
```

该列是否产生以货币计价的数据。

类型：

[bool](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")

注意

此类实例在访问`DataSet`的属性时动态创建。例如，`close`是此类的一个实例。管道 API 用户永远不应该直接构造此类实例。

```py
property currency_aware
```

该列是否产生以货币计价的数据。

```py
property currency_conversion
```

应用于本学期的货币转换规范。

```py
property dataset
```

此列绑定的数据集。

```py
fx(currency)
```

构造此列的货币转换版本。

参数：

**货币** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* *zipline.currency.Currency*) – 要将此列的数据转换成的货币。

返回：

**列** – 产生与`self`相同数据的列，但货币转换为`currency`。

返回类型：

绑定列

```py
graph_repr()
```

用于渲染管道图的简短表示。

```py
property metadata
```

这个列的元数据的副本。

```py
property name
```

这个列的名称。

```py
property qualname
```

这个列的全名。

```py
recursive_repr()
```

用于在递归上下文中渲染的简短表示。

```py
specialize(domain)
```

将`self`特化为具体的域。

```py
unspecialize()
```

将列泛化为通用形式。

这等效于`column.specialize(GENERIC)`。

```py
class zipline.pipeline.data.DataSetFamily
```

管道数据集家族的基类。

数据集家族用于表示那些行唯一标识符不仅需要资产和日期坐标的场景。一个`数据集家族`也可以被视为一组`数据集`对象的集合，每个对象都有相同的列、域和 ndim。

`数据集家族`对象是通过一个或多个`列`对象以及一个额外的字段：`extra_dims`来定义的。

`extra_dims`字段定义了除资产和日期之外必须固定的坐标，以生成逻辑时间序列。列对象决定了家族切片将共享的列。

`extra_dims`被表示为一个有序字典，其中键是维度名称，值是沿该维度的唯一值集合。

要在管道表达式中使用`数据集家族`，必须使用`slice()`方法为每个额外维度选择一个特定值。例如，给定一个`数据集家族`：

```py
class SomeDataSet(DataSetFamily):
    extra_dims = [
        ('dimension_0', {'a', 'b', 'c'}),
        ('dimension_1', {'d', 'e', 'f'}),
    ]

    column_0 = Column(float)
    column_1 = Column(bool) 
```

这个数据集可能代表具有以下列的表：

```py
sid :: int64
asof_date :: datetime64[ns]
timestamp :: datetime64[ns]
dimension_0 :: str
dimension_1 :: str
column_0 :: float64
column_1 :: bool 
```

这里我们可以看到隐含的`sid`、`asof_date`和`timestamp`列以及额外维度列。

这个`数据集家族`可以转换为常规的`数据集`：

```py
DataSetSlice = SomeDataSet.slice(dimension_0='a', dimension_1='e') 
```

这个切片数据集代表了在高维数据集中`(dimension_0 == 'a') & (dimension_1 == 'e')`的行。

```py
classmethod slice(*args, **kwargs)
```

对数据集家族进行切片以生成按资产和日期索引的数据集。

参数：

+   ***args** –

+   ****kwargs** – 沿每个额外维度固定的坐标。

返回：

**数据集** – 一个按资产和日期索引的常规管道数据集。

返回类型：

数据集

注意

用于生成结果的额外维度坐标可通过`extra_coords`属性获得。

```py
class zipline.pipeline.data.EquityPricing
```

`DataSet`包含每日交易价格和成交量。

```py
close = EquityPricing.close::float64
```

```py
high = EquityPricing.high::float64
```

```py
low = EquityPricing.low::float64
```

```py
open = EquityPricing.open::float64
```

```py
volume = EquityPricing.volume::float64
```

### 内置因子

因子旨在以一种提取算法可以交易信号的方式转换输入数据。

```py
class zipline.pipeline.factors.AverageDollarVolume(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

平均每日美元交易量

**默认输入**：[EquityPricing.close, EquityPricing.volume]

**默认窗口长度**：无

```py
compute(today, assets, out, close, volume)
```

通过一个函数重写此方法，该函数将一个值写入 out。

```py
class zipline.pipeline.factors.BollingerBands(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

布林带技术指标。[`zh.wikipedia.org/wiki/布林带`](https://zh.wikipedia.org/wiki/布林带)

**默认输入**：`zipline.pipeline.data.EquityPricing.close`

参数：

+   **输入**（*长度-1 可迭代***[*BoundColumn**]*） - 计算布林带的表达式。

+   **window_length**（*int > 0*） - 计算布林带的回溯窗口长度。

+   **k**（[*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) - 用于创建上下带的标准偏差数。

```py
compute(today, assets, out, close, k)
```

通过一个函数重写此方法，该函数将一个值写入 out。

```py
class zipline.pipeline.factors.BusinessDaysSincePreviousEvent(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), domain=sentinel('NotSpecified'), *args, **kwargs)
```

自上一个事件以来的业务日的抽象类。返回每个资产自最近事件日期以来的**工作日**（不是交易日！）的数量。

这并不使用交易日，以与 BusinessDaysUntilNextEarnings 保持对称。

今天宣布或即将宣布事件的资产将产生 0.0 的价值。在前一个工作日宣布事件的资产将产生 1.0 的价值。

对于事件日期为 NaT 的资产，将产生 NaN 值。

示例

`BusinessDaysSincePreviousEvent`可用于创建事件驱动因子。例如，你可能只想交易在过去 5 个工作日内有 asof_date 数据点的资产。为此，你可以创建一个`BusinessDaysSincePreviousEvent`因子，将你的数据集中相关的 asof_date 列作为输入，如下所示：

```py
# Factor computing number of days since most recent asof_date
# per asset.
days_since_event = BusinessDaysSincePreviousEvent(
    inputs=[MyDataset.asof_date]
)

# Filter returning True for each asset whose most recent asof_date
# was in the last 5 business days.
recency_filter = (days_since_event <= 5) 
```

```py
dtype = dtype('float64')
```

```py
class zipline.pipeline.factors.BusinessDaysUntilNextEvent(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), domain=sentinel('NotSpecified'), *args, **kwargs)
```

自下一个事件以来的业务日的抽象类。返回每个资产到下一个已知事件日期的**工作日**（不是交易日！）的数量。

这并不使用交易日，因为交易日历包含的信息可能在计算调用时对算法不可用。

例如，2001 年 9 月 11 日纽约证券交易所的收盘价在 9 月 10 日对算法来说是未知的。

今天宣布或即将宣布事件的资产将产生 0.0 的价值。将在下一个即将到来的工作日宣布事件的资产将产生 1.0 的价值。

对于事件日期为 NaT 的资产，将产生 NaN 值。

```py
dtype = dtype('float64')
```

```py
class zipline.pipeline.factors.DailyReturns(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

计算收盘价的每日百分比变化。

**默认输入**：[EquityPricing.close]

```py
class zipline.pipeline.factors.ExponentialWeightedMovingAverage(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

指数加权移动平均线

**默认输入**：无

**默认窗口长度**：无

参数：

+   **输入** (*长度为 1 的列表/元组* *的* *BoundColumn*) – 计算平均值的表达式。

+   **窗口长度** (*大于 0 的整数*) – 计算平均值的回溯窗口长度。

+   **衰减率** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *0 < 衰减率 <= 1*) –

    折扣过去观测值的加权因子。

    计算历史平均值时，行乘以序列：

    ```py
    decay_rate, decay_rate ** 2, decay_rate ** 3, ... 
    ```

注意

+   此类也可以通过名称`EWMA`导入。

另请参阅

[`pandas.DataFrame.ewm()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.ewm.html#pandas.DataFrame.ewm "(in pandas v2.0.3)")

```py
compute(today, assets, out, data, decay_rate)
```

通过一个函数重写此方法，该函数将一个值写入输出。

```py
class zipline.pipeline.factors.ExponentialWeightedMovingStdDev(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

指数加权移动标准差

**默认输入**：无

**默认窗口长度**：无

参数：

+   **输入** (*长度为 1 的列表/元组* *的* *BoundColumn*) – 计算平均值的表达式。

+   **窗口长度** (*大于 0 的整数*) – 计算平均值的回溯窗口长度。

+   **衰减率** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *0 < 衰减率 <= 1*) –

    折扣过去观测值的加权因子。

    计算历史平均值时，行乘以序列：

    ```py
    decay_rate, decay_rate ** 2, decay_rate ** 3, ... 
    ```

注意

+   此类也可以通过名称`EWMSTD`导入。

另请参阅

`pandas.DataFrame.ewm()`

```py
compute(today, assets, out, data, decay_rate)
```

通过一个函数重写此方法，该函数将一个值写入输出。

```py
class zipline.pipeline.factors.Latest(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

因子在每天产生 inputs[0]的最新已知值。

DataSet 列的.latest 属性返回此因子的实例。

```py
compute(today, assets, out, data)
```

通过一个函数重写此方法，该函数将一个值写入输出。

```py
zipline.pipeline.factors.MACDSignal
```

别名为`MovingAverageConvergenceDivergenceSignal`

```py
class zipline.pipeline.factors.MaxDrawdown(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

最大回撤

**默认输入**：无

**默认窗口长度**：无

```py
compute(today, assets, out, data)
```

通过一个函数重写此方法，该函数将一个值写入输出。

```py
class zipline.pipeline.factors.Returns(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

计算给定窗口长度内收盘价的变化百分比。

**默认输入**：[EquityPricing.close]

```py
compute(today, assets, out, close)
```

通过一个函数重写此方法，该函数将一个值写入输出。

```py
class zipline.pipeline.factors.RollingPearson(base_factor, target, correlation_length, mask=sentinel('NotSpecified'))
```

计算给定因子列与另一个因子/BoundColumn 的列或数据切片/单列之间的皮尔逊相关系数的因子。

参数：

+   **base_factor** (*zipline.pipeline.Factor*) – 与目标计算每个列相关性的因子。

+   **目标** (*zipline.pipeline.Term 具有数值数据类型*) – 与 base_factor 产生的每个数据列计算相关性的项。此项可以是因子、BoundColumn 或切片。如果目标为二维，则按资产计算相关性。

+   **相关长度** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 计算每个相关系数的回溯窗口长度。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 描述哪些资产（列）的 base_factor 应该每天计算其与目标的相关性。

另请参阅

[`scipy.stats.pearsonr()`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.pearsonr.html#scipy.stats.pearsonr "(in SciPy v1.11.1)"), `Factor.pearsonr()`, `zipline.pipeline.factors.RollingPearsonOfReturns`

注意

大多数用户应该调用 Factor.pearsonr 而不是直接构造这个类的实例。

```py
compute(today, assets, out, base_data, target_data)
```

使用一个函数重写这个方法，该函数将一个值写入 out。

```py
class zipline.pipeline.factors.RollingSpearman(base_factor, target, correlation_length, mask=sentinel('NotSpecified'))
```

一个因子，计算给定因子各列与另一个因子/绑定列或数据切片/单列数据的斯皮尔曼等级相关系数。

参数：

+   **base_factor** (*zipline.pipeline.Factor*) – 计算其每个列与目标相关性的因子。

+   **目标** (*zipline.pipeline.Term with a numeric dtype*) – 与 base_factor 产生的每个数据列计算相关性的项。这个项可以是因子、绑定列或切片。如果目标项是二维的，则按资产计算相关性。

+   **相关长度** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 计算每个相关系数的回溯窗口长度。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 描述哪些资产（列）的 base_factor 应该每天计算其与目标的相关性。

另请参阅

[`scipy.stats.spearmanr()`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.spearmanr.html#scipy.stats.spearmanr "(in SciPy v1.11.1)"), `Factor.spearmanr()`, `zipline.pipeline.factors.RollingSpearmanOfReturns`

注意

大多数用户应该调用 Factor.spearmanr 而不是直接构造这个类的实例。

```py
compute(today, assets, out, base_data, target_data)
```

使用一个函数重写这个方法，该函数将一个值写入 out。

```py
class zipline.pipeline.factors.RollingLinearRegressionOfReturns(target, returns_length, regression_length, mask=sentinel('NotSpecified'))
```

对给定资产的所有其他资产的回报进行普通最小二乘回归预测。

参数：

+   **目标** (*zipline.assets.Asset*) – 对所有其他资产进行回归的资产。

+   **回报长度** (*int >= 2*) – 计算回报的回溯窗口长度。日回报需要长度为 2 的窗口。

+   **regression_length** (*int >= 1*) – 计算每个回归的回溯窗口长度。

+   **mask** (*zipline.pipeline.Filter**,* *可选*) – 描述每天应将哪些资产与目标资产进行回归的过滤器。

注意

在许多资产上计算此因子可能耗时。建议使用掩码来限制计算回归的资产数量。

此因子旨在返回五个输出：

+   alpha，一个计算每个回归截距的因子。

+   beta，一个计算每个回归斜率的因子。

+   r_value，一个计算每个回归的相关系数的因子。

+   p_value，一个计算每个回归的双侧 p 值的因子，用于假设检验，其原假设是斜率为零。

+   stderr，一个计算每个回归的估计标准误差的因子。

如需了解更多关于多输出因子的帮助，请参阅`zipline.pipeline.CustomFactor`。

示例

假设以下是三个不同资产的 10 天回报示例：

```py
 SPY    MSFT     FB
2017-03-13    -.03     .03    .04
2017-03-14    -.02    -.03    .02
2017-03-15    -.01     .02    .01
2017-03-16       0    -.02    .01
2017-03-17     .01     .04   -.01
2017-03-20     .02    -.03   -.02
2017-03-21     .03     .01   -.02
2017-03-22     .04    -.02   -.02 
```

假设我们想通过 SPY 的回报来预测每只股票的回报，使用滚动 5 天回溯窗口。我们可以通过以下方式计算从 2017-03-17 到 2017-03-22 的滚动回归系数（alpha 和 beta）：

```py
regression_factor = RollingRegressionOfReturns(
    target=sid(8554),
    returns_length=10,
    regression_length=5,
)
alpha = regression_factor.alpha
beta = regression_factor.beta 
```

从 2017-03-17 到 2017-03-22 计算`alpha`的结果如下：

```py
 SPY    MSFT     FB
2017-03-17       0    .011   .003
2017-03-20       0   -.004   .004
2017-03-21       0    .007   .006
2017-03-22       0    .002   .008 
```

从 2017-03-17 到 2017-03-22 计算`beta`的结果如下：

```py
 SPY    MSFT     FB
2017-03-17       1      .3   -1.1
2017-03-20       1      .2     -1
2017-03-21       1     -.3     -1
2017-03-22       1     -.3    -.9 
```

请注意，alpha 列中 SPY 的所有值均为 0，beta 列中所有值均为 1，因为 SPY 与其自身的回归线仅仅是函数 y = x。

要了解其他每个值是如何计算的，以 2017-03-17 MSFT 的`alpha`和`beta`值（分别为.011 和.3）为例。这些值是通过运行线性回归预测 MSFT 的回报从 SPY 的回报，使用从 2017-03-17 开始并回溯 5 天的值。也就是说，回归是在 x = [-.03, -.02, -.01, 0, .01]和 y = [.03, -.03, .02, -.02, .04]上运行的，并产生了一个斜率为.3 和一个截距为.011 的结果。

另请参阅

`zipline.pipeline.factors.RollingPearsonOfReturns`， `zipline.pipeline.factors.RollingSpearmanOfReturns`

```py
class zipline.pipeline.factors.RollingPearsonOfReturns(target, returns_length, correlation_length, mask=sentinel('NotSpecified'))
```

计算给定资产的回报与所有其他资产的回报之间的皮尔逊积矩相关系数。

皮尔逊相关系数是大多数人所说的“相关系数”或“R 值”。

参数：

+   **目标** (*zipline.assets.Asset*) – 与所有其他资产相关联的资产。

+   **回报长度** (*int >= 2*) – 计算回报的回溯窗口长度。每日回报需要 2 天的窗口长度。

+   **相关长度** (*int >= 1*) – 计算每个相关系数的回溯窗口长度。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 描述每天应计算其与目标资产相关性的资产的过滤器。

注意

在多个资产上计算此因子可能耗时。建议使用掩码来限制计算相关性的资产数量。

示例

设以下为三个不同资产的 10 天回报示例：

```py
 SPY    MSFT     FB
2017-03-13    -.03     .03    .04
2017-03-14    -.02    -.03    .02
2017-03-15    -.01     .02    .01
2017-03-16       0    -.02    .01
2017-03-17     .01     .04   -.01
2017-03-20     .02    -.03   -.02
2017-03-21     .03     .01   -.02
2017-03-22     .04    -.02   -.02 
```

假设我们关注的是 SPY 在 2017-03-17 至 2017-03-22 期间与每只股票的滚动回报相关性，使用 5 天回溯窗口（即，我们计算 5 天数据的相关系数）。我们可以通过以下方式实现：

```py
rolling_correlations = RollingPearsonOfReturns(
    target=sid(8554),
    returns_length=10,
    correlation_length=5,
) 
```

从 2017-03-17 到 2017-03-22 计算`rolling_correlations`的结果为：

```py
 SPY   MSFT     FB
2017-03-17       1    .15   -.96
2017-03-20       1    .10   -.96
2017-03-21       1   -.16   -.94
2017-03-22       1   -.16   -.85 
```

请注意，SPY 列的所有值都是 1，因为任何数据系列与其自身的相关性始终为 1。要了解其他值是如何计算的，以 MSFT 列中的.15 为例。这是从 2017-03-17（-.03, -.02, -.01, 0, .01）回溯的 SPY 回报与 MSFT 回报（.03, -.03, .02, -.02, .04）之间的相关系数。

另请参阅

`zipline.pipeline.factors.RollingSpearmanOfReturns`, `zipline.pipeline.factors.RollingLinearRegressionOfReturns`

```py
class zipline.pipeline.factors.RollingSpearmanOfReturns(target, returns_length, correlation_length, mask=sentinel('NotSpecified'))
```

计算给定资产的回报与所有其他资产回报的斯皮尔曼等级相关系数。

参数：

+   **目标** (*zipline.assets.Asset*) – 与所有其他资产相关的资产。

+   **回报长度** (*int >= 2*) – 计算回报的回溯窗口长度。每日回报需要 2 天的窗口长度。

+   **相关长度** (*int >= 1*) – 计算每个相关系数的回溯窗口长度。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 描述每天应计算其与目标资产相关性的资产的过滤器。

注意

在多个资产上计算此因子可能耗时。建议使用掩码来限制计算相关性的资产数量。

另请参阅

`zipline.pipeline.factors.RollingPearsonOfReturns`, `zipline.pipeline.factors.RollingLinearRegressionOfReturns`

```py
class zipline.pipeline.factors.SimpleBeta(target, regression_length, allowed_missing_percentage=0.25)
```

因子，产生每项资产的日回报与单个“目标”资产的日回报之间的回归线的斜率。

参数：

+   **目标** (*zipline.Asset*) – 其他资产应与之回归的资产。

+   **回归长度** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 用于回归的日回报天数。

+   **允许缺失百分比** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 计算贝塔时允许缺失的回报观察值的百分比（介于 0 和 1 之间）。超过此百分比的回报观察值缺失的资产将产生 NaN 值。默认行为是 25%的输入可以缺失。

```py
compute(today, assets, out, all_returns, target_returns, allowed_missing_count)
```

通过编写一个函数来覆盖此方法，该函数将值写入输出。

```py
dtype = dtype('float64')
```

```py
graph_repr()
```

渲染管道图时使用的简短 repr。

```py
property target
```

获取贝塔计算的目标。

```py
class zipline.pipeline.factors.RSI(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

相对强弱指数

**默认输入**：`zipline.pipeline.data.EquityPricing.close`

**默认窗口长度**：15

```py
compute(today, assets, out, closes)
```

通过编写一个函数来覆盖此方法，该函数将值写入输出。

```py
class zipline.pipeline.factors.SimpleMovingAverage(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

任意列的平均值

**默认输入**：无

**默认窗口长度**：无

```py
compute(today, assets, out, data)
```

通过编写一个函数来覆盖此方法，该函数将值写入输出。

```py
class zipline.pipeline.factors.VWAP(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

成交量加权平均价格

**默认输入**：[EquityPricing.close, EquityPricing.volume]

**默认窗口长度**：无

```py
class zipline.pipeline.factors.WeightedAverageValue(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

用于 VWAP 类计算的辅助工具。

**默认输入**：无

**默认窗口长度**：无

```py
compute(today, assets, out, base, weight)
```

通过编写一个函数来覆盖此方法，该函数将值写入输出。

```py
class zipline.pipeline.factors.PercentChange(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

计算给定窗口长度内的百分比变化。

**默认输入**：无

**默认窗口长度**：无

注释

百分比变化计算公式为`(新值 - 旧值) / abs(旧值)`。

```py
compute(today, assets, out, values)
```

通过编写一个函数来覆盖此方法，该函数将值写入输出。

```py
class zipline.pipeline.factors.PeerCount(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

给定分类器中不同类别的对等计数。此因子由分类器实例方法 peer_count()返回。

**默认输入**：无

**默认窗口长度**：1

```py
compute(today, assets, out, classifier_values)
```

通过编写一个函数来覆盖此方法，该函数将值写入输出。

### 内置过滤器

```py
class zipline.pipeline.filters.All(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

一个过滤器，要求资产在`window_length`连续天内产生 True。

**默认输入**：无

**默认窗口长度**：无

```py
compute(today, assets, out, arg)
```

通过编写一个函数来覆盖此方法，该函数将值写入输出。

```py
class zipline.pipeline.filters.AllPresent(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

管道过滤器，指示给定窗口的输入项有数据。

```py
compute(today, assets, out, value)
```

通过编写一个函数来覆盖此方法，该函数将值写入输出。

```py
class zipline.pipeline.filters.Any(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

一个过滤器，要求资产在过去`window_length`天内至少有一天产生 True。

**默认输入**：无

**默认窗口长度**：无

```py
compute(today, assets, out, arg)
```

通过一个函数覆盖此方法，该函数将一个值写入 out。

```py
class zipline.pipeline.filters.AtLeastN(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

一个过滤器，要求资产在过去`window_length`天内至少有 N 天产生 True。

**默认输入**：无

**默认窗口长度**：无

```py
compute(today, assets, out, arg, N)
```

通过一个函数覆盖此方法，该函数将一个值写入 out。

```py
class zipline.pipeline.filters.SingleAsset(asset)
```

一个仅对给定资产计算为 True 的过滤器。

```py
graph_repr()
```

在渲染 GraphViz 图时使用的简短 repr。

```py
class zipline.pipeline.filters.StaticAssets(assets)
```

一个仅对预先确定的一组资产计算为 True 的过滤器。

`StaticAssets`主要用于调试或在事先知道的一组固定资产上交互式计算流水线术语。

参数：

**assets** (*iterable***[*Asset**]*) – 要过滤的资产的可迭代对象。

```py
class zipline.pipeline.filters.StaticSids(sids)
```

一个仅对预先确定的一组 sids 计算为 True 的过滤器。

`StaticSids`主要用于调试或在事先知道的一组固定 sids 上交互式计算流水线术语。

参数：

**sids** (*iterable**[*[*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*]*) – 要过滤的 sids 的可迭代对象。

### 流水线引擎

执行`Pipeline`的计算引擎定义了核心计算算法。

主要的入口点是 SimplePipelineEngine.run_pipeline，它实现了以下用于执行流水线的算法：

1.  确定流水线的域。

1.  构建流水线中所有术语的依赖图，包含每个术语从其输入中需要多少额外行的信息。

1.  将(2)中计算的域与我们的 AssetFinder 结合起来，生成一个“生命周期矩阵”。生命周期矩阵是一个布尔值的 DataFrame，其标签是日期 x 资产。每个条目对应一个(日期, 资产)对，并指示在给定日期该资产是否可交易。

1.  生成一个包含缓存或预先计算术语的“工作区”字典。

1.  对在(1)中构建的图进行拓扑排序，以生成任何未预填充术语的执行顺序。

1.  按照在(5)中计算的顺序迭代术语。对于每个术语：

    1.  从工作区获取术语的输入。

    1.  计算每个术语并将结果存储在工作区中。

    1.  如果不再需要结果，则从工作区中删除它们，以减少执行期间的内存使用。

1.  从工作区提取流水线的输出，并将它们转换成“窄”格式，输出标签由流水线的屏幕决定。

```py
class zipline.pipeline.engine.PipelineEngine
```

```py
abstract run_pipeline(pipeline, start_date, end_date, hooks=None)
```

从`start_date`到`end_date`计算`pipeline`的值。

参数：

+   **流水线** (*zipline.pipeline.Pipeline*) – 要运行的流水线。

+   **start_date** (*pd.Timestamp*) – 计算矩阵的起始日期。

+   **end_date** (*pd.Timestamp*) – 计算矩阵的结束日期。

+   **钩子** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11)")*[**实现**(**PipelineHooks**)**]**,* *可选*) – 用于检测管道执行的钩子。

返回：

**结果** – 计算结果的框架。

`结果`列对应于管道列的条目，它应该是一个字典，将字符串映射到`zipline.pipeline.Term`的实例。

对于`开始日期`和`结束日期`之间的每个日期，`结果`将包含通过管道筛选的每个资产的行。筛选为`None`表示应为每天存在的每个资产返回一行。

返回类型：

pd.DataFrame

```py
abstract run_chunked_pipeline(pipeline, start_date, end_date, chunksize, hooks=None)
```

从`开始日期`到`结束日期`计算`管道`的值，日期块大小为`块大小`。

分块执行减少了内存消耗，并且可能根据管道内容减少计算时间。

参数：

+   **管道** (*Pipeline*) – 要运行的管道。

+   **开始日期** (*pd.Timestamp*) – 运行管道的起始日期。

+   **结束日期** (*pd.Timestamp*) – 运行管道的结束日期。

+   **块大小** ([*整数*](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11)")) – 一次执行的天数。

+   **钩子** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11)")*[**实现**(**PipelineHooks**)**]**,* *可选*) – 用于检测管道执行的钩子。

返回：

**结果** – 计算结果的框架。

`结果`列对应于管道列的条目，它应该是一个字典，将字符串映射到`zipline.pipeline.Term`的实例。

对于`开始日期`和`结束日期`之间的每个日期，`结果`将包含通过管道筛选的每个资产的行。筛选为`None`表示应为每天存在的每个资产返回一行。

返回类型：

pd.DataFrame

另请参阅

`zipline.pipeline.engine.PipelineEngine.run_pipeline()`

```py
class zipline.pipeline.engine.SimplePipelineEngine(get_loader, asset_finder, default_domain=GENERIC, populate_initial_workspace=None, default_hooks=None)
```

计算每个术语独立的 PipelineEngine 类。

参数：

+   **获取加载器** (*可调用*) – 一个函数，给定一个可加载的术语，返回一个 PipelineLoader 以用于检索该术语的原始数据。

+   **资产查找器** (*zipline.assets.AssetFinder*) – 一个 AssetFinder 实例。我们依赖 AssetFinder 来确定在任何时间点哪些资产在顶级宇宙中。

+   **populate_initial_workspace** (*callable**,* *optional*) – 在计算管道时用于填充初始工作区的函数。有关更多信息，请参阅`zipline.pipeline.engine.default_populate_initial_workspace()`。

+   **default_hooks** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*,* *optional*) – 应该用于检测此引擎执行的所有管道的钩子列表。

另请参阅

`zipline.pipeline.engine.default_populate_initial_workspace()`

```py
__init__(get_loader, asset_finder, default_domain=GENERIC, populate_initial_workspace=None, default_hooks=None)
```

```py
run_chunked_pipeline(pipeline, start_date, end_date, chunksize, hooks=None)
```

计算从`start_date`到`end_date`的`pipeline`的值，每次执行的天数为`chunksize`。

分块执行减少了内存消耗，并且可能会根据管道内容减少计算时间。

参数：

+   **pipeline** (*Pipeline*) – 要运行的管道。

+   **start_date** (*pd.Timestamp*) – 管道运行的起始日期。

+   **end_date** (*pd.Timestamp*) – 管道运行的结束日期。

+   **chunksize** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 每次执行的天数。

+   **hooks** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*[**implements**(**PipelineHooks**)**]**,* *optional*) – 用于对管道执行进行检测的钩子。

返回值：

**result** – 计算结果的框架。

`result`列对应于 pipeline.columns 的条目，它应该是一个字典，将字符串映射到`zipline.pipeline.Term`的实例。

对于`start_date`和`end_date`之间的每个日期，`result`将包含通过 pipeline.screen 的每个资产的行。`None`的筛选条件表示应该为每天存在的每个资产返回一行。

返回类型：

pd.DataFrame

另请参阅

`zipline.pipeline.engine.PipelineEngine.run_pipeline()`

```py
run_pipeline(pipeline, start_date, end_date, hooks=None)
```

计算从`start_date`到`end_date`的`pipeline`的值。

参数：

+   **pipeline** (*zipline.pipeline.Pipeline*) – 要运行的管道。

+   **start_date** (*pd.Timestamp*) – 计算矩阵的起始日期。

+   **end_date** (*pd.Timestamp*) – 计算矩阵的结束日期。

+   **hooks** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*[**implements**(**PipelineHooks**)**]**,* *optional*) – 用于对管道执行进行检测的钩子。

返回值：

**result** – 计算结果的框架。

`result`列对应于 pipeline.columns 的条目，它应该是一个字典，将字符串映射到`zipline.pipeline.Term`的实例。

对于`start_date`和`end_date`之间的每个日期，`result`将包含通过 pipeline.screen 的每个资产的行。`None`的屏幕表示应该为每天存在的每个资产返回一行。

返回类型：

pd.DataFrame

```py
zipline.pipeline.engine.default_populate_initial_workspace(initial_workspace, root_mask_term, execution_plan, dates, assets)
```

`populate_initial_workspace`的默认实现。此函数返回未作任何修改的`initial_workspace`参数。

参数：

+   **初始工作区** ([*字典*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")*[**类似数组**]*) – 在我们填充任何缓存术语之前的工作区初始状态。

+   **根掩码术语** (*Term*) – 根掩码术语，通常是`AssetExists()`。这是为了计算各个术语的日期所必需的。

+   **执行计划** (*ExecutionPlan*) – 正在运行的管道的执行计划。

+   **日期** (*pd.DatetimeIndex*) – 在此管道运行中请求的所有日期，包括用于回溯窗口的额外日期。

+   **资产** (*pd.Int64Index*) – 在计算窗口中存在的所有资产。

返回：

**填充的初始工作区** – 开始计算的工作区。

返回类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[(术语, 类似数组)]

### 数据加载器

有几种加载器需要实现由`PipelineLoader`定义的接口，以向`Pipeline`提供数据。

```py
class zipline.pipeline.loaders.base.PipelineLoader(*args, **kwargs)
```

管道加载器的接口。

```py
load_adjusted_array(domain, columns, dates, sids, mask)
```

加载`columns`的 AdjustedArrays 数据。

参数：

+   **域** (*zipline.pipeline.domain.Domain*) – 必须加载请求数据的管道的域。

+   **列** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")**[*zipline.pipeline.data.dataset.BoundColumn**]*) – 请求数据的列。

+   **日期** (*pd.DatetimeIndex*) – 请求数据的日期。

+   **sids** (*pd.Int64Index*) – 请求数据的资产标识符。

+   **掩码** (*np.array**[**ndim=2**,* *dtype=bool**]*) – 形状为(len(dates), len(sids))的布尔数组，指示我们相信在哪些日期上请求的资产是活跃/可交易的。某些加载器使用此进行优化。

返回：

**数组** – 从列到 AdjustedArray 的映射，表示在请求日期上对请求 sids 的点对时滚动视图。

返回类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[(BoundColumn -> zipline.lib.adjusted_array.AdjustedArray)]

```py
__init__()
```

```py
class zipline.pipeline.loaders.frame.DataFrameLoader(column, baseline, adjustments=None)
```

一个从 DataFrame 读取输入的 PipelineLoader。

主要用于测试，但如果数据适合内存，也可用于实际工作。

参数：

+   **column** (*zipline.pipeline.data.BoundColumn*) – 该列的数据可由该加载器加载。

+   **baseline** ([*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)")) – 一个 DataFrame，其索引类型为 DatetimeIndex，列类型为 Int64Index。日期应标记为算法可以**获取**值的第一个日期。这意味着在提供给此类之前，OHLCV 数据通常应向后移动一个交易日。

+   **adjustments** ([*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)")*,* *default=None*) –

    具有以下列的 DataFrame：

    sid : int value : any kind : int (zipline.pipeline.loaders.frame.ADJUSTMENT_TYPES) start_date : datetime64 (可以是 NaT) end_date : datetime64 (必须设置) apply_date : datetime64 (必须设置)

    None 的默认值被解释为“不对基准进行调整”。

```py
__init__(column, baseline, adjustments=None)
```

```py
format_adjustments(dates, assets)
```

构建一个符合 AdjustedArray 预期的 Adjustment 对象字典。

返回一个字典，形式如下：{ # 整数索引，对应于我们应该应用调整列表的日期。1 : [ Float64Multiply(first_row=2, last_row=4, col=3, value=0.5), Float64Overwrite(first_row=3, last_row=5, col=1, value=2.0), … ], … }

```py
load_adjusted_array(domain, columns, dates, sids, mask)
```

从我们的存储基准中加载数据。

```py
class zipline.pipeline.loaders.equity_pricing_loader.EquityPricingLoader(raw_price_reader, adjustments_reader, fx_reader)
```

一个用于加载每日 OHLCV 数据的 PipelineLoader。

参数：

+   **raw_price_reader** (*zipline.data.session_bars.SessionBarReader*) – 提供原始价格的阅读器。

+   **adjustments_reader** (*zipline.data.adjustments.SQLiteAdjustmentReader*) – 提供价格/成交量调整的阅读器。

+   **fx_reader** (*zipline.data.fx.FXRateReader*) – 提供货币转换的阅读器。

```py
__init__(raw_price_reader, adjustments_reader, fx_reader)
```

```py
zipline.pipeline.loaders.equity_pricing_loader.USEquityPricingLoader
```

别名为 `EquityPricingLoader`

```py
class zipline.pipeline.loaders.events.EventsLoader(events, next_value_columns, previous_value_columns)
```

支持加载事件字段下一个和前一个值的 PipelineLoaders 的基类。

目前不支持调整。

参数：

+   **events** (*pd.DataFrame*) –

    代表与特定公司相关的事件（例如股票回购或收益公告）的 DataFrame。

    `events` 必须至少包含三列：：

    sidint64

    与每个事件关联的资产 ID。

    event_datedatetime64[ns]

    事件发生的日期。

    timestampdatetime64[ns]

    我们得知事件发生的日期。

+   **next_value_columns** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")*[**BoundColumn -> str**]*) – 数据集列到原始字段名的映射，用于在搜索下一个事件值时使用。

+   **previous_value_columns** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")*[**BoundColumn -> str**]*) – 数据集列到原始字段名的映射，用于在搜索前一个事件值时使用。

```py
__init__(events, next_value_columns, previous_value_columns)
```

```py
class zipline.pipeline.loaders.earnings_estimates.EarningsEstimatesLoader(estimates, name_map)
```

一个抽象的管道加载器，用于可以加载数据的估计数据，可以根据列的数据集的 num_announcements 属性，从日历日期向前/向后加载可变数量的季度。如果需要应用拆分调整，必须提供加载器、拆分调整的列和拆分调整的 asof 日期。

参数：

+   **estimates** (*pd.DataFrame*) –

    原始估计数据；必须至少包含 5 列：

    sidint64

    与每个估计关联的资产 id。

    event_datedatetime64[ns]

    估计所针对的事件发生/已发生的日期。

    timestampdatetime64[ns]

    我们得知该估计的日期。

    fiscal_quarterint64

    事件发生/将发生的季度。

    fiscal_yearint64

    事件发生/将发生的年份。

+   **name_map** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")*[**str -> str**]*) – 此加载器将加载的 BoundColumns 名称到事件中相应列名称的映射。

```py
__init__(estimates, name_map)
```

### 内置因子

因子旨在以一种提取算法可以交易信号的方式转换输入数据。

```py
class zipline.pipeline.factors.AverageDollarVolume(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

平均每日美元交易量

**默认输入：** [EquityPricing.close, EquityPricing.volume]

**默认窗口长度：** None

```py
compute(today, assets, out, close, volume)
```

通过一个函数覆盖此方法，该函数将一个值写入 out。

```py
class zipline.pipeline.factors.BollingerBands(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

布林带技术指标。[`en.wikipedia.org/wiki/Bollinger_Bands`](https://en.wikipedia.org/wiki/Bollinger_Bands)

**默认输入：** `zipline.pipeline.data.EquityPricing.close`

参数：

+   **inputs** (*长度为 1 的可迭代对象***[*BoundColumn**]*) – 用于计算布林带的表达式。

+   **window_length** (*int > 0*) – 用于计算布林带的回溯窗口长度。

+   **k** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")) – 用于创建上下带的正负标准差的数量。

```py
compute(today, assets, out, close, k)
```

通过一个函数覆盖此方法，该函数将一个值写入 out。

```py
class zipline.pipeline.factors.BusinessDaysSincePreviousEvent(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), domain=sentinel('NotSpecified'), *args, **kwargs)
```

自前一个事件以来的营业日抽象类。返回每个资产自最近事件日期以来的**营业日**（非交易日！）数量。

这与 BusinessDaysUntilNextEarnings 保持对称，不使用交易日。

今天宣布或即将宣布事件的资产将产生 0.0 的值。昨天宣布事件的资产将产生 1.0 的值。

对于事件日期为 NaT 的资产，将产生 NaN 值。

示例

`BusinessDaysSincePreviousEvent`可用于创建事件驱动的因子。例如，你可能只想交易最近 5 个工作日内有 asof_date 数据点的资产。为此，你可以创建一个`BusinessDaysSincePreviousEvent`因子，并提供数据集中相关的 asof_date 列作为输入，如下所示：

```py
# Factor computing number of days since most recent asof_date
# per asset.
days_since_event = BusinessDaysSincePreviousEvent(
    inputs=[MyDataset.asof_date]
)

# Filter returning True for each asset whose most recent asof_date
# was in the last 5 business days.
recency_filter = (days_since_event <= 5) 
```

```py
dtype = dtype('float64')
```

```py
class zipline.pipeline.factors.BusinessDaysUntilNextEvent(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), domain=sentinel('NotSpecified'), *args, **kwargs)
```

业务日自下一个事件以来的抽象类。返回每个资产到下一个已知事件日期的**业务日**（非交易日！）数量。

这不会使用交易日，因为交易日历包含的信息可能在计算时对算法不可用。

例如，2001 年 9 月 11 日的纽约证券交易所收盘价，在 9 月 10 日时算法是无法知晓的。

今天宣布或即将宣布事件的资产将产生 0.0 的值。将在下一个即将到来的业务日宣布事件的资产将产生 1.0 的值。

对于事件日期为 NaT 的资产，将产生 NaN 值。

```py
dtype = dtype('float64')
```

```py
class zipline.pipeline.factors.DailyReturns(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

计算收盘价的日百分比变化。

**默认输入**：[EquityPricing.close]

```py
class zipline.pipeline.factors.ExponentialWeightedMovingAverage(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

指数加权移动平均

**默认输入**：无

**默认窗口长度**：无

参数：

+   **输入**（长度为 1 的列表/元组，包含*BoundColumn*）– 用于计算平均值的表达式。

+   **window_length**（*int > 0*）– 用于计算平均值的回溯窗口长度。

+   **decay_rate**（[*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")，*0 < decay_rate <= 1*）–

    用于折扣过去观测值的权重因子。

    在计算历史平均值时，行会乘以序列：

    ```py
    decay_rate, decay_rate ** 2, decay_rate ** 3, ... 
    ```

注意

+   此类也可以通过名称`EWMA`导入。

另请参阅

[`pandas.DataFrame.ewm()`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.ewm.html#pandas.DataFrame.ewm "(in pandas v2.0.3)")

```py
compute(today, assets, out, data, decay_rate)
```

通过一个函数重写此方法，该函数将值写入 out。

```py
class zipline.pipeline.factors.ExponentialWeightedMovingStdDev(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

指数加权移动标准差

**默认输入**：无

**默认窗口长度**：无

参数：

+   **输入**（长度为 1 的列表/元组，包含*BoundColumn*）– 用于计算平均值的表达式。

+   **window_length**（*int > 0*）– 用于计算平均值的回溯窗口长度。

+   **decay_rate**（[*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")，*0 < decay_rate <= 1*）–

    用于折扣过去观测值的权重因子。

    在计算历史平均值时，行会乘以序列：

    ```py
    decay_rate, decay_rate ** 2, decay_rate ** 3, ... 
    ```

注意

+   此类也可以通过名称`EWMSTD`导入。

参见

`pandas.DataFrame.ewm()`

```py
compute(today, assets, out, data, decay_rate)
```

使用一个函数重写此方法，该函数将一个值写入 out。

```py
class zipline.pipeline.factors.Latest(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

因子，生成输入[0]在每个日期的最近已知值。

数据集列的.latest 属性返回此因子的实例。

```py
compute(today, assets, out, data)
```

使用一个函数重写此方法，该函数将一个值写入 out。

```py
zipline.pipeline.factors.MACDSignal
```

别名为`MovingAverageConvergenceDivergenceSignal`

```py
class zipline.pipeline.factors.MaxDrawdown(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

最大回撤

**默认输入**：无

**默认窗口长度**：无

```py
compute(today, assets, out, data)
```

使用一个函数重写此方法，该函数将一个值写入 out。

```py
class zipline.pipeline.factors.Returns(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

计算给定窗口长度内收盘价的变化百分比。

**默认输入**：[EquityPricing.close]

```py
compute(today, assets, out, close)
```

使用一个函数重写此方法，该函数将一个值写入 out。

```py
class zipline.pipeline.factors.RollingPearson(base_factor, target, correlation_length, mask=sentinel('NotSpecified'))
```

计算给定因子列与另一个因子/绑定列或数据切片/单列之间的皮尔逊相关系数的因子。

参数：

+   **base_factor** (*zipline.pipeline.Factor*) – 要计算其与目标的每个列的相关性的因子。

+   **target** (*zipline.pipeline.Term with a numeric dtype*) – 与 base_factor 生成的每个数据列计算相关性的项。此项可以是因子、绑定列或切片。如果目标项是二维的，则按资产计算相关性。

+   **correlation_length** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 计算每个相关系数的长度回溯窗口。

+   **mask** (*zipline.pipeline.Filter**,* *optional*) – 一个过滤器，描述每天应计算其与目标的相关性的 base_factor 的哪些资产（列）。

参见

[`scipy.stats.pearsonr()`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.pearsonr.html#scipy.stats.pearsonr "(in SciPy v1.11.1)"), `Factor.pearsonr()`, `zipline.pipeline.factors.RollingPearsonOfReturns`

注释

大多数用户应该调用 Factor.pearsonr 而不是直接构造此类的一个实例。

```py
compute(today, assets, out, base_data, target_data)
```

使用一个函数重写此方法，该函数将一个值写入 out。

```py
class zipline.pipeline.factors.RollingSpearman(base_factor, target, correlation_length, mask=sentinel('NotSpecified'))
```

计算给定因子列与另一个因子/绑定列或数据切片/单列之间的斯皮尔曼等级相关系数的因子。

参数：

+   **base_factor** (*zipline.pipeline.Factor*) – 要计算其与目标的每个列的相关性的因子。

+   **target** (*zipline.pipeline.Term with a numeric dtype*) – 与 base_factor 生成的每个数据列计算相关性的项。此项可以是因子、绑定列或切片。如果目标项是二维的，则按资产计算相关性。

+   **相关长度** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 计算每个相关系数的回溯窗口长度。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 描述了哪些资产（列）的基础因子应该每天计算与目标的关联性。

另请参阅

[`scipy.stats.spearmanr()`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.spearmanr.html#scipy.stats.spearmanr "(in SciPy v1.11.1)"), `Factor.spearmanr()`, `zipline.pipeline.factors.RollingSpearmanOfReturns`

注释

大多数用户应该调用 Factor.spearmanr 而不是直接构造这个类的实例。

```py
compute(today, assets, out, base_data, target_data)
```

通过一个函数重写此方法，该函数将一个值写入 out。

```py
class zipline.pipeline.factors.RollingLinearRegressionOfReturns(target, returns_length, regression_length, mask=sentinel('NotSpecified'))
```

执行一个普通最小二乘回归，预测给定资产的所有其他资产的回报。

参数：

+   **目标** (*zipline.assets.Asset*) – 针对所有其他资产进行回归的资产。

+   **回报长度** (*int >= 2*) – 计算回报的回溯窗口长度。每日回报需要 2 的窗口长度。

+   **回归长度** (*int >= 1*) – 计算每个回归的回溯窗口长度。

+   **掩码** (*zipline.pipeline.Filter**,* *可选*) – 描述了哪些资产应该每天针对目标资产进行回归。

注释

在许多资产上计算这个因子可能会很耗时。建议使用掩码来限制计算回归的资产数量。

这个因子被设计为返回五个输出：

+   alpha，一个计算每个回归截距的因子。

+   beta，一个计算每个回归斜率的因子。

+   r_value，一个计算每个回归相关系数的因子。

+   p_value，一个计算每个回归的双侧 p 值的因子，其零假设是斜率为零。

+   stderr，一个计算每个回归估计标准误差的因子。

有关多输出因子的更多帮助，请参阅`zipline.pipeline.CustomFactor`。

示例

让以下成为三个不同资产的 10 天回报的例子：

```py
 SPY    MSFT     FB
2017-03-13    -.03     .03    .04
2017-03-14    -.02    -.03    .02
2017-03-15    -.01     .02    .01
2017-03-16       0    -.02    .01
2017-03-17     .01     .04   -.01
2017-03-20     .02    -.03   -.02
2017-03-21     .03     .01   -.02
2017-03-22     .04    -.02   -.02 
```

假设我们感兴趣的是从 2017-03-17 到 2017-03-22 的滚动 5 天回溯窗口中，从 SPY 的回报预测每只股票的回报。我们可以通过以下方式计算滚动回归系数（alpha 和 beta）：

```py
regression_factor = RollingRegressionOfReturns(
    target=sid(8554),
    returns_length=10,
    regression_length=5,
)
alpha = regression_factor.alpha
beta = regression_factor.beta 
```

从 2017-03-17 到 2017-03-22 计算`alpha`的结果如下：

```py
 SPY    MSFT     FB
2017-03-17       0    .011   .003
2017-03-20       0   -.004   .004
2017-03-21       0    .007   .006
2017-03-22       0    .002   .008 
```

从 2017-03-17 到 2017-03-22 计算`beta`的结果如下：

```py
 SPY    MSFT     FB
2017-03-17       1      .3   -1.1
2017-03-20       1      .2     -1
2017-03-21       1     -.3     -1
2017-03-22       1     -.3    -.9 
```

注意，SPY 的 alpha 列都是 0，beta 列都是 1，因为 SPY 与自身的回归线仅仅是函数 y = x。

要了解其他每个值是如何计算的，以 2017-03-17 MSFT 的`alpha`和`beta`值（分别为.011 和.3）为例。这些值是通过运行线性回归预测 MSFT 的回报率从 SPY 的回报率得出的，使用从 2017-03-17 开始的值并回溯 5 天。也就是说，回归是在 x = [-.03, -.02, -.01, 0, .01]和 y = [.03, -.03, .02, -.02, .04]上运行的，并产生了一个斜率为.3 和一个截距为.011 的结果。

另请参阅

`zipline.pipeline.factors.RollingPearsonOfReturns`, `zipline.pipeline.factors.RollingSpearmanOfReturns`

```py
class zipline.pipeline.factors.RollingPearsonOfReturns(target, returns_length, correlation_length, mask=sentinel('NotSpecified'))
```

计算给定资产的回报率与所有其他资产的回报率之间的皮尔逊积矩相关系数。

皮尔逊相关系数是大多数人所说的“相关系数”或“R 值”。

参数：

+   **target**（*zipline.assets.Asset*）– 与所有其他资产相关的资产。

+   **returns_length**（*int >= 2*）– 计算回报率的回溯窗口长度。每日回报率需要一个长度为 2 的窗口。

+   **correlation_length**（*int >= 1*）– 计算每个相关系数的回溯窗口长度。

+   **mask**（*zipline.pipeline.Filter**,* *可选*）– 一个过滤器，描述每天应计算与目标资产相关的资产。

注意

计算许多资产的这一因子可能耗时。建议使用掩码来限制计算相关性的资产数量。

示例

让以下成为三个不同资产的 10 天回报率的示例：

```py
 SPY    MSFT     FB
2017-03-13    -.03     .03    .04
2017-03-14    -.02    -.03    .02
2017-03-15    -.01     .02    .01
2017-03-16       0    -.02    .01
2017-03-17     .01     .04   -.01
2017-03-20     .02    -.03   -.02
2017-03-21     .03     .01   -.02
2017-03-22     .04    -.02   -.02 
```

假设我们对 2017-03-17 到 2017-03-22 期间 SPY 与每只股票的滚动回报率相关性感兴趣，使用 5 天回溯窗口（即，我们计算 5 天数据上的每个相关系数）。我们可以通过以下方式实现：

```py
rolling_correlations = RollingPearsonOfReturns(
    target=sid(8554),
    returns_length=10,
    correlation_length=5,
) 
```

从 2017-03-17 到 2017-03-22 计算`rolling_correlations`的结果给出：

```py
 SPY   MSFT     FB
2017-03-17       1    .15   -.96
2017-03-20       1    .10   -.96
2017-03-21       1   -.16   -.94
2017-03-22       1   -.16   -.85 
```

注意，SPY 的列都是 1，因为任何数据系列与自身的相关性总是 1。要了解其他每个值是如何计算的，以 MSFT 列中的.15 为例。这是 SPY 的回报率从 2017-03-17 回溯（-.03, -.02, -.01, 0, .01）和 MSFT 的回报率（.03, -.03, .02, -.02, .04）之间的相关系数。

另请参阅

`zipline.pipeline.factors.RollingSpearmanOfReturns`, `zipline.pipeline.factors.RollingLinearRegressionOfReturns`

```py
class zipline.pipeline.factors.RollingSpearmanOfReturns(target, returns_length, correlation_length, mask=sentinel('NotSpecified'))
```

计算给定资产的回报与所有其他资产回报的斯皮尔曼等级相关系数。

参数：

+   **target** (*zipline.assets.Asset*) – 与所有其他资产相关的资产。

+   **returns_length** (*int >= 2*) – 计算回报的回溯窗口长度。日回报需要长度为 2 的窗口。

+   **correlation_length** (*int >= 1*) – 计算每个相关系数的回溯窗口长度。

+   **mask** (*zipline.pipeline.Filter**,* *可选*) – 描述哪些资产应该每天计算与目标资产的相关性的过滤器。

注释

在许多资产上计算此因子可能耗时。建议使用掩码来限制计算相关性的资产数量。

另请参阅

`zipline.pipeline.factors.RollingPearsonOfReturns`, `zipline.pipeline.factors.RollingLinearRegressionOfReturns`

```py
class zipline.pipeline.factors.SimpleBeta(target, regression_length, allowed_missing_percentage=0.25)
```

因子产生每项资产的日回报与单一“目标”资产的日回报之间回归线的斜率。

参数：

+   **target** (*zipline.Asset*) – 其他资产应与之回归的资产。

+   **regression_length** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 用于回归的日回报的天数。

+   **allowed_missing_percentage** ([*float*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*,* *可选*) – 计算贝塔时允许缺失的回报观察值的百分比（介于 0 和 1 之间）。超过此百分比的回报观察值缺失的资产将产生 NaN 值。默认行为是允许 25%的输入缺失。

```py
compute(today, assets, out, all_returns, target_returns, allowed_missing_count)
```

通过一个函数重写此方法，该函数将值写入输出。

```py
dtype = dtype('float64')
```

```py
graph_repr()
```

在渲染管道图时使用的简短 repr。

```py
property target
```

获取贝塔计算的目标。

```py
class zipline.pipeline.factors.RSI(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

相对强弱指数

**默认输入**: `zipline.pipeline.data.EquityPricing.close`

**默认窗口长度**: 15

```py
compute(today, assets, out, closes)
```

通过一个函数重写此方法，该函数将值写入输出。

```py
class zipline.pipeline.factors.SimpleMovingAverage(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

任意列的平均值

**默认输入**: 无

**默认窗口长度**: 无

```py
compute(today, assets, out, data)
```

通过一个函数重写此方法，该函数将值写入输出。

```py
class zipline.pipeline.factors.VWAP(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

成交量加权平均价格

**默认输入：** [EquityPricing.close, EquityPricing.volume]

**默认窗口长度：** 无

```py
class zipline.pipeline.factors.WeightedAverageValue(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

用于类似 VWAP 计算的辅助工具。

**默认输入：** 无

**默认窗口长度：** 无

```py
compute(today, assets, out, base, weight)
```

通过一个函数重写此方法，该函数将值写入 out。

```py
class zipline.pipeline.factors.PercentChange(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

计算给定 `window_length` 的百分比变化。

**默认输入：** 无

**默认窗口长度：** 无

注释

百分比变化计算为 `(new - old) / abs(old)`。

```py
compute(today, assets, out, values)
```

通过一个函数重写此方法，该函数将值写入 out。

```py
class zipline.pipeline.factors.PeerCount(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

给定分类器中不同类别的对等计数。此因子由分类器实例方法 peer_count() 返回。

**默认输入：** 无

**默认窗口长度：** 1

```py
compute(today, assets, out, classifier_values)
```

通过一个函数重写此方法，该函数将值写入 out。

### 内置过滤器

```py
class zipline.pipeline.filters.All(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

一个过滤器，要求资产连续 `window_length` 天产生 True。

**默认输入：** 无

**默认窗口长度：** 无

```py
compute(today, assets, out, arg)
```

通过一个函数重写此方法，该函数将值写入 out。

```py
class zipline.pipeline.filters.AllPresent(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

管道过滤器，指示给定窗口的输入项有数据。

```py
compute(today, assets, out, value)
```

通过一个函数重写此方法，该函数将值写入 out。

```py
class zipline.pipeline.filters.Any(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

一个过滤器，要求资产在过去 `window_length` 天内至少有一天产生 True。

**默认输入：** 无

**默认窗口长度：** 无

```py
compute(today, assets, out, arg)
```

通过一个函数重写此方法，该函数将值写入 out。

```py
class zipline.pipeline.filters.AtLeastN(inputs=sentinel('NotSpecified'), outputs=sentinel('NotSpecified'), window_length=sentinel('NotSpecified'), mask=sentinel('NotSpecified'), dtype=sentinel('NotSpecified'), missing_value=sentinel('NotSpecified'), ndim=sentinel('NotSpecified'), **kwargs)
```

一个过滤器，要求资产在过去 `window_length` 天内至少有 N 天产生 True。

**默认输入：** 无

**默认窗口长度：** 无

```py
compute(today, assets, out, arg, N)
```

通过一个函数重写此方法，该函数将值写入 out。

```py
class zipline.pipeline.filters.SingleAsset(asset)
```

一个过滤器，仅对给定资产计算为 True。

```py
graph_repr()
```

在渲染 GraphViz 图形时使用的简短 repr。

```py
class zipline.pipeline.filters.StaticAssets(assets)
```

一个过滤器，对于预先确定的一组资产计算为 True。

`StaticAssets` 主要用于调试或在事先已知固定资产集的情况下，交互式计算管道项。

参数：

**assets** (*iterable***[*Asset**]*) – 要过滤的资产的可迭代对象。

```py
class zipline.pipeline.filters.StaticSids(sids)
```

一个过滤器，对于预先确定的一组 sid 计算为 True。

`StaticSids` 主要用于调试或在事先已知固定 sid 集的情况下，交互式计算管道项。

参数：

**sids** (*iterable**[*[*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*]*) – 要过滤的 sid 的可迭代对象。

### 管道引擎

执行 `Pipeline` 的计算引擎定义了核心计算算法。

主要入口点是 SimplePipelineEngine.run_pipeline，它实现了以下执行管道的算法：

1.  确定管道的域。

1.  构建管道中所有项的依赖图，并提供每个项从其输入中需要多少额外行的信息。

1.  将(2)中计算的域与我们的 AssetFinder 结合起来，生成一个“生命周期矩阵”。生命周期矩阵是一个布尔值的 DataFrame，其标签是日期 x 资产。每个条目对应于一个(日期，资产)对，并指示在给定日期该资产是否可交易。

1.  生成一个包含缓存或预计算项的“工作区”字典。

1.  对在(1)中构建的图进行拓扑排序，以生成任何未预填充项的执行顺序。

1.  按照在(5)中计算的顺序迭代项。对于每个项：

    1.  从工作区中获取项的输入。

    1.  计算每个项并将结果存储在工作区中。

    1.  如果不再需要结果，则从工作区中删除它们以减少执行期间的内存使用。

1.  从工作区中提取管道的输出，并将它们转换为“窄”格式，输出标签由管道的屏幕决定。

```py
class zipline.pipeline.engine.PipelineEngine
```

```py
abstract run_pipeline(pipeline, start_date, end_date, hooks=None)
```

从`start_date`到`end_date`计算`pipeline`的值。

参数：

+   **pipeline** (*zipline.pipeline.Pipeline*) – 要运行的管道。

+   **start_date** (*pd.Timestamp*) – 计算矩阵的起始日期。

+   **end_date** (*pd.Timestamp*) – 计算矩阵的结束日期。

+   **hooks** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*[**implements**(**PipelineHooks**)**]**,* *可选*) – 用于检测 Pipeline 执行的钩子。

返回：

**result** – 计算结果的框架。

`result`列对应于 pipeline.columns 的条目，这应该是一个字典，将字符串映射到`zipline.pipeline.Term`的实例。

对于`start_date`和`end_date`之间的每个日期，`result`将包含通过 pipeline.screen 的每个资产的行。屏幕为`None`表示应该为每天存在的每个资产返回一行。

返回类型：

pd.DataFrame

```py
abstract run_chunked_pipeline(pipeline, start_date, end_date, chunksize, hooks=None)
```

从`start_date`到`end_date`计算`pipeline`的值，日期块大小为`chunksize`。

分块执行减少了内存消耗，并且可能会根据管道内容减少计算时间。

参数：

+   **pipeline** (*Pipeline*) – 要运行的管道。

+   **start_date** (*pd.Timestamp*) – 运行管道的起始日期。

+   **end_date** (*pd.Timestamp*) – 运行管道的结束日期。

+   **chunksize** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 一次执行的天数。

+   **hooks** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*[**implements**(**PipelineHooks**)**]**,* *可选*) – 用于检测 Pipeline 执行的钩子。

返回：

**result** – 计算结果的框架。

结果列对应于 pipeline.columns 的条目，该条目应为将字符串映射到`zipline.pipeline.Term`实例的字典。

对于`start_date`和`end_date`之间的每个日期，结果将包含通过 pipeline.screen 的每个资产的行。`None`的筛选条件表示应为每天存在的每个资产返回一行。

返回类型：

pd.DataFrame

另请参阅

`zipline.pipeline.engine.PipelineEngine.run_pipeline()`

```py
class zipline.pipeline.engine.SimplePipelineEngine(get_loader, asset_finder, default_domain=GENERIC, populate_initial_workspace=None, default_hooks=None)
```

PipelineEngine 类，该类独立计算每个项。

参数：

+   **get_loader** (*可调用函数*) – 给定一个可加载项的函数，返回用于检索该项原始数据的 PipelineLoader。

+   **asset_finder** (*zipline.assets.AssetFinder*) – 一个 AssetFinder 实例。我们依赖 AssetFinder 来确定在任何时间点哪些资产位于顶级宇宙中。

+   **populate_initial_workspace** (*可调用函数*，*可选*）– 在计算管道时用于填充初始工作区的函数。有关更多信息，请参阅`zipline.pipeline.engine.default_populate_initial_workspace()`。

+   **default_hooks** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*，*可选*）– 应使用此引擎执行的所有管道的检测钩子列表。

另请参阅

`zipline.pipeline.engine.default_populate_initial_workspace()`

```py
__init__(get_loader, asset_finder, default_domain=GENERIC, populate_initial_workspace=None, default_hooks=None)
```

```py
run_chunked_pipeline(pipeline, start_date, end_date, chunksize, hooks=None)
```

计算从`start_date`到`end_date`的`pipeline`的值，日期块大小为`chunksize`。

分块执行减少了内存消耗，并且根据管道内容可能会减少计算时间。

参数：

+   **pipeline** (*Pipeline*) – 要运行的管道。

+   **start_date** (*pd.Timestamp*) – 运行管道的开始日期。

+   **结束日期** (*pd.Timestamp*) – 运行管道的结束日期。

+   **chunksize** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 一次执行的天数。

+   **hooks** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*[**实现**(**PipelineHooks**)**]**，*可选*）– 用于检测管道执行的钩子。

返回值：

**result** – 计算结果的框架。

结果列对应于 pipeline.columns 的条目，该条目应为将字符串映射到`zipline.pipeline.Term`实例的字典。

对于`开始日期`和`结束日期`之间的每个日期，`结果`将包含通过管道.screen 的每个资产的行。`None`的屏幕表示应该为每天存在的每个资产返回一行。

返回类型：

pd.DataFrame

另请参阅

`zipline.pipeline.engine.PipelineEngine.run_pipeline()`

```py
run_pipeline(pipeline, start_date, end_date, hooks=None)
```

从`开始日期`到`结束日期`计算`管道`的值。

参数：

+   **管道** (*zipline.pipeline.Pipeline*) – 要运行的管道。

+   **开始日期** (*pd.Timestamp*) – 计算矩阵的起始日期。

+   **结束日期** (*pd.Timestamp*) – 计算矩阵的结束日期。

+   **钩子** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*[**implements**(**PipelineHooks**)**]**,* *可选*) – 用于检测管道执行的钩子。

返回：

**结果** – 计算结果的框架。

`结果`列对应于 pipeline.columns 的条目，它应该是一个字典，将字符串映射到`zipline.pipeline.Term`的实例。

对于`开始日期`和`结束日期`之间的每个日期，`结果`将包含通过管道.screen 的每个资产的行。`None`的屏幕表示应该为每天存在的每个资产返回一行。

返回类型：

pd.DataFrame

```py
zipline.pipeline.engine.default_populate_initial_workspace(initial_workspace, root_mask_term, execution_plan, dates, assets)
```

`填充初始工作区`的默认实现。此函数返回`initial_workspace`参数，不做任何修改。

参数：

+   **初始工作区** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")*[**array-like**]*) – 在用任何缓存项填充之前，我们的初始工作区。

+   **根掩码项** (*Term*) – 根掩码项，通常为`AssetExists()`。这是为了计算各个项的日期。

+   **执行计划** (*ExecutionPlan*) – 正在运行的管道的执行计划。

+   **日期** (*pd.DatetimeIndex*) – 此管道运行中请求的所有日期，包括用于回溯窗口的额外日期。

+   **资产** (*pd.Int64Index*) – 正在计算的窗口中存在的所有资产。

返回：

**填充的初始工作区** – 开始计算的工作区。

返回类型：

[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[term, array-like]

### 数据加载器

有多个加载器需要实现由`PipelineLoader`定义的接口，以向`Pipeline`提供数据。

```py
class zipline.pipeline.loaders.base.PipelineLoader(*args, **kwargs)
```

管道加载器的接口。

```py
load_adjusted_array(domain, columns, dates, sids, mask)
```

加载`columns`的数据作为 AdjustedArrays。

参数：

+   **域** (*zipline.pipeline.domain.Domain*) – 请求的数据必须加载的管道的域。

+   **列** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")**[*zipline.pipeline.data.dataset.BoundColumn**]*) – 请求数据的列。

+   **日期** (*pd.DatetimeIndex*) – 请求数据的日期。

+   **sids** (*pd.Int64Index*) – 请求数据的资产标识符。

+   **掩码** (*np.array*[*ndim=2*,*dtype=bool*]*) – 形状为(len(dates), len(sids))的布尔数组，指示我们认为请求的资产在哪些日期是活跃/可交易的。这在某些加载器的优化中使用。

返回:

**数组** – 从列到 AdjustedArray 的映射，表示请求的 sids 在请求的日期上的点-时间滚动视图。

返回类型:

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[BoundColumn -> zipline.lib.adjusted_array.AdjustedArray]

```py
__init__()
```

```py
class zipline.pipeline.loaders.frame.DataFrameLoader(column, baseline, adjustments=None)
```

从 DataFrames 读取输入的 PipelineLoader。

主要用于测试，但如果您的数据适合内存，也可以用于实际工作。

参数:

+   **列** (*zipline.pipeline.data.BoundColumn*) – 该列的数据可通过此加载器加载。

+   **基线** ([*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)")) – 具有 DatetimeIndex 类型索引和 Int64Index 类型列的 DataFrame。日期应标记为算法可以**获取**值的第一个日期。这意味着在提供给此类之前，OHLCV 数据通常应向后移动一个交易日。

+   **调整** ([*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)")*,* *默认=None*) –

    具有以下列的 DataFrame：

    sid : int 值 : 任意类型 : int (zipline.pipeline.loaders.frame.ADJUSTMENT_TYPES) start_date : datetime64 (可以是 NaT) end_date : datetime64 (必须设置) apply_date : datetime64 (必须设置)

    默认的 None 被解释为“不对基线进行调整”。

```py
__init__(column, baseline, adjustments=None)
```

```py
format_adjustments(dates, assets)
```

构建 AdjustedArray 预期的 Adjustment 对象字典格式。

返回一个字典，形式如下：{ # 我们应该应用调整列表的日期在日期中的整数索引。 1 : [ Float64Multiply(first_row=2, last_row=4, col=3, value=0.5), Float64Overwrite(first_row=3, last_row=5, col=1, value=2.0), … ], … }

```py
load_adjusted_array(domain, columns, dates, sids, mask)
```

从我们的存储基线加载数据。

```py
class zipline.pipeline.loaders.equity_pricing_loader.EquityPricingLoader(raw_price_reader, adjustments_reader, fx_reader)
```

用于加载每日 OHLCV 数据的 PipelineLoader。

参数:

+   **raw_price_reader** (*zipline.data.session_bars.SessionBarReader*) – 提供原始价格的阅读器。

+   **adjustments_reader** (*zipline.data.adjustments.SQLiteAdjustmentReader*) – 提供价格/数量调整的阅读器。

+   **fx_reader** (*zipline.data.fx.FXRateReader*) – 提供货币转换的阅读器。

```py
__init__(raw_price_reader, adjustments_reader, fx_reader)
```

```py
zipline.pipeline.loaders.equity_pricing_loader.USEquityPricingLoader
```

别名为 `EquityPricingLoader`

```py
class zipline.pipeline.loaders.events.EventsLoader(events, next_value_columns, previous_value_columns)
```

支持加载事件字段下一个和前一个值的 PipelineLoaders 的基类。

目前不支持调整。

参数：

+   **事件** (*pd.DataFrame*) –

    代表与特定公司相关的事件（例如股票回购或盈利公告）的数据框。

    `events` 必须至少包含三列：

    sidint64

    与每个事件关联的资产 id。

    event_datedatetime64[ns]

    事件发生的日期。

    时间戳 datetime64[ns]

    我们得知事件的日期。

+   **next_value_columns** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")*[**BoundColumn -> str**]*) – 从数据集列到原始字段名称的映射，用于在搜索下一个事件值时应使用的字段名称。

+   **previous_value_columns** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")*[**BoundColumn -> str**]*) – 从数据集列到原始字段名称的映射，用于在搜索前一个事件值时应使用的字段名称。

```py
__init__(events, next_value_columns, previous_value_columns)
```

```py
class zipline.pipeline.loaders.earnings_estimates.EarningsEstimatesLoader(estimates, name_map)
```

一个抽象的估计数据管道加载器，可以根据列的数据集的 num_announcements 属性从日历日期向前/向后加载可变数量的季度数据。如果需要应用分割调整，必须提供加载器、分割调整列和分割调整的 asof 日期。

参数：

+   **estimates** (*pd.DataFrame*) –

    原始估计数据；必须至少包含 5 列：

    sidint64

    与每个估计关联的资产 id。

    event_datedatetime64[ns]

    估计所针对的事件将/已经发生的日期。

    时间戳 datetime64[ns]

    我们得知估计的时间。

    fiscal_quarterint64

    事件发生/将发生的季度。

    fiscal_yearint64

    事件发生/将发生的年份。

+   **name_map** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")*[**str -> str**]*) – 此加载器将加载的 BoundColumns 的名称到事件中相应列的名称的映射。

```py
__init__(estimates, name_map)
```

## 交易所和资产元数据

```py
class zipline.assets.ExchangeInfo(name, canonical_name, country_code)
```

资产交易的交易所。

参数：

+   **名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* *None*) – 交易所的全名，例如‘纽约证券交易所’或‘纳斯达克全球市场’。

+   **canonical_name** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 交易所的规范名称，例如“NYSE”或“NASDAQ”。如果为 None，则将与名称相同。

+   **country_code** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 交易所所在国家的代码。

```py
name
```

交易所的全名，例如“纽约证券交易所”或“纳斯达克全球市场”。

类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") 或 None

```py
canonical_name
```

交易所的规范名称，例如“NYSE”或“NASDAQ”。如果为 None，则将与名称相同。

类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
country_code
```

交易所所在国家的代码。

类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
calendar
```

交易所使用的交易日历。

类型：

交易日历

```py
property calendar
```

该交易所使用的交易日历。

```py
class zipline.assets.Asset
```

交易算法可以拥有的实体的基类。

```py
sid
```

分配给资产的持久唯一标识符。

类型：

[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")

```py
symbol
```

资产最近一次交易的代号。如果资产更换代号，此字段可能会在没有警告的情况下更改。如果需要持久标识符，请使用`sid`。

类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
asset_name
```

资产的全名。

类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
exchange
```

资产交易的交易所的规范简称（例如，“NYSE”）。

类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
exchange_full
```

资产交易的交易所全名（例如，“纽约证券交易所”）。

类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
exchange_info
```

该资产上市的交易所信息。

类型：

zipline.assets.ExchangeInfo

```py
country_code
```

表示资产交易所在国家的两个字符代码。

类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
start_date
```

资产首次交易日期。

类型：

pd.Timestamp

```py
end_date
```

资产最后一次交易的日期。在 Quantopian 上，对于仍在交易的资产，此值设置为当前（实时）日期。

类型：

pd.Timestamp

```py
tick_size
```

该资产价格最小变动金额。

类型：

[float](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")

```py
auto_close_date
```

在模拟中，该资产的持仓将在该日期自动清算为现金。默认情况下，这是`end_date`之后的三天。

类型：

pd.Timestamp

```py
from_dict()
```

从字典构建资产实例。

```py
is_alive_for_session()
```

返回资产在给定 dt 时是否存活。

参数：

**session_label** (*pd.Timestamp*) – 要检查的所需会话标签。（UTC 午夜）

返回：

**布尔值**

返回类型：

资产在给定 dt 时是否存活。

```py
is_exchange_open()
```

参数：

**dt_ 分钟** (*pd.Timestamp* *(**UTC**,* *时区感知**)*) – 要检查的分钟。

返回：

**布尔值**

返回类型：

资产的交易所是否在给定分钟内开放。

```py
to_dict()
```

转换为包含资产所有属性的 Python 字典。

这在调试时通常很有用。

返回：

**作为字典**

返回类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")

```py
class zipline.assets.Equity
```

资产子类，代表对公司、信托或合伙企业的部分所有权。

```py
class zipline.assets.Future
```

资产子类，代表期货合约的所有权。

```py
to_dict()
```

转换为 Python 字典。

```py
class zipline.assets.AssetConvertible
```

可转换为资产整数表示的类型的抽象基类。

包括资产、字符串和整数

## 交易日历 API

算法执行的时间线事件遵循特定的`TradingCalendar`。

## 数据 API

### 写入器

```py
class zipline.data.bcolz_daily_bars.BcolzDailyBarWriter(filename, calendar, start_session, end_session)
```

能够将每日 OHLCV 数据写入磁盘的类，该格式可以被 BcolzDailyOHLCVReader 高效读取。

参数：

+   **文件名** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 我们应该写入输出的位置。

+   **日历** (*zipline.utils.calendar.trading_calendar*) – 用于计算资产日历偏移的日历。

+   **开始会话** (*pd.Timestamp*) – 午夜 UTC 会话标签。

+   **结束会话** (*pd.Timestamp*) – 午夜 UTC 会话标签。

另请参阅

`zipline.data.bcolz_daily_bars.BcolzDailyBarReader`

```py
write(data, assets=None, show_progress=False, invalid_data_behavior='warn')
```

参数：

+   **数据** (*可迭代**[*[*元组*](https://docs.python.org/3/library/stdtypes.html#tuple "(in Python v3.11)")*[*[*整数*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* [*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)") *或* *bcolz.ctable**]**]*) – 要写入的数据块。每个块应为 sid 和该资产数据的元组。

+   **资产** ([*集合*](https://docs.python.org/3/library/stdtypes.html#set "(in Python v3.11)")*[*[*整数*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*]**,* *可选*) – 应包含在`data`中的资产。如果提供了此参数，我们将检查`data`与资产的匹配情况，并提供更详细的进度信息。

+   **显示进度** ([*布尔值*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 在写入时是否显示进度条。

+   **无效数据行为** (*{'警告'**,* *'引发'**,* *'忽略'}**,* *可选*) – 当遇到超出 uint32 范围的数据时应该采取的措施。

返回：

**表** – 新写入的表。

返回类型：

bcolz.ctable

```py
write_csvs(asset_map, show_progress=False, invalid_data_behavior='warn')
```

从我们的资产映射中读取 CSV 作为数据框。

参数：

+   **资产映射** ([*字典*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")*[**整数 -> 字符串**]*) – 资产 ID 到包含该资产 CSV 数据的文件路径的映射

+   **显示进度** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 在写入时是否显示进度条。

+   **无效数据行为** (*{'warn'**,* *'raise'**,* *'ignore'}*) – 当遇到超出 uint32 范围的数据时应该做什么。

```py
class zipline.data.adjustments.SQLiteAdjustmentWriter(conn_or_path, equity_daily_bar_reader, overwrite=False)
```

供 SQLiteAdjustmentReader 读取的数据写入器

参数：

+   **conn_or_path** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* [*sqlite3.Connection*](https://docs.python.org/3/library/sqlite3.html#sqlite3.Connection "(in Python v3.11)")) – 目标 sqlite 数据库的句柄。

+   **equity_daily_bar_reader** (*SessionBarReader*) – 用于股息写入的日柱读取器。

+   **覆盖** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选**,* *默认=False*) – 如果为 True 且 conn_or_path 是字符串，则在连接之前删除给定路径上的任何现有文件。

另请参阅

`zipline.data.adjustments.SQLiteAdjustmentReader`

```py
calc_dividend_ratios(dividends)
```

计算应用于权益的比率，当回顾定价历史时，价格在除息日平滑，市场调整权益价值的变化。

返回：

与拆分和合并相同的框架格式，包含键 - sid，权益的 id - effective_date，以秒为单位的日期，在该日期应用比率。 - ratio，应用于向后查看定价数据的比率。

返回类型：

DataFrame

```py
write(splits=None, mergers=None, dividends=None, stock_dividends=None)
```

将数据写入 SQLite 文件，供 SQLiteAdjustmentReader 读取。

参数：

+   **拆分** ([*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)")*,* *可选*) –

    包含拆分数据的 DataFrame。该 DataFrame 的格式为：

    effective_dateint

    调整应应用的日期，表示为自 Unix 纪元以来的秒数。

    ratiofloat

    应用于有效日期之前所有数据的一个值。对于开盘、最高、最低和收盘，这些值乘以比率。成交量除以该值。

    sidint

    与此调整相关的资产 id。

+   **合并** ([*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)")*,* *可选*) –

    包含合并数据的 DataFrame。该 DataFrame 的格式为：

    effective_dateint

    调整应应用的日期，表示为自 Unix 纪元以来的秒数。

    ratiofloat

    应用于有效日期之前所有数据的一个值。对于开盘、最高、最低和收盘，这些值乘以比率。成交量不受影响。

    sidint

    与此调整相关的资产 id。

+   **股息** ([*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)")*,* *可选*) –

    包含股息数据的 DataFrame。DataFrame 的格式为：

    证券识别码 int

    与此调整关联的资产 ID。

    除权日期 datetime64

    为了有资格获得支付，必须持有某只股票的日期。

    宣布日期 datetime64

    向公众宣布股息的日期。

    支付日期 datetime64

    股息分配的日期。

    记录日期 datetime64

    检查股票所有权以确定股息分配的日期。

    amountfloat

    每股支付的现金金额。

    股息比率计算为：`1.0 - (股息价值 / "除权日前一天的收盘价")`

+   **股票股息** (*pandas.DataFrame*, *可选*) –

    包含股票股息数据的 DataFrame。DataFrame 的格式为：

    > 证券识别码 int
    > 
    > 与此调整关联的资产 ID。
    > 
    > 除权日期 datetime64
    > 
    > 为了有资格获得支付，必须持有某只股票的日期。
    > 
    > 宣布日期 datetime64
    > 
    > 向公众宣布股息的日期。
    > 
    > 支付日期 datetime64
    > 
    > 股息分配的日期。
    > 
    > 记录日期 datetime64
    > 
    > 检查股票所有权以确定股息分配的日期。
    > 
    > 支付 sid int
    > 
    > 应该用现金而不是股份支付的股份的资产 ID。
    > 
    > 比率 float
    > 
    > 在持有的 sid 中，应该用支付 sid 的新股份支付的当前持有股份的比例。

另请参阅

`zipline.data.adjustments.SQLiteAdjustmentReader`

```py
write_dividend_data(dividends, stock_dividends=None)
```

同时写入股息支付和派生的价格调整比率。

```py
write_dividend_payouts(frame)
```

将股息支付数据写入 SQLite 表 dividend_payouts。

```py
class zipline.assets.AssetDBWriter(engine)
```

用于将数据写入资产数据库的类。

参数：

**引擎** (*Engine* 或 [*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – SQLAlchemy 引擎或 SQL 数据库的路径。

```py
init_db(txn=None)
```

连接到数据库并创建表。

参数：

**txn** (*sa.engine.Connection*, *可选*) – 要执行的事务块。如果未提供，将使用提供的引擎启动新事务。

返回：

**元数据** – 描述新资产数据库的元数据。

返回类型：

sa.MetaData

```py
write(equities=None, futures=None, exchanges=None, root_symbols=None, equity_supplementary_mappings=None, chunk_size=999)
```

将资产元数据写入 sqlite 数据库。

参数：

+   **股票** (*pd.DataFrame*, *可选*) –

    股票元数据。此 DataFrame 的列包括：

    > 符号 str
    > 
    > 该股票的符号。
    > 
    > 资产名称 str
    > 
    > 该资产的全名。
    > 
    > 开始日期 datetime
    > 
    > 该资产创建的日期。
    > 
    > 结束日期 datetime，可选
    > 
    > 我们拥有该资产交易数据的最后一个日期。
    > 
    > 首次交易日期 datetime，可选
    > 
    > 我们拥有该资产交易数据的第一个日期。
    > 
    > 自动关闭日期 datetime，可选
    > 
    > 关闭此资产中任何头寸的日期。
    > 
    > 交易所 str
    > 
    > 该资产交易的交易所。

    此 DataFrame 的索引应包含 sids。

+   **期货** (*pd.DataFrame*, *可选*) –

    期货合约元数据。该数据框的列包括：

    > 符号字符串
    > 
    > 该期货合约的代码。
    > 
    > 根符号字符串
    > 
    > 根符号，或去除到期日的符号。
    > 
    > 资产名称字符串
    > 
    > 该资产的全名。
    > 
    > 开始日期时间, 可选
    > 
    > 该资产创建的日期。
    > 
    > 结束日期时间, 可选
    > 
    > 我们拥有该资产交易数据的最后日期。
    > 
    > 首次交易时间, 可选
    > 
    > 我们首次拥有该资产交易数据的日期。
    > 
    > 交易所字符串
    > 
    > 该资产交易的交易所。
    > 
    > 通知日期时间
    > 
    > 合约持有人可能被迫接受合约资产实物交割的日期。
    > 
    > 到期日期时间
    > 
    > 合约到期的日期。
    > 
    > 自动关闭日期时间
    > 
    > 经纪商将自动关闭该合约中任何持仓的日期。
    > 
    > 最小变动价位浮点数
    > 
    > 合约的最小价格变动。
    > 
    > 乘数：浮点数
    > 
    > 该合约所代表的标的资产的数量。

+   **exchanges** (*pd.DataFrame**,* *optional*) –

    资产可以交易的交易所。该数据框的列包括：

    > 交易所字符串
    > 
    > 交易所的全名。
    > 
    > 规范名称字符串
    > 
    > 交易所的规范名称。
    > 
    > 国家代码字符串
    > 
    > 交易所的 ISO 3166 alpha-2 国家代码。

+   **root_symbols** (*pd.DataFrame**,* *optional*) –

    期货合约的根符号。该数据框的列包括：

    > 根符号字符串
    > 
    > 根符号名称。
    > 
    > 根符号的唯一 id。
    > 
    > 该根符号的唯一 id。
    > 
    > 行业字符串, 可选
    > 
    > 该根符号所属的行业。
    > 
    > 描述字符串, 可选
    > 
    > 该根符号的简短描述。
    > 
    > 交易所字符串
    > 
    > 该根符号交易的交易所。

+   **equity_supplementary_mappings** (*pd.DataFrame**,* *optional*) – 将任意类型的值映射到资产的额外映射。

+   **chunk_size** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *optional*) – 一次写入 SQLite 表的行数。这默认为 sqlite 中默认的绑定参数数量。如果您编译的 sqlite3 有更多或更少的绑定参数，您可能希望在此传递该值。

另请参阅

`zipline.assets.asset_finder`

```py
write_direct(equities=None, equity_symbol_mappings=None, equity_supplementary_mappings=None, futures=None, exchanges=None, root_symbols=None, chunk_size=999)
```

将资产元数据写入 sqlite 数据库，格式与资产数据库中存储的格式相同。

参数：

+   **equities** (*pd.DataFrame**,* *optional*) –

    股票元数据。该数据框的列包括：

    > 符号字符串
    > 
    > 该股票的代码。
    > 
    > 资产名称字符串
    > 
    > 该资产的全名。
    > 
    > 开始日期时间
    > 
    > 该资产创建的日期。
    > 
    > 结束日期时间, 可选
    > 
    > 我们拥有该资产交易数据的最后日期。
    > 
    > 首次交易时间, 可选
    > 
    > 我们首次拥有该资产交易数据的日期。
    > 
    > 自动关闭日期时间, 可选
    > 
    > 关闭该资产中任何持仓的日期。
    > 
    > 交易所字符串
    > 
    > 该资产交易的交易所。

    该数据框的索引应包含 sids。

+   **futures** (*pd.DataFrame**,* *optional*) –

    期货合约元数据。该数据框的列包括：

    > 符号字符串
    > 
    > 此期货合约的股票代码。
    > 
    > 根符号字符串
    > 
    > 根符号，或去除到期日的符号。
    > 
    > 资产名称字符串
    > 
    > 此资产的全名。
    > 
    > 开始日期时间，可选
    > 
    > 此资产创建的日期。
    > 
    > 结束日期时间，可选
    > 
    > 我们拥有此资产交易数据的最后日期。
    > 
    > 首次交易日期时间，可选
    > 
    > 我们拥有此资产交易数据的第一个日期。
    > 
    > 交易所字符串
    > 
    > 此资产交易的交易所。
    > 
    > 通知日期时间
    > 
    > 合约所有者可能被迫接受合约资产实物交割的日期。
    > 
    > 到期日期时间
    > 
    > 合约到期日期。
    > 
    > 自动关闭日期时间
    > 
    > 经纪人将自动关闭此合约中任何头寸的日期。
    > 
    > 刻度大小浮点数
    > 
    > 合约的最小价格变动。
    > 
    > 乘数：浮点数
    > 
    > 此合约所代表的基础资产的数量。

+   **交易所** (*pd.DataFrame**,* *可选*) –

    资产可以交易的交易所。此数据框的列是：

    > 交易所字符串
    > 
    > 交易所的全名。
    > 
    > 规范名称字符串
    > 
    > 交易所的规范名称。
    > 
    > 国家代码字符串
    > 
    > 交易所的 ISO 3166 alpha-2 国家代码。

+   **根符号** (*pd.DataFrame**,* *可选*) –

    期货合约的根符号。此数据框的列是：

    > 根符号字符串
    > 
    > 根符号名称。
    > 
    > 根符号 _id 整数
    > 
    > 此根符号的唯一 id。
    > 
    > 部门字符串，可选
    > 
    > 此根符号的部门。
    > 
    > 描述字符串，可选
    > 
    > 此根符号的简短描述。
    > 
    > 交易所字符串
    > 
    > 此根符号交易的交易所。

+   **权益补充映射** (*pd.DataFrame**,* *可选*) – 将任意类型的值映射到资产的额外映射。

+   **块大小** ([*整数*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 一次写入 SQLite 表的行数。这默认为 sqlite 中绑定参数的默认数量。如果您使用更多或更少的参数编译 sqlite3，您可能希望在此传递该值。

### 读者

```py
class zipline.data.bcolz_daily_bars.BcolzDailyBarReader(table, read_all_threshold=3000)
```

由 BcolzDailyOHLCVWriter 编写的原始定价数据的读者。

参数：

+   **表** (*bcolz.ctable*) – 包含定价数据的 ctable，其属性对应于下面的属性列表。

+   **读取所有阈值** ([*整数*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 当权益数量低于此阈值时，数据通过为每个资产读取 carray 的切片来读取。高于此阈值时，数据通过将所有资产的所有数据拉入内存，然后为每个交易日和资产对索引该数组来读取。用于在使用少量或大量权益时调整读取性能。

```py
The table with which this loader interacts contains the following
```

```py
attributes
```

```py
first_row
```

从资产 _id 到数据集中具有该 id 的第一行的索引的映射。

类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")

```py
last_row
```

从资产 _id 到数据集中具有该 id 的最后一行的索引的映射。

类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")

```py
calendar_offset
```

从资产 _id 映射到日历索引的第一行。

类型：

[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")

```py
start_session_ns
```

此数据集中使用的第一个会话的纪元纳秒。

类型：

[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")

```py
end_session_ns
```

此数据集中使用的最后一个会话的纪元纳秒。

类型：

[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")

```py
calendar_name
```

使用的交易日历的字符串标识符（例如，“NYSE”）。

类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
We use first_row and last_row together to quickly find ranges of rows to
```

```py
load when reading an asset's data into memory.
```

```py
We use calendar_offset and calendar to orient loaded blocks within a
```

```py
range of queried dates.
```

注意

Bcolz CTable 由列和属性组成。此加载器与之交互的表包含以下列：

[‘open’, ‘high’, ‘low’, ‘close’, ‘volume’, ‘day’, ‘id’]。

这些列中的数据被解释如下：

+   价格列（‘open’, ‘high’, ‘low’, ‘close’）被解释为 1000 * 交易美元价值。

+   成交量被解释为交易量。

+   日被解释为自 1970 年 1 月 1 日 UTC 午夜以来的秒数。

+   Id 是行的资产 id。

每列数据首先按资产分组，然后在其每个资产块内按日期排序。

该表旨在表示长时间范围的数据，例如十年的股票数据，因此每个资产块的长度不相等。这些块被剪辑到每个资产的已知开始和结束日期，以减少需要包含的空值数量，以使数据集规则/立方。

当在同一索引上跨开、高、低、闭和成交量读取时，应表示相同的资产和日期。

另请参阅

`zipline.data.bcolz_daily_bars.BcolzDailyBarWriter`

```py
currency_codes(sids)
```

获取请求的 sids 报价的货币。

假设 sid 的价格始终以单一货币报价。

参数：

**sids** (*np.array**[**int64**]*) – 需要货币的 sids 数组。

返回：

**currency_codes** – `sids`的上市货币的货币代码数组。实现应为货币未知的 sids 返回 None。

返回类型：

np.array[[object](https://docs.python.org/3/library/functions.html#object "(in Python v3.11)")]

```py
get_last_traded_dt(asset, day)
```

获取`dt`或之前`asset`交易的最新分钟。

如果在`dt`或之前没有交易，返回`pd.NaT`。

参数：

+   **asset** (*zipline.asset.Asset*) – 获取最后一个交易分钟的资产。

+   **dt** (*pd.Timestamp*) – 开始搜索最后一个交易分钟的时间。

返回：

**last_traded** – 使用输入 dt 作为视点的给定资产的最后一个交易 dt。

返回类型：

pd.Timestamp

```py
get_value(sid, dt, field)
```

参数：

+   **sid** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 资产标识符。

+   **day** (*datetime64-like*) – 请求数据的日期的午夜。

+   **colname** (*string*) – 价格字段。例如：（‘open’, ‘high’, ‘low’, ‘close’, ‘volume’）

返回：

给定 sid 在给定日期的列名的现货价格。如果给定的日期和 sid 在股票的日期范围之前或之后，则引发 NoDataOnDate 异常。如果日期在日期范围内，但价格为 0，则返回-1。

返回类型：

[float](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")

```py
property last_available_dt
```

返回：**dt** – 读者可以提供数据的最后一个会话。 :rtype: pd.Timestamp

```py
load_raw_arrays(columns, start_date, end_date, assets)
```

参数：

+   **列** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)") *of* [*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – ‘开盘’, ‘最高’, ‘最低’, ‘收盘’, 或 ‘成交量’

+   **开始日期** (*Timestamp*) – 窗口范围的开始。

+   **结束日期** (*Timestamp*) – 窗口范围的结束。

+   **资产** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)") *of* [*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 窗口中的资产标识符。

返回：

包含每个字段的 ndarrays 列表，形状为(范围内的分钟数, sids)，dtype 为 float64，包含开始和结束 dt 范围内的相应字段的值。

返回类型：

[list](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)") of np.ndarray

```py
sid_day_index(sid, day)
```

参数：

+   **sid** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 资产标识符。

+   **日期** (*datetime64-like*) – 请求数据的日期的午夜。

返回：

给定 sid 和日期的数据磁带索引。如果给定的日期和 sid 在股票的日期范围之前或之后，则引发 NoDataOnDate 异常。

返回类型：

[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")

```py
class zipline.data.adjustments.SQLiteAdjustmentReader(conn)
```

根据公司行为从 SQLite 数据库加载调整。

期望以 SQLiteAdjustmentWriter 输出的格式编写的数据。

参数：

**连接** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* [*sqlite3.Connection*](https://docs.python.org/3/library/sqlite3.html#sqlite3.Connection "(in Python v3.11)")) – 用于加载数据的连接。

另请参阅

`zipline.data.adjustments.SQLiteAdjustmentWriter`

```py
load_adjustments(dates, assets, should_include_splits, should_include_mergers, should_include_dividends, adjustment_type)
```

从底层调整数据库加载调整对象集合。

参数：

+   **日期** (*pd.DatetimeIndex*) – 需要调整的日期。

+   **资产** (*pd.Int64Index*) – 需要调整的资产。

+   **应包含拆分** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 是否应包含拆分调整。

+   **应包含合并** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 是否应包含合并调整。

+   **should_include_dividends** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 是否应包括股息调整。

+   **adjustment_type** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 是否应在输出中包括价格调整、数量调整或两者。

返回：

**adjustments** – 一个字典，包含从索引到调整对象的价格和/或数量调整映射，以在索引处应用。

返回类型：

[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[str -> dict[int -> Adjustment]]

```py
unpack_db_to_component_dfs(convert_dates=False)
```

返回调整文件中已知表的集合，以 DataFrame 形式。

参数：

**convert_dates** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 默认情况下，日期以自纪元以来的秒数返回。如果 convert_dates 为 True，则日期列中的所有整数都将转换为日期时间。

返回：

**dfs** – 一个字典，将表名映射到相应的 DataFrame 版本的表，其中所有日期列都已从整数强制转换回日期时间。

返回类型：

dict{str->DataFrame}

```py
class zipline.assets.AssetFinder(engine, future_chain_predicates={'AD': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'BP': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'CD': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'EL': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'GC': functools.partial(<built-in function delivery_predicate>, {'M', 'Z', 'Q', 'V', 'G', 'J'}), 'JY': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'ME': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'PA': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'PL': functools.partial(<built-in function delivery_predicate>, {'J', 'F', 'V', 'N'}), 'SV': functools.partial(<built-in function delivery_predicate>, {'H', 'N', 'Z', 'U', 'K'}), 'XG': functools.partial(<built-in function delivery_predicate>, {'M', 'Z', 'Q', 'V', 'G', 'J'}), 'YS': functools.partial(<built-in function delivery_predicate>, {'H', 'N', 'Z', 'U', 'K'})})
```

AssetFinder 是一个接口，用于访问由`AssetDBWriter`编写的资产元数据数据库。

此类提供了通过唯一整数 ID 或通过符号查找资产的方法。出于历史原因，我们将这些唯一 ID 称为“sids”。

参数：

+   **引擎** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* *SQLAlchemy.引擎*) – 一个连接到资产数据库的引擎，或者可以被 SQLAlchemy 解析为 URI 的字符串。

+   **future_chain_predicates** ([*dict*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")) – 一个字典，将期货根符号映射到接受参数的谓词函数

+   **是** (*作为参数的合约，并返回合约是否应该*) –

+   **链。** (*包含在*) –

另请参阅

`zipline.assets.AssetDBWriter`

```py
property equities_sids
```

资产查找器中股票的所有 sids。

```py
equities_sids_for_country_code(country_code)
```

返回给定国家的所有 sids。

参数：

**country_code** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – ISO 3166 alpha-2 国家代码。

返回：

其交易所位于该国家的 sids。

返回类型：

[tuple](https://docs.python.org/3/library/stdtypes.html#tuple "(in Python v3.11)")[[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")]

```py
equities_sids_for_exchange_name(exchange_name)
```

返回给定 exchange_name 的所有 sids。

参数：

**exchange_name** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) –

返回：

其交易所位于该国家的 sids。

返回类型：

[tuple](https://docs.python.org/3/library/stdtypes.html#tuple "(in Python v3.11)")[[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")]

```py
property futures_sids
```

资产查找器中所有期货合约的 sid。

```py
get_supplementary_field(sid, field_name, as_of_date)
```

获取资产的补充字段的值。

参数：

+   **sid** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 要查询的资产的 sid。

+   **field_name** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 补充字段的名称。

+   **as_of_date** (*pd.Timestamp*, *None*) – 返回该日期上的最后一个已知值。如果为 None，则仅当我们只为该 sid 记录了一个值时才返回值。如果为 None 且我们记录了多个值，则会引发 MultipleValuesFoundForSid。

引发：

+   **NoValueForSid** – 如果我们没有该资产的值，或者在 as_of_date 上没有已知的值。

+   **MultipleValuesFoundForSid** – 如果我们有多个值，并且为 as_of_date 传递了 None。

```py
group_by_type(sids)
```

按资产类型对 sid 列表进行分组。

参数：

**sids** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*[*[*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*]*) –

返回：

**types** – 一个字典，将唯一的资产类型映射到从 sids 中提取的 sid 列表。如果我们无法查找资产，则为其分配一个 None 键。

返回类型：

[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") or None -> list[[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")]]

```py
lifetimes(dates, include_start_date, country_codes)
```

计算指定日期范围内资产生命周期的 DataFrame。

参数：

+   **dates** (*pd.DatetimeIndex*) – 要计算生命周期的日期。

+   **include_start_date** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) –

    是否将资产在其开始日期视为存活。

    这在回测上下文中很有用，其中生命周期用于表示“我是否在当天早上有该资产的数据？”对于许多金融指标（例如每日收盘价），直到资产的第一天结束时，数据才可用。

+   **country_codes** (*iterable*[*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*) – 要获取其生命周期的国家代码。

返回：

**lifetimes** – 一个 dtype 为 bool 的框架，日期作为索引，资产的 Int64Index 作为列。在 lifetimes.loc[date, asset]处的值将为 True，当且仅当资产在 date 存在。如果 include_start_date 为 False，则当 date == asset.start_date 时，lifetimes.loc[date, asset]将为 false。

返回类型：

pd.DataFrame

另请参阅

[`numpy.putmask`](https://numpy.org/doc/stable/reference/generated/numpy.putmask.html#numpy.putmask "(in NumPy v1.25)"), `zipline.pipeline.engine.SimplePipelineEngine._compute_root_mask`

```py
lookup_asset_types(sids)
```

检索 sid 列表的资产类型。

参数：

**sids** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*[*[*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*]*) –

返回：

**类型** – 提供的 sids 的资产类型。

返回类型：

[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[sid -> str 或 None]

```py
lookup_future_symbol(symbol)
```

按符号查找未来合约。

参数：

**符号** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 所需合约的符号。

返回：

**未来** – 由`符号`引用的未来合约。

返回类型：

Future

引发：

**SymbolNotFound** – 当找不到名为‘symbol’的合约时引发。

```py
lookup_generic(obj, as_of_date, country_code)
```

将对象转换为资产或资产序列。

此方法主要作为实现用户界面 API 的便利，该 API 可以处理多种输入类型。在已经知道输入预期类型的情况下，不应在内部代码中使用。

参数：

+   **obj** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* [*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *Asset**,* *ContinuousFuture**,或* *iterable*) – 要转换为一个或多个资产的对象。整数被解释为 sids。字符串被解释为股票代码。资产和连续期货保持不变。

+   **截至日期** (*pd.Timestamp* *或* *None*) – 用于消除歧义的 Timestamp。与 lookup_symbol 中的语义相同。

+   **国家代码** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* *None*) – 用于消除歧义的 ISO-3166 国家代码。与 lookup_symbol 中的语义相同。

返回：

**匹配项，缺失** –

`匹配项`是转换的结果。`缺失`是一个列表

包含无法解析的任何值。如果`obj`不是可迭代的，`缺失`将是一个空列表。

返回类型：

[tuple](https://docs.python.org/3/library/stdtypes.html#tuple "(in Python v3.11)")

```py
lookup_symbol(symbol, as_of_date, fuzzy=False, country_code=None)
```

按符号查找股票。

参数：

+   **符号** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要解析的股票代码。

+   **截至日期** ([*datetime.datetime*](https://docs.python.org/3/library/datetime.html#datetime.datetime "(in Python v3.11)") *或* *None*) – 查找此符号的最后所有者。如果`as_of_date`为 None，则只能解析确切拥有该股票代码的股票。

+   **模糊** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 是否使用模糊符号匹配？模糊符号匹配尝试解决股份类别表示的差异。例如，有些人可能将`BRK`的`A`股类别表示为`BRK.A`，而其他人可能写成`BRK_A`。

+   **country_code** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中)") *或* *无**，* *可选*) – 限制搜索的国家。如果未提供，搜索将跨越所有国家，这增加了含糊查找的可能性。

返回：

**equity** – 在给定的`as_of_date`上持有`symbol`的股票，或者如果`as_of_date`为无，则持有`symbol`的唯一股票。

返回类型：

股票

引发：

+   **SymbolNotFound** – 当没有股票曾经持有给定的符号时引发。

+   **MultipleSymbolsFound** – 当未提供`as_of_date`且有多个股票持有`symbol`时引发。当`fuzzy=True`且在`as_of_date`上有多个候选`symbol`时也会引发。当未提供`country_code`且符号在多个国家之间含糊不清时也会引发。

```py
lookup_symbols(symbols, as_of_date, fuzzy=False, country_code=None)
```

按符号查找股票列表。

等效于：

```py
[finder.lookup_symbol(s, as_of, fuzzy) for s in symbols] 
```

但可能更快，因为重复的查找被缓存。

参数：

+   **symbols** (*序列**[*[*str*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中)")*]*) – 要解析的股票代码序列。

+   **as_of_date** (*pd.Timestamp*) – 转发到`lookup_symbol`。

+   **fuzzy** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(在 Python v3.11)")*,* *可选*) – 转发到`lookup_symbol`。

+   **country_code** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中)") *或* *无**，* *可选*) – 限制搜索的国家。如果未提供，搜索将跨越所有国家，这增加了含糊查找的可能性。

返回：

**equities**

返回类型：

[列表](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11 中)")[股票]

```py
retrieve_all(sids, default_none=False)
```

检索 sids 中的所有资产。

参数：

+   **sids** (*可迭代* *的* [*int*](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11)")) – 要检索的资产。

+   **default_none** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(在 Python v3.11)")) – 如果为 True，对于失败的查找返回无。如果为 False，引发 SidsNotFound。

返回：

**assets** – 与 sids 长度相同的列表，包含与请求的 sids 对应的资产（或无）。

返回类型：

[列表](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11 中)")[资产或无]

引发：

**SidsNotFound** – 当请求的 sid 未找到且 default_none=False 时。

```py
retrieve_asset(sid, default_none=False)
```

检索给定 sid 的资产。

```py
retrieve_equities(sids)
```

为 sid 列表检索股票对象。

用户通常不需要使用此方法（相反，他们应该更喜欢更通用/友好的 retrieve_assets），但它有文档化的接口和测试，因为它在上游使用。

参数：

**sids** (*可迭代**[*[*int*](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11)")*]*) –

返回：

**equities**

返回类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[整数 -> 股票]

引发：

**未找到股票** – 当请求的任何资产未找到时。

```py
retrieve_futures_contracts(sids)
```

检索可迭代证券识别码的 Future 对象。

用户通常不需要使用此方法（相反，他们应该更喜欢更通用/友好的检索资产方法），但由于它在上游使用，因此具有文档化的接口和测试。

参数：

**证券识别码** (*可迭代**[*[整数*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*]*) –

返回：

**股票**

返回类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[整数 -> 股票]

引发：

**未找到股票** – 当请求的任何资产未找到时。

```py
property sids
```

资产查找器中的所有证券识别码。

```py
class zipline.data.data_portal.DataPortal(asset_finder, trading_calendar, first_trading_day, equity_daily_reader=None, equity_minute_reader=None, future_daily_reader=None, future_minute_reader=None, adjustment_reader=None, last_available_session=None, last_available_minute=None, minute_history_prefetch_length=1560, daily_history_prefetch_length=40)
```

访问 zipline 模拟所需的所有数据的接口。

这由模拟运行器用于回答有关数据的问题，例如获取某一天的资产价格或服务历史调用。

参数：

+   **资产查找器** (*zipline.assets.assets.AssetFinder*) – 用于解析资产的 AssetFinder 实例。

+   **交易日历** (*zipline.utils.calendar.exchange_calendar.TradingCalendar*) – 提供分钟到交易日信息的日历实例。

+   **首个交易日** (*pd.Timestamp*) – 模拟的首个交易日。

+   **股票日数据读取器** (*BcolzDailyBarReader**,* *可选*) – 用于股票的日数据读取器。这将用于服务日数据回测或分钟回测中的日历史调用。如果没有提供日数据读取器，但提供了分钟数据读取器，则分钟数据将被汇总以服务日请求。

+   **股票分钟数据读取器** (*BcolzMinuteBarReader**,* *可选*) – 用于股票的分钟数据读取器。这将用于服务分钟数据回测或分钟历史调用。如果没有提供日数据读取器，也可以用于服务日调用。

+   **期货日数据读取器** (*BcolzDailyBarReader**,* *可选*) – 用于期货的日数据读取器。这将用于服务日数据回测或分钟回测中的日历史调用。如果没有提供日数据读取器，但提供了分钟数据读取器，则分钟数据将被汇总以服务日请求。

+   **期货分钟数据读取器** (*BcolzFutureMinuteBarReader**,* *可选*) – 用于期货的分钟数据读取器。这将用于服务分钟数据回测或分钟历史调用。如果没有提供日数据读取器，也可以用于服务日调用。

+   **调整阅读器** (*SQLite 调整写入器**,* *可选*) – 调整阅读器。这用于将拆分、股息和其他调整数据应用于读取器的原始数据。

+   **最后可用会话** (*pd.Timestamp**,* *可选*) – 会话级别数据中可用的最后一个会话。

+   **最后可用分钟** (*pd.Timestamp**,* *可选*) – 分钟级别数据中可用的最后一分钟。

```py
get_adjusted_value(asset, field, dt, perspective_dt, data_frequency, spot_value=None)
```

返回表示在给定 dt 时所需资产字段的值的标量值，已应用调整。

参数：

+   **资产** (*资产*) – 所需数据的资产。

+   **字段** (*{'开盘'**,* *'高价'**,* *'低价'**,* *'收盘'**,* *'成交量'**,* *'价格'**,* *'最后交易'}*) – 资产的所需字段。

+   **dt** (*pd.Timestamp*) – 所需值的时间戳。

+   **perspective_dt** (*pd.Timestamp*) – 从哪个时间戳回看数据。

+   **数据频率** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要查询的数据频率；即数据是“每日”还是“分钟”条形图

返回：

**值** – 在`dt`时给定`字段`的`资产`的值，已知由`perspective_dt`应用的任何调整。返回类型基于所请求的`字段`。如果字段是“开盘”、“高价”、“低价”、“收盘”或“价格”之一，则值将为浮点数。如果`字段`是“成交量”，则值将为整数。如果`字段`是“最后交易”，则值将为时间戳。

返回类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)"), [整数](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)"), 或 pd.Timestamp

```py
get_adjustments(assets, field, dt, perspective_dt)
```

返回在 dt 和 perspective_dt 之间给定字段和资产列表的调整列表

参数：

+   **资产** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)") *的* *类型资产**，或* *资产*) – 所需调整的资产或资产。

+   **字段** (*{'开盘'**,* *'高价'**,* *'低价'**,* *'收盘'**,* *'成交量'**,* *'价格'**,* *'最后交易'}*) – 资产的所需字段。

+   **dt** (*pd.Timestamp*) – 所需值的时间戳。

+   **perspective_dt** (*pd.Timestamp*) – 从哪个时间戳回看数据。

返回：

**调整** – 该字段的调整。

返回类型：

[列表](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[调整]

```py
get_current_future_chain(continuous_future, dt)
```

根据连续期货规范，检索在给定 dt 时合约的未来链。

返回：

**未来链** – 活跃期货的列表，其中第一个索引是连续期货定义指定的当前合约，第二个是下一个即将到来的合约，依此类推。

返回类型：

[列表](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11 中")[期货]

```py
get_fetcher_assets(dt)
```

返回当前日期根据提取器数据定义的资产列表。

返回：

**列表**

返回类型：

资产对象的列表。

```py
get_history_window(assets, end_dt, bar_count, frequency, field, data_frequency, ffill=True)
```

公共 API 方法，返回包含所请求历史窗口的数据框。数据完全调整。

参数：

+   **资产** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11 中") *的* *zipline.data.Asset 对象*) – 所需数据的资产。

+   **条形图计数** ([*整数*](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11 中")) – 所需的条形图数量。

+   **频率** (*字符串*) – “1d”或“1m”

+   **字段** (*字符串*) – 资产的所需字段。

+   **数据频率** (*字符串*) – 要查询的数据的频率；即数据是‘每日’还是‘分钟’条形图。

+   **ffill** (*布尔*) – 前向填充缺失值。仅在字段为‘价格’时有效。

返回类型：

包含所请求数据的数据框。

```py
get_last_traded_dt(asset, dt, data_frequency)
```

给定资产和 dt，返回从给定 dt 视角的最后交易 dt。

如果在 dt 上有交易，答案是提供的 dt。

```py
get_scalar_asset_spot_value(asset, field, dt, data_frequency)
```

公共 API 方法，返回代表所需资产字段在给定 dt 时的值的标量值。

参数：

+   **资产** (*资产*) – 所需数据的资产或资产。这不能是任意的 AssetConvertible。

+   **字段** (*{'开盘价'**,* *'最高价'**,* *'最低价'**,* *'收盘价'**,* *'成交量'**,*) – ‘价格’, ‘最后交易价’} 资产的所需字段。

+   **dt** (*pd.Timestamp*) – 所需值的时间戳。

+   **数据频率** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中")) – 要查询的数据的频率；即数据是‘每日’还是‘分钟’条形图

返回：

**值** – `字段`对于`资产`的现货价值。返回类型基于所请求的`字段`。如果字段是‘开盘价’, ‘最高价’, ‘最低价’, ‘收盘价’或‘价格’之一，值将是一个浮点数。如果`字段`是‘成交量’，值将是一个整数。如果`字段`是‘最后交易价’，值将是一个时间戳。

返回类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中"), [整数](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11 中"), 或 pd.Timestamp

```py
get_splits(assets, dt)
```

返回给定 sid 和给定 dt 的任何拆分。

参数：

+   **资产** (*容器*) – 我们想要拆分的资产。

+   **dt** (*pd.Timestamp*) – 我们检查拆分的日期。注意：这预计是 UTC 午夜。

返回：

**拆分** – 拆分列表，其中每个拆分是(资产, 比率)元组。

返回类型：

[列表](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11 中")[(资产, [浮点数](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中"))]

```py
get_spot_value(assets, field, dt, data_frequency)
```

公共 API 方法，返回表示所需资产字段在给定 dt 的标量值。

参数：

+   **assets** (*Asset**,* *ContinuousFuture**, 或* *iterable* *of* *same.*) – 所需数据的资产或资产。

+   **field** (*{'open'**,* *'high'**,* *'low'**,* *'close'**,* *'volume'**,*) – ‘price’, ‘last_traded’} 资产的所需字段。

+   **dt** (*pd.Timestamp*) – 所需值的时间戳。

+   **data_frequency** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要查询的数据频率；即数据是“每日”还是“分钟”条形图

返回值：

**value** – `asset`的`field`的现货价值。返回类型基于请求的`field`。如果字段是“open”，“high”，“low”，“close”或“price”之一，则值将为浮点数。如果`field`是“volume”，则值将为整数。如果`field`是“last_traded”，则值将为 Timestamp。

返回类型：

[float](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)"), [int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)"), 或 pd.Timestamp

```py
get_stock_dividends(sid, trading_days)
```

返回给定交易范围内特定 sid 的所有股票股息。

参数：

+   **sid** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 应返回其股票股息的资产。

+   **trading_days** (*pd.DatetimeIndex*) – 交易范围。

返回值：

+   **list** (*具有所有相关属性填充的对象列表。*)

+   *所有时间戳字段都转换为 pd.Timestamps。*

```py
handle_extra_source(source_df, sim_params)
```

额外来源始终有一个 sid 列。

我们将给定数据（通过前向填充）扩展到模拟日期的完整范围，以便在模拟期间快速查找。

```py
class zipline.sources.benchmark_source.BenchmarkSource(benchmark_asset, trading_calendar, sessions, data_portal, emission_rate='daily', benchmark_returns=None)
```

```py
daily_returns(start, end=None)
```

返回给定期间的每日回报。

参数：

+   **start** (*datetime*) – 包含开始会话标签。

+   **end** (*datetime**,* *可选*) – 包含结束会话标签。如果未提供，则将`start`视为标量键。

返回值：

**returns** – 给定期间的回报。索引将是交易日历范围[start, end]。如果只提供`start`，则返回该日的标量值。

返回类型：

pd.Series 或 [float](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")

```py
get_range(start_dt, end_dt)
```

查找给定期间的回报。

参数：

+   **start_dt** (*datetime*) – 包含开始标签。

+   **end_dt** (*datetime*) – 包含结束标签。

返回值：

**returns** – 回报序列。

返回类型：

pd.Series

另请参阅

`zipline.sources.benchmark_source.BenchmarkSource.daily_returns`

`此方法期望分钟输入如果`emission_rate == 'minute'` 并且在 `emission_rate == 'daily'` 时会话标签。`

```py`
get_value(dt)
```

查找给定 dt 的回报。

参数：

**dt** (*datetime*) – 要查找的标签。

返回：

**返回** – 给定 dt 或会话的回报。

返回类型：

[float](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")

另请参阅

`zipline.sources.benchmark_source.BenchmarkSource.daily_returns`

`如果`emission_rate == 'minute'`，此方法期望输入分钟数据，当`emission_rate == 'daily'`时，期望输入会话标签。`

``### 捆绑包

```py
zipline.data.bundles.register(name='__no__default__', f='__no__default__', calendar_name='NYSE', start_session=None, end_session=None, minutes_per_day=390, create_writers=True)
```

注册数据捆绑包摄取函数。

参数：

+   **名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 捆绑包的名称。

+   **f** (*可调用*) –

    将传递给此函数的摄取函数。

    > environmapping
    > 
    > 正在运行的环境。
    > 
    > asset_db_writerAssetDBWriter
    > 
    > 要写入的资产数据库写入器。
    > 
    > minute_bar_writerBcolzMinuteBarWriter
    > 
    > 要写入的分钟条形写入器。
    > 
    > daily_bar_writerBcolzDailyBarWriter
    > 
    > 要写入的日条形写入器。
    > 
    > adjustment_writerSQLiteAdjustmentWriter
    > 
    > 要写入的调整数据库写入器。
    > 
    > calendartrading_calendars.TradingCalendar
    > 
    > 要摄取的交易日历。
    > 
    > start_sessionpd.Timestamp
    > 
    > 要摄取的数据的第一个会话。
    > 
    > end_sessionpd.Timestamp
    > 
    > 要摄取的数据的最后一个会话。
    > 
    > cacheDataFrameCache
    > 
    > 一个映射对象，用于临时存储数据框。在加载失败的情况下，应使用它来缓存中间数据。在成功加载后，将自动清理。
    > 
    > show_progressbool
    > 
    > 在可能的情况下显示当前加载的进度。

+   **日历名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 用于对齐捆绑数据的日历名称。默认值为‘NYSE’。

+   **开始会话** (*pd.Timestamp**,* *可选*) – 我们想要数据的第一个会话。如果未提供，或者日期超出了日历支持的范围，则使用日历的第一个会话。

+   **结束会话** (*pd.Timestamp**,* *可选*) – 我们想要数据的最后一个会话。如果未提供，或者日期超出了日历支持的范围，则使用日历的最后一个会话。

+   **每天分钟数** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 每个正常交易日的分钟数。

+   **create_writers** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 摄取机制是否应为摄取函数创建写入器。在不需要它们的情况下，例如`quantopian-quandl`捆绑包，可以禁用此功能作为优化。

注意

此函数可以用作装饰器，例如：

```py
@register('quandl')
def quandl_ingest_function(...):
    ... 
```

另请参阅

`zipline.data.bundles.bundles`

```py
zipline.data.bundles.ingest(name, environ=os.environ, date=None, show_progress=True)
```

为给定捆绑包摄取数据。

参数：

+   **名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 捆绑包的名称。

+   **环境变量** (*映射*,*可选*) – 环境变量。默认情况下为 os.environ。

+   **时间戳** (*datetime*,*可选*) – 用于加载的时间戳。默认情况下为当前时间。

+   **资产版本** (*可迭代**[*[*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*]**,*可选*) – 要降级的资产数据库的版本。

+   **显示进度** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,*可选*) – 告诉摄取函数在可能的情况下显示进度。

```py
zipline.data.bundles.load(name, environ=os.environ, date=None)
```

Loads a previously ingested bundle.

Parameters:

+   **名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – bundle 的名称。

+   **环境变量** (*映射*,*可选*) – 环境变量。默认为 os.environ。

+   **时间戳** (*datetime*,*可选*) – 要查找的数据的时间戳。默认为当前时间。

Returns:

**bundle_data** – 此 bundle 的原始数据读取器。

Return type:

BundleData

```py
zipline.data.bundles.unregister(name)
```

Unregister a bundle.

Parameters:

**名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要注销的 bundle 的名称。

Raises:

**UnknownBundle** – 当没有使用给定名称注册 bundle 时引发。

See also

`zipline.data.bundles.bundles`

```py
zipline.data.bundles.bundles
```

The bundles that have been registered as a mapping from bundle name to bundle data. This mapping is immutable and may only be updated through `register()` or `unregister()`.

### Writers

```py
class zipline.data.bcolz_daily_bars.BcolzDailyBarWriter(filename, calendar, start_session, end_session)
```

Class capable of writing daily OHLCV data to disk in a format that can be read efficiently by BcolzDailyOHLCVReader.

Parameters:

+   **文件名** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 我们应该写入输出的位置。

+   **日历** (*zipline.utils.calendar.trading_calendar*) – 用于计算资产日历偏移的日历。

+   **开始会话** (*pd.Timestamp*) – 午夜 UTC 会话标签。

+   **结束会话** (*pd.Timestamp*) – 午夜 UTC 会话标签。

See also

`zipline.data.bcolz_daily_bars.BcolzDailyBarReader`

```py
write(data, assets=None, show_progress=False, invalid_data_behavior='warn')
```

Parameters:

+   **数据** (*可迭代**[*[*元组*](https://docs.python.org/3/library/stdtypes.html#tuple "(in Python v3.11)")*[*[*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* [*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)") *或* *bcolz.ctable**]**]*) – 要写入的数据块。每个块应为 sid 和该资产的数据的元组。

+   **资产** ([*集合*](https://docs.python.org/3/library/stdtypes.html#set "(in Python v3.11)")*[*[*整数*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*]**,* *可选*) – 应包含在`data`中的资产。如果提供了这个参数，我们将检查`data`与资产的对应关系，并提供更好的进度信息。

+   **显示进度** ([*布尔*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 在写入时是否显示进度条。

+   **无效数据行为** (*{'警告'**,* *'引发'**,* *'忽略'}**,* *可选*) – 当遇到超出 uint32 范围的数据时应该采取的措施。

返回：

**表** – 新写入的表。

返回类型：

bcolz.ctable

```py
write_csvs(asset_map, show_progress=False, invalid_data_behavior='warn')
```

从我们的资产映射中将 CSV 读取为数据框。

参数：

+   **资产映射** ([*字典*](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")*[**int -> str**]*) – 资产 id 到包含该资产 CSV 数据的文件路径的映射

+   **显示进度** ([*布尔*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 在写入时是否显示进度条。

+   **无效数据行为** (*{'警告'**,* *'引发'**,* *'忽略'}*) – 当遇到超出 uint32 范围的数据时应该采取的措施。

```py
class zipline.data.adjustments.SQLiteAdjustmentWriter(conn_or_path, equity_daily_bar_reader, overwrite=False)
```

供 SQLiteAdjustmentReader 读取的数据写入器

参数：

+   **conn_or_path** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* [*sqlite3.Connection*](https://docs.python.org/3/library/sqlite3.html#sqlite3.Connection "(in Python v3.11)")) – 目标 sqlite 数据库的句柄。

+   **equity_daily_bar_reader** (*SessionBarReader*) – 用于股息写入的日条形图读取器。

+   **覆盖** ([*布尔*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选**,* *默认=False*) – 如果为 True 且 conn_or_path 是字符串，则在连接前删除给定路径上的任何现有文件。

另请参阅

`zipline.data.adjustments.SQLiteAdjustmentReader`

```py
calc_dividend_ratios(dividends)
```

计算在查看定价历史时应用于权益的比率，以便在除息日价格平滑，市场调整因即将到来的股息而导致的权益价值变化。

返回：

与拆分和合并相同的格式的帧，键包括 - sid，权益的 id - 生效日期，应用比率的秒级日期 - 比率，应用于向后查看定价数据的比率。

返回类型：

数据框

```py
write(splits=None, mergers=None, dividends=None, stock_dividends=None)
```

将数据写入 SQLite 文件，供 SQLiteAdjustmentReader 读取。

参数：

+   **拆分** ([*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)")*,* *可选*) –

    包含拆分数据的数据框。该数据框的格式为：

    生效日期整数

    调整应应用的日期，以 Unix 纪元以来的秒数表示。

    比率浮点数

    对于有效日期之前的所有数据应用的值。对于开盘、最高、最低和收盘价，这些值乘以比率。成交量除以该值。

    sidint

    与此调整相关的资产 ID。

+   **合并** ([*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)")*,* *可选*) –

    包含合并数据的 DataFrame。该数据框的格式为：

    effective_dateint

    调整应应用的日期，表示为自 Unix 纪元以来的秒数。

    ratiofloat

    对于有效日期之前的所有数据应用的值。对于开盘、最高、最低和收盘价，这些值乘以比率。成交量不受影响。

    sidint

    与此调整相关的资产 ID。

+   **股息** ([*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)")*,* *可选*) –

    包含股息数据的 DataFrame。该数据框的格式为：

    sidint

    与此调整相关的资产 ID。

    ex_datedatetime64

    必须持有股票以有资格获得支付的日期。

    declared_datedatetime64

    向公众宣布股息的日期。

    pay_datedatetime64

    股息分配的日期。

    record_datedatetime64

    检查股票所有权以确定股息分配的日期。

    amountfloat

    每股支付的现金金额。

    股息比率计算为：`1.0 - (dividend_value / "ex_date 前一天的收盘价")`

+   **股票股息** ([*pandas.DataFrame*](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame "(in pandas v2.0.3)")*,* *可选*) –

    包含股票股息数据的 DataFrame。该数据框的格式为：

    > sidint
    > 
    > 与此调整相关的资产 ID。
    > 
    > ex_datedatetime64
    > 
    > 必须持有股票以有资格获得支付的日期。
    > 
    > declared_datedatetime64
    > 
    > 向公众宣布股息的日期。
    > 
    > pay_datedatetime64
    > 
    > 股息分配的日期。
    > 
    > record_datedatetime64
    > 
    > 检查股票所有权的日期，以确定股息的分配。
    > 
    > payment_sidint
    > 
    > 应支付的股票资产 ID，而不是现金。
    > 
    > ratiofloat
    > 
    > 当前持有的 sid 中应支付新 payment_sid 股票的持股比率。

另请参阅

`zipline.data.adjustments.SQLiteAdjustmentReader`

```py
write_dividend_data(dividends, stock_dividends=None)
```

写入股息支付和派生的价格调整比率。

```py
write_dividend_payouts(frame)
```

将股息支付数据写入 SQLite 表 dividend_payouts。

```py
class zipline.assets.AssetDBWriter(engine)
```

用于将数据写入资产数据库的类。

参数：

**引擎** (*Engine* *或* [*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – SQLAlchemy 引擎或 SQL 数据库的路径。

```py
init_db(txn=None)
```

连接到数据库并创建表。

参数：

**txn**（*sa.engine.Connection*，*可选*）- 要执行的事务块。如果未提供，将使用提供的引擎启动新事务。

返回：

**元数据** - 描述新资产数据库的元数据。

返回类型：

sa.元数据

```py
write(equities=None, futures=None, exchanges=None, root_symbols=None, equity_supplementary_mappings=None, chunk_size=999)
```

将资产元数据写入 sqlite 数据库。

参数：

+   **股票**（*pd.DataFrame*，*可选*）-

    股票元数据。该数据框的列包括：

    > 符号字符串
    > 
    > 该股票的股票代码。
    > 
    > 资产名称字符串
    > 
    > 该资产的全名。
    > 
    > 开始日期时间
    > 
    > 该资产创建的日期。
    > 
    > 结束日期时间，可选
    > 
    > 我们拥有该资产交易数据的最后一个日期。
    > 
    > 首次交易日期时间，可选
    > 
    > 我们拥有该资产交易数据的第一个日期。
    > 
    > 自动关闭日期时间，可选
    > 
    > 关闭该资产中任何持仓的日期。
    > 
    > 交易所字符串
    > 
    > 该资产交易的交易所。

    该数据框的索引应包含 sids。

+   **期货**（*pd.DataFrame*，*可选*）-

    期货合约元数据。该数据框的列包括：

    > 符号字符串
    > 
    > 该期货合约的股票代码。
    > 
    > 根符号字符串
    > 
    > 根符号，或去除到期日的符号。
    > 
    > 资产名称字符串
    > 
    > 该资产的全名。
    > 
    > 开始日期时间，可选
    > 
    > 该资产创建的日期。
    > 
    > 结束日期时间，可选
    > 
    > 我们拥有该资产交易数据的最后一个日期。
    > 
    > 首次交易日期时间，可选
    > 
    > 我们拥有该资产交易数据的第一个日期。
    > 
    > 交换字符串
    > 
    > 该资产交易的交易所。
    > 
    > 通知日期时间
    > 
    > 合约持有人可能被迫接受合约资产实物交割的日期。
    > 
    > 到期日期时间
    > 
    > 合约到期日期。
    > 
    > 自动关闭日期时间
    > 
    > 经纪商将自动关闭该合约中任何持仓的日期。
    > 
    > 刻度大小浮点数
    > 
    > 合约的最小价格变动。
    > 
    > 乘数：浮点数
    > 
    > 该合约所代表的标的资产的数量。

+   **交易所**（*pd.DataFrame*，*可选*）-

    资产可以进行交易的交易所。该数据框的列包括：

    > 交易所字符串
    > 
    > 交易所的全名。
    > 
    > 规范名称字符串
    > 
    > 交易所的规范名称。
    > 
    > 国家代码字符串
    > 
    > 交易所的 ISO 3166 alpha-2 国家代码。

+   **根符号**（*pd.DataFrame*，*可选*）-

    期货合约的根符号。该数据框的列包括：

    > 根符号字符串
    > 
    > 根符号名称。
    > 
    > 根符号 ID 整数
    > 
    > 该根符号的唯一 ID。
    > 
    > 部门字符串，可选
    > 
    > 该根符号所属的部门。
    > 
    > 描述字符串，可选
    > 
    > 该根符号的简短描述。
    > 
    > 交易所字符串
    > 
    > 该根符号交易的交易所。

+   **股票补充映射**（*pd.DataFrame*，*可选*）- 将任意类型的值映射到资产的额外映射。

+   **块大小** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 一次写入 SQLite 表的行数。这默认为 sqlite 中默认的绑定参数数量。如果您编译的 sqlite3 有更多或更少的绑定参数，您可能希望在此传递该值。

另请参阅

`zipline.assets.asset_finder`

```py
write_direct(equities=None, equity_symbol_mappings=None, equity_supplementary_mappings=None, futures=None, exchanges=None, root_symbols=None, chunk_size=999)
```

以资产数据库中存储的格式将资产元数据写入 sqlite 数据库。

参数：

+   **股票** (*pd.DataFrame**,* *可选*) –

    股票元数据。此数据框的列包括：

    > 代码字符串
    > 
    > 此股票的代码。
    > 
    > 资产名称字符串
    > 
    > 此资产的全名。
    > 
    > 开始日期时间
    > 
    > 此资产创建的日期。
    > 
    > 结束日期时间，可选
    > 
    > 我们拥有此资产交易数据的最后一天。
    > 
    > 首次交易日期时间，可选
    > 
    > 我们拥有此资产交易数据的第一天。
    > 
    > 自动关闭日期时间，可选
    > 
    > 在此资产中关闭任何头寸的日期。
    > 
    > 交易所字符串
    > 
    > 此资产交易的交易所。

    此数据框的索引应包含 sids。

+   **期货** (*pd.DataFrame**,* *可选*) –

    期货合约元数据。此数据框的列包括：

    > 代码字符串
    > 
    > 此期货合约的代码。
    > 
    > 根符号字符串
    > 
    > 根符号，或去除到期日的符号。
    > 
    > 资产名称字符串
    > 
    > 此资产的全名。
    > 
    > 开始日期时间，可选
    > 
    > 此资产创建的日期。
    > 
    > 结束日期时间，可选
    > 
    > 我们拥有此资产交易数据的最后一天。
    > 
    > 首次交易日期时间，可选
    > 
    > 我们拥有此资产交易数据的第一天。
    > 
    > 交易所字符串
    > 
    > 此资产交易的交易所。
    > 
    > 通知日期时间
    > 
    > 合约持有人可能被迫接受合约资产实物交割的日期。
    > 
    > 到期日期时间
    > 
    > 合约到期日。
    > 
    > 自动关闭日期时间
    > 
    > 经纪人将自动关闭此合约中任何头寸的日期。
    > 
    > 最小变动价位浮点数
    > 
    > 合约的最小价格变动。
    > 
    > 乘数：浮点数
    > 
    > 此合约代表的标的资产数量。

+   **交易所** (*pd.DataFrame**,* *可选*) –

    可以交易资产的交易所。此数据框的列包括：

    > 交易所字符串
    > 
    > 交易所的全名。
    > 
    > 规范名称字符串
    > 
    > 交易所的规范名称。
    > 
    > 国家代码字符串
    > 
    > 交易所的 ISO 3166 alpha-2 国家代码。

+   **根符号** (*pd.DataFrame**,* *可选*) –

    期货合约的根符号。此数据框的列包括：

    > 根符号字符串
    > 
    > 根符号名称。
    > 
    > 根符号 ID 整数
    > 
    > 此根符号的唯一 ID。
    > 
    > 部门字符串，可选
    > 
    > 这个根符号的部门。
    > 
    > 描述字符串，可选
    > 
    > 此根符号的简短描述。
    > 
    > 交易所字符串
    > 
    > 此根符号交易的交易所。

+   **股票补充映射** (*pd.DataFrame**,* *可选*) – 将任意类型的值映射到资产的额外映射。

+   **chunk_size** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *optional*) – 一次写入 SQLite 表的行数。这默认为 sqlite 中默认的绑定参数数量。如果您使用更多或更少的参数编译 sqlite3，您可能希望在此处传递该值。

### 读取器

```py
class zipline.data.bcolz_daily_bars.BcolzDailyBarReader(table, read_all_threshold=3000)
```

由 BcolzDailyOHLCVWriter 编写的原始定价数据的读取器。

参数：

+   **表** (*bcolz.ctable*) – 包含定价数据的 ctable，其属性对应于下面的属性列表。

+   **read_all_threshold** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 当股票数量低于此阈值时，数据通过从 carray 中读取每个资产的切片来读取。高于此阈值时，数据通过将所有资产的数据拉入内存，然后为每个日期和资产对索引到该数组中来读取。用于调整使用少量或大量股票时的读取性能。

```py
The table with which this loader interacts contains the following
```

```py
attributes
```

```py
first_row
```

从资产 _id 到数据集中具有该 id 的第一行的索引的映射。

类型：

[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")

```py
last_row
```

从资产 _id 到数据集中具有该 id 的最后一行的索引的映射。

类型：

[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")

```py
calendar_offset
```

从资产 _id 到第一行的日历索引的映射。

类型：

[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")

```py
start_session_ns
```

在此数据集中使用的第一个会话的纪元纳秒。

类型：

[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")

```py
end_session_ns
```

在此数据集中使用的最后一个会话的纪元纳秒。

类型：

[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")

```py
calendar_name
```

使用的交易日历的字符串标识符（例如，“NYSE”）。

类型：

[str](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")

```py
We use first_row and last_row together to quickly find ranges of rows to
```

```py
load when reading an asset's data into memory.
```

```py
We use calendar_offset and calendar to orient loaded blocks within a
```

```py
range of queried dates.
```

注释

Bcolz CTable 由列和属性组成。此加载器与之交互的表包含以下列：

[‘open’, ‘high’, ‘low’, ‘close’, ‘volume’, ‘day’, ‘id’]。

这些列中的数据解释如下：

+   价格列（‘open’, ‘high’, ‘low’, ‘close’）被解释为 1000 * 交易美元价值。

+   成交量被解释为交易成交量。

+   日期被解释为自 UTC 时间 1970 年 1 月 1 日午夜以来的秒数。

+   Id 是行资产的标识符。

每个列中的数据按资产分组，然后按每个资产块内的日期排序。

该表格旨在展示一个长时间范围的数据，例如十年的股票数据，因此每个资产块的长度并不相等。这些块被剪辑到每个资产的已知开始和结束日期，以减少需要包含的空值数量，从而使数据集保持规则/立方体形状。

当使用相同的索引跨开盘、最高、最低、收盘和成交量读取时，应表示相同的资产和日期。

另请参阅

`zipline.data.bcolz_daily_bars.BcolzDailyBarWriter`

```py
currency_codes(sids)
```

获取请求的 sid 报价的货币。

假设 sid 的价格始终以单一货币报价。

参数：

**sids** (*np.array**[**int64**]*) – 需要货币的 sid 数组。

返回：

**currency_codes** – 列出`sids`货币的货币代码数组。对于货币未知的 sid，实现应返回 None。

返回类型：

np.array[[object](https://docs.python.org/3/library/functions.html#object "(in Python v3.11)")]

```py
get_last_traded_dt(asset, day)
```

获取`asset`在`dt`之前或当天交易的最新分钟。

如果在`dt`之前没有交易，则返回`pd.NaT`。

参数：

+   **asset** (*zipline.asset.Asset*) – 获取最后一次交易的资产。

+   **dt** (*pd.Timestamp*) – 开始搜索最后一次交易分钟的时间。

返回：

**last_traded** – 使用输入 dt 作为视点，给定资产的最后一次交易的 dt。

返回类型：

pd.Timestamp

```py
get_value(sid, dt, field)
```

参数：

+   **sid** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 资产标识符。

+   **day** (*datetime64-like*) – 请求数据的日期的午夜。

+   **colname** (*string*) – 价格字段。例如：（‘open’, ‘high’, ‘low’, ‘close’, ‘volume’）

返回：

给定日期和 sid 的 colname 的现货价格。如果给定的日期和 sid 在股票的日期范围之前或之后，则引发 NoDataOnDate 异常。如果日期在日期范围内，但价格为 0，则返回-1。

返回类型：

[float](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")

```py
property last_available_dt
```

返回：**dt** – 读者可以提供数据的最后一个会话。 :rtype: pd.Timestamp

```py
load_raw_arrays(columns, start_date, end_date, assets)
```

参数：

+   **columns** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)") *of* [*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – ‘open’, ‘high’, ‘low’, ‘close’, 或 ‘volume’

+   **start_date** (*Timestamp*) – 窗口范围的开始。

+   **end_date** (*Timestamp*) – 窗口范围的结束。

+   **assets** ([*list*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)") *of* [*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 窗口中的资产标识符。

返回：

包含每个字段的 ndarrays 列表，形状为（范围中的分钟数，sid），数据类型为 float64，包含开始和结束 dt 范围内的相应字段的值。

返回类型：

[list](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)") of np.ndarray

```py
sid_day_index(sid, day)
```

参数：

+   **sid** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")) – 资产标识符。

+   **day** (*datetime64-like*) – 请求数据的日期的午夜。

返回：

为给定 sid 和日期索引数据带。如果在股票的日期范围内给定的日期和 sid 之前或之后，则引发 NoDataOnDate 异常。

返回类型：

[int](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")

```py
class zipline.data.adjustments.SQLiteAdjustmentReader(conn)
```

根据企业行为从 SQLite 数据库加载调整。

期望以 SQLiteAdjustmentWriter 输出的格式编写的数据。

参数：

**连接** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* [*sqlite3.Connection*](https://docs.python.org/3/library/sqlite3.html#sqlite3.Connection "(in Python v3.11)")) – 从中加载数据的连接。

另请参阅

`zipline.data.adjustments.SQLiteAdjustmentWriter`

```py
load_adjustments(dates, assets, should_include_splits, should_include_mergers, should_include_dividends, adjustment_type)
```

从基础调整数据库加载 Adjustment 对象集合。

参数：

+   **日期** (*pd.DatetimeIndex*) – 需要调整的日期。

+   **资产** (*pd.Int64Index*) – 需要调整的资产。

+   **应包括拆分调整** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 是否应包括拆分调整。

+   **应包括合并调整** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 是否应包括合并调整。

+   **应包括股息调整** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) – 是否应包括股息调整。

+   **调整类型** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 是否应在输出中包括价格调整、数量调整或两者。

返回：

**调整** – 包含价格和/或数量调整映射的字典，从索引到调整对象，在索引处应用。

返回类型：

[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[str -> dict[int -> Adjustment]]

```py
unpack_db_to_component_dfs(convert_dates=False)
```

返回调整文件中已知表的集合，以 DataFrame 形式。

参数：

**转换日期** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 默认情况下，日期以自 EPOCH 以来的秒数返回。如果 convert_dates 为 True，则所有日期列中的整数都将转换为日期时间。

返回：

**dfs** – 将表名映射到相应 DataFrame 版本的字典，其中所有日期列都已从整数强制转换回日期时间。

返回类型：

dict{str->DataFrame}

```py
class zipline.assets.AssetFinder(engine, future_chain_predicates={'AD': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'BP': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'CD': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'EL': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'GC': functools.partial(<built-in function delivery_predicate>, {'M', 'Z', 'Q', 'V', 'G', 'J'}), 'JY': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'ME': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'PA': functools.partial(<built-in function delivery_predicate>, {'Z', 'U', 'H', 'M'}), 'PL': functools.partial(<built-in function delivery_predicate>, {'J', 'F', 'V', 'N'}), 'SV': functools.partial(<built-in function delivery_predicate>, {'H', 'N', 'Z', 'U', 'K'}), 'XG': functools.partial(<built-in function delivery_predicate>, {'M', 'Z', 'Q', 'V', 'G', 'J'}), 'YS': functools.partial(<built-in function delivery_predicate>, {'H', 'N', 'Z', 'U', 'K'})})
```

资产查找器是`AssetDBWriter`编写资产元数据的数据库的接口。

该类提供按唯一整数 ID 或按符号查找资产的方法。出于历史原因，我们将这些唯一 ID 称为‘sids’。

参数：

+   **引擎** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中") *或* *SQLAlchemy.引擎*) – 一个引擎，用于连接到资产数据库，或者可以被 SQLAlchemy 解析为 URI 的字符串。

+   **future_chain_predicates** ([*字典*](https://docs.python.org/3/library/stdtypes.html#dict "(在 Python v3.11 中")) – 一个字典，将期货根符号映射到接受

+   **be** (*作为参数的合约并返回是否* *或* *不合约应该*) –

+   **链。** (*包含在*) –

**另请参阅**

`zipline.assets.AssetDBWriter`

```py
property equities_sids
```

资产查找器中所有股票的 sid。

```py
equities_sids_for_country_code(country_code)
```

返回给定国家的所有 sid。

**参数**：

**country_code** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中")) – ISO 3166 alpha-2 国家代码。

**返回**：

其交易所位于该国家的 sid。

**返回类型**：

[元组](https://docs.python.org/3/library/stdtypes.html#tuple "(在 Python v3.11 中")[[整数](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11 中")")

```py
equities_sids_for_exchange_name(exchange_name)
```

返回给定 exchange_name 的所有 sid。

**参数**：

**exchange_name** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中")) –

**返回**：

其交易所位于该国家的 sid。

**返回类型**：

[元组](https://docs.python.org/3/library/stdtypes.html#tuple "(在 Python v3.11 中")[[整数](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11 中")")

```py
property futures_sids
```

资产查找器中所有期货合约的 sid。

```py
get_supplementary_field(sid, field_name, as_of_date)
```

获取资产的补充字段的值。

**参数**：

+   **sid** ([*整数*](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11 中")) – 要查询的资产的 sid。

+   **field_name** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中")) – 补充字段的名称。

+   **as_of_date** (*pd.Timestamp**,* *None*) – 返回该日期上的最后一个已知值。如果为 None，则仅当我们对这个 sid 只有一个值时才返回值。如果为 None 且我们有多个值，则引发 MultipleValuesFoundForSid。

**引发**：

+   **NoValueForSid** – 如果我们没有此资产的值，或者在 as_of_date 上没有已知的值。

+   **MultipleValuesFoundForSid** – 如果我们有这个资产的多个值，并且为 as_of_date 传递了 None。

```py
group_by_type(sids)
```

按资产类型对 sid 列表进行分组。

**参数**：

**sids** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11 中")*[*[*整数*](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11 中")")*]*) –

**返回**：

**类型** – 一个字典，将唯一的资产类型映射到从 sid 中提取的 sid 列表。如果我们无法查找资产，我们将为其分配一个 None 键。

返回类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[[字符串](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")或无 -> 列表[[整数](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")]]

```py
lifetimes(dates, include_start_date, country_codes)
```

计算表示指定日期范围内资产生命周期的 DataFrame。

参数：

+   **日期** (*pd.DatetimeIndex*) – 用于计算生命周期的日期。

+   **include_start_date** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")) –

    是否将资产在其开始日期视为存活。

    这在回测环境中很有用，其中生命周期用于表示“我在这个日期的早晨是否有这个资产的数据？”对于许多金融指标（例如每日收盘价），在资产的第一天结束之前，数据不可用于该资产。

+   **国家代码** (*可迭代[*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*]*) – 获取生命周期的国家代码。

返回：

**生命周期** – 一个数据类型为布尔的框架，以日期为索引，以资产的 Int64Index 为列。在 lifetimes.loc[date, asset]处的值将为 True，当且仅当资产在 date 存在。如果 include_start_date 为 False，则当 date == asset.start_date 时，lifetimes.loc[date, asset]将为 false。

返回类型：

pd.DataFrame

另请参阅

[`numpy.putmask`](https://numpy.org/doc/stable/reference/generated/numpy.putmask.html#numpy.putmask "(in NumPy v1.25)"), `zipline.pipeline.engine.SimplePipelineEngine._compute_root_mask`

```py
lookup_asset_types(sids)
```

检索一组 sid 的资产类型。

参数：

**sids** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")*[*[*整数*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*]*) –

返回：

**类型** – 提供的 sid 的资产类型。

返回类型：

[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[sid -> 字符串或无]

```py
lookup_future_symbol(symbol)
```

通过符号查找未来合约。

参数：

**符号** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 所需合约的符号。

返回：

**未来** – 由`symbol`引用的未来合约。

返回类型：

未来

引发：

**SymbolNotFound** – 当找不到名为‘symbol’的合约时引发。

```py
lookup_generic(obj, as_of_date, country_code)
```

将对象转换为资产或资产序列。

此方法主要作为实现用户界面 API 的便利方法存在，该 API 可以处理多种类型的输入。在我们已经知道输入的预期类型的情况下，不应在内部代码中使用它。

参数：

+   **obj** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* [*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *Asset**,* *ContinuousFuture**, 或* *iterable*) – 要转换成一个或多个资产的对象。整数被解释为 sid。字符串被解释为股票代码。资产和连续期货保持不变。

+   **as_of_date** (*pd.Timestamp* *或* *None*) – 用于消除股票代码查找歧义的时间戳。与 lookup_symbol 中的语义相同。

+   **country_code** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* *None*) – 用于消除股票代码查找歧义的 ISO-3166 国家代码。与 lookup_symbol 中的语义相同。

返回：

**matches, missing** –

`matches` 是转换的结果。`missing` 是一个列表

包含无法解析的任何值的列表。如果 `obj` 不是可迭代的，`missing` 将是一个空列表。

返回类型：

[tuple](https://docs.python.org/3/library/stdtypes.html#tuple "(in Python v3.11)")

```py
lookup_symbol(symbol, as_of_date, fuzzy=False, country_code=None)
```

通过符号查找股票。

参数：

+   **symbol** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要解析的股票代码。

+   **as_of_date** ([*datetime.datetime*](https://docs.python.org/3/library/datetime.html#datetime.datetime "(in Python v3.11)") *或* *None*) – 查找此符号的最后持有者，直到这个日期时间。如果 `as_of_date` 为 None，则只能解析股票，如果只有一个股票曾经拥有该股票代码。

+   **fuzzy** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 是否使用模糊符号匹配？模糊符号匹配尝试解析股份类别表示的差异。例如，有些人可能将 `BRK` 的 `A` 股份类别表示为 `BRK.A`，而其他人可能写成 `BRK_A`。

+   **country_code** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)") *或* *None**, *可选*) – 要限制搜索的国家。如果不提供，搜索将跨越所有国家，这增加了模糊查找的可能性。

返回：

**equity** – 在给定的 `as_of_date` 上持有 `symbol` 的股票，或者如果 `as_of_date` 为 None，则持有 `symbol` 的唯一股票。

返回类型：

Equity

引发：

+   **SymbolNotFound** – 当没有股票持有给定符号时引发。

+   **MultipleSymbolsFound** – 当没有给出 `as_of_date` 并且有多个股票持有 `symbol` 时引发。当 `fuzzy=True` 并且 `as_of_date` 上有多个候选符号时也会引发。当没有给出 `country_code` 并且符号在多个国家之间模棱两可时也会引发。

```py
lookup_symbols(symbols, as_of_date, fuzzy=False, country_code=None)
```

通过符号查找股票列表。

等效于：

```py
[finder.lookup_symbol(s, as_of, fuzzy) for s in symbols] 
```

但由于重复查找被记忆化，可能更快。

参数：

+   **symbols**（*[str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")的*序列*）– 要解析的股票代码序列。

+   **as_of_date**（*pd.Timestamp*）– 转发给`lookup_symbol`。

+   **fuzzy**（*[bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")，可选）– 转发给`lookup_symbol`。

+   **country_code**（*[str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")或 None，可选）– 要限制搜索的国家。如果不提供，搜索将覆盖所有国家，这增加了模糊查找的可能性。

返回：

**equities**

返回类型：

[list](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[Equity]

```py
retrieve_all(sids, default_none=False)
```

检索 sids 中的所有资产。

参数：

+   **sids**（*[int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")的*可迭代对象*）– 要检索的资产。

+   **default_none**（*[bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")）– 如果为 True，对于失败的查找返回 None。如果为 False，则引发 SidsNotFound。

返回：

**assets** – 与 sids 长度相同的列表，包含与请求的 sids 对应的资产（或 Nones）。

返回类型：

[list](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[Asset 或 None]

引发：

**SidsNotFound** – 当请求的 sid 未找到且 default_none=False 时。

```py
retrieve_asset(sid, default_none=False)
```

检索给定 sid 的资产。

```py
retrieve_equities(sids)
```

为列表中的 sids 检索 Equity 对象。

用户通常不需要使用这个方法（相反，他们应该优先使用更通用/友好的 retrieve_assets 方法），但由于它在上游被使用，因此它有一个文档化的接口和测试。

参数：

**sids**（*[int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")的*可迭代对象*）–

返回：

**equities**

返回类型：

[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[int -> Equity]

引发：

**EquitiesNotFound** – 当请求的任何资产未找到时。

```py
retrieve_futures_contracts(sids)
```

为可迭代对象中的 sids 检索 Future 对象。

用户通常不需要使用这个方法（相反，他们应该优先使用更通用/友好的 retrieve_assets 方法），但由于它在上游被使用，因此它有一个文档化的接口和测试。

参数：

**sids**（*[int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")的*可迭代对象*）–

返回：

**equities**

返回类型：

[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")[int -> Equity]

引发：

**EquitiesNotFound** – 当请求的任何资产未找到时。

```py
property sids
```

资产查找器中的所有 sid。

```py
class zipline.data.data_portal.DataPortal(asset_finder, trading_calendar, first_trading_day, equity_daily_reader=None, equity_minute_reader=None, future_daily_reader=None, future_minute_reader=None, adjustment_reader=None, last_available_session=None, last_available_minute=None, minute_history_prefetch_length=1560, daily_history_prefetch_length=40)
```

接口到 zipline 模拟所需的所有数据。

这由模拟运行器用于回答有关数据的问题，例如获取某一天资产的价格或服务历史调用。

参数：

+   **asset_finder** (*zipline.assets.assets.AssetFinder*) – 用于解析资产的 AssetFinder 实例。

+   **trading_calendar** (*zipline.utils.calendar.exchange_calendar.TradingCalendar*) – 提供分钟到会话信息的日历实例。

+   **first_trading_day** (*pd.Timestamp*) – 模拟的第一个交易日。

+   **equity_daily_reader** (*BcolzDailyBarReader**,* *optional*) – 股票的日条形图阅读器。它将用于服务日数据回测或分钟回测中的日历史调用。如果没有提供日条形图阅读器但提供了分钟条形图阅读器，则分钟数据将被汇总以满足日请求。

+   **equity_minute_reader** (*BcolzMinuteBarReader**,* *optional*) – 股票的分钟条形图阅读器。它将用于服务分钟数据回测或分钟历史调用。如果没有提供日条形图阅读器，则可用于服务日调用。

+   **future_daily_reader** (*BcolzDailyBarReader**,* *optional*) – 期货的日条形图阅读器。它将用于服务日数据回测或分钟回测中的日历史调用。如果没有提供日条形图阅读器但提供了分钟条形图阅读器，则分钟数据将被汇总以满足日请求。

+   **future_minute_reader** (*BcolzFutureMinuteBarReader**,* *optional*) – 期货的分钟条形图阅读器。它将用于服务分钟数据回测或分钟历史调用。如果没有提供日条形图阅读器，则可用于服务日调用。

+   **adjustment_reader** (*SQLiteAdjustmentWriter**,* *optional*) – 调整阅读器。它用于将拆分、股息和其他调整数据应用于阅读器提供的原始数据。

+   **last_available_session** (*pd.Timestamp**,* *optional*) – 会话级数据中可用的最后一个会话。

+   **last_available_minute** (*pd.Timestamp**,* *optional*) – 分钟级数据中可用的最后一分钟。

```py
get_adjusted_value(asset, field, dt, perspective_dt, data_frequency, spot_value=None)
```

返回一个标量值，表示在给定 dt 时，所需资产字段的值，已应用调整。

参数：

+   **asset** (*Asset*) – 所需数据的资产。

+   **field** (*{'open'**,* *'high'**,* *'low'**,* *'close'**,* *'volume'**,* *'price'**,* *'last_traded'}*) – 资产的所需字段。

+   **dt** (*pd.Timestamp*) – 所需值的时间戳。

+   **perspective_dt** (*pd.Timestamp*) – 从该时间点回溯查看数据的起始时间戳。

+   **数据频率** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11)")) – 要查询的数据的频率；即数据是‘每日’还是‘分钟’条形图

返回：

**值** – 给定`字段`的`资产`在`时间戳`的值，已知`视角时间戳`的任何调整都已应用。返回类型基于所请求的`字段`。如果字段是‘开盘’，‘最高’，‘最低’，‘收盘’或‘价格’之一，则值将为浮点数。如果`字段`是‘成交量’，则值将为整数。如果`字段`是‘最后交易’，则值将为时间戳。

返回类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11)")，[整数](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11)")，或 pd.Timestamp

```py
get_adjustments(assets, field, dt, perspective_dt)
```

返回给定字段和资产列表在时间戳和视角时间戳之间的调整列表。

参数：

+   **资产** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11)") *的* *类型资产**，或* *资产*) – 所需的调整的资产或资产。

+   **字段** (*{'开盘'**,* *'最高'**,* *'最低'**,* *'收盘'**,* *'成交量'**,* *'价格'**,* *'最后交易'}*) – 资产的所需字段。

+   **时间戳** (*pd.Timestamp*) – 所需值的时间戳。

+   **视角时间戳** (*pd.Timestamp*) – 从哪个时间戳回看数据。

返回：

**调整** – 该字段的调整。

返回类型：

[列表](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11)")[调整]

```py
get_current_future_chain(continuous_future, dt)
```

根据连续期货规范，检索给定时间戳的合约的未来链。

返回：

**未来链** – 活跃期货的列表，其中第一个索引是根据连续期货定义指定的当前合约，第二个是下一个即将到来的合约，依此类推。

返回类型：

[列表](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11)")[期货]

```py
get_fetcher_assets(dt)
```

返回根据提取器数据定义的当前日期的资产列表。

返回：

**列表**

返回类型：

资产对象的列表。

```py
get_history_window(assets, end_dt, bar_count, frequency, field, data_frequency, ffill=True)
```

公共 API 方法，返回包含请求的历史窗口的数据框。数据已完全调整。

参数：

+   **资产** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11)") *的* *zipline.data.Asset 对象*) – 所需数据的资产。

+   **条形图数量** ([*整数*](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11)")) – 所需的条形图数量。

+   **频率** (*字符串*) – “1d”或“1m”

+   **字段** (*字符串*) – 资产的所需字段。

+   **数据频率** (*字符串*) – 要查询的数据的频率；即数据是‘每日’还是‘分钟’条形图。

+   **前向填充** (*布尔值*) – 前向填充缺失值。仅在字段为‘价格’时有效。

返回类型：

包含请求数据的 DataFrame。

```py
get_last_traded_dt(asset, dt, data_frequency)
```

给定一个资产和 dt，返回从给定 dt 视角的最后交易 dt。

如果在 dt 有交易，答案是 dt 提供的。

```py
get_scalar_asset_spot_value(asset, field, dt, data_frequency)
```

公共 API 方法，返回一个标量值，表示在给定 dt 时所需资产字段的值。

参数：

+   **资产** (*Asset*) – 所需数据的资产或资产。这不能是任意的 AssetConvertible。

+   **字段** (*{'open'**,* *'high'**,* *'low'**,* *'close'**,* *'volume'**,*) – ‘price’, ‘last_traded’} 资产的所需字段。

+   **dt** (*pd.Timestamp*) – 所需值的时间戳。

+   **数据频率** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中")) ) – 要查询的数据频率；即数据是‘每日’还是‘分钟’柱状图

返回：

**值** – 对于`资产`的`字段`的即时值。返回类型基于所请求的`字段`。如果`字段`是‘open’, ‘high’, ‘low’, ‘close’, 或 ‘price’之一，值将是一个浮点数。如果`字段`是‘volume’，值将是一个整数。如果`字段`是‘last_traded’，值将是一个 Timestamp。

返回类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中")), [整数](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11 中")), 或 pd.Timestamp

```py
get_splits(assets, dt)
```

返回给定 sid 和给定 dt 的任何分割。

参数：

+   **资产** (*容器*) – 我们想要分割的资产。

+   **dt** (*pd.Timestamp*) – 我们检查分割的日期。注意：这预计是 UTC 午夜。

返回：

**分割** – 分割列表，其中每个分割是一个(资产, 比率)元组。

返回类型：

[列表](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11 中)")[(资产, [浮点数](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中")))]

```py
get_spot_value(assets, field, dt, data_frequency)
```

公共 API 方法，返回一个标量值，表示在给定 dt 时所需资产字段的值。

参数：

+   **资产** (*Asset**,* *ContinuousFuture**, 或* *相同* *的* *iterable*.) – 所需数据的资产或资产。

+   **字段** (*{'open'**,* *'high'**,* *'low'**,* *'close'**,* *'volume'**,*) – ‘price’, ‘last_traded’} 资产的所需字段。

+   **dt** (*pd.Timestamp*) – 所需值的时间戳。

+   **数据频率** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中")) ) – 要查询的数据频率；即数据是‘每日’还是‘分钟’柱状图

返回：

**值** – 对于`资产`的`字段`的即时值。返回类型基于所请求的`字段`。如果`字段`是‘open’, ‘high’, ‘low’, ‘close’, 或 ‘price’之一，值将是一个浮点数。如果`字段`是‘volume’，值将是一个整数。如果`字段`是‘last_traded’，值将是一个 Timestamp。

返回类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")，[整数](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11 中)")，或 pd.Timestamp

```py
get_stock_dividends(sid, trading_days)
```

返回给定交易范围内特定 sid 的所有股票股息。

参数：

+   **sid** ([*整数*](https://docs.python.org/3/library/functions.html#int "(在 Python v3.11 中)")) – 应返回其股票股息的资产。

+   **trading_days** (*pd.DatetimeIndex*) – 交易范围。

返回：

+   **列表** (*包含所有相关属性已填充的对象列表。*)

+   *所有时间戳字段都转换为 pd.Timestamps。*

```py
handle_extra_source(source_df, sim_params)
```

额外的数据源总是有一个 sid 列。

我们将给定数据（通过前向填充）扩展到模拟日期的完整范围，以便在模拟期间快速查找。

```py
class zipline.sources.benchmark_source.BenchmarkSource(benchmark_asset, trading_calendar, sessions, data_portal, emission_rate='daily', benchmark_returns=None)
```

```py
daily_returns(start, end=None)
```

返回给定时间段内的每日回报。

参数：

+   **start** (*datetime*) – 包含开始会话标签。

+   **end** (*datetime**,* *可选*) – 包含结束会话标签。如果不提供，将`开始`视为标量键。

返回：

**返回** – 给定时间段内的回报。索引将是交易日历的范围[开始，结束]。如果只提供`开始`，则返回该日的标量值。

返回类型：

pd.Series 或 [浮点数](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")

```py
get_range(start_dt, end_dt)
```

查找给定时间段的回报。

参数：

+   **start_dt** (*datetime*) – 包含开始标签。

+   **end_dt** (*datetime*) – 包含结束标签。

返回：

**返回** – 回报序列。

返回类型：

pd.Series

另请参阅

`zipline.sources.benchmark_source.BenchmarkSource.daily_returns`

`如果`emission_rate == 'minute'`，此方法期望分钟级输入，而当`emission_rate == 'daily'`时，期望会话标签。`

```py`
get_value(dt)
```

查找给定 dt 的回报。

参数：

**dt** (*datetime*) – 查找的标签。

返回：

**返回** – 给定 dt 或会话的回报。

返回类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")

另请参阅

`zipline.sources.benchmark_source.BenchmarkSource.daily_returns`

`如果`emission_rate == 'minute'`，此方法期望分钟级输入，而当`emission_rate == 'daily'`时，期望会话标签。``

``### 包

```py
zipline.data.bundles.register(name='__no__default__', f='__no__default__', calendar_name='NYSE', start_session=None, end_session=None, minutes_per_day=390, create_writers=True)
```

注册数据包摄取函数。

参数：

+   **name** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11)")) – 包的名称。

+   **f** (*可调用*) –

    摄取函数。此函数将被传递：

    > environmapping
    > 
    > 此操作运行的环境。
    > 
    > asset_db_writerAssetDBWriter
    > 
    > 写入的资产数据库写入器。
    > 
    > minute_bar_writerBcolzMinuteBarWriter
    > 
    > 写入的分钟条形写入器。
    > 
    > daily_bar_writerBcolzDailyBarWriter
    > 
    > 写入的每日条形写入器。
    > 
    > adjustment_writerSQLiteAdjustmentWriter
    > 
    > 要写入的调整数据库写入器。
    > 
    > calendartrading_calendars.TradingCalendar
    > 
    > 要导入的交易日历。
    > 
    > start_sessionpd.Timestamp
    > 
    > 要导入的数据的第一个会话。
    > 
    > end_sessionpd.Timestamp
    > 
    > 要导入的数据的最后一个会话。
    > 
    > cacheDataFrameCache
    > 
    > 用于临时存储数据帧的映射对象。在加载失败的情况下，应使用此对象来缓存中间结果。在成功加载后，这将自动清理。
    > 
    > show_progressbool
    > 
    > 尽可能显示当前加载的进度。

+   **日历名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 用于对齐捆绑包数据的日历名称。默认值为‘NYSE’。

+   **start_session** (*pd.Timestamp**,* *可选*) – 我们想要数据的第一个会话。如果未提供，或者日期超出了日历支持的范围，则使用日历的第一个会话。

+   **end_session** (*pd.Timestamp**,* *可选*) – 我们想要数据的最后一个会话。如果未提供，或者日期超出了日历支持的范围，则使用日历的最后一个会话。

+   **minutes_per_day** ([*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*,* *可选*) – 每个正常交易日的分钟数。

+   **create_writers** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 导入机制是否应为导入函数创建写入器。在不需要它们的情况下，例如`quantopian-quandl`捆绑包，可以禁用此选项以进行优化。

注意

此函数可用作装饰器，例如：

```py
@register('quandl')
def quandl_ingest_function(...):
    ... 
```

另请参阅

`zipline.data.bundles.bundles`

```py
zipline.data.bundles.ingest(name, environ=os.environ, date=None, show_progress=True)
```

为给定的捆绑包导入数据。

参数：

+   **名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 捆绑包的名称。

+   **environ** (*mapping**,* *可选*) – 环境变量。默认为 os.environ。

+   **时间戳** (*datetime**,* *可选*) – 用于加载的时间戳。默认情况下，这是当前时间。

+   **assets_versions** (*Iterable**[*[*int*](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")*]**,* *可选*) – 要降级的资产数据库版本。

+   **show_progress** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 告诉导入函数在可能的情况下显示进度。

```py
zipline.data.bundles.load(name, environ=os.environ, date=None)
```

加载以前导入的捆绑包。

参数：

+   **名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 捆绑包的名称。

+   **environ** (*mapping**,* *可选*) – 环境变量。默认为 os.environ。

+   **时间戳** (*datetime**,* *可选*) – 要查找的数据的时间戳。默认为当前时间。

返回：

**bundle_data** – 此捆绑包的原始数据读取器。

返回类型：

BundleData

```py
zipline.data.bundles.unregister(name)
```

注销捆绑包。

参数：

**名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要注销的捆绑包的名称。

引发：

**UnknownBundle** – 当未使用给定名称注册任何捆绑包时引发。

另请参阅

`zipline.data.bundles.bundles`

```py
zipline.data.bundles.bundles
```

已注册的捆绑包，作为从捆绑包名称到捆绑包数据的映射。此映射是不可变的，只能通过`register()`或`unregister()`更新。

## 风险指标

### 算法状态

```py
class zipline.finance.ledger.Ledger(trading_sessions, capital_base, data_frequency)
```

分类账跟踪所有订单和交易，以及投资组合和头寸的当前状态。

```py
portfolio
```

正在管理的更新投资组合。

类型：

zipline.protocol.Portfolio

```py
account
```

正在管理的更新账户。

类型：

zipline.protocol.Account

```py
position_tracker
```

当前的一组头寸。

类型：

PositionTracker

```py
todays_returns
```

当天的回报。在分钟排放模式下，这是部分日子的回报。在每日排放模式下，这是`daily_returns[session]`。

类型：

[float](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")

```py
daily_returns_series
```

每日回报序列。尚未结束的日子将持有`np.nan`的值。

类型：

pd.Series

```py
daily_returns_array
```

作为 ndarray 的每日回报。尚未结束的日子将持有`np.nan`的值。

类型：

np.ndarray

```py
orders(dt=None)
```

检索给定栏或整个模拟中所有订单的字典形式。

参数：

**dt** (*pd.Timestamp* *或* *None**,* *可选*) – 要查找订单的特定日期时间。如果未传递或明确传递 None，则将返回所有订单。

返回：

**订单** – 订单信息。

返回类型：

[list](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[[dict](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")]

```py
override_account_fields(settled_cash=sentinel('not_overridden'), accrued_interest=sentinel('not_overridden'), buying_power=sentinel('not_overridden'), equity_with_loan=sentinel('not_overridden'), total_positions_value=sentinel('not_overridden'), total_positions_exposure=sentinel('not_overridden'), regt_equity=sentinel('not_overridden'), regt_margin=sentinel('not_overridden'), initial_margin_requirement=sentinel('not_overridden'), maintenance_margin_requirement=sentinel('not_overridden'), available_funds=sentinel('not_overridden'), excess_liquidity=sentinel('not_overridden'), cushion=sentinel('not_overridden'), day_trades_remaining=sentinel('not_overridden'), leverage=sentinel('not_overridden'), net_leverage=sentinel('not_overridden'), net_liquidation=sentinel('not_overridden'))
```

覆盖`self.account`上的字段。

```py
property portfolio
```

计算当前投资组合。

注释

这是缓存的，重复访问不会重新计算投资组合，直到投资组合可能已更改。

```py
process_commission(commission)
```

处理佣金。

参数：

**佣金** (*zp.Event*) – 要支付的佣金。

```py
process_dividends(next_session, asset_finder, adjustment_reader)
```

为下一次会议处理股息。

这将为我们赢得任何股息，其除息日是下一次会议，以及支付任何股息，其支付日期是下一次会议。

```py
process_order(order)
```

跟踪已下的订单。

参数：

**订单** (*zp.Order*) – 要记录的订单。

```py
process_splits(splits)
```

通过修改任何必要的位置来处理一系列拆分。

参数：

**拆分** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11 中)")***(*[资产**,* [*浮点数*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")*)**]*) – 拆分列表。每个拆分都是一个元组（资产，比率）。

```py
process_transaction(transaction)
```

向分类账添加交易，根据需要更新当前状态。

参数：

**交易** (*zp.Transaction*) – 要执行的交易。

```py
transactions(dt=None)
```

检索给定条形图或整个模拟的所有交易的字典形式。

参数：

**dt** (*pd.Timestamp* *或* *None**,* *可选*) – 用于查找交易的特定日期时间。如果没有传递，或者明确传递了 None，则将返回所有交易。

返回：

**交易** – 交易信息。

返回类型：

[列表](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11 中)")[[字典](https://docs.python.org/3/library/stdtypes.html#dict "(在 Python v3.11 中)")]

```py
update_portfolio()
```

强制计算当前投资组合状态。

```py
class zipline.protocol.Portfolio(start_date=None, capital_base=0.0)
```

提供对当前投资组合状态只读访问的对象。

参数：

+   **开始日期** (*pd.Timestamp*) – 记录的周期的开始日期。

+   **资本基础** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")) – 投资组合的起始值。这将用作起始现金、当前现金和投资组合价值。

```py
positions
```

包含有关当前持有仓位的信息的类似字典的对象。

类型：

zipline.protocol.Positions

```py
cash
```

投资组合中当前持有的现金金额。

类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")

```py
portfolio_value
```

投资组合持仓的当前清算价值。这等于`现金 + 总和(股份 * 价格)`

类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")

```py
starting_cash
```

回测开始时投资组合中的现金金额。

类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")

```py
property current_portfolio_weights
```

通过计算其持有的价值除以所有持仓的总价值来计算投资组合中每项资产的权重。

每只股票的价值是其价格乘以持有的股份数量。每个期货合约的价值是其单位价格乘以持有的股份数量乘以乘数。

```py
class zipline.protocol.Account
```

账户对象跟踪有关交易账户的信息。随着算法运行，这些值会更新，其键保持不变。如果连接到经纪人，可以使用经纪人报告的交易账户值更新这些值。

```py
class zipline.finance.ledger.PositionTracker(data_frequency)
```

持有的仓位当前状态。

参数：

**数据频率** (*{'每日'**,* *'分钟'}*) – 模拟的数据频率。

```py
earn_dividends(cash_dividends, stock_dividends)
```

给定一组除息日均为下一个交易日的股息列表，计算并存储每个股息支付日应支付的现金和/或股票支付。

参数：

+   **现金股息** (*可迭代* *的* *(**资产**,* *金额**,* *支付日期**)* *命名元组*) –

+   **stock_dividends** (*iterable* *of* *(**asset**,* *payment_asset**,* *ratio**,* *pay_date**)*) – namedtuples。

```py
handle_splits(splits)
```

通过修改任何必要的持仓来处理分割列表。

参数：

**分割** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")) – 分割列表。每个分割是一个元组（资产，比率）。

返回：

**int** – 持仓。

返回类型：

修改每个后剩余的零头现金

```py
pay_dividends(next_trading_day)
```

根据累积的账本记录，返回应支付的股息所对应的现金支付。

```py
property stats
```

持仓的当前状态。

返回：

**stats** – 当前的持仓统计数据。

返回类型：

PositionStats

注意

这是缓存的，重复访问不会重新计算统计数据，除非统计数据可能已更改。

```py
class zipline.finance._finance_ext.PositionStats
```

从当前持仓计算得出的值。

```py
gross_exposure
```

总持仓敞口。

类型：

float64

```py
gross_value
```

总持仓价值。

类型：

float64

```py
long_exposure
```

仅多头寸的敞口。

类型：

float64

```py
long_value
```

仅多头寸的价值。

类型：

float64

```py
net_exposure
```

净持仓敞口。

类型：

float64

```py
net_value
```

净持仓价值。

类型：

float64

```py
short_exposure
```

仅空头寸的敞口。

类型：

float64

```py
short_value
```

仅空头寸的价值。

类型：

float64

```py
longs_count
```

多头寸的数量。

类型：

int64

```py
shorts_count
```

空头寸的数量。

类型：

int64

```py
position_exposure_array
```

以与 `position_tracker.positions` 相同的顺序排列的每个持仓的敞口。

类型：

np.ndarray[float64]

```py
position_exposure_series
```

以与 `position_tracker.positions` 相同的顺序排列的每个持仓的敞口。索引是每个资产的数字 sid。

类型：

pd.Series[float64]

注意

`position_exposure_array` 和 `position_exposure_series` 共享相同的底层内存。如果您每分钟都在访问，则应首选数组接口以获得更好的性能。

`position_exposure_array` 和 `position_exposure_series` 在下次更新统计数据时可能会被修改。不要依赖这些对象在访问 `stats` 时保持不变。如果需要冻结值，必须进行复制。

### 内置指标

```py
class zipline.finance.metrics.metric.SimpleLedgerField(ledger_field, packet_field=None)
```

每栏或每节发出账本字段的当前值。

参数：

+   **ledger_field** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要读取的账本字段。

+   **packet_field** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 要在数据包中填充的字段名称。如果未提供，将使用`ledger_field`。

```py
class zipline.finance.metrics.metric.DailyLedgerField(ledger_field, packet_field=None)
```

类似于 `SimpleLedgerField`，但也将当前值放入 `cumulative_perf` 部分。

参数：

+   **ledger_field** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要读取的账本字段。

+   **packet_field** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *optional*) – 数据包中要填充的字段的名称。如果未提供，将使用`ledger_field`。

```py
class zipline.finance.metrics.metric.StartOfPeriodLedgerField(ledger_field, packet_field=None)
```

跟踪周期开始时分类账字段的值。

参数：

+   **ledger_field** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要读取的分类账字段。

+   **packet_field** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *optional*) – 数据包中要填充的字段的名称。如果未提供，将使用`ledger_field`。

```py
class zipline.finance.metrics.metric.StartOfPeriodLedgerField(ledger_field, packet_field=None)
```

跟踪周期开始时分类账字段的值。

参数：

+   **ledger_field** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要读取的分类账字段。

+   **packet_field** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *optional*) – 数据包中要填充的字段的名称。如果未提供，将使用`ledger_field`。

```py
class zipline.finance.metrics.metric.Returns
```

跟踪算法的每日和累计收益。

```py
class zipline.finance.metrics.metric.BenchmarkReturnsAndVolatility
```

跟踪基准的每日和累计收益以及基准收益的波动性。

```py
class zipline.finance.metrics.metric.CashFlow
```

跟踪每日和累计现金流。

注释

由于历史原因，该字段在数据包中被命名为“capital_used”。

```py
class zipline.finance.metrics.metric.Orders
```

跟踪每日订单。

```py
class zipline.finance.metrics.metric.Transactions
```

跟踪每日交易。

```py
class zipline.finance.metrics.metric.Positions
```

跟踪每日持仓。

```py
class zipline.finance.metrics.metric.ReturnsStatistic(function, field_name=None)
```

报告模拟结束时从算法收益计算出的标量或时间序列的指标。

参数：

+   **function** (*callable*) – 对每日收益调用的函数。

+   **field_name** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *optional*) – 字段的名称。如果未提供，将使用`function.__name__`。

```py
class zipline.finance.metrics.metric.AlphaBeta
```

模拟结束时的阿尔法和贝塔相对于基准。

```py
class zipline.finance.metrics.metric.MaxLeverage
```

跟踪账户最大杠杆率。

### 指标集

```py
zipline.finance.metrics.register(name, function=None)
```

注册新的指标集。

参数：

+   **name** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 指标集的名称

+   **function** (*callable*) – 产生指标集的可调用对象。

注释

如果只传递`name`，则可以用作装饰器。

另请参阅

`zipline.finance.metrics.get_metrics_set`, `zipline.finance.metrics.unregister_metrics_set`

```py
zipline.finance.metrics.load(name)
```

返回与给定名称注册的指标集的实例。

返回：

**指标** – 指标集的新实例。

返回类型：

[set](https://docs.python.org/3/library/stdtypes.html#set "(in Python v3.11)")[Metric]

引发：

[**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError "(in Python v3.11)") – 当没有注册到`name`的指标集时引发。

```py
zipline.finance.metrics.unregister(name)
```

注销现有的指标集。

参数：

**name** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 指标集的名称

另请参阅

`zipline.finance.metrics.register_metrics_set`

```py
zipline.data.finance.metrics.metrics_sets
```

已注册的指标集，作为指标集名称到加载函数的映射。此映射是不可变的，只能通过 `register()` 或 `unregister()` 进行更新。

### 算法状态

```py
class zipline.finance.ledger.Ledger(trading_sessions, capital_base, data_frequency)
```

账本记录所有订单和交易，以及投资组合和持仓的当前状态。

```py
portfolio
```

被管理的更新投资组合。

类型：

zipline.protocol.Portfolio

```py
account
```

被管理的更新账户。

类型：

zipline.protocol.Account

```py
position_tracker
```

当前持仓集合。

类型：

PositionTracker

```py
todays_returns
```

当天的回报。在分钟排放模式下，这是部分日回报。在每日排放模式下，这是 `daily_returns[session]`。

类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")

```py
daily_returns_series
```

每日回报系列。尚未完成的交易日将持有 `np.nan` 值。

类型：

pd.Series

```py
daily_returns_array
```

每日回报作为 ndarray。尚未完成的交易日将持有 `np.nan` 值。

类型：

np.ndarray

```py
orders(dt=None)
```

获取给定时间段内或整个模拟过程中的所有订单记录的字典形式。

参数：

**dt** (*pd.Timestamp* *或* *None**,* *可选*) – 要查找订单的特定日期时间。如果未传递或明确传递 None，则将返回所有订单。

返回：

**订单** – 订单信息。

返回类型：

[列表](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")[[字典](https://docs.python.org/3/library/stdtypes.html#dict "(in Python v3.11)")]

```py
override_account_fields(settled_cash=sentinel('not_overridden'), accrued_interest=sentinel('not_overridden'), buying_power=sentinel('not_overridden'), equity_with_loan=sentinel('not_overridden'), total_positions_value=sentinel('not_overridden'), total_positions_exposure=sentinel('not_overridden'), regt_equity=sentinel('not_overridden'), regt_margin=sentinel('not_overridden'), initial_margin_requirement=sentinel('not_overridden'), maintenance_margin_requirement=sentinel('not_overridden'), available_funds=sentinel('not_overridden'), excess_liquidity=sentinel('not_overridden'), cushion=sentinel('not_overridden'), day_trades_remaining=sentinel('not_overridden'), leverage=sentinel('not_overridden'), net_leverage=sentinel('not_overridden'), net_liquidation=sentinel('not_overridden'))
```

覆盖 `self.account` 上的字段。

```py
property portfolio
```

计算当前投资组合。

注释

这是缓存的，重复访问不会重新计算投资组合，除非投资组合可能已更改。

```py
process_commission(commission)
```

处理佣金。

参数：

**佣金** (*zp.Event*) – 支付的佣金。

```py
process_dividends(next_session, asset_finder, adjustment_reader)
```

为下一个交易日处理股息。

这将使我们获得下一个交易日除息日的任何股息，以及支付下一个交易日支付日的任何股息。

```py
process_order(order)
```

跟踪已下达的订单。

参数：

**订单** (*zp.Order*) – 要记录的订单。

```py
process_splits(splits)
```

通过修改任何必要的位置来处理拆分列表。

参数：

**拆分** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(in Python v3.11)")***(*[*资产**,* [*浮点数*](https://docs.python.org/3/library/functions.html#float "(in Python v3.11)")*)**]*) – 拆分列表。每个拆分是一个元组（资产，比率）。

```py
process_transaction(transaction)
```

向账本添加一笔交易，并根据需要更新当前状态。

参数：

**交易** (*zp.Transaction*) – 要执行的交易。

```py
transactions(dt=None)
```

获取给定时间段内或整个模拟过程中的所有交易记录的字典形式。

参数：

**dt** (*pd.Timestamp* *或* *None**,* *可选*) – 用于查找交易的特定日期时间。如果没有传递或明确传递 None，则将返回所有交易。

返回：

**交易** – 交易信息。

返回类型：

[列表](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11)")[[字典](https://docs.python.org/3/library/stdtypes.html#dict "(在 Python v3.11)")]

```py
update_portfolio()
```

强制计算当前投资组合状态。

```py
class zipline.protocol.Portfolio(start_date=None, capital_base=0.0)
```

提供对当前投资组合状态的只读访问的对象。

参数：

+   **开始日期** (*pd.Timestamp*) – 记录周期的开始日期。

+   **资本基础** ([*浮点数*](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11)")) – 投资组合的起始价值。这将用作起始现金、当前现金和投资组合价值。

```py
positions
```

包含有关当前持有头寸信息的类似字典的对象。

类型：

zipline.protocol.Positions

```py
cash
```

投资组合中当前持有的现金金额。

类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")

```py
portfolio_value
```

投资组合持有的当前清算价值。这等于`现金 + 总和(份额 * 价格)`

类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")

```py
starting_cash
```

回测开始时投资组合中的现金金额。

类型：

[浮点数](https://docs.python.org/3/library/functions.html#float "(在 Python v3.11 中)")

```py
property current_portfolio_weights
```

通过计算其持有的价值除以所有头寸的总价值来计算投资组合中每个资产的权重。

每个股票的价值是其价格乘以持有的股份数量。每个期货合约的价值是其单位价格乘以持有的股份数量乘以乘数。

```py
class zipline.protocol.Account
```

账户对象跟踪有关交易账户的信息。随着算法运行，这些值会更新，其键保持不变。如果连接到经纪人，可以使用经纪人报告的交易账户值更新这些值。

```py
class zipline.finance.ledger.PositionTracker(data_frequency)
```

持有的头寸的当前状态。

参数：

**数据频率** (*{'每日'**,* *'分钟'}*) – 模拟的数据频率。

```py
earn_dividends(cash_dividends, stock_dividends)
```

给定一个股息列表，其除息日都是下一个交易日，计算并存储应在每个股息的支付日期支付的现金和/或股票支付。

参数：

+   **现金分红** (*iterable* *of* *(**资产**,* *金额**,* *支付日期**)* *namedtuples*) –

+   **股票分红** (*iterable* *of* *(**资产**,* *支付资产**,* *比率**,* *支付日期**)*) – namedtuples。

```py
handle_splits(splits)
```

通过修改任何所需的头寸来处理拆分列表。

参数：

**拆分** ([*列表*](https://docs.python.org/3/library/stdtypes.html#list "(在 Python v3.11)")) – 拆分的列表。每个拆分是(资产, 比率)的元组。

返回：

**整数** – 头寸。

返回类型：

修改每个后，剩余的现金来自分数份额

```py
pay_dividends(next_trading_day)
```

根据应根据累积的簿记支付的股息返回现金支付。

```py
property stats
```

仓位的当前状态。

返回：

**统计数据** – 当前统计数据的位置统计数据。

返回类型：

PositionStats

注释

这是缓存的，重复访问不会重新计算统计数据，除非统计数据可能已更改。

```py
class zipline.finance._finance_ext.PositionStats
```

从当前仓位计算的值。

```py
gross_exposure
```

总仓位暴露。

类型：

浮点数 64 位

```py
gross_value
```

总仓位价值。

类型：

浮点数 64 位

```py
long_exposure
```

仅长仓位的暴露。

类型：

浮点数 64 位

```py
long_value
```

仅长仓位的价值。

类型：

浮点数 64 位

```py
net_exposure
```

净仓位暴露。

类型：

浮点数 64 位

```py
net_value
```

净仓位价值。

类型：

浮点数 64 位

```py
short_exposure
```

仅短仓位的暴露。

类型：

浮点数 64 位

```py
short_value
```

仅短仓位的价值。

类型：

浮点数 64 位

```py
longs_count
```

长仓位的数量。

类型：

整数 64 位

```py
shorts_count
```

短仓位的数量。

类型：

整数 64 位

```py
position_exposure_array
```

与 `position_tracker.positions` 相同顺序的每个仓位的暴露。

类型：

np.ndarray[float64]

```py
position_exposure_series
```

与 `position_tracker.positions` 相同顺序的每个仓位的暴露。索引是每个资产的数字 sid。

类型：

pd.Series[float64]

注释

`position_exposure_array` 和 `position_exposure_series` 共享相同的底层内存。如果您需要每分钟访问一次，则应首选数组接口以获得更好的性能。

`position_exposure_array` 和 `position_exposure_series` 在仓位跟踪器下一次更新统计数据时可能会被修改。不要依赖这些对象在访问 `stats` 时保持不变。如果您需要冻结值，则必须进行复制。

### 内置指标

```py
class zipline.finance.metrics.metric.SimpleLedgerField(ledger_field, packet_field=None)
```

每个条形图或每个会话发出分类账字段的当前值。

参数：

+   **ledger_field** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要读取的分类账字段。

+   **packet_field** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 要在数据包中填充的字段名称。如果未提供，将使用 `ledger_field`。

```py
class zipline.finance.metrics.metric.DailyLedgerField(ledger_field, packet_field=None)
```

类似于 `SimpleLedgerField`，但也将当前值放入 `cumulative_perf` 部分。

参数：

+   **ledger_field** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要读取的分类账字段。

+   **packet_field** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 要在数据包中填充的字段名称。如果未提供，将使用 `ledger_field`。

```py
class zipline.finance.metrics.metric.StartOfPeriodLedgerField(ledger_field, packet_field=None)
```

跟踪分类账字段在周期开始时的价值。

参数：

+   **ledger_field** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要读取的分类账字段。

+   **packet_field** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 要在数据包中填充的字段名称。如果未提供，将使用 `ledger_field`。

```py
class zipline.finance.metrics.metric.StartOfPeriodLedgerField(ledger_field, packet_field=None)
```

跟踪分类账字段在周期开始时的价值。

参数：

+   **账本字段** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 要读取的账本字段。

+   **数据包字段** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 数据包中要填充的字段的名称。如果未提供，将使用 `ledger_field`。

```py
class zipline.finance.metrics.metric.Returns
```

跟踪算法的日回报率和累计回报率。

```py
class zipline.finance.metrics.metric.BenchmarkReturnsAndVolatility
```

跟踪基准的日回报率和累计回报率，以及基准回报率的波动性。

```py
class zipline.finance.metrics.metric.CashFlow
```

跟踪每日和累计现金流。

注释

由于历史原因，此字段在数据包中名为‘capital_used’。

```py
class zipline.finance.metrics.metric.Orders
```

跟踪每日订单。

```py
class zipline.finance.metrics.metric.Transactions
```

跟踪每日交易。

```py
class zipline.finance.metrics.metric.Positions
```

跟踪每日持仓。

```py
class zipline.finance.metrics.metric.ReturnsStatistic(function, field_name=None)
```

报告模拟结束时从算法回报计算出的标量或时间序列的指标。

参数：

+   **函数** (*可调用*) – 用于每日回报的调用函数。

+   **字段名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 字段的名称。如果未提供，则将使用 `function.__name__`。

```py
class zipline.finance.metrics.metric.AlphaBeta
```

模拟结束时相对于基准的阿尔法和贝塔。

```py
class zipline.finance.metrics.metric.MaxLeverage
```

跟踪账户最大杠杆率。

### 指标集

```py
zipline.finance.metrics.register(name, function=None)
```

注册新的指标集。

参数：

+   **名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 指标集的名称

+   **函数** (*可调用*) – 产生指标集的可调用对象。

注释

如果只传递 `name`，则可以用作装饰器。

另请参阅

`zipline.finance.metrics.get_metrics_set`, `zipline.finance.metrics.unregister_metrics_set`

```py
zipline.finance.metrics.load(name)
```

返回已使用给定名称注册的指标集的实例。

返回：

**指标** – 指标集的新实例。

返回类型：

[set](https://docs.python.org/3/library/stdtypes.html#set "(in Python v3.11)")[Metric]

引发：

[**ValueError**](https://docs.python.org/3/library/exceptions.html#ValueError "(in Python v3.11)") – 当未注册到 `name` 的指标集时引发

```py
zipline.finance.metrics.unregister(name)
```

注销现有的指标集。

参数：

**名称** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 指标集的名称

另请参阅

`zipline.finance.metrics.register_metrics_set`

```py
zipline.data.finance.metrics.metrics_sets
```

已注册的指标集，作为从指标集名称到加载函数的映射。此映射是不可变的，只能通过 `register()` 或 `unregister()` 进行更新。

## 实用工具

### 缓存

```py
class zipline.utils.cache.CachedObject(value, expires)
```

用于维护带有到期日期的缓存对象的简单结构。

参数：

+   **值** ([*对象*](https://docs.python.org/3/library/functions.html#object "(in Python v3.11)")) – 要缓存的对象。

+   **到期** (*datetime-like*) – 值的到期日期。对于**严格大于**到期日期的日期，缓存被认为无效。

示例

```py
>>> from pandas import Timestamp, Timedelta
>>> expires = Timestamp('2014', tz='UTC')
>>> obj = CachedObject(1, expires)
>>> obj.unwrap(expires - Timedelta('1 minute'))
1
>>> obj.unwrap(expires)
1
>>> obj.unwrap(expires + Timedelta('1 minute'))
... 
Traceback (most recent call last):
  ...
Expired: 2014-01-01 00:00:00+00:00 
```

```py
class zipline.utils.cache.ExpiringCache(cache=None, cleanup=<function ExpiringCache.<lambda>>)
```

一个包含多个 CachedObjects 的缓存，它会返回包装的值，如果值已过期，则会引发异常并删除 CachedObject。

参数：

+   **缓存** (*类似字典**,* *可选*) – 一个类似字典的对象实例，至少需要支持：__del__, __getitem__, __setitem__。如果为 None，则默认使用字典。

+   **清理** (*可调用**,* *可选*) – 一个接受单个参数（一个缓存对象）的方法，在缓存对象过期之前被调用，并在删除对象之前调用。如果没有提供，默认是一个无操作。

示例

```py
>>> from pandas import Timestamp, Timedelta
>>> expires = Timestamp('2014', tz='UTC')
>>> value = 1
>>> cache = ExpiringCache()
>>> cache.set('foo', value, expires)
>>> cache.get('foo', expires - Timedelta('1 minute'))
1
>>> cache.get('foo', expires + Timedelta('1 minute'))
Traceback (most recent call last):
  ...
KeyError: 'foo' 
```

```py
class zipline.utils.cache.dataframe_cache(path=None, lock=None, clean_on_failure=True, serialization='pickle')
```

用于数据帧的基于磁盘的缓存。

`dataframe_cache`是一个可变的字符串名称到 pandas DataFrame 对象的映射。该对象可以用作上下文管理器，在退出时删除缓存目录。

参数：

+   **路径** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")*,* *可选*) – 缓存目录的路径。文件将写入为`路径/<键名>`。

+   **锁** (*Lock**,* *可选*) – 用于多线程/多进程访问缓存的线程锁。如果没有提供，则不会使用锁。

+   **clean_on_failure** ([*bool*](https://docs.python.org/3/library/functions.html#bool "(in Python v3.11)")*,* *可选*) – 如果在上下文管理器中引发异常，是否应该清理目录。

+   **序列化** (*{'msgpack'**,* *'pickle:<n>'}**,* *可选*) – 数据应该如何进行序列化。如果传递了`'pickle'`，则可以传递一个可选的 pickle 协议，例如：`'pickle:3'`，表示使用 pickle 协议 3。

注意

语法`缓存[:]`将所有键值对加载到内存中作为一个字典。缓存使用的是一种临时文件格式，这种格式可能会在 zipline 的不同版本之间发生变化。

```py
class zipline.utils.cache.working_file(final_path, *args, **kwargs)
```

一个用于管理临时文件的上下文管理器，如果在上下文中没有引发异常，则将移动到非临时位置。

参数：

+   **最终路径** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 提交时移动文件的位置。

+   ***args** – 转发给 NamedTemporaryFile。

+   ****kwargs** – 转发给 NamedTemporaryFile。

注意

如果没有异常，文件将在 __exit__ 时移动。`working_file`使用[`shutil.move()`](https://docs.python.org/3/library/shutil.html#shutil.move "(in Python v3.11)")来移动实际文件，这意味着它与[`shutil.move()`](https://docs.python.org/3/library/shutil.html#shutil.move "(in Python v3.11)")具有同样强的保证。

```py
class zipline.utils.cache.working_dir(final_path, *args, **kwargs)
```

一个用于管理临时目录的上下文管理器，如果在上下文中没有引发异常，则将移动到非临时位置。

参数：

+   **最终路径** ([*str*](https://docs.python.org/3/library/stdtypes.html#str "(in Python v3.11)")) – 提交时移动文件的位置。

+   ***args** – 转发给 tmp_dir。

+   ****kwargs** – 转发给 tmp_dir。

注意

如果没有异常，文件将在 __exit__ 时移动。`working_dir`使用`dir_util.copy_tree()`来移动实际文件，这意味着它与`dir_util.copy_tree()`一样具有强保证。

### 命令行

```py
zipline.utils.cli.maybe_show_progress(it, show_progress, **kwargs)
```

可选择为给定的迭代器显示进度条。

参数：

+   **迭代器** (*可迭代*) – 底层迭代器。

+   **显示进度** ([*布尔*](https://docs.python.org/3/library/functions.html#bool "(在 Python v3.11)")) – 是否应显示进度。

+   ****kwargs** – 转发给 click 进度条。

返回：

**迭代上下文** – 一个上下文管理器，其进入是实际要使用的迭代器。

返回类型：

上下文管理器

示例

```py
with maybe_show_progress([1, 2, 3], True) as ns:
     for n in ns:
         ... 
```

### 缓存

```py
class zipline.utils.cache.CachedObject(value, expires)
```

一个简单的结构，用于维护带有过期日期的缓存对象。

参数：

+   **值** ([*对象*](https://docs.python.org/3/library/functions.html#object "(在 Python v3.11)")) – 要缓存的对象。

+   **过期时间** (*类似 datetime*) – 值的过期日期。对于**严格大于**过期时间的日期，缓存被认为无效。

示例

```py
>>> from pandas import Timestamp, Timedelta
>>> expires = Timestamp('2014', tz='UTC')
>>> obj = CachedObject(1, expires)
>>> obj.unwrap(expires - Timedelta('1 minute'))
1
>>> obj.unwrap(expires)
1
>>> obj.unwrap(expires + Timedelta('1 minute'))
... 
Traceback (most recent call last):
  ...
Expired: 2014-01-01 00:00:00+00:00 
```

```py
class zipline.utils.cache.ExpiringCache(cache=None, cleanup=<function ExpiringCache.<lambda>>)
```

多个 CachedObjects 的缓存，它返回包装的值，如果值已过期，则引发并删除 CachedObject。

参数：

+   **缓存** (*类似字典**,* *可选*) – 一个类似字典的对象实例，至少需要支持：__del__, __getitem__, __setitem__。如果为 None，则默认使用字典。

+   **清理** (*可调用**,* *可选*) – 一种方法，它接受一个参数，即缓存的对象，并在缓存对象过期时调用，在删除对象之前。如果不提供，默认为无操作。

示例

```py
>>> from pandas import Timestamp, Timedelta
>>> expires = Timestamp('2014', tz='UTC')
>>> value = 1
>>> cache = ExpiringCache()
>>> cache.set('foo', value, expires)
>>> cache.get('foo', expires - Timedelta('1 minute'))
1
>>> cache.get('foo', expires + Timedelta('1 minute'))
Traceback (most recent call last):
  ...
KeyError: 'foo' 
```

```py
class zipline.utils.cache.dataframe_cache(path=None, lock=None, clean_on_failure=True, serialization='pickle')
```

用于数据帧的基于磁盘的缓存。

`dataframe_cache` 是一个可变的字符串名称到 pandas DataFrame 对象的映射。此对象可以用作上下文管理器，在退出时删除缓存目录。

参数：

+   **路径** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11)")*,* *可选*) – 缓存的目录路径。文件将作为`path/<keyname>`写入。

+   **锁** (*锁**,* *可选*) – 用于多线程/多进程访问缓存的线程锁。如果不提供，将不使用锁定。

+   **在失败时清理** ([*布尔*](https://docs.python.org/3/library/functions.html#bool "(在 Python v3.11)")*,* *可选*) – 如果在上下文管理器中引发异常，是否应该清理目录。

+   **序列化** (*{'msgpack'**,* *'pickle:<n>'}**,* *可选*) – 数据应该如何被序列化。如果传递了`'pickle'`，可以传递一个可选的 pickle 协议，例如：`'pickle:3'`，表示使用 pickle 协议 3。

注意

语法`cache[:]`将所有键值对加载到内存中作为字典。缓存使用临时文件格式，该格式可能在 zipline 的不同版本之间发生变化。

```py
class zipline.utils.cache.working_file(final_path, *args, **kwargs)
```

一个用于管理临时文件的上下文管理器，如果在上下文中没有引发异常，则该文件将被移动到非临时位置。

参数：

+   **最终路径** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11)")) – 提交时移动文件的位置。

+   ***args** – 转发给 NamedTemporaryFile。

+   ****kwargs** – 转发给 NamedTemporaryFile。

注意

如果没有异常，文件将在 __exit__ 时移动。`working_file`使用[`shutil.move()`](https://docs.python.org/3/library/shutil.html#shutil.move "(在 Python v3.11 中)")来移动实际文件，这意味着它与[`shutil.move()`](https://docs.python.org/3/library/shutil.html#shutil.move "(在 Python v3.11 中)")具有同样强的保证。

```py
class zipline.utils.cache.working_dir(final_path, *args, **kwargs)
```

一个用于管理临时目录的上下文管理器，如果在上下文中没有引发异常，则该目录将被移动到非临时位置。

参数：

+   **最终路径** ([*字符串*](https://docs.python.org/3/library/stdtypes.html#str "(在 Python v3.11 中)")) – 提交时移动文件的位置。

+   ***args** – 转发给 tmp_dir。

+   ****kwargs** – 转发给 tmp_dir。

注意

如果没有异常，文件将在 __exit__ 时移动。`working_dir`使用`dir_util.copy_tree()`来移动实际文件，这意味着它与`dir_util.copy_tree()`具有同样强的保证。

### 命令行

```py
zipline.utils.cli.maybe_show_progress(it, show_progress, **kwargs)
```

可选择为给定迭代器显示进度条。

参数：

+   **它** (*可迭代*) – 底层迭代器。

+   **显示进度** ([*布尔*](https://docs.python.org/3/library/functions.html#bool "(在 Python v3.11 中)")) – 是否应显示进度。

+   ****kwargs** – 转发给点击进度条。

返回：

**迭代上下文** – 一个上下文管理器，其进入是实际要使用的迭代器。

返回类型：

上下文管理器

示例

```py
with maybe_show_progress([1, 2, 3], True) as ns:
     for n in ns:
         ... 
`````````
