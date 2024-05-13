# TA-Lib 集成

> 原文：[`gbeced.github.io/pyalgotrade/docs/v0.20/html/talib.html`](https://gbeced.github.io/pyalgotrade/docs/v0.20/html/talib.html)

**pyalgotrade.talibext.indicator** 模块提供了与 TA-Lib 的 Python 包装器集成的功能（[`mrjbq7.github.com/ta-lib/`](http://mrjbq7.github.com/ta-lib/)），使得可以直接使用 `pyalgotrade.dataseries.DataSeries` 或 `pyalgotrade.dataseries.bards.BarDataSeries` 实例调用 TA-Lib 函数，而不是使用 numpy 数组。

如果您熟悉**talib**模块，那么使用**pyalgotrade.talibext.indicator**模块应该很简单。在独立使用**talib**时，您会像这样做：

```py
import numpy
import talib

data = numpy.random.random(100)
upper, middle, lower = talib.BBANDS(data, matype=talib.MA_T3) 
```

要在您的策略中使用**pyalgotrade.talibext.indicator**模块，您应该这样做：

```py
def onBars(self, bars):
    closeDs = self.getFeed().getDataSeries("orcl").getCloseDataSeries()
    upper, middle, lower = pyalgotrade.talibext.indicator.BBANDS(closeDs, 100, matype=talib.MA_T3)
    if upper != None:
        print "%s" % upper[-1] 
```

**pyalgotrade.talibext.indicator** 模块中的每个函数接收一个或多个数据序列（大多数只接收一个）以及要从数据序列中使用的值的数量。在上面的例子中，我们正在计算过去 100 个收盘价的布林带。

如果参数名称是**ds**，那么你应该传递一个常规的`pyalgotrade.dataseries.DataSeries`实例，就像上面的例子中所示的那样。

如果参数名称是**barDs**，那么你应该传递一个`pyalgotrade.dataseries.bards.BarDataSeries`实例，就像下一个例子中所示的那样：

```py
def onBars(self, bars):
    barDs = self.getFeed().getDataSeries("orcl")
    sar = indicator.SAR(barDs, 100)
    if sar != None:
        print "%s" % sar[-1] 
```

通过**pyalgotrade.talibext.indicator**模块可用以下 TA-Lib 函数：

`pyalgotrade.talibext.indicator.``AD`(*barDs*, *count*)

查金 A/D 线

`pyalgotrade.talibext.indicator.``ADOSC`(*barDs*, *count*, *fastperiod=-2147483648*, *slowperiod=-2147483648*)

查金 A/D 振荡器

`pyalgotrade.talibext.indicator.``ADX`(*barDs*, *count*, *timeperiod=-2147483648*)

平均方向运动指数

`pyalgotrade.talibext.indicator.``ADXR`(*barDs*, *count*, *timeperiod=-2147483648*)

平均方向运动指数评分

`pyalgotrade.talibext.indicator.``APO`(*ds*, *count*, *fastperiod=-2147483648*, *slowperiod=-2147483648*, *matype=0*)

绝对价格振荡器

`pyalgotrade.talibext.indicator.``AROON`(*barDs*, *count*, *timeperiod=-2147483648*)

阿隆

`pyalgotrade.talibext.indicator.``AROONOSC`(*barDs*, *count*, *timeperiod=-2147483648*)

阿隆振荡器

`pyalgotrade.talibext.indicator.``ATR`(*barDs*, *count*, *timeperiod=-2147483648*)

平均真实波幅

`pyalgotrade.talibext.indicator.``AVGPRICE`(*barDs*, *count*)

平均价格

`pyalgotrade.talibext.indicator.``BBANDS`(*ds*, *count*, *timeperiod=-2147483648*, *nbdevup=-4e+37*, *nbdevdn=-4e+37*, *matype=0*)

布林带

`pyalgotrade.talibext.indicator.``BETA`(*ds1*, *ds2*, *count*, *timeperiod=-2147483648*)

贝塔

`pyalgotrade.talibext.indicator.``BOP`(*barDs*, *count*)

力量平衡

`pyalgotrade.talibext.indicator.``CCI`(*barDs*, *count*, *timeperiod=-2147483648*)

商品通道指数

`pyalgotrade.talibext.indicator.``CDL2CROWS`(*barDs*, *count*)

两只乌鸦

`pyalgotrade.talibext.indicator.``CDL3BLACKCROWS`(*barDs*, *count*)

三只黑乌鸦

`pyalgotrade.talibext.indicator.``CDL3INSIDE`(*barDs*, *count*)

三内上涨/下跌

`pyalgotrade.talibext.indicator.``CDL3LINESTRIKE`(*barDs*, *count*)

三线攻击

`pyalgotrade.talibext.indicator.``CDL3OUTSIDE`(*barDs*, *count*)

三外上涨/下跌

`pyalgotrade.talibext.indicator.``CDL3STARSINSOUTH`(*barDs*, *count*)

南方三星

`pyalgotrade.talibext.indicator.``CDL3WHITESOLDIERS`(*barDs*, *count*)

三个白兵

`pyalgotrade.talibext.indicator.``CDLABANDONEDBABY`(*barDs*, *count*, *penetration=-4e+37*)

遗弃的婴儿

`pyalgotrade.talibext.indicator.``CDLADVANCEBLOCK`(*barDs*, *count*)

先进块

`pyalgotrade.talibext.indicator.``CDLBELTHOLD`(*barDs*, *count*)

饽饽持仓

`pyalgotrade.talibext.indicator.``CDLBREAKAWAY`(*barDs*, *count*)

脱钩

`pyalgotrade.talibext.indicator.``CDLCLOSINGMARUBOZU`(*barDs*, *count*)

收盘光头光脚

`pyalgotrade.talibext.indicator.``CDLCONCEALBABYSWALL`(*barDs*, *count*)

掩盖婴儿燕

`pyalgotrade.talibext.indicator.``CDLCOUNTERATTACK`(*barDs*, *count*)

反击模式

`pyalgotrade.talibext.indicator.``CDLDARKCLOUDCOVER`(*barDs*, *count*, *penetration=-4e+37*)

乌云压顶

`pyalgotrade.talibext.indicator.``CDLDOJI`(*barDs*, *count*)

十字星

`pyalgotrade.talibext.indicator.``CDLDOJISTAR`(*barDs*, *count*)

十字星

`pyalgotrade.talibext.indicator.``CDLDRAGONFLYDOJI`(*barDs*, *count*)

蜻蜓十字

`pyalgotrade.talibext.indicator.``CDLENGULFING`(*barDs*, *count*)

吞没模式

`pyalgotrade.talibext.indicator.``CDLEVENINGDOJISTAR`(*barDs*, *count*, *penetration=-4e+37*)

傍晚十字星

`pyalgotrade.talibext.indicator.``CDLEVENINGSTAR`(*barDs*, *count*, *penetration=-4e+37*)

傍晚之星

`pyalgotrade.talibext.indicator.``CDLGAPSIDESIDEWHITE`(*barDs*, *count*)

上/下缺口并排的白色线条

`pyalgotrade.talibext.indicator.``CDLGRAVESTONEDOJI`(*barDs*, *count*)

墓碑十字

`pyalgotrade.talibext.indicator.``CDLHAMMER`(*barDs*, *count*)

锤子

`pyalgotrade.talibext.indicator.``CDLHANGINGMAN`(*barDs*, *count*)

吊人

`pyalgotrade.talibext.indicator.``CDLHARAMI`(*barDs*, *count*)

孕线模式

`pyalgotrade.talibext.indicator.``CDLHARAMICROSS`(*barDs*, *count*)

孕线交叉模式

`pyalgotrade.talibext.indicator.``CDLHIGHWAVE`(*barDs*, *count*)

高浪蜡烛

`pyalgotrade.talibext.indicator.``CDLHIKKAKE`(*barDs*, *count*)

吸星大法

`pyalgotrade.talibext.indicator.``CDLHIKKAKEMOD`(*barDs*, *count*)

修改的吸星大法

`pyalgotrade.talibext.indicator.``CDLHOMINGPIGEON`(*barDs*, *count*)

家鸽

`pyalgotrade.talibext.indicator.``CDLIDENTICAL3CROWS`(*barDs*, *count*)

相同的三乌鸦

`pyalgotrade.talibext.indicator.``CDLINNECK`(*barDs*, *count*)

脖颈模式

`pyalgotrade.talibext.indicator.``CDLINVERTEDHAMMER`(*barDs*, *count*)

倒锤子

`pyalgotrade.talibext.indicator.``CDLKICKING`(*barDs*, *count*)

踢

`pyalgotrade.talibext.indicator.``CDLKICKINGBYLENGTH`(*barDs*, *count*)

踢 - 牛/熊由更长的光头确定

`pyalgotrade.talibext.indicator.``CDLLADDERBOTTOM`(*barDs*, *count*)

梯底

`pyalgotrade.talibext.indicator.``CDLLONGLEGGEDDOJI`(*barDs*, *count*)

长腿十字星

`pyalgotrade.talibext.indicator.``CDLLONGLINE`(*barDs*, *count*)

长线蜡烛

`pyalgotrade.talibext.indicator.``CDLMARUBOZU`(*barDs*, *count*)

光头

`pyalgotrade.talibext.indicator.``CDLMATCHINGLOW`(*barDs*, *count*)

匹配低位

`pyalgotrade.talibext.indicator.``CDLMATHOLD`(*barDs*, *count*, *penetration=-4e+37*)

矩阵持有

`pyalgotrade.talibext.indicator.``CDLMORNINGDOJISTAR`(*barDs*, *count*, *penetration=-4e+37*)

晨星十字

`pyalgotrade.talibext.indicator.``CDLMORNINGSTAR`(*barDs*, *count*, *penetration=-4e+37*)

晨星

`pyalgotrade.talibext.indicator.``CDLONNECK`(*barDs*, *count*)

在脖子模式

`pyalgotrade.talibext.indicator.``CDLPIERCING`(*barDs*, *count*)

刺透模式

`pyalgotrade.talibext.indicator.``CDLRICKSHAWMAN`(*barDs*, *count*)

面包车人

`pyalgotrade.talibext.indicator.``CDLRISEFALL3METHODS`(*barDs*, *count*)

上升/下降三法

`pyalgotrade.talibext.indicator.``CDLSEPARATINGLINES`(*barDs*, *count*)

分隔线

`pyalgotrade.talibext.indicator.``CDLSHOOTINGSTAR`(*barDs*, *count*)

长尾锤

`pyalgotrade.talibext.indicator.``CDLSHORTLINE`(*barDs*, *count*)

短线蜡烛

`pyalgotrade.talibext.indicator.``CDLSPINNINGTOP`(*barDs*, *count*)

陀螺顶

`pyalgotrade.talibext.indicator.``CDLSTALLEDPATTERN`(*barDs*, *count*)

停滞模式

`pyalgotrade.talibext.indicator.``CDLSTICKSANDWICH`(*barDs*, *count*)

杆子三明治

`pyalgotrade.talibext.indicator.``CDLTAKURI`(*barDs*, *count*)

打瞌睡（底部）

`pyalgotrade.talibext.indicator.``CDLTASUKIGAP`(*barDs*, *count*)

领带缺口

`pyalgotrade.talibext.indicator.``CDLTHRUSTING`(*barDs*, *count*)

冲刺模式

`pyalgotrade.talibext.indicator.``CDLTRISTAR`(*barDs*, *count*)

三星模式

`pyalgotrade.talibext.indicator.``CDLUNIQUE3RIVER`(*barDs*, *count*)

独特的三江

`pyalgotrade.talibext.indicator.``CDLUPSIDEGAP2CROWS`(*barDs*, *count*)

上缺口两乌鸦

`pyalgotrade.talibext.indicator.``CDLXSIDEGAP3METHODS`(*barDs*, *count*)

上缺口/下缺口三法

`pyalgotrade.talibext.indicator.``CMO`(*ds*, *count*, *timeperiod=-2147483648*)

查德动量振荡器

`pyalgotrade.talibext.indicator.``CORREL`(*ds1*, *ds2*, *count*, *timeperiod=-2147483648*)

皮尔逊相关系数（r）

`pyalgotrade.talibext.indicator.``DEMA`(*ds*, *count*, *timeperiod=-2147483648*)

双指数移动平均

`pyalgotrade.talibext.indicator.``DX`(*barDs*, *count*, *timeperiod=-2147483648*)

方向运动指数

`pyalgotrade.talibext.indicator.``EMA`(*ds*, *count*, *timeperiod=-2147483648*)

指数移动平均

`pyalgotrade.talibext.indicator.``HT_DCPERIOD`(*ds*, *count*)

Hilbert 变换 - 主导周期

`pyalgotrade.talibext.indicator.``HT_DCPHASE`(*ds*, *count*)

Hilbert 变换 - 主导周期相位

`pyalgotrade.talibext.indicator.``HT_PHASOR`(*ds*, *count*)

Hilbert 变换 - 相量分量

`pyalgotrade.talibext.indicator.``HT_SINE`(*ds*, *count*)

Hilbert 变换 - 正弦波

`pyalgotrade.talibext.indicator.``HT_TRENDLINE`(*ds*, *count*)

Hilbert 变换 - 瞬时趋势线

`pyalgotrade.talibext.indicator.``HT_TRENDMODE`(*ds*, *count*)

Hilbert 变换 - 趋势 vs 周期模式

`pyalgotrade.talibext.indicator.``KAMA`(*ds*, *count*, *timeperiod=-2147483648*)

Kaufman 自适应移动平均

`pyalgotrade.talibext.indicator.``LINEARREG`(*ds*, *count*, *timeperiod=-2147483648*)

线性回归

`pyalgotrade.talibext.indicator.``LINEARREG_ANGLE`(*ds*, *count*, *timeperiod=-2147483648*)

线性回归角度

`pyalgotrade.talibext.indicator.``LINEARREG_INTERCEPT`(*ds*, *count*, *timeperiod=-2147483648*)

线性回归截距

`pyalgotrade.talibext.indicator.``LINEARREG_SLOPE`(*ds*, *count*, *timeperiod=-2147483648*)

线性回归斜率

`pyalgotrade.talibext.indicator.``MA`(*ds*, *count*, *timeperiod=-2147483648*, *matype=0*)

所有移动平均

`pyalgotrade.talibext.indicator.``MACD`(*ds*, *count*, *fastperiod=-2147483648*, *slowperiod=-2147483648*, *signalperiod=-2147483648*)

移动平均收敛/发散

`pyalgotrade.talibext.indicator.``MACDEXT`(*ds*, *count*, *fastperiod=-2147483648*, *fastmatype=0*, *slowperiod=-2147483648*, *slowmatype=0*, *signalperiod=-2147483648*, *signalmatype=0*)

具有可控制 MA 类型的 MACD

`pyalgotrade.talibext.indicator.``MACDFIX`(*ds*, *count*, *signalperiod=-2147483648*)

移动平均收敛/发散 修正 12/26

`pyalgotrade.talibext.indicator.``MAMA`(*ds*, *count*, *fastlimit=-4e+37*, *slowlimit=-4e+37*)

MESA 自适应移动平均

`pyalgotrade.talibext.indicator.``MAX`(*ds*, *count*, *timeperiod=-2147483648*)

一段时间内的最高值

`pyalgotrade.talibext.indicator.``MAXINDEX`(*ds*, *count*, *timeperiod=-2147483648*)

指定周期内最高值的索引

`pyalgotrade.talibext.indicator.``MEDPRICE`(*barDs*, *count*)

中位价

`pyalgotrade.talibext.indicator.``MFI`(*barDs*, *count*, *timeperiod=-2147483648*)

资金流量指标

`pyalgotrade.talibext.indicator.``MIDPOINT`(*ds*, *count*, *timeperiod=-2147483648*)

一段时间内的中点

`pyalgotrade.talibext.indicator.``MIDPRICE`(*barDs*, *count*, *timeperiod=-2147483648*)

一段时间内的中点价格

`pyalgotrade.talibext.indicator.``MIN`(*ds*, *count*, *timeperiod=-2147483648*)

指定周期内的最低值

`pyalgotrade.talibext.indicator.``MININDEX`(*ds*, *count*, *timeperiod=-2147483648*)

在指定时间段内的最低值的指数

`pyalgotrade.talibext.indicator.``MINMAX`(*ds*, *count*, *timeperiod=-2147483648*)

在指定时间段内的最低值和最高值

`pyalgotrade.talibext.indicator.``MINMAXINDEX`(*ds*, *count*, *timeperiod=-2147483648*)

在指定时间段内的最低值和最高值的指数

`pyalgotrade.talibext.indicator.``MINUS_DI`(*barDs*, *count*, *timeperiod=-2147483648*)

负向动向指标

`pyalgotrade.talibext.indicator.``MINUS_DM`(*barDs*, *count*, *timeperiod=-2147483648*)

负向动向指数

`pyalgotrade.talibext.indicator.``MOM`(*ds*, *count*, *timeperiod=-2147483648*)

动量

`pyalgotrade.talibext.indicator.``NATR`(*barDs*, *count*, *timeperiod=-2147483648*)

归一化平均真实波幅

`pyalgotrade.talibext.indicator.``OBV`(*ds1*, *volumeDs*, *count*)

量价均衡指标

`pyalgotrade.talibext.indicator.``PLUS_DI`(*barDs*, *count*, *timeperiod=-2147483648*)

正向动向指数

`pyalgotrade.talibext.indicator.``PLUS_DM`(*barDs*, *count*, *timeperiod=-2147483648*)

正向动向指数

`pyalgotrade.talibext.indicator.``PPO`(*ds*, *count*, *fastperiod=-2147483648*, *slowperiod=-2147483648*, *matype=0*)

百分比价格振荡器

`pyalgotrade.talibext.indicator.``ROC`(*ds*, *count*, *timeperiod=-2147483648*)

变化率: ((price/prevPrice)-1)*100

`pyalgotrade.talibext.indicator.``ROCP`(*ds*, *count*, *timeperiod=-2147483648*)

百分比价格波动率: (price-prevPrice)/prevPrice

`pyalgotrade.talibext.indicator.``ROCR`(*ds*, *count*, *timeperiod=-2147483648*)

变化率比率: (price/prevPrice)

`pyalgotrade.talibext.indicator.``ROCR100`(*ds*, *count*, *timeperiod=-2147483648*)

变化率比率 100 比例: (price/prevPrice)*100

`pyalgotrade.talibext.indicator.``RSI`(*ds*, *count*, *timeperiod=-2147483648*)

相对强弱指标

`pyalgotrade.talibext.indicator.``SAR`(*barDs*, *count*, *acceleration=-4e+37*, *maximum=-4e+37*)

抛物线转向

`pyalgotrade.talibext.indicator.``SAREXT`(*barDs*, *count*, *startvalue=-4e+37*, *offsetonreverse=-4e+37*, *accelerationinitlong=-4e+37*, *accelerationlong=-4e+37*, *accelerationmaxlong=-4e+37*, *accelerationinitshort=-4e+37*, *accelerationshort=-4e+37*, *accelerationmaxshort=-4e+37*)

抛物线转向-扩展

`pyalgotrade.talibext.indicator.``SMA`(*ds*, *count*, *timeperiod=-2147483648*)

简单移动平均线

`pyalgotrade.talibext.indicator.``STDDEV`(*ds*, *count*, *timeperiod=-2147483648*, *nbdev=-4e+37*)

标准差

`pyalgotrade.talibext.indicator.``STOCH`(*barDs*, *count*, *fastk_period=-2147483648*, *slowk_period=-2147483648*, *slowk_matype=0*, *slowd_period=-2147483648*, *slowd_matype=0*)

随机指标

`pyalgotrade.talibext.indicator.``STOCHF`(*barDs*, *count*, *fastk_period=-2147483648*, *fastd_period=-2147483648*, *fastd_matype=0*)

快速随机指标

`pyalgotrade.talibext.indicator.``STOCHRSI`(*ds*, *count*, *timeperiod=-2147483648*, *fastk_period=-2147483648*, *fastd_period=-2147483648*, *fastd_matype=0*)

随机相对强度指数

`pyalgotrade.talibext.indicator.``SUM`(*ds*, *count*, *timeperiod=-2147483648*)

求和

`pyalgotrade.talibext.indicator.``T3`(*ds*, *count*, *timeperiod=-2147483648*, *vfactor=-4e+37*)

三重指数移动平均线（T3）

`pyalgotrade.talibext.indicator.``TEMA`(*ds*, *count*, *timeperiod=-2147483648*)

三重指数移动平均线

`pyalgotrade.talibext.indicator.``TRANGE`(*barDs*, *count*)

真实范围

`pyalgotrade.talibext.indicator.``TRIMA`(*ds*, *count*, *timeperiod=-2147483648*)

三角形移动平均

`pyalgotrade.talibext.indicator.``TRIX`(*ds*, *count*, *timeperiod=-2147483648*)

三重平滑 EMA 的 1 日变化率（ROC）

`pyalgotrade.talibext.indicator.``TSF`(*ds*, *count*, *timeperiod=-2147483648*)

时间序列预测

`pyalgotrade.talibext.indicator.``TYPPRICE`(*barDs*, *count*)

典型价格

`pyalgotrade.talibext.indicator.``ULTOSC`(*barDs*, *count*, *timeperiod1=-2147483648*, *timeperiod2=-2147483648*, *timeperiod3=-2147483648*)

终极振荡器

`pyalgotrade.talibext.indicator.``VAR`(*ds*, *count*, *timeperiod=-2147483648*, *nbdev=-4e+37*)

方差

`pyalgotrade.talibext.indicator.``WCLPRICE`(*barDs*, *count*)

加权收盘价

`pyalgotrade.talibext.indicator.``WILLR`(*barDs*, *count*, *timeperiod=-2147483648*)

威廉%R

`pyalgotrade.talibext.indicator.``WMA`(*ds*, *count*, *timeperiod=-2147483648*)

加权移动平均线

