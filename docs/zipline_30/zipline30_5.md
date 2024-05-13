# 度量

> 原文：[`zipline.ml4trading.io/risk-and-perf-metrics.html`](https://zipline.ml4trading.io/risk-and-perf-metrics.html)

风险和性能度量是 Zipline 在运行模拟时计算的汇总值。这些度量可以是关于算法性能的，如回报或现金流，或者是算法的风险性，如波动性或贝塔。度量可以每分钟、每天或一次在模拟结束时报告。单个度量可以选择在适当的情况下在多个时间尺度上报。

## 度量集

Zipline 将风险和性能度量分组为称为“度量集”的集合。单个度量集定义了单个回测期间要跟踪的所有度量。度量集可以包含在不同时间尺度上报的度量。默认度量集将计算一系列度量，如算法回报、波动性、夏普比率和贝塔。

## 选择度量集

在运行模拟时，用户可以选择要报告的度量集。选择度量集的方式取决于运行算法的接口。

### 命令行和 IPython 魔术

在使用命令行或 IPython 魔术接口运行时，可以通过传递`--metrics-set`参数来选择度量集。例如：

```py
$  zipline  run  algorithm.py  -s  2014-01-01  -e  2014-02-01  --metrics-set  my-metrics-set 
```

### `run_algorithm`

在使用`run_algorithm()`接口运行时，可以通过`metrics_set`参数传递度量集。这可以是已注册度量集的名称，也可以是一组度量对象。例如：

```py
run_algorithm(..., metrics_set='my-metrics-set')
run_algorithm(..., metrics_set={MyMetric(), MyOtherMetric(), ...}) 
```

## 不带度量运行

计算风险和性能度量并非免费，这会增加回测的总运行时间。在积极开发算法时，通常有助于跳过这些计算以加快调试周期。要禁用所有度量的计算和报告，用户可以选择内置度量集`none`。例如：

```py
$  zipline  run  algorithm.py  -s  2014-01-01  -e  2014-02-01  --metrics-set  none 
```

## 定义新度量

度量是实现以下方法子集的任何对象：

+   `start_of_simulation`

+   `end_of_simulation`

+   `start_of_session`

+   `end_of_session`

+   `end_of_bar`

这些函数将在其名称指示的时间被调用，此时度量对象可以收集任何所需信息，并可选地报告计算值。如果度量在某个时间不需要进行任何处理，则可以省略对该方法的定义。

度量应该是可重用的，这意味着单个度量类实例可以用于多个回测。度量不需要同时支持多个模拟，这意味着内部缓存和数据在`start_of_simulation`和`end_of_simulation`之间是一致的。

### `start_of_simulation`

`start_of_simulation`方法应被视为每个模拟的构造函数。该方法应初始化单个模拟期间所需的任何缓存。

`start_of_simulation`方法应具有以下签名：

```py
def start_of_simulation(self,
                        ledger,
                        emission_rate,
                        trading_calendar,
                        sessions,
                        benchmark_source):
    ... 
```

`ledger` 是 `Ledger` 的一个实例，它维护着模拟的状态。这可以用来查找算法的起始投资组合价值。

`emission_rate` 是一个表示度量报告的最小频率的字符串。`emission_rate` 将是 `minute` 或 `daily`。当 `emission_rate` 是 `daily` 时，`end_of_bar` 将根本不会被调用。

`trading_calendar` 是 `TradingCalendar` 的一个实例，它是模拟所使用的交易日历。

`sessions` 是一个 [`pandas.DatetimeIndex`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DatetimeIndex.html#pandas.DatetimeIndex "(in pandas v2.0.3)")，它持有模拟将要执行的会话标签，按排序顺序。

`benchmark_source` 是 `BenchmarkSource` 的一个实例，它是 `set_benchmark()` 指定的基准回报的接口。

### `end_of_simulation`

`end_of_simulation` 方法应具有以下签名：

```py
def end_of_simulation(self,
                      packet,
                      ledger,
                      trading_calendar,
                      sessions,
                      data_portal,
                      benchmark_source):
    ... 
```

`ledger` 是 `Ledger` 的一个实例，它维护着模拟的状态。这可以用来查找算法的最终投资组合价值。

`packet` 是一个字典，用于写入给定度量的模拟结束值。

`trading_calendar` 是 `TradingCalendar` 的一个实例，它是模拟所使用的交易日历。

`sessions` 是一个 [`pandas.DatetimeIndex`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DatetimeIndex.html#pandas.DatetimeIndex "(in pandas v2.0.3)")，它持有模拟已经执行的会话标签，按排序顺序。

`data_portal` 是 `DataPortal` 的一个实例，它是度量对定价数据的接口。

`benchmark_source` 是 `BenchmarkSource` 的一个实例，它是 `set_benchmark()` 指定的基准回报的接口。

### `start_of_session`

`start_of_session` 方法可能会看到与之前的 `end_of_session` 略有不同的 `ledger` 或 `data_portal` 视图，如果拥有的任何期货的价格在交易会话之间移动或发生资本变动。

`start_of_session` 方法应具有以下签名：

```py
def start_of_session(self,
                     ledger,
                     session_label,
                     data_portal):
    ... 
```

`ledger` 是 `Ledger` 的一个实例，它维护着模拟的状态。这可以用来查找算法的当前投资组合价值。

`session_label` 是一个 [`Timestamp`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html#pandas.Timestamp "(in pandas v2.0.3)")，它是即将运行的会话的标签。

`data_portal` 是 `DataPortal` 的一个实例，它是度量与定价数据接口。

### `end_of_session`

`end_of_session` 方法应具有以下签名：

```py
def end_of_session(self,
                   packet,
                   ledger,
                   session_label,
                   session_ix,
                   data_portal): 
```

`packet` 是一个用于写入会话结束值的字典。该字典包含两个子字典：`daily_perf` 和 `cumulative_perf`。在适用的情况下，`daily_perf` 应包含当前日的价值，而 `cumulative_perf` 应包含到当前时间为止的整个模拟的累积价值。

`ledger` 是 `Ledger` 的一个实例，它维护着模拟的状态。这可以用来查找算法的当前投资组合价值。

`session_label` 是一个 [`Timestamp`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html#pandas.Timestamp "(in pandas v2.0.3)")，它是刚刚完成的会话的标签。

`session_ix` 是一个 [`int`](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")，它是当前正在运行的交易会话的索引。这提供了通过 `ledger.daily_returns_array[:session_ix + 1]` 高效访问每日回报的方式。

`data_portal` 是 `DataPortal` 的一个实例，它是度量与定价数据接口。

### `end_of_bar`

注意

`end_of_bar` 仅在 `emission_mode` 为 `minute` 时调用。

`end_of_bar` 方法应具有以下签名：

```py
def end_of_bar(self,
               packet,
               ledger,
               dt,
               session_ix,
               data_portal): 
```

`packet` 是一个用于写入会话结束值的字典。该字典包含两个子字典：`minute_perf` 和 `cumulative_perf`。在适用的情况下，`minute_perf` 应包含当前部分日的价值，而 `cumulative_perf` 应包含到当前时间为止的整个模拟的累积价值。

`ledger` 是 `Ledger` 的一个实例，它维护着模拟的状态。这可以用来查找算法的当前投资组合价值。

`dt` 是一个 [`Timestamp`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html#pandas.Timestamp "(in pandas v2.0.3)")，它是刚刚完成的条形的标签。

`session_ix`是一个[`int`](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")，它是当前正在运行的交易会话的索引。这提供了通过`ledger.daily_returns_array[:session_ix + 1]`高效访问每日回报的方式。

`data_portal`是`DataPortal`的一个实例，它是度量对定价数据的接口。

## 定义新的度量集

用户可以使用`zipline.finance.metrics.register()`注册新的度量集。这可以用于装饰一个不带参数的函数，该函数返回一组新的度量对象实例。例如：

```py
from zipline.finance import metrics

@metrics.register('my-metrics-set')
def my_metrics_set():
    return {MyMetric(), MyOtherMetric(), ...} 
```

这可以嵌入用户的`extension.py`中。

将度量集定义为生成一组的函数，而不是仅仅是一组，是因为用户可能想要获取外部数据或资源来构建他们的度量。通过将此置于可调用对象后面，用户不需要在未使用度量集时获取资源。

## 度量集

Zipline 将风险和性能度量分组到称为“度量集”的集合中。单个度量集定义了在单个回测期间要跟踪的所有度量。度量集可以包含在不同时间尺度上报的度量。默认度量集将计算一系列度量，如算法回报、波动性、夏普比率和贝塔。

## 选择度量集

在运行模拟时，用户可以选择要报告的度量集。如何选择度量集取决于用于运行算法的接口。

### 命令行和 IPython 魔法

当通过命令行或 IPython 魔法接口运行时，可以通过传递`--metrics-set`参数来选择度量集。例如：

```py
$  zipline  run  algorithm.py  -s  2014-01-01  -e  2014-02-01  --metrics-set  my-metrics-set 
```

### `run_algorithm`

当通过`run_algorithm()`接口运行时，可以通过`metrics_set`参数传递度量集。这可以是已注册度量集的名称，也可以是一组度量对象。例如：

```py
run_algorithm(..., metrics_set='my-metrics-set')
run_algorithm(..., metrics_set={MyMetric(), MyOtherMetric(), ...}) 
```

### 命令行和 IPython 魔法

当通过命令行或 IPython 魔法接口运行时，可以通过传递`--metrics-set`参数来选择度量集。例如：

```py
$  zipline  run  algorithm.py  -s  2014-01-01  -e  2014-02-01  --metrics-set  my-metrics-set 
```

### `run_algorithm`

当通过`run_algorithm()`接口运行时，可以通过`metrics_set`参数传递度量集。这可以是已注册度量集的名称，也可以是一组度量对象。例如：

```py
run_algorithm(..., metrics_set='my-metrics-set')
run_algorithm(..., metrics_set={MyMetric(), MyOtherMetric(), ...}) 
```

## 不带度量运行

计算风险和性能指标不是免费的，它会增加回测的总运行时间。在积极开发算法时，通常有助于跳过这些计算以加快调试周期。要禁用所有指标的计算和报告，用户可以选择内置的指标集 `none`。例如：

```py
$  zipline  run  algorithm.py  -s  2014-01-01  -e  2014-02-01  --metrics-set  none 
```

## 定义新指标

指标是实现以下方法子集的任何对象：

+   `start_of_simulation`

+   `end_of_simulation`

+   `start_of_session`

+   `end_of_session`

+   `end_of_bar`

这些函数将在其名称指示的时间被调用，届时指标对象可以收集任何所需信息，并可选择报告计算值。如果某个指标在这些时间不需要进行任何处理，则可以省略对该方法的定义。

指标应该是可重用的，这意味着单个指标类实例应该能够用于多个回测。指标不需要同时支持多个模拟，这意味着内部缓存和数据在 `start_of_simulation` 和 `end_of_simulation` 之间是一致的。

### `start_of_simulation`

应将 `start_of_simulation` 方法视为每个模拟的构造函数。该方法应初始化单个模拟期间所需的任何缓存。

`start_of_simulation` 方法应具有以下签名：

```py
def start_of_simulation(self,
                        ledger,
                        emission_rate,
                        trading_calendar,
                        sessions,
                        benchmark_source):
    ... 
```

`ledger` 是 `Ledger` 的一个实例，它维护着模拟的状态。这可以用来查找算法的起始投资组合价值。

`emission_rate` 是一个字符串，表示指标应报告的最小频率。`emission_rate` 将是 `minute` 或 `daily`。当 `emission_rate` 为 `daily` 时，`end_of_bar` 将根本不会被调用。

`trading_calendar` 是 `TradingCalendar` 的一个实例，它是模拟所使用的交易日历。

`sessions` 是一个 [`pandas.DatetimeIndex`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DatetimeIndex.html#pandas.DatetimeIndex "(in pandas v2.0.3)")，它保存了模拟将执行的会话标签，按排序顺序排列。

`benchmark_source` 是 `BenchmarkSource` 的一个实例，它是指定基准的回报接口，由 `set_benchmark()` 指定。

### `end_of_simulation`

`end_of_simulation` 方法应具有以下签名：

```py
def end_of_simulation(self,
                      packet,
                      ledger,
                      trading_calendar,
                      sessions,
                      data_portal,
                      benchmark_source):
    ... 
```

`ledger` 是 `Ledger` 的一个实例，它维护着模拟的状态。这可以用来查找算法的最终投资组合价值。

`packet` 是一个字典，用于写入给定指标的模拟结束值。

`trading_calendar` 是 `TradingCalendar` 的一个实例，它是模拟使用的交易日历。

`sessions` 是一个 [`pandas.DatetimeIndex`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DatetimeIndex.html#pandas.DatetimeIndex "(in pandas v2.0.3)")，它保存了模拟已执行的交易时段标签，按排序顺序排列。

`data_portal` 是 `DataPortal` 的一个实例，它是度量标准对定价数据的接口。

`benchmark_source` 是 `BenchmarkSource` 的一个实例，它是返回由 `set_benchmark()` 指定的基准的接口。

### `start_of_session`

`start_of_session` 方法可能会看到与之前的 `end_of_session` 略有不同的 `ledger` 或 `data_portal` 视图，如果拥有的任何期货的价格在交易时段之间移动或发生资本变动。

方法 `start_of_session` 应该具有以下签名：

```py
def start_of_session(self,
                     ledger,
                     session_label,
                     data_portal):
    ... 
```

`ledger` 是 `Ledger` 的一个实例，它维护模拟的状态。这可以用来查找算法的当前投资组合价值。

`session_label` 是一个 [`Timestamp`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html#pandas.Timestamp "(in pandas v2.0.3)")，表示即将运行的交易时段的标签。

`data_portal` 是 `DataPortal` 的一个实例，它是度量标准对定价数据的接口。

### `end_of_session`

方法 `end_of_session` 应该具有以下签名：

```py
def end_of_session(self,
                   packet,
                   ledger,
                   session_label,
                   session_ix,
                   data_portal): 
```

`packet` 是一个用于写入交易时段结束值的字典。该字典包含两个子字典：`daily_perf` 和 `cumulative_perf`。在适用的情况下，`daily_perf` 应包含当天的值，而 `cumulative_perf` 应包含到当前时间为止的整个模拟的累积值。

`ledger` 是 `Ledger` 的一个实例，它维护模拟的状态。这可以用来查找算法的当前投资组合价值。

`session_label` 是一个 [`Timestamp`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html#pandas.Timestamp "(in pandas v2.0.3)")，表示刚刚完成的交易时段的标签。

`session_ix` 是一个 [`int`](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")，表示当前正在运行的交易时段的索引。提供这个索引是为了通过 `ledger.daily_returns_array[:session_ix + 1]` 高效访问每日回报。

`data_portal` 是 `DataPortal` 的一个实例，它是度量标准与定价数据接口的接口。

### `end_of_bar`

注意

`end_of_bar` 仅在 `emission_mode` 为 `minute` 时被调用。

`end_of_bar` 方法应具有以下签名：

```py
def end_of_bar(self,
               packet,
               ledger,
               dt,
               session_ix,
               data_portal): 
```

`packet` 是一个字典，用于写入会话结束时的值。该字典包含两个子字典：`minute_perf` 和 `cumulative_perf`。在适用的情况下，`minute_perf` 应包含当前部分日的值，而 `cumulative_perf` 应包含到当前时间为止的整个模拟的累积值。

`ledger` 是 `Ledger` 的一个实例，它维护模拟的状态。这可以用来查找算法当前的资产组合值。

`dt` 是一个 [`Timestamp`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html#pandas.Timestamp "(in pandas v2.0.3)")，它是刚刚完成的条形的标签。

`session_ix` 是一个 [`int`](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")，它是当前正在运行的交易会话的索引。这提供给允许通过 `ledger.daily_returns_array[:session_ix + 1]` 高效访问每日回报。

`data_portal` 是 `DataPortal` 的一个实例，它是度量标准与定价数据接口的接口。

### `start_of_simulation`

`start_of_simulation` 方法应被视为每个模拟的构造函数。该方法应初始化单个模拟期间所需的任何缓存。

`start_of_simulation` 方法应具有以下签名：

```py
def start_of_simulation(self,
                        ledger,
                        emission_rate,
                        trading_calendar,
                        sessions,
                        benchmark_source):
    ... 
```

`ledger` 是 `Ledger` 的一个实例，它维护模拟的状态。这可以用来查找算法起始的资产组合值。

`emission_rate` 是一个字符串，表示度量标准应报告的最小频率。`emission_rate` 将是 `minute` 或 `daily` 之一。当 `emission_rate` 为 `daily` 时，`end_of_bar` 根本不会被调用。

`trading_calendar` 是 `TradingCalendar` 的一个实例，它是模拟使用的交易日历。

`sessions` 是一个 [`pandas.DatetimeIndex`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DatetimeIndex.html#pandas.DatetimeIndex "(in pandas v2.0.3)")，它保存模拟将执行的会话标签，按排序顺序排列。

`benchmark_source` 是 `BenchmarkSource` 的一个实例，它是 `set_benchmark()` 指定的基准的回报接口。

### `end_of_simulation`

`end_of_simulation` 方法应该具有以下签名：

```py
def end_of_simulation(self,
                      packet,
                      ledger,
                      trading_calendar,
                      sessions,
                      data_portal,
                      benchmark_source):
    ... 
```

`ledger` 是 `Ledger` 的一个实例，它维护模拟的状态。这可以用来查找算法的最终投资组合值。

`packet` 是一个字典，用于将给定度量的模拟结束值写入。

`trading_calendar` 是 `TradingCalendar` 的一个实例，它是模拟使用的交易日历。

`sessions` 是一个 [`pandas.DatetimeIndex`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DatetimeIndex.html#pandas.DatetimeIndex "(in pandas v2.0.3)")，它保存了模拟执行的会话标签，按排序顺序排列。

`data_portal` 是 `DataPortal` 的一个实例，它是度量与定价数据接口的接口。

`benchmark_source` 是 `BenchmarkSource` 的一个实例，它是与 `set_benchmark()` 指定的基准的回报接口。

### `start_of_session`

`start_of_session` 方法可能会看到与之前的 `end_of_session` 略有不同的 `ledger` 或 `data_portal` 视图，如果任何拥有的期货的价格在交易会话之间移动或发生资本变动。

`start_of_session` 方法应该具有以下签名：

```py
def start_of_session(self,
                     ledger,
                     session_label,
                     data_portal):
    ... 
```

`ledger` 是 `Ledger` 的一个实例，它维护模拟的状态。这可以用来查找算法的当前投资组合值。

`session_label` 是一个 [`Timestamp`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html#pandas.Timestamp "(in pandas v2.0.3)")，它是即将运行的会话的标签。

`data_portal` 是 `DataPortal` 的一个实例，它是度量与定价数据接口的接口。

### `end_of_session`

`end_of_session` 方法应该具有以下签名：

```py
def end_of_session(self,
                   packet,
                   ledger,
                   session_label,
                   session_ix,
                   data_portal): 
```

`packet` 是一个字典，用于写入会话结束值。该字典包含两个子字典：`daily_perf` 和 `cumulative_perf`。在适用的情况下，`daily_perf` 应包含当天的当前值，而 `cumulative_perf` 应包含截至当前时间的整个模拟的累积值。

`ledger` 是 `Ledger` 的一个实例，它维护模拟的状态。这可以用来查找算法的当前投资组合值。

`session_label` 是一个 [`Timestamp`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html#pandas.Timestamp "(in pandas v2.0.3)")，它是刚刚完成的会话的标签。

`session_ix` 是一个 [`int`](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")，它是当前正在运行的交易会话的索引。提供这个参数是为了通过 `ledger.daily_returns_array[:session_ix + 1]` 高效访问每日回报。

`data_portal` 是 `DataPortal` 的一个实例，它是度量与定价数据接口。

### `end_of_bar`

注意

`end_of_bar` 仅在 `emission_mode` 为 `minute` 时被调用。

`end_of_bar` 方法应该具有以下签名：

```py
def end_of_bar(self,
               packet,
               ledger,
               dt,
               session_ix,
               data_portal): 
```

`packet` 是一个字典，用于写入会话结束时的值。该字典包含两个子字典：`minute_perf` 和 `cumulative_perf`。在适用的情况下，`minute_perf` 应包含当前部分日的值，而 `cumulative_perf` 应包含到当前时间为止的整个模拟的累积值。

`ledger` 是 `Ledger` 的一个实例，它维护着模拟的状态。这可以用来查找算法当前的资产组合价值。

`dt` 是一个 [`Timestamp`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html#pandas.Timestamp "(in pandas v2.0.3)")，它是刚刚完成的条形的标签。

`session_ix` 是一个 [`int`](https://docs.python.org/3/library/functions.html#int "(in Python v3.11)")，它是当前正在运行的交易会话的索引。提供这个参数是为了通过 `ledger.daily_returns_array[:session_ix + 1]` 高效访问每日回报。

`data_portal` 是 `DataPortal` 的一个实例，它是度量与定价数据接口。

## 定义新的度量集

用户可以使用 `zipline.finance.metrics.register()` 来注册一个新的度量集。这可以用来装饰一个不接受参数并返回一组新的度量对象实例的函数。例如：

```py
from zipline.finance import metrics

@metrics.register('my-metrics-set')
def my_metrics_set():
    return {MyMetric(), MyOtherMetric(), ...} 
```

这可以嵌入到用户的 `extension.py` 中。

将度量集定义为生成一组度量的函数，而不是直接定义一组度量，是因为用户可能想要获取外部数据或资源来构建他们的度量。通过将这个过程放在一个可调用的对象后面，用户不需要在度量集未被使用时获取资源。
