# TA-Lib

> 原文：[`www.backtrader.com/docu/talib/talib/`](https://www.backtrader.com/docu/talib/talib/)

即使*backtrader*提供了大量内置指标，而且开发指标主要是定义输入、输出并以自然方式编写公式，一些人还是想要使用*TA-LIB*。一些原因包括：

+   指标*X*在库中而不在*backtrader*中（作者将乐意接受请求）

+   *TA-LIB*行为是众所周知的，人们信任老牌产品

为了满足各种口味，提供了*TA-LIB*集成。

## 要求

+   [用于 TA-Lib 的 Python 包装器](https://github.com/mrjbq7/ta-lib)

+   它所需的任何依赖项（例如*numpy*）

安装细节在*GitHub*仓库上。

## 使用*ta-lib*

与*backtrader*中已内置的任何指标使用起来一样容易。例如一个*简单移动平均线*。首先是*backtrader*的一个：

```py
import backtrader as bt

class MyStrategy(bt.Strategy):
    params = (('period', 20),)

    def __init__(self):
        self.sma = bt.indicators.SMA(self.data, period=self.p.period)
        ...

...
```

现在是*ta-lib*示例：

```py
import backtrader as bt

class MyStrategy(bt.Strategy):
    params = (('period', 20),)

    def __init__(self):
        self.sma = bt.talib.SMA(self.data, timeperiod=self.p.period)
        ...

...
```

Et voilá！当然，*ta-lib*指标的*params*由库本身定义，而不是由*backtrader*定义。在这种情况下，*ta-lib*中的*SMA*接受一个名为`timeperiod`的参数来定义操作窗口的大小。

对于需要多个输入的指标，例如*随机指标*：

```py
import backtrader as bt

class MyStrategy(bt.Strategy):
    params = (('period', 20),)

    def __init__(self):
        self.stoc = bt.talib.STOCH(self.data.high, self.data.low, self.data.close,
                                   fastk_period=14, slowk_period=3, slowd_period=3)

        ...

...
```

注意`high`、`low`和`close`已经分别传递。可以随时传递`open`而不是`low`（或任何其他数据系列）进行实验。

*ta-lib*指标文档已自动解析并添加到*backtrader*文档中。您也可以查看*ta-lib*源代码/文档。或者额外执行以下操作：

```py
print(bt.talib.SMA.__doc__)
```

这种情况下的输出是：

```py
SMA([input_arrays], [timeperiod=30])

Simple Moving Average (Overlap Studies)

Inputs:
    price: (any ndarray)
Parameters:
    timeperiod: 30
Outputs:
    real
```

这提供了一些信息：

+   预期的是哪个*输入*（忽略``ndarray``注释，因为 backtrader 在后台管理转换）

+   哪些*参数*和哪些默认值

+   指标实际提供了哪些*线*

### 移动平均线和 MA_Type。

为了为像`bt.talib.STOCH`这样的指标选择特定的*移动平均线*，标准的*ta-lib* `MA_Type`可以通过`backtrader.talib.MA_Type`访问。例如：

```py
import backtrader as bt
print('SMA:', bt.talib.MA_Type.SMA)
print('T3:', bt.talib.MA_Type.T3)
```

## 绘制 ta-lib 指标

与常规用法一样，绘制*ta-lib*指标无需任何特殊操作。

注意

输出为*CANDLE*（所有寻找蜡烛图案的指标）的指标提供二进制输出：0 或 100。为了避免向图表添加`subplot`，自动绘图转换为在识别出该模式的时间点上在*数据*上绘制它们。

## 示例和比较

以下是比较一些*ta-lib*指标的输出与*backtrader*中等效内置指标的图表。需要考虑的是：

+   *ta-lib*指标在绘图时带有`TA_`前缀。这是由示例特别完成的，以帮助用户区分它们。

+   *移动平均线*（如果两者产生相同的结果）将被绘制在现有的*移动平均线*之上。这两个指标不能分开观察，如果是这样，测试就通过了。

+   所有样本都包括一个`CDLDOJI`指标作为参考

### KAMA（Kaufman 移动平均）

这是第一个示例，因为它是唯一一个（从所有直接比较的指标中）具有差异的示例：

+   样本的初始值不同

+   在某个时间点，两种*KAMA*实现会收敛并具有相同的行为。

在分析了*ta-lib*源代码之后：

+   *ta-lib*中的实现对*KAMA*的第一个值进行了非行业标准的选择。

    可以从源代码中看到选择（引用源代码）：*这里使用昨天的价格作为前一天的 KAMA。*

*backtrader*做出了通常的选择，与*Stockcharts*的选择相同：

+   [StockCharts 上的 KAMA](http://stockcharts.com/school/doku.php?id=chart_school:technical_indicators:kaufman_s_adaptive_moving_average)

    *由于我们需要一个初始值来开始计算，因此第一个 KAMA 只是一个简单的移动平均*

因此有所不同。此外：

+   *ta-lib*的`KAMA`实现不允许指定用于调整*Kaufman*定义的*可调节常数*的`fast`和`slow`周期。

示例执行：

```py
$ ./talibtest.py --plot --ind kama
```

输出

![image](img/96adf12f470193982b80cb0e4b04f936.png)

### SMA

```py
$ ./talibtest.py --plot --ind sma
```

输出

![image](img/854a2174af3a979739fc533d27e6303f.png)

### EMA

```py
$ ./talibtest.py --plot --ind ema
```

输出

![image](img/bb565d4ebf80767c1845e14855425dba.png)

### 随机指标

```py
$ ./talibtest.py --plot --ind stoc
```

输出

![image](img/4cbafdc7fc9c487af417443d61eb6a7b.png)

### RSI

```py
$ ./talibtest.py --plot --ind rsi
```

输出

![image](img/c2de2e76ef142baa81dd8013275e619d.png)

### MACD

```py
$ ./talibtest.py --plot --ind macd
```

输出

![image](img/cd9c636ab704454de629be07e4916303.png)

### 布林带

```py
$ ./talibtest.py --plot --ind bollinger
```

输出

![image](img/3873648e820de0c0fdd15ee7015a1500.png)

### AROON

请注意，*ta-lib*选择先放*down*线，并且颜色与*backtrader*内置指标相比是反转的。

```py
$ ./talibtest.py --plot --ind aroon
```

输出

![image](img/37f6242b93bafec219a22f1b43cdf523.png)

### 终极波动指标

```py
$ ./talibtest.py --plot --ind ultimate
```

输出

![image](img/4417f633399efe2e57e8bbe262242074.png)

### Trix

```py
$ ./talibtest.py --plot --ind trix
```

输出

![image](img/f272a8ba189616dee14e4ee7d9819b19.png)

### ADXR

这里*backtrader*提供了`ADX`和`ADXR`线。

```py
$ ./talibtest.py --plot --ind adxr
```

输出

![image](img/418b94a30afcba6da96af98c78127b48.png)

### DEMA

```py
$ ./talibtest.py --plot --ind dema
```

输出

![image](img/0ffed62784c7804e19c61010bf3bba27.png)

### TEMA

```py
$ ./talibtest.py --plot --ind tema
```

输出

![image](img/e60b1389d6b46d952c2dbbaf589ee7d0.png)

### PPO

这里*backtrader*不仅提供`ppo`线，还提供了更传统的`macd`方法。

```py
$ ./talibtest.py --plot --ind ppo
```

输出

![image](img/f6e2a0829ea03025af80f61183a12524.png)

### WilliamsR

```py
$ ./talibtest.py --plot --ind williamsr
```

输出

![image](img/d8e9f717b4eaf1de2c3c4c27dfb37f13.png)

### ROC

所有指标显示的形状完全相同，但如何跟踪*动量*或*变化率*有几种定义。

```py
$ ./talibtest.py --plot --ind roc
```

输出

![image](img/43b16c19fb1a12c47be66f17b61b3a5e.png)

## 示例用法

```py
$ ./talibtest.py --help
usage: talibtest.py [-h] [--data0 DATA0] [--fromdate FROMDATE]
                    [--todate TODATE]
                    [--ind {sma,ema,stoc,rsi,macd,bollinger,aroon,ultimate,trix,kama,adxr,dema,tema,ppo,williamsr,roc}]
                    [--no-doji] [--use-next] [--plot [kwargs]]

Sample for ta-lib

optional arguments:
  -h, --help            show this help message and exit
  --data0 DATA0         Data to be read in (default:
                        ../../datas/yhoo-1996-2015.txt)
  --fromdate FROMDATE   Starting date in YYYY-MM-DD format (default:
                        2005-01-01)
  --todate TODATE       Ending date in YYYY-MM-DD format (default: 2006-12-31)
  --ind {sma,ema,stoc,rsi,macd,bollinger,aroon,ultimate,trix,kama,adxr,dema,tema,ppo,williamsr,roc}
                        Which indicator pair to show together (default: sma)
  --no-doji             Remove Doji CandleStick pattern checker (default:
                        False)
  --use-next            Use next (step by step) instead of once (batch)
                        (default: False)
  --plot [kwargs], -p [kwargs]
                        Plot the read data applying any kwargs passed For
                        example (escape the quotes if needed): --plot
                        style="candle" (to plot candles) (default: None)
```

## 示例代码

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
import datetime

import backtrader as bt

class TALibStrategy(bt.Strategy):
    params = (('ind', 'sma'), ('doji', True),)

    INDS = ['sma', 'ema', 'stoc', 'rsi', 'macd', 'bollinger', 'aroon',
            'ultimate', 'trix', 'kama', 'adxr', 'dema', 'ppo', 'tema',
            'roc', 'williamsr']

    def __init__(self):
        if self.p.doji:
            bt.talib.CDLDOJI(self.data.open, self.data.high,
                             self.data.low, self.data.close)

        if self.p.ind == 'sma':
            bt.talib.SMA(self.data.close, timeperiod=25, plotname='TA_SMA')
            bt.indicators.SMA(self.data, period=25)
        elif self.p.ind == 'ema':
            bt.talib.EMA(timeperiod=25, plotname='TA_SMA')
            bt.indicators.EMA(period=25)
        elif self.p.ind == 'stoc':
            bt.talib.STOCH(self.data.high, self.data.low, self.data.close,
                           fastk_period=14, slowk_period=3, slowd_period=3,
                           plotname='TA_STOCH')

            bt.indicators.Stochastic(self.data)

        elif self.p.ind == 'macd':
            bt.talib.MACD(self.data, plotname='TA_MACD')
            bt.indicators.MACD(self.data)
            bt.indicators.MACDHisto(self.data)
        elif self.p.ind == 'bollinger':
            bt.talib.BBANDS(self.data, timeperiod=25,
                            plotname='TA_BBANDS')
            bt.indicators.BollingerBands(self.data, period=25)

        elif self.p.ind == 'rsi':
            bt.talib.RSI(self.data, plotname='TA_RSI')
            bt.indicators.RSI(self.data)

        elif self.p.ind == 'aroon':
            bt.talib.AROON(self.data.high, self.data.low, plotname='TA_AROON')
            bt.indicators.AroonIndicator(self.data)

        elif self.p.ind == 'ultimate':
            bt.talib.ULTOSC(self.data.high, self.data.low, self.data.close,
                            plotname='TA_ULTOSC')
            bt.indicators.UltimateOscillator(self.data)

        elif self.p.ind == 'trix':
            bt.talib.TRIX(self.data, timeperiod=25,  plotname='TA_TRIX')
            bt.indicators.Trix(self.data, period=25)

        elif self.p.ind == 'adxr':
            bt.talib.ADXR(self.data.high, self.data.low, self.data.close,
                          plotname='TA_ADXR')
            bt.indicators.ADXR(self.data)

        elif self.p.ind == 'kama':
            bt.talib.KAMA(self.data, timeperiod=25, plotname='TA_KAMA')
            bt.indicators.KAMA(self.data, period=25)

        elif self.p.ind == 'dema':
            bt.talib.DEMA(self.data, timeperiod=25, plotname='TA_DEMA')
            bt.indicators.DEMA(self.data, period=25)

        elif self.p.ind == 'ppo':
            bt.talib.PPO(self.data, plotname='TA_PPO')
            bt.indicators.PPO(self.data, _movav=bt.indicators.SMA)

        elif self.p.ind == 'tema':
            bt.talib.TEMA(self.data, timeperiod=25, plotname='TA_TEMA')
            bt.indicators.TEMA(self.data, period=25)

        elif self.p.ind == 'roc':
            bt.talib.ROC(self.data, timeperiod=12, plotname='TA_ROC')
            bt.talib.ROCP(self.data, timeperiod=12, plotname='TA_ROCP')
            bt.talib.ROCR(self.data, timeperiod=12, plotname='TA_ROCR')
            bt.talib.ROCR100(self.data, timeperiod=12, plotname='TA_ROCR100')
            bt.indicators.ROC(self.data, period=12)
            bt.indicators.Momentum(self.data, period=12)
            bt.indicators.MomentumOscillator(self.data, period=12)

        elif self.p.ind == 'williamsr':
            bt.talib.WILLR(self.data.high, self.data.low, self.data.close,
                           plotname='TA_WILLR')
            bt.indicators.WilliamsR(self.data)

def runstrat(args=None):
    args = parse_args(args)

    cerebro = bt.Cerebro()

    dkwargs = dict()
    if args.fromdate:
        fromdate = datetime.datetime.strptime(args.fromdate, '%Y-%m-%d')
        dkwargs['fromdate'] = fromdate

    if args.todate:
        todate = datetime.datetime.strptime(args.todate, '%Y-%m-%d')
        dkwargs['todate'] = todate

    data0 = bt.feeds.YahooFinanceCSVData(dataname=args.data0, **dkwargs)
    cerebro.adddata(data0)

    cerebro.addstrategy(TALibStrategy, ind=args.ind, doji=not args.no_doji)

    cerebro.run(runcone=not args.use_next, stdstats=False)
    if args.plot:
        pkwargs = dict(style='candle')
        if args.plot is not True:  # evals to True but is not True
            npkwargs = eval('dict(' + args.plot + ')')  # args were passed
            pkwargs.update(npkwargs)

        cerebro.plot(**pkwargs)

def parse_args(pargs=None):

    parser = argparse.ArgumentParser(
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
        description='Sample for sizer')

    parser.add_argument('--data0', required=False,
                        default='../../datas/yhoo-1996-2015.txt',
                        help='Data to be read in')

    parser.add_argument('--fromdate', required=False,
                        default='2005-01-01',
                        help='Starting date in YYYY-MM-DD format')

    parser.add_argument('--todate', required=False,
                        default='2006-12-31',
                        help='Ending date in YYYY-MM-DD format')

    parser.add_argument('--ind', required=False, action='store',
                        default=TALibStrategy.INDS[0],
                        choices=TALibStrategy.INDS,
                        help=('Which indicator pair to show together'))

    parser.add_argument('--no-doji', required=False, action='store_true',
                        help=('Remove Doji CandleStick pattern checker'))

    parser.add_argument('--use-next', required=False, action='store_true',
                        help=('Use next (step by step) '
                              'instead of once (batch)'))

    # Plot options
    parser.add_argument('--plot', '-p', nargs='?', required=False,
                        metavar='kwargs', const=True,
                        help=('Plot the read data applying any kwargs passed\n'
                              '\n'
                              'For example (escape the quotes if needed):\n'
                              '\n'
                              '  --plot style="candle" (to plot candles)\n'))

    if pargs is not None:
        return parser.parse_args(pargs)

    return parser.parse_args()

if __name__ == '__main__':
    runstrat()
```
