# 技术 - 技术指标

> 原文：[`gbeced.github.io/pyalgotrade/docs/v0.20/html/technical.html`](https://gbeced.github.io/pyalgotrade/docs/v0.20/html/technical.html)

*class* `pyalgotrade.technical.``EventWindow`(*windowSize*, *dtype=<type 'float'>*, *skipNone=True*)

基类：`object`

一个 EventWindow 类负责在一系列值的移动窗口上进行计算。

| 参数： |
| --- |

+   **windowSize**（*int.*） - 窗口的大小。必须大于 0。

+   **dtype**（*数据类型.*） - 数组的期望数据类型。

+   **skipNone**（*布尔.*） - 如果 None 值不应包含在窗口中，则为 True。

|

注意

这是一个基类，不应直接使用。

`getValue`()

重写以使用窗口中的值计算值。

`getValues`()

返回一个具有窗口中值的 numpy.array。

`getWindowSize`()

返回窗口大小。

*class* `pyalgotrade.technical.``EventBasedFilter`(*dataSeries*, *eventWindow*, *maxLen=None*)

基类：`pyalgotrade.dataseries.SequenceDataSeries`

EventBasedFilter 类负责捕获 `pyalgotrade.dataseries.DataSeries` 中的新值，并使用 `EventWindow` 计算新值。

| 参数： |
| --- |

+   **dataSeries**（`pyalgotrade.dataseries.DataSeries`.） - 正在过滤的 DataSeries 实例。

+   **eventWindow**（`EventWindow`.） - 用于计算新值的 EventWindow 实例。

+   **maxLen**（*int.*） - 要保持的最大值数。一旦有限长度已满，添加新项时，将从对端丢弃相应数量的项。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

## 示例

以下示例显示如何组合 `EventWindow` 和 `EventBasedFilter` 来构建自定义过滤器：

```py
from __future__ import print_function

from pyalgotrade import dataseries
from pyalgotrade import technical

# An EventWindow is responsible for making calculations using a window of values.
class Accumulator(technical.EventWindow):
    def getValue(self):
        ret = None
        if self.windowFull():
            ret = self.getValues().sum()
        return ret

# Build a sequence based DataSeries.
seqDS = dataseries.SequenceDataSeries()
# Wrap it with a filter that will get fed as new values get added to the underlying DataSeries.
accum = technical.EventBasedFilter(seqDS, Accumulator(3))

# Put in some values.
for i in range(0, 50):
    seqDS.append(i)

# Get some values.
print(accum[0])  # Not enough values yet.
print(accum[1])  # Not enough values yet.
print(accum[2])  # Ok, now we should have at least 3 values.
print(accum[3])

# Get the last value, which should be equal to 49 + 48 + 47.
print(accum[-1]) 
```

输出应为：

```py
None
None
3.0
6.0
144.0 
```

## 移动平均

*class* `pyalgotrade.technical.ma.``SMA`(*dataSeries*, *period*, *maxLen=None*)

基类：`pyalgotrade.technical.EventBasedFilter`

简单移动平均滤波器。

| 参数： |
| --- |

+   **dataSeries**（`pyalgotrade.dataseries.DataSeries`.） - 正在过滤的 DataSeries 实例。

+   **period**（*int.*） - 用于计算 SMA 的值数。

+   **maxLen** (*int.*) – 持有的值的最大数量。一旦有限长度满了，当添加新项时，相应数量的项将从相反端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

*类* `pyalgotrade.technical.ma.``EMA`(*dataSeries*, *period*, *maxLen=None*)

基类: `pyalgotrade.technical.EventBasedFilter`

指数移动平均过滤器。

| 参数: |
| --- |

+   **dataSeries** (`pyalgotrade.dataseries.DataSeries`.) – 正在被过滤的 DataSeries 实例。

+   **period** (*int.*) – 用于计算 EMA 的值的数量。必须是大于 1 的整数。

+   **maxLen** (*int.*) – 持有的值的最大数量。一旦有限长度满了，当添加新项时，相应数量的项将从相反端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

*类* `pyalgotrade.technical.ma.``WMA`(*dataSeries*, *weights*, *maxLen=None*)

基类: `pyalgotrade.technical.EventBasedFilter`

加权移动平均过滤器。

| 参数: |
| --- |

+   **dataSeries** (`pyalgotrade.dataseries.DataSeries`.) – 正在被过滤的 DataSeries 实例。

+   **weights** (*list.*) – 一个具有权重的 int/float 列表。

+   **maxLen** (*int.*) – 持有的值的最大数量。一旦有限长度满了，当添加新项时，相应数量的项将从相反端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

*类* `pyalgotrade.technical.vwap.``VWAP`(*dataSeries*, *period*, *useTypicalPrice=False*, *maxLen=None*)

基类: `pyalgotrade.technical.EventBasedFilter`

成交量加权平均价格过滤器。

| 参数: |
| --- |

+   **dataSeries** (`pyalgotrade.dataseries.bards.BarDataSeries`.) – 正在被过滤的 DataSeries 实例。

+   **period** (*int.*) – 用于计算 VWAP 的值的数量。

+   **useTypicalPrice** (*boolean.*) – 如果应该使用典型价格而不是收盘价格，则为 True。

+   **maxLen** (*int.*) – 持有的值的最大数量。一旦有限长度满了，当添加新项时，相应数量的项将从相反端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|  ## 动量指标

*类* `pyalgotrade.technical.macd.``MACD`(*dataSeries*, *fastEMA*, *slowEMA*, *signalEMA*, *maxLen=None*)

基类: `pyalgotrade.dataseries.SequenceDataSeries`

按照[`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_convergence_divergence_macd`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_convergence_divergence_macd)中描述的移动平均收敛-背离指标。

| 参数： |
| --- |

+   **dataSeries**（`pyalgotrade.dataseries.DataSeries`） - 正在被过滤的 DataSeries 实例。

+   **fastEMA**（*整数*） - 用于计算快速 EMA 的数值数量。

+   **slowEMA**（*整数*） - 用于计算慢速 EMA 的数值数量。

+   **signalEMA**（*整数*） - 用于计算信号 EMA 的数值数量。

+   **maxLen**（*整数*） - 要保留的最大数值数量。一旦有界长度已满，当添加新项目时，将从相反端丢弃相应数量的项目。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

`getHistogram`()

返回一个带有直方图（MACD 和信号之间的差异）的`pyalgotrade.dataseries.DataSeries`。

`getSignal`()

返回一个带有 MACD 上的 EMA 的`pyalgotrade.dataseries.DataSeries`。

*类* `pyalgotrade.technical.rsi.``RSI`（*dataSeries*, *周期*, *maxLen=None*）

基类：`pyalgotrade.technical.EventBasedFilter`

按照[`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:relative_strength_index_rsi`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:relative_strength_index_rsi)中描述的相对强度指数过滤器。

| 参数： |
| --- |

+   **dataSeries**（`pyalgotrade.dataseries.DataSeries`） - 正在被过滤的 DataSeries 实例。

+   **period**（*整数*） - 周期。请注意，如果周期是**n**，则使用**n+1**个值。必须大于 1。

+   **maxLen**（*整数*） - 要保留的最大数值数量。一旦有界长度已满，当添加新项目时，将从相反端丢弃相应数量的项目。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

*类* `pyalgotrade.technical.stoch.``StochasticOscillator`（*barDataSeries*, *周期*, *dSMAPeriod=3*, *useAdjustedValues=False*, *maxLen=None*）

基类：`pyalgotrade.technical.EventBasedFilter`

根据 [`stockcharts.com/school/doku.php?st=stochastic+oscillator&id=chart_school:technical_indicators:stochastic_oscillator_fast_slow_and_full`](http://stockcharts.com/school/doku.php?st=stochastic+oscillator&id=chart_school:technical_indicators:stochastic_oscillator_fast_slow_and_full) 描述的快速随机振荡器过滤器。注意，此过滤器返回的值是 %K。要访问 %D，请使用 `getD()`。

| 参数: |
| --- |

+   **barDataSeries** (`pyalgotrade.dataseries.bards.BarDataSeries`.) – 正在过滤的 BarDataSeries 实例。

+   **period** (*int.*) – 期间。必须 > 1。

+   **dSMAPeriod** (*int.*) – %D 的 SMA 周期。必须 > 1。

+   **useAdjustedValues** (*boolean.*) – True 表示使用调整后的 Low/High/Close 值。

+   **maxLen** (*int.*) – 持有的最大值数量。一旦有界长度满了，当添加新项时，相应数量的项将从另一端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

`getD`()

返回一个 `pyalgotrade.dataseries.DataSeries`，其中包含 %D 值。

*class* `pyalgotrade.technical.roc.``RateOfChange`(*dataSeries*, *valuesAgo*, *maxLen=None*)

基础: `pyalgotrade.technical.EventBasedFilter`

根据 [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:rate_of_change_roc_and_momentum`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:rate_of_change_roc_and_momentum) 描述的变化率过滤器。

| 参数: |
| --- |

+   **dataSeries** (`pyalgotrade.dataseries.DataSeries`.) – 正在过滤的 DataSeries 实例。

+   **valuesAgo** (*int.*) – 给定值与之比较的值的数量。必须 > 0。

+   **maxLen** (*int.*) – 持有的最大值数量。一旦有界长度满了，当添加新项时，相应数量的项将从另一端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|  ## 其他指

*class* `pyalgotrade.technical.atr.``ATR`(*barDataSeries*, *period*, *useAdjustedValues=False*, *maxLen=None*)

基础: `pyalgotrade.technical.EventBasedFilter`

基于 [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:average_true_range_atr`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:average_true_range_atr) 描述的平均真实范围过滤器

| 参数: |
| --- |

+   **barDataSeries** (`pyalgotrade.dataseries.bards.BarDataSeries`.) – 正在过滤的 BarDataSeries 实例。

+   **period** (*整数.*) – 平均周期。必须 > 1。

+   **useAdjustedValues** (*布尔值.*) – True 表示使用调整后的低/高/收盘价值。

+   **maxLen** (*整数.*) – 持有的最大值数。一旦有界长度已满，当添加新项目时，相应数量的项目将从另一端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

*类* `pyalgotrade.technical.bollinger.``BollingerBands`(*dataSeries*, *period*, *numStdDev*, *maxLen=None*)

基类: `object`

Bollinger Bands 过滤器如 [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:bollinger_bands`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:bollinger_bands) 描述的那样。

| 参数: |
| --- |

+   **dataSeries** (`pyalgotrade.dataseries.DataSeries`.) – 正在过滤的 DataSeries 实例。

+   **period** (*整数.*) – 用于计算的值的数量。必须 > 1。

+   **numStdDev** (*整数.*) – 用于上部和下部带的标准偏差数量。

+   **maxLen** (*整数.*) – 持有的最大值数。一旦有界长度已满，当添加新项目时，相应数量的项目将从另一端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

`getLowerBand`()

返回下部带作为`pyalgotrade.dataseries.DataSeries`。

`getMiddleBand`()

返回中间带作为`pyalgotrade.dataseries.DataSeries`。

`getUpperBand`()

返回上部带作为`pyalgotrade.dataseries.DataSeries`。

`pyalgotrade.technical.cross.``cross_above`(*values1*, *values2*, *start=-2*, *end=None*)

在两个 DataSeries 对象之间的指定周期内检查交叉条件。

它返回在给定周期内 values1 跨过 values2 的次数。

| 参数: |
| --- |

+   **values1** (`pyalgotrade.dataseries.DataSeries`.) – 跨过的 DataSeries。

+   **values2** (`pyalgotrade.dataseries.DataSeries`.) – 被跨越的 DataSeries。

+   **start** (*整数.*) – 范围的开始。

+   **end** (*整数.*) – 范围的结束。

|

注意

默认的开始和结束值检查最后 2 个值的交叉条件。

`pyalgotrade.technical.cross.``cross_below`(*values1*, *values2*, *start=-2*, *end=None*)

检查两个 DataSeries 对象之间指定期间内的交叉下方条件。

它返回给定期间内 values1 下方交叉 values2 的次数。

| 参数： |
| --- |

+   **values1**（`pyalgotrade.dataseries.DataSeries`.）- 进行交叉的 DataSeries。

+   **values2**（`pyalgotrade.dataseries.DataSeries`.）- 被交叉的 DataSeries。

+   **start**（*int.*）- 范围的开始。

+   **end**（*int.*）- 范围的结束。

|

注意

默认的开始和结束值检查最近 2 个值的交叉下方条件。

*类* `pyalgotrade.technical.cumret.``CumulativeReturn`（*dataSeries*，*maxLen=None*）

基类：`pyalgotrade.technical.EventBasedFilter`

此过滤器计算另一个数据序列上的累积收益。

| 参数： |
| --- |

+   **dataSeries**（`pyalgotrade.dataseries.DataSeries`.）- 被过滤的 DataSeries 实例。

+   **maxLen**（*int.*）- 最多要保留的值数。一旦有限长度已满，当添加新项时，相应数量的项将从另一端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

*类* `pyalgotrade.technical.highlow.``High`（*dataSeries*，*period*，*maxLen=None*）

基类：`pyalgotrade.technical.EventBasedFilter`

此过滤器计算最高值。

| 参数： |
| --- |

+   **dataSeries**（`pyalgotrade.dataseries.DataSeries`.）- 被过滤的 DataSeries 实例。

+   **period**（*int.*）- 用于计算最高值的数值数量。

+   **maxLen**（*int.*）- 最多要保留的值数。一旦有限长度已满，当添加新项时，相应数量的项将从另一端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

*类* `pyalgotrade.technical.highlow.``Low`（*dataSeries*，*period*，*maxLen=None*）

基类：`pyalgotrade.technical.EventBasedFilter`

此过滤器计算最低值。

| 参数： |
| --- |

+   **dataSeries**（`pyalgotrade.dataseries.DataSeries`.）- 被过滤的 DataSeries 实例。

+   **period**（*int.*）- 用于计算最低值的数值数量。

+   **maxLen**（*int.*）- 最多要保留的值数。一旦有限长度已满，当添加新项时，相应数量的项将从另一端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

*类* `pyalgotrade.technical.hurst.``HurstExponent`(*dataSeries*, *period*, *minLags=2*, *maxLags=20*, *logValues=True*, *maxLen=None*)

基类：`pyalgotrade.technical.EventBasedFilter`

赫斯特指数过滤器。

| 参数: |
| --- |

+   **dataSeries** (`pyalgotrade.dataseries.DataSeries`.) – 正在过滤的 DataSeries 实例。

+   **period** (*int.*) – 用于计算赫斯特指数的值数量。

+   **minLags** (*int.*) – 要使用的最小滞后数。必须 >= 2。

+   **maxLags** (*int.*) – 要使用的最大滞后数。必须 > minLags。

+   **maxLen** (*int.*) – 保持的最大值数量。一旦有界长度已满，当添加新项目时，相应数量的项目将从对端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

*类* `pyalgotrade.technical.linebreak.``Line`(*low*, *high*, *dateTime*, *white*)

基类：`object`

线性断裂图表中的一条线。

`getDateTime`()

日期时间。

`getHigh`()

高值。

`getLow`()

低值。

`isBlack`()

如果线是黑色的（价格下跌），则为 True。

`isWhite`()

如果线是白色的（价格上涨），则为 True。

*类* `pyalgotrade.technical.linebreak.``LineBreak`(*barDataSeries*, *reversalLines*, *useAdjustedValues=False*, *maxLen=None*)

基类：`pyalgotrade.dataseries.SequenceDataSeries`

描述了[线性断裂过滤器](http://stockcharts.com/school/doku.php?id=chart_school:chart_analysis:three_line_break)。这是一个`Line`实例的 DataSeries。

| 参数: |
| --- |

+   **barDataSeries** (`pyalgotrade.dataseries.bards.BarDataSeries`.) – 正在过滤的 DataSeries 实例。

+   **reversalLines** (*int.*) – 要检查以计算反转的线数。必须大于 1。

+   **useAdjustedValues** (*boolean.*) – True 表示使用调整后的高/低/收盘价值。

+   **maxLen** (*int.*) – 保持的最大值数量。一旦有界长度已满，当添加新项目时，相应数量的项目将从对端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。此值不能小于 reversalLines。

|

*类* `pyalgotrade.technical.linreg.``LeastSquaresRegression`(*dataSeries*, *windowSize*, *maxLen=None*)

基类：`pyalgotrade.technical.EventBasedFilter`

根据最小二乘法回归计算值。

| 参数: |
| --- |

+   **dataSeries**（`pyalgotrade.dataseries.DataSeries`.）– 要过滤的 DataSeries 实例。

+   **windowSize**（*int.*）– 用于计算回归的值的数量。

+   **maxLen**（*int.*）– 要保持的最大值数量。一旦有限长度已满，当添加新项时，相应数量的项将从相反端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

`getValueAt`（*dateTime*）

根据回归线在给定时间计算值。

| 参数： | **dateTime**（`datetime.datetime`.）– 要计算值的日期时间。如果基础 DataSeries 中的值不足，则返回 None。 |
| --- | --- |

*class* `pyalgotrade.technical.linreg.``Slope`（*dataSeries*, *period*, *maxLen=None*）

基类：`pyalgotrade.technical.EventBasedFilter`

斜率过滤器计算最小二乘回归线的斜率。

| 参数： |
| --- |

+   **dataSeries**（`pyalgotrade.dataseries.DataSeries`.）– 要过滤的 DataSeries 实例。

+   **period**（*int.*）– 用于计算斜率的值数量。

+   **maxLen**（*int.*）– 要保持的最大值数量。一旦有限长度已满，当添加新项时，相应数量的项将从相反端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

注意

此过滤器忽略了不同值之间经过的时间。

*class* `pyalgotrade.technical.stats.``StdDev`（*dataSeries*, *period*, *ddof=0*, *maxLen=None*）

基类：`pyalgotrade.technical.EventBasedFilter`

标准差过滤器。

| 参数： |
| --- |

+   **dataSeries**（`pyalgotrade.dataseries.DataSeries`.）– 要过滤的 DataSeries 实例。

+   **period**（*int.*）– 用于计算标准差的值数量。

+   **ddof**（*int.*）– Delta 自由度。

+   **maxLen**（*int.*）– 要保持的最大值数量。一旦有限长度已满，当添加新项时，相应数量的项将从相反端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

*class* `pyalgotrade.technical.stats.``ZScore`（*dataSeries*, *period*, *ddof=0*, *maxLen=None*）

基类：`pyalgotrade.technical.EventBasedFilter`

Z-Score 过滤器。

| 参数： |
| --- |

+   **dataSeries**（`pyalgotrade.dataseries.DataSeries`.）– 要过滤的 DataSeries 实例。

+   **period**（*int.*）– 用于计算 Z-Score 的值数量。

+   **ddof** (*int.*) – 用于标准差的 delta 自由度。

+   **maxLen** (*int.*) – 保持的最大值数量。一旦有限长度已满，当添加新项时，相应数量的项将从另一端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

### 目录

+   技术指标 – Technical indicators

    +   示例

    +   移动平均线

    +   动量指标

    +   其他指标

#### 上一个主题

条形供给 – Bar providers

#### 下一个主题

经纪人 – 订单管理类

### 本页

+   显示源码

### 快速搜索

输入搜索词或模块、类或函数名称。

### 导航

+   索引

+   模块 |

+   下一个 |

+   上一个 |

+   PyAlgoTrade 0.20 文档 »

+   代码文档 »
