# 指标 - ta-lib - 参考

> 原文：[`www.backtrader.com/docu/talibindautoref/`](https://www.backtrader.com/docu/talibindautoref/)

## TA-Lib 指标参考

### ACOS

ACOS([input_arrays])

向量三角函数 ACos（Math Transform）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线条：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### AD

AD([input_arrays])

蔡金 A/D 线（Volume Indicators）

输入：

```py
prices: [‘high’, ‘low’, ‘close’, ‘volume’]
```

输出：

```py
real
```

线条：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### ADD

ADD([input_arrays])

向量算术加法（Math Operators）

输入：

```py
price0: (any ndarray) price1: (any ndarray)
```

输出：

```py
real
```

线条：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### ADOSC

ADOSC([input_arrays], [fastperiod=3], [slowperiod=10])

蔡金 A/D 振荡器（Volume Indicators）

输入：

```py
prices: [‘high’, ‘low’, ‘close’, ‘volume’]
```

参数：

```py
fastperiod: 3 slowperiod: 10
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* fastperiod (3)

* slowperiod (10)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### ADX

ADX([input_arrays], [timeperiod=14])

平均趋向指数（Momentum Indicators）

输入：

```py
prices: [‘high’, ‘low’, ‘close’]
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### ADXR

ADXR([input_arrays], [timeperiod=14])

平均趋向指数评级（Momentum Indicators）

输入：

```py
prices: [‘high’, ‘low’, ‘close’]
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### APO

APO([input_arrays], [fastperiod=12], [slowperiod=26], [matype=0])

绝对价格振荡器（Momentum Indicators）

输入：

```py
price: (any ndarray)
```

参数：

```py
fastperiod: 12 slowperiod: 26 matype: 0 (Simple Moving Average)
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* fastperiod (12)

* slowperiod (26)

* matype (0)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### AROON

AROON([input_arrays], [timeperiod=14])

Aroon（Momentum Indicators）

输入：

```py
prices: [‘high’, ‘low’]
```

参数：

```py
timeperiod: 14
```

输出：

```py
aroondown aroonup
```

线条：

```py
* aroondown

* aroonup
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* aroondown: - ls (–)

* aroonup: - ls (-)
```

### AROONOSC

AROONOSC([input_arrays], [timeperiod=14])

Aroon Oscillator（Momentum Indicators）

输入：

```py
prices: [‘high’, ‘low’]
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### ASIN

ASIN([input_arrays])

向量三角函数 ASin（Math Transform）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线条：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### ATAN

ATAN([input_arrays])

向量三角函数 ATan（Math Transform）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线条：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### ATR

ATR([input_arrays], [timeperiod=14])

真实波幅（Volatility Indicators）

输入：

```py
prices: [‘high’, ‘low’, ‘close’]
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### AVGPRICE

AVGPRICE([input_arrays])

平均价格（Price Transform）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
real
```

线条：

```py
* real
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### BBANDS

BBANDS([input_arrays], [timeperiod=5], [nbdevup=2], [nbdevdn=2], [matype=0])

布林带（Overlap Studies）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 5 nbdevup: 2 nbdevdn: 2 matype: 0 (Simple Moving
Average)
```

输出：

```py
upperband middleband lowerband
```

线条：

```py
* upperband

* middleband

* lowerband
```

参数：

```py
* timeperiod (5)

* nbdevup (2)

* nbdevdn (2)

* matype (0)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* middleband: - _samecolor (True) - ls (-)

* upperband:

* lowerband: - _samecolor (True)
```

### BETA

BETA([input_arrays], [timeperiod=5])

贝塔（Statistic Functions）

输入：

```py
price0: (any ndarray) price1: (any ndarray)
```

参数：

```py
timeperiod: 5
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (5)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### BOP

BOP([input_arrays])

势力均衡（Momentum Indicators）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
real
```

线条：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### CCI

CCI([input_arrays], [timeperiod=14])

商品通道指数（Momentum Indicators）

输入：

```py
prices: [‘high’, ‘low’, ‘close’]
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

CDL2###CROWS

CDL2CROWS([input_arrays])

两只乌鸦（Pattern Recognition）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDL2CROWS)
```

CDL3###BLACKCROWS

CDL3BLACKCROWS([input_arrays])

三黑鸦（Pattern Recognition）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDL3BLACKCROWS)
```

