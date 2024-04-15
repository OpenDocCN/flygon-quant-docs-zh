# 策略选择再访

> 原文：[`www.backtrader.com/blog/posts/2017-05-16-stsel-revisited/stsel-revisited/`](https://www.backtrader.com/blog/posts/2017-05-16-stsel-revisited/stsel-revisited/)

最初的策略选择方法使用了两种策略，它们是手动注册的，以及一个简单的 `[0, 1]` 列表来决定哪个是策略的目标。

由于 Python 提供了许多元类的反射可能性，可以实际上自动化这种方法。让我们使用 `装饰器` 方法来做这件事，在这种情况下可能是最不侵入性的方法（不需要为策略定义 *元类*）

# 重新设计工厂

工厂现在：

+   在策略之前声明

+   具有一个空的 `_STRATS` 类属性（之前它包含要返回的策略）

+   具有一个 `register` 类方法，将用作装饰器，并接受一个参数，该参数将添加到 `_STRATS`

+   具有一个 `COUNT` 类方法，将返回一个迭代器（实际上是一个 `range`），其中包含要优化的可用策略的计数

+   对实际工厂方法没有任何更改：`__new__`，它仍然使用 `idx` 参数来返回 `_STRATS` 类属性中给定索引处的任何内容

```py
`class StFetcher(object):
    _STRATS = []

    @classmethod
    def register(cls, target):
        cls._STRATS.append(target)

    @classmethod
    def COUNT(cls):
        return range(len(cls._STRATS))

    def __new__(cls, *args, **kwargs):
        idx = kwargs.pop('idx')

        obj = cls._STRATSidx
        return obj` 
```

如下：

+   `StFetcher` 策略工厂不再包含任何硬编码的策略

# 装饰待优化的策略

示例中的策略不需要重新设计。使用 `StFetcher` 的 `register` 方法进行装饰就足以将它们添加到选择池中。

```py
`@StFetcher.register
class St0(bt.SignalStrategy):` 
```

和

```py
`@StFetcher.register
class St1(bt.SignalStrategy):` 
```

# 利用 `COUNT`

在将策略工厂添加到系统中并使用 `optstrategy` 时，过去的手动 `[0, 1]` 列表可以完全替换为对 `StFetcher.COUNT()` 的透明调用。硬编码已经结束。

```py
 `cerebro.optstrategy(StFetcher, idx=StFetcher.COUNT())` 
```

## 一个样本运行

```py
`$ ./stselection-revisited.py --optreturn
Strat 0 Name OptReturn:
  - analyzer: OrderedDict([(u'rtot', 0.04847392369449283), (u'ravg', 9.467563221580632e-05), (u'rnorm', 0.02414514457151587), (u'rnorm100', 2.414514457151587)])

Strat 1 Name OptReturn:
  - analyzer: OrderedDict([(u'rtot', 0.05124714332260593), (u'ravg', 0.00010009207680196471), (u'rnorm', 0.025543999840699633), (u'rnorm100', 2.5543999840699634)])` 
```

我们的两种策略已经运行并且（如预期）产生了不同的结果。

注意

该示例很简单，但已经在所有可用的 CPU 上运行。使用 `--maxpcpus=1` 执行将更快。对于更复杂的情况，使用所有 CPU 将很有用。

## 结论

选择已经完全自动化。与之前一样，可以想象类似于查询数据库以获取可用策略数量，然后逐个获取策略的方法。

### 样本用法

```py
`$ ./stselection-revisited.py --help
usage: strategy-selection.py [-h] [--data DATA] [--maxcpus MAXCPUS]
                             [--optreturn]

Sample for strategy selection

optional arguments:
  -h, --help         show this help message and exit
  --data DATA        Data to be read in (default:
                     ../../datas/2005-2006-day-001.txt)
  --maxcpus MAXCPUS  Limit the numer of CPUs to use (default: None)
  --optreturn        Return reduced/mocked strategy object (default: False)` 
```

### 代码

已经包含在 backtrader 的源代码中

```py
`from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse

import backtrader as bt
from backtrader.utils.py3 import range

class StFetcher(object):
    _STRATS = []

    @classmethod
    def register(cls, target):
        cls._STRATS.append(target)

    @classmethod
    def COUNT(cls):
        return range(len(cls._STRATS))

    def __new__(cls, *args, **kwargs):
        idx = kwargs.pop('idx')

        obj = cls._STRATSidx
        return obj

@StFetcher.register
class St0(bt.SignalStrategy):
    def __init__(self):
        sma1, sma2 = bt.ind.SMA(period=10), bt.ind.SMA(period=30)
        crossover = bt.ind.CrossOver(sma1, sma2)
        self.signal_add(bt.SIGNAL_LONG, crossover)

@StFetcher.register
class St1(bt.SignalStrategy):
    def __init__(self):
        sma1 = bt.ind.SMA(period=10)
        crossover = bt.ind.CrossOver(self.data.close, sma1)
        self.signal_add(bt.SIGNAL_LONG, crossover)

def runstrat(pargs=None):
    args = parse_args(pargs)

    cerebro = bt.Cerebro()
    data = bt.feeds.BacktraderCSVData(dataname=args.data)
    cerebro.adddata(data)

    cerebro.addanalyzer(bt.analyzers.Returns)
    cerebro.optstrategy(StFetcher, idx=StFetcher.COUNT())
    results = cerebro.run(maxcpus=args.maxcpus, optreturn=args.optreturn)

    strats = [x[0] for x in results]  # flatten the result
    for i, strat in enumerate(strats):
        rets = strat.analyzers.returns.get_analysis()
        print('Strat {} Name {}:\n  - analyzer: {}\n'.format(
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
    runstrat()` 
```
