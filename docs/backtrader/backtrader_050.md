# 指标参考

> 原文：[`www.backtrader.com/docu/indautoref/`](https://www.backtrader.com/docu/indautoref/)

### 加速度/减速度振荡器

别名：

```py
* AccDeOsc
```

加速/减速技术指标（AC）测量当前驱动力的加速度和减速度。该指标将在任何驱动力变化之前改变方向，而驱动力又会在价格之前改变方向。

公式：

```py
* AcdDecOsc = AwesomeOscillator - SMA(AwesomeOscillator, period)
```

见：

```py
* [`www.metatrader5.com/en/terminal/help/indicators/bw_indicators/ao`](https://www.metatrader5.com/en/terminal/help/indicators/bw_indicators/ao)

* [`www.ifcmarkets.com/en/ntx-indicators/ntx-indicators-accelerator-decelerator-oscillator`](https://www.ifcmarkets.com/en/ntx-indicators/ntx-indicators-accelerator-decelerator-oscillator)
```

线条：

```py
* accde
```

参数：

```py
* period (5)

* movav (SMA)
```

绘制信息：

```py
* plot (True)

* plotmaster (None)

* legendloc (None)

* subplot (True)

* plotname ()

* plotskip (False)

* plotabove (False)

* plotlinelabels (False)

* plotlinevalues (True)

* plotvaluetags (True)
```

+   绘制垂直边距（0.0）

+   绘制水平线（[]）

+   绘制垂直刻度线（[]）

+   绘制水平线（[]）

+   强制绘制（False）

绘制线条：

+   accde：

    +   _ 方法（条形图）

    +   alpha（0.5）

    +   宽度（1.0）

### 累计

别名：

+   累计总和，累积和

数据值的累积总和

公式：

+   累积 += 数据

线条：

+   累计

参数：

+   种子（0.0）

绘制信息：

+   绘制（True）

+   主绘图器（无）

+   图例位置（无）

+   子图（True）

+   绘制名称（）

+   跳过绘制（False）

+   绘制在上方（False）

+   绘制线条标签（False）

+   绘制线条数值（True）

+   绘制数值标签（True）

+   绘制垂直边距（0.0）

+   绘制水平线（[]）

+   绘制垂直刻度线（[]）

+   绘制水平线（[]）

+   强制绘制（False）

绘制线条：

+   累计：

### 自适应移动平均线

别名：

+   KAMA，自适应移动平均线

由佩里·考夫曼在他的书“更聪明的交易”中定义。

这是具有连续缩放平滑因子的移动平均线，考虑市场方向和波动性。平滑因子是从 2 个指数移动平均值平滑因子计算的，一个快速的和一个慢速的。

如果市场趋势，值将趋向于快速 ema 平滑期。如果市场不趋势，则会朝向慢速 EMA 平滑期。

这是 SmoothingMovingAverage 的子类，一旦覆盖，就会考虑到平滑因子的实时性质

公式：

+   方向 = 收盘价 - 收盘价 _ 周期

+   波动性 = sumN（abs（收盘价 - 收盘价 _n），周期）

+   效率比率 = abs（方向 / 波动性）

+   快速 = 2 /（快速周期 + 1）

+   慢速 = 2 /（慢速周期 + 1）

+   smfactor = 平方（efficiency_ratio *（fast - slow）+ slow）

+   smfactor1 = 1.0 - smfactor

+   初始种子值是一个简单移动平均值

另请参阅：

+   [`fxcodebase.com/wiki/index.php/Kaufman’s_Adaptive_Moving_Average_(KAMA`](http://fxcodebase.com/wiki/index.php/Kaufman's_Adaptive_Moving_Average_(KAMA))

+   [`www.metatrader5.com/en/terminal/help/analytics/indicators/trend_indicators/ama`](http://www.metatrader5.com/en/terminal/help/analytics/indicators/trend_indicators/ama)

+   [`help.cqg.com/cqgic/default.htm#!Documents/adaptivemovingaverag2.htm`](http://help.cqg.com/cqgic/default.htm#!Documents/adaptivemovingaverag2.htm)

线条：

+   KAMA

参数：

+   周期（30）

+   快速（2）

+   慢速（30）

绘制信息：

+   绘制（True）

+   主绘图器（无）

+   图例位置（无）

+   子图（False）

+   绘制名称（）

+   跳过绘制（False）

+   绘制在上方（False）

+   绘制线条标签（False）

+   绘制线条数值（True）

+   绘制线条数值（True）

+   绘制垂直边距（0.0）

+   绘制水平线（[]）

+   绘制垂直线（[]）

+   绘制水平线（[]）

+   强制绘制（False）

绘制线条：

+   KAMA：

### 自适应移动平均线包络线

别名：

+   KAMA 包络线，自适应移动平均线包络线

自适应移动平均线和包络线分开了“perc”

公式：

+   KAMA（自自适应移动平均线）的来源

+   top = kama * (1 + 百分比)

+   bot = kama * (1 - 百分比)

另请参见：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes)

Lines:

+   kama

+   top

+   bot

Params:

+   period (30)

+   fast (2)

+   slow (30)

+   perc (2.5)

PlotInfo:

+   plot (是)

+   plotmaster (无)

+   legendloc (无)

+   subplot (否)

+   plotname ()

+   plotskip (否)

+   plotabove (否)

+   plotlinelabels (否)

+   plotlinevalues (是)

+   plotvaluetags (是)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (否)

PlotLines:

+   kama:

+   top:

    +   _samecolor (是)

+   bot:

    +   _samecolor (是)

### AdaptiveMovingAverageOscillator

Alias:

+   AdaptiveMovingAverageOsc, KAMAOscillator, KAMAOsc, MovingAverageAdaptiveOscillator, MovingAverageAdaptiveOsc

自适应移动平均的振荡周围的振荡

Lines:

+   kama

Params:

+   period (30)

+   fast (2)

+   slow (30)

PlotInfo:

+   plot (是)

+   plotmaster (无)

+   legendloc (无)

+   subplot (是)

+   plotname ()

+   plotskip (否)

+   plotabove (否)

+   plotlinelabels (否)

+   plotlinevalues (是)

+   plotvaluetags (是)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (否)

PlotLines:

+   kama:

+   _0:

    +   _name (osc)

### AllN

如果`period`中的所有值评估为非零（即`True`），则其值为`True`（存储为`1.0`）

使用内置的`all`进行计算

Formula:

+   alln = all(data, period)

Lines:

+   alln

Params:

+   period (1)

PlotInfo:

+   plot (是)

+   plotmaster (无)

+   legendloc (无)

+   subplot (是)

+   plotname ()

+   plotskip (否)

+   plotabove (否)

+   plotlinelabels (否)

+   plotlinevalues (是)

+   plotvaluetags (是)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (否)

PlotLines:

+   alln:

### AnyN

如果`period`中的任何值评估为非零（即`True`），则其值为`True`（存储为`1.0`）

使用内置的`any`进行计算

Formula:

+   anyn = any(data, period)

Lines:

+   anyn

Params:

+   period (1)

PlotInfo:

+   plot (是)

+   plotmaster (无)

+   legendloc (无)

+   subplot (是)

+   plotname ()

+   plotskip (否)

+   plotabove (否)

+   plotlinelabels (否)

+   plotlinevalues (是)

+   plotvaluetags (是)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (否)

PlotLines:

+   anyn:

### ApplyN

为给定周期计算`func`

Formula:

+   line = func(data, period)

Lines:

+   应用

Params:

+   period (1)

+   func (无)

PlotInfo:

+   plot (是)

+   plotmaster (无)

+   legendloc (无)

+   subplot (是)

+   plotname ()

+   plotskip (否)

+   plotabove (否)

+   plotlinelabels (否)

+   plotlinevalues (是)

+   plotvaluetags (是)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (否)

PlotLines:

+   应用:

### AroonDown

这是由 Tushar Chande 于 1995 年开发的 AroonUpDown 指标的 AroonDown。

Formula:

+   down = 100 * (period - 最低点距离) / period

Note:

```py
The lines oscillate between 0 and 100\. That means that the “distance” to
the last highest or lowest must go from 0 to period so that the formula
can yield 0 and 100.

Hence the lookback period is period + 1, because the current bar is also
taken into account. And therefore this indicator needs an effective
lookback period of period + 1.
```

参见：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:aroon`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:aroon)

线条：

+   aroondown

参数：

+   周期 (14)

+   上限线 (70)

+   下限线 (30)

绘图信息：

+   绘图 (True)

+   plotmaster (None)

+   图例位置 (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.05)

+   plotyhlines ([0, 100])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线条：

+   aroondown:

### AroonOscillator

别名：

+   AroonOsc

这是 AroonUpDown 指标的变体，显示 AroonUp 和 AroonDown 值之间的当前差异，试图呈现一种表明哪个更强的可视化效果（大于 0 -> AroonUp，小于 0 -> AroonDown）

公式：

+   aroonosc = aroonup - aroondown

查看：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:aroon`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:aroon)

线条：

+   aroonosc

参数：

+   周期 (14)

+   上限线 (70)

+   下限线 (30)

绘图信息：

+   绘图 (True)

+   plotmaster (None)

+   图例位置 (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.05)

+   plotyhlines ([0, 100])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线条：

+   aroonosc：

### AroonUp

这是 1995 年 Tushar Chande 开发的 AroonUpDown 指标中的 AroonUp。

公式：

+   up = 100 * (周期 - 距离最高点的距离) / 周期

注意：

```py
The lines oscillate between 0 and 100\. That means that the “distance” to
the last highest or lowest must go from 0 to period so that the formula
can yield 0 and 100.

Hence the lookback period is period + 1, because the current bar is also
taken into account. And therefore this indicator needs an effective
lookback period of period + 1.
```

查看：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:aroon`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:aroon)

线条：

+   aroonup

参数：

+   周期 (14)

+   上限线 (70)

+   下限线 (30)

绘图信息：

+   绘图 (True)

+   plotmaster (None)

+   图例位置 (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.05)

+   plotyhlines ([0, 100])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线条：

+   aroonup:

### AroonUpDown

别名：

+   AroonIndicator

由 Tushar Chande 于 1995 年开发。

它试图通过计算给定周期内最后高点/低点的距离来确定趋势是否存在（AroonUp/AroonDown）

公式：

+   up = 100 * (周期 - 距离最高点的距离) / 周期

+   down = 100 * (周期 - 距离最低点的距离) / 周期

注意：

```py
The lines oscillate between 0 and 100\. That means that the “distance” to
the last highest or lowest must go from 0 to period so that the formula
can yield 0 and 100.

Hence the lookback period is period + 1, because the current bar is also
taken into account. And therefore this indicator needs an effective
lookback period of period + 1.
```

查看：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:aroon`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:aroon)

线条：

+   aroonup

+   aroondown

参数：

+   周期 (14)

+   上限线 (70)

+   下限线 (30)

绘图信息：

+   绘图 (True)

+   plotmaster (None)

+   图例位置 (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.05)

+   plotyhlines ([0, 100])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线条：

+   aroonup:

+   aroondown:

### AroonUpDownOscillator

别名：

+   AroonUpDownOsc

同时显示 AroonUpDown 和 AroonOsc 的指标

公式：

```py
(None, uses the aforementioned indicators)
```

另请参阅：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:aroon`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:aroon)

线条：

+   aroonup

+   aroondown

+   aroonosc

参数：

+   周期（14）

+   上限（70）

+   下限（30）

绘图信息：

+   绘图（True）

+   绘图主控制（None）

+   说明位置（None）

+   子绘图（True）

+   绘图名称（）

+   跳过绘制（False）

+   在上方绘制（False）

+   绘制线条标签（False）

+   绘制线条值（True）

+   绘制值标签（True）

+   绘图 Y 轴边距（0.05）

+   绘制 Y 轴水平线（[0, 100]）

+   绘制 Y 轴刻度（[]）

+   绘制水平线（[]）

+   绘制力度（False）

绘图线条：

+   aroonup：

+   aroondown：

+   aroonosc：

### 平均值

别名：

+   算术平均数，平均值

对给定的数据进行算术平均化处理，以一定周期为基础

公式：

+   av = data(period) / period

另请参阅：

+   [`zh.wikipedia.org/wiki/算術平均`](https://en.wikipedia.org/wiki/Arithmetic_mean)

线条：

+   av

参数：

+   周期（1）

绘图信息：

+   绘图（True）

+   绘图主控制（None）

+   说明位置（None）

+   子绘图（True）

+   绘图名称（）

+   跳过绘制（False）

+   在上方绘制（False）

+   绘制线条标签（False）

+   绘制线条值（True）

+   绘制值标签（True）

+   绘制 Y 轴边距（0.0）

+   绘制 Y 轴水平线（[]）

+   绘制 Y 轴刻度（[])

+   绘制水平线（[]）

+   绘制力度（False）

绘图线条：

+   av：

### 平均方向运动指数

别名：

+   ADX

由 J. Welles Wilder，Jr.在其书籍*“Technical Trading Systems”*中于 1978 年首次定义。

旨在衡量趋势强度

此指标仅显示 ADX：

+   使用 PlusDirectionalIndicator（PlusDI）来获取+DI

+   使用 MinusDirectionalIndicator（MinusDI）来获取-DI

+   使用 Directional Indicator（DI）来获取+DI，-DI

+   使用 AverageDirectionalIndexRating（ADXR）来获取 ADX，ADXR

+   使用 DirectionalMovementIndex（DMI）来获取 ADX，+DI，-DI

+   使用 DirectionalMovement（DM）来获取 ADX，ADXR，+DI，-DI

公式：

+   upmove = high - high(-1)

+   downmove = low(-1) - low

+   +dm = upmove 如果 upmove > downmove 且 upmove > 0 则为 upmove，否则为 0

+   -dm = downmove 如果 downmove > upmove 且 downmove > 0 则为 downmove，否则为 0

+   +di = 100 * MovingAverage(+dm, period) / atr(period)

+   -di = 100 * MovingAverage(-dm, period) / atr(period)

+   dx = 100 * abs(+di - -di) / (+di + -di)

+   adx = MovingAverage(dx, period)

使用的移动平均线是最初由 Wilder 定义的那种，即 SmoothedMovingAverage

查看：

+   [`zh.wikipedia.org/wiki/平均方向指數`](https://en.wikipedia.org/wiki/Average_directional_movement_index)

线条：

+   adx

参数：

+   周期（14）

+   movav（SmoothedMovingAverage）

绘图信息：

+   绘图（True）

+   绘图主控制（None）

+   说明位置（None）

+   子绘图（True）

+   绘图名称（）

+   跳过绘制（False）

+   在上方绘制（False）

+   绘制线条标签（False）

+   绘制线条值（True）

+   绘制值标签（True）

+   绘制 Y 轴边距（0.0）

+   绘制 Y 轴水平线（[]）

+   绘制 Y 轴刻度（[]）

+   绘制水平线（[]）

+   绘制力度（False）

绘制线条：

+   plusDI：

    +   _name (+DI)

+   minusDI：

    +   _name (-DI)

+   adx：

    +   _name (ADX)

### 平均方向运动指数评级

别名：

+   ADXR

由 J. Welles Wilder，Jr.在其书籍*“Technical Trading Systems”*中于 1978 年首次定义。

旨在衡量趋势强度。

ADXR 是 ADX 在周期棒之前的平均值

This indicator shows the ADX and ADXR:

+   Use PlusDirectionalIndicator (PlusDI) to get +DI

+   Use MinusDirectionalIndicator (MinusDI) to get -DI

+   Use Directional Indicator (DI) to get +DI, -DI

+   Use AverageDirectionalIndex (ADX) to get ADX

+   Use DirectionalMovementIndex (DMI) to get ADX, +DI, -DI

+   Use DirectionalMovement (DM) to get ADX, ADXR, +DI, -DI

Formula:

+   upmove = high - high(-1)

+   downmove = low(-1) - low

+   +dm = upmove if upmove > downmove and upmove > 0 else 0

+   -dm = downmove if downmove > upmove and downmove > 0 else 0

+   +di = 100 * MovingAverage(+dm, period) / atr(period)

+   -di = 100 * MovingAverage(-dm, period) / atr(period)

+   dx = 100 * abs(+di - -di) / (+di + -di)

+   adx = MovingAverage(dx, period)

+   adxr = (adx + adx(-period)) / 2

使用的移动平均线是最初由 Wilder 定义的 SmoothedMovingAverage

See:

+   [`en.wikipedia.org/wiki/Average_directional_movement_index`](https://en.wikipedia.org/wiki/Average_directional_movement_index)

Lines:

+   adx

+   adxr

Params:

+   period (14)

+   movav (SmoothedMovingAverage)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   plusDI:

    +   _name (+DI)

+   minusDI:

    +   _name (-DI)

+   adx:

    +   _name (ADX)

+   adxr:

    +   _name (ADXR)

### AverageTrueRange

Alias:

+   ATR

Defined by J. Welles Wilder, Jr. in 1978 in his book *“New Concepts in Technical Trading Systems”*.

这个想法是考虑收盘价来计算范围，如果它产生的范围比日间范围（高 - 低）大。

Formula:

+   SmoothedMovingAverage(TrueRange, period)

See:

+   [`en.wikipedia.org/wiki/Average_true_range`](https://en.wikipedia.org/wiki/Average_true_range)

Lines:

+   atr

Params:

+   period (14)

+   movav (SmoothedMovingAverage)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   atr:

### AwesomeOscillator

Alias:

+   AwesomeOsc, AO

Awesome Oscillator (AO) 是一个动量指标，反映市场推动力的精确变化，有助于识别趋势的强度直至形成和逆转点。

Formula:

+   median price = (high + low) / 2

+   AO = SMA(median price, 5)- SMA(median price, 34)

See:

+   [`www.metatrader5.com/en/terminal/help/indicators/bw_indicators/awesome`](https://www.metatrader5.com/en/terminal/help/indicators/bw_indicators/awesome)

+   [`www.ifcmarkets.com/en/ntx-indicators/awesome-oscillator`](https://www.ifcmarkets.com/en/ntx-indicators/awesome-oscillator)

Lines:

+   ao

Params:

+   fast (5)

+   slow (34)

+   movav (SMA)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

绘图线：

+   ao：

    +   _method（bar）

    +   alpha（0.5）

    +   width（1.0）

### BaseApplyN

应用于 ApplyN 和其他可能接受`func`作为参数但希望在指标中定义线条的基类。

在给定的周期内计算`func`，其中 func 被作为参数，也称为命名参数或`kwarg`

公式：

+   lines[0] = func（data，period）

除第一行（索引 0）外定义的任何额外行都不会被计算

参数：

+   period（1）

+   func（None）

绘图信息：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

### BollingerBands

别名：

+   BBands

由约翰·伯林格在 80 年代定义。它通过在 x 标准偏差处定义上限和下限来衡量波动性

公式：

+   midband = SimpleMovingAverage（close，period）

+   topband = midband + devfactor * 标准差（数据，期间）

+   botband = midband - devfactor * 标准差（数据，期间）

参见：

+   [`en.wikipedia.org/wiki/Bollinger_Bands`](https://en.wikipedia.org/wiki/Bollinger_Bands)

线条：

+   mid

+   top

+   bot

参数：

+   期间（20）

+   devfactor（2.0）

+   movav（MovingAverageSimple）

绘图信息：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（False）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

绘图线：

+   mid：

    +   ls（–）

+   top：

    +   _samecolor（True）

+   bot：

    +   _samecolor（True）

### BollingerBandsPct

使用百分比线扩展布林带

线条：

+   mid

+   top

+   bot

+   pctb

参数：

+   期间（20）

+   devfactor（2.0）

+   movav（MovingAverageSimple）

绘图信息：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（False）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

绘图线：

+   mid：

    +   ls（–）

+   top：

    +   _samecolor（True）

+   bot：

    +   _samecolor（True）

+   pctb：

    +   _name（％B）

### CointN

计算给定`period`的数据源的分数（coint_t）和 pvalue

使用`pandas`和`statsmodels`（用于`coint`）

线条：

+   分数

+   pvalue

参数：

+   期间（10）

+   回归©

绘图信息：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

绘图线：

+   score：

+   pvalue：

### 商品频道指数

别名：

+   CCI

由唐纳德·兰伯特于 1980 年引入，用于测量“典型价格”（见下文）从其均值的变化，以识别极端和反转

公式：

+   tp = 典型价格=（高+低+收盘）/ 3

+   tpmean = MovingAverage（tp，period）

+   偏差= tp  - tpmean

+   meandev = MeanDeviation（tp）

+   cci = 偏差/（meandeviation * factor）

参见：

+   [`en.wikipedia.org/wiki/Commodity_channel_index`](https://en.wikipedia.org/wiki/Commodity_channel_index)

线条：

+   cci

参数：

+   期间（20）

+   因子（0.015）

+   movav（MovingAverageSimple）

+   upperband（100.0）

+   lowerband（-100.0）

绘图信息：

+   绘图（真）

+   plotmaster（无）

+   legendloc（无）

+   subplot（真）

+   plotname（）

+   plotskip（假）

+   plotabove（假）

+   plotlinelabels（假）

+   plotlinevalues（真）

+   plotvaluetags（真）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（假）

PlotLines：

+   cci：

### CrossDown

如果第 1 个提供的数据向上穿过第 2 个指标，则此指标发出信号

它确实需要查看第 1 个和第 2 个数据的当前时间索引（0）和前一个时间索引（-1）

公式：

+   diff = data - data1

+   downcross = last_non_zero_diff > 0 and data0(0) < data1(0)

线条：

+   交叉

绘图信息：

+   绘图（真）

+   plotmaster（无）

+   legendloc（无）

+   subplot（真）

+   plotname（）

+   plotskip（假）

+   plotabove（假）

+   plotlinelabels（假）

+   plotlinevalues（真）

+   plotvaluetags（真）

+   plotymargin（0.05）

+   plotyhlines（[0.0，1.0]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（假）

PlotLines：

+   交叉：

### CrossOver

如果提供的数据（2）向上或向下交叉，则此指标发出信号。

+   如果第 1 个数据向上穿过第 2 个数据，那么为 1.0

+   如果第 1 个数据向下穿过第 2 个数据，则为-1.0

它确实需要查看第 1t 和第 2 个数据的当前时间索引（0）和前一个时间索引（-1）

公式：

+   diff = data - data1

+   upcross = last_non_zero_diff < 0 and data0(0) > data1(0)

+   downcross = last_non_zero_diff > 0 and data0(0) < data1(0)

+   交叉 = upcross - downcross

线条：

+   交叉点

绘图信息：

+   绘图（真）

+   plotmaster（无）

+   legendloc（无）

+   subplot（真）

+   plotname（）

+   plotskip（假）

+   plotabove（假）

+   plotlinelabels（假）

+   plotlinevalues（真）

+   plotvaluetags（真）

+   plotymargin（0.05）

+   plotyhlines（[-1.0，1.0]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（假）

PlotLines：

+   交叉：

### CrossUp

如果第 1 个提供的数据向上穿过第 2 个指标，则此指标发出信号

它确实需要查看第 1 个和第 2 个数据的当前时间索引（0）和前一个时间索引（-1）

公式：

+   diff = data - data1

+   upcross = last_non_zero_diff < 0 and data0(0) > data1(0)

线条：

+   交叉

绘图信息：

+   绘图（真）

+   plotmaster（无）

+   legendloc（无）

+   subplot（真）

+   plotname（）

+   plotskip（假）

+   plotabove（假）

+   plotlinelabels（假）

+   plotlinevalues（真）

+   plotvaluetags（真）

+   plotymargin（0.05）

+   plotyhlines（[0.0，1.0]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（假）

PlotLines：

+   交叉：

### DV2

由[`cssanalytics.wordpress.com/`](http://cssanalytics.wordpress.com/)的 David Varadi 开发的 RSI(2)替代品

这似乎是*有界*版本。

另见：

+   [`web.archive.org/web/20131216100741/http://quantingdutchman.wordpress.com/2010/08/06/dv2-indicator-for-amibroker/`](http://web.archive.org/web/20131216100741/http://quantingdutchman.wordpress.com/2010/08/06/dv2-indicator-for-amibroker/)

线条：

+   dv2

Params:

+   period (252)

+   maperiod (2)

+   _movav (SMA)

PlotInfo:

+   plot (是)

+   plotmaster (无)

+   legendloc (无)

+   subplot (是)

+   plotname ()

+   plotskip (否)

+   plotabove (否)

+   plotlinelabels (否)

+   plotlinevalues (是)

+   plotvaluetags (是)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (否)

PlotLines:

+   dv2:

### DemarkPivotPoint

通过考虑较大时间段的过去周期内价格条组件的平均值来定义显著水平。例如，在操作天数时，值是从已经“过去”的月份固定价格中获取的。

使用此指标的示例:

data = btfeeds.ADataFeed(dataname=x, timeframe=bt.TimeFrame.Days) cerebro.adddata(data) cerebro.resampledata(data, timeframe=bt.TimeFrame.Months)

在策略的`__init__`方法中：

pivotindicator = btind.DemarkPivotPoiont(self.data1) # 重新采样的数据

该指标将尝试自动绘制到非重新采样的数据。要禁用此行为，请在构造过程中使用以下内容:

+   _autoplot=False

注意:

示例显示*days*和*months*，但可以使用任何时间段的组合。请参阅文献以获取推荐的组合

Formula:

+   如果 close < open x = high + (2 x low) + close

+   如果 close > open x = (2 x high) + low + close

+   如果 Close == open x = high + low + (2 x close)

+   p = x / 4

+   support1 = x / 2 - high

+   resistance1 = x / 2 - low

参见:

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:pivot_points`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:pivot_points)

Lines:

+   p

+   s1

+   r1

Params:

+   open (否)

+   close (否)

+   _autoplot (是)

+   level1 (0.382)

+   level2 (0.618)

+   level3 (1.0)

PlotInfo:

+   plot (是)

+   plotmaster (无)

+   legendloc (无)

+   subplot (否)

+   plotname ()

+   plotskip (否)

+   plotabove (否)

+   plotlinelabels (否)

+   plotlinevalues (是)

+   plotvaluetags (是)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (否)

PlotLines:

+   p:

+   s1:

+   r1:

### DetrendedPriceOscillator

Alias:

+   DPO

由 Joe DiNapoli 在他的书籍*“使用 DiNapoli 水平交易”*中定义

它测量价格变动与移动平均线（趋势）之间的差异，因此从价格中去除了“趋势”因素。

Formula:

+   movav = MovingAverage(close, period)

+   dpo = close - movav(偏移周期 / 2 + 1)

参见:

+   [`en.wikipedia.org/wiki/Detrended_price_oscillator`](https://en.wikipedia.org/wiki/Detrended_price_oscillator)

Lines:

+   dpo

Params:

+   period (20)

+   movav (MovingAverageSimple)

PlotInfo:

+   plot (是)

+   plotmaster (无)

+   legendloc (无)

+   subplot (是)

+   plotname ()

+   plotskip (否)

+   plotabove (否)

+   plotlinelabels (否)

+   plotlinevalues (是)

+   plotvaluetags (是)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([0.0])

+   plotforce (否)

PlotLines:

+   dpo:

### DicksonMovingAverage

Alias:

+   DMA, DicksonMA

由 Nathan Dickson 定义

*Dickson Moving Average* 结合了 `ZeroLagIndicator`（又称 *ErrorCorrecting* 或 *EC*） by *Ehlers*，和 `HullMovingAverage` 试图提供接近 *Jurik* Moving Averages 的结果

公式：

+   ec = ZeroLagIndicator(period, gainlimit)

+   hma = HullMovingAverage(hperiod)

+   dma =（ec + hma）/ 2

+   *ZeroLagIndicator* 的默认移动平均是 EMA，但可以通过参数 *_movav* 更改

    -*注意**：传入的移动平均必须计算 alpha（和 1 - alpha），并将它们作为属性 `alpha` 和 `alpha1` 可用

+   第 2^(nd) 个移动平均可以通过参数 *_hma* 从 *Hull* 更改为其他任何东西

另请参阅：

+   [`www.reddit.com/r/algotrading/comments/4xj3vh/dickson_moving_average`](https://www.reddit.com/r/algotrading/comments/4xj3vh/dickson_moving_average)

线：

+   dma

参数：

+   周期（30）

+   gainlimit（50）

+   hperiod（7）

+   _movav（EMA）

+   _hma（HMA）

PlotInfo：

+   绘图（True）

+   plotmaster（无）

+   图例位置（无）

+   子图（False）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   dma：

### DicksonMovingAverageEnvelope

别名：

+   DMAEnvelope、DicksonMAEnvelope

DicksonMovingAverage 和信封带将其与 “perc” 分开

公式：

+   dma（来自 DicksonMovingAverage）

+   顶部 = dma *（1 + perc）

+   机器人 = dma *（1 - perc）

另请参阅：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes)

线：

+   dma

+   顶部

+   机器人

参数：

+   周期（30）

+   gainlimit（50）

+   hperiod（7）

+   _movav（EMA）

+   _hma（HMA）

+   perc（2.5）

PlotInfo：

+   绘图（True）

+   plotmaster（无）

+   图例位置（无）

+   子图（False）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   dma：

+   顶部：

    +   _samecolor（True）

+   机器人：

    +   _samecolor（True）

### DicksonMovingAverageOscillator

别名：

+   DicksonMovingAverageOsc、DMAOscillator、DMAOsc、DicksonMAOscillator、DicksonMAOsc

DicksonMovingAverage 围绕其数据的振荡

线：

+   dma

参数：

+   周期（30）

+   gainlimit（50）

+   hperiod（7）

+   _movav（EMA）

+   _hma（HMA）

PlotInfo：

+   绘图（True）

+   plotmaster（无）

+   图例位置（无）

+   子图（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   dma：

+   _0：

    +   _name（osc）

### DirectionalIndicator

别名：

+   DI

由 J. Welles Wilder, Jr. 在 1978 年在他的书 *“New Concepts in Technical Trading Systems”* 中定义。

旨在衡量趋势强度

此指标显示 +DI、-DI：

+   使用 PlusDirectionalIndicator（PlusDI）获取 +DI

+   使用 MinusDirectionalIndicator（MinusDI）获取 -DI

+   使用 AverageDirectionalIndex（ADX）获取 ADX

+   使用 AverageDirectionalIndexRating（ADXR）获取 ADX、ADXR

+   使用 DirectionalMovementIndex（DMI）获取 ADX，+DI，-DI

+   使用 DirectionalMovement（DM）获取 ADX，ADXR，+DI，-DI

公式：

+   upmove = high - high(-1)

+   downmove = low(-1) - low

+   +dm = 如果 upmove > downmove 且 upmove > 0 则为 upmove，否则为 0

+   -dm = 如果 downmove > upmove 且 downmove > 0 则为 downmove，否则为 0

+   +di = 100 * MovingAverage(+dm, period) / atr(period)

+   -di = 100 * MovingAverage(-dm, period) / atr(period)

使用的移动平均线是最初由 Wilder 定义的 SmoothedMovingAverage

参见：

+   [`en.wikipedia.org/wiki/Average_directional_movement_index`](https://en.wikipedia.org/wiki/Average_directional_movement_index)

Lines：

+   plusDI

+   minusDI

参数：

+   周期（14）

+   movav（SmoothedMovingAverage）

PlotInfo：

+   绘图（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   plusDI：

+   minusDI：

### DirectionalMovement

别名：

+   DM

由 J. Welles Wilder, Jr.在他的书*“技术交易系统中的新概念”*中于 1978 年定义。

旨在衡量趋势强度

此指标显示 ADX，ADXR，+DI，-DI。

+   使用 PlusDirectionalIndicator（PlusDI）获取+DI

+   使用 MinusDirectionalIndicator（MinusDI）获取-DI

+   使用 Directional Indicator（DI）获取+DI，-DI

+   使用 AverageDirectionalIndex（ADX）获取 ADX

+   使用 AverageDirectionalIndexRating（ADXR）获取 ADX，ADXR

+   使用 DirectionalMovementIndex（DMI）获取 ADX，+DI，-DI

公式：

+   upmove = high - high(-1)

+   downmove = low(-1) - low

+   +dm = 如果 upmove > downmove 且 upmove > 0 则为 upmove，否则为 0

+   -dm = 如果 downmove > upmove 且 downmove > 0 则为 downmove，否则为 0

+   +di = 100 * MovingAverage(+dm, period) / atr(period)

+   -di = 100 * MovingAverage(-dm, period) / atr(period)

+   dx = 100 * abs(+di - -di) / (+di + -di)

+   adx = MovingAverage(dx, period)

使用的移动平均线是最初由 Wilder 定义的 SmoothedMovingAverage

参见：

+   [`en.wikipedia.org/wiki/Average_directional_movement_index`](https://en.wikipedia.org/wiki/Average_directional_movement_index)

Lines：

+   adx

+   adxr

+   plusDI

+   minusDI

参数：

+   周期（14）

+   movav（SmoothedMovingAverage）

PlotInfo：

+   绘图（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   plusDI：

+   minusDI：

+   adx：

    +   _name（ADX）

+   adxr：

    +   _name（ADXR）

### DirectionalMovementIndex

别名：

+   DMI

由 J. Welles Wilder, Jr.在他的书*“技术交易系统中的新概念”*中于 1978 年定义。

旨在衡量趋势强度

此指标显示 ADX，+DI，-DI：

+   使用 PlusDirectionalIndicator（PlusDI）获取+DI

+   使用 MinusDirectionalIndicator（MinusDI）获取-DI

+   使用 Directional Indicator（DI）获取+DI，-DI

+   使用 AverageDirectionalIndex（ADX）获取 ADX

+   使用 AverageDirectionalIndexRating（ADXRating）获取 ADX，ADXR

+   使用方向运动（DM）来获取 ADX、ADXR、+DI、-DI

公式：

+   上升幅度 = 最高 - 最高(-1)

+   下降幅度 = 低(-1) - 低

+   +dm = 上升幅度 如果 上升幅度 > 下降幅度 并且 上升幅度 > 0 则 0

+   -dm = 下降幅度 如果 下降幅度 > 上升幅度 并且 下降幅度 > 0 则 0

+   +di = 100 * 移动平均值(+dm, period) / atr(period)

+   -di = 100 * 移动平均值(-dm, period) / atr(period)

+   dx = 100 * abs(+di - -di) / (+di + -di)

+   adx = 移动平均值(dx, period)

使用的移动平均值是最初由 Wilder 定义的平滑移动平均

参见：

+   [`zh.wikipedia.org/wiki/%E5%B9%B3%E5%9D%87%E6%96%B9%E5%90%91%E5%8F%91%E5%B1%95%E6%8C%87%E6%95%B0`](https://zh.wikipedia.org/wiki/%E5%B9%B3%E5%9D%87%E6%96%B9%E5%90%91%E5%8F%91%E5%B1%95%E6%8C%87%E6%95%B0)

线条：

+   adx

+   plusDI

+   minusDI

参数：

+   期间（14）

+   movav（平滑移动平均）

图形信息：

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线：

+   plusDI：

+   minusDI：

+   adx：

    +   _name (ADX)

### 双指数移动平均

别名：

+   DEMA，双指数移动平均

DEMA 首次于 1994 年在“股票与商品技术分析”杂志中 Patrick G. Mulloy 的文章“用更快的移动平均值平滑数据”中引入。

它试图减少与移动平均相关的固有滞后

公式：

+   dema = (2.0 - ema(data, period) - ema(ema(data, period), period)

参见：

```py
(None)
```

线条：

+   dema

参数：

+   期间（30）

+   _movav (EMA)

图形信息：

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (False)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线：

+   dema：

### 双指数移动平均信封

别名：

+   DEMA 信封，移动平均双指数信封

双指数移动平均和信封带将其与“perc”分开

公式：

+   dema（来自双指数移动平均）

+   顶部 = dema * (1 + perc)

+   bot = dema * (1 - perc)

另请参阅：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes)

线条：

+   dema

+   顶部

+   bot

参数：

+   期间（30）

+   _movav (EMA)

+   perc (2.5)

图形信息：

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (False)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线：

+   dema：

+   顶部：

    +   _samecolor (True)

+   bot：

    +   _samecolor (True)

### 双指数移动平均振荡器

别名：

+   双指数移动平均振荡器，DEMA 振荡器，DEMAOsc，移动平均双指数振荡器，移动平均双指数振荡器

双指数移动平均在其数据周围的振荡

线条：

+   dema

参数：

+   期间（30）

+   _movav (EMA)

图形信息：

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   dema：

+   _0：

    +   _name（osc）

### 下降日

由 J. Welles Wilder，Jr.于 1978 年在他的书籍*“技术交易系统中的新概念”*中为 RSI 定义

记录了“下降”的天数，即：收盘价低于前一天。

公式：

+   downday = max（close_prev - close，0）

另请参阅：

+   [维基百科 - 相对强度指数](https://en.wikipedia.org/wiki/Relative_strength_index)

Lines：

+   downday

Params：

+   期间（1）

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   downday：

### 下降日

由 J. Welles Wilder，Jr.于 1978 年在他的书籍*“技术交易系统中的新概念”*中为 RSI 定义

记录了“下降”的天数，即：收盘价低于前一天。

注意：

+   此版本返回一个布尔值，而不是差值

公式：

+   downday = close_prev > close

另请参阅：

+   [维基百科 - 相对强度指数](https://en.wikipedia.org/wiki/Relative_strength_index)

Lines：

+   downday

Params：

+   期间（1）

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   downday：

### 下降幅度

由 J. Welles Wilder，Jr.于 1978 年在他的书籍*“技术交易系统中的新概念”*中定义，作为方向运动系统的一部分来计算方向指标。

如果给定数据低于前一天，则为正值

公式：

+   downmove = 数据（-1） - 数据

另请参阅：

+   [维基百科 - 平均方向运动指数](https://en.wikipedia.org/wiki/Average_directional_movement_index)

Lines：

+   downmove

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   downmove：

### 包络

它创建了与给定百分比分隔的源数据的包络带

公式：

+   src = 数据源

+   顶部 = src *（1 + perc）

+   bot = src *（1 - perc）

另请参阅：

+   [股票图表学校 - 技术指标 - 移动平均包络](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes)

Lines：

+   src

+   顶部

+   bot

Params：

+   perc（2.5）

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（False）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘制线：

+   源：

    +   _plotskip (True)

+   top：

    +   _samecolor (True)

+   bot：

    +   _samecolor (True)

### 指数移动平均

别名：

+   EMA，指数移动平均

通过指数移动平滑数据的移动平均。

它是 SmoothingMovingAverage 的子类。

+   self.smfactor -> 2 / (1 + period)

+   self.smfactor1 -> 1 - self.smfactor

公式：

+   movav = prev * (1.0 - smoothfactor) + newdata * smoothfactor

另请参阅：

+   [`en.wikipedia.org/wiki/Moving_average#Exponential_moving_average`](https://en.wikipedia.org/wiki/Moving_average#Exponential_moving_average)

线条：

+   指数移动平均

参数：

+   周期 (30)

绘图信息：

+   绘制 (True)

+   plotmaster (None)

+   图例位置 (None)

+   subplot (False)

+   绘图名称 ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘制线：

+   ema：

### 指数移动平均包络

别名：

+   EMA 包络，指数移动平均包络

指数移动平均和从中分离出的包络带“perc”

公式：

+   ema（来自指数移动平均）

+   top = ema * (1 + perc)

+   bot = ema * (1 - perc)

另请参阅：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes)