CDL3###INSIDE

CDL3INSIDE([input_arrays])

三内部上升/下降（Pattern Recognition）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDL3INSIDE)
```

CDL3###LINESTRIKE

CDL3LINESTRIKE([input_arrays])

三线行云（Pattern Recognition）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDL3LINESTRIKE)
```

CDL3###OUTSIDE

CDL3OUTSIDE（[输入数组]）

三外上/下（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDL3OUTSIDE)
```

CDL3###STARSINSOUTH

CDL3STARSINSOUTH（[输入数组]）

南方三星（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDL3STARSINSOUTH)
```

CDL3###WHITESOLDIERS

CDL3WHITESOLDIERS（[输入数组]）

三只前进的白兵（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDL3WHITESOLDIERS)
```

### CDLABANDONEDBABY

CDLABANDONEDBABY（[输入数组]，[穿透=0.3]）

弃婴（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

参数：

```py
penetration: 0.3
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

参数：

```py
* penetration (0.3)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLABANDONEDBABY)
```

### CDLADVANCEBLOCK

CDLADVANCEBLOCK（[输入数组]）

先进区块（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLADVANCEBLOCK)
```

### CDLBELTHOLD

CDLBELTHOLD（[输入数组]）

持有（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLBELTHOLD)
```

### CDLBREAKAWAY

CDLBREAKAWAY（[输入数组]）

突破（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLBREAKAWAY)
```

### CDLCLOSINGMARUBOZU

CDLCLOSINGMARUBOZU（[输入数组]）

收盘光头兵（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLCLOSINGMARUBOZU)
```

### CDLCONCEALBABYSWALL

CDLCONCEALBABYSWALL（[输入数组]）

隐蔽的宝宝吞噬（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLCONCEALBABYSWALL)
```

### CDLCOUNTERATTACK

CDLCOUNTERATTACK（[输入数组]）

反击（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLCOUNTERATTACK)
```

### CDLDARKCLOUDCOVER

CDLDARKCLOUDCOVER（[输入数组]，[穿透=0.5]）

黑云盖顶（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

参数：

```py
penetration: 0.5
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

参数：

```py
* penetration (0.5)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLDARKCLOUDCOVER)
```

### CDLDOJI

CDLDOJI（[输入数组]）

十字（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLDOJI)
```

### CDLDOJISTAR

CDLDOJISTAR（[输入数组]）

十字星（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLDOJISTAR)
```

### CDLDRAGONFLYDOJI

CDLDRAGONFLYDOJI（[输入数组]）

蜻蜓十字（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLDRAGONFLYDOJI)
```

### CDLENGULFING

CDLENGULFING（[输入数组]）

吞没模式（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLENGULFING)
```

### CDLEVENINGDOJISTAR

CDLEVENINGDOJISTAR（[输入数组]，[穿透=0.3]）

晚十字星（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

参数：

```py
penetration: 0.3
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

参数：

```py
* penetration (0.3)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLEVENINGDOJISTAR)
```

### CDLEVENINGSTAR

CDLEVENINGSTAR（[输入数组]，[穿透=0.3]）

晚星（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

参数：

```py
penetration: 0.3
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

参数：

```py
* penetration (0.3)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLEVENINGSTAR)
```

### CDLGAPSIDESIDEWHITE

CDLGAPSIDESIDEWHITE（[输入数组]）

上/下缺口并排的白线（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLGAPSIDESIDEWHITE)
```

### CDLGRAVESTONEDOJI

CDLGRAVESTONEDOJI（[输入数组]）

墓碑十字（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLGRAVESTONEDOJI)
```

### CDLHAMMER

CDLHAMMER（[输入数组]）

锤子（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLHAMMER)
```

### CDLHANGINGMAN

CDLHANGINGMAN（[输入数组]）

吊人（图案识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线条：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLHANGINGMAN)
```

### CDLHARAMI

CDLHARAMI（[输入数组]）

哈拉米模式（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLHARAMI)
```

### CDLHARAMICROSS

CDLHARAMICROSS（[输入数组]）

