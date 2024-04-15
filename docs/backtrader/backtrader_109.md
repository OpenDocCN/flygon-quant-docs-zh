# 动量策略

> 原文：[`www.backtrader.com/blog/2019-05-20-momentum-strategy/momentum-strategy/`](https://www.backtrader.com/blog/2019-05-20-momentum-strategy/momentum-strategy/)

在另一篇很棒的文章中，*Teddy Koker* 再次展示了 *算法交易* 策略的发展路径：

+   首先应用 `pandas` 进行研究

+   使用 `backtrader` 进行回测

真棒！！！

文章可以在以下位置找到：

+   [`teddykoker.com/2019/05/momentum-strategy-from-stocks-on-the-move-in-python/`](https://teddykoker.com/2019/05/momentum-strategy-from-stocks-on-the-move-in-python/)

*Teddy Koker* 给我留了言，询问我是否可以评论 *backtrader* 的使用。我的**观点**可以在下面看到。这仅仅是我的个人意见，因为作为 *backtrader* 的作者，我对如何最好地使用该平台有偏见。

我个人对某些结构如何表述的偏好，不必与其他人使用平台的偏好相匹配。

注意

实际上，让平台能够插入几乎任何内容，并以不同的方式执行相同的操作，是一个有意识的决定，让人们按照他们认为合适的方式使用它（在平台旨在实现的约束条件、语言可能性和我所做的失败设计决定的范围内）。

在这里，我们只关注可以以不同方式完成的事情。是否 *"不同"* 更好总是一个看法问题。而 *backtrader* 的作者并不总是必须在使用 *backtrader* 进行开发时始终正确（因为实际的开发必须适合开发者，而不是 *backtrader* 的作者）

## 参数：`dict` vs `tuple of tuples`

`backtrader` 提供的许多示例，以及文档和/或博客中提供的示例，都使用 `tuple of tuples` 模式进行参数设置。例如，来自代码的示例：

```py
`class Momentum(bt.Indicator):
    lines = ('trend',)
    params = (('period', 90),)` 
```

在这种范例中，一直有机会使用 `dict`。

```py
`class Momentum(bt.Indicator):
    lines = ('trend',)
    params = dict(period=90)  # or params = {'period': 90}` 
```

随着时间的推移，这种方式变得更易于使用，并成为作者首选的模式。

注意

作者更喜欢 `dict(period=90)`，因为它更易于输入，不需要引号。但是，许多其他人更喜欢花括号表示法，`{'period': 90}`。

`dict` 和 `tuple` 方法之间的根本区别：

+   使用 `tuple of tuples` 参数保留了声明顺序，这在枚举参数时可能很重要。

    提示

    在 Python `3.7`（以及 `3.6`，如果使用 *CPython*，即使这是一个实现细节）中，默认排序字典使声明顺序不会成为问题。

在下面作者修改的示例中，将使用 `dict` 表示法。

## `Momentum` 指标

在文章中，这就是指标的定义方式

```py
`class Momentum(bt.Indicator):
    lines = ('trend',)
    params = (('period', 90),)

    def __init__(self):
        self.addminperiod(self.params.period)

    def next(self):
        returns = np.log(self.data.get(size=self.p.period))
        x = np.arange(len(returns))
        slope, _, rvalue, _, _ = linregress(x, returns)
        annualized = (1 + slope) ** 252
        self.lines.trend[0] = annualized * (rvalue ** 2)` 
```

**使用力量**，即使用已经存在的东西，比如 `PeriodN` 指标，它：

+   已经定义了一个 `period` 参数，并知道如何将其传递给系统

因此，这可能更好

```py
`class Momentum(bt.ind.PeriodN):
    lines = ('trend',)
    params = dict(period=50)

    def next(self):
        ...` 
```

我们已经跳过了为了使用 `addminperiod` 而定义 `__init__` 的需要，这只应在特殊情况下使用。

继续进行，*backtrader* 定义了一个 `OperationN` 指标，它必须具有定义的属性 `func`，该属性将作为参数传递 `period` 个 bars，并将返回值放入定义的线中。

有了这个想法，一个人可以将以下内容想象成潜在的代码

```py
`def momentum_func(the_array):
    r = np.log(the_array)
    slope, _, rvalue, _, _ = linregress(np.arange(len(r)), r)
    annualized = (1 + slope) ** 252
    return annualized * (rvalue ** 2)

class Momentum(bt.ind.OperationN):
    lines = ('trend',)
    params = dict(period=50)
    func = momentum_func` 
```

这意味着我们已经将指标的复杂性移出了指标。我们甚至可以从外部库导入 `momentum_func`，如果底层函数发生变化，指标就不需要进行任何更改以反映新的行为。作为额外的奖励，我们拥有**纯粹**的声明性指标。没有 `__init__`，没有 `addminperiod`，也没有 `next`。

## 策略

让我们看看 `__init__` 部分。

```py
`class Strategy(bt.Strategy):
    def __init__(self):
        self.i = 0
        self.inds = {}
        self.spy = self.datas[0]
        self.stocks = self.datas[1:]

        self.spy_sma200 = bt.indicators.SimpleMovingAverage(self.spy.close,
                                                            period=200)
        for d in self.stocks:
            self.inds[d] = {}
            self.inds[d]["momentum"] = Momentum(d.close,
                                                period=90)
            self.inds[d]["sma100"] = bt.indicators.SimpleMovingAverage(d.close,
                                                                       period=100)
            self.inds[d]["atr20"] = bt.indicators.ATR(d,
                                                      period=20)` 
```

关于风格的一些事情：

+   尽可能使用参数而不是固定值

+   在大多数情况下，使用更短和更简洁的名称（例如用于导入）会增加可读性。

+   充分利用 Python

+   不要为数据源使用 `close`。通用地传递数据源，它将使用 close。这可能看起来不相关，但在尝试在各处保持代码的通用性时（比如在指标中），这确实有所帮助。

一个人应该考虑的第一件事：*如果可能的话，将一切都保持为参数*。因此

```py
`class Strategy(bt.Strategy):
    params = dict(
        momentum=Momentum,  # parametrize the momentum and its period
        momentum_period=90,

        movav=bt.ind.SMA,  # parametrize the moving average and its periods
        idx_period=200,
        stock_period=100,

        volatr=bt.ind.ATR,  # parametrize the volatility and its period
        vol_period=20,
    )

    def __init__(self):
        # self.i = 0  # See below as to why the counter is commented out
        self.inds = collections.defaultdict(dict)  # avoid per data dct in for

        # Use "self.data0" (or self.data) in the script to make the naming not
        # fixed on this being a "spy" strategy. Keep things generic
        # self.spy = self.datas[0]
        self.stocks = self.datas[1:]

        # Again ... remove the name "spy"
        self.idx_mav = self.p.movav(self.data0, period=self.p.idx_period)
        for d in self.stocks:
            self.inds[d]['mom'] = self.p.momentum(d, period=self.momentum_period)
            self.inds[d]['mav'] = self.p.movav(d, period=self.p.stock_period)
            self.inds[d]['vol'] = self.p.volatr(d, period=self.p.vol_period)` 
```

通过使用 `params` 并更改几个命名约定，我们使 `__init__`（以及策略）完全可定制且通用（任何地方都没有 `spy` 的引用）

### `next` 及其 `len`

*backtrader* 尽可能使用 Python 范式。它肯定有时会失败，但它会尝试。

让我们看看 `next` 发生了什么

```py
 `def next(self):
        if self.i % 5 == 0:
            self.rebalance_portfolio()
        if self.i % 10 == 0:
            self.rebalance_positions()
        self.i += 1` 
```

Python 的 `len` 范式正是所需之处。让我们来使用它。

```py
 `def next(self):
        l = len(self)
        if l % 5 == 0:
            self.rebalance_portfolio()
        if l % 10 == 0:
            self.rebalance_positions()` 
```

正如你所见，没有必要保留 `self.i` 计数器。策略和大多数对象的长度由系统一直提供、计算和更新。

### `next` 和 `prenext`

代码包含了这种转发

```py
 `def prenext(self):
        # call next() even when data is not available for all tickers
        self.next()` 
```

在进入 `next` 时没有保障

```py
 `def next(self):
        if self.i % 5 == 0:
            self.rebalance_portfolio()
        ...` 
```

好吧，我们知道正在使用一个无幸存者偏差的数据集，但一般来说，不保护 `prenext => next` 转发不是一个好主意。

+   *backtrader* 在所有缓冲区（指标、数据源）至少可以提供一个数据点时调用 `next`。一个 `100-bar` 移动平均线显然只有在从数据源获取了 100 个数据点时才会提供数据。

    这意味着在进入 `next` 时，数据源将有 `100 个数据点` 可供检查，而移动平均值只有 `1 个数据点`

+   *backtrader* 提供 `prenext` 作为钩子，让开发者在上述保证能够实现之前访问事物。例如，当有多个数据源并且它们的开始日期不同时，这是有用的。开发者可能希望在满足所有数据源（和相关指标）的所有保证之前进行一些检查或操作，并且在第一次调用 `next` 之前。

在一般情况下，`prenext => next`转发应该有这样的保护措施：

```py
 `def prenext(self):
        # call next() even when data is not available for all tickers
        self.next()

    def next(self):
        d_with_len = [d for d in self.datas if len(d)]
        ...` 
```

这意味着只有来自`self.datas`的子集`d_with_len`才能得到保证使用。

注意

对于指标也必须使用类似的保护措施。

因为对于策略的整个生命周期来说这样做似乎是毫无意义的，可以进行如此优化

```py
 `def __init__(self):
        ...
        self.d_with_len = []

    def prenext(self):
        # Populate d_with_len
        self.d_with_len = [d for d in self.datas if len(d)]
        # call next() even when data is not available for all tickers
        self.next()

    def nextstart(self):
        # This is called exactly ONCE, when next is 1st called and defaults to
        # call `next`
        self.d_with_len = self.datas  # all data sets fulfill the guarantees now

        self.next()  # delegate the work to next

    def next(self):
        # we can now always work with self.d_with_len with no calculation
        ...` 
```

保护计算已移至`prenext`，当保证满足时将停止调用它。然后将调用`nextstart`，通过重写它，我们可以重置数据集的`list`，以便与之一起工作，即：`self.datas`

并且通过这样做，所有保护措施都已从`next`中删除。

### 使用定时器的`next`

虽然作者的意图是每 5/10 天重新平衡（投资组合/头寸），但这可能意味着每周/两周重新平衡。

`len(self) % period`方法在以下情况下会失败：

+   数据集未从星期一开始

+   在交易假期期间，这将导致重新平衡脱离轨道

为了克服这一点，可以使用*backtrader*中的内置功能。

+   使用[Docs - Timers](https://www.backtrader.com/docu/timers/timers.html)

使用它们将确保在应该发生时进行重新平衡。让我们假设意图是在星期五重新平衡

让我们在我们的策略中为`params`和`__init__`增加一点魔法

```py
`class Strategy(bt.Strategy):
    params = dict(
       ...
       rebal_weekday=5,  # rebalance 5 is Friday
    )

    def __init__(self):
        ...
        self.add_timer(
            when=bt.Timer.SESSION_START,
            weekdays=[self.p.rebal_weekday],
            weekcarry=True,  # if a day isn't there, execute on the next
        )
        ...` 
```

现在我们已经准备好知道今天是星期五了。即使星期五恰好是交易日，添加`weekcarry=True`也确保我们会在星期一收到通知（或者如果星期一也是假日则为星期二，等等）

定时器的通知在`notify_timer`中进行

```py
`def notify_timer(self, timer, when, *args, **kwargs):
    self.rebalance_portfolio()` 
```

因为原始代码中还有每`10`个条形图进行一次`rebalance_positions`，所以可以：

+   添加第 2 个定时器，也适用于星期五

+   使用计数器只在每第 2 次调用时执行操作，甚至可以在定时器本身使用`allow=callable`参数

注意

定时器甚至可以更好地用于实现模式，比如：

+   每月的第 2 和第 4 个星期五重新平衡投资组合

+   `rebalance_positions`仅在每个月的第 4 个星期五进行。

### 一些额外的事项

其他一些事情可能纯粹是个人喜好的问题。

**个人喜好 1**

始终使用预先构建的比较而不是在`next`期间比较事物。例如来自代码（多次使用）

```py
 `if self.spy < self.spy_sma200:
            return` 
```

我们可以做以下事情。首先在`__init__`期间

```py
 `def __init__(self):
        ...
        self.spy_filter = self.spe < self.spy_sma200` 
```

以后

```py
 `if self.spy_filter:
            return` 
```

考虑到这一点，如果我们想要改变`spy_filter`条件，我们只需在`__init__`中执行一次，而不是在代码中的多个位置执行。

同样的情况也可能适用于此处的另一个比较`d < self.inds[d]["sma100"]`：

```py
 `# sell stocks based on criteria
        for i, d in enumerate(self.rankings):
            if self.getposition(self.data).size:
                if i > num_stocks * 0.2 or d < self.inds[d]["sma100"]:
                    self.close(d)` 
```

这也可以在`__init__`期间预先构建，并因此更改为如下所示

```py
 `# sell stocks based on criteria
        for i, d in enumerate(self.rankings):
            if self.getposition(self.data).size:
                if i > num_stocks * 0.2 or self.inds[d]['sma_signal']:
                    self.close(d)` 
```

**个人喜好 2**

将一切都作为参数。例如，在上面的几行中，我们看到一个`0.2`，它在代码的几个部分中都被使用：**将其作为参数**。同样，还有其他值，如`0.001`和`100`（实际上已经建议将其作为创建移动平均值的参数）。

将所有东西都作为参数，可以通过只改变*策略*的实例化而不是策略本身来打包代码并尝试不同的方法。
