# 一个动态指标

> 原文：[`www.backtrader.com/blog/posts/2018-02-06-dynamic-indicator/dynamic-indicator/`](https://www.backtrader.com/blog/posts/2018-02-06-dynamic-indicator/dynamic-indicator/)

指标是棘手的东西。不是因为它们在一般情况下很难编码，而是因为名称具有误导性，并且人们对指标有不同的期望。

让我们至少尝试定义在*backtrader*生态系统内什么是*指标*。

它是一个定义了至少一个输出*线*的对象，可能定义影响其行为的参数，并接受一个或多个数据源作为输入。

为了尽可能使指标通用，选择了以下设计原则：

+   输入数据源可以是任何看起来像数据源的东西，这带来了一个直接的优势：因为其他指标看起来像数据源，所以可以将指标作为输入传递给其他指标。

+   不携带`datetime` *线*负载。这是因为输入可能没有自己的`datetime`负载进行同步。并且根据系统范围内的通用`datetime`进行同步可能是不正确的，因为指标可能正在使用来自*周*时间框架的数据，而系统时间可能以*秒*为单位进行计时，因为这是多个数据源之一的最低分辨率。

+   操作必须是幂等的，即：如果使用相同的输入两次调用且参数没有更改，则输出必须相同。

    请注意，可以要求指标在相同的时间点使用相同的输入多次执行操作。尽管这似乎是不需要的，但如果系统支持*数据重放*（即：从较小的时间框架实时构建较大的时间框架），则需要这样做。

+   最后：*指标*将其输出值写入当前时间的时刻，即：索引`0`。否则它将被命名为`Study`。`Study`将寻找模式并在过去写入输出值。

    例如请参阅[Backtrader 社区 - ZigZag](https://community.backtrader.com/topic/773/zigzag-indicator/)

一旦（在*backtrader*生态系统中）定义清晰，让我们尝试看看如何实际编写一个*动态*指标。似乎我们不能，因为从上述的设计原则来看，指标的操作过程多多少少是……不可变的。

## 自从……以来的最高高点

通常启动的一个指标是`Highest`（别名为`MaxN`），以获得给定周期内的*最高*某物。如

```py
`import backtrader as bt

class MyStrategy(bt.Strategy)
    def __init__(self):
        self.the_highest_high_15 = bt.ind.Highest(self.data.high, period=15)

    def next(self):
        if self.the_highest_high_15 > X:
            print('ABOUT TO DO SOMETHING')` 
```

在此代码片段中，我们实例化`Highest`以跟踪最近 15 个周期内的最高高点。如果最高高点大于`X`，将执行某些操作。

这里的关键是：

+   *周期*被固定为`15`

## 使其动态

有时，我们需要指标是动态的，并且根据实时条件改变其行为。例如，请参阅 *backtrader* 社区中的这个问题：[自开仓以来的最高高点](https://community.backtrader.com/topic/850/highest-high-since-position-was-opened/)

当然，我们不知道何时会开/平仓，并且将 `period` 设置为固定值如 `15` 是没有意义的。让我们看看我们如何做，将所有东西打包到一个指标中

### 动态参数

我们首先将使用我们将在指标生命周期中更改的参数，通过它实现动态性。

```py
`import backtrader as bt

class DynamicHighest(bt.Indicator):
    lines = ('dyn_highest',)
    params = dict(tradeopen=False)

    def next(self):
        if self.p.tradeopen:
            self.lines.dyn_highest[0] = max(self.data[0], self.dyn_highest[-1])

class MyStrategy(bt.Strategy)
    def __init__(self):
        self.dyn_highest = DynamicHighest(self.data.high)

    def notify_trade(self, trade):
        self.dyn_highest.p.tradeopen = trade.isopen

    def next(self):
        if self.dyn_highest > X:
            print('ABOUT TO DO SOMETHING')` 
```

让我们来吧！我们拥有它，到目前为止我们还没有违反为我们的指标制定的规则。让我们看看指标

+   它定义了一个名为 `dyn_highest` 的输出 *line*

+   它有一个参数 `tradeopen=False`

+   （是的，它接受数据源，只是因为它是 `Indicator` 的子类）

+   如果我们总是使用相同的输入调用 `next`，它将始终返回相同的值

唯一的事情：

+   如果参数的值发生变化，则输出也会发生变化（上面的规则说，只要参数不发生变化，输出保持不变）

我们在 `notify_trade` 中使用这个来影响我们的 `DynamicHighest`

+   我们使用通知的 `trade` 的值 `isopen` 作为一个标志，以知道我们是否需要记录输入数据的最高点

+   当 `trade` 关闭时，`isopen` 的值将为 `False`，我们将停止记录最高值

供参考：[Backtrader Documentation Trade](https://www.backtrader.com/docu/trade.html)

简单！！！

### 使用方法

有些人会反对修改在指标声明中的 `param`，并且应该只在实例化期间设置。

好的，让我们使用一个方法。

```py
`import backtrader as bt

class DynamicHighest(bt.Indicator):
    lines = ('dyn_highest',)

    def __init__(self):
        self._tradeopen = False

    def tradeopen(self, yesno):
        self._tradeopen = yesno

    def next(self):
        if self._tradeopen:
            self.lines.dyn_highest[0] = max(self.data[0], self.dyn_highest[-1])

class MyStrategy(bt.Strategy)
    def __init__(self):
        self.dyn_highest = DynamicHighest(self.data.high)

    def notify_trade(self, trade):
        self.dyn_highest.tradeopen(trade.isopen)

    def next(self):
        if self.dyn_highest > X:
            print('ABOUT TO DO SOMETHING')` 
```

并没有太大的区别，但现在指标有了一些额外的样板代码，包括 `__init__` 和方法 `tradeopen(self, yesno)`。但是我们的 `DynamicHighest` 的动态性是相同的。

### 奖励：让它变成通用目的

让我们恢复 `params` 并使指标成为可以应用不同函数而不仅仅是 `max` 的指标

```py
`import backtrader as bt

class DynamicFn(bt.Indicator):
    lines = ('dyn_highest',)
    params = dict(fn=None)

    def __init__(self):
        self._tradeopen = False
        # Safeguard for not set function
        self._fn = self.p.fn or lambda x, y: x

    def tradeopen(self, yesno):
        self._tradeopen = yesno

    def next(self):
        if self._tradeopen:
            self.lines.dyn_highest[0] = self._fn(self.data[0], self.dyn_highest[-1])

class MyStrategy(bt.Strategy)
    def __init__(self):
        self.dyn_highest = DynamicHighest(self.data.high, fn=max)

    def notify_trade(self, trade):
        self.dyn_highest.tradeopen(trade.isopen)

    def next(self):
        if self.dyn_highest > X:
            print('ABOUT TO DO SOMETHING')` 
```

说了做到！我们已经添加了：

+   `params=dict(fn=None)`

    收集最终用户想要使用的函数

+   如果用户没有传递特定函数，则使用占位符函数的保护措施：

    ```py
    `# Safeguard for not set function
    self._fn = self.p.fn or lambda x, y: x` 
    ```

+   并且我们使用函数（或占位符）进行计算：

    ```py
    `self.lines.dyn_highest[0] = self._fn(self.data[0], self.dyn_highest[-1])` 
    ```

+   在我们（现在命名为）`DynamicFn` 指标的调用中声明我们想要使用的函数… `max`（这里没有惊喜）：

    ```py
    `self.dyn_highest = DynamicHighest(self.data.high, fn=max)` 
    ```

今天剩下的不多了…享受它！！！