哈拉米十字模式（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLHARAMICROSS)
```

### CDLHIGHWAVE

CDLHIGHWAVE（[输入数组]）

高浪蜡烛（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLHIGHWAVE)
```

### CDLHIKKAKE

CDLHIKKAKE（[输入数组]）

Hikkake 模式（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLHIKKAKE)
```

### CDLHIKKAKEMOD

CDLHIKKAKEMOD（[输入数组]）

修改的 Hikkake 模式（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLHIKKAKEMOD)
```

### CDLHOMINGPIGEON

CDLHOMINGPIGEON（[输入数组]）

家鸽（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLHOMINGPIGEON)
```

CDLIDENTICAL3###CROWS

CDLIDENTICAL3CROWS（[输入数组]）

相同的三只乌鸦（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLIDENTICAL3CROWS)
```

### CDLINNECK

CDLINNECK（[输入数组]）

颈内模式（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLINNECK)
```

### CDLINVERTEDHAMMER

CDLINVERTEDHAMMER（[输入数组]）

倒锤子（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLINVERTEDHAMMER)
```

### CDLKICKING

CDLKICKING（[输入数组]）

踢腿（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

��：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLKICKING)
```

### CDLKICKINGBYLENGTH

CDLKICKINGBYLENGTH（[输入数组]）

踢腿-由较长的光头光脚决定（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLKICKINGBYLENGTH)
```

### CDLLADDERBOTTOM

CDLLADDERBOTTOM（[输入数组]）

阶梯底部（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLLADDERBOTTOM)
```

### CDLLONGLEGGEDDOJI

CDLLONGLEGGEDDOJI（[输入数组]）

长腿十字（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLLONGLEGGEDDOJI)
```

### CDLLONGLINE

CDLLONGLINE（[输入数组]）

长线蜡烛（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLLONGLINE)
```

### CDLMARUBOZU

CDLMARUBOZU（[输入数组]）

光头光脚（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLMARUBOZU)
```

### CDLMATCHINGLOW

CDLMATCHINGLOW（[输入数组]）

匹配低点（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLMATCHINGLOW)
```

### CDLMATHOLD

CDLMATHOLD（[输入数组]，[穿透=0.5]）

马特霍尔德（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

参数：

```py
penetration: 0.5
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

参数：

```py
* penetration (0.5)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLMATHOLD)
```

### CDLMORNINGDOJISTAR

CDLMORNINGDOJISTAR（[输入数组]，[穿透=0.3]）

晨十字星（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

参数：

```py
penetration: 0.3
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

参数：

```py
* penetration (0.3)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLMORNINGDOJISTAR)
```

### CDLMORNINGSTAR

CDLMORNINGSTAR（[输入数组]，[穿透=0.3]）

晨星（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

参数：

```py
penetration: 0.3
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

参数：

```py
* penetration (0.3)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLMORNINGSTAR)
```

### CDLONNECK

CDLONNECK（[输入数组]）

颈上模式（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLONNECK)
```

### CDLPIERCING

CDLPIERCING（[输入数组]）

刺透模式（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLPIERCING)
```

### CDLRICKSHAWMAN

CDLRICKSHAWMAN（[输入数组]）

人力车夫（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLRICKSHAWMAN)
```

CDLRISEFALL3###METHODS

CDLRISEFALL3METHODS（[输入数组]）

上涨缺口两鸦（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘制信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLRISEFALL3METHODS)
```

### CDLSEPARATINGLINES

CDLSEPARATINGLINES（[input_arrays]）

分离线（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘制信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLSEPARATINGLINES)
```

### CDLSHOOTINGSTAR

CDLSHOOTINGSTAR（[input_arrays]）

流星（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘制信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLSHOOTINGSTAR)
```

### CDLSHORTLINE

CDLSHORTLINE（[input_arrays]）

短线蜡烛（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘制信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLSHORTLINE)
```

### CDLSPINNINGTOP

CDLSPINNINGTOP（[input_arrays]）

纺锤顶（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘制信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLSPINNINGTOP)
```

### CDLSTALLEDPATTERN

CDLSTALLEDPATTERN（[input_arrays]）

停滞模式（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘制信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLSTALLEDPATTERN)
```

### CDLSTICKSANDWICH

CDLSTICKSANDWICH（[input_arrays]）

条形三明治（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘制信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLSTICKSANDWICH)
```

