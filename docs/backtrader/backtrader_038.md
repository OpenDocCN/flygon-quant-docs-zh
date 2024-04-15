# Filters Reference

> 原文：[`www.backtrader.com/docu/filters-reference/`](https://www.backtrader.com/docu/filters-reference/)

## SessionFilter

#### class backtrader.filters.SessionFilter(data)

此类可以作为过滤器应用于数据源，并将过滤掉超出常规会话时间的盘中 bar（即：盘前/盘后市场数据）

这是一个“非简单”的过滤器，必须管理数据的堆栈（在初始化和 **call** 期间传递）

它不需要“last”方法，因为它没有需要传递的内容

## SessionFilterSimple

#### class backtrader.filters.SessionFilterSimple(data)

此类可以作为过滤器应用于数据源，并将过滤掉超出常规会话时间的盘中 bar（即：盘前/盘后市场数据）

这是一个“简单”过滤器，不必管理数据的堆栈（在初始化和 **call** 期间传递）

它不需要“last”方法，因为它没有需要传递的内容

Bar 管理将由 SimpleFilterWrapper 类完成，该类在 DataBase.addfilter_simple 调用时添加

## SessionFilller

#### class backtrader.filters.SessionFiller(data)

声明会话开始/结束时间内的数据源的 Bar Filler。

填充 bar 是使用声明的 Data Source `timeframe` 和 `compression` 构建的（用于计算中间缺失的时间）

参数：

+   fill_price (def: None):

    如果传递了 None，则将使用前一个 bar 的收盘价。例如，要得到一个 bar，该 bar 需要时间，但不会在图表中显示... 使用 float(‘Nan’)

+   fill_vol (def: float(‘NaN’)):

    用于填充缺失的 volume 的值

+   fill_oi (def: float(‘NaN’)):

    用于填充缺失的持仓量的值

+   skip_first_fill (def: True):

    在看到第一个有效 bar 时，不要从 sessionstart 填充到该 bar

## CalendarDays

#### class backtrader.filters.CalendarDays(data)

在交易日中添加缺失的日历天的 Bar Filler

参数：

+   fill_price (def: None):

    > 0：用于填充 0 或 None 的给定值：使用最后已知的收盘价 -1：使用最后一个 bar 的中点（High-Low 平均值）

+   fill_vol (def: float(‘NaN’)):

    用于填充缺失的 volume 的值

+   fill_oi (def: float(‘NaN’)):

    用于填充缺失的持仓量的值

## BarReplayer_Open

#### class backtrader.filters.BarReplayer_Open(data)

此过滤器将一个 bar 拆分为两部分：

+   `Open`：将使用 bar 的开盘价提供一个初始价格 bar，在该 bar 中，四个组件（OHLC）相等

    该初始 bar 的 volume/openinterest 字段为 0

+   `OHLC`：原始 bar 包含原始的 `volume`/`openinterest`

拆分模拟了一次回放，无需使用 *replay* 过滤器。

## DaySplitter_Close

#### class backtrader.filters.DaySplitter_Close(data)

将每日 bar 拆分为两部分，模拟用于重放数据的 2 个 tick：

+   第一个 tick：`OHLX`

    `Close` 将被替换为 `Open`、`High` 和 `Low` 的 *平均值*

    此 tick 使用会话开放时间

和

+   第二个 tick：`CCCC`

    `Close`价格将用于价格的四个组成部分

    会话结束时间用于此点

体积将根据以下参数分配给 2 个点：

+   `closevol`（默认：`0.5`）该值表示从 0.0 到 1.0 的绝对比例，应分配给*收盘*点。其余将分配给`OHLX`点。

**此过滤器意在与** `cerebro.replaydata` **一同使用**

## 平均趋势烛（HeikinAshi）

#### 类 backtrader.filters.HeikinAshi(data)

此过滤器重新构建开盘价、最高价、最低价、收盘价以绘制平均趋势烛

参见：

```py
`* [`en.wikipedia.org/wiki/Candlestick_chart#Heikin_Ashi_candlesticks`](https://en.wikipedia.org/wiki/Candlestick_chart#Heikin_Ashi_candlesticks)

* [`stockcharts.com/school/doku.php?id=chart_school:chart_analysis:heikin_ashi`](http://stockcharts.com/school/doku.php?id=chart_school:chart_analysis:heikin_ashi)` 
```

## 砖形图（Renko）

#### 类 backtrader.filters.Renko(data)

修改数据流以绘制砖形图（或砖块）

参数：

+   `hilo`（默认：*False*）使用最高价和最低价而不是收盘价来决定是否需要新砖

+   `size`（默认：*None*）每个砖的考虑大小

+   `autosize`（默认：*20.0*）如果*size*为*None*，则将用于自动计算砖的大小（简单地将当前价格除以给定值）

+   `dynamic`（默认：*False*）如果*True*并使用*autosize*，则移至新砖时将重新计算砖的大小。这当然会消除砖的完美对齐。

+   `align`（默认：*1.0*）用于对齐砖的价格边界的因子。例如，如果价格为*3563.25*，而*align*为*10.0*，则得到的对齐价格将为*3560*。计算方式：

    +   3563.25 / 10.0 = 356.325

    +   四舍五入并去除小数 -> 356

    +   356 * 10.0 -> 3560

参见：

```py
`* [`stockcharts.com/school/doku.php?id=chart_school:chart_analysis:renko`](http://stockcharts.com/school/doku.php?id=chart_school:chart_analysis:renko)` 
```
