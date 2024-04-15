# 操作平台

> 原文：[`www.backtrader.com/docu/operating/`](https://www.backtrader.com/docu/operating/)

## 线迭代器

为了参与操作，平台使用线迭代器的概念。它们松散地模仿了 Python 的迭代器，但实际上与它们毫不相关。

策略和指标都是线迭代器。

线迭代器的概念试图描述以下内容：

+   一条线迭代器踢动从属线迭代器，告诉它们迭代

+   然后，线迭代器遍历其自己声明的命名行并设置值

迭代的关键，就像普通的 Python 迭代器一样，是：

+   `next` 方法

    它将被每次迭代调用。 *线迭代器*具有的并作为逻辑/计算基础的`datas`数组已经被平台移动到下一个索引（除了数据重播）

    在满足线迭代器的**最小周期**时调用。稍后将详细介绍。

但因为它们不是常规迭代器，所以还存在两种额外的方法：

+   `prenext`

    在满足**最小周期**的条件之前调用**线迭代器**。

+   `nextstart`

    当满足**最小周期**的条件时**仅调用一次**线迭代器。

    默认行为是将调用转发给`next`，但如果需要的话当然可以重写。

### *指标*的额外方法

为了加快操作速度，指标支持一种称为 runonce 的批处理操作模式。这不是严格必需的（一个`next`方法就足够了），但它极大地减少了时间。

runonce 方法规则使得与索引 0 的点的获取/设置无效，并依赖于直接访问保存数据的底层数组，并为每个状态传递正确的索引。

定义的方法遵循 next 系列的命名：

+   `once(self, start, end)`

    在满足最小周期的条件时调用。必须在从内部数组的起始位置为零开始的 start 和 end 之间处理内部数组

+   `preonce(self, start, end)`

    在满足最小周期之前调用。

+   `oncestart(self, start, end)`

    当满足最小周期的条件时**仅调用一次**。

    默认行为是将调用转发给`once`，但如果需要的话当然可以重写。

### 最小周期

一张图值千言，而在这种情况下可能还有一个例子。一个简单移动平均能够解释它：

```py
`class SimpleMovingAverage(Indicator):
    lines = ('sma',)
    params = dict(period=20)

    def __init__(self):
        ...  # Not relevant for the explanation

    def prenext(self):
        print('prenext:: current period:', len(self))

    def nextstart(self):
        print('nextstart:: current period:', len(self))
        # emulate default behavior ... call next
        self.next()

    def next(self):
        print('next:: current period:', len(self))` 
```

实例化可能如下所示：

```py
`sma = btind.SimpleMovingAverage(self.data, period=25)` 
```

简要解释：

+   假设传递给移动平均的数据是标准数据源，默认周期为`1`，即数据源生成一个没有初始延迟的条形图。

+   然后，**“period=25”**实例化的移动平均将按以下方式调用其方法：

    +   `prenext` 24 次

    +   `nextstart` 1 次（依次调用`next`）

    +   `next` 再次直到*数据源*耗尽

让我们来看一个致命的指标：*另一个简单移动平均值*的*a SimpleMovingAverage*。实例化可能如下所示：

```py
`sma1 = btind.SimpleMovingAverage(self.data, period=25)

sma2 = btind.SimpleMovingAverage(sma1, period=20)` 
```

现在发生的情况是：

+   对于`sma1`也是如上所述

+   `sma2`正在接收一个**数据源**，其*最小周期*为 25，这是我们的`sma1`，因此

+   `sma2`方法的调用方式如下：

    +   `prenext`首次调用 25 + 18 次，总共 43 次

    +   25 次让`sma1`产生其第 1 个合理值。

    +   18 次累积额外的`sma1`值

    +   总共 19 个值（在 25 次调用后再加 1 次，然后再加 18 次）

    +   `nextstart`然后调用 1 次（轮流调用`next`）

    +   `next`另外调用 n 次，直到*数据源*耗尽

当系统已经处理了 44 个柱状时，平台会调用`next`。

*最小周期*已自动调整为传入数据。

策略和指标遵循这种行为：

+   只有当自动计算的最小周期已达到时，才会调用`next`（除了对`nextstart`的初始钩子调用）

注意。

相同的规则适用于**runonce**批量操作模式的`preonce`、`oncestart`和`once`

注意。

*最小周期*行为可以被操纵，尽管不建议这样做。如果希望使用，在策略或指标中使用`setminperiod(minperiod)`方法

## 已启动并运行。

启动和运行至少涉及 3 个*Lines*对象：

+   一个数据源

+   一个策略（实际上是从策略派生的类）

+   一个 Cerebro（西班牙语中的“大脑”）。

## 数据源

这些对象显然提供将通过应用计算（直接和/或带有指标）进行回测的数据。

该平台提供了几个数据源：

+   几种 CSV 格式和一个通用的 CSV 阅读器

+   雅虎在线获取器

+   支持接收*Pandas DataFrames*和*blaze*对象

+   与*Interacive Brokers*、*Visual Chart*和*Oanda*一起的实时数据源。

该平台不对数据源的内容（如时间段和压缩）做任何假设。这些值，连同名称，可以供信息用途和高级操作（例如数据源重采样，将例如 5 分钟数据源转换为每日数据源）。

设置 Yahoo Finance 数据源的示例：

```py
`import backtrader as bt
import backtrader.feeds as btfeeds

...

datapath = 'path/to/your/yahoo/data.csv'

data = btfeeds.YahooFinanceCSVData(
    dataname=datapath,
    reversed=True)` 
```

显示了雅虎的可选`reversed`参数，因为直接从雅虎下载的 CSV 文件从最新日期开始，而不是从最旧日期开始。

如果您的数据跨越了大的时间范围，则实际加载的数据可以限制如下：

```py
`data = btfeeds.YahooFinanceCSVData(
    dataname=datapath,
    reversed=True
    fromdate=datetime.datetime(2014, 1, 1),
    todate=datetime.datetime(2014, 12, 31))` 
```

如果存在，*fromdate*和*todate*都将被包含在数据源中。

如已提及的时间段、压缩和名称可添加：

```py
`data = btfeeds.YahooFinanceCSVData(
    dataname=datapath,
    reversed=True
    fromdate=datetime.datetime(2014, 1, 1),
    todate=datetime.datetime(2014, 12, 31)
    timeframe=bt.TimeFrame.Days,
    compression=1,
    name='Yahoo'
   )` 
```

如果数据已绘制，则将使用这些值。

## 一个策略（派生）类

注意。

在继续之前，并且为了更简化的方法，请检查文档的*Signals*部分，如果不希望子类化策略。

使用该平台的任何人的目标都是对数据进行回测，这是在策略（派生类）内完成的。

至少有 2 种方法需要定制：

+   `__init__`

+   `next`

在初始化过程中，对数据和其他计算进行了创建和准备，以后将应用逻辑。

稍后将调用`next`方法来应用每个数据条的逻辑。

注意

如果传递了不同时间框架（因此具有不同的条形计数）的数据源，则将调用`next`方法以获取主数据（传递给 cerebro 的第一个数据，见下文），它必须是具有较小时间框架的数据

注意

如果使用数据重播功能，则将为相同的条形进行多次调用`next`方法，因为条形的发展正在重播。

一个基本的派生自类的策略：

```py
`class MyStrategy(bt.Strategy):

    def __init__(self):

        self.sma = btind.SimpleMovingAverage(self.data, period=20)

    def next(self):

        if self.sma > self.data.close:
            self.buy()

        elif self.sma < self.data.close:
            self.sell()` 
```

策略具有其他方法（或挂钩点）可以重写：

```py
`class MyStrategy(bt.Strategy):

    def __init__(self):

        self.sma = btind.SimpleMovingAverage(self.data, period=20)

    def next(self):

        if self.sma > self.data.close:
            submitted_order = self.buy()

        elif self.sma < self.data.close:
            submitted_order = self.sell()

    def start(self):
        print('Backtesting is about to start')

    def stop(self):
        print('Backtesting is finished')

    def notify_order(self, order):
        print('An order new/changed/executed/canceled has been received')` 
```

`start`和`stop`方法应该是不言而喻的。与预期的一样，并且遵循打印函数中的文本，当策略需要通知时，将调用`notify_order`方法。用例：

+   请求购买或出售（如下所示）

    购买/出售将返回一个*order*，该订单将提交给经纪人。保留对此已提交订单的引用由调用者自行决定。

    例如，可以用它来确保如果订单仍未处理，则不会提交新订单。

+   如果订单被接受/执行/取消/更改，则经纪人将通过 notify 方法将状态更改（例如执行大小）通知回策略

快速入门指南在`notify_order`方法中有一个完整而实用的订单管理示例。

还可以使用其他策略类做更多事情：

+   `buy`/`sell`/`close`

    使用底层的*broker*和*sizer*向经纪人发送购买/出售订单

    也可以通过手动创建订单并将其传递给经纪人来完成相同的操作。但是该平台旨在使使用它的人更容易。

    `close`将获取当前的市场持仓并立即关闭它。

+   `getposition`（或属性“position”）

    返回当前的市场持仓

+   `setsizer`/`getsizer`（或属性“sizer”）

    这些允许设置/获取底层的 Stake Sizer。可以对提供相同情况的不同 Stake 的 Sizer 检查相同的逻辑（固定大小，与资本成比例，指数）

    有很多文献，但 Van K. Tharp 在这个领域有出色的书籍。

策略是*Lines*对象，这些对象支持参数，这些参数使用标准 Python kwargs 参数进行收集：

```py
`class MyStrategy(bt.Strategy):

    params = (('period', 20),)

    def __init__(self):

        self.sma = btind.SimpleMovingAverage(self.data, period=self.params.period)

    ...
    ...` 
```

注意`SimpleMovingAverage`如何不再以固定值 20 进行实例化，而是使用已为策略定义的参数“period”进行实例化。

## 一个 Cerebro

一旦数据源可用并且策略已定义，Cerebro 实例就是将所有内容汇集在一起并执行操作的工具。实例化一个很容易：

```py
`cerebro = bt.Cerebro()` 
```

如果没有特殊要求，则默认情况下会进行处理。

+   创建默认经纪人

+   操作无佣金

+   数据源将被预加载

+   默认执行模式将是 runonce（批量操作），这是更快的模式

    所有指标必须支持`runonce`模式以实现全速运行。平台中包含的指标可以。

    自定义指标不需要实现 runonce 功能。`Cerebro`将模拟它，这意味着那些不兼容 runonce 的指标将运行得更慢。但大部分系统仍将以批处理模式运行。

由于数据源已经可用，策略也已经创建（之前创建），将它们组合在一起并使其运行的标准方法是：

```py
`cerebro.adddata(data)
cerebro.addstrategy(MyStrategy, period=25)
cerebro.run()` 
```

请注意以下内容：

+   数据源“实例”被添加

+   MyStrategy“类”被添加以及将传递给它的参数（kwargs）。

    MyStrategy 的实例化将由 cerebro 在后台完成，并且“addstrategy”中的任何 kwargs 将被传递给它

用户可以根据需要添加任意多的策略和数据源。策略如何相互通信以实现协调（如果需要）并不受平台的强制/限制。

当然，Cerebro 提供了额外的可能性：

+   决定预加载和操作模式：

    ```py
    `cerebro = bt.Cerebro(runonce=True, preload=True)` 
    ```

    这里有一个约束：`runonce`需要预加载（如果不是，批处理操作将无法运行）。当然，预加载数据源并不强制执行`runonce`

+   `setbroker` / `getbroker`（以及*broker*属性）

    如果需要，可以设置自定义经纪人。实际经纪人实例也可以被访问

+   绘图。在常规情况下，就像这样简单：

    ```py
    `cerebro.run()
    cerebro.plot()` 
    ```

    绘图需要一些参数进行自定义

    +   `numfigs=1`

        如果图表过于密集，可以将其拆分为多个图表

    +   `plotter=None`

        可以传递自定义绘图器实例，cerebro 将不会实例化默认绘图器

    +   `**kwargs` - 标准关键字参数

        这将传递给绘图器。

    请查看绘图部分获取更多信息。

+   优化策略。

    如上所述，Cerebro 获得一个派生自 Strategy 的类（而不是实例），以及将在调用“run”时传递给它的关键字参数。

    这样可以实现优化。相同的 Strategy 类将根据需要实例化多次，并使用新参数。如果一个实例已经传递给 cerebro…这将是不可能的。

    请求优化如下：

    ```py
    `cerebro.optstrategy(MyStrategy, period=xrange(10, 20))` 
    ```

    方法`optstrategy`具有与`addstrategy`相同的签名，但会进行额外的管理工作以确保优化按预期运行。一个策略可能期望一个*范围*作为策略的正常参数，而`addstrategy`不会对传递的参数做任何假设。

    另一方面，`optstrategy`将理解可迭代对象是一组值，必须按顺序传递给每个 Strategy 类的实例化。

    注意，传递的不是单个值，而是*一系列*值。在这个简单的情况下，将尝试这个策略的 10 个值 10 -> 19（20 是上限）。

    如果开发了更复杂的策略并具有额外的参数，它们都可以传递给*optstrategy*。必须不经过优化的参数可以直接传递，而无需最终用户创建一个仅包含一个值的虚拟可迭代对象。例如：

    ```py
    `cerebro.optstrategy(MyStrategy, period=xrange(10, 20), factor=3.5)` 
    ```

    `optstrategy`方法看到因子并在后台为具有单个元素的因子创建（所需的）虚拟可迭代对象（例如 3.5 中的示例）。

    注意

    交互式 Python shell 和某些类型的在*Windows*下的冻结可执行文件对 Python 的`multiprocessing`模块存在问题。

    请阅读有关`multiprocessing`的 Python 文档。
