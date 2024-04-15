# 分析员参考

> 原文：[`www.backtrader.com/docu/analyzers-reference/`](https://www.backtrader.com/docu/analyzers-reference/)

## AnnualReturn

#### class backtrader.analyzers.AnnualReturn()

此分析器通过查看年初和年末来计算年度回报

参数：

+   （无）

成员属性：

+   `rets`：计算的年度回报列表

+   `ret`：年度回报的字典（键：年份）

**get_analysis**:

+   返回年度回报的字典（键：年份）

## Calmar

#### class backtrader.analyzers.Calmar()

此分析器计算 CalmarRatio 时间范围，该范围可能与基础数据中使用的范围不同 参数：

+   `timeframe`（默认：`None`）如果为`None`，则系统中第 1 个数据的`timeframe`将被使用

    传递`TimeFrame.NoTimeFrame`以考虑没有时间约束的整个数据集

+   `compression`（默认：`None`）

    仅用于次日时间范围，例如通过指定“TimeFrame.Minutes”和 60 作为压缩来在小时时间范围上工作

    如果为`None`，则将使用系统的第 1 个数据的压缩

+   *None*

+   `fund`（默认：`None`）

    如果为`None`，则经纪人的实际模式（fundmode - True/False）将被自动检测，以决定收益是否基于总净资产价值或基金价值。参见经纪人文档中的`set_fundmode`

    将其设置为`True`或`False`以获得特定行为

#### - ``get_analysis``()

返回具有时间段键和相应滚动 Calmar 比率的 OrderedDict

#### - ``calmar``最新计算的 calmar 比率()

## 回撤

#### class backtrader.analyzers.DrawDown()

此分析器计算交易系统的回撤统计数据，例如以％为单位和以美元为单位的回撤值，以％为单位和以美元为单位的最大回撤，回撤长度和最大回撤长度

参数：

+   `fund`（默认：`None`）

    如果为`None`，则经纪人的实际模式（fundmode - True/False）将被自动检测，以决定收益是否基于总净资产价值或基金价值。参见经纪人文档中的`set_fundmode`

    将其设置为`True`或`False`以获得特定行为

#### - ``get_analysis``()

返回一个字典（支持点表示法和子字典）作为值的回撤统计，可用以下键/属性：

+   `drawdown` - 0.xx％的回撤值

+   `moneydown` - 货币单位中的回撤值

+   `len` - 回撤长度

+   `max.drawdown` - 0.xx％的最大回撤值

+   `max.moneydown` - 最大资金单位中的最大回撤值

+   `max.len` - 最大回撤长度

## TimeDrawDown

#### class backtrader.analyzers.TimeDrawDown()

此分析器计算所选时间范围内的交易系统回撤，该时间范围可能与基础数据中使用的范围不同 参数：

+   `timeframe`（默认：`None`）如果为`None`，则系统中第 1 个数据的`timeframe`将被使用

    传递`TimeFrame.NoTimeFrame`以考虑没有时间约束的整个数据集

+   `compression`（默认：`None`）

    仅用于亚日时间框架，例如通过指定“TimeFrame.Minutes”和 60 作为压缩来在小时时间框架上工作

    如果为`None`，则将使用系统第一条数据的压缩

+   *None*

+   `fund`（默认：`None`）

    如果为`None`，则将自动检测经纪人的实际模式（fundmode - True/False），以决定收益是基于总净资产价值还是基金价值。请参阅经纪人文档中的`set_fundmode`

    将其设置为`True`或`False`以获得特定行为

#### - ``get_analysis``()

返回一个字典（支持`.`表示法和子字典）作为值的回撤统计信息，可用的键/属性如下：

+   `drawdown` - 回撤值为 0.xx %

+   `maxdrawdown` - 回撤值为货币单位

+   `maxdrawdownperiod` - 回撤长度

#### - 这些在运行时作为属性可用()

+   `dd`

+   `maxdd`

+   `maxddlen`

## 总杠杆

#### 类`backtrader.analyzers.GrossLeverage()`

此分析器按时间框架计算当前策略的总杠杆

参数：

+   `fund`（默认：`None`）

    如果为`None`，则将自动检测经纪人的实际模式（fundmode - True/False），以决定收益是基于总净资产价值还是基金价值。请参阅经纪人文档中的`set_fundmode`

    将其设置为`True`或`False`以获得特定行为

#### - `get_analysis()`

返回一个字典，其中值为收益，键为每个收益的日期时间点

## PositionsValue

#### 类`backtrader.analyzers.PositionsValue()`

此分析器报告当前数据集的持仓价值

参数：

+   时间框架（默认：`None`）如果为`None`，则将使用系统第一条数据的时间框架

+   压缩（默认：`None`）

    仅用于亚日时间框架，例如通过指定“TimeFrame.Minutes”和 60 作为压缩来在小时时间框架上工作

    如果为`None`，则将使用系统第一条数据的压缩

+   headers（默认：`False`）

    向字典添加一个初始键，其中包含结果的名称（“Datetime”作为键

+   现金（默认：`False`）

    将实际现金作为额外头寸包含（对于标题，将使用“cash”作为名称）

#### - `get_analysis()`

返回一个字典，其中值为收益，键为每个收益的日期时间点

## PyFolio

#### 类`backtrader.analyzers.PyFolio()`

此分析器使用 4 个子分析器收集数据并将其转换为与`pyfolio`兼容的数据集

子分析器

+   `TimeReturn`

    用于计算全局投资组合价值的收益

+   `PositionsValue`

    用于计算每个数据的持仓价值。它将`headers`和`cash`参数设置为`True`

+   `Transactions`

    用于记录每笔交易的数据（大小，价格，价值）。将`headers`参数设置为`True`

+   `GrossLeverage`

    跟踪总杠杆（策略投资了多少）

参数：

```py
These are passed transparently to the children

* timeframe (default: `bt.TimeFrame.Days`)

  If `None` then the timeframe of the 1st data of the system will be
  used

* compression (default: 1\`)

  If `None` then the compression of the 1st data of the system will be
  used
```

`timeframe` 和 `compression` 都遵循 `pyfolio` 的默认行为，即使用 *daily* 数据并将其上采样以获得年度收益等值。

#### - 获取分析()

返回一个以值为收益和以键为每个收益的日期时间点的字典

#### 获取 `pf_items()`

返回一个包含收益为值和每个收益为键的字典

```py
pyfolio`

returns, positions, transactions, gross_leverage
```

因为对象旨在直接输入到 `pyfolio`，此方法通过本地导入 `pandas` 将内部 *backtrader* 结果转换为 *pandas DataFrames*，这是例如 `pyfolio.create_full_tear_sheet` 期望的输入

如果未安装 `pandas`，则该方法将中断

## LogReturnsRolling

#### 类 backtrader.analyzers.LogReturnsRolling()

此分析器计算给定时间框架和压缩的滚动收益

参数：

+   `timeframe`（默认：`None`）如果`None`系统中的第一个数据的`timeframe`将被使用

    传递 `TimeFrame.NoTimeFrame` 来考虑没有时间约束的整个数据集

+   `compression`（默认：`None`）

    仅用于子日时间框架，例如通过指定“TimeFrame.Minutes”和压缩为 60 来工作在小时时间框架上

    如果为 `None`，则将使用系统的第一个数据的压缩

+   `data`（默认：`None`）

    跟踪的参考资产，而不是组合价值。

    **注意**：此数据必须已经通过 `addata`、`resampledata` 或 `replaydata` 添加到 `cerebro` 实例中

+   `firstopen`（默认：`True`）

    当跟踪 `data` 的收益时，当穿越时间框架边界时，例如 `Years` 时，将执行以下操作：

    +   上一年的最后一个 `close` 用作参考价格，以查看当前年份的收益

    问题在于第一个计算，因为数据**没有上一个**收盘价。因此，当此参数为 `True` 时，将使用 *开盘* 价格进行第一个计算。

    这要求数据源具有 `open` 价格（对于 `close`，将使用标准的 `[0]` 符号，不涉及字段价格）

    否则将使用初始收盘价。

+   `fund`（默认：`None`）

    如果为 `None`，则将自动检测经纪人的实际模式（fundmode - True/False），以决定收益是基于总净资产价值还是基金价值。请参阅经纪人文档中的 `set_fundmode`

    将其设置为 `True` 或 `False` 以获得特定行为

#### - 获取分析()

返回一个以值为收益和以键为每个收益的日期时间点的字典

## 期间统计

#### 类 backtrader.analyzers.PeriodStats()

计算给定时间框架的基本统计信息

参数：

+   `timeframe`（默认：`Years`）如果为 `None`，则将使用系统中第一个数据的 `timeframe`

    传递 `TimeFrame.NoTimeFrame` 来考虑没有时间约束的整个数据集

+   `compression`（默认：`1`）

    仅用于子日时间框架，例如通过指定“TimeFrame.Minutes”和压缩为 60 来工作在小时时间框架上

    如果是`None`，则将使用系统的第一个数据进行压缩

+   `fund`（默认值：`None`）

    如果是`None`，则会自动检测经纪人的实际模式（fundmode - True/False），以决定收益是否基于总净资产价值或基金价值。参见经纪人文档中的`set_fundmode`

    将其设置为`True`或`False`以获得特定行为

`get_analysis`返回一个包含键的字典：

+   `average`

+   `stddev`

+   `positive`

+   `negative`

+   `nochange`

+   `best`

+   `worst`

如果参数`zeroispos`设置为`True`，则没有变化的期间将被视为正值

## 返回

#### 类 backtrader.analyzers.Returns()

使用对数方法计算的总、平均、复合和年化收益

参见：

+   [`www.crystalbull.com/sharpe-ratio-better-with-log-returns/`](https://www.crystalbull.com/sharpe-ratio-better-with-log-returns/)

参数：

+   `timeframe`（默认值：`None`）

    如果是`None`，则将使用系统的第一个数据的`timeframe`

    将`TimeFrame.NoTimeFrame`传递以考虑整个数据集，不受时间限制

+   `compression`（默认值：`None`）

    仅用于亚日时间框架，例如通过指定“TimeFrame.Minutes”和 60 作为压缩来处理小时时间框架

    如果是`None`，则将使用系统的第一个数据进行压缩

+   `tann`（默认值：`None`）

    年度化（normalization）所使用的周期数

    即：

    +   `days: 252`

    +   `weeks: 52`

    +   `months: 12`

    +   `years: 1`

+   `fund`（默认值：`None`）

    如果是`None`，则会自动检测经纪人的实际模式（fundmode - True/False），以决定收益是否基于总净资产价值或基金价值。参见经纪人文档中的`set_fundmode`

    将其设置为`True`或`False`以获得特定行为

#### - get_analysis()

返回一个以返回值为值和每个返回值的日期时间点为键的字典

返回的字典有以下键：

+   `rtot`：总复合回报

+   `ravg`：整个周期的平均回报（特定于时间框架）

+   `rnorm`：年化/标准化回报

+   `rnorm100`：以 100%表示的年化/标准化回报

## SharpeRatio

#### 类 backtrader.analyzers.SharpeRatio()

此分析器使用一个风险免费资产（简单地是利率）计算策略的 SharpeRatio

参数：

+   `timeframe`：（默认值：`TimeFrame.Years`）

+   `compression`（默认值：`1`）

    仅用于亚日时间框架，例如通过指定“TimeFrame.Minutes”和 60 作为压缩来处理小时时间框架

+   `riskfreerate`（默认值：0.01 -> 1%）

    以年为单位表达（见下文的`convertrate`）

+   `convertrate`（默认值：`True`）

    将`riskfreerate`从年度转换为月度、周度或日度利率。不支持亚日转换

+   `factor`（默认值：`None`）

    如果是`None`，则将从预定义表中选择将风险无风险利率的转换因子从*年度*转换为所选择的时间框架

    天数：252，周数：52，月数：12，年数：1

    否则将使用指定的值

+   `annualize`（默认值：`False`）

    如果`convertrate`为`True`，则*SharpeRatio*将以所选的`timeframe`传递。

    在大多数情况下，SharpeRatio 以年化形式呈现。将`riskfreerate`从年化转换为月度、周度或日度利率。不支持亚日转换

+   `stddev_sample`（默认值：`False`）

    如果设置为`True`，则将计算*标准差*，并将平均值中的分母减`1`。当计算*标准差*时使用此项，如果考虑到并非所有样本都用于计算，则会使用此项。这称为*Bessels'校正*

+   `daysfactor`（默认值：`None`）

    旧名称为`factor`。如果设置为除`None`之外的任何值，并且`timeframe`为`TimeFrame.Days`，则会假定这是旧代码，并将使用该值

+   `legacyannual`（默认值：`False`）

    使用`AnnualReturn`返回分析器，正如其名称所示，仅适用于年份

+   `fund`（默认值：`None`）

    如果为`None`，则将自动检测经纪人的实际模式（fundmode - True/False），以决定回报是基于总净资产值还是基金值。请参阅经纪人文档中的`set_fundmode`

    将其设置为`True`或`False`以获取特定行为

#### - 获取分析()

返回一个带有键“sharperatio”的字典，其中包含比率

## SharpeRatio_A

#### class backtrader.analyzers.SharpeRatio_A()

SharpeRatio 的扩展，直接以年化形式返回 Sharpe 比率

以下参数已从`SharpeRatio`更改

+   `annualize`（默认值：`True`）

## SQN

#### class backtrader.analyzers.SQN()

SQN 或系统质量数。由 Van K. Tharp 定义以对交易系统进行分类。

+   1.6 - 1.9 低于平均水平

+   2.0 - 2.4 平均

+   2.5 - 2.9 良好

+   3.0 - 5.0 优秀

+   5.1 - 6.9 极好

+   7.0 - 圣杯？

公式：

+   平方根（交易次数）* 平均（交易利润）/ 标准差（交易利润）

当交易次数≥ 30 时，sqn 值应被视为可靠

#### - 获取分析()

返回一个带有键“sqn”和“trades”（考虑的交易数）的字典

## TimeReturn

#### class backtrader.analyzers.TimeReturn()

此分析器通过查看时间范围的起始点和结束点来计算回报

参数：

+   `timeframe`（默认值：`None`）如果为`None`，则将使用系统中的第一个数据的`timeframe`

    通过传递`TimeFrame.NoTimeFrame`来考虑没有时间约束的整个数据集

+   `compression`（默认值：`None`）

    仅用于亚日时间框架，例如通过指定“TimeFrame.Minutes”和 60 作为压缩来处理每小时时间框架

    如果为`None`，则将使用系统的第一个数据的压缩

+   `data`（默认值：`None`）

    跟踪的参考资产而不是投资组合价值。

    **注意**：此数据必须已添加到具有`addata`、`resampledata`或`replaydata`的`cerebro`实例中

+   `firstopen`（默认值：`True`）

    当跟踪`data`的回报时，当跨越时间范围边界时，例如`Years`时，会执行以下操作：

    +   上一年的最后一个`close`用作参考价格，以查看当前年度的回报

    问题是第一次计算，因为数据没有**先前的**收盘价。 因此，当此参数为`True`时，将使用*开盘*价进行第一次计算。

    这需要数据源具有`open`价格（对于`close`，将使用标准[0]表示法，而不参考字段价格）

    否则将使用初始收盘价。

+   `fund`（默认值：`None`）

    如果为`None`，则将自动检测经纪人的实际模式（fundmode - True/False），以决定基于总净资产值还是基金价值的回报。请参阅经纪人文档中的`set_fundmode`

    将其设置为特定行为的`True`或`False`

#### - get_analysis()

返回一个带有值作为值的字典，并将每个返回的日期时间点作为键

## TradeAnalyzer

#### 类 backtrader.analyzers.TradeAnalyzer()

提供了有关已关闭交易的统计信息（还保留了未平仓交易的计数）

+   总开/闭交易

+   Streak 赢得/失去 当前/最长的

+   利润与损失 总数/平均数

+   赢得/失去 计数/总 PNL/平均 PNL/最大 PNL

+   长/空头 计数/ 总 PNL / 平均 PNL / 最大 PNL

    +   赢得/失去 计数/总 PNL/平均 PNL/最大 PNL

+   长度（市场上的条）

    +   总数/平均数/最大值/最小值

    +   赢得/失去 总数/平均数/最大值/最小值

    +   长/短 总数/平均值/最大值/最小值

    +   赢得/失去 总数/平均数/最大值/最小值

**注意**：分析器使用“自动”字典进行字段处理，这意味着如果没有执行交易，将不会生成任何统计数据。

在这种情况下，`get_analysis`返回的字典中将有一个字段/子字段，即：

+   dictname[‘total’][‘total’]，其值将为 0（该字段还可以使用点表示法 dictname.total.total）。

## 交易

#### 类 backtrader.analyzers.Transactions()

此分析器报告了系统中发生的每笔交易

它查看订单执行位以从每个`next`周期开始创建`Position`，初始值为 0。

结果将在下一个周期记录交易时使用

参数：

+   headers（默认值：`True`）

    向字典添加一个初始键，该键包含具有数据名称的结果

    这个分析器的建模是为了方便与`pyfolio`集成，头部名称取自用于它的样本：

    ```py
    'date', 'amount', 'price', 'sid', 'symbol', 'value'` 
    ```

#### - get_analysis()

返回一个带有值作为值的字典，并将每个返回的日期时间点作为键

## VWR

#### 类 backtrader.analyzers.VWR()

变异性加权回报：对数收益率的夏普比率更好

别名：

+   变异性加权回报

见：

+   [`www.crystalbull.com/sharpe-ratio-better-with-log-returns/`](https://www.crystalbull.com/sharpe-ratio-better-with-log-returns/)

参数：

+   `timeframe`（默认值：`None`）如果为`None`，则将报告整个回测期间的完整收益

    传递`TimeFrame.NoTimeFrame`以考虑没有时间限制的整个数据集

+   `compression`（默认值：`None`）

    仅用于亚日时间框架，例如通过指定“TimeFrame.Minutes”和 60 作为压缩来处理小时时间框架

    如果为`None`，则将使用系统第一个数据的压缩

+   `tann`（默认值：`None`）

    用于计算平均收益的年化（标准化）周期数。如果为`None`，则将使用标准的`t`值，即：

    +   `days: 252`

    +   `weeks: 52`

    +   `months: 12`

    +   `years: 1`

+   `tau`（默认值：`2.0`）

    计算的因子（参见文献）

+   `sdev_max`（默认值：`0.20`）

    最大标准偏差（参见文献）

+   `fund`（默认值：`None`）

    如果为`None`，则会自动检测经纪人的实际模式（fundmode - True/False），以决定收益是基于总净资产值还是基金价值。请参阅经纪人文档中的`set_fundmode`

    将其设置为`True`或`False`以获得特定的行为

#### - 获取分析()

返回一个字典，其中收益作为值，每个收益对应的日期时间点作为键

返回的字典包含以下键：

+   `vwr`: 变异权重收益
