# 扩展数据源

> 原文：[`www.backtrader.com/docu/extending-a-datafeed/`](https://www.backtrader.com/docu/extending-a-datafeed/)

GitHub 上的问题实际上推动了完成文档部分或帮助我理解 `backtrader` 是否具有我最初设想的易用性和灵活性，以及沿途做出的决策。

在这种情况下是 [问题 #9](https://github.com/mementum/backtrader/issues/9)。

最终问题似乎归结为：

+   最终用户是否能轻松地扩展现有机制，以添加额外信息，比如像`open`、`high`等的其他现有价格信息点呢？

据我所知，问题的答案是：**是的**

发帖人似乎有以下需求（来自[问题 #6](https://github.com/mementum/backtrader/issues/6)）：

+   正在解析为 CSV 格式的数据源

+   使用 `GenericCSVData` 加载信息

    这种通用 csv 支持是针对这个 [问题 #6](https://github.com/mementum/backtrader/issues/6) 开发的。

+   这是一个额外的字段，显然包含需要传递的 P/E 信息，这些信息将传递给解析后的 CSV 数据。

让我们继续 CSV 数据源开发和 GenericCSVData 示例帖子。

步骤：

+   假设 P/E 信息被设置在解析后的 CSV 数据中。

+   使用 `GenericCSVData` 作为基类

+   将现有线（open/high/low/close/volumen/openinterest）扩展为 `pe`

+   给调用者添加一个参数，让其确定 P/E 信息的列位置。

结果：

```py
`from backtrader.feeds import GenericCSVData

class GenericCSV_PE(GenericCSVData):

    # Add a 'pe' line to the inherited ones from the base class
    lines = ('pe',)

    # openinterest in GenericCSVData has index 7 ... add 1
    # add the parameter to the parameters inherited from the base class
    params = (('pe', 8),)` 
```

工作完成了…

稍后，在策略中使用这个数据源时：

```py
`import backtrader as bt

....

class MyStrategy(bt.Strategy):

    ...

    def next(self):

        if self.data.close > 2000 and self.data.pe < 12:
            # TORA TORA TORA --- Get off this market
            self.sell(stake=1000000, price=0.01, exectype=Order.Limit)
    ...` 
```

## 绘制那条额外的 P/E 线

显然，数据源中的这行额外信息没有自动化的绘图支持。

最好的替代方法是对该行进行简单移动平均，并在单独的轴上绘制它：

```py
`import backtrader as bt
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
    ...` 
```
