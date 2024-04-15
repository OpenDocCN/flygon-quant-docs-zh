# Python 的隐藏功能（1）

> 原文：[`www.backtrader.com/blog/posts/2016-11-20-python-hidden-powers/hidden-powers/`](https://www.backtrader.com/blog/posts/2016-11-20-python-hidden-powers/hidden-powers/)

只有当真正遇到*backtrader*的实际用户时，才能意识到平台中使用的抽象和 Python 功能是否有意义。

在不放弃*pythonic*座右铭的情况下，*backtrader*试图尽可能给用户更多的控制权，同时通过利用 Python 提供的*隐藏*功能来简化使用。

这是系列中第一篇文章中的第一个示例。

## 它是一个数组还是其他什么？

一个非常快速的例子：

```py
import backtrader as bt

class MyStrategy(bt.Strategy):

    def __init__(self):

        self.hi_lo_avg = (self.data.high + self.data.low) / 2.0

    def next(self):
        if self.hi_lo_avg[0] > another_value:
            print('we have a winner!')

...
...
cerebro.addstrategy(MyStrategy)
cerebro.run()
```

很快就会出现的一个问题是：

+   在`__init__`期间也可以使用`[]`吗？

用户已经尝试并且 Python 因异常而停止运行，所以提出这个问题。

答案是：

+   不，使用`[]`不是在初始化期间使用的。

接下来的问题是：

+   那么在`__init__`期间`self.hi_lo_avg`实际存储了什么，如果不是数组？

对程序员来说，答案并不令人困惑，但对选择 Python 的算法交易员来说可能会令人困惑

+   这是一个惰性计算的对象，在`cerebro.run`阶段通过`[]`运算符计算和传递值，即在策略的`next`方法中。

要点：在`next`方法中，数组索引运算符`[]`将使您能够访问过去和当前时间点的计算值。

## 秘密就在于这个方法

*运算符重载*才是真正的方法。让我们分解*高低平均值*的计算：

```py
self.hi_lo_avg = (self.data.high + self.data.low) / 2.0
```

组件：

+   `self.data.high`和`self.data.low`本身就是*对象*（在*backtrader*命名方案中称为*lines*）

在许多情况下，它们被错误地视为纯*arrays*，但它们并不是。它们被视为对象的原因是：

+   *backtrader*中已经实现了`0`和`-1`索引方案

+   控制缓冲区大小并链接到其他对象

最重要的一点是：

+   重载运算符以返回*对象*

为什么下面的操作返回一个*lines*对象。让我们开始：

```py
temp = self.data.high - self.data.low
```

然后将临时对象除以`2.0`并赋值给成员变量：

```py
self.hi_lo_avg = temp / 2.0
```

这再次返回另一个*lines*对象。因为运算符重载不仅适用于直接在*lines*对象之间执行的操作，还适用于例如这种除法的算术运算。

这意味着`self.hi_lo_avg`引用了一个*lines*对象。这个对象在策略的`next`方法中或作为*指标*或其他计算的输入中非常有用。

## 一个*逻辑运算符*的例子

上面的示例在`__init__`期间使用了一个算术运算符，后来在`next`中结合了`[0]`和逻辑运算符`>`。

因为运算符重载不仅限于*算术*，让我们举个例子，将一个指标添加进来。第一次尝试可能是：

```py
import backtrader as bt

class MyStrategy(bt.Strategy):

    def __init__(self):
        self.hi_lo_avg = (self.data.high + self.data.low) / 2.0
        self.sma = bt.indicators.SMA(period=30)

    def next(self):
        if self.hi_lo_avg[0] > self.sma[0]:
            print('we have a winner!')

...
...
cerebro.addstrategy(MyStrategy)
cerebro.run()
```

但在这种情况下，只是从`another_value`改为了`self.sma[0]`。让我们改进一下：

```py
import backtrader as bt

class MyStrategy(bt.Strategy):

    def __init__(self):
        self.hi_lo_avg = (self.data.high + self.data.low) / 2.0
        self.sma = bt.indicators.SMA(period=30)

    def next(self):
        if self.hi_lo_avg > self.sma:
            print('we have a winner!')

...
...
cerebro.addstrategy(MyStrategy)
cerebro.run()
```

为了好处。运算符重载在`next`中也是有效的，用户可以直接删除`[0]`并直接比较对象。

如果所有这些都是实际可能的，那实际上似乎有点过度。但好消息是还有更多。看看这个例子：

```py
import backtrader as bt

class MyStrategy(bt.Strategy):

    def __init__(self):
        hi_lo_avg = (self.data.high + self.data.low) / 2.0
        sma = bt.indicators.SMA(period=30)
        self.signal = hi_lo_avg > sma

    def next(self):
        if self.signal:
            print('we have a winner!')

...
...
cerebro.addstrategy(MyStrategy)
cerebro.run()
```

我们已经做了两件事：

1.  创建一个名为`self.signal`的*lines*对象，将*high-low-average*与*Simple Moving Average*的值进行比较。

    如上所述，当计算完成时，这个对象在`next`中是有用的。

1.  在检查`signal`是否为`True`时，去掉`next`中对`[0]`的使用。这是因为运算符也已经被重载用于布尔运算。

## 结论

希望这能让人们更清楚地了解在`__init__`中执行操作时实际发生了什么，以及运算符重载实际是如何进行的。