### CDLTAKURI

CDLTAKURI（[input_arrays]）

田坑（具有非常长下影线的蜻蜓十字线）（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘制信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLTAKURI)
```

### CDLTASUKIGAP

CDLTASUKIGAP（[input_arrays]）

田坑缺口（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘制信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLTASUKIGAP)
```

### CDLTHRUSTING

CDLTHRUSTING（[input_arrays]）

冲击模式（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘制信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLTHRUSTING)
```

### CDLTRISTAR

CDLTRISTAR（[input_arrays]）

三星模式（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘制信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLTRISTAR)
```

CDLUNIQUE3###RIVER

CDLUNIQUE3RIVER（[input_arrays]）

唯一的 3 条河（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘制信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLUNIQUE3RIVER)
```

CDLUPSIDEGAP2###CROWS

CDLUPSIDEGAP2CROWS（[input_arrays]）

上涨缺口两鸦（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘制信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLUPSIDEGAP2CROWS)
```

CDLXSIDEGAP3###METHODS

CDLXSIDEGAP3METHODS（[input_arrays]）

上涨/下跌缺口三法（模式识别）

输入：

```py
prices: [‘open’, ‘high’, ‘low’, ‘close’]
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer

* _candleplot
```

绘制信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (True)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* integer: - _plotskip (True)

* _candleplot: - marker (d) - fillstyle (full) - markersize (7.0) -
  ls () - _name (CDLXSIDEGAP3METHODS)
```

### CEIL

CEIL（[input_arrays]）

向量向上取整（数学变换）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线：

```py
* real
```

绘制信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* real: - ls (-)
```

### CMO

CMO（[input_arrays]，[timeperiod=14]）

钱德动量摆动指标（动量指标）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘制信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* real: - ls (-)
```

### CORREL

CORREL（[input_arrays]，[timeperiod=30]）

皮尔逊相关系数®（统计函数）

输入：

```py
price0: (any ndarray) price1: (any ndarray)
```

参数：

```py
timeperiod: 30
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (30)
```

绘制信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* real: - ls (-)
```

### COS

COS（[input_arrays]）

向量三角余弦（数学变换）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线：

```py
* real
```

绘制信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* real: - ls (-)
```

### COSH

COSH（[input_arrays]）

向量三角函数 Cosh（数学变换）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线：

```py
* real
```

绘制信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* real: - ls (-)
```

### DEMA

DEMA（[input_arrays]，[timeperiod=30]）

双指数移动平均线（重叠研究）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 30
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (30)
```

绘制信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* real: - ls (-)
```

### DIV

DIV（[input_arrays]）

向量算术除法（数学运算符）

输入：

```py
price0: (any ndarray) price1: (any ndarray)
```

输出：

```py
real
```

线：

```py
* real
```

绘制信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* real: - ls (-)
```

### DX

DX（[input_arrays]，[timeperiod=14]）

方向运动指数（动量指标）

输入：

```py
prices: [‘high’, ‘low’, ‘close’]
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘制信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘制线：

```py
* real: - ls (-)
```

### EMA

EMA（[input_arrays]，[timeperiod=30]）

指数移动平均线（重叠研究）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 30
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (30)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### EXP

EXP([input_arrays])

向量算术指数（数学变换）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### FLOOR

FLOOR([input_arrays])

向量下限（数学变换）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

HT_###周期

HT_DCPERIOD([input_arrays])

希尔伯特变换 - 主导周期周期（周期指标）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

HT_###周期相位

HT_DCPHASE([input_arrays])

希尔伯特变换 - 主导周期相位（周期指标）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

HT_###相位计量器

HT_PHASOR([input_arrays])

希尔伯特变换 - 相位分量（周期指标）

输入：

```py
price: (any ndarray)
```

输出：

```py
inphase quadrature
```

线：

```py
* inphase

* quadrature
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* inphase: - ls (-)

* quadrature: - ls (–)
```

HT_###正弦

HT_SINE([input_arrays])

希尔伯特变换 - 正弦波（周期指标）

输入：

```py
price: (any ndarray)
```

输出：

```py
sine leadsine
```

