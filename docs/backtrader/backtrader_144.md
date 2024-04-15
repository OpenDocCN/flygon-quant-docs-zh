# 策略选择

> 原文：[`www.backtrader.com/blog/posts/2016-10-29-strategy-selection/strategy-selection/`](https://www.backtrader.com/blog/posts/2016-10-29-strategy-selection/strategy-selection/)

休斯顿，我们有问题：

+   *cerebro*不打算多次运行。这不是第 1 次，而不是认为用户做错了，似乎这是一个用例。

这个有趣的用例是通过[Ticket 177](https://github.com/mementum/backtrader/issues/177)提出的。在这种情况下，*cerebro*被多次使用来评估从外部数据源获取的不同策略。

*backtrader*仍然可以支持这种用例，但不是以尝试的直接方式。

# 优化选择

*backtrader*中内置的优化已经做了所需的事情：

+   实例化多个策略实例并收集结果

唯一的一件事是*instances*都属于同一个*class*。这就是 Python 帮助我们控制对象创建的地方。

首先，让我们使用*backtrader*内置的*Signal*技术将两个非常快速的策略添加到脚本中

```py
class St0(bt.SignalStrategy):
    def __init__(self):
        sma1, sma2 = bt.ind.SMA(period=10), bt.ind.SMA(period=30)
        crossover = bt.ind.CrossOver(sma1, sma2)
        self.signal_add(bt.SIGNAL_LONG, crossover)

class St1(bt.SignalStrategy):
    def __init__(self):
        sma1 = bt.ind.SMA(period=10)
        crossover = bt.ind.CrossOver(self.data.close, sma1)
        self.signal_add(bt.SIGNAL_LONG, crossover)
```

这再也不能更简单了。

现在让我们来展示这两个策略的魔力。

```py
class StFetcher(object):
    _STRATS = [St0, St1]

    def __new__(cls, *args, **kwargs):
        idx = kwargs.pop('idx')

        obj = cls._STRATSidx
        return obj
```

Et voilá！当类`StFetcher`被实例化时，方法`__new__`控制实例的创建。在这种情况下：

+   获取传递给它的`idx`参数

+   使用这个参数从存储我们之前示例策略的`_STRATS`列表中获取策略

    注意

    没有什么能阻止使用这个`idx`值从服务器和/或数据库中获取策略。

+   实例化并返回*fecthed*策略

## 运行展示

```py
 cerebro.addanalyzer(bt.analyzers.Returns)
    cerebro.optstrategy(StFetcher, idx=[0, 1])
    results = cerebro.run(maxcpus=args.maxcpus, optreturn=args.optreturn)
```

确实！优化就是这样！我们不再使用`addstrategy`，而是使用`optstrategy`并传递一个`idx`值的数组。这些值将被优化引擎迭代。

因为`cerebro`可以在每次优化中托管多个策略，结果将包含一个*列表的列表*。每个子列表是每次优化的结果。

在我们的情况下，每次只有 1 个策略，我们可以快速展平结果并提取我们添加的分析器的值。

```py
 strats = [x[0] for x in results]  # flatten the result
    for i, strat in enumerate(strats):
        rets = strat.analyzers.returns.get_analysis()
        print('Strat {} Name {}:\n - analyzer: {}\n'.format(
            i, strat.__class__.__name__, rets))
```

## 一个示例运行

```py
./strategy-selection.py

Strat 0 Name St0:
  - analyzer: OrderedDict([(u'rtot', 0.04847392369449283), (u'ravg', 9.467563221580632e-05), (u'rnorm', 0.02414514457151587), (u'rnorm100', 2.414514457151587)])

Strat 1 Name St1:
  - analyzer: OrderedDict([(u'rtot', 0.05124714332260593), (u'ravg', 0.00010009207680196471), (u'rnorm', 0.025543999840699633), (u'rnorm100', 2.5543999840699634)])
```

我们的 2 个策略已经运行并交付了（如预期的）不同的结果。

注意

这个示例很简单，但已经在所有可用的 CPU 上运行过。使用`--maxpcpus=1`来执行会更快。对于更复杂的场景，使用所有 CPU 将会很有用。

## 结论

*策略选择*用例是可能的，不需要绕过*backtrader*或*Python*本身的任何内置设施。

### 示例用法

```py
$ ./strategy-selection.py --help
usage: strategy-selection.py [-h] [--data DATA] [--maxcpus MAXCPUS]
                             [--optreturn]

Sample for strategy selection

optional arguments:
  -h, --help         show this help message and exit
  --data DATA        Data to be read in (default:
                     ../../datas/2005-2006-day-001.txt)
  --maxcpus MAXCPUS  Limit the numer of CPUs to use (default: None)
  --optreturn        Return reduced/mocked strategy object (default: False)
```

### 代码

这已经包含在 backtrader 的源代码中

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse

import backtrader as bt

class St0(bt.SignalStrategy):
    def __init__(self):
        sma1, sma2 = bt.ind.SMA(period=10), bt.ind.SMA(period=30)
        crossover = bt.ind.CrossOver(sma1, sma2)
        self.signal_add(bt.SIGNAL_LONG, crossover)

class St1(bt.SignalStrategy):
    def __init__(self):
        sma1 = bt.ind.SMA(period=10)
        crossover = bt.ind.CrossOver(self.data.close, sma1)
        self.signal_add(bt.SIGNAL_LONG, crossover)

class StFetcher(object):
    _STRATS = [St0, St1]

    def __new__(cls, *args, **kwargs):
        idx = kwargs.pop('idx')

        obj = cls._STRATSidx
        return obj

def runstrat(pargs=None):
    args = parse_args(pargs)

    cerebro = bt.Cerebro()
    data = bt.feeds.BacktraderCSVData(dataname=args.data)
    cerebro.adddata(data)

    cerebro.addanalyzer(bt.analyzers.Returns)
    cerebro.optstrategy(StFetcher, idx=[0, 1])
    results = cerebro.run(maxcpus=args.maxcpus, optreturn=args.optreturn)

    strats = [x[0] for x in results]  # flatten the result
    for i, strat in enumerate(strats):
        rets = strat.analyzers.returns.get_analysis()
        print('Strat {} Name {}:\n - analyzer: {}\n'.format(
            i, strat.__class__.__name__, rets))

def parse_args(pargs=None):

    parser = argparse.ArgumentParser(
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
        description='Sample for strategy selection')

    parser.add_argument('--data', required=False,
                        default='../../datas/2005-2006-day-001.txt',
                        help='Data to be read in')

    parser.add_argument('--maxcpus', required=False, action='store',
                        default=None, type=int,
                        help='Limit the numer of CPUs to use')

    parser.add_argument('--optreturn', required=False, action='store_true',
                        help='Return reduced/mocked strategy object')

    return parser.parse_args(pargs)

if __name__ == '__main__':
    runstrat()
```
