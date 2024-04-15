# 指标开发

> 原文：[`www.backtrader.com/docu/inddev/`](https://www.backtrader.com/docu/inddev/)

如果除了一个或多个获胜策略之外，还必须开发其他内容，则此内容是自定义指标。

根据作者的说法，平台内的这种开发是简单的。

需要以下内容：

+   从指标派生的类（直接或从已经存在的子类）

+   定义它将保持的*线条*

    指标必须至少有一条线。如果是从现有指标派生的，则可能已经定义了线条（们）

+   可选地定义可以更改行为的参数

+   可选地提供/自定义一些元素，以便合理地绘制指标

+   在`__init__`中提供完全定义的操作，并将其绑定（分配）到指标的线条（们）上，否则提供`next`和（可选）`once`方法

    如果可以在初始化期间使用逻辑/算术运算完全定义指标，并将结果分配给线条：完成

    如果不是这种情况，至少必须提供一个`next`，在该方法中指标必须将一个值分配给索引为 0 的线条（们）

    可以通过提供*once*方法来实现对**runonce**模式（批量操作）的计算优化。

## 重要提示：幂等性

指标为它们接收的每个柱状图生成输出。不必假设同一柱状图将被发送多少次。操作必须是幂等的。

其背后的原理是：

+   同一柱状图（索引）可以多次发送，其值会发生变化（即变化的值是收盘价）

这使得例如，“重播”每日会话，但使用可能由 5 分钟柱状图组成的股票数据成为可能。

这也可以使平台从实时数据源获取值。

### 一个虚拟（但功能性的）指标

所以它可以是：

```py
class DummyInd(bt.Indicator):
    lines = ('dummyline',)

    params = (('value', 5),)

    def __init__(self):
        self.lines.dummyline = bt.Max(0.0, self.params.value)
```

完成！指标将始终输出相同的值：如果大于 0.0，则为 0.0 或 self.params.value。

相同的指标，但使用了下一个方法：

```py
class DummyInd(bt.Indicator):
    lines = ('dummyline',)

    params = (('value', 5),)

    def next(self):
        self.lines.dummyline[0] = max(0.0, self.params.value)
```

完成！相同的行为。

注意

注意在`__init__`版本中如何使用`bt.Max`将其分配给 Line 对象`self.lines.dummyline`。

`bt.Max`返回一个*lines*对象，该对象会自动为传递给指标的每个柱状图迭代。

如果使用`max`，则赋值将毫无意义，因为指标将具有具有固定值的成员变量，而不是线条。

在`next`过程中，直接使用浮点值进行工作，并且可以使用标准的`max`内置函数

让我们回顾一下`self.lines.dummyline`是长格式的，可以缩写为：

+   `self.l.dummyline`

甚至可以：

+   `self.dummyline`

后者仅在代码没有将其与成员属性混淆时才可能存在。

第三个版本提供了一个额外的`once`方法来优化计算：

```py
class DummyInd(bt.Indicator):
    lines = ('dummyline',)

    params = (('value', 5),)

    def next(self):
        self.lines.dummyline[0] = max(0.0, self.params.value)

    def once(self, start, end):
       dummy_array = self.lines.dummyline.array

       for i in xrange(start, end):
           dummy_array[i] = max(0.0, self.params.value)
```

更有效，但开发`once`方法已迫使深入挖掘。实际上已经查看了内部情况。

`__init__` 版本无论如何都是最好的：

+   一切都限于初始化

+   `next` 和 `once`（都经过优化，因为 `bt.Max` 已经包含了它们）会自动提供，无需操作索引和/或公式

如果需要开发，指标还可以覆盖与 `next` 和 `once` 关联的方法：

+   `prenext` 和 `nexstart`

+   `preonce` 和 `oncestart`

### 手动/自动最小周期

如果可能的话，平台会计算，但可能需要手动操作。

这是一个*简单移动平均*的潜在实现：

```py
class SimpleMovingAverage1(Indicator):
    lines = ('sma',)
    params = (('period', 20),)

    def next(self):
        datasum = math.fsum(self.data.get(size=self.p.period))
        self.lines.sma[0] = datasum / self.p.period
```

尽管看起来合理，但平台并不知道最小周期是多少，即使参数被命名为“period”（名称可能会误导，并且一些指标接收到多个具有不同用途的“period”）

在这种情况下，`next`将被调用，已经为第 1 个柱，一切都将爆炸，因为 get 不能返回所需的 `self.p.period`。

在解决这种情况之前，必须考虑以下事项：

+   提供给指标的数据源可能已经具有**最小周期**

例如，可以对样本*SimpleMovingAverage*进行处理：

+   一个常规数据源

    这有一个默认的最小周期为 1（只需等待进入系统的第 1 个柱形图）

+   另一个移动平均……而这又已经有一个*周期*

    如果这是 20，再次我们的示例移动平均值也是 20，我们最终会得到一个最小周期为 40 个柱的周期

    实际上，内部计算说 39 …… 因为一旦第一个移动平均产生了一个柱，这就计为下一个移动平均，从而创建了一个重叠的柱，因此需要 39 个。

+   其他也携带周期的指标/对象

缓解情况的方法如下：

```py
class SimpleMovingAverage1(Indicator):
    lines = ('sma',)
    params = (('period', 20),)

    def __init__(self):
        self.addminperiod(self.params.period)

    def next(self):
        datasum = math.fsum(self.data.get(size=self.p.period))
        self.lines.sma[0] = datasum / self.p.period
```

`addminperiod` 方法告诉系统考虑该指标所需的额外*周期*柱到任何可能存在的最小周期。

有时这绝对不是必要的，如果所有计算都是使用已经向系统传达其周期需求的对象完成的。

带有直方图的快速*MACD*实现：

```py
from backtrader.indicators import EMA

class MACD(Indicator):
    lines = ('macd', 'signal', 'histo',)
    params = (('period_me1', 12), ('period_me2', 26), ('period_signal', 9),)

    def __init__(self):
        me1 = EMA(self.data, period=self.p.period_me1)
        me2 = EMA(self.data, period=self.p.period_me2)
        self.l.macd = me1 - me2
        self.l.signal = EMA(self.l.macd, period=self.p.period_signal)
        self.l.histo = self.l.macd - self.l.signal
```

完成！不需要考虑最小周期。

+   `EMA`代表*指数移动平均*（一个平台内置的别名）

    这个（已经在平台上）已经说明了它需要什么

+   指标“macd”和“signal”的命名线被分配了已经携带声明的对象（在幕后）周期的对象

    +   macd 取自“me1 - me2”操作的周期，这又取自 me1 和 me2 的周期的最大值（它们都是具有不同周期的指数移动平均值）

    +   signal 直接取 Exponential Moving Average 在 macd 上的周期。这个 EMA 还考虑了已经存在的 macd 周期和计算自身所需样本（period_signal）的数量

    +   histo 取两个操作数“signal - macd”的最大值。一旦两者准备好了，histo 也可以生成一个值

## 一个完全定制的指标

让我们开发一个简单的自定义指标，用于“指示”移动平均线（可以通过参数进行修改）是否高于给定数据：

```py
import backtrader as bt
import backtrader.indicators as btind

class OverUnderMovAv(bt.Indicator):
    lines = ('overunder',)
    params = dict(period=20, movav=btind.MovAv.Simple)

    def __init__(self):
        movav = self.p.movav(self.data, period=self.p.period)
        self.l.overunder = bt.Cmp(movav, self.data)
```

完成！如果平均值高于数据，则指标将具有“1”值，如果低于数据，则为“-1”。

如果数据是定期数据源，与收盘价进行比较会产生 1 和-1。

尽管在*绘图*部分可以看到更多内容，并且为了在绘图世界中表现良好，可以添加一些内容：

```py
import backtrader as bt
import backtrader.indicators as btind

class OverUnderMovAv(bt.Indicator):
    lines = ('overunder',)
    params = dict(period=20, movav=bt.ind.MovAv.Simple)

    plotinfo = dict(
        # Add extra margins above and below the 1s and -1s
        plotymargin=0.15,

        # Plot a reference horizontal line at 1.0 and -1.0
        plothlines=[1.0, -1.0],

        # Simplify the y scale to 1.0 and -1.0
        plotyticks=[1.0, -1.0])

    # Plot the line "overunder" (the only one) with dash style
    # ls stands for linestyle and is directly passed to matplotlib
    plotlines = dict(overunder=dict(ls='--'))

    def _plotlabel(self):
        # This method returns a list of labels that will be displayed
        # behind the name of the indicator on the plot

        # The period must always be there
        plabels = [self.p.period]

        # Put only the moving average if it's not the default one
        plabels += [self.p.movav] * self.p.notdefault('movav')

        return plabels

    def __init__(self):
        movav = self.p.movav(self.data, period=self.p.period)
        self.l.overunder = bt.Cmp(movav, self.data)
```