线：

```py
* sine

* leadsine
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* leadsine: - ls (–)

* sine: - ls (-)
```

HT_###趋势线

HT 趋势线([input_arrays])

希尔伯特变换 - 瞬时趋势线（重叠研究）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线：

```py
* real
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

HT_###趋势模式

HT 趋势模式([input_arrays])

希尔伯特变换 - 趋势 vs 周期模式（周期指标）

输入：

```py
price: (any ndarray)
```

输出：

```py
integer (values are -100, 0 or 100)
```

线：

```py
* integer
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* integer: - ls (-)
```

### KAMA

KAMA([input_arrays], [timeperiod=30])

考夫曼自适应移动平均线（重叠研究）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 30
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (30)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### 线性回归

线性回归([input_arrays], [timeperiod=14])

线性回归（统计函数）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

线性回归角度

线性回归角度([input_arrays], [timeperiod=14])

线性回归角度（统计函数）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

线性回归截距

线性回归截距([input_arrays], [timeperiod=14])

线性回归截距（统计函数）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

线性回归斜率

线性回归斜率([input_arrays], [timeperiod=14])

线性回归斜率（统计函数）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### LN

LN([input_arrays])

向量自然对数（数学变换）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

LOG10

LOG10([input_arrays])

向量对数 10（数学变换）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### MA

MA([input_arrays], [timeperiod=30], [matype=0])

移动平均（重叠研究）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 30 matype: 0 (Simple Moving Average)
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (30)

* matype (0)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### MACD

MACD([input_arrays], [fastperiod=12], [slowperiod=26], [signalperiod=9])

移动平均收敛/发散（动量指标）

输入：

```py
price: (any ndarray)
```

参数：

```py
fastperiod: 12 slowperiod: 26 signalperiod: 9
```

输出：

```py
macd macdsignal macdhist
```

线：

```py
* macd

* macdsignal

* macdhist
```

参数：

```py
* fastperiod (12)

* slowperiod (26)

* signalperiod (9)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* macdsignal: - ls (–)

* macd: - ls (-)

* macdhist: - _method (bar)
```

### MACDEXT

MACDEXT([input_arrays], [fastperiod=12], [fastmatype=0], [slowperiod=26], [slowmatype=0], [signalperiod=9], [signalmatype=0])

可控 MA 类型的 MACD（动量指标）

输入：

```py
price: (any ndarray)
```

参数：

```py
fastperiod: 12 fastmatype: 0 slowperiod: 26 slowmatype: 0
signalperiod: 9 signalmatype: 0
```

输出：

```py
macd macdsignal macdhist
```

线：

```py
* macd

* macdsignal

* macdhist
```

参数：

```py
* fastperiod (12)

* fastmatype (0)

* slowperiod (26)

* slowmatype (0)

* signalperiod (9)

* signalmatype (0)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* macdsignal: - ls (–)

* macd: - ls (-)

* macdhist: - _method (bar)
```

### MACDFIX

MACDFIX([input_arrays], [signalperiod=9])

移动平均收敛/发散修正 12/26（动量指标）

输入：

```py
price: (any ndarray)
```

参数：

```py
signalperiod: 9
```

输出：

```py
macd macdsignal macdhist
```

线：

```py
* macd

* macdsignal

* macdhist
```

参数：

```py
* signalperiod (9)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* macdsignal: - ls (–)

* macd: - ls (-)

* macdhist: - _method (bar)
```

### MAMA

MAMA（[input_arrays]，[fastlimit=0.5]，[slowlimit=0.05]）

MESA 自适应移动平均线（重叠研究）

输入：

```py
price: (any ndarray)
```

参数：

```py
fastlimit: 0.5 slowlimit: 0.05
```

输出：

```py
mama fama
```

线条：

```py
* mama

* fama
```

参数：

```py
* fastlimit (0.5)

* slowlimit (0.05)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* mama: - ls (-)

* fama: - ls (–)
```

### MAVP

MAVP（[input_arrays]，[minperiod=2]，[maxperiod=30]，[matype=0]）

变动平均线（重叠研究）

输入：

```py
price: (any ndarray) periods: (any ndarray)
```

参数：

```py
minperiod: 2 maxperiod: 30 matype: 0 (Simple Moving Average)
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* minperiod (2)

