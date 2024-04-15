# 策略参考

> 原文：[`www.backtrader.com/docu/strategy-reference/`](https://www.backtrader.com/docu/strategy-reference/)

内置策略的参考

### MA_CrossOver

别名：

```py
`* SMA_CrossOver` 
```

这是一个仅限开多头的策略，基于移动平均线交叉

注意：

```py
`* Although the default` 
```

买入逻辑：

```py
`* No position is open on the data

* The `fast` moving averagecrosses over the `slow` strategy to the
  upside.` 
```

卖出逻辑：

```py
`* A position exists on the data

* The `fast` moving average crosses over the `slow` strategy to the
  downside` 
```

订单执行类型：

```py
`* Market` 
```

线：

```py
`* datetime` 
```

参数：

```py
`* fast (10)

* slow (30)

* _movav (<class ‘backtrader.indicators.sma.SMA’>)` 
```

### SignalStrategy

这个`Strategy`的子类旨在使用**信号**自动运行。

*信号*通常是指标，预期输出值为：

+   `> 0`是一个`long`指示

+   `< 0`是一个`short`指示

有 5 种*信号*类型，分为 2 组。

**主要组**：

+   `LONGSHORT`：此信号的`long`和`short`指示都会被采纳

+   `LONG`：

    +   `long`指示被认为是开多头

    +   `short`指示被认为是用于*关闭*多头仓位。但是：

    +   如果系统中存在`LONGEXIT`（见下文）信号，则会用于退出多头

    +   如果有`SHORT`信号可用且没有`LONGEXIT`可用，则会用于在开启`short`之前关闭`long`

+   `SHORT`：

    +   `short`指示被认为是开空头

    +   `long`指示被认为是用于*关闭*空头仓位。但是：

    +   如果系统中存在`SHORTEXIT`（见下文）信号，则会用于退出空头

    +   如果有`LONG`信号可用且没有`SHORTEXIT`可用，则会用于在开启`long`之前关闭`short`

**退出组**：

这两个信号旨在覆盖其他信号并提供退出`long`/`short`仓位的标准。

+   `LONGEXIT`：`short`指示被认为是用于退出`long`仓位

+   `SHORTEXIT`：`long`指示被认为是用于退出`short`仓位

**订单发出**

订单执行类型为`Market`，有效性为`None`（*直到取消为止*）

参数：

+   `signals`（默认：`[]`）：一个列表/元组的列表/元组，允许实例化信号并分配到正确的类型

    预计通过`cerebro.add_signal`来管理此参数

+   `_accumulate`（默认：`False`）：允许进入市场（多头/空头），即使已经在市场中

+   `_concurrent`（默认：`False`）：允许即使有订单正在等待执行，也可以发出订单

+   `_data`（默认：`None`）：如果系统中存在多个数据，这是订单的目标。这可以是

    +   `None`：系统中的第一个数据将被使用

    +   一个`int`：表示在该位置插入的数据

    +   一个`str`：在创建数据时给定的名称（参数`name`）或在使用`cerebro.adddata(..., name=)`将其添加到 cerebro 时给定的名称

    +   一个`data`实例

线：

```py
`* datetime` 
```

参数：

```py
`* signals ([])

* _accumulate (False)

* _concurrent (False)

* _data (None)` 
```
