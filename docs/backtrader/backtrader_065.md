# Fillers

> 原文：[`www.backtrader.com/docu/filler/`](https://www.backtrader.com/docu/filler/)

当涉及使用成交量执行订单时，*backtrader* 经纪人模拟具有默认策略：

+   忽略成交量

这基于两个前提：

+   在足够流动的市场中进行交易，以完全吸收一次性 *买入/卖出* 订单

+   真实成交量匹配需要真实世界

    快速示例是 `Fill or Kill` 订单。即使到了 *tick* 分辨率并且有足够的 *fill* 量，*backtrader* 经纪人也无法知道市场上有多少额外的参与者，以区分这样一个订单是否会匹配以坚持 `Fill` 部分，或者该订单是否应该 `Kill`。

但是 *broker* 可以接受 *Volume Fillers*，这些填充器确定在给定时间点使用多少成交量用于 *order matching*。

## 填充器的签名

*backtrader* 生态系统中的一个 *filler* 可以是符合以下签名的任何 *callable*：

```py
callable(order, price, ago)
```

其中：

+   `order` 是将要执行的订单

    此对象提供对 `data` 对象的访问，该对象是操作目标，创建大小/价格，执行价格/大小/剩余大小和其他细节

+   订单将被执行的 `price`

+   `ago` 是在其中查找体积和价格元素的 *order* 中的 `data` 的索引

    在几乎所有情况下，这将是 `0`（当前时间点），但在一种角落情况下，以涵盖 `Close` 订单，这可能是 `-1`

    例如，要访问条形图的体积：

    ```py
    barvolume = order.data.volume[ago]` 
    ```

可调用对象可以是函数，也可以是例如支持 `__call__` 方法的类的实例，如：

```py
class MyFiller(object):
    def __call__(self, order, price, ago):
        pass
```

## 向经纪人添加一个 Filler

最直接的方法是使用 `set_filler`：

```py
import backtrader as bt

cerebro = Cerebro()
cerebro.broker.set_filler(bt.broker.fillers.FixedSize())
```

第二种选择是完全替换 `broker`，虽然这可能只适用于已重写部分功能的 `BrokerBack` 的子类：

```py
import backtrader as bt

cerebro = Cerebro()
filler = bt.broker.fillers.FixedSize()
newbroker = bt.broker.BrokerBack(filler=filler)
cerebro.broker = newbroker
```

## 示例

*backtrader* 源代码中包含一个名为 `volumefilling` 的示例，允许测试一些集成的 `fillers`（最初全部）

## 参考资料

#### class backtrader.fillers.FixedSize()

使用 *百分比* 的体积返回给定订单的执行大小。

此百分比通过参数 `perc` 设置

参数：

+   `size`（默认值：`None`）要执行的最大尺寸。如果执行时的实际体积小于大小，则也是限制

    如果此参数的值计算为 False，则整个条的体积将用于匹配订单

#### class backtrader.fillers.FixedBarPerc()

使用条中体积的 *百分比* 返回给定订单的执行大小。

此百分比通过参数 `perc` 设置

参数：

+   `perc`（默认值：`100.0`）（有效值：`0.0 - 100.0`）

    用于执行订单的成交量栏的百分比

#### class backtrader.fillers.BarPointPerc()

返回给定订单的执行大小。成交量将在 *high*-*low* 范围内均匀分布，使用 `minmov` 进行分区。

从给定价格的分配交易量中，将使用 `perc` 百分比

参数：

+   `minmov`（默认值：`0.01`）

    最小价格变动。用于将范围 *高*-*低* 分割，以在可能的价格之间按比例分配交易量

+   `perc`（默认值：`100.0`）（有效值：`0.0 - 100.0`）

    分配给订单执行价格的交易量百分比，用于匹配