* maxperiod (30)

* matype (0)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* real: - ls (-)
```

### 最大

MAX（[input_arrays]，[timeperiod=30]）

指定期间内的最高值（数学运算符）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 30
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (30)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* real: - ls (-)
```

### MAXINDEX

MAXINDEX（[input_arrays]，[timeperiod=30]）

指定期间内最高值的索引（数学运算符）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 30
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer
```

参数：

```py
* timeperiod (30)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* integer: - ls (-)
```

### MEDPRICE

MEDPRICE（[input_arrays]）

中间价（价格转换）

输入：

```py
prices: [‘high’, ‘low’]
```

输出：

```py
real
```

线条：

```py
* real
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* real: - ls (-)
```

### MFI

MFI（[input_arrays]，[timeperiod=14]）

资金流量指数（动量指标）

输入：

```py
prices: [‘high’, ‘low’, ‘close’, ‘volume’]
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* real: - ls (-)
```

### MIDPOINT

MIDPOINT（[input_arrays]，[timeperiod=14]）

期间内的中点（重叠研究）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* real: - ls (-)
```

### MIDPRICE

MIDPRICE（[input_arrays]，[timeperiod=14]）

期间的中间价（重叠研究）

输入：

```py
prices: [‘high’, ‘low’]
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* real: - ls (-)
```

### 最小

MIN（[input_arrays]，[timeperiod=30]）

指定期间内的最低值（数学运算符）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 30
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (30)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* real: - ls (-)
```

### MININDEX

MININDEX（[input_arrays]，[timeperiod=30]）

指定期间内最低值的索引（数学运算符）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 30
```

输出：

```py
integer (values are -100, 0 or 100)
```

线条：

```py
* integer
```

参数：

```py
* timeperiod (30)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* integer: - ls (-)
```

### MINMAX

MINMAX（[input_arrays]，[timeperiod=30]）

指定期间内的最低和最高值（数学运算符）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 30
```

输出：

```py
min max
```

线条：

```py
* min

* max
```

参数：

```py
* timeperiod (30)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* max: - ls (-)

* min: - ls (-)
```

### MINMAXINDEX

MINMAXINDEX（[input_arrays]，[timeperiod=30]）

指定周期内的最低和最高值的指数（数学运算符）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 30
```

输出：

```py
minidx maxidx
```

线条：

```py
* minidx

* maxidx
```

参数：

```py
* timeperiod (30)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* maxidx: - ls (-)

* minidx: - ls (-)
```

MINUS_###DI

MINUS_DI（[input_arrays]，[timeperiod=14]）

负向指标（动量指标）

输入：

```py
prices: [‘high’, ‘low’, ‘close’]
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* real: - ls (-)
```

MINUS_###DM

MINUS_DM（[input_arrays]，[timeperiod=14]）

负向趋势运动（动量指标）

输入：

```py
prices: [‘high’, ‘low’]
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* real: - ls (-)
```

### MOM

MOM（[input_arrays]，[timeperiod=10]）

动量（动量指标）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 10
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (10)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* real: - ls (-)
```

### MULT

MULT（[input_arrays]）

矢量算术乘法（数学运算符）

输入：

```py
price0: (any ndarray) price1: (any ndarray)
```

输出：

```py
real
```

线条：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* real: - ls (-)
```

### NATR

NATR（[input_arrays]，[timeperiod=14]）

归一化的平均真实范围（波动率指标）

输入：

```py
prices: [‘high’, ‘low’, ‘close’]
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* real: - ls (-)
```

### OBV

OBV（[input_arrays]）

在平衡量上（成交量指标）

输入：

```py
price: (any ndarray) prices: [‘volume’]
```

输出：

```py
real
```

线条：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

折线：

```py
* real: - ls (-)
```

PLUS_###DI

PLUS_DI（[input_arrays]，[timeperiod=14]）

正向指标（动量指标）

输入：

```py
prices: [‘high’, ‘low’, ‘close’]
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

PLUS_###DM

PLUS_DM（[输入数组]，[时间周期=14]）

正向趋势指标（动量指标）

输入：

```py
prices: [‘high’, ‘low’]
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### PPO

