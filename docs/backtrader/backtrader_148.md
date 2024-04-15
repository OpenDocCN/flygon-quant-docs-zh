# 优化改进

> 原文：[`www.backtrader.com/blog/posts/2016-09-05-optimization-improvements/optimization-improvements/`](https://www.backtrader.com/blog/posts/2016-09-05-optimization-improvements/optimization-improvements/)

*backtrader*的版本 1.8.12.99 包括了在多进程中管理*数据源*和*结果*的改进。

注意

两者的行为已经确定

这些选项的行为可以通过两个新的*Cerebro*参数来控制：

+   `optdatas`（默认：`True`）

    如果为`True`且正在优化（系统可以`preload`和使用`runonce`），数据预加载将仅在主进程中执行一次，以节省时间和资源。

+   `optreturn`（默认：`True`）

    如果为`True`，优化结果将不是完整的`Strategy`对象（以及所有*数据*、*指标*、*观察者*...），而是具有以下属性的对象（与`Strategy`中相同）：

    +   `params`（或`p`）策略执行时的参数

    +   策略执行的`analyzers`

    在大多数情况下，只需要*analyzers*和使用哪些*params*来评估策略的表现。如果需要对生成的值（例如*指标*）进行详细分析，请关闭此选项

## 数据源管理

在*优化*场景中，这是*Cerebro*参数的一个可能组合：

+   `preload=True`（默认）

    在运行任何回测代码之前，数据源将被预加载

+   `runonce=True`（默认）

    *指标*将在批处理模式下通过紧密的*for*循环计算，而不是逐步进行。

如果两个条件都为`True`且`optdatas=True`，那么：

+   在生成新的子进程（负责执行*回测*的进程）之前，*数据源*将在主进程中预加载

## 结果管理

在*优化*场景中，评估每个*策略*运行时使用的不同*参数*时，两件事情应该起到最重要的作用：

+   `strategy.params`（或`strategy.p`）

    用于回测的实际值集

+   `strategy.analyzers`

    负责提供*策略*实际表现评估的对象。例如：

    `SharpeRatio_A`（年化*夏普比率*）

当`optreturn=True`时，将创建占位符对象，而不是返回完整的*策略*实例，这些对象具有上述两个属性，以便进行评估。

这样可以避免传递许多生成的数据，例如在*回测*期间指标生成的值

如果希望获得*完整的策略对象*，只需在 cerebro *实例化*期间或进行`cerebro.run`时将`optreturn=False`设置即可。

## 一些测试运行

*backtrader*源代码中的*优化*示例已经扩展，以添加对`optdatas`和`optreturn`的控制（实际上是禁用它们）

### 单核运行

作为参考，当 CPU 数量限制为`1`且未使用`multiprocessing`模块时会发生什么：

```py
$ ./optimization.py --maxcpus 1
==================================================
**************************************************
--------------------------------------------------
OrderedDict([(u'smaperiod', 10), (u'macdperiod1', 12), (u'macdperiod2', 26), (u'macdperiod3', 9)])
**************************************************
--------------------------------------------------
OrderedDict([(u'smaperiod', 10), (u'macdperiod1', 13), (u'macdperiod2', 26), (u'macdperiod3', 9)])
...
...
OrderedDict([(u'smaperiod', 29), (u'macdperiod1', 19), (u'macdperiod2', 29), (u'macdperiod3', 14)])
==================================================
Time used: 184.922727833
```

### 多核运行

在不限制 CPU 数量的情况下，Python 的`multiprocessing`模块将尝试使用所有 CPU。`optdatas` 和 `optreturn` 将被禁用

#### `optdata` 和 `optreturn` 都处于激活状态

默认行为：

```py
$ ./optimization.py
...
...
...
==================================================
Time used: 56.5889185394
```

通过多核和*数据源*和*结果*的改进，总体上从 `184.92` 秒降低到 `56.58` 秒。

请注意，示例中使用了`252`个条形和指标只生成长度为`252`点的值。这只是一个例子。

真正的问题是有多少是归因于新行为。

#### `optreturn` 处于未激活状态

让我们将完整的*策略*对象传递给调用者：

```py
$ ./optimization.py --no-optreturn
...
...
...
==================================================
Time used: 67.056914007
```

执行时间增加了 `18.50%`（或速度提升了 `15.62%`）。

#### `optdatas` 处于未激活状态

每个子进程都被强制加载自己的*数据源*集合：

```py
$ ./optimization.py --no-optdatas
...
...
...
==================================================
Time used: 72.7238112637
```

执行时间增加了 `28.52%`（或速度提升了 `22.19%`）。

#### 两者都处于未激活状态

仍然使用多核但是旧的非改进行为：

```py
$ ./optimization.py --no-optdatas --no-optreturn
...
...
...
==================================================
Time used: 83.6246643786
```

执行时间增加了 `47.79%`（或速度提升了 `32.34%`）。

这表明使用多核是时间提升的主要贡献因素。

注意

这些执行是在一台配备有 `i7-4710HQ`（4 核 / 8 逻辑）和 16 GBytes 内存的笔记本电脑上在 Windows 10 64 位系统下完成的。在其他条件下，里程可能有所不同。

## 总结

+   在优化过程中减少时间的最大因素是多核的使用

+   示例运行了带有 `optdatas` 和 `optreturn` 的显示速度提升约为 `22.19%` 和 `15.62%` （在测试中两者一起提升了 `32.34%`）。

## 示例用法

```py
$ ./optimization.py --help
usage: optimization.py [-h] [--data DATA] [--fromdate FROMDATE]
                       [--todate TODATE] [--maxcpus MAXCPUS] [--no-runonce]
                       [--exactbars EXACTBARS] [--no-optdatas]
                       [--no-optreturn] [--ma_low MA_LOW] [--ma_high MA_HIGH]
                       [--m1_low M1_LOW] [--m1_high M1_HIGH] [--m2_low M2_LOW]
                       [--m2_high M2_HIGH] [--m3_low M3_LOW]
                       [--m3_high M3_HIGH]

Optimization

optional arguments:
  -h, --help            show this help message and exit
  --data DATA, -d DATA  data to add to the system
  --fromdate FROMDATE, -f FROMDATE
                        Starting date in YYYY-MM-DD format
  --todate TODATE, -t TODATE
                        Starting date in YYYY-MM-DD format
  --maxcpus MAXCPUS, -m MAXCPUS
                        Number of CPUs to use in the optimization
                          - 0 (default): use all available CPUs
                          - 1 -> n: use as many as specified
  --no-runonce          Run in next mode
  --exactbars EXACTBARS
                        Use the specified exactbars still compatible with preload
                          0 No memory savings
                          -1 Moderate memory savings
                          -2 Less moderate memory savings
  --no-optdatas         Do not optimize data preloading in optimization
  --no-optreturn        Do not optimize the returned values to save time
  --ma_low MA_LOW       SMA range low to optimize
  --ma_high MA_HIGH     SMA range high to optimize
  --m1_low M1_LOW       MACD Fast MA range low to optimize
  --m1_high M1_HIGH     MACD Fast MA range high to optimize
  --m2_low M2_LOW       MACD Slow MA range low to optimize
  --m2_high M2_HIGH     MACD Slow MA range high to optimize
  --m3_low M3_LOW       MACD Signal range low to optimize
  --m3_high M3_HIGH     MACD Signal range high to optimize
```

## 示例代码

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
import datetime
import time

from backtrader.utils.py3 import range

import backtrader as bt
import backtrader.indicators as btind
import backtrader.feeds as btfeeds

class OptimizeStrategy(bt.Strategy):
    params = (('smaperiod', 15),
              ('macdperiod1', 12),
              ('macdperiod2', 26),
              ('macdperiod3', 9),
              )

    def __init__(self):
        # Add indicators to add load

        btind.SMA(period=self.p.smaperiod)
        btind.MACD(period_me1=self.p.macdperiod1,
                   period_me2=self.p.macdperiod2,
                   period_signal=self.p.macdperiod3)

def runstrat():
    args = parse_args()

    # Create a cerebro entity
    cerebro = bt.Cerebro(maxcpus=args.maxcpus,
                         runonce=not args.no_runonce,
                         exactbars=args.exactbars,
                         optdatas=not args.no_optdatas,
                         optreturn=not args.no_optreturn)

    # Add a strategy
    cerebro.optstrategy(
        OptimizeStrategy,
        smaperiod=range(args.ma_low, args.ma_high),
        macdperiod1=range(args.m1_low, args.m1_high),
        macdperiod2=range(args.m2_low, args.m2_high),
        macdperiod3=range(args.m3_low, args.m3_high),
    )

    # Get the dates from the args
    fromdate = datetime.datetime.strptime(args.fromdate, '%Y-%m-%d')
    todate = datetime.datetime.strptime(args.todate, '%Y-%m-%d')

    # Create the 1st data
    data = btfeeds.BacktraderCSVData(
        dataname=args.data,
        fromdate=fromdate,
        todate=todate)

    # Add the Data Feed to Cerebro
    cerebro.adddata(data)

    # clock the start of the process
    tstart = time.clock()

    # Run over everything
    stratruns = cerebro.run()

    # clock the end of the process
    tend = time.clock()

    print('==================================================')
    for stratrun in stratruns:
        print('**************************************************')
        for strat in stratrun:
            print('--------------------------------------------------')
            print(strat.p._getkwargs())
    print('==================================================')

    # print out the result
    print('Time used:', str(tend - tstart))

def parse_args():
    parser = argparse.ArgumentParser(
        description='Optimization',
        formatter_class=argparse.RawTextHelpFormatter,
    )

    parser.add_argument(
        '--data', '-d',
        default='../../datas/2006-day-001.txt',
        help='data to add to the system')

    parser.add_argument(
        '--fromdate', '-f',
        default='2006-01-01',
        help='Starting date in YYYY-MM-DD format')

    parser.add_argument(
        '--todate', '-t',
        default='2006-12-31',
        help='Starting date in YYYY-MM-DD format')

    parser.add_argument(
        '--maxcpus', '-m',
        type=int, required=False, default=0,
        help=('Number of CPUs to use in the optimization'
              '\n'
              '  - 0 (default): use all available CPUs\n'
              '  - 1 -> n: use as many as specified\n'))

    parser.add_argument(
        '--no-runonce', action='store_true', required=False,
        help='Run in next mode')

    parser.add_argument(
        '--exactbars', required=False, type=int, default=0,
        help=('Use the specified exactbars still compatible with preload\n'
              '  0 No memory savings\n'
              '  -1 Moderate memory savings\n'
              '  -2 Less moderate memory savings\n'))

    parser.add_argument(
        '--no-optdatas', action='store_true', required=False,
        help='Do not optimize data preloading in optimization')

    parser.add_argument(
        '--no-optreturn', action='store_true', required=False,
        help='Do not optimize the returned values to save time')

    parser.add_argument(
        '--ma_low', type=int,
        default=10, required=False,
        help='SMA range low to optimize')

    parser.add_argument(
        '--ma_high', type=int,
        default=30, required=False,
        help='SMA range high to optimize')

    parser.add_argument(
        '--m1_low', type=int,
        default=12, required=False,
        help='MACD Fast MA range low to optimize')

    parser.add_argument(
        '--m1_high', type=int,
        default=20, required=False,
        help='MACD Fast MA range high to optimize')

    parser.add_argument(
        '--m2_low', type=int,
        default=26, required=False,
        help='MACD Slow MA range low to optimize')

    parser.add_argument(
        '--m2_high', type=int,
        default=30, required=False,
        help='MACD Slow MA range high to optimize')

    parser.add_argument(
        '--m3_low', type=int,
        default=9, required=False,
        help='MACD Signal range low to optimize')

    parser.add_argument(
        '--m3_high', type=int,
        default=15, required=False,
        help='MACD Signal range high to optimize')

    return parser.parse_args()

if __name__ == '__main__':
    runstrat()
```
