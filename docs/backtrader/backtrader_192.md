# 扩展数据源

> 原文：[`www.backtrader.com/blog/posts/2015-08-07-extending-a-datafeed/extending-a-datafeed/`](https://www.backtrader.com/blog/posts/2015-08-07-extending-a-datafeed/extending-a-datafeed/)

GitHub 上的问题实际上正在推动完成文档部分或帮助我了解 backtrader 是否具有我最初设想的易用性和灵活性，并沿途做出的决定。

在这种情况下是[问题＃9](https://github.com/mementum/backtrader/issues/9)。

最终问题似乎归结为：

+   最终用户是否可以轻松扩展现有机制，以添加额外信息，以行的形式传递到其他现有价格信息点，如`open`，`high`等？

就我理解的问题，答案是：**是的**

发帖人似乎有这些要求（来自[问题＃6](https://github.com/mementum/backtrader/issues/6)）：

+   一个被解析为 CSV 格式的数据源

+   使用`GenericCSVData`来加载信息

    这种通用的 CSV 支持是为了响应这个[问题＃6](https://github.com/mementum/backtrader/issues/6)而开发的

+   一个额外字段，显然包含需要传递到解析的 CSV 数据中的 P/E 信息

让我们在 CSV 数据源开发和通用 CSV 数据源示例帖子的基础上构建。

步骤：

+   假设 P/E 信息被设置在被解析的 CSV 数据中

+   使用`GenericCSVData`作为基类

+   将现有线（open/high/low/close/volumen/openinterest）扩展为`pe`

+   添加一个参数，让调用者确定 P/E 信息的列位置

结果：

```py
from backtrader.feeds import GenericCSVData

class GenericCSV_PE(GenericCSVData):

    # Add a 'pe' line to the inherited ones from the base class
    lines = ('pe',)

    # openinterest in GenericCSVData has index 7 ... add 1
    # add the parameter to the parameters inherited from the base class
    params = (('pe', 8),)
```

工作完成了...

稍后，在策略中使用这个数据源时：

```py
import backtrader as bt

....

class MyStrategy(bt.Strategy):

    ...

    def next(self):

        if self.data.close > 2000 and self.data.pe < 12:
            # TORA TORA TORA --- Get off this market
            self.sell(stake=1000000, price=0.01, exectype=Order.Limit)
    ...
```

## 绘制额外的 P/E 线

显然，在数据源中没有对该额外线的自动绘图支持。

最好的选择是对该线进行简单移动平均，并在单独的轴上绘制它：

```py
import backtrader as bt
import backtrader.indicators as btind

....

class MyStrategy(bt.Strategy):

    def __init__(self):

        # The indicator autoregisters and will plot even if no obvious
        # reference is kept to it in the class
        btind.SMA(self.data.pe, period=1, subplot=False)

    ...

    def next(self):

        if self.data.close > 2000 and self.data.pe < 12:
            # TORA TORA TORA --- Get off this market
            self.sell(stake=1000000, price=0.01, exectype=Order.Limit)
    ...
```