PPO（[输入数组]，[fastperiod=12]，[slowperiod=26]，[matype=0]）

百分比价格振荡器（动量指标）

输入：

```py
price: (any ndarray)
```

参数：

```py
fastperiod: 12 slowperiod: 26 matype: 0 (Simple Moving Average)
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* fastperiod (12)

* slowperiod (26)

* matype (0)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### ROC

ROC（[输入数组]，[时间周期=10]）

变化率：((price/prevPrice)-1)*100（动量指标）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 10
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (10)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### ROCP

ROCP（[输入数组]，[时间周期=10]）

变化率百分比：(price-prevPrice)/prevPrice（动量指标）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 10
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (10)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### ROCR

ROCR（[输入数组]，[时间周期=10]）

变化率比率：（price/prevPrice）（动量指标）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 10
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (10)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

ROCR100

ROCR100（[输入数组]，[时间周期=10]）

百分比价格振荡比率 100 比例：（价格/prevPrice）*100（动量指标）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 10
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (10)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### RSI

RSI（[输入数组]，[时间周期=14]）

相对强度指数（动量指标）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### SAR

SAR（[输入数组]，[acceleration=0.02]，[maximum=0.2]）

抛物线 SAR（重叠研究）

输入：

```py
prices: [‘high’, ‘low’]
```

参数：

```py
acceleration: 0.02 maximum: 0.2
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* acceleration (0.02)

* maximum (0.2)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### SAREXT

SAREXT（[输入数组]，[startvalue=0]，[offsetonreverse=0]，[accelerationinitlong=0.02]，[accelerationlong=0.02]，[accelerationmaxlong=0.2]，[accelerationinitshort=0.02]，[accelerationshort=0.02]，[accelerationmaxshort=0.2]）

抛物线 SAR - 扩展（重叠研究）

输入：

```py
prices: [‘high’, ‘low’]
```

参数：

```py
startvalue: 0 offsetonreverse: 0 accelerationinitlong: 0.02
accelerationlong: 0.02 accelerationmaxlong: 0.2
accelerationinitshort: 0.02 accelerationshort: 0.02
accelerationmaxshort: 0.2
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* startvalue (0)

* offsetonreverse (0)

* accelerationinitlong (0.02)

* accelerationlong (0.02)

* accelerationmaxlong (0.2)

* accelerationinitshort (0.02)

* accelerationshort (0.02)

* accelerationmaxshort (0.2)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### SIN

SIN（[输入数组]）

矢量双曲正弦（数学转换）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线条：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### SINH

SINH（[输入数组]）

矢量双曲正弦（数学转换）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线条：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### SMA

SMA（[输入数组]，[时间周期=30]）

简单移动平均线（重叠研究）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 30
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (30)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### SQRT

SQRT（[输入数组]）

矢量平方根（数学转换）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线条：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### STDDEV

STDDEV（[输入数组]，[时间周期=5]，[nbdev=1]）

标准差（统计函数）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 5 nbdev: 1
```

输出：

```py
real
```

线条：

```py
* real
```

参数：

```py
* timeperiod (5)

* nbdev (1)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### STOCH

STOCH（[输入数组]，[fastk_period=5]，[slowk_period=3]，[slowk_matype=0]，[slowd_period=3]，[slowd_matype=0]）

随机线（动量指标）

输入：

```py
prices: [‘high’, ‘low’, ‘close’]
```

参数：

```py
fastk_period: 5 slowk_period: 3 slowk_matype: 0 slowd_period: 3
slowd_matype: 0
```

输出：

```py
slowk slowd
```

线条：

```py
* slowk

* slowd
```

参数：

```py
* fastk_period (5)

* slowk_period (3)

* slowk_matype (0)

* slowd_period (3)

* slowd_matype (0)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* slowk: - ls (–)

* slowd: - ls (–)
```

### STOCHF

STOCHF（[输入数组]，[fastk_period=5]，[fastd_period=3]，[fastd_matype=0]）

快速随机线（动量指标）

输入：

```py
prices: [‘high’, ‘low’, ‘close’]
```

参数：

```py
fastk_period: 5 fastd_period: 3 fastd_matype: 0
```

输出：

```py
fastk fastd
```

线条：

```py
* fastk

