# 平台概念

> 原文：[`www.backtrader.com/docu/concepts/`](https://www.backtrader.com/docu/concepts/)

这是平台某些概念的集合。它试图收集可在使用平台时有用的信息片段。

## 开始之前

所有小代码示例都假设以下导入可用：

```py
`import backtrader as bt
import backtrader.indicators as btind
import backtrader.feeds as btfeeds` 
```

注意

访问子模块的另一种替代语法，如*指标*和*数据源*：

```py
`import backtrader as bt` 
```

然后：

```py
`thefeed = bt.feeds.OneOfTheFeeds(...)
theind = bt.indicators.SimpleMovingAverage(...)` 
```

## 数据源 - 传递它们

与平台工作的基础将通过*策略*完成。这些将获得*数据源*。平台最终用户不需要关心接收它们：

*数据源被自动提供为策略的成员变量，以数组形式和数组位置的快捷方式*

策略派生类声明和运行平台的快速预览：

```py
`class MyStrategy(bt.Strategy):
    params = dict(period=20)

    def __init__(self):

        sma = btind.SimpleMovingAverage(self.datas[0], period=self.params.period)

    ...

cerebro = bt.Cerebro()

...

data = btfeeds.MyFeed(...)
cerebro.adddata(data)

...

cerebro.addstrategy(MyStrategy, period=30)

...` 
```

注意以下内容：

+   策略的`__init__`方法未接收到`*args`或`**kwargs`（仍然可以使用它们）。

+   存在成员变量`self.datas`，其为包含至少一个项目的数组/列表/可迭代对象（希望至少有一个项目，否则将引发异常）。

是的。*数据源*被添加到平台上，它们将按照它们被添加到系统中的顺序显示在策略内部。

注意

这也适用于`指标`，如果最终用户开发自己的自定义指标或者查看某些现有指标参考的源代码时。

### 数据源的快捷方式

可以直接使用附加自动成员变量访问 self.datas 数组项：

+   `self.data`目标为`self.datas[0]`

+   `self.dataX`目标为`self.datas[X]`

然后的示例：

```py
`class MyStrategy(bt.Strategy):
    params = dict(period=20)

    def __init__(self):

        sma = btind.SimpleMovingAverage(self.data, period=self.params.period)

    ...` 
```

### 省略数据源

上述示例可以进一步简化为：

```py
`class MyStrategy(bt.Strategy):
    params = dict(period=20)

    def __init__(self):

        sma = btind.SimpleMovingAverage(period=self.params.period)

    ...` 
```

`self.data`已从`SimpleMovingAverage`的调用中完全移除。如果这样做，指标（在本例中为`SimpleMovingAverage`）将接收正在创建的对象的第一个数据（*策略*），即`self.data`（又名`self.data0`或`self.datas[0]`）

### 几乎一切都是*数据源*

不仅数据源是数据，也可以传递。`指标`和`操作`的结果也是数据。

在上一个示例中，`SimpleMovingAverage`将`self.datas[0]`作为输入进行操作。具有操作和额外指标的示例：

```py
`class MyStrategy(bt.Strategy):
    params = dict(period1=20, period2=25, period3=10, period4)

    def __init__(self):

        sma1 = btind.SimpleMovingAverage(self.datas[0], period=self.p.period1)

        # This 2nd Moving Average operates using sma1 as "data"
        sma2 = btind.SimpleMovingAverage(sma1, period=self.p.period2)

        # New data created via arithmetic operation
        something = sma2 - sma1 + self.data.close

        # This 3rd Moving Average operates using something  as "data"
        sma3 = btind.SimpleMovingAverage(something, period=self.p.period3)

        # Comparison operators work too ...
        greater = sma3 > sma1

        # Pointless Moving Average of True/False values but valid
        # This 4th Moving Average operates using greater  as "data"
        sma3 = btind.SimpleMovingAverage(greater, period=self.p.period4)

    ...` 
```

基本上，一旦被操作，一切都会转换为可以用作数据源的对象。

## 参数

平台中的几乎每个其他`class`都支持*参数*的概念。

+   参数连同默认值声明为类属性（元组的元组或类似字典的对象）

+   关键字参数（`**kwargs`）被扫描以匹配参数，如果找到，则从`**kwargs`中删除它们并将值分配给相应的参数。

+   参数最终可以通过访问成员变量`self.params`（简写：`self.p`）在类的实例中使用。

前面的快速策略预览已经包含了一个参数示例，但为了冗余起见，再次，只关注参数。使用*元组*：

```py
`class MyStrategy(bt.Strategy):
    params = (('period', 20),)

    def __init__(self):
        sma = btind.SimpleMovingAverage(self.data, period=self.p.period)` 
```

并且使用一个`dict`：

```py
`class MyStrategy(bt.Strategy):
    params = dict(period=20)

    def __init__(self):
        sma = btind.SimpleMovingAverage(self.data, period=self.p.period)` 
```

## 行

再次，平台上几乎每个其他对象都是`Lines`启用的对象。从最终用户的角度来看，这意味着：

+   它可以容纳一个或多个线系列，其中线系列是一个值数组，将这些值放在一起形成一条线。

一个*line*（或*lineseries*）的很好的例子是由股票收盘价形成的线。这实际上是价格演变的一个众所周知的图表表示（称为*Line on Close*）

平台的常规使用只关注**访问**`lines`。前面的迷你策略示例，稍微扩展一下，再次派上用场：

```py
`class MyStrategy(bt.Strategy):
    params = dict(period=20)

    def __init__(self):

        self.movav = btind.SimpleMovingAverage(self.data, period=self.p.period)

    def next(self):
        if self.movav.lines.sma[0] > self.data.lines.close[0]:
            print('Simple Moving Average is greater than the closing price')` 
```

已公开两个具有`lines`的对象：

+   `self.data` 它有一个`lines`属性，其中又包含一个`close`属性

+   `self.movav` 是一个`SimpleMovingAverage`指标 它有一个`lines`属性，其中又包含一个`sma`属性

注

从这可以明显看出，`lines`是有名称的。它们也可以按照声明顺序顺序访问，但这只应在`Indicator`开发中使用

两个*lines*，即`close`和`sma`，都可以查询一个点（*索引 0*）以比较值。

有一种缩写访问*行*的方法存在：

+   `xxx.lines` 可以缩短为 `xxx.l`

+   `xxx.lines.name` 可以缩短为 `xxx.lines_name`

+   类似策略和指标的复杂对象提供了对数据线的快速访问

    +   `self.data_name` 提供了对 `self.data.lines.name` 的直接访问

    +   这也适用于编号的数据变量：`self.data1_name` -> `self.data1.lines.name`

此外，线路名称可以直接访问：

+   `self.data.close` 和 `self.movav.sma`

    但是如果实际上正在访问*行*，则该符号并不像之前的符号那样清晰。

不是

使用这两个后续符号**设置**/**分配**行不受支持

### *Lines*声明

如果正在开发一个*Indicator*，则必须声明该指标具有的*lines*。

正如与*params*一样，这次以类属性的形式发生，**仅**作为元组。不支持字典，因为它们不按插入顺序存储事物。

对于简单移动平均线，应该这样做：

```py
`class SimpleMovingAverage(Indicator):
    lines = ('sma',)

    ...` 
```

注

如果将单个字符串传递给元组，则元组中需要跟随声明的*逗号*，否则字符串中的每个字母都将被解释为要添加到元组中的项目。这可能是 Python 语法出错的几个情况之一。

如前面的例子所示，此声明在*Indicator*中创建了一个`sma`线，可以在策略的逻辑中（可能也可以由其他指标）稍后访问以创建更复杂的指标。

对于开发而言，有时以通用的非命名方式访问行是有用的，这就是编号访问发挥作用的地方：

+   `self.lines[0]` 指向 `self.lines.sma`

如果定义了更多行，则可以使用索引 1、2 和更高的索引来访问它们。

当然，还存在额外的简写版本：

+   `self.line` 指向 `self.lines[0]`

+   `self.lineX` 指向 `self.lines[X]`

+   `self.line_X` 指向 `self.lines[X]`

在接收*数据源*的对象内，这些数据源下方的行也可以通过数字快速访问：

+   `self.dataY` 指向 `self.data.lines[Y]`

+   `self.dataX_Y` 指向 `self.dataX.lines[X]`，这是 `self.datas[X].lines[Y]` 的完整简化版本

### 在*数据源*中访问 `lines`

在*数据源*内部，也可以访问 `lines`，省略 `lines`。这使得使用像 `close` 价格这样的内容更加自然。

例如：

```py
`data = btfeeds.BacktraderCSVData(dataname='mydata.csv')

...

class MyStrategy(bt.Strategy):

    ...

    def next(self):

        if self.data.close[0] > 30.0:
            ...` 
```

这似乎比也有效的更自然：`if self.data.lines.close[0] > 30.0:`。同样的情况不适用于带有以下理由的`Indicators`：

+   `Indicator` 可能有一个属性 `close`，它保存一个中间计算结果，稍后交付给实际的 `lines`，也称为 `close`

在*数据源*的情况下，不会进行任何计算，因为它只是一个数据源。

### *行*长度

*行*有一组点，并在执行过程中动态增长，因此可以随时通过调用标准的 Python `len` 函数来测量长度。

这适用于例如：

+   数据源

+   策略

+   指标

*数据源*中存在另一个属性，当数据 **预加载** 时适用：

+   方法 `buflen`

该方法返回*数据源*可用的实际条形图数。

`len` 和 `buflen` 的区别

+   `len` 报告已处理了多少个条形图

+   `buflen` 报告为数据源加载了多少个条形图

如果两者返回相同的值，则要么没有预加载数据，要么条形图的处理已消耗所有预加载的条形图（除非系统连接到实时数据源，否则这将意味着处理结束）

### 行和参数的继承

一种元语言用于支持*参数*和*行*的声明。为了使其与标准 Python 继承规则兼容，已经尽一切努力。

#### 参数继承

继承应该按预期工作：

+   支持多重继承

+   从基类继承参数

+   如果多个基类定义了相同的参数，则使用继承列表中最后一个类的默认值

+   如果在子类中重新定义了相同的参数，则新的默认值将取代基类的默认值

#### 行继承

+   支持多重继承

+   来自所有基类的行都会被继承。作为*命名*行，如果相同的名称在基类中使用了多次，则只会有一个版本的行

## 索引：0 和 -1

*行*如前所述是线系列，并在绘制时一起组成一条线（就像沿时间轴连接所有收盘价一样）

要在常规代码中访问这些点，选择使用基于**0**的方法来获取/设置当前*get/set*即时。

策略只获取值。指标还设置值。

在之前的快速策略示例中，`next`方法被简要看到：

```py
`def next(self):
    if self.movav.lines.sma[0] > self.data.lines.close[0]:
        print('Simple Moving Average is greater than the closing price')` 
```

逻辑是通过应用索引`0` *获取*移动平均值和当前收盘价的当前值。

注意

实际上，对于索引`0`，并且在应用逻辑/算术运算符时，可以直接进行比较，如下所示：

```py
`if self.movav.lines.sma > self.data.lines.close:
    ...` 
```

在文档后面看运算符的解释。

设置是用于开发时使用的，例如，一个 Indicator，因为必须通过该指标设置当前输出值。

可以按以下方式计算当前获取/设置点的 SimpleMovingAverage：

```py
`def next(self):
  self.line[0] = math.fsum(self.data.get(0, size=self.p.period)) / self.p.period` 
```

访问先前设置的点是按照 Python 为访问数组/可迭代时定义的`-1`进行建模

+   它指向数组的最后一项

平台将最后设置的项（当前实时获取/设置点之前）视为`-1`。

因此，将当前`close`与*前一个*`close`进行比较是一个`0` vs `-1`的事情。例如，在策略中：

```py
`def next(self):
    if self.data.close[0] > self.data.close[-1]:
        print('Closing price is higher today')` 
```

当然，从`-1`之前设置的价格将使用`-2、-3、...`进行访问。

## 切片

*backtrader*不支持对*lines*对象进行切片，这是一种设计决策，遵循了`[0]`和`[-1]`索引方案。对于常规可索引的 Python 对象，您会执行以下操作：

```py
`myslice = self.my_sma[0:]  # slice from the beginning til the end` 
```

但请记住，在选择为`0`时……实际上是当前交付的值，后面没有了。另外：

```py
`myslice = self.my_sma[0:-1]  # slice from the beginning til the end` 
```

再次……`0`是当前值，`-1`是最新（先前）的交付值。这就是为什么在*backtrader*生态系统中从`0` -> `-1`切片没有意义的原因。

如果支持切片，将如下所示：

```py
`myslice = self.my_sma[:0]  # slice from current point backwards to the beginning` 
```

或：

```py
`myslice = self.my_sma[-1:0]  # last value and current value` 
```

或：

```py
`myslice = self.my_sma[-3:-1]  # from last value backwards to the 3rd last value` 
```

### 获取一个切片

仍然可以获取具有最新值的数组。语法：

```py
`myslice = self.my_sma.get(ago=0, size=1)  # default values shown` 
```

这将返回一个具有`1`值（`size=1`）的数组，当前时刻`0`作为向后查找的起始点。

要从当前时间点获取 10 个值（即：最后 10 个值）：

```py
`myslice = self.my_sma.get(size=10)  # ago defaults to 0` 
```

当然，数组具有您期望的顺序。最左边的值是最旧的值，最右边的值是最新的值（它是一个常规的 Python 数组，而不是一个*lines*对象）

要获取最后 10 个值，仅跳过当前点：

```py
`myslice = self.my_sma.get(ago=-1, size=10)` 
```

## 行：延迟索引

`[]`操作符语法用于在`next`逻辑阶段提取单个值。 *Lines*对象支持另一种符号，通过*延迟行对象*在`__init__`阶段访问值。

假设逻辑的兴趣是将前一个*close*值与*简单移动平均值*的实际值进行比较。而不是在每个`next`迭代中手动执行，可以生成一个预先定义的*lines*对象：

```py
`class MyStrategy(bt.Strategy):
    params = dict(period=20)

    def __init__(self):

        self.movav = btind.SimpleMovingAverage(self.data, period=self.p.period)
        self.cmpval = self.data.close(-1) > self.sma

    def next(self):
        if self.cmpval[0]:
            print('Previous close is higher than the moving average')` 
```

在这里，正在使用`(delay)`符号：

+   这提供了一个`close`价格的副本，但是延迟了`-1`。

    比较`self.data.close(-1) > self.sma`生成另一个*lines*对象，如果条件为`True`则返回`1`，如果为`False`则返回`0`

## 线耦合

运算符`()`可以像上面显示的那样与`delay`值一起使用，以提供*lines*对象的延迟版本。

如果使用语法*WITHOUT*提供`delay`值，则返回一个`LinesCoupler` *lines*对象。这旨在在操作*datas*具有不同时间框架的指标之间建立耦合。

具有不同时间框架的数据源具有不同的*长度*，在其上操作的指标复制数据的长度。例如：

+   每年的日数据源大约有 250 个柱状图

+   每年的周数据源有 52 个柱状图

尝试创建一个操作（例如），比较两个*简单移动平均*，每个操作在上述引用的数据上运行，会出现问题。不清楚如何将每日时间框架的 250 个柱状图与每周时间框架的 52 个柱状图匹配。

读者可以想象在后台进行`date`比较，以找出一天 - 一周的对应关系，但是：

+   `指标`只是数学公式，没有*日期时间*信息。

    它们对环境一无所知，只知道如果数据提供足够的值，就可以进行计算。

`()`（空调用）符号来拯救：

```py
`class MyStrategy(bt.Strategy):
    params = dict(period=20)

    def __init__(self):

        # data0 is a daily data
        sma0 = btind.SMA(self.data0, period=15)  # 15 days sma
        # data1 is a weekly data
        sma1 = btind.SMA(self.data1, period=5)  # 5 weeks sma

        self.buysig = sma0 > sma1()

    def next(self):
        if self.buysig[0]:
            print('daily sma is greater than weekly sma1')` 
```

在这里，较大时间框架指标`sma1`与每日时间框架耦合为`sma1()`。这返回一个与`sma0`的更大柱状图兼容的对象，并复制由`sma1`产生的值，有效地将 52 周柱状图分散在 250 日柱状图中

## 运算符，使用自然构造

为了实现“易用性”目标，该平台允许（在 Python 的约束条件下）使用运算符。为了进一步增强这一目标，运算符的使用已分为两个阶段。

### 第 1 阶段 - 运算符创建对象

即使没有明确指定，示例已经展示了这一点。在像指标和策略这样的对象的初始化阶段（`__init__`方法）中，运算符创建可以进行操作、分配或保留作为后续在策略逻辑评估阶段使用的对象。

再次展示了一个简单移动平均的潜在实现，进一步分解为步骤。

简单移动平均指标`__init__`中的代码可能如下所示：

```py
`def __init__(self):
    # Sum N period values - datasum is now a *Lines* object
    # that when queried with the operator [] and index 0
    # returns the current sum

    datasum = btind.SumN(self.data, period=self.params.period)

    # datasum (being *Lines* object although single line) can be
    # naturally divided by an int/float as in this case. It could
    # actually be divided by anothr *Lines* object.
    # The operation returns an object assigned to "av" which again
    # returns the current average at the current instant in time
    # when queried with [0]

    av = datasum / self.params.period

    # The av *Lines* object can be naturally assigned to the named
    # line this indicator delivers. Other objects using this
    # indicator will have direct access to the calculation

    self.line.sma = av` 
```

在策略初始化期间展示了一个更完整的用例：

```py
`class MyStrategy(bt.Strategy):

    def __init__(self):

        sma = btind.SimpleMovinAverage(self.data, period=20)

        close_over_sma = self.data.close > sma
        sma_dist_to_high = self.data.high - sma

        sma_dist_small = sma_dist_to_high < 3.5

        # Unfortunately "and" cannot be overridden in Python being
        # a language construct and not an operator and thus a
        # function has to be provided by the platform to emulate it

        sell_sig = bt.And(close_over_sma, sma_dist_small)` 
```

在上述操作完成后，*sell_sig*是一个*Lines*对象，可以在策略的逻辑中稍后使用，指示条件是否满足。

### 第二阶段 - 自然的运算符

让我们首先记住，策略有一个`next`方法，系统处理每个柱状图时都会调用该方法。这就是运算符实际处于第 2 阶段模式的地方。在前面的示例基础上构建：

```py
`class MyStrategy(bt.Strategy):

    def __init__(self):

        self.sma = sma = btind.SimpleMovinAverage(self.data, period=20)

        close_over_sma = self.data.close > sma
        self.sma_dist_to_high = self.data.high - sma

        sma_dist_small = sma_dist_to_high < 3.5

        # Unfortunately "and" cannot be overridden in Python being
        # a language construct and not an operator and thus a
        # function has to be provided by the platform to emulate it

        self.sell_sig = bt.And(close_over_sma, sma_dist_small)

    def next(self):

        # Although this does not seem like an "operator" it actually is
        # in the sense that the object is being tested for a True/False
        # response

        if self.sma > 30.0:
            print('sma is greater than 30.0')

        if self.sma > self.data.close:
            print('sma is above the close price')

        if self.sell_sig:  # if sell_sig == True: would also be valid
            print('sell sig is True')
        else:
            print('sell sig is False')

        if self.sma_dist_to_high > 5.0:
            print('distance from sma to hig is greater than 5.0')` 
```

不是一个非常有用的策略，只是一个例子。在第 2 阶段，运算符返回预期的值（如果测试真实性则返回布尔值，如果与浮点数比较则返回浮点数），算术运算也是如此。

注意

请注意，比较实际上并没有使用[]运算符。这是为了进一步简化事情。

`if self.sma > 30.0:` … 比较`self.sma[0]`和`30.0`（第 1 行和当前值）

`if self.sma > self.data.close:` … 比较`self.sma[0]`和`self.data.close[0]`

### 一些未被覆盖的运算符/函数

Python 不允许覆盖所有内容，因此提供了一些函数来处理这些情况。

注意

仅在阶段 1 中使用，以创建稍后提供值的对象。

运算符：

+   `and` -> `And`

+   `or` -> `Or`

逻辑控制：

+   `if` -> `If`

函数：

+   `any` -> `Any`

+   `all` -> `All`

+   `cmp` -> `Cmp`

+   `max` -> `Max`

+   `min` -> `Min`

+   `sum` -> `Sum`

    `Sum`实际上使用`math.fsum`作为底层操作，因为平台使用浮点数，并且应用常规的`sum`可能会影响精度。

+   `reduce` -> `Reduce`

这些实用运算符/函数操作可迭代对象。可迭代对象中的元素可以是常规的 Python 数值类型（整数，浮点数，...）也可以是具有*Lines*的对象。

一个生成非常愚蠢买入信号的例子：

```py
`class MyStrategy(bt.Strategy):

    def __init__(self):

        sma1 = btind.SMA(self.data.close, period=15)
        self.buysig = bt.And(sma1 > self.data.close, sma1 > self.data.high)

    def next(self):
        if self.buysig[0]:
            pass  # do something here` 
```

很明显，如果`sma1`高于最高价，那么它必须高于收盘价。但重点是说明`bt.And`的用法。

使用`bt.If`：

```py
`class MyStrategy(bt.Strategy):

    def __init__(self):

        sma1 = btind.SMA(self.data.close, period=15)
        high_or_low = bt.If(sma1 > self.data.close, self.data.low, self.data.high)
        sma2 = btind.SMA(high_or_low, period=15)` 
```

分解：

+   在`data.close`上生成一个`SMA`，周期为`15`

+   然后

    +   `bt.If`如果*sma*的值大于`close`，则返回`low`，否则返回`high`

    请记住，当调用`bt.If`时并不会返回实际值。它返回一个*Lines*对象，就像一个*SimpleMovingAverage*一样。

    值将在系统运行时计算

+   生成的`bt.If` *Lines*对象然后被传递给第 2 个`SMA`，有时会使用`low`价格，有时会使用`high`价格进行计算

这些**函数**也接受数值。同样的例子，稍作修改：

```py
`class MyStrategy(bt.Strategy):

    def __init__(self):

        sma1 = btind.SMA(self.data.close, period=15)
        high_or_30 = bt.If(sma1 > self.data.close, 30.0, self.data.high)
        sma2 = btind.SMA(high_or_30, period=15)` 
```

现在第 2 个移动平均值使用`30.0`或`high`价格执行计算，取决于`sma`与`close`的逻辑状态

注意

值`30`在内部转换为一个伪可迭代对象，始终返回`30`
