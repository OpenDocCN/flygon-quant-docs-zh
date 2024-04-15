# 位置

> 原文：[`www.backtrader.com/docu/position/`](https://www.backtrader.com/docu/position/)

通常在策略内部检查资产的持仓情况：

+   `position`（一种属性）或 `getposition(data=None, broker=None)`

    这将返回由 cerebro 提供的默认 `broker` 中策略的 `datas[0]` 上的位置

一个持仓只是指：

+   资产持有 `size`

+   平均价格为 `price`

它作为一种状态，并且例如可以用来决定是否要发出订单（例如：仅在没有持仓时才进入多头持仓）

## 参考：Position

#### 类 backtrader.position.Position(size=0, price=0.0)

保持并更新持仓的大小和价格。该对象与任何资产无关，仅保留大小和价格。

成员属性：

```py
`* size (int): current size of the position

* price (float): current price of the position` 
```

Position 实例可以使用 len(position) 进行测试，以查看大小是否为空