* fastd
```

参数：

```py
* fastk_period (5)

* fastd_period (3)

* fastd_matype (0)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* fastk: - ls (-)

* fastd: - ls (-)
```

### STOCHRSI

STOCHRSI（[输入数组]，[时间周期=14]，[fastk_period=5]，[fastd_period=3]，[fastd_matype=0]）

随机相对强度指数（动量指标）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 14 fastk_period: 5 fastd_period: 3 fastd_matype: 0
```

输出：

```py
fastk fastd
```

线条：

```py
* fastk

* fastd
```

参数：

```py
* timeperiod (14)

* fastk_period (5)

* fastd_period (3)

* fastd_matype (0)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* fastk: - ls (-)

* fastd: - ls (-)
```

### SUB

SUB([input_arrays])

向量算术减法（数学运算符）

输入：

```py
price0: (any ndarray) price1: (any ndarray)
```

输出：

```py
real
```

线：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### SUM

SUM([input_arrays], [timeperiod=30])

求和（数学运算符）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 30
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (30)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

T3

T3([input_arrays], [timeperiod=5], [vfactor=0.7])

三重指数移动平均线（T3）（重叠研究）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 5 vfactor: 0.7
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (5)

* vfactor (0.7)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### TAN

TAN([input_arrays])

向量三角函数正切（数学变换）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### TANH

TANH([input_arrays])

向量三角函数正切（数学变换）

输入：

```py
price: (any ndarray)
```

输出：

```py
real
```

线：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### TEMA

TEMA([input_arrays], [timeperiod=30])

三重指数移动平均线（重叠研究）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 30
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (30)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### TRANGE

TRANGE([input_arrays])

真实波幅（波动性指标）

输入：

```py
prices: [‘high’, ‘low’, ‘close’]
```

输出：

```py
real
```

线：

```py
* real
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### TRIMA

TRIMA([input_arrays], [timeperiod=30])

三角形移动平均线（重叠研究）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 30
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (30)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### TRIX

TRIX([input_arrays], [timeperiod=30])

一天的三重平滑指数移动平均线（ROC）（动量指标）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 30
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (30)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### TSF

TSF([input_arrays], [timeperiod=14])

时间序列预测（统计函数）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### TYPPRICE

TYPPRICE([input_arrays])

典型价格（价格转换）

输入：

```py
prices: [‘high’, ‘low’, ‘close’]
```

输出：

```py
real
```

线：

```py
* real
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### ULTOSC

ULTOSC([input_arrays], [timeperiod1=7], [timeperiod2=14], [timeperiod3=28])

终极振荡器（动量指标）

输入：

```py
prices: [‘high’, ‘low’, ‘close’]
```

参数：

```py
timeperiod1: 7 timeperiod2: 14 timeperiod3: 28
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod1 (7)

* timeperiod2 (14)

* timeperiod3 (28)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### VAR

VAR([input_arrays], [timeperiod=5], [nbdev=1])

方差（统计函数）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 5 nbdev: 1
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (5)

* nbdev (1)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### WCLPRICE

WCLPRICE([input_arrays])

加权收盘价（价格转换）

输入：

```py
prices: [‘high’, ‘low’, ‘close’]
```

输出：

```py
real
```

线：

```py
* real
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### WILLR

WILLR([input_arrays], [timeperiod=14])

威廉指标（动量指标）

输入：

```py
prices: [‘high’, ‘low’, ‘close’]
```

参数：

```py
timeperiod: 14
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (14)
```

绘图信息：

```py
* subplot (True)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```

### WMA

WMA([input_arrays], [timeperiod=30])

加权移动平均线（重叠研究）

输入：

```py
price: (any ndarray)
```

参数：

```py
timeperiod: 30
```

输出：

```py
real
```

线：

```py
* real
```

参数：

```py
* timeperiod (30)
```

绘图信息：

```py
* subplot (False)

* plot (True)

* plotskip (False)

* plotname ()

* plotforce (False)

* plotyhlines ([])

* plothlines ([])

* plotabove (False)

* plotymargin (0.0)

* plotlinelabels (False)

* plotmaster (None)

* plotyticks ([])
```

绘图线：

```py
* real: - ls (-)
```
