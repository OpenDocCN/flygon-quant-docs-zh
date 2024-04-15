# 佣金：信用

> 原文：[`www.backtrader.com/docu/commission-credit/`](https://www.backtrader.com/docu/commission-credit/)

在某些情况下，真实经纪人的现金金额可能会减少，因为资产操作包括利率。例如：

+   股票空头交易

+   ETF 即多头又空头

收费直接影响经纪账户的现金余额。但它仍然可以被视为佣金方案的一部分。因此，它已经在*backtrader*中建模。

`CommInfoBase`类（以及与之相关的`CommissionInfo`主接口对象）已经扩展了：

+   两个新参数，允许设置利率，并确定是否仅应用于空头或同时适用于多头和空头

## 参数

+   `interest`（默认：`0.0`）

    如果这个值不为零，则这是持有空头头寸的年利息。这主要是针对股票空头交易的

    应用的默认公式：`days * price * size * (interest / 365)`

    必须以绝对值指定：0.05 -> 5%

    注意

    可以通过重写方法`get_credit_interest`来改变行为

+   `interest_long`（默认：`False`）

    一些产品（如 ETF）在空头和多头头寸上都会被收取利息。如果这是`True`，并且`interest`不为零，利息将在两个方向上都收取

## 公式

默认实现将使用以下公式：

```py
`days * abs(size) * price * (interest / 365)` 
```

其中：

+   `days`：自仓位开启或上次计算信用利息以来经过的天数

## 重写公式

为了改变`CommissionInfo`的公式子类化是必需的。需要被重写的方法是：

```py
`def _get_credit_interest(self, size, price, days, dt0, dt1):
  '''
 This method returns  the cost in terms of credit interest charged by
 the broker.

 In the case of ``size > 0`` this method will only be called if the
 parameter to the class ``interest_long`` is ``True``

 The formulat for the calculation of the credit interest rate is:

 The formula: ``days * price * abs(size) * (interest / 365)``

 Params:
 - ``data``: data feed for which interest is charged

 - ``size``: current position size. > 0 for long positions and < 0 for
 short positions (this parameter will not be ``0``)

 - ``price``: current position price

 - ``days``: number of days elapsed since last credit calculation
 (this is (dt0 - dt1).days)

 - ``dt0``: (datetime.datetime) current datetime

 - ``dt1``: (datetime.datetime) datetime of previous calculation

 ``dt0`` and ``dt1`` are not used in the default implementation and are
 provided as extra input for overridden methods
 '''` 
```

可能是*经纪人*在计算利率时不考虑周末或银行假日。在这种情况下，这个子类会奏效

```py
`import backtrader as bt

class MyCommissionInfo(bt.CommInfo):

   def _get_credit_interest(self, size, price, days, dt0, dt1):
       return 1.0 * abs(size) * price * (self.p.interest / 365.0)` 
```

在这种情况下，在公式中：

+   `days`已被`1.0`替代

因为如果周末/银行假日不计入，下一次计算将始终在上次计算后的`1`个交易日后发生
