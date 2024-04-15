# 用户自定义佣金

> 原文：[`www.backtrader.com/docu/user-defined-commissions/commission-schemes-subclassing/`](https://www.backtrader.com/docu/user-defined-commissions/commission-schemes-subclassing/)

重塑 CommInfo 对象到实际形式的最重要部分涉及：

+   保留原始的 `CommissionInfo` 类和行为

+   为轻松创建用户定义的佣金打开大门

+   将格式 xx% 设为新佣金方案的默认值而不是 0.xx（只是一种品味问题），保持行为可配置

注意

请参阅下面的 `CommInfoBase` 的文档字符串以获取参数参考

## 定义佣金方案

这涉及到 1 或 2 个步骤

1.  子类化 `CommInfoBase`

    简单地更改默认参数可能就足够了。`backtrader` 已经在模块 `backtrader.commissions` 中的一些定义中这样做了。期货的常规行业标准是每个合同和每轮的固定金额。定义如下：

    ```py
    `class CommInfo_Futures_Fixed(CommInfoBase):
        params = (
            ('stocklike', False),
            ('commtype', CommInfoBase.COMM_FIXED),
        )` 
    ```

    对于股票和百分比佣金：

    ```py
    `class CommInfo_Stocks_Perc(CommInfoBase):
        params = (
            ('stocklike', True),
            ('commtype', CommInfoBase.COMM_PERC),
        )` 
    ```

    如上所述，这里对百分比的解释的默认是：**xx%**。如果希望使用旧的/其他行为 **0.xx**，可以轻松实现：

    ```py
    `class CommInfo_Stocks_PercAbs(CommInfoBase):
        params = (
            ('stocklike', True),
            ('commtype', CommInfoBase.COMM_PERC),
            ('percabs', True),
        )` 
    ```

1.  覆盖（如果需要的话） `_getcommission` 方法

    定义如下：

    ```py
    `def _getcommission(self, size, price, pseudoexec):
      '''Calculates the commission of an operation at a given price

     pseudoexec: if True the operation has not yet been executed
     '''` 
    ```

    更多详细信息请参见下面的实际示例

## 如何应用到平台上

一旦 `CommInfoBase` 的子类就位，关键是使用 `broker.addcommissioninfo` 而不是通常的 `broker.setcommission`。后者将在内部使用传统的 `CommissionInfoObject`。

说起来容易做起来难：

```py
...

comminfo = CommInfo_Stocks_PercAbs(commission=0.005)  # 0.5%
cerebro.broker.addcommissioninfo(comminfo)
```

`addcommissioninfo` 方法定义如下：

```py
def addcommissioninfo(self, comminfo, name=None):
    self.comminfo[name] = comminfo
```

设置 `name` 意味着 `comminfo` 对象仅适用于具有该名称的资产。默认值 `None` 意味着它适用于系统中的所有资产。

## 一个实际的例子

[票号 #45](https://github.com/mementum/backtrader/issues/45) 询问适用于期货的佣金方案，是百分比方式，并在佣金计算中使用合同的“虚拟”价值的佣金百分比。即：在佣金计算中包括未来合约的倍数。

这应该很容易：

```py
import backtrader as bt

class CommInfo_Fut_Perc_Mult(bt.CommInfoBase):
    params = (
      ('stocklike', False),  # Futures
      ('commtype', bt.CommInfoBase.COMM_PERC),  # Apply % Commission
    # ('percabs', False),  # pass perc as xx% which is the default
    )

    def _getcommission(self, size, price, pseudoexec):
        return size * price * self.p.commission * self.p.mult
```

将其加入系统：

```py
comminfo = CommInfo_Fut_Perc_Mult(
    commission=0.1,  # 0.1%
    mult=10,
    margin=2000  # Margin is needed for futures-like instruments
)

cerebro.addcommissioninfo(comminfo)
```

如果格式 **0.xx** 被偏好为默认值，只需将参数 `percabs` 设置为 `True`：

```py
class CommInfo_Fut_Perc_Mult(bt.CommInfoBase):
    params = (
      ('stocklike', False),  # Futures
      ('commtype', bt.CommInfoBase.COMM_PERC),  # Apply % Commission
      ('percabs', True),  # pass perc as 0.xx
    )

comminfo = CommInfo_Fut_Perc_Mult(
    commission=0.001,  # 0.1%
    mult=10,
    margin=2000  # Margin is needed for futures-like instruments
)

cerebro.addcommissioninfo(comminfo)
```

这一切都应该行得通。

## 解释 `pseudoexec`

让我们回顾一下 `_getcommission` 的定义：

```py
def _getcommission(self, size, price, pseudoexec):
  '''Calculates the commission of an operation at a given price

 pseudoexec: if True the operation has not yet been executed
 '''
```

`pseudoexec` 参数的目的可能看起来很模糊，但它确实有其作用。

+   平台可能调用此方法来预先计算可用现金和一些其他任务

+   这意味着该方法可能（而且实际上会）使用相同的参数调用多次

`pseudoexec` 表示调用是否对应于订单的实际执行。虽然乍一看这可能似乎“不相关”，但如果考虑以下情景，它就很重要：

+   一家经纪人在合同数量超过 5000 单位后会给期货来回佣金打 5 折

    在这种情况下，如果没有`pseudoexec`，对该方法的多次非执行调用将迅速触发折扣已生效的假设。

将情景付诸实践：

```py
import backtrader as bt

class CommInfo_Fut_Discount(bt.CommInfoBase):
    params = (
      ('stocklike', False),  # Futures
      ('commtype', bt.CommInfoBase.COMM_FIXED),  # Apply Commission

      # Custom params for the discount
      ('discount_volume', 5000),  # minimum contracts to achieve discount
      ('discount_perc', 50.0),  # 50.0% discount
    )

    negotiated_volume = 0  # attribute to keep track of the actual volume

    def _getcommission(self, size, price, pseudoexec):
        if self.negotiated_volume > self.p.discount_volume:
           actual_discount = self.p.discount_perc / 100.0
        else:
           actual_discount = 0.0

        commission = self.p.commission * (1.0 - actual_discount)
        commvalue = size * price * commission

        if not pseudoexec:
           # keep track of actual real executed size for future discounts
           self.negotiated_volume += size

        return commvalue
```

现在，`pseudoexec`的目的和存在应该清楚了。

## CommInfoBase 文档字符串和参数

参见[佣金：股票 vs 期货](https://example.org/commissions_stocks_vs_futures)以获取`CommInfoBase`的参考。
