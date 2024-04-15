# Python 的隐藏力量（3）

> 原文：[`www.backtrader.com/blog/posts/2016-11-25-hidden-powers-3/hidden-powers/`](https://www.backtrader.com/blog/posts/2016-11-25-hidden-powers-3/hidden-powers/)

最后，在关于如何在 *backtrader* 中使用 Python 的隐藏力量的系列中，一些魔术变量是如何出现的。

## `self.datas` 和其他属性是从哪里来的？

常见的类（或其子类）`Strategy`、`Indicator`、`Analyzer`、`Observer` 都自动定义了属性，例如包含 *data feeds* 的数组。

数据源被添加到 `cerebro` 实例中，如下所示：

```py
`from datetime import datetime
import backtrader as bt

cerebro = bt.Cerebro()
data = bt.YahooFinanceData(dataname=my_ticker, fromdate=datetime(2016, 1, 1))
cerebro.adddata(data)

...` 
```

我们的示例中的获胜策略将在 `close` 超过 *Simple Moving Average* 时进行多头操作。我们将使用 *Signals* 来缩短示例：

```py
`class MyStrategy(bt.SignalStrategy):
    params = (('period', 30),)

    def __init__(self):
        mysig = self.data.close > bt.indicators.SMA(period=self.p.period)
        self.signal_add(bt.signal.SIGNAL_LONG, mysig)` 
```

这些被添加到混合中：

```py
`cerebro.addstrategy(MyStrategy)` 
```

任何读者都会注意到：

+   `__init__` 不带任何参数，无论是否命名

+   没有 `super` 调用，因此基类没有直接要求执行其初始化

+   `mysig` 的定义引用了 `self.data`，这可能与添加到 `cerebro` 的 `YahooFinanceData` 实例有关。

    **确实如此！**

实际上还有其他属性存在，但在示例中看不到。例如：

+   `self.datas`：包含添加到 `cerebro` ��所有 *data feeds* 的数组

+   `self.dataX`：其中 `X` 是一个数字，反映了数据添加到 `cerebro` 中的顺序（`data0` 是上面添加的数据）

+   `self.data`：指向 `self.data0`。只是一个方便的快捷方式，因为大多数示例和策略只针对单个数据

更多内容请查看文档：

+   [`www.backtrader.com/docu/concepts.html`](https://www.backtrader.com/docu/concepts.html)

+   [`www.backtrader.com/docu/datafeed.html`](https://www.backtrader.com/docu/datafeed.html)

## 这些属性是如何创建的？

在本系列的第二篇文章中，看到类创建机制和实例创建机制被拦截。后者用于执行此操作。

+   `cerebro` 通过 `adstrategy` 接收 *class*。

+   当需要时，它将实例化自身并将自身添加为属性

+   在创建 `Strategy` 实例时，策略的 `new` 类方法被拦截，并检查 `cerebro` 中有哪些 *data feeds* 可用。

    它确实创建了上面提到的 *array* 和 *aliases*

这种机制应用于 *backtrader* 生态系统中的许多其他对象，以简化最终用户的操作。因此：

+   例如，没有必要不断创建包含名为 `datas` 的参数的函数原型，也不需要将其分配给 `self.datas`。

    因为它在后台自动完成

## 这种拦截的另一个示例

让我们定义一个获胜的指标，并将其添加到一个获胜的策略中。我们将重新包装 *close over SMA* 的概念：

```py
`class MyIndicator(bt.Indicator):
    params = (('period', 30),)
    lines = ('signal',)

    def __init__(self):
        self.lines.signal = self.data - bt.indicators.SMA` 
```

现在将其添加到常规策略中：

```py
`class MyStrategy(bt.Strategy):
    params = (('period', 30),)

    def __init__(self):
        self.mysig = MyIndicator(period=self.p.period)

    def next(self):
        if self.mysig:
            pass  # do something like buy ...` 
```

从上面的代码中显然在 `MyIndicator` 中进行了计算：

```py
`self.lines.signal = self.data - bt.indicators.SMA` 
```

但似乎没有地方执行这个操作。正如本系列中的第 1 篇文章所示，该操作生成一个*对象*，分配给`self.lines.signal`，然后发生以下情况：

+   这个对象还拦截了自己的创建过程

+   它扫描*堆栈*以了解正在创建的上下文，本例中是在`MyIndicators`的实例内部创建。

+   在*初始化*完成后，将自身添加到`MyIndicator`的内部结构中

+   稍后当计算`MyIndicator`时，它将反过来计算操作，该操作位于由`self.lines.signal`引用的对象内部

## 不错，但是谁计算`MyIndicator`

完全相同的过程被遵循：

+   `MyIndicator`在创建过程中扫描堆栈并找到`MyStrategy`

+   并将自身添加到`MyStrategy`的结构中

+   就在调用`next`之前，要求`MyIndicator`重新计算自身，这反过来告诉`self.lines.signal`重新计算自身

这个过程可以有多层间接性。

对用户来说最好的事情是：

+   当创建某物时无需添加像`register_operation`这样的调用

+   无需手动触发计算

## 总结

本系列中的最后一篇文章展示了另一个例子，说明了如何利用类/实例创建拦截来使最终用户的生活更轻松：

+   在需要的地方从生态系统中添加对象并创建别名

+   自动注册类并触发计算
