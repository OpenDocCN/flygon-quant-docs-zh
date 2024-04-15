# 用户定义的佣金

> 原文：[`www.backtrader.com/blog/posts/2015-11-20-commission-schemes-subclassing/commission-schemes-subclassing/`](https://www.backtrader.com/blog/posts/2015-11-20-commission-schemes-subclassing/commission-schemes-subclassing/)

佣金方案实现不久前进行了重新设计。最重要的部分是：

+   保留原始的 CommissionInfo 类和行为

+   为轻松创建用户定义的佣金打开大门

+   将格式 xx%设置为新佣金方案的默认值，而不是 0.xx（只是一种口味），保持行为可配置

在扩展佣金中概述了基础知识。

注意

请参见下文`CommInfoBase`的文档字符串以获取参数参考

## 定义佣金方案

它涉及 1 或 2 个步骤

1.  子类化`CommInfoBase`

    简单地更改默认参数可能就足够了。`backtrader`已经在模块`backtrader.commissions`中对一些定义进行了这样的更改。期货的常规行业标准是每合约和每轮固定金额。定义可以如下进行：

    ```py
    class CommInfo_Futures_Fixed(CommInfoBase):
        params = (
            ('stocklike', False),
            ('commtype', CommInfoBase.COMM_FIXED),
        )` 
    ```

    对于股票和百分比佣金：

    ```py
    class CommInfo_Stocks_Perc(CommInfoBase):
        params = (
            ('stocklike', True),
            ('commtype', CommInfoBase.COMM_PERC),
        )` 
    ```

    如上所述，百分比的默认解释（作为参数`commission`传递）为：**xx%**。如果希望使用旧的/其他行为 **0.xx**，可以轻松实现：

    ```py
    class CommInfo_Stocks_PercAbs(CommInfoBase):
        params = (
            ('stocklike', True),
            ('commtype', CommInfoBase.COMM_PERC),
            ('percabs', True),
        )` 
    ```

1.  覆盖（如果需要）`_getcommission`方法

    定义如下：

    ```py
    def _getcommission(self, size, price, pseudoexec):
       '''Calculates the commission of an operation at a given price

       pseudoexec: if True the operation has not yet been executed
       '''` 
    ```

    更多实际示例的细节见下文

## 如何将其应用于平台

一旦`CommInfoBase`子类就位，诀窍就是使用`broker.addcommissioninfo`而不是通常的`broker.setcommission`。后者将在内部使用旧有的`CommissionInfoObject`。

说起来比做起来容易：

```py
...

comminfo = CommInfo_Stocks_PercAbs(commission=0.005)  # 0.5%
cerebro.broker.addcommissioninfo(comminfo)
```

`addcommissioninfo`方法的定义如下：

```py
def addcommissioninfo(self, comminfo, name=None):
    self.comminfo[name] = comminfo
```

设置`name`意味着`comminfo`对象只适用于具有该名称的资产。`None`的默认值意味着它适用于系统中的所有资产。

## 一个实际的示例

[Ticket #45](https://github.com/mementum/backtrader/issues/45)要求有一个适用于期货的佣金方案，按比例计算，并使用合同的整个“虚拟”价值的佣金百分比。即：在佣金计算中包括期货乘数。

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

将其放入系统中：

```py
comminfo = CommInfo_Fut_Perc_Mult(
    commission=0.1,  # 0.1%
    mult=10,
    margin=2000  # Margin is needed for futures-like instruments
)

cerebro.broker.addcommission(comminfo)
```

如果默认值偏好格式为 **0.xx**，只需将参数`percabs`设置为`True`：

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

cerebro.broker.addcommissioninfo(comminfo)
```

所有这些都应该奏效。

## 解释`pseudoexec`

让我们回顾一下`_getcommission`的定义：

```py
def _getcommission(self, size, price, pseudoexec):
    '''Calculates the commission of an operation at a given price

    pseudoexec: if True the operation has not yet been executed
    '''
```

`pseudoexec`参数的目的可能看起来很模糊，但它确实有用。

+   平台可能会调用此方法进行可用现金的预先计算和一些其他任务

+   这意味着该方法可能会（而且实际上会）多次使用相同的参数进行调用。

`pseudoexec` 指示调用是否对应于实际执行订单。虽然乍一看这可能看起来“不相关”，但如果考虑到以下情景，它就是相关的：

+   一家经纪人在协商合同数量超过 5000 单位后，将提供期货往返佣金 50%的折扣。

    在这种情况下，如果没有 `pseudoexec`，对该方法的多次非执行调用将很快触发折扣已经生效的假设。

将场景投入实际运作：

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

`pseudoexec` 的目的和存在意义现在应该是清楚的了。

## CommInfoBase 文档字符串和参数

这里是：

```py
class CommInfoBase(with_metaclass(MetaParams)):
    '''Base Class for the Commission Schemes.

    Params:

      - commission (def: 0.0): base commission value in percentage or monetary
        units

      - mult (def 1.0): multiplier applied to the asset for value/profit

      - margin (def: None): amount of monetary units needed to open/hold an
        operation. It only applies if the final ``_stocklike`` attribute in the
        class is set to False

      - commtype (def: None): Supported values are CommInfoBase.COMM_PERC
        (commission to be understood as %) and CommInfoBase.COMM_FIXED
        (commission to be understood as monetary units)

        The default value of ``None`` is a supported value to retain
        compatibility with the legacy ``CommissionInfo`` object. If
        ``commtype`` is set to None, then the following applies:

          - margin is None: Internal _commtype is set to COMM_PERC and
            _stocklike is set to True (Operating %-wise with Stocks)

          - margin is not None: _commtype set to COMM_FIXED and _stocklike set
            to False (Operating with fixed rount-trip commission with Futures)

        If this param is set to something else than None, then it will be
        passed to the internal ``_commtype`` attribute and the same will be
        done with the param ``stocklike`` and the internal attribute
        ``_stocklike``

      - stocklike (def: False):  Indicates if the instrument is Stock-like or
        Futures-like (see the ``commtype`` discussion above)

      - percabs (def: False): when ``commtype`` is set to COMM_PERC, whether
        the parameter ``commission`` has to be understood as XX% or 0.XX

        If this param is True: 0.XX
        If this param is False: XX%

    Attributes:

      - _stocklike: Final value to use for Stock-like/Futures-like behavior
      - _commtype: Final value to use for PERC vs FIXED commissions

      This two are used internally instead of the declared params to enable the
      compatibility check described above for the legacy ``CommissionInfo``
      object
    '''

    COMM_PERC, COMM_FIXED = range(2)

    params = (
        ('commission', 0.0), ('mult', 1.0), ('margin', None),
        ('commtype', None),
        ('stocklike', False),
        ('percabs', False),
    )
```
