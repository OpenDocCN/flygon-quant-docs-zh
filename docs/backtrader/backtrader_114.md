# 开发递归指标（带种子）

> 原文：[`www.backtrader.com/blog/posts/2018-01-27-recursive-indicators/recursive-indicator/`](https://www.backtrader.com/blog/posts/2018-01-27-recursive-indicators/recursive-indicator/)

*backtrader* 的最初目标之一是：

+   能够快速原型化指标以测试新想法

它不必是一个完美的指标，但能够快速轻松地开发它们确实有所帮助。为了确认设计是正确的，*backtrader*标准武器库中的第一个指标之一是*指数移动平均线*（又称*EMA*），根据定义是：**递归**。

注意

小知识：您可能会想象第一个指标是*简单移动平均线*

由于在[backtrader 社区](https://community.backtrader.com/topic/833/indicator-values-before-period-kicks-in)中发布了如何开发递归指标的问题，让我们快速开发一个`ExponentialMovingAverage`指标。

像这样的递归指标

+   它使用先前的值来计算当前值

您可以在[Wikipedia - 指数移动平均线](https://en.wikipedia.org/wiki/Moving_average#Exponential_moving_average)中看到数学示例

如果您足够勇敢读完所有内容，您会发现期间用于计算*指数平滑*。我们将使用它。

为了解决计算第一个值的难题，*行业*决定使用前`period`个值的简单平均值。

作为杠杆，我们将使用`bt.indicators.PeriodN`，它：

+   已经定义了一个`period`参数

+   通知框架关于最终用户使用的实际`period`

在此处查看其定义：[文档 - 指标参考](https://www.backtrader.com/docu/indautoref.html)

然后让我们开发我们的`EMA`

```py
`import backtrader as bt

class EMA(bt.indicators.PeriodN):
    params = {'period': 30}  # even if defined, we can redefine the default value
    lines = ('ema',)  # our output line

    def __init__(self):
        self.alpha = 2.0 / (1.0 + self.p.period)  # period -> exp smoothing factor

    def nextstart(self):  # calculate here the seed value
        self.lines.ema[0] = sum(self.data.get(size=self.p.period)) / self.p.period

    def next(self):
        ema1 = self.lines.ema[-1]  # previous EMA value
        self.lines.ema[0] = ema1 * (1.0 - self.alpha) + self.data[0] * self.alpha` 
```

几乎比说更容易。关键在于在`nextstart`中提供种子值，其中

+   当指标的最小预热期满足时将被调用一次。

    与`next`相反，它将为系统传递的每个新数据值调用

`nextstart`的默认实现只是将工作委托给`next`，对于大多数指标（例如*简单移动平均线*）来说，这是正确的做法。但在这种情况下，重写并提供种子值是关键。

## 将其与数据一起绘制

作为一个移动平均线，如果指标绘制在计算平均值的数据的同一轴上会很好。因为我们从`PeriodN`继承了绘图的默认值（在文档中查看）：

```py
`subplot=True` 
```

这当然意味着我们的指标将创建一个`subplot`（图表上的另一个轴）。这可以很容易地被覆盖。

```py
`import backtrader as bt

class EMA(bt.indicators.PeriodN):
    plot = dict(subplot=False)` 
```

完成。如果您想控制更多绘图选项，请查看[文档 - 绘图](https://www.backtrader.com/docu/plotting/plotting.html)

祝你好运！
