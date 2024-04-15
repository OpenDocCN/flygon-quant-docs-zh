# Cerebro

> 原文：[`www.backtrader.com/docu/cerebro/`](https://www.backtrader.com/docu/cerebro/)

这个类是`backtrader`的基石，因为它作为以下方面的中心点：

1.  收集所有输入（*数据源*）、行动者（*策略*）、旁观者（*观察员*）、评论者（*分析器*）和文档编制者（*写作者*），确保节目随时进行。

1.  执行回测/或实时数据提供/交易

1.  返回结果

1.  提供对绘图设施的访问

## 收集输入

1.  首先创建一个`cerebro`：

    ```py
    `cerebro = bt.Cerebro(**kwargs)` 
    ```

    支持一些`**kwargs`来控制执行，参见参考文献（这些参数后来也可以应用于`run`方法）

1.  添加*数据源*

    最常见的模式是`cerebro.adddata(data)`，其中`data`已经实例化为*数据源*。例如：

    ```py
    `data = bt.BacktraderCSVData(dataname='mypath.days', timeframe=bt.TimeFrame.Days)
    cerebro.adddata(data)` 
    ```

    *重新采样*和*回放*数据是可能的，遵循相同的模式：

    ```py
    `data = bt.BacktraderCSVData(dataname='mypath.min', timeframe=bt.TimeFrame.Minutes)
    cerebro.resampledata(data, timeframe=bt.TimeFrame.Days)` 
    ```

    或：

    ```py
    `data = bt.BacktraderCSVData(dataname='mypath.min', timeframe=bt.TimeFrame.Minutes)
    cerebro.replaydatadata(data, timeframe=bt.TimeFrame.Days)` 
    ```

    系统可以接受任意数量的数据源，包括混合常规数据与重新采样和/或重放数据。当然，其中一些组合肯定毫无意义，因此在能够组合数据时会应用一些限制：*时间对齐*。请参阅数据-多时间框架、数据重新采样-重新采样和数据-重播部分。

1.  添加`Strategies`

    与已经是类实例的`数据源`不同，`cerebro`直接接受`Strategy`类和传递给它的参数。背后的原理是：*在优化场景中，该类将被实例化多次并传递不同的参数*

    即使没有运行*优化*，该模式仍然适用：

    ```py
    `cerebro.addstrategy(MyStrategy, myparam1=value1, myparam2=value2)` 
    ```

    当*优化*参数时，必须添加为可迭代对象。详细说明请参见*优化*部分。基本模式：

    ```py
    `cerebro.optstrategy(MyStrategy, myparam1=range(10, 20))` 
    ```

    运行`MyStrategy` 10 次，其中`myparam1`的值从 10 到 19（请记住，Python 中的范围是半开放的，`20`不会被包含）

1.  其他元素

    还有一些其他元素可以添加以增强回测体验。查看相应的部分以了解详情。方法包括：

    +   `addwriter`

    +   `addanalyzer`

    +   `addobserver`（或`addobservermulti`）

1.  更改经纪人

    Cerebro 将在`backtrader`中使用默认经纪人，但可以被覆盖：

    ```py
    `broker = MyBroker()
    cerebro.broker = broker  # property using getbroker/setbroker methods` 
    ```

1.  接收通知

    如果*数据源*和/或*经纪人*发送通知（或创建它们的*存储*提供程序），它们将通过`Cerebro.notify_store`方法接收。有三（3）种处理这些通知的方法

    +   通过`addnotifycallback(callback)`调用向`cerebro`实例添加*回调*。回调函数必须支持此签名：

    ```py
    `callback(msg, *args, **kwargs)` 
    ```

    实际的`msg`、`*args`和`**kwargs`接收的内容是实现定义的（完全取决于*数据/经纪人/存储*），但一般可以期望它们是*可打印*的，以便接收和实验。

    +   在添加到`cerebro`实例的`Strategy`子类中覆盖`notify_store`方法。

    签名：`notify_store(self, msg, *args, **kwargs)`

    +   子类`Cerebro`并覆盖`notify_store`（与`Strategy`中的签名相同）

    这应该是最不受欢迎的方法

## 执行回测

有一个单一的方法可以做到这一点，但它支持几个选项（也可以在实例化时指定），以决定如何运行：

```py
result = cerebro.run(**kwargs)
```

请参阅下面的参考文献，了解可用的参数。

### 标准观察者

`cerebro`（除非另有说明）会自动实例化*三个*标准观察者

+   一个*Broker*观察者，用于跟踪`cash`和`value`（投资组合）

+   一个*Trades*观察者，应显示每次交易的有效性如何

+   一个*Buy/Sell*观察者，应记录操作何时执行

如果希望更干净的绘图，只需使用`stdstats=False`禁用它们

## 返回结果

`cerebro`在回测期间返回所创建的策略实例。这允许分析它们的操作，因为可以访问策略中的所有元素：

```py
result = cerebro.run(**kwargs)
```

由`run`返回的`result`的格式取决于是否使用了*优化*（使用`optstrategy`添加了一个*策略*）：

+   所有使用`addstrategy`添加的策略

    `result`将是在回测期间运行的实例的`list`

+   使用`optstrategy`添加了 1 个或多个策略

    `result`将是`list`的`list`。每个内部列表将包含每次优化运行后的策略

注意

*优化*的默认行为已更改为仅返回系统中存在的*分析器*，以减轻跨计算机核心的消息传递负担。

如果希望返回完整的策略集，请将参数`optreturn`设置为`False`

## 提供对绘图功能的访问

如果安装了`matplotlib`，则可以绘制策略图。通常的模式是：

```py
cerebro.plot()
```

请参阅下文的参考文献和绘图部分

## 回测逻辑

事物流程的简要概述：

1.  传递任何存储通知

1.  要求数据源提供下一组 ticks/bars

    **版本更改：**版本 1.9.0.99 中已更改：*新行为*

    数据源通过查看下一个将提供的*datetime*来同步。在新周期中尚未交易的数据源仍提供旧数据点，而具有新数据的数据源则提供此数据（以及指标的计算）

    *旧行为*（在*Cerebro*中使用`oldsync=True`时保留）

    第 1 个插入系统的数据是`datamaster`，系统将等待它提供一个 tick

    其他数据源或多或少是`datamaster`的从属，且：

    ```py
     `* If the next tick to deliver is newer (datetime-wise) than the one
       delivered by the `datamaster` it will not be delivered

     * May return without delivering a new tick for a number of reasons` 
    ```

    逻辑被设计为轻松同步多个数据源和具有不同时间框架的数据源

1.  通知策略有关已排队的经纪人订单、交易和现金/价值的通知

1.  告诉经纪人接受排队的订单，并使用新数据执行待处理的订单

1.  调用策略的 `next` 方法以使策略评估新数据（并可能发出在经纪人中排队的订单）

    根据阶段，策略/指标的最小周期要求可能在满足之前是 `prenext` 还是 `nextstart`。

    在内部，策略还会触发 `observers`、`indicators`、`analyzers` 和其他活动元素

1.  告诉任何 `writers` 将数据写入其目标

注意：

注意

在上述第 `1` 步中，当 *数据源* 传递新的柱集时，这些柱是 **关闭** 的。 这意味着数据已经发生了。

因此，在第 `4` 步中策略发出的 *订单* 无法使用第 `1` 步的数据 *执行*。

这意味着订单将使用 `x + 1` 的概念执行。 其中 `x` 是订单执行的柱时刻，而 `x + 1` 是下一个，它是可能的订单执行的最早时刻

## 参考

#### 类 backtrader.Cerebro()

参数：

+   `preload`（默认：`True`）

是否预加载传递给 cerebro 的不同 `data feeds` 用于策略

+   `runonce`（默认：`True`）

在向量化模式下运行 `Indicators` 以加速整个系统。 策略和观察器将始终基于事件运行。

+   `live`（默认：`False`）

如果没有数据通过数据的 `islive` 方法报告自身为 *live*，但最终用户仍希望以 `live` 模式运行，可以将此参数设置为 true

这将同时停用 `preload` 和 `runonce`。 对内存节省方案没有影响。

在向量化模式下运行 `Indicators` 以加速整个系统。 策略和观察器将始终基于事件运行。

+   `maxcpus`（默认：无->所有可用核心）

    同时使用多少核心进行优化

+   `stdstats`（默认：`True`）

如果为 True，默认观察者将被添加：经纪人（现金和价值）、交易和买卖

+   `oldbuysell`（默认：`False`）

如果 `stdstats` 为 `True` 并且观察者自动添加，则此开关控制 `BuySell` 观察者的主要行为

+   `False`：使用现代行为，在低/高价格的下方/上方分别绘制买入/卖出信号，以避免图表混乱

+   `True`：使用已弃用的行为，在给定时刻的订单执行的平均价格绘制买入/卖出信号。 这当然会在 OHLC 柱或 Cloe 柱上方绘制，难以识别图的内容。

+   `oldtrades`（默认：`False`）

如果 `stdstats` 为 `True` 并且观察者自动添加，则此开关控制 `Trades` 观察者的主要行为

+   `False`：使用现代行为，其中所有数据的交易都用不同的标记绘制

+   `True`：使用旧的交易观察器，它使用相同的标记绘制交易，仅在它们是正数还是负数时有所区别。

+   `exactbars`（默认：`False`）

默认情况下，每个值都会存储在内存中的一行中。

可能的值：

```py
* `True` or `1`: all “lines” objects reduce memory usage to the
  automatically calculated minimum period.

  If a Simple Moving Average has a period of 30, the underlying data
  will have always a running buffer of 30 bars to allow the
  calculation of the Simple Moving Average

  * This setting will deactivate `preload` and `runonce`

  * Using this setting also deactivates **plotting**

* `-1`: datafreeds and indicators/operations at strategy level will
  keep all data in memory.

  For example: a `RSI` internally uses the indicator `UpDay` to
  make calculations. This subindicator will not keep all data in
  memory

  * This allows to keep `plotting` and `preloading` active.

  * `runonce` will be deactivated

* `-2`: data feeds and indicators kept as attributes of the
  strategy will keep all points in memory.

  For example: a `RSI` internally uses the indicator `UpDay` to
  make calculations. This subindicator will not keep all data in
  memory

  If in the `__init__` something like
  `a = self.data.close - self.data.high` is defined, then `a`
  will not keep all data in memory

  * This allows to keep `plotting` and `preloading` active.

  * `runonce` will be deactivated
```

+   `objcache`（默认：`False`）

实验选项，用于实现线条对象的缓存并减少它们的数量。从 UltimateOscillator 示例：

```py
bp = self.data.close - TrueLow(self.data)
tr = TrueRange(self.data)  # -> creates another TrueLow(self.data)
```

如果这是`True`，则`TrueRange`内的第 2 个`TrueLow(self.data)`将与`bp`计算中的签名匹配。它将被重用。

边缘情况可能会发生，其中这会使线条对象超出其最小周期并且导致故障，因此已禁用。

+   `writer`（默认：`False`）

如果设置为`True`，将创建一个默认的 WriterFile，它将打印到 stdout。它将被添加到策略中（除了用户代码添加的任何其他写入器）

+   `tradehistory`（默认：`False`）

如果设置为`True`，它将激活每个策略的每次交易的更新事件记录。这也可以通过策略方法`set_tradehistory`逐个策略地完成。

+   `optdatas`（默认：`True`）

如果为`True`且正在优化（并且系统可以`preload`并使用`runonce`，数据预加载将仅在主进程中执行一次，以节省时间和资源。

测试显示，从在`83`秒的样本执行中移动到`66`秒的执行速度提高了约`20%`。

+   `optreturn`（默认：`True`）

如果为`True`，则优化结果将不是完整的`Strategy`对象（以及所有*datas*、*indicators*、*observers*...），而是具有以下属性的对象（与`Strategy`中相同）：

```py
* `params` (or `p`) the strategy had for the execution

* `analyzers` the strategy has executed
```

在大多数情况下，只有*分析器*和与之相关的*参数*是评估策略性能所需的内容。如果需要对生成的值（例如*指标*）进行详细分析，则关闭此选项。

测试显示执行时间提高了`13% - 15%`。与`optdatas`结合使用，总增益增加到了优化运行的`32%`的总体加速。

+   `oldsync`（默认：`False`）

从版本 1.9.0.99 开始，多个数据的同步（相同或不同的时间框架）已更改为允许长度不同的数据。

如果希望保留旧的行为，并将 data0 设置为系统的主要数据，则将此参数设置为 true。

+   `tz`（默认：`None`）

为策略添加全局时区。参数`tz`可以是

```py
* `None`: in this case the datetime displayed by strategies will be
  in UTC, which has been always the standard behavior

* `pytz` instance. It will be used as such to convert UTC times to
  the chosen timezone

* `string`. Instantiating a `pytz` instance will be attempted.

* `integer`. Use, for the strategy, the same timezone as the
  corresponding `data` in the `self.datas` iterable (`0` would
  use the timezone from `data0`)
```

+   `cheat_on_open`（默认：`False`）

在调用策略的`next_open`方法之前将会调用它。这发生在`next`之前，也发生在经纪人有机会评估订单之前。指标尚未重新计算。这允许发出一个订单，该订单考虑了前一天的指标，但使用`open`价格进行股份计算。

对于`cheat_on_open`订单执行，还需要调用`cerebro.broker.set_coo(True)`或使用`BackBroker(coo=True)`实例化一个经纪人（其中*coo*代表 cheat-on-open），或者将`broker_coo`参数设置为`True`。除非在下面禁用，否则 Cerebro 将自动执行此操作。

+   `broker_coo`（默认：`True`）

这将自动调用经纪人的`set_coo`方法，并使用`True`来激活`cheat_on_open`执行。仅当`cheat_on_open`也为`True`时才执行。

+   `quicknotify`（默认值：`False`）

经纪人通知将在*下一个*价格传递之前立即传递。 对于回测，这没有任何影响，但对于实时经纪人，通知可能会在交付柱形图之前很久就发生了。 当设置为 `True` 时，通知将尽快传递（请参阅实时数据源中的 `qcheck`）

设置为 `False` 以实现兼容性。 可能会更改为 `True`

#### addstorecb(callback)

添加一个回调来获取将由 notify_store 方法处理的消息

回调函数的签名必须支持以下内容：

+   `callback(msg, *args, **kwargs)`

实际接收到的 `msg`、`*args` 和 `**kwargs` 取决于实现（完全依赖于 *数据/经纪人/存储*），但通常应该期望它们是*可打印的*，以便接收和实验。

#### notify_store(msg, *args, **kwargs)

在 cerebro 中接收存储通知

此方法可以在 `Cerebro` 的子类中重写

如果 `name` 不为 None，则将其放入 `data._name` 中，该参数用于装饰/绘图目的。

#### adddatacb(callback)

adddata(data, name=None)

向获取将由 notify_data 方法处理的消息添加一个回调

+   callback(data, status, *args, **kwargs)

实际接收到的 `*args` 和 `**kwargs` 取决于实现（完全依赖于 *数据/经纪人/存储*），但通常应该期望它们是*可打印的*，以便接收和实验。

#### 实际接收到的 `msg`、`*args` 和 `**kwargs` 取决于实现（完全依赖于 *数据/经纪人/存储*），但通常应该期望它们是*可打印的*，以便接收和实验。

在 cerebro 中接收数据通知

此方法可以在 `Cerebro` 的子类中重写

回调函数的签名必须支持以下内容：

#### notify_data(data, status, *args, **kwargs)

向组合中添加一个 `Data Feed` 实例。

添加一个 `callback` 来获取将由 notify_store 方法处理的消息

#### resampledata(dataname, name=None, **kwargs)

向系统添加一个将由系统重新采样的 `Data Feed`

如果 `name` 不为 None，则将其放入 `data._name` 中，该参数用于装饰/绘图目的。

任何其他支持重新采样过滤器的 kwargs，如 `timeframe`、`compression`、`todate`，都将被透明地传递

#### replaydata(dataname, name=None, **kwargs)

向系统添加一个将由系统重播的 `Data Feed`

如果 `name` 不为 None，则将其放入 `data._name` 中，该参数用于装饰/绘图目的。

任何其他支持回放过滤器的 kwargs，如 `timeframe`、`compression`、`todate`，都将被透明地传递

#### chaindata(*args, **kwargs)

将几个数据源串联成一个

如果 `name` 被传递为命名参数且不为 None，则将其放入 `data._name` 中，该参数用于装饰/绘图目的。

如果为 `None`，则将使用第 1 个数据的名称

#### rolloverdata(*args, **kwargs)

将几个数据源链接到一个数据源中

如果将 `name` 作为命名参数传递，并且不为 None，则将放入 `data._name` 中，用于装饰/绘图目的。

如果为 `None`，则将使用第 1 个数据的名称

任何其他的 kwargs 将传递给 RollOver 类

#### addstrategy(strategy, *args, **kwargs)

将 `Strategy` 类添加到混合中，以进行单次运行。实例化将在`运行`时发生。

args 和 kwargs 将像在实例化期间一样传递给策略。

返回用于引用其他对象（如调整器）的添加索引

#### optstrategy(strategy, *args, **kwargs)

将 `Strategy` 类添加到混合中以进行优化。实例化将在`运行`时发生。

args 和 kwargs 必须是可迭代的，其中包含要检查的值。

示例：如果一个策略接受一个参数 `period`，为了优化目的，`optstrategy` 的调用如下所示：

+   cerebro.optstrategy(MyStrategy, period=(15, 25))

这将为值 15 和 25 执行优化。而

+   cerebro.optstrategy(MyStrategy, period=range(15, 25))

将使用 `period` 值 15 -> 25（因为 Python 中的范围是半开放的，所以不包括 25）执行 MyStrategy

如果传递了一个参数，但不应该进行优化，则调用如下：

+   cerebro.optstrategy(MyStrategy, period=(15,))

请注意，`period` 仍然传递为可迭代对象 … 只有 1 个元素

`backtrader` 将尝试识别诸如以下情况：

+   cerebro.optstrategy(MyStrategy, period=15)

并在可能的情况下创建内部伪可迭代对象

#### optcallback(cb)

将 *callback* 添加到将在每个策略运行时调用的回调列表中进行优化

签名：cb(strategy)

#### addindicator(indcls, *args, **kwargs)

将 `Indicator` 类添加到混合中。实例化将在传递的策略的`运行`时完成

#### addobserver(obscls, *args, **kwargs)

将 `Observer` 类添加到混合中。实例化将在`运行`时完成

#### addobservermulti(obscls, *args, **kwargs)

将 `Observer` 类添加到混合中。实例化将在`运行`时完成

它将每个“数据”系统中添加一次。一个用例是观察单个数据的买入/卖出观察者。

反例是 CashValue，它观察系统范围的值

#### addanalyzer(ancls, *args, **kwargs)

将 `Analyzer` 类添加到混合中。实例化将在`运行`时完成

#### addwriter(wrtcls, *args, **kwargs)

将 `Writer` 类添加到混合中。实例化将在`运行`时在 cerebro 中完成

#### run(**kwargs)

执行回测的核心方法。传递给它的任何 `kwargs` 都会影响实例化时 `Cerebro` 的标准参数的值。

如果 `cerebro` 没有数据，则该方法将立即退出。

它具有不同的返回值：

+   对于无优化：包含使用 `addstrategy` 添加的策略类实例的列表

+   用于优化：包含使用`addstrategy`添加的 Strategy 类实例的列表的列表

#### runstop()

如果从策略内部或其他任何地方调用，包括其他线程，执行将尽快停止。

#### setbroker(broker)

为此策略设置特定的`broker`实例，替换从 cerebro 继承的实例。

#### getbroker()

返回经纪人实例。

这也可以作为名为`broker`的`property`使用

#### 绘图（plotter=None，numfigs=1，iplot=True，start=None，end=None，width=16，height=9，dpi=300，tight=True，use=None，**kwargs）

在 cerebro 内部绘制策略

如果`plotter`为 None，则会创建一个默认的`Plot`实例，并在实例化期间将`kwargs`传递给它。

`numfigs`将绘图分成指定数量的图表，如果需要，可以减少图表密度

`iplot`：如果为`True`并在`notebook`中运行，则图表将内联显示

`use`：将其设置为所需 matplotlib 后端的名称。它将优先于`iplot`

`start`：策略或`datetime.date`、`datetime.datetime`实例的日期时间线数组的索引，指示绘图的开始

`end`：策略的日期时间线数组的索引或`datetime.date`、`datetime.datetime`实例，指示绘图的结束

`width`：保存图形的英寸

`height`：保存图形的英寸

`dpi`：保存图形的每英寸点数的质量

`tight`：仅保存实际内容而不是图形的框架

#### 添加 sizer(sizercls，*args，**kwargs）

添加一个`Sizer`类（和 args），它是添加到 cerebro 的任何策略的默认 sizer

#### addsizer_byidx(idx，sizercls，*args，**kwargs）

按 idx 添加一个`Sizer`类。此 idx 是与`addstrategy`返回的兼容引用。只有由`idx`引用的策略将接收此大小

#### add_signal(sigtype，sigcls，*sigargs，**sigkwargs）

向系统添加一个信号，稍后将其添加到`SignalStrategy`中。

#### signal_concurrent(onoff)

如果向系统添加信号并且将`concurrent`值设置为 True，则将允许并发订单

#### signal_accumulate(onoff)

如果向系统添加信号并且将`accumulate`值设置为 True，则在已经进入市场时进入市场，将允许增加头寸

#### signal_strategy(stratcls，*args，**kwargs）

添加一个可以接受信号的 SignalStrategy 子类

#### addcalendar(cal)

向系统添加全局交易日历。个别数据源可能具有覆盖全局日历的单独日历

`cal`可以是`TradingCalendar`的实例、字符串或`pandas_market_calendars`的实例。字符串将被实例化为`PandasMarketCalendar`（需要系统中安装`pandas_market_calendar`模块。

如果传递的是 TradingCalendarBase 的子类（而不是实例），它将被实例化

#### addtz(tz)

这也可以通过参数`tz`完成

为策略添加全局时区。参数`tz`可以是

+   `None`：在这种情况下，策略显示的日期时间将为 UTC，这一直是标准行为

+   `pytz`实例。将按此使用它将 UTC 时间转换为所选时区

+   `string`。将尝试实例化一个`pytz`实例。

+   `integer`。对于策略，使用与`self.datas`可迭代对象中相应`data`相同的时区（`0`将使用来自`data0`的时区）

#### add_timer(when, offset=datetime.timedelta(0), repeat=datetime.timedelta(0), weekdays=[], weekcarry=False, monthdays=[], monthcarry=True, allow=None, tzdata=None, strats=False, cheat=False, *args, **kwargs)

安排定时器以调用`notify_timer`

+   **参数**

    **when** (*-*) – 可以是

    +   `datetime.time`实例（见下文`tzdata`）

    +   `bt.timer.SESSION_START`表示会话开始

    +   `bt.timer.SESSION_END`表示会话结束

    +   必须是`datetime.timedelta`实例的`offset`

    用于偏移值`when`。与`SESSION_START`和`SESSION_END`结合使用时，它具有有意义的用途，用于指示诸如在会话开始后`15 分钟`调用定时器之类的事情。

    +   必须是`datetime.timedelta`实例的`repeat`

        指示如果在第 1 次调用后，后续调用将在同一会话中按计划的`repeat`增量中安排

        一旦定时器超过会话结束，它将被重置为`when`的原始值

    +   `weekdays`：一个**排序**的可迭代对象，其中的整数表示可以实际调用定时器的日期（ISO 代码，星期一为 1，星期日为 7）

        如果未指定，定时器将在所有日期上都处于活动状态。

    +   `weekcarry`（默认值：`False`）。如果为`True`且周几未见（例如：交易假期），则定时器将在下一天执行（即使在新的一周中）

    +   `monthdays`：一个**排序**的可迭代对象，其中的整数表示定时器必须执行的月份中的哪一天。例如，每个月的第*15*天始终执行

        如果未指定，定时器将在所有日期上都处于活动状态

    +   `monthcarry`（默认值：`True`）。如果当天没有看到（周末、交易假期），则定时器将在下一个可用日期执行。

    +   `allow`（默认值：`None`）。一个回调函数，接收一个`datetime.date`实例，并返回`True`如果该日期允许定时器，否则返回`False`

    +   `tzdata`可以是`None`（默认值），一个`pytz`实例或一个`data feed`实例。

        `None`：`when`按面值解释（即使它不是 UTC，也会处理它）

        `pytz`实例：`when`将被解释为时区实例指定的本地时间。

        `data feed`实例：`when`将被解释为数据源实例的`tz`参数指定的本地时间。

        注意

        如果`when`是`SESSION_START`或`SESSION_END`且`tzdata`为`None`，则系统中的第 1 个*数据源*（也称为`self.data0`）将用作查找会话时间的参考。

    +   `strats`（默认值：`False`）还会调用策略的`notify_timer`

    +   `cheat`（默认为 `False`）如果为 `True`，则会在经纪人有机会评估订单之前调用计时器。例如，在交易会话开始之前，可以根据开盘价下订单。

    +   `*args`: 任何额外的参数都将传递给 `notify_timer`。

    +   `**kwargs`: 任何额外的关键字参数都将传递给 `notify_timer`。

返回值：

+   创建的计时器

#### notify_timer(timer, when, *args, **kwargs)

接收计时器通知，其中 `timer` 是由 `add_timer` 返回的计时器，`when` 是调用时间。`args` 和 `kwargs` 是传递给 `add_timer` 的任何额外参数。

实际的 `when` 时间可能会晚一些，但系统可能无法在之前调用计时器。该值是计时器值，而不是系统时间。

#### add_order_history(orders, notify=True)

将订单历史记录添加到经纪人中，以供性能评估直接执行

+   `orders`: 是一个可迭代对象（例如列表、元组、迭代器、生成器），其中每个元素也将是一个具有以下子元素的可迭代对象（有 2 种格式）

    `[datetime, size, price]` 或 `[datetime, size, price, data]`

    注意

    必须按照日期时间升序排序（或生成排序的元素）。

    其中：

    +   `datetime` 是一个 python `date/datetime` 实例，或者是一个格式为 YYYY-MM-DD[THH:MM:SS[.us]] 的字符串，方括号中的元素是可选的。

    +   `size` 是一个整数（买入为正，卖出为负）

    +   `price` 是一个浮点数/整数

    +   如果存在 `data`，则可以采用以下任何值

        +   *None* - 将使用第 1 个数据源作为目标。

        +   *integer* - 将使用该索引对应的数据（在**Cerebro**中的插入顺序）。

        +   *string* - 该名称的数据，例如通过 `cerebro.addata(data, name=value)` 分配，将作为目标。

+   `notify`（默认值：*True*）

    如果设为 `True`，则会通知系统中插入的第 1 个策略，其会根据每个订单中的信息创建人工订单。

注意

隐含在描述中的是需要添加一个数据源，该数据源是订单的目标。例如，分析器需要追踪回报率。
