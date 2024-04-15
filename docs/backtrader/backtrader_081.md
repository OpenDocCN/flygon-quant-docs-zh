# 观察者参考

> 原文：[`www.backtrader.com/docu/observers-reference/`](https://www.backtrader.com/docu/observers-reference/)

## 基准

#### 类 backtrader.observers.Benchmark()

此观察者存储策略的*回报*和作为系统传递的数据之一的参考资产的*回报*。

参数：

+   `timeframe`（默认：`None`）如果为`None`，则将报告整个回测期间的完整收益

+   `compression`（默认：`None`）

    仅用于子日时间框架，例如通过指定“TimeFrame.Minutes”和 60 作为压缩来处理小时时间框架

+   `data`（默认：`None`）

    用于跟踪的参考资产，以进行比较。

    **注意**：此数据必须已添加到 `cerebro` 实例中，并使用 `addata`、`resampledata` 或 `replaydata`。

+   `_doprenext`（默认：`False`）

    基准将从策略启动的时间点开始进行（即：当策略的最小周期已满足时）。

    将此设置为`True`将记录从数据源的起始点开始的基准值。

+   `firstopen`（默认：`False`）

    将其保持为`False`可确保价值与基准之间的第一个比较点从 0%开始，因为基准不会使用其开盘价。

    查看 `TimeReturn` 分析器参考以获取参数含义的全面解释

+   `fund`（默认：`None`）

    如果为`None`，则经纪人的实际模式（fundmode - True/False）将被自动检测以决定收益是基于总净资产值还是基金价值。请参阅经纪人文档中的 `set_fundmode`

    将其设置为`True`或`False`以获取特定行为

请记住，在`run`的任何时刻，可以通过查看*lines*的名称以索引`0`来检查当前值。

## 经纪人

#### 类 backtrader.observers.Broker(*args, **kwargs)

此观察者在经纪人中跟踪当前现金金额和投资组合价值（包括现金）

参数：无

### 经纪人 - 现金

#### 类 backtrader.observers.Cash(*args, **kwargs)

此观察者跟踪经纪人中当前现金金额

参数：无

### 经纪人 - 值

#### 类 backtrader.observers.Value(*args, **kwargs)

此观察者在经纪人中跟踪当前投资组合价值，包括现金。

参数：

+   `fund`（默认：`None`）

    如果为`None`，则经纪人的实际模式（fundmode - True/False）将被自动检测以决定收益是基于总净资产值还是基金价值。请参阅经纪人文档中的 `set_fundmode`

    将其设置为`True`或`False`以获取特定行为

## BuySell

#### 类 backtrader.observers.BuySell(*args, **kwargs)

此观察者跟踪个别买入/卖出订单（单独执行），并将它们绘制在图表上，沿着执行价格水平附近的数据

参数：

```py
* `barplot` (default: `False`) Plot buy signals below the minimum and
  sell signals above the maximum.

  If `False` it will plot on the average price of executions during a
  bar

* `bardist` (default: `0.015` 1.5%) Distance to max/min when
  `barplot` is `True`
```

## 回撤

#### 类 backtrader.observers.DrawDown()

此观察者跟踪当前回撤水平（绘制）和最大回撤（不绘制）水平

参数：

+   `fund`（默认：`None`）

    如果为`None`，则会自动检测经纪人的实际模式（基金模式 - True/False），以决定收益是基于总净资产值还是基金价值。请参阅经纪人文档中的`set_fundmode`

    将其设置为`True`或`False`以实现特定行为

## 时间收益

#### 类`backtrader.observers.TimeReturn()`

该观察器存储策略的*收益*。

参数：

+   `timeframe`（默认：`None`）如果为`None`，则将报告整个回测期间的完整收益

    将`TimeFrame.NoTimeFrame`传递以考虑整个数据集而没有时间约束

+   `compression`（默认：`None`）

    仅用于子日时间框架，例如通过指定“TimeFrame.Minutes”和 60 作为压缩来处理小时时间框架

+   `fund`（默认：`None`）

    如果为`None`，则会自动检测经纪人的实际模式（基金模式 - True/False），以决定收益是基于总净资产值还是基金价值。请参阅经纪人文档中的`set_fundmode`

    将其设置为`True`或`False`以实现特定行为

请记住，在`run`的任何时刻，可以通过查看索引为`0`的*名称*的*行*来检查当前值。

## 交易

#### 类`backtrader.observers.Trades()`

该观察器跟踪完整交易并绘制在关闭交易时实现的 PnL 水平。

当仓位从 0（或越过 0）变为 X 时，交易处于开放状态，然后在回到 0（或以相反方向越过 0）时关闭

参数：

```py
* `pnlcomm` (def: `True`)

  Show net/profit and loss, i.e.: after commission. If set to `False`
  if will show the result of trades before commission
```

## 对数收益

#### 类`backtrader.observers.LogReturns()`

该观察器存储策略或 a 的*对数收益*

参数：

+   `timeframe`（默认：`None`）如果为`None`，则将报告整个回测期间的完整收益

    将`TimeFrame.NoTimeFrame`传递以考虑整个数据集而没有时间约束

+   `compression`（默认：`None`）

    仅用于子日时间框架，例如通过指定“TimeFrame.Minutes”和 60 作为压缩来处理小时时间框架

+   `fund`（默认：`None`）

    如果为`None`，则会自动检测经纪人的实际模式（基金模式 - True/False），以决定收益是基于总净资产值还是基金价值。请参阅经纪人文档中的`set_fundmode`

    将其设置为`True`或`False`以实现特定行为

请记住，在`run`的任何时刻，可以通过查看索引为`0`的*名称*的*行*来检查当前值。

## 对数收益 2

#### 类`backtrader.observers.LogReturns2()`

扩展观察器 LogReturns 以显示两个工具

## 基金价值

#### 类`backtrader.observers.FundValue(*args, **kwargs)`

该观察器跟踪当前基金样式的值

参数：无

## 基金份额

#### 类`backtrader.observers.FundShares(*args, **kwargs)`

该观察器跟踪当前基金样式的份额

参数：无
