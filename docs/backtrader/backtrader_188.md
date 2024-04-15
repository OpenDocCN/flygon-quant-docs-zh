# 自动化 backtrader 回测。

> 原文：[`www.backtrader.com/blog/posts/2015-08-16-backtesting-with-almost-no-programming/backtesting-with-almost-no-programming/`](https://www.backtrader.com/blog/posts/2015-08-16-backtesting-with-almost-no-programming/backtesting-with-almost-no-programming/)

到目前为止，所有 backtrader 的示例和工作样本都是从头开始创建一个主要的 **Python** 模块，加载数据、策略、观察者，并准备现金和佣金方案。

*算法交易*的一个目标是交易的自动化，鉴于 bactrader 是一个用于检查交易算法的回测平台（因此是一个*算法交易*平台），自动化使用 backtrader 是一个显而易见的目标。

注意

2015 年 8 月 22 日

`bt-run.py` 中包含了对 `Analyzer` 的支持。

`backtrader` 的开发版本现在包含了 `bt-run.py` 脚本，它自动化了大多数任务，并将作为常规软件包的一部分与 `backtrader` 一起安装。

`bt-run.py` 允许最终用户：

+   指明必须加载的数据。

+   设置加载数据的格式。

+   指定数据的日期范围

+   禁用标准观察者。

+   从内置的或 Python 模块中加载一个或多个观察者（例如：回撤）

+   为经纪人设置现金和佣金方案参数（佣金、保证金、倍数）

+   启用绘图，控制图表数量和数据呈现风格。

最后：

+   加载策略（内置的或来自 Python 模块）

+   向加载的策略传递参数。

请参阅下面关于脚本的**用法***。

## 应用用户定义的策略。

让我们考虑以下策略：

+   简单地加载一个 SimpleMovingAverage（默认周期 15）

+   打印输出。

+   文件名为 mymod.py。

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import backtrader as bt
import backtrader.indicators as btind

class MyTest(bt.Strategy):
    params = (('period', 15),)

    def log(self, txt, dt=None):
        ''' Logging function fot this strategy'''
        dt = dt or self.data.datetime[0]
        if isinstance(dt, float):
            dt = bt.num2date(dt)
        print('%s, %s' % (dt.isoformat(), txt))

    def __init__(self):
        sma = btind.SMA(period=self.p.period)

    def next(self):
        ltxt = '%d, %.2f, %.2f, %.2f, %.2f, %.2f, %.2f'

        self.log(ltxt %
                 (len(self),
                  self.data.open[0], self.data.high[0],
                  self.data.low[0], self.data.close[0],
                  self.data.volume[0], self.data.openinterest[0]))
```

用通常的测试样本执行策略很容易：简单：

```py
./bt-run.py --csvformat btcsv \
            --data ../samples/data/sample/2006-day-001.txt \
            --strategy ./mymod.py
```

图表输出。

![image](img/693cb0d9584d9a4aa924666f63b1b76c.png)

控制台输出：

```py
2006-01-20T23:59:59+00:00, 15, 3593.16, 3612.37, 3550.80, 3550.80, 0.00, 0.00
2006-01-23T23:59:59+00:00, 16, 3550.24, 3550.24, 3515.07, 3544.31, 0.00, 0.00
2006-01-24T23:59:59+00:00, 17, 3544.78, 3553.16, 3526.37, 3532.68, 0.00, 0.00
2006-01-25T23:59:59+00:00, 18, 3532.72, 3578.00, 3532.72, 3578.00, 0.00, 0.00
...
...
2006-12-22T23:59:59+00:00, 252, 4109.86, 4109.86, 4072.62, 4073.50, 0.00, 0.00
2006-12-27T23:59:59+00:00, 253, 4079.70, 4134.86, 4079.70, 4134.86, 0.00, 0.00
2006-12-28T23:59:59+00:00, 254, 4137.44, 4142.06, 4125.14, 4130.66, 0.00, 0.00
2006-12-29T23:59:59+00:00, 255, 4130.12, 4142.01, 4119.94, 4119.94, 0.00, 0.00
```

同样的策略但是：

+   将参数 `period` 设置为 50。

命令行：

```py
./bt-run.py --csvformat btcsv \
            --data ../samples/data/sample/2006-day-001.txt \
            --strategy ./mymod.py \
            period 50
```

图表输出。

![image](img/a65a3088e0484b67c8bbe3942c3907e1.png)

## 使用内置策略。

`backtrader` 将逐渐包含示例（教科书）策略。随着 `bt-run.py` 脚本一起，一个标准的*简单移动平均线交叉*策略已包含在内。名称：

+   `SMA_CrossOver`

+   参数。

    +   快速（默认 10）快速移动平均的周期

    +   慢（默认 30）慢速移动平均的周期

如果快速移动平均线向上穿过快速移动平均线并且在慢速移动平均线向下穿过快速移动平均线后卖出（仅在之前已购买的情况下）。

代码。

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import backtrader as bt
import backtrader.indicators as btind

class SMA_CrossOver(bt.Strategy):

    params = (('fast', 10), ('slow', 30))

    def __init__(self):

        sma_fast = btind.SMA(period=self.p.fast)
        sma_slow = btind.SMA(period=self.p.slow)

        self.buysig = btind.CrossOver(sma_fast, sma_slow)

    def next(self):
        if self.position.size:
            if self.buysig < 0:
                self.sell()

        elif self.buysig > 0:
            self.buy()
```

标准执行：

```py
./bt-run.py --csvformat btcsv \
            --data ../samples/data/sample/2006-day-001.txt \
            --strategy :SMA_CrossOver
```

注意‘:’。加载策略的标准表示法（见下文）是：

+   模块：策略。

遵循以下规则：

+   如果模块存在且指定了策略，则将使用该策略。

+   如果模块存在但未指定策略，则将返回模块中找到的第一个策略。

+   如果未指定模块，则假定“strategy”是指 `backtrader` 包中的策略

后者是我们的情况。

输出。

![image](img/1a545394a8a1243eb800fd080611025a.png)

最后一个示例添加佣金方案、现金并更改参数：

```py
./bt-run.py --csvformat btcsv \
            --data ../samples/data/sample/2006-day-001.txt \
            --cash 20000 \
            --commission 2.0 \
            --mult 10 \
            --margin 2000 \
            --strategy :SMA_CrossOver \
            fast 5 slow 20
```

输出。

![image](img/c53f9a28e3a40efa6d33fb0be4a89d7d.png)

我们已经对策略进行了回测：

+   更改移动平均周期

+   设置新的起始资金

+   为期货类工具设置佣金方案

    查看每根柱子中现金的连续变化，因为现金会根据期货类工具的每日变动进行调整。

## 添加分析器

注意

添加了分析器示例

`bt-run.py`还支持使用与策略相同的语法添加`Analyzers`来选择内部/外部分析器。

以`SharpeRatio`分析 2005-2006 年为例：

```py
./bt-run.py --csvformat btcsv \
            --data ../samples/data/sample/2005-2006-day-001.txt \
            --strategy :SMA_CrossOver \
            --analyzer :SharpeRatio
```

输出：

```py
====================
== Analyzers
====================
##  sharperatio
--  sharperatio : 11.6473326097
```

良好的策略！！！（实际示例中纯粹是运气，而且也没有佣金）

图表（仅显示分析器不在图表中，因为分析器无法绘制，它们不是线对象）

![image](img/8740716b754260baf410806aa63edd1a.png)

## 脚本的用法

直接从脚本中：

```py
$ ./bt-run.py --help
usage: bt-run.py [-h] --data DATA
                 [--csvformat {yahoocsv_unreversed,vchart,sierracsv,yahoocsv,vchartcsv,btcsv}]
                 [--fromdate FROMDATE] [--todate TODATE] --strategy STRATEGY
                 [--nostdstats] [--observer OBSERVERS] [--analyzer ANALYZERS]
                 [--cash CASH] [--commission COMMISSION] [--margin MARGIN]
                 [--mult MULT] [--noplot] [--plotstyle {bar,line,candle}]
                 [--plotfigs PLOTFIGS]
                 ...

Backtrader Run Script

positional arguments:
  args                  args to pass to the loaded strategy

optional arguments:
  -h, --help            show this help message and exit

Data options:
  --data DATA, -d DATA  Data files to be added to the system
  --csvformat {yahoocsv_unreversed,vchart,sierracsv,yahoocsv,vchartcsv,btcsv}, -c {yahoocsv_unreversed,vchart,sierracsv,yahoocsv,vchartcsv,btcsv}
                        CSV Format
  --fromdate FROMDATE, -f FROMDATE
                        Starting date in YYYY-MM-DD[THH:MM:SS] format
  --todate TODATE, -t TODATE
                        Ending date in YYYY-MM-DD[THH:MM:SS] format

Strategy options:
  --strategy STRATEGY, -st STRATEGY
                        Module and strategy to load with format
                        module_path:strategy_name. module_path:strategy_name
                        will load strategy_name from the given module_path
                        module_path will load the module and return the first
                        available strategy in the module :strategy_name will
                        load the given strategy from the set of built-in
                        strategies

Observers and statistics:
  --nostdstats          Disable the standard statistics observers
  --observer OBSERVERS, -ob OBSERVERS
                        This option can be specified multiple times Module and
                        observer to load with format
                        module_path:observer_name. module_path:observer_name
                        will load observer_name from the given module_path
                        module_path will load the module and return all
                        available observers in the module :observer_name will
                        load the given strategy from the set of built-in
                        strategies

Analyzers:
  --analyzer ANALYZERS, -an ANALYZERS
                        This option can be specified multiple times Module and
                        analyzer to load with format
                        module_path:analzyer_name. module_path:analyzer_name
                        will load observer_name from the given module_path
                        module_path will load the module and return all
                        available analyzers in the module :anaylzer_name will
                        load the given strategy from the set of built-in
                        strategies

Cash and Commission Scheme Args:
  --cash CASH, -cash CASH
                        Cash to set to the broker
  --commission COMMISSION, -comm COMMISSION
                        Commission value to set
  --margin MARGIN, -marg MARGIN
                        Margin type to set
  --mult MULT, -mul MULT
                        Multiplier to use

Plotting options:
  --noplot, -np         Do not plot the read data
  --plotstyle {bar,line,candle}, -ps {bar,line,candle}
                        Plot style for the input data
  --plotfigs PLOTFIGS, -pn PLOTFIGS
                        Plot using n figures
```

以及代码：

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
import datetime
import inspect
import itertools
import random
import string
import sys

import backtrader as bt
import backtrader.feeds as btfeeds
import backtrader.indicators as btinds
import backtrader.observers as btobs
import backtrader.strategies as btstrats
import backtrader.analyzers as btanalyzers

DATAFORMATS = dict(
    btcsv=btfeeds.BacktraderCSVData,
    vchartcsv=btfeeds.VChartCSVData,
    vchart=btfeeds.VChartData,
    sierracsv=btfeeds.SierraChartCSVData,
    yahoocsv=btfeeds.YahooFinanceCSVData,
    yahoocsv_unreversed=btfeeds.YahooFinanceCSVData
)

def runstrat():
    args = parse_args()

    stdstats = not args.nostdstats

    cerebro = bt.Cerebro(stdstats=stdstats)

    for data in getdatas(args):
        cerebro.adddata(data)

    # Prepare a dictionary of extra args passed to push them to the strategy

    # pack them in pairs
    packedargs = itertools.izip_longest(*[iter(args.args)] * 2, fillvalue='')

    # prepare a string for evaluation, eval and store the result
    evalargs = 'dict('
    for key, value in packedargs:
        evalargs += key + '=' + value + ','
    evalargs += ')'
    stratkwargs = eval(evalargs)

    # Get the strategy and add it with any arguments
    strat = getstrategy(args)
    cerebro.addstrategy(strat, **stratkwargs)

    obs = getobservers(args)
    for ob in obs:
        cerebro.addobserver(ob)

    ans = getanalyzers(args)
    for an in ans:
        cerebro.addanalyzer(an)

    setbroker(args, cerebro)

    runsts = cerebro.run()
    runst = runsts[0]  # single strategy and no optimization

    if runst.analyzers:
        print('====================')
        print('== Analyzers')
        print('====================')
        for name, analyzer in runst.analyzers.getitems():
            print('## ', name)
            analysis = analyzer.get_analysis()
            for key, val in analysis.items():
                print('-- ', key, ':', val)

    if not args.noplot:
        cerebro.plot(numfigs=args.plotfigs, style=args.plotstyle)

def setbroker(args, cerebro):
    broker = cerebro.getbroker()

    if args.cash is not None:
        broker.setcash(args.cash)

    commkwargs = dict()
    if args.commission is not None:
        commkwargs['commission'] = args.commission
    if args.margin is not None:
        commkwargs['margin'] = args.margin
    if args.mult is not None:
        commkwargs['mult'] = args.mult

    if commkwargs:
        broker.setcommission(**commkwargs)

def getdatas(args):
    # Get the data feed class from the global dictionary
    dfcls = DATAFORMATS[args.csvformat]

    # Prepare some args
    dfkwargs = dict()
    if args.csvformat == 'yahoo_unreversed':
        dfkwargs['reverse'] = True

    fmtstr = '%Y-%m-%d'
    if args.fromdate:
        dtsplit = args.fromdate.split('T')
        if len(dtsplit) > 1:
            fmtstr += 'T%H:%M:%S'

        fromdate = datetime.datetime.strptime(args.fromdate, fmtstr)
        dfkwargs['fromdate'] = fromdate

    fmtstr = '%Y-%m-%d'
    if args.todate:
        dtsplit = args.todate.split('T')
        if len(dtsplit) > 1:
            fmtstr += 'T%H:%M:%S'
        todate = datetime.datetime.strptime(args.todate, fmtstr)
        dfkwargs['todate'] = todate

    datas = list()
    for dname in args.data:
        dfkwargs['dataname'] = dname
        data = dfcls(**dfkwargs)
        datas.append(data)

    return datas

def getmodclasses(mod, clstype, clsname=None):
    clsmembers = inspect.getmembers(mod, inspect.isclass)

    clslist = list()
    for name, cls in clsmembers:
        if not issubclass(cls, clstype):
            continue

        if clsname:
            if clsname == name:
                clslist.append(cls)
                break
        else:
            clslist.append(cls)

    return clslist

def loadmodule(modpath, modname=''):
    # generate a random name for the module
    if not modname:
        chars = string.ascii_uppercase + string.digits
        modname = ''.join(random.choice(chars) for _ in range(10))

    version = (sys.version_info[0], sys.version_info[1])

    if version < (3, 3):
        mod, e = loadmodule2(modpath, modname)
    else:
        mod, e = loadmodule3(modpath, modname)

    return mod, e

def loadmodule2(modpath, modname):
    import imp

    try:
        mod = imp.load_source(modname, modpath)
    except Exception, e:
        return (None, e)

    return (mod, None)

def loadmodule3(modpath, modname):
    import importlib.machinery

    try:
        loader = importlib.machinery.SourceFileLoader(modname, modpath)
        mod = loader.load_module()
    except Exception, e:
        return (None, e)

    return (mod, None)

def getstrategy(args):
    sttokens = args.strategy.split(':')

    if len(sttokens) == 1:
        modpath = sttokens[0]
        stname = None
    else:
        modpath, stname = sttokens

    if modpath:
        mod, e = loadmodule(modpath)

        if not mod:
            print('')
            print('Failed to load module %s:' % modpath, e)
            sys.exit(1)
    else:
        mod = btstrats

    strats = getmodclasses(mod=mod, clstype=bt.Strategy, clsname=stname)

    if not strats:
        print('No strategy %s / module %s' % (str(stname), modpath))
        sys.exit(1)

    return strats[0]

def getanalyzers(args):
    analyzers = list()
    for anspec in args.analyzers or []:

        tokens = anspec.split(':')

        if len(tokens) == 1:
            modpath = tokens[0]
            name = None
        else:
            modpath, name = tokens

        if modpath:
            mod, e = loadmodule(modpath)

            if not mod:
                print('')
                print('Failed to load module %s:' % modpath, e)
                sys.exit(1)
        else:
            mod = btanalyzers

        loaded = getmodclasses(mod=mod, clstype=bt.Analyzer, clsname=name)

        if not loaded:
            print('No analyzer %s / module %s' % ((str(name), modpath)))
            sys.exit(1)

        analyzers.extend(loaded)

    return analyzers

def getobservers(args):
    observers = list()
    for obspec in args.observers or []:

        tokens = obspec.split(':')

        if len(tokens) == 1:
            modpath = tokens[0]
            name = None
        else:
            modpath, name = tokens

        if modpath:
            mod, e = loadmodule(modpath)

            if not mod:
                print('')
                print('Failed to load module %s:' % modpath, e)
                sys.exit(1)
        else:
            mod = btobs

        loaded = getmodclasses(mod=mod, clstype=bt.Observer, clsname=name)

        if not loaded:
            print('No observer %s / module %s' % ((str(name), modpath)))
            sys.exit(1)

        observers.extend(loaded)

    return observers

def parse_args():
    parser = argparse.ArgumentParser(
        description='Backtrader Run Script')

    group = parser.add_argument_group(title='Data options')
    # Data options
    group.add_argument('--data', '-d', action='append', required=True,
                       help='Data files to be added to the system')

    datakeys = list(DATAFORMATS.keys())
    group.add_argument('--csvformat', '-c', required=False,
                       default='btcsv', choices=datakeys,
                       help='CSV Format')

    group.add_argument('--fromdate', '-f', required=False, default=None,
                       help='Starting date in YYYY-MM-DD[THH:MM:SS] format')

    group.add_argument('--todate', '-t', required=False, default=None,
                       help='Ending date in YYYY-MM-DD[THH:MM:SS] format')

    # Module where to read the strategy from
    group = parser.add_argument_group(title='Strategy options')
    group.add_argument('--strategy', '-st', required=True,
                       help=('Module and strategy to load with format '
                             'module_path:strategy_name.\n'
                             '\n'
                             'module_path:strategy_name will load '
                             'strategy_name from the given module_path\n'
                             '\n'
                             'module_path will load the module and return '
                             'the first available strategy in the module\n'
                             '\n'
                             ':strategy_name will load the given strategy '
                             'from the set of built-in strategies'))

    # Observers
    group = parser.add_argument_group(title='Observers and statistics')
    group.add_argument('--nostdstats', action='store_true',
                       help='Disable the standard statistics observers')

    group.add_argument('--observer', '-ob', dest='observers',
                       action='append', required=False,
                       help=('This option can be specified multiple times\n'
                             '\n'
                             'Module and observer to load with format '
                             'module_path:observer_name.\n'
                             '\n'
                             'module_path:observer_name will load '
                             'observer_name from the given module_path\n'
                             '\n'
                             'module_path will load the module and return '
                             'all available observers in the module\n'
                             '\n'
                             ':observer_name will load the given strategy '
                             'from the set of built-in strategies'))

    # Anaylzers
    group = parser.add_argument_group(title='Analyzers')
    group.add_argument('--analyzer', '-an', dest='analyzers',
                       action='append', required=False,
                       help=('This option can be specified multiple times\n'
                             '\n'
                             'Module and analyzer to load with format '
                             'module_path:analzyer_name.\n'
                             '\n'
                             'module_path:analyzer_name will load '
                             'observer_name from the given module_path\n'
                             '\n'
                             'module_path will load the module and return '
                             'all available analyzers in the module\n'
                             '\n'
                             ':anaylzer_name will load the given strategy '
                             'from the set of built-in strategies'))

    # Broker/Commissions
    group = parser.add_argument_group(title='Cash and Commission Scheme Args')
    group.add_argument('--cash', '-cash', required=False, type=float,
                       help='Cash to set to the broker')
    group.add_argument('--commission', '-comm', required=False, type=float,
                       help='Commission value to set')
    group.add_argument('--margin', '-marg', required=False, type=float,
                       help='Margin type to set')

    group.add_argument('--mult', '-mul', required=False, type=float,
                       help='Multiplier to use')

    # Plot options
    group = parser.add_argument_group(title='Plotting options')
    group.add_argument('--noplot', '-np', action='store_true', required=False,
                       help='Do not plot the read data')

    group.add_argument('--plotstyle', '-ps', required=False, default='bar',
                       choices=['bar', 'line', 'candle'],
                       help='Plot style for the input data')

    group.add_argument('--plotfigs', '-pn', required=False, default=1,
                       type=int, help='Plot using n figures')

    # Extra arguments
    parser.add_argument('args', nargs=argparse.REMAINDER,
                        help='args to pass to the loaded strategy')

    return parser.parse_args()

if __name__ == '__main__':
    runstrat()
```
