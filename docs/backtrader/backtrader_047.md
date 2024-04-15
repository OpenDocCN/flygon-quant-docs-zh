# 使用指标

> 原文：[`www.backtrader.com/docu/induse/`](https://www.backtrader.com/docu/induse/)

在平台中，指标可以在两个地方使用：

+   在策略内部

+   在其他指标内部

## 指标的作用

1.  `Indicators`始终在*Strategy*中的`__init__`期间实例化

1.  在`next`期间使用/检查指标值（或其派生的值）

有一个重要的公理需要考虑：

+   在`__init__`期间声明的任何`Indicator`（或派生值）将在调用`next`之前预先计算。

让我们来看一下差异和操作模式。

## `__init__`与`next`

事情的工作方式如下：

+   在`__init__`期间涉及**lines**对象的任何**操作**都会生成另一个**lines**对象

+   在`next`期间涉及**lines**对象的任何**操作**都会产生常规的 Python 类型，如浮点数和布尔值。

### 在`__init__`期间

在`__init__`期间操作的示例：

```py
`hilo_diff = self.data.high - self.data.low` 
```

变量`hilo_diff`保存对在调用`next`之前预先计算的**lines**对象的引用，并且可以使用标准数组表示法`[]`访问

很明显，对于数据源的每个条数据，它包含了高低之间的差异。

这也适用于将简单的**lines**（如 self.data 数据源中的 lines）与复杂的指标混合使用时：

```py
`sma = bt.SimpleMovingAverage(self.data.close)
close_sma_diff = self.data.close - sma` 
```

现在`close_sma_diff`再次包含一个**line**对象。

使用逻辑运算符：

```py
`close_over_sma = self.data.close > sma` 
```

现在生成的**lines**对象将包含一个布尔数组。

### 在`next`期间

操作示例（逻辑运算符）：

```py
`close_over_sma = self.data.close > self.sma` 
```

使用等效数组（索引从 0 开始的表示法）：

```py
`close_over_sma = self.data.close[0] > self.sma[0]` 
```

在这种情况下，`close_over_sma`产生一个布尔值，该值是通过将`self.data.close`和`self.sma`应用到`[0]`运算符返回的两个浮点值进行比较的结果

### `__init__`与`next`*为什么*

逻辑简化（以及随之的易用性）是关键。计算和大部分相关逻辑可以在`__init__`期间声明，在`next`期间将实际操作逻辑保持最小化。

实际上还有一个附加好处：**速度**（由于在开头解释的预计算）

一个完整的示例，在`__init__`期间生成一个**buy**信号：

```py
`class MyStrategy(bt.Strategy):

    def __init__(self):

        sma1 = btind.SimpleMovingAverage(self.data)
        ema1 = btind.ExponentialMovingAverage()

        close_over_sma = self.data.close > sma1
        close_over_ema = self.data.close > ema1
        sma_ema_diff = sma1 - ema1

        buy_sig = bt.And(close_over_sma, close_over_ema, sma_ema_diff > 0)

    def next(self):

        if buy_sig:
            self.buy()` 
```

注意

Python 的`and`运算符不能被重载，迫使平台定义自己的`And`。其他构造也是如此，比如`Or`和`If`

显然，`__init__`期间的“声明式”方法将`next`的膨胀（其中实际策略工作发生）最小化。

（不要忘记还有一个加速因素）

注意

当逻辑变得非常复杂并涉及多个操作时，通常更好的做法是将其封装在一个`Indicator`内部。

## 一些注意事项

在上面的示例中，与其他平台相比，`backtrader`中已经简化了两件事情：

+   声明的`Indicators`既不会得到一个**parent**参数（就像它们被创建的策略一样），也不会调用任何类型的“register”方法/函数。

    尽管如此，策略仍将触发 `Indicators` 的计算和因操作而生成的任何 **lines** 对象（如 `sma - ema`）

+   `ExponentialMovingAverage` 在没有 `self.data` 的情况下被实例化

    这是故意的。如果没有传递 `data`，则将自动传递父级的第一个数据（在本例中，正在创建的策略）

## 指标绘图

首先和最重要的是：

+   声明的 `Indicators` 将自动绘制（如果调用了 cerebro.plot）

+   来自操作的 **lines** 对象不会被绘制（如 `close_over_sma = self.data.close > self.sma`）

    如果需要，有一个辅助的 `LinePlotterIndicator`，它可以使用以下方法绘制此类操作：

    ```py
    `close_over_sma = self.data.close > self.sma
    LinePlotterIndicator(close_over_sma, name='Close_over_SMA')` 
    ```

    `name` 参数为此指标持有的 **单个** 线条命名。

### 控制绘图

在开发 `Indicator` 时，可以添加一个 `plotinfo` 声明。它可以是元组的元组（2 个元素）、一个 `dict` 或一个 `OrderedDict`。它看起来像：

```py
`class MyIndicator(bt.Indicator):

    ....
    plotinfo = dict(subplot=False)
    ....` 
```

该值稍后可以按如下方式访问（和设置）（如果需要的话）：

```py
`myind = MyIndicator(self.data, someparam=value)
myind.plotinfo.subplot = True` 
```

该值甚至可以在实例化期间设置：

```py
`myind = MyIndicator(self.data, someparams=value, subplot=True)` 
```

`subplot=True` 将传递给（幕后）实例化的成员变量 `plotinfo`，用于指标。

`plotinfo` 提供以下参数来控制绘图行为：

+   `plot`（默认值：`True`）

    指标是否要绘制或不绘制

+   `subplot`（默认值：`True`）

    是否在不同窗口中绘制指标。对于像移动平均这样的指标，默认值更改为 `False`

+   `plotname`（默认值：`''`）

    将绘图名称设置为显示在图表上。空值意味着将使用指标的规范名称（`class.__name__`）。这有一些限制，因为 Python 标识符不能使用例如算术运算符。

    像 DI+ 这样的指标将声明如下：

    ```py
    `class DIPlus(bt.Indicator):
        plotinfo=dict(plotname='DI+')` 
    ```

    使图表“更美观”

+   `plotabove`（默认值：`False`）

    通常将指标绘制在它们操作的数据下方（那些 `subplot=True` 的指标）。将此设置为 `True` 将使指标绘制在数据之上。

+   `plotlinelabels`（默认值：`False`）

    用于“指标”上的“指标”。如果计算 RSI 的 SimpleMovingAverage，则绘图通常会显示相应绘制线的名称“SimpleMovingAverage”。这是“Indicator”的名称，而不是实际绘制的线的名称。

    这种默认行为是有意义的，因为用户通常希望看到使用 RSI 创建了 SimpleMovingAverage。

    如果将值设置为 `True`，则将使用 SimpleMovingAverage 中线的实际名称。

+   `plotymargin`（默认值：`0.0`）

    要在指标的顶部和底部留下的边距量（`0.15` -> 15%）。有时 `matplotlib` 绘图会延伸到轴的顶部/底部，可能希望有一些边距

+   `plotyticks`（默认值：`[]`）

    用于控制绘制的 y 轴刻度

    如果传递了一个空列表，“y ticks”将自动计算。对于像随机指标这样的指标，将其设置为已知的行业标准可能是有意义的，例如：`[20.0, 50.0, 80.0]`。

    一些指标提供诸如`upperband`和`lowerband`之类的参数，实际上用于操作 y ticks。

+   `plothlines`（默认值：`[]`）

    用于控制沿指标轴绘制水平线。

    如果传递了一个空列表，将不会绘制任何水平线。

    对于像随机指标这样的指标，绘制已知的行业标准线可能是有意义的，例如：`[20.0, 80.0]`。

    一些指标提供诸如`upperband`和`lowerband`之类的参数，实际上用于操作水平线。

+   `plotyhlines`（默认值：`[]`）

    用于同时使用单个参数控制 plotyticks 和 plothlines。

+   `plotforce`（默认值：`False`）

    如果由于某种原因你认为某个指标应该绘制但却没有绘制……将此设置为`True`作为最后的手段。