线条：

+   指数移动平均

+   top

+   bot

参数：

+   周期 (30)

+   百分比 (2.5)

绘图信息：

+   绘制 (True)

+   plotmaster (None)

+   图例位置 (None)

+   subplot (False)

+   绘图名称 ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘制线：

+   ema：

+   top：

    +   _samecolor (True)

+   bot：

    +   _samecolor (True)

### 指数移动平均振荡器

别名：

+   指数移动平均振荡器，EMAOscillator，EMAOsc，MovingAverageExponentialOscillator，MovingAverageExponentialOsc

指数移动平均围绕其数据的振荡

线条：

+   指数移动平均

参数：

+   周期 (30)

绘图信息：

+   绘制 (True)

+   plotmaster (None)

+   图例位置 (None)

+   subplot (True)

+   绘图名称 ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘制线：

+   ema：

+   _0：

    +   _name (osc)

### 指数平滑

别名：

+   指数平滑法

通过指数平滑在一段时间内平均给定数据

以常规算术平均（平均值）作为种子值，考虑数据的前几个周期值

公式：

+   av = prev * (1 - alpha) + data * alpha

另请参阅：

+   [`en.wikipedia.org/wiki/Exponential_smoothing`](https://en.wikipedia.org/wiki/Exponential_smoothing)

线条：

+   av

参数：

+   周期 (1)

+   alpha (None)

绘图信息：

+   绘制 (True)

+   plotmaster (None)

+   图例位置 (None)

+   subplot (True)

+   绘图名称 ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   av：

### ExponentialSmoothingDynamic

别名：

+   ExpSmoothingDynamic

对给定数据进行指数平滑处理，使用指数平滑化

正常的算术平均值（平均值）被视为数据的第一个周期值的种子值

注意：

+   alpha 是一个可以动态计算的值数组

公式：

+   av = prev * (1 - alpha) + data * alpha

也参见：

+   [`en.wikipedia.org/wiki/Exponential_smoothing`](https://en.wikipedia.org/wiki/Exponential_smoothing)

Lines:

+   av

参数：

+   period (1)

+   alpha (None)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   av：

### FibonacciPivotPoint

通过考虑较大时间框架的过去周期内价格条组件的平均值来定义显着水平。例如，在处理天数时，值来自已经“过去”的月份固定价格。

Fibonacci 级别（可配置）用于定义支撑/阻力级别

使用此指标的示例：

data = btfeeds.ADataFeed(dataname=x, timeframe=bt.TimeFrame.Days) cerebro.adddata(data) cerebro.resampledata(data, timeframe=bt.TimeFrame.Months)

在策略的 `__init__` 方法中：

pivotindicator = btind.FibonacciPivotPoiont(self.data1) # resampled 数据

此指标将尝试自动绘制到非重新采样的数据。要禁用此行为，请在构造过程中使用以下内容：

+   _autoplot=False

注意：

示例显示*天*和*月*，但可以使用任何时间范围的组合。请参阅文献以获取推荐的组合

公式：

+   pivot = (h + l + c) / 3 # 变体重复关闭或添加打开

+   support1 = p - level1 * (high - low) # level1 0.382

+   support2 = p - level2 * (high - low) # level2 0.618

+   support3 = p - level3 * (high - low) # level3 1.000

+   resistance1 = p + level1 * (high - low) # level1 0.382

+   resistance2 = p + level2 * (high - low) # level2 0.618

+   resistance3 = p + level3 * (high - low) # level3 1.000

参见：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:pivot_points`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:pivot_points)

Lines:

+   p

+   s1

+   s2

+   s3

+   r1

+   r2

+   r3

参数：

+   open (False)

+   close (False)

+   _autoplot (True)

+   level1 (0.382)

+   level2 (0.618)

+   level3 (1.0)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (False)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   p：

+   s1：

+   s2:

+   s3：

+   r1:

+   r2：

+   r3：

### FindFirstIndex

返回满足由参数 _evalfunc 生成的条件的最后一个数据的索引

注意：

```py
Returned indexes look backwards. 0 is the current index and 1 is
the previous bar.
```

公式：

+   索引=第一个为 data[index] == _evalfunc（data）的数据索引

线条：

+   索引

参数：

+   期间（1）

+   _evalfunc（None）

绘图信息：

+   绘图（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   绘图线值（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

绘图线：

+   索引：

### FindFirstIndexHighest

返回在期间内最高的最后数据的索引

注意：

```py
Returned indexes look backwards. 0 is the current index and 1 is
the previous bar.
```

公式：

+   索引=第一个数据的索引，这是最高的

线条：

+   索引

参数：

+   期间（1）

+   _evalfunc（<built-in function="" max="">）</built-in>

绘图信息：

+   绘图（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   绘图线值（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

绘图线：

+   索引：

### FindFirstIndexLowest

返回在期间内最低的第一个数据的索引

注意：

```py
Returned indexes look backwards. 0 is the current index and 1 is
the previous bar.
```

公式：

+   索引=第一个数据的索引，这是最低的

线条：

+   索引

参数：

+   期间（1）

+   _evalfunc（<built-in function="" min="">）</built-in>

绘图信息：

+   绘图（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   绘图线值（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

绘图线：

+   索引：

### FindLastIndex

返回满足由参数 _evalfunc 生成的条件的最后数据的索引

注意：

```py
Returned indexes look backwards. 0 is the current index and 1 is
the previous bar.
```

公式：

+   索引=最后一个为 data[index] == _evalfunc（data）的数据索引

线条：

+   索引

参数：

+   期间（1）

+   _evalfunc（None）

绘图信息：

+   绘图（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   绘图线值（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

绘图线：

+   索引：

### FindLastIndexHighest

返回在期间内最高的最后数据的索引

注意：

```py
Returned indexes look backwards. 0 is the current index and 1 is
the previous bar.
```

公式：

+   索引=最后一条数据的索引，这是最高的

线条：

+   索引

参数：

+   期间（1）

+   _evalfunc（<built-in function="" max="">）</built-in>

绘图信息：

+   绘图（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   绘图线值（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

绘图线：

+   索引：

### FindLastIndexLowest

返回在期间内最低的最后数据的索引

注意：

```py
Returned indexes look backwards. 0 is the current index and 1 is
the previous bar.
```

公式：

+   索引=最后一条数据的索引，这是最低的

线条：

+   索引

参数：

+   期间（1）

+   _evalfunc（<built-in function="" min="">）</built-in>

绘图信息：

+   绘图（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   绘图名称（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   绘图线值（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   index：

### 分形

参考资料：

```py
[Ref 1] [`www.investopedia.com/articles/trading/06/fractals.asp`](http://www.investopedia.com/articles/trading/06/fractals.asp)
```

线条：

+   fractal_bearish

+   fractal_bullish

参数：

+   period（5）

+   bardist（0.015）

+   shift_to_potential_fractal（2）

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（False）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   fractal_bearish：

    +   marker（^）

    +   markersize（4.0）

    +   颜色（浅蓝色）

    +   fillstyle（full）

    +   ls（）

+   fractal_bullish：

    +   marker（v）

    +   markersize（4.0）

    +   颜色（浅蓝色）

    +   fillstyle（full）

    +   ls（）

### HeikinAshi

Heikin Ashi 蜡烛线的形式

公式：

```py
ha_open = (ha_open(-1) + ha_close(-1)) / 2
ha_high = max(hi, ha_open, ha_close)
ha_low = min(lo, ha_open, ha_close)
ha_close = (open + high + low + close) / 4
```

另请参阅：

```py
[`en.wikipedia.org/wiki/Candlestick_chart#Heikin_Ashi_candlesticks`](https://en.wikipedia.org/wiki/Candlestick_chart#Heikin_Ashi_candlesticks)
[`stockcharts.com/school/doku.php?id=chart_school:chart_analysis:heikin_ashi`](http://stockcharts.com/school/doku.php?id=chart_school:chart_analysis:heikin_ashi)
```

线条：

+   ha_open

+   ha_high

+   ha_low

+   ha_close

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（False）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   ha_open：

+   ha_high：

+   ha_low：

+   ha_close：

### 最高

别名：

+   MaxN

计算给定周期内数据的最高值

使用内置的`max`进行计算

公式：

+   highest = max（data，period）

线条：

+   highest

参数：

+   period（1）

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   最高：

### HullMovingAverage

别名：

+   HMA，HullMA

由艾伦·赫尔（Alan Hull）提出

Hull 移动平均解决了一个古老的难题，即如何使移动平均对当前价格活动更具响应性，同时保持曲线的平滑性。实际上，HMA 几乎完全消除了滞后，并且同时设法改善了平滑度。

公式：

+   hma = wma（2 * wma（data，period // 2） - wma（data，period），sqrt（period））

另请参阅：

+   [`alanhull.com/hull-moving-average`](http://alanhull.com/hull-moving-average)

注意：

+   请注意，最终最小周期不是与参数`period`一起传递的周期。在此期间完成了最终的移动平均值，其中周期是原始值的*平方根*。

    在默认情况下的`30`，移动平均产生非 NAN 值之前的最终最小周期为`34`

线条：

+   hma

参数：

+   period（30）

+   _movav（WMA）

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（False）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   hma：

### HullMovingAverageEnvelope

别名：

+   HMAEnvelope，HullMAEnvelope

HullMovingAverage 和包络带与其相隔“perc”

公式：

+   hma（来自 HullMovingAverage）

+   top = hma *（1 + perc）

+   bot = hma *（1 - perc）

另请参阅：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes)

线：

+   hma

+   top

+   bot

参数：

+   周期（30）

+   _movav（WMA）

+   perc（2.5）

PlotInfo:

+   plot（True）

+   plotmaster（无）

+   legendloc（无）

+   subplot（False）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines:

+   hma:

+   top:

    +   _samecolor（True）

+   bot:

    +   _samecolor（True）

### HullMovingAverageOscillator

别名：

+   HullMovingAverageOsc，HMAOscillator，HMAOsc，HullMAOscillator，HullMAOsc

HullMovingAverage 围绕其数据的振荡

线：

+   hma

参数：

+   周期（30）

+   _movav（WMA）

PlotInfo:

+   plot（True）

+   plotmaster（无）

+   legendloc（无）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines:

+   hma:

+   _0:

    +   _name（osc）

### HurstExponent

别名：

```py
- Hurst

References:

- [`www.quantopian.com/posts/hurst-exponent`](https://www.quantopian.com/posts/hurst-exponent)

- [`www.quantopian.com/posts/some-code-from-ernie-chans-new-book-implemented-in-python`](https://www.quantopian.com/posts/some-code-from-ernie-chans-new-book-implemented-in-python)
```

结果的解释

```py
1\. Geometric random walk (H=0.5)

1\. Mean-reverting series (H<0.5)

1\. Trending Series (H>0.5)
```

重要说明：

+   默认周期为`40`，但用户的实验表明，至少需要 2000 个样本（即：至少 2000 个周期）才能获得稳定的值。

+   lag_start 和 lag_end 值默认为`2`和`self.p.period / 2`，除非指定了参数。

    用户的实验也表明，约为`10`和`500`的值会产生良好的结果

原始值（40、2、self.p.period / 2）保留了向后兼容性

线：

+   hurst

参数：

+   周期（40）

+   lag_start（无）

+   lag_end（无）

PlotInfo:

+   plot（True）

+   plotmaster（无）

+   legendloc（无）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines:

+   hurst:

### 一目均衡表

由记者 Goichi Hosoda 于 1969 年开发并发表在他的书中

公式：

+   tenkan_sen =（最高（高，tenkan）+ 最低（低，tenkan））/ 2.0

+   kijun_sen =（最高（高，kijun）+ 最低（低，kijun））/ 2.0

    推进的 2 个值将推迟到未来的 26 个条

+   senkou_span_a =（tenkan_sen + kijun_sen）/ 2.0

+   senkou_span_b =（（最高（高，senkou）+ 最低（低，senkou））/ 2.0

    这被推进到过去的 26 个条

+   chikou = close

云（Kumo）由 senkou_spans 之间的区域形成

查看：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:ichimoku_cloud`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:ichimoku_cloud)

线：

+   tenkan_sen

+   kijun_sen

+   senkou_span_a

+   senkou_span_b

+   chikou_span

参数：

+   tenkan（9）

+   kijun（26）

+   senkou（52）

+   senkou_lead（26）

+   chikou（26）

PlotInfo:

+   plot（True）

+   plotmaster（无）

+   legendloc（无）

+   subplot（False）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   senkou_span_a:

    +   _fill_gt (('senkou_span_b', 'g'))

    +   _fill_lt (('senkou_span_b', 'r'))

+   tenkan_sen:

+   kijun_sen:

+   senkou_span_b:

+   chikou_span:

### KnowSureThing

Alias:

+   KST

它是一种“求和”动量指标。 由马丁·普林格（Martin Pring）开发，并于 1992 年在《股票与商品》（Stocks & Commodities）杂志上发表。

公式：

+   rcma1 = MovAv(roc100(rp1), period)

+   rcma2 = MovAv(roc100(rp2), period)

+   rcma3 = MovAv(roc100(rp3), period)

+   rcma4 = MovAv(roc100(rp4), period)

+   kst = 1.0 * rcma1 + 2.0 * rcma2 + 3.0 * rcma3 + 4.0 * rcma4

+   signal = MovAv(kst, speriod)

参见：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:know_sure_thing_kst`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:know_sure_thing_kst)

参数

+   `rma1`、`rma2`、`rma3`、`rma4`：用于 ROC 的移动平均线

+   `rp1`、`rp2`、`rp3`、`rp4`：用于 ROC 的参数

+   `rsig`: 用于信号线的移动平均

+   `rfactors`：应用于不同 MovAv（ROCs）的因子列表

+   `_movav` 和 `_movavs`，允许更改用于计算 kst 和信号的移动平均类型

Lines:

+   kst

+   signal

参数：

+   rp1 (10)

+   rp2 (15)

+   rp3 (20)

+   rp4 (30)

+   rma1 (10)

+   rma2 (10)

+   rma3 (10)

+   rma4 (10)

+   rsignal (9)

+   rfactors ([1.0, 2.0, 3.0, 4.0])

+   _rmovav (SMA)

+   _smovav (SMA)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([0.0])

+   plotforce (False)

PlotLines:

+   kst:

+   signal:

### LaguerreFilter

Alias:

+   LAGF

由约翰·F·埃勒斯（John F. Ehlers）在 2004 年的《股票与期货的控制分析》（Cybernetic Analysis for Stock and Futures）中定义，由 Wiley 出版。 ISBN：978-0-471-46307-8

`gamma` 应该在 `0.2` 和 `0.8` 之间，理论上在默认值 `0.5` 处找到最佳平衡

Lines:

+   lfilter

参数：

+   period (1)

+   gamma (0.5)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (False)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   lfilter:

### LaguerreRSI

Alias:

+   LRSI

由约翰·F·埃勒斯（John F. Ehlers）在 2004 年的《股票与期货的控制分析》（Cybernetic Analysis for Stock and Futures）中定义，由 Wiley 出版。 ISBN：978-0-471-46307-8

拉盖尔 RSI 试图通过使用拉盖尔滤波器提供一种更好的 RSI，从而提供了一种类似于*时间扭曲但没有时间旅行*的方法。 这可以更快地对价格变化做出反应

`gamma` 应该在 `0.2` 和 `0.8` 之间，理论上在默认值 `0.5` 处找到最佳平衡

Lines:

+   lrsi

Params:

+   period (6)

+   gamma (0.5)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.15)

+   plotyhlines ([])

+   plotyticks ([0.0, 0.2, 0.5, 0.8, 1.0])

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   lrsi：

### LinePlotterIndicator

PlotInfo：

+   plot（True）

+   plotmaster（无）

+   legendloc（无）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

### 最低

别名：

+   MinN

计算给定周期内数据的最低值

使用内置的`min`进行计算

Formula：

+   lowest = min（data，period）

Lines：

+   lowest

Params：

+   period（1）

PlotInfo：

+   plot（True）

+   plotmaster（无）

+   legendloc（无）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   lowest：

### MACD

移动平均收敛发散。由杰拉尔德·阿普尔在 70 年代定义。

它测量短期和长期移动平均线的距离，以试图识别趋势。

连续-发散收敛的第二个滞后移动平均值应在被 macd 越过时提供一个“信号”

Formula：

+   macd = ema（data，me1_period）- ema（data，me2_period）

+   signal = ema（macd，signal_period）

见：

+   [`en.wikipedia.org/wiki/MACD`](https://en.wikipedia.org/wiki/MACD)

Lines：

+   macd

+   signal

Params：

+   period_me1（12）

+   period_me2（26）

+   period_signal（9）

+   movav（指数移动平均）

PlotInfo：

+   plot（True）

+   plotmaster（无）

+   legendloc（无）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[0.0]）

+   plotforce（False）

PlotLines：

+   signal：

    +   ls（–）

+   macd：

### MACDHisto

别名：

+   MACDHistogram

MACD 的子类，添加了 macd 和信号线之间差异的“直方图”

Formula：

+   histo = macd - signal

见：

+   [`en.wikipedia.org/wiki/MACD`](https://en.wikipedia.org/wiki/MACD)

Lines：

+   macd

+   signal

+   histo

Params：

+   period_me1（12）

+   period_me2（26）

+   period_signal（9）

+   movav（指数移动平均）

PlotInfo：

+   plot（True）

+   plotmaster（无）

+   legendloc（无）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[0.0]）

+   plotforce（False）

PlotLines：

+   signal：

    +   ls（–）

+   macd：

+   histo：

    +   _method（bar）

    +   alpha（0.5）

    +   width（1.0）

### MeanDeviation

别名：

+   MeanDev

MeanDeviation（别名 MeanDev）

计算给定周期的传递数据的平均偏差

注意：

+   如果提供了 2 个数据作为参数，则第 2 个被视为第一个的平均值

Formula：

+   mean = MovingAverage（data，period）（或提供的平均值）

+   absdeviation = abs（data - mean）

+   meandev = MovingAverage（absdeviation，period）

见：

+   [`en.wikipedia.org/wiki/Average_absolute_deviation`](https://en.wikipedia.org/wiki/Average_absolute_deviation)

Lines：

+   meandev

Params：

+   周期（20）

+   movav（MovingAverageSimple）

PlotInfo：

+   plot（True）

+   plotmaster（无）

+   图例位置（无）

+   子图（True）

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线：

+   meandev：

### 负方向指标

别名：

+   MinusDI

由 J. Welles Wilder, Jr.在他的书*“技术交易系统的新概念”*中于 1978 年定义。

旨在衡量趋势强度

此指标显示-DI：

+   使用加正向指示器（PlusDI）获取+DI

+   使用定向指示器（DI）获取+DI，-DI

+   使用平均方向指数（ADX）获取 ADX

+   使用平均定向指数评级（ADXR）获取 ADX，ADXR

+   使用定向运动指数（DMI）获取 ADX，+DI，-DI

+   使用定向运动（DM）获取 ADX，ADXR，+DI，-DI

公式：

+   上涨 = 高 - 高（-1）

+   下跌 = 低（-1） - 低

+   -dm = 如果下跌大于上涨且下跌大于 0 则下跌，否则为 0

+   -di = 100 * 移动平均线（-dm，周期）/ atr（周期）

所使用的移动平均线是最初由 Wilder 定义的那个，平滑移动平均线

查看：

+   [平均定向运动指数](https://en.wikipedia.org/wiki/Average_directional_movement_index)

线条：

+   minusDI

参数：

+   周期（14）

+   movav（平滑移动平均线）

绘图信息：

+   绘图（True）

+   plotmaster（无）

+   图例位置（无）

+   子图（True）

+   绘图名称（-DirectionalIndicator）

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线：

+   plusDI：

    +   _name（+DI）

+   minusDI：

### 动量

通过计算当前价格与给定周期前价格之间的差异来衡量价格的变化

公式：

+   动量 = 数据 - 数据周期

查看：

+   [动量（技术分析）](https://en.wikipedia.org/wiki/Momentum_(technical_analysis))

线条：

+   动量

参数：

+   周期（12）

绘图信息：

+   绘图（True）

+   plotmaster（无）

+   图例位置（无）

+   子图（True）

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([0.0])

+   plotforce (False)

绘图线：

+   动量：

### 动量振荡器

别名：

+   动量振荡器

通过一段时间内价格变化的比率来衡量

公式：

+   mosc = 100 * (data / data_period)

查看：

+   [动量指标](http://ta.mql4.com/indicators/oscillators/momentum)

线条：

+   momosc

参数：

+   周期（12）

+   带宽（100.0）

绘图信息：

+   绘图（True）

+   plotmaster（无）

+   图例位置（无）

+   子图（True）

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线：

+   momosc：

### 移动平均基础

参数：

+   周期（30）

绘图信息：

+   绘图（True）

+   plotmaster（无）

+   图例位置（无）

+   子图（False）

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

### 移动平均简单

别名：

+   SMA，简单移动平均

过去 n 个周期的非加权平均值

公式：

+   movav = Sum(data, period) / period

参见：

+   [简单移动平均](https://zh.wikipedia.org/wiki/%E7%A7%BB%E5%8A%A8%E5%B9%B3%E5%9D%87#%E7%AE%80%E5%8D%95%E7%A7%BB%E5%8A%A8%E5%B9%B3%E5%9D%87)

Lines:

+   sma

参数：

+   period (30)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (False)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   sma:

### 移动平均简单信封

别名：

+   SMAEnvelope, SimpleMovingAverageEnvelope

简单移动平均和以“perc”分开的信封带

公式：

+   sma（来自 MovingAverageSimple）

+   top = sma * (1 + perc)

+   bot = sma * (1 - perc)

参见：

+   [移动平均线上的波动带](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes)

Lines:

+   sma

+   top

+   bot

参数：

+   period (30)

+   perc (2.5)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (False)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   sma:

+   top:

    +   _samecolor (True)

+   bot:

    +   _samecolor (True)

### 移动平均简单振荡器

别名：

+   MovingAverageSimpleOsc, SMAOscillator, SMAOsc, SimpleMovingAverageOscillator, SimpleMovingAverageOsc

移动平均简单振荡器围绕其数据的振荡

Lines:

+   sma

参数：

+   period (30)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   sma:

+   _0:

    +   _name (osc)

### 非零差异

别名：

+   NZD

跟踪两个数据输入之间的差异，如果当前差异为零，则记住最后一个非零值

公式：

+   diff = data - data1

+   nzd = diff if diff else diff(-1)

Lines:

+   nzd

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   nzd:

### OLS_BetaN

使用`pandas.ols`计算数据 1 对数据 0 的回归

使用`pandas`

Lines:

+   beta

参数：

+   period (10)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   beta:

### OLS_Slope_InterceptN

使用`statsmodel.OLS`（普通最小二乘法）对 data0 进行 data1 的线性回归计算

使用`pandas`和`statsmodels`

使用`prepend_constant`来影响`sm.add_constant`的参数`prepend`

Lines:

+   slope

+   intercept

Params:

+   period (10)

+   prepend_constant (True)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   slope:

+   intercept:

### OLS_TransformationN

计算 data0 和 data1 的`zscore`。虽然它不直接使用任何外部包，但它依赖于`OLS_SlopeInterceptN`，该包使用`pandas`和`statsmodels`

Lines:

+   spread

+   spread_mean

+   spread_std

+   zscore

Params:

+   period (10)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   spread:

+   spread_mean:

+   spread_std:

+   zscore:

### OperationN

计算给定周期的“func”

用于具有周期的类的基类，可以使用可调用对象表达逻辑

注意：

```py
Base classes must provide a “func” attribute which is a callable
```

Formula:

+   line = func(data, period)

Params:

+   period (1)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

### Oscillator

给定数据在另一个数据周围的振荡

Datas:

```py
This indicator can accept 1 or 2 datas for the calculation.
```

+   如果提供了 1 组数据，必须是一个复杂的“Lines”对象（指标），该对象还具有“datas”。示例：移动平均线

    计算的振荡将是移动平均线（示例中）围绕用于平均计算的数据的振荡

+   如果提供了 2 组数据，则计算的振荡将是第 2 组数据在第 1 组数据周围的振荡

Formula:

+   1 组数据 -> osc = data.data - data

+   2 组数据 -> osc = data0 - data1

Lines:

+   osc

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   _0:

    +   _name (osc)

+   osc:

### OscillatorMixIn

MixIn 类，用于与另一个指标创建一个子类。该指标的主要线将从另一个基类的主要线中减去，从而创建振荡器

使用方法是：

+   类 XXXOscillator(XXX, OscillatorMixIn)

Formula:

+   XXX 计算 lines[0]

+   osc = self.data - XXX.lines[0]

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线：

+   _0：

    +   _name（振荡器）

### 抛物线 SAR

别名：

+   PSAR

由 J. Welles Wilder, Jr. 在他的书 *“新技术交易系统中的新概念”* 中于 1978 年定义，用于 RSI

SAR 代表 *停止和转向*，该指标旨在作为进入（和转向）的信号

如何选择书中第 1 个信号并未指定，增加/减少条数

查看：

+   [抛物线 SAR](https://en.wikipedia.org/wiki/Parabolic_SAR)

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:parabolic_sar`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:parabolic_sar)

线条：

+   PSAR

参数：

+   期间（2）

+   af（0.02）

+   afmax (0.2)

图形信息：

+   plot (True)

+   plotmaster (None)

+   图例位置（无）

+   subplot (False)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线：

+   PSAR：

    +   标记（.）

    +   标记大小（4.0）

    +   颜色（黑色）

    +   填充样式（full）

    +   ls（）

### 百分比变化

别名：

+   百分比变化

衡量当前值相对于周期条之前的值的百分比变化

线条：

+   百分比变化

参数：

+   期间（30）

图形信息：

+   plot (True)

+   plotmaster (None)

+   图例位置（无）

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线：

+   百分比变化：

    +   _name（%变化）

### 百分比排名

别名：

+   百分比排名

表示当前值相对于周期条之前的值的百分位等级

线条：

+   百分比排名

参数：

+   期间（50）

+   func（<function percentrank.="">at 0x000001F1E4478B70>)</function>

图形信息：

+   plot (True)

+   plotmaster (None)

+   图例位置（无）

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线：

+   百分比排名：

### 百分价格振荡器

别名：

+   PPO，百分价格振荡器

显示短期和长期指数移动平均之间的差异，以百分比表示。MACD 也是如此，但以绝对点表示。

以百分比表示差异允许在基础值显着不同时比较不同时间点的指标。

公式：

+   po = 100 *（ema（short）- ema（long））/ ema（long）

查看：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:price_oscillators_ppo`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:price_oscillators_ppo)

线条：

+   ppo

+   信号

+   直方图

参数：

+   期间 1（12）

+   期间 2（26）

+   _movav（指数移动平均）

+   信号周期（9）

图形信息：

+   plot (True)

+   plotmaster (None)

+   图例位置（无）

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[0.0]）

+   plotforce（False）

PlotLines：

+   histo：

    +   _method（bar）

    +   alpha（0.5）

    +   width（1.0）

+   ppo：

+   signal：

### PercentagePriceOscillatorShort

别名：

+   PPOShort, PercPriceOscShort

显示以百分比表示的短期和长期指数移动平均值之间的差异。MACD 也是如此，但是以绝对点表示。

通过百分比表达差异允许在基础值明显不同时比较指标在不同时间点的情况。

大多数在线文献显示百分比计算将长期指数移动平均作为分母。一些像 MetaStock 这样的来源使用短期指数移动平均。

Formula：

+   po = 100 *（ema（short）- ema（long））/ ema（short）

查看：

+   [`www.metastock.com/Customer/Resources/TAAZ/?c=3&p=94`](http://www.metastock.com/Customer/Resources/TAAZ/?c=3&p=94)

Lines：

+   ppo

+   signal

+   histo

Params：

+   period1（12）

+   period2（26）

+   _movav（指数移动平均）

+   period_signal（9）

PlotInfo：

+   绘图（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[0.0]）

+   plotforce（False）

PlotLines：

+   histo：

    +   _method（bar）

    +   alpha（0.5）

    +   width（1.0）

+   ppo：

+   signal：

### PeriodN

指示器的基类，采用周期（**init** 必须通过 super 或显式调用）

此类没有定义的线

Params：

+   period（1）

PlotInfo：

+   绘图（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

### PivotPoint

通过考虑较大时间框架的过去时期的价格条组件的平均值来定义显着性水平。例如，当操作天数时，值是从已经“过去”的月固定价格中获得的。

使用此指标的示例：

data = btfeeds.ADataFeed(dataname=x, timeframe=bt.TimeFrame.Days) cerebro.adddata(data) cerebro.resampledata(data, timeframe=bt.TimeFrame.Months)

在策略的 `__init__` 方法中：

pivotindicator = btind.PivotPoiont(self.data1) # 重新采样数据

指示器将尝试自动绘制非重新采样数据。要禁用此行为，请在构造过程中使用以下内容：

+   _autoplot=False

注意：

此示例显示 *天* 和 *月*，但任何时间框架的组合都可以使用。请参考文献以获取推荐的组合。

Formula：

+   pivot =（h + l + c）/ 3 # 变体重复关闭或添加开放

+   support1 = 2.0 * pivot - high

+   support2 = pivot -（high - low）

+   resistance1 = 2.0 * pivot - low

+   resistance2 = pivot +（high - low）

查看：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:pivot_points`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:pivot_points)

+   [`en.wikipedia.org/wiki/Pivot_point_(technical_analysis)`](https://en.wikipedia.org/wiki/Pivot_point_(technical_analysis))

Lines：

+   p

+   s1

+   s2

+   r1

+   r2

参数：

+   open (False)

+   close (False)

+   _autoplot（True）

PlotInfo：

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (False)

+   plotname（）

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines ([])

+   plotforce (False)

PlotLines：

+   p：

+   s1：

+   s2：

+   r1：

+   r2：

### PlusDirectionalIndicator

别名：

+   PlusDI

J.韦尔斯·怀尔德（J. Welles Wilder，Jr.）在 1978 年的书 *“技术交易系统中的新概念”* 中定义。

旨在衡量趋势强度

此指标显示 +DI：

+   使用 MinusDirectionalIndicator（MinusDI）获取 -DI

+   使用方向指标（DI）获取 +DI、-DI

+   使用 AverageDirectionalIndex（ADX）获取 ADX

+   使用 AverageDirectionalIndexRating（ADXR）获取 ADX、ADXR

+   使用 DirectionalMovementIndex（DMI）获取 ADX、+DI、-DI

+   使用 DirectionalMovement (DM) 来获取 ADX、ADXR、+DI、-DI

公式：

+   upmove = high - high(-1)

+   downmove = low(-1) - low

+   +dm = 如果 upmove > downmove 且 upmove > 0 则 upmove else 0

+   +di = 100 * MovingAverage（+dm，period）/ atr（period）

采用最初由怀尔德定义的移动平均线，即 SmoothedMovingAverage

参见：

+   [`en.wikipedia.org/wiki/Average_directional_movement_index`](https://en.wikipedia.org/wiki/Average_directional_movement_index)

Lines：

+   plusDI

参数：

+   期间（14）

+   movav（SmoothedMovingAverage）

PlotInfo：

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname（+DirectionalIndicator）

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines：

+   plusDI：

+   minusDI：

    +   _name（-DI）

### PrettyGoodOscillator

别名：

+   PGO，PrettyGoodOsc

“相当不错的振荡器”（PGO）由马克·约翰逊（Mark Johnson）设计，以期平均真实范围（见平均真实范围）在类似时期内的简单移动平均值为基准，表示为当前收盘价与其简单移动平均值之间的距离。

因此，例如 PGO 值为 +2.5 意味着当前收盘价比 SMA 高出 2.5 个平均日范围。

约翰逊的方法是将其用作较长期交易的突破系统。如果 PGO 上升到 3.0 以上，则做多；或者低于 -3.0，则做空，并且在两种情况下在返回零时（即 SMA 收盘）退出。

公式：

+   pgo =（data.close - sma（data，period））/ atr（data，period）

另请参阅：

+   [`user42.tuxfamily.org/chart/manual/Pretty-Good-Oscillator.html`](http://user42.tuxfamily.org/chart/manual/Pretty-Good-Oscillator.html)

Lines：

+   pgo

参数：

+   period（14）

+   _movav（MovingAverageSimple）

PlotInfo：

+   plot (True)

+   plotmaster（None）

+   legendloc (None)

+   subplot (True)

+   plotname（）

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks（[]）

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   pgo：

### PriceOscillator

别名：

+   PriceOsc，AbsolutePriceOscillator，APO，AbsPriceOsc

显示以点表示的短期和长期指数移动平均线之间的差异。

Formula：

+   po = ema（short）- ema（long）

查看：

+   [`www.metastock.com/Customer/Resources/TAAZ/?c=3&p=94`](http://www.metastock.com/Customer/Resources/TAAZ/?c=3&p=94)

Lines：

+   po

Params：

+   period1（12）

+   period2（26）

+   _movav（ExponentialMovingAverage）

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[0.0]）

+   plotforce（False）

PlotLines：

+   po：

### RSI_EMA

使用维基百科描述的指数移动平均线

查看：

+   [`en.wikipedia.org/wiki/Relative_strength_index`](https://en.wikipedia.org/wiki/Relative_strength_index)

Lines：

+   rsi

Params：

+   期间（14）

+   movav（ExponentialMovingAverage）

+   upperband（70.0）

+   lowerband（30.0）

+   safediv（False）

+   safehigh（100.0）

+   safelow（50.0）

+   lookback（1）

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   rsi：

### RSI_SMA

别名：

+   RSI_Cutler

使用维基百科和其他来源描述的简单移动平均线

查看：

+   [`en.wikipedia.org/wiki/Relative_strength_index`](https://en.wikipedia.org/wiki/Relative_strength_index)

Lines：

+   rsi

Params：

+   期间（14）

+   movav（MovingAverageSimple）

+   upperband（70.0）

+   lowerband（30.0）

+   safediv（False）

+   safehigh（100.0）

+   safelow（50.0）

+   lookback（1）

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   rsi：

### RSI_Safe

RSI 的子类，将参数`safediv`更改为默认值`True`

查看：

+   [`en.wikipedia.org/wiki/Relative_strength_index`](https://en.wikipedia.org/wiki/Relative_strength_index)

Lines：

+   rsi

Params：

+   期间（14）

+   movav（SmoothedMovingAverage）

+   upperband（70.0）

+   lowerband（30.0）

+   safediv（True）

+   safehigh（100.0）

+   safelow（50.0）

+   lookback（1）

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   rsi：

### RateOfChange

别名：

+   ROC

测量一段时间内价格变化的比率

Formula：

+   roc =（data-data_period）/ data_period

查看：

+   [`en.wikipedia.org/wiki/Momentum_(technical_analysis`](https://en.wikipedia.org/wiki/Momentum_(technical_analysis))

Lines：

+   roc

Params：

+   期间（12）

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线：

+   roc：

### RateOfChange100

别名：

+   ROC100

测量一段时间内价格变化的比率，基准为 100

这是股票图表中 ROC 的定义示例

公式：

+   roc = 100 * (data - data_period) / data_period

参见：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:rate_of_change_roc_and_momentum`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:rate_of_change_roc_and_momentum)

线：

+   roc100

参数：

+   period (12)

绘图信息：

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线：

+   roc100:

### ReduceN

计算`period`数据点的`function`应用的减少值

使用内置的`reduce`进行计算，以及子类定义的`func`

公式：

+   reduced = reduce(function(data, period)), initializer=initializer)

注意：

+   为了模仿 python 的`reduce`，此指标将`function`作为第 1 个非命名参数，而不像其他指标只接受命名参数

线：

+   减少

参数：

+   period (1)

绘图信息：

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线：

+   减少：

### 相对动量指数

别名：

+   RMI

描述：相对动量指数由罗杰·奥尔特曼开发，并在 1993 年 2 月《股票与大宗商品技术分析》杂志上发表的文章中介绍。

你典型的 RSI 从收盘到收盘计算上涨和下跌天数，而相对动量指数从收盘相对于 x 天前的收盘计算上涨和下跌天数。结果是一个稍微平滑的 RSI。

用法：与其他 RSI 一样使用。有超买和超卖区域，也可用于分歧和趋势分析。

参见：

+   [`www.marketvolume.com/technicalanalysis/relativemomentumindex.asp`](https://www.marketvolume.com/technicalanalysis/relativemomentumindex.asp)

+   [`www.tradingview.com/script/UCm7fIvk-FREE-INDICATOR-Relative-Momentum-Index-RMI/`](https://www.tradingview.com/script/UCm7fIvk-FREE-INDICATOR-Relative-Momentum-Index-RMI/)

+   [`www.prorealcode.com/prorealtime-indicators/relative-momentum-index-rmi/`](https://www.prorealcode.com/prorealtime-indicators/relative-momentum-index-rmi/)

线：

+   rsi

参数：

+   period (20)

+   movav (SmoothedMovingAverage)

+   upperband (70.0)

+   lowerband (30.0)

+   safediv (False)

+   safehigh (100.0)

+   safelow (50.0)

+   lookback (5)

绘图信息：

+   plot (True)

+   plotmaster (None)

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   rsi：

    +   _name（rmi）

### 相对强弱指数

别名：

+   RSI，RSI_SMMA，RSI_Wilder

由 J.威尔斯·怀尔德（J. Welles Wilder, Jr.）在 1978 年的书籍*“技术交易系统中的新概念”*中定义。

它通过计算经过平均值平滑后的更高收盘价和更低收盘价的比率来衡量动量，将结果归一化在 0 到 100 之间

公式：

+   up = upday（data）

+   down = downday（data）

+   maup = movingaverage（up，period）

+   madown = movingaverage（down，period）

+   rs = maup / madown

+   rsi = 100 - 100 /（1 + rs）

使用的移动平均线是最初由 Wilder 定义的 SmoothedMovingAverage

参见：

+   [相对强弱指数](https://en.wikipedia.org/wiki/Relative_strength_index)

注意：

+   `safediv`（默认值：False）如果此参数为 True，则将检查 rs = maup / madown 的除法，以防发生`0 / 0`或`x / 0`除法的特殊情况

+   `safehigh`（默认值：100.0）将用作`x / 0`情况下的 RSI 值

+   `safelow`（默认值：50.0）将用作`0 / 0`情况下的 RSI 值

线：

+   rsi

参数：

+   周期（14）

+   movav（SmoothedMovingAverage）

+   上限值（70.0）

+   lowerband（30.0）

+   safediv（False）

+   safehigh（100.0）

+   safelow（50.0）

+   lookback（1）

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   rsi：

### 信号

线：

+   信号

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   信号：

### SmoothedMovingAverage

别名：

+   SMMA，WilderMA，MovingAverageSmoothed，MovingAverageWilder，ModifiedMovingAverage

Wilder 在他的 1978 年著作《技术交易中的新概念》中使用的平滑移动平均线

最初在他的书中定义为：

+   new_value =（old_value *（period - 1）+ new_data）/ period

可以表示为具有以下因子的平滑移动平均：

+   self.smfactor -> 1.0 / period

+   self.smfactor1 -> 1.0 - self.smfactor

公式：

+   movav = prev *（1.0 - smoothfactor）+ newdata * smoothfactor

另请参阅：

+   [移动平均线#修改移动平均线](https://en.wikipedia.org/wiki/Moving_average#Modified_moving_average)

线：

+   smma

参数：

+   周期（30）

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（False）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   smma：

### SmoothedMovingAverageEnvelope

Alias：

+   SMMAEnvelope, WilderMAEnvelope, MovingAverageSmoothedEnvelope, MovingAverageWilderEnvelope, ModifiedMovingAverageEnvelope

SmoothedMovingAverage 和包络带从中分离了“perc”

Formula：

+   smma（来自 SmoothedMovingAverage）

+   top = smma * (1 + perc)

+   bot = smma * (1 - perc)

另请参阅：

+   [移动平均线包络](https://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes)

Lines：

+   smma

+   top

+   bot

Params：

+   period（30）

+   perc（2.5）

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（False）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   smma：

+   top：

    +   _samecolor（True）

+   bot：

    +   _samecolor（True）

### SmoothedMovingAverageOscillator

Alias：

+   SmoothedMovingAverageOsc, SMMAOscillator, SMMAOsc, WilderMAOscillator, WilderMAOsc, MovingAverageSmoothedOscillator, MovingAverageSmoothedOsc, MovingAverageWilderOscillator, MovingAverageWilderOsc, ModifiedMovingAverageOscillator, ModifiedMovingAverageOsc

SmoothedMovingAverage 在其数据周围的振荡

Lines：

+   smma

Params：

+   period（30）

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   smma：

+   _0：

    +   _name（osc）

### StandardDeviation

Alias：

+   StdDev

计算给定周期的传递数据的标准差

注意：

+   如果作为参数提供了 2 个数据，则第 2 个被视为第一个的平均值

+   `safepow`（默认：False）如果此参数为 True，则标准差将被计算为 pow(abs(meansq - sqmean), 0.5)，以防止由浮点表示引起的可能的`meansq - sqmean`负结果。

Formula：

+   meansquared = SimpleMovingAverage(pow(data, 2), period)

+   squaredmean = pow(SimpleMovingAverage(data, period), 2)

+   stddev = pow(meansquared - squaredmean, 0.5) # 开平方根

参见：

+   [标准差](https://zh.wikipedia.org/wiki/%E6%A8%99%E6%BA%96%E5%B7%AE)：

Lines：

+   stddev

Params：

+   期间（20）

+   movav（MovingAverageSimple）

+   safepow（True）

PlotInfo：

+   plot（True）

+   plotmaster（None）

+   legendloc（None）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   stddev：

### Stochastic

Alias：

+   StochasticSlow

常规（或慢速版本）添加了额外的移动平均层，因此：

+   StochasticFast 的 percD 线成为 percK 线

+   percD 变为原 percD 的 period_dslow 的移动平均线

Formula：

+   k = k

+   d = d

+   d = MovingAverage(d, period_dslow)

见：

+   [`zh.wikipedia.org/wiki/随机震荡指标`](https://en.wikipedia.org/wiki/Stochastic_oscillator)

绘制线条：

+   percK

+   percD

参数：

+   period (14)

+   period_dfast (3)

+   movav (简单移动平均)

+   upperband (80.0)

+   lowerband (20.0)

+   safediv (False)

+   safezero (0.0)

+   period_dslow (3)

绘制信息：

+   plot (是)

+   plotmaster (无)

+   legendloc (无)

+   subplot (是)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (是)

+   plotvaluetags (是)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘制线条：

+   percD：

    +   _name (%D)

    +   ls (–)

+   percK：

    +   _name (%K)

### 快速随机

由 50 年代的乔治·莱恩博士提出。它将收盘价格与价格范围进行比较，并试图显示如果收盘价格接近极端值，则趋同性。

+   如果收盘价格接近最高价，则指数将上升

+   如果收盘价格接近最低价，则指数大致会下降

如果极端值继续增长但收盘价格不是以同样的方式增长（与极端值的距离增加），则显示发散。

公式：

+   hh = highest(data.high, period)

+   ll = lowest(data.low, period)

+   knum = data.close - ll

+   kden = hh - ll

+   k = 100 * (knum / kden)

+   d = 移动平均(k, period_dfast)

见：

+   [`zh.wikipedia.org/wiki/随机震荡指标`](https://en.wikipedia.org/wiki/Stochastic_oscillator)

绘制线条：

+   percK

+   percD

参数：

+   period (14)

+   period_dfast (3)

+   movav (简单移动平均)

+   upperband (80.0)

+   lowerband (20.0)

+   safediv (False)

+   safezero (0.0)

绘制信息：

+   plot (是)

+   plotmaster (无)

+   legendloc (无)

+   subplot (是)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (是)

+   plotvaluetags (是)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘制线条：

+   percD：

    +   _name (%D)

    +   ls (–)

+   percK：

    +   _name (%K)

### 完整随机

此版本显示 3 条可能的线：

+   percK

+   percD

+   percSlow

公式：

+   k = d

+   d = 移动平均(k, period_dslow)

+   dslow =

见：

+   [`zh.wikipedia.org/wiki/随机震荡指标`](https://en.wikipedia.org/wiki/Stochastic_oscillator)

绘制线条：

+   percK

+   percD

+   percDSlow

参数：

+   period (14)

+   period_dfast (3)

+   movav (简单移动平均)

+   upperband (80.0)

+   lowerband (20.0)

+   safediv (False)

+   safezero (0.0)

+   period_dslow (3)

绘制信息：

+   plot (是)

+   plotmaster (无)

+   legendloc (无)

+   subplot (是)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (是)

+   plotvaluetags (是)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘制线条：

+   percD：

    +   _name (%D)

    +   ls (–)

+   percK：

    +   _name (%K)

+   percDSlow：

    +   _name (%DSlow)

### SumN

计算给定周期内数据值的总和

使用 `math.fsum` 进行计算，而不是内置的 `sum`，以避免精度错误

公式：

+   sumn = sum(data, period)

绘制线条：

+   sumn

参数：

+   period (1)

绘制信息：

+   plot (是)

+   plotmaster (无)

+   legendloc (无)

+   subplot (是)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (是)

+   plotvaluetags (是)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   sumn：

### TripleExponentialMovingAverage

别名：

+   TEMA，MovingAverageTripleExponential

TEMA 首次于 1994 年在“股票与商品技术分析”杂志上的文章“使用更快的移动平均值平滑数据”中被 Patrick G. Mulloy 介绍。

它试图减少与移动平均线相关的固有滞后

公式：

+   ema1 = ema(data, period)

+   ema2 = ema（ema1，period）

+   ema3 = ema（ema2，period）

+   tema = 3 * ema1 - 3 * ema2 + ema3

参见：

```py
(None)
```

线条：

+   tema

参数：

+   period（30）

+   _movav（EMA）

PlotInfo：

+   plot（True）

+   plotmaster（无）

+   legendloc（无）

+   subplot（False）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   tema：

### TripleExponentialMovingAverageEnvelope

别名：

+   TEMAEnvelope，MovingAverageTripleExponentialEnvelope

三重指数移动平均和带分离的“perc”

公式：

+   tema（来自 TripleExponentialMovingAverage）

+   top = tema *（1 + perc）

+   bot = tema *（1- perc）

另请参阅：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes)

线条：

+   tema

+   top

+   bot

参数：

+   period（30）

+   _movav（EMA）

+   perc（2.5）

PlotInfo：

+   plot（True）

+   plotmaster（无）

+   legendloc（无）

+   subplot（False）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   tema：

+   top：

    +   _samecolor（True）

+   bot：

    +   _samecolor（True）

### TripleExponentialMovingAverageOscillator

别名：

+   TripleExponentialMovingAverageOsc，TEMAOscillator，TEMAOsc，MovingAverageTripleExponentialOscillator，MovingAverageTripleExponentialOsc

三重指数移动平均在其数据周围的振荡

线条：

+   tema

参数：

+   period（30）

+   _movav（EMA）

PlotInfo：

+   plot（True）

+   plotmaster（无）

+   legendloc（无）

+   subplot（True）

+   plotname（）

+   plotskip（False）

+   plotabove（False）

+   plotlinelabels（False）

+   plotlinevalues（True）

+   plotvaluetags（True）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（False）

PlotLines：

+   tema：

+   _0：

    +   _name（osc）

### Trix

别名：

+   TRIX

Jack Hutson 在 80 年代首次定义，并显示三重指数平滑移动平均线的变化率（%）或斜率

公式：

+   ema1 = EMA（data，period）

+   ema2 = EMA（ema1，period）

+   ema3 = EMA（ema2，period）

+   trix = 100 *（ema3 - ema3（-1））/ ema3（-1）

    最终公式可以简化为：100 *（ema3 / ema3（-1）- 1）

使用的移动平均线是最初由 Wilder 定义的 SmoothedMovingAverage

参见：

+   [`en.wikipedia.org/wiki/Trix_(technical_analysis`](https://en.wikipedia.org/wiki/Trix_(technical_analysis))

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:trix`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:trix)

Lines:

+   trix

Params:

+   period (15)

+   _rocperiod (1)

+   _movav (EMA)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([0.0])

+   plotforce (False)

PlotLines:

+   trix:

### TrixSignal

Trix 的扩展，带有信号线（类似 MACD）

Formula:

+   trix = Trix(data, period)

+   signal = EMA(trix, sigperiod)

See:

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:trix`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:trix)

Lines:

+   trix

+   signal

Params:

+   period (15)

+   _rocperiod (1)

+   _movav (EMA)

+   sigperiod (9)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([0.0])

+   plotforce (False)

PlotLines:

+   trix:

+   signal:

### TrueHigh

由 J·威尔斯·怀尔德（J. Welles Wilder, Jr.）于 1978 年在他的著作*“技术交易系统中的新概念”*中定义的

记录“真实高点”，即当天最高价和昨日收盘价的较大值

Formula:

+   truehigh = max(high, close_prev)

See:

+   [`en.wikipedia.org/wiki/Average_true_range`](https://en.wikipedia.org/wiki/Average_true_range)

Lines:

+   truehigh

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   truehigh:

### TrueLow

由 J·威尔斯·怀尔德（J. Welles Wilder, Jr.）于 1978 年在他的著作*“技术交易系统中的新概念”*中为 ATR 定义

记录“真实低点”，即当天最低价和昨日收盘价的较小值

Formula:

+   truelow = min(low, close_prev)

See:

+   [`en.wikipedia.org/wiki/Average_true_range`](https://en.wikipedia.org/wiki/Average_true_range)

Lines:

+   truelow

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   truelow:

### TrueRange

Alias:

+   TR

由 J·威尔斯·怀尔德（J. Welles Wilder, Jr.）于 1978 年在他的著作新概念中的技术交易系统中定义

Formula:

+   max(high - low, abs(high - prev_close), abs(prev_close - low)

    可简化为

+   max(high, prev_close) - min(low, prev_close)

See:

+   [`en.wikipedia.org/wiki/Average_true_range`](https://en.wikipedia.org/wiki/Average_true_range)

思路是考虑昨日收盘价以计算范围，如果它产生的范围比日间范围（高价 - 低价）大，则采用昨日收盘价

线条：

+   tr

绘图信息：

+   绘制（是）

+   plotmaster（无）

+   legendloc（无）

+   subplot（是）

+   plotname（）

+   plotskip（否）

+   plotabove（否）

+   plotlinelabels（否）

+   plotlinevalues（是）

+   plotvaluetags（是）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（否）

绘图线条：

+   tr：

### 真实强度指标

别名：

+   TSI

真实强度指标最初由其作者 William Blau 在《股票与商品》杂志上介绍。 它用双指数（默认）的价格来衡量动量。

如果极端值持续增长但收盘价没有以相同的方式增长（与极端值的距离增长），则显示发散

公式：

+   价格变动=收盘价-收盘价（向前 pchange 个周期）

+   sm1_simple = EMA（price_close_change，period1）

+   sm1_double = EMA（sm1_simple，period2）

+   sm2_simple = EMA（abs（price_close_change），period1）

+   sm2_double = EMA（sm2_simple，period2）

+   tsi = 100.0 * sm1_double / sm2_double

参见：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:true_strength_index`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:true_strength_index)

参数

+   `period1`：第 1 个平滑的周期

+   `period2`：第 2 个平滑的周期

+   `pchange`：价格变动的回溯期

+   `_movav`：应用于平滑的移动平均线

线条：

+   tsi

参数：

+   period1（25）

+   period2（13）

+   pchange（1）

+   _movav（EMA）

绘图信息：

+   绘制（是）

+   plotmaster（无）

+   legendloc（无）

+   subplot（是）

+   plotname（）

+   plotskip（否）

+   plotabove（否）

+   plotlinelabels（否）

+   plotlinevalues（是）

+   plotvaluetags（是）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（否）

绘图线条：

+   tsi：

### 终极振荡器

公式：

```py
# Buying Pressure = Close - TrueLow
BP = Close - Minimum(Low or Prior Close)

# TrueRange = TrueHigh - TrueLow
TR = Maximum(High or Prior Close)  -  Minimum(Low or Prior Close)

Average7 = (7-period BP Sum) / (7-period TR Sum)
Average14 = (14-period BP Sum) / (14-period TR Sum)
Average28 = (28-period BP Sum) / (28-period TR Sum)

UO = 100 x [(4 x Average7)+(2 x Average14)+Average28]/(4+2+1)
```

参见：

+   [`en.wikipedia.org/wiki/Ultimate_oscillator`](https://en.wikipedia.org/wiki/Ultimate_oscillator)

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:ultimate_oscillator`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:ultimate_oscillator)

线条：

+   uo

参数：

+   p1（7）

+   p2（14）

+   p3（28）

+   upperband（70.0）

+   lowerband（30.0）

绘图信息：

+   绘制（是）

+   plotmaster（无）

+   legendloc（无）

+   subplot（是）

+   plotname（）

+   plotskip（否）

+   plotabove（否）

+   plotlinelabels（否）

+   plotlinevalues（是）

+   plotvaluetags（是）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   plotyticks（[]）

+   plothlines（[]）

+   plotforce（否）

绘图线条：

+   uo：

### 上涨日

由 J. Welles Wilder Jr.在他的书《“技术交易系统中的新概念”》中为 RSI 首次引入

记录“上涨”天数，即收盘价高于前一天。

公式：

+   upday = max（close - close_prev，0）

参见：

+   [`en.wikipedia.org/wiki/Relative_strength_index`](https://en.wikipedia.org/wiki/Relative_strength_index)

线条：

+   上涨日

参数：

+   期间（1）

绘图信息：

+   绘制（是）

+   plotmaster（无）

+   legendloc（无）

+   subplot（是）

+   plotname（）

+   plotskip（否）

+   plotabove（否）

+   plotlinelabels（否）

+   plotlinevalues（是）

+   plotvaluetags（是）

+   plotymargin（0.0）

+   plotyhlines（[]）

+   绘图 y 刻度 ([])

+   绘制水平线 ([])

+   plotforce (假)

绘图线条：

+   上升日：

### 上升日布尔值

由 J.韦尔斯·怀尔德（J. Welles Wilder, Jr.）在其 1978 年的著作*“技术交易系统中的新概念”*中为 RSI 定义

记录“上涨”的天数，即：收盘价高于前一天。

注意：

+   此版本返回布尔值而不是差异

公式：

+   上升日 = close > close_prev

参见：

+   [`en.wikipedia.org/wiki/Relative_strength_index`](https://en.wikipedia.org/wiki/Relative_strength_index)

线条：

+   上升日

参数：

+   周期 (1)

绘图信息：

+   绘图 (是)

+   plotmaster (无)

+   图例位置 (无)

+   子图 (是)

+   绘图名称 ()

+   跳过绘制 (假)

+   plotabove (假)

+   绘制线条标签 (假)

+   绘制线条值 (是)

+   plotvaluetags (是)

+   绘图 y 边距 (0.0)

+   plotyhlines ([])

+   绘图 y 刻度 ([])

+   绘制水平线 ([])

+   plotforce (假)

绘图线条：

+   上升日：

### 上升幅度

由 J.韦尔斯·怀尔德（J. Welles Wilder, Jr.）在其 1978 年的著作*“技术交易系统中的新概念”*中定义，作为方向运动系统的一部分来计算方向指标。

如果给定数据比前一天高，则为正

公式：

+   上升幅度 = data - data(-1)

参见：

+   [`en.wikipedia.org/wiki/Average_directional_movement_index`](https://en.wikipedia.org/wiki/Average_directional_movement_index)

线条：

+   上升幅度

绘图信息：

+   绘图 (是)

+   plotmaster (无)

+   图例位置 (无)

+   子图 (是)

+   绘图名称 ()

+   跳过绘制 (假)

+   plotabove (假)

+   绘制线条标签 (假)

+   绘制线条值 (是)

+   plotvaluetags (是)

+   绘图 y 边距 (0.0)

+   plotyhlines ([])

+   绘图 y 刻度 ([])

+   绘制水平线 ([])

+   plotforce (假)

绘图线条：

+   上升幅度：

### 涡流

参见：

+   [`www.vortexindicator.com/VFX_VORTEX.PDF`](http://www.vortexindicator.com/VFX_VORTEX.PDF)

线条：

+   vi_plus

+   vi_minus

参数：

+   周期 (14)

绘图信息：

+   绘图 (是)

+   plotmaster (无)

+   图例位置 (无)

+   子图 (是)

+   绘图名称 ()

+   跳过绘制 (假)

+   plotabove (假)

+   绘制线条标签 (假)

+   绘制线条值 (是)

+   plotvaluetags (是)

+   绘图 y 边距 (0.0)

+   plotyhlines ([])

+   绘图 y 刻度 ([])

+   绘制水平线 ([])

+   plotforce (假)

绘图线条：

+   vi_plus：

    +   _ 名称 (+VI)

+   vi_minus：

    +   _ 名称 (-VI)

### 加权平均

别名：

+   加权平均

计算给定数据在一段时间内的加权平均

默认权重（如果没有提供）是线性的，以分配更多的权重给最近的数据

结果将乘以给定的“coef”

公式：

+   av = coef * sum(mul(data, period), weights)

参见：

+   [`en.wikipedia.org/wiki/Weighted_arithmetic_mean`](https://en.wikipedia.org/wiki/Weighted_arithmetic_mean)

线条：

+   av

参数：

+   周期 (1)

+   系数 (1.0)

+   权重 (())

绘图信息：

+   绘图 (是)

+   plotmaster (无)

+   图例位置 (无)

+   子图 (是)

+   绘图名称 ()

+   跳过绘制 (假)

+   plotabove (假)

+   绘制线条标签 (假)

+   绘制线条值 (是)

+   plotvaluetags (是)

+   绘图 y 边距 (0.0)

+   plotyhlines ([])

+   绘图 y 刻度 ([])

+   绘制水平线 ([])

+   plotforce (假)

绘图线条：

+   av：

### 加权移动平均

别名：

+   WMA, 加权移动平均

一种对数值进行算术加权的移动平均，最新值具有更大的权重

公式：

+   weights = range(1, period + 1)

+   coef = 2 /（period *（period + 1））

+   movav = coef * Sum（weight[i] * data[period - i] for i in range（period））

参见：

+   [`en.wikipedia.org/wiki/Moving_average#Weighted_moving_average`](https://en.wikipedia.org/wiki/Moving_average#Weighted_moving_average)

线条：

+   wma

参数：

+   周期（30）

绘图信息：

+   plot（True）

+   plotmaster（None）

+   legendloc (None)

+   subplot (False)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线条：

+   wma：

### 加权移动平均线包络

别名：

+   WMAEnvelope，MovingAverageWeightedEnvelope

加权移动平均线和包络线从中分离出了“perc”

公式：

+   wma（来自加权移动平均线）

+   顶部= wma *（1 + perc）

+   底部= wma *（1 - perc）

参见：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes)

线条：

+   wma

+   顶部

+   底部

参数：

+   周期（30）

+   perc（2.5）

绘图信息：

+   plot（True）

+   plotmaster（None）

+   legendloc (None)

+   subplot (False)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线条：

+   wma：

+   顶部：

    +   _samecolor (True)

+   底部：

    +   _samecolor (True)

### 加权移动平均线振荡器

别名：

+   加权移动平均线振荡器，WMAOscillator，WMAOsc，MovingAverageWeightedOscillator，MovingAverageWeightedOsc

加权移动平均线的振荡

线条：

+   wma

参数：

+   周期（30）

绘图信息：

+   plot（True）

+   plotmaster（None）

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线条：

+   wma：

+   _0：

    +   _name (osc)

### WilliamsAD

由拉里·威廉姆斯（Larry Williams）提供。它通过使用 UpDays 和 DownDays 的概念，累积地测量价格是在积累（上涨）还是分配（下跌）。

价格可以向上走，但以不再显示积累的方式，因为上涨天数和下跌天数正在互相抵消，产生了分歧。

参见：- [`www.metastock.com/Customer/Resources/TAAZ/?p=125`](http://www.metastock.com/Customer/Resources/TAAZ/?p=125) - [`ta.mql4.com/indicators/trends/williams_accumulation_distribution`](http://ta.mql4.com/indicators/trends/williams_accumulation_distribution)

线条：

+   ad

绘图信息：

+   plot（True）

+   plotmaster（None）

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

绘图线条：

+   ad：

### 威廉斯 R

由拉里·威廉姆斯（Larry Williams）开发，用于显示收盘价格与给定期间的最高-最低范围的关系。

被称为**威廉斯%R**（但在 Python 标识符中不允许使用%）

Formula：

+   num = highest_period - close

+   den = highestg_period - lowest_period

+   percR = (num / den) * -100.0

参见：

+   [`en.wikipedia.org/wiki/Williams_%25R`](https://en.wikipedia.org/wiki/Williams_%25R)

Lines：

+   percR

Params：

+   period (14)

+   upperband (-20.0)

+   lowerband (-80.0)

PlotInfo：

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname (Williams R%)

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines：

+   percR：

    +   _name (R%)

### 零滞后指数移动平均线

Alias：

+   ZLEMA, ZeroLagEma

零滞后指数移动平均线（ZLEMA）是 EMA 的变体，它增加了一个动量项，旨在减少平均值中的滞后，以更紧密地跟踪当前价格。

Formula：

+   lag = (period - 1) / 2

+   zlema = ema(2 * data - data(-lag))

另请参阅：

+   [`user42.tuxfamily.org/chart/manual/Zero_002dLag-Exponential-Moving-Average.html`](http://user42.tuxfamily.org/chart/manual/Zero_002dLag-Exponential-Moving-Average.html)

Lines：

+   zlema

Params：

+   period (30)

+   _movav (EMA)

PlotInfo：

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (False)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines：

+   zlema：

### ZeroLagExponentialMovingAverageEnvelope

Alias：

+   ZLEMAEnvelope, ZeroLagEmaEnvelope

零滞后指数移动平均线和“perc”分隔的包络线带

Formula：

+   zlema（来自 ZeroLagExponentialMovingAverage）

+   top = zlema * (1 + perc)

+   bot = zlema * (1 - perc)

另请参阅：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes)

Lines：

+   zlema

+   上

+   bot

Params：

+   period (30)

+   _movav (EMA)

+   perc (2.5)

PlotInfo：

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (False)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines：

+   zlema：

+   top：

    +   _samecolor (True)

+   bot：

    +   _samecolor (True)

### ZeroLagExponentialMovingAverageOscillator

Alias：

+   ZeroLagExponentialMovingAverageOsc, ZLEMAOscillator, ZLEMAOsc, ZeroLagEmaOscillator, ZeroLagEmaOsc

零滞后指数移动平均线在其数据周围的振荡

Lines：

+   zlema

Params：

+   period (30)

+   _movav (EMA)

PlotInfo：

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines：

+   zlema：

+   _0：

    +   _name (osc)

### ZeroLagIndicator

Alias：

+   ZLIndicator, ZLInd, EC, ErrorCorrecting

由约翰·埃勒斯和里克·韦伊撰写

零滞后指标（ZLIndicator）是 EMA 的一种变体，通过尝试最小化误差（距离价格 - 误差校正）来修改 EMA，从而减少滞后

公式：

+   EMA(data, period)

+   对于每次迭代，计算 EMA 的最佳误差校正（参见论文和/或代码），迭代范围为 `-bestgain` -> `+bestgain` 用于误差校正因子（两者都包括在内）

+   默认移动平均值是 EMA，但可以通过参数 `_movav` 进行更改

    **注意**：传递的移动平均值必须计算 alpha（和 1 - alpha），并在实例中提供这些属性 `alpha` 和 `alpha1`

参见：

+   [`www.mesasoftware.com/papers/ZeroLag.pdf`](http://www.mesasoftware.com/papers/ZeroLag.pdf)

Lines:

+   ec

Params:

+   period (30)

+   gainlimit (50)

+   _movav (EMA)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (False)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   ec:

### ZeroLagIndicatorEnvelope

Alias:

+   ZLIndicatorEnvelope, ZLIndEnvelope, ECEnvelope, ErrorCorrectingEnvelope

ZeroLagIndicator 和包络带从中间“perc”分开

公式：

+   ec（来自 ZeroLagIndicator）

+   top = ec * (1 + perc)

+   bot = ec * (1 - perc)

参见：

+   [`stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes`](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:moving_average_envelopes)

Lines:

+   ec

+   top

+   bot

Params:

+   period (30)

+   gainlimit (50)

+   _movav (EMA)

+   perc (2.5)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (False)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   ec:

+   top:

    +   _samecolor (True)

+   bot:

    +   _samecolor (True)

### ZeroLagIndicatorOscillator

Alias:

+   ZeroLagIndicatorOsc, ZLIndicatorOscillator, ZLIndicatorOsc, ZLIndOscillator, ZLIndOsc, ECOscillator, ECOsc, ErrorCorrectingOscillator, ErrorCorrectingOsc

ZeroLagIndicator 在其数据周围的振荡

Lines:

+   ec

Params:

+   period (30)

+   gainlimit (50)

+   _movav (EMA)

PlotInfo:

+   plot (True)

+   plotmaster (None)

+   legendloc (None)

+   subplot (True)

+   plotname ()

+   plotskip (False)

+   plotabove (False)

+   plotlinelabels (False)

+   plotlinevalues (True)

+   plotvaluetags (True)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (False)

PlotLines:

+   ec:

+   _0:

    +   _name (osc)

### haDelta

Alias:

+   haD

Heikin Ashi Delta. Defined by Dan Valcu in his book “Heikin-Ashi: How to Trade Without Candlestick Patterns “.

This indicator measures difference between Heikin Ashi close and open of Heikin Ashi candles, the body of the candle.

要获得信号，请添加由 3 期移动平均值平滑的 haDelta。

为了正确使用，指标的数据必须先经过 Heikin Ahsi 过滤器传递。

公式：

+   haDelta = Heikin Ashi close - Heikin Ashi open（哈尔塔 = 平均柱线收盘价 - 平均柱线开盘价）

+   smoothed = movav(haDelta, period)（平滑的 = 哈尔塔移动平均（哈尔塔，期间））

Lines:（线条）

+   haDelta（哈尔塔）

+   smoothed（平滑的）

Params:（参数）

+   period (3)

+   movav (简单移动平均)

+   autoheikin (真)

PlotInfo:（绘图信息）

+   plot (真)

+   plotmaster (无)

+   legendloc (无)

+   subplot (真)

+   plotname ()（绘图名称）

+   plotskip (假)

+   plotabove (假)

+   plotlinelabels (假)

+   plotlinevalues (真)

+   plotvaluetags (真)

+   plotymargin (0.0)

+   plotyhlines ([])

+   plotyticks ([])

+   plothlines ([])

+   plotforce (假)

PlotLines:（绘制线条）

+   haDelta：（哈尔塔）

    +   color (红色)

+   smoothed:（平滑的）

    +   color (灰色)

    +   _fill_gt ((0, '绿色'))

    +   _fill_lt ((0, '红色'))
