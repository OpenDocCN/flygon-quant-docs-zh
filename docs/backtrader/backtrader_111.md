# 改进随机的 Python 互联网学习笔记

> 原文：[`www.backtrader.com/blog/posts/2018-04-22-improving-code/improving-code/`](https://www.backtrader.com/blog/posts/2018-04-22-improving-code/improving-code/)

每隔一段时间，互联网上会出现带有*backtrader*代码的样本。在我看来有几个是中文。最新的一个在这里：

+   [`blog.csdn.net/qq_26948675/article/details/80016633`](https://blog.csdn.net/qq_26948675/article/details/80016633)

标题是：*backtrader-学习笔记 2*，显然（谢谢谷歌）被翻译为*backtrader-学习笔记 2*。如果那些是学习笔记，让我们尝试在那里真正可以改进代码的地方进行改进，在我个人看来，那就是*backtrader*最擅长的地方。

在学习笔记中策略的`__init__`方法中，我们发现以下内容

```py
`def __init__(self):
    ...
    self.ma1 = bt.indicators.SMA(self.datas[0],
                                   period=self.p.period
                                  )
    self.ma2 = bt.indicators.SMA(self.datas[1],
                                   period=self.p.period
                                  )` 
```

这里没有什么好争论的（风格是非常个人的事情，我不会触及那方面）

在策略的`next`方法中，以下是买入和卖出的逻辑决策。

```py
`...
# Not yet ... we MIGHT BUY if ...
if (self.ma1[0]-self.ma1[-1])/self.ma1[-1]>(self.ma2[0]-self.ma2[-1])/self.ma2[-1]:
...` 
```

和

```py
`...
# Already in the market ... we might sell
if (self.ma1[0]-self.ma1[-1])/self.ma1[-1]<=(self.ma2[0]-self.ma2[-1])/self.ma2[-1]:
...` 
```

这两个逻辑块是可以做得更好的，这样也会增加可读性、可维护性和调整性（如果需要的话）

不是将移动平均值的比较（当前点`0`和上一个点`-1`）后跟一些除法，让我们看看如何让它预先计算。

让我们调整`__init__`

```py
`def __init__(self):
    ...

    # Let's create the moving averages as before
    ma1 = bt.ind.SMA(self.data0, period=self.p.period)
    ma2 = bt.ind.SMA(self.data1, period=self.p.period)

    # Use line delay notation (-x) to get a ref to the -1 point
    ma1_pct = ma1 / ma1(-1) - 1.0  # The ma1 percentage part
    ma2_pct = ma2 / ma2(-1) - 1.0  # The ma2 percentage part

    self.buy_sig = ma1_pct > ma2_pct  # buy signal
    self.sell_sig = ma1_pct <= ma2_pct  # sell signal` 
```

现在我们可以将其带到`next`方法并执行以下操作：

```py
`def next(self):
    ...
    # Not yet ... we MIGHT BUY if ...
    if self.buy_sig:
    ...

    ...
    # Already in the market ... we might sell
    if self.sell_sig:
    ...` 
```

注意，我们甚至不必使用`self.buy_sig[0]`，因为通过`if self.buy_sig`进行的布尔测试已经被*backtrader*机制翻译成了对`[0]`的检查

在我看来，通过在`__init__`中使用标准算术和逻辑操作来定义逻辑（并使用行延迟符号`(-x)`）使代码变得更好。

无论如何，作为结束语，人们也可以尝试使用内置的`PercentChange`指标（又名`PctChange`）

参见：[backtrader 文档 - 指标参考](https://www.backtrader.com/docu/indautoref.html)

正如名称所示，它已经计算了一定周期内的百分比变化。现在`__init__`中的代码看起来是这样的

```py
`def __init__(self):
    ...

    # Let's create the moving averages as before
    ma1 = bt.ind.SMA(self.data0, period=self.p.period)
    ma2 = bt.ind.SMA(self.data1, period=self.p.period)

    ma1_pct = bt.ind.PctChange(ma1, period=1)  # The ma1 percentage part
    ma2_pct = bt.ind.PctChange(ma2, period=1)  # The ma2 percentage part

    self.buy_sig = ma1_pct > ma2_pct  # buy signal
    self.sell_sig = ma1_pct <= ma2_pct  # sell signal` 
```

在这种情况下，并没有太大的区别，但如果计算更大更复杂的话，这绝对可以为你省下很多麻烦。

祝愉快的*回溯交易*！
