# 实际应用

> 原文：[`www.backtrader.com/blog/posts/2015-08-27-real-world-usage/real-world-usage/`](https://www.backtrader.com/blog/posts/2015-08-27-real-world-usage/real-world-usage/)

最后，似乎将其扎根于开发 backtrader 是值得的。

在看到上周欧洲市场的情况看起来像世界末日之后，一个朋友问我是否可以查看我们图表软件中的数据，看看下跌范围与以前类似情况的比较如何。

当然我可以，但我说我可以做的不仅仅是看图表，因为我可以迅速：

+   创建一个快速的`LegDown`指标来测量下跌的范围。它也可以被命名为`HighLowRange`或`HiLoRange`。幸运的是，如果认为有必要，可以通过`alias`来解决这个问题。

+   创建一个`LegDownAnalyzer`，它将收集结果并对其进行排序。

这导致了一个额外的请求：

+   在接下来的 5、10、15、20 天内（交易后…）的跌幅后的复苏。

    通过使用`LegUp`指标解决，它会将值写回以与相应的`LegDown`对齐。

这项工作很快就完成了（在我空闲时间的允许范围内），并与请求者共享了结果。但是…只有我看到了潜在问题：

+   改进`bt-run.py`中的自动化程式

    +   多种策略/观察者/分析器以及分开的 kwargs

    +   直接将指标注入策略中，每个指标都带有 kwargs

    +   单一的绘图参数也接受 kwargs。

+   在`Analyzer` API 中进行改进，以实现对结果的自动**打印**功能（结果以`dict`-like 实例返回），并具有直接的`data`访问别名。

尽管如此：

+   由于我编写了一个混合声明并额外使用`next`来对齐`LegDown`和`LegUp`值的实现组合，出现了一个隐晦的错误。

    这个错误是为了简化传递多个`Lines`的单个数据而引入的，以便`Indicators`可以对每条线进行操作作为单独的数据。

后者将我推向：

+   添加一个与`LineDelay`相反的背景对象以“看”到“未来”。

    实际上这意味着实际值被写入过去的数组位置。

一旦所有这些都就位了，就是重新测试以上请求提出的（小？）挑战，看看如何更轻松地解决以及更快地（在实现时间上）解决的时候了。

最后，执行和结果为从 1998 年至今的 Eurostoxx 50 期货：

```py
bt-run.py \
    --csvformat vchartcsv \
    --data ../datas/sample/1998-2015-estx50-vchart.txt \
    --analyzer legdownup \
    --pranalyzer \
    --nostdstats \
    --plot

====================
== Analyzers
====================
##########
legdownupanalyzer
##########
Date,LegDown,LegUp_5,LegUp_10,LegUp_15,LegUp_20
2008-10-10,901.0,331.0,69.0,336.0,335.0
2001-09-11,889.0,145.0,111.0,239.0,376.0
2008-01-22,844.0,328.0,360.0,302.0,344.0
2001-09-21,813.0,572.0,696.0,816.0,731.0
2002-07-24,799.0,515.0,384.0,373.0,572.0
2008-01-23,789.0,345.0,256.0,319.0,290.0
2001-09-17,769.0,116.0,339.0,405.0,522.0
2008-10-09,768.0,102.0,0.0,120.0,208.0
2001-09-12,764.0,137.0,126.0,169.0,400.0
2002-07-23,759.0,331.0,183.0,285.0,421.0
2008-10-16,758.0,102.0,222.0,310.0,201.0
2008-10-17,740.0,-48.0,219.0,218.0,116.0
2015-08-24,731.0,nan,nan,nan,nan
2002-07-22,729.0,292.0,62.0,262.0,368.0
...
...
...
2001-10-05,-364.0,228.0,143.0,286.0,230.0
1999-01-04,-370.0,219.0,99.0,-7.0,191.0
2000-03-06,-382.0,-60.0,-127.0,-39.0,-161.0
2000-02-14,-393.0,-92.0,90.0,340.0,230.0
2000-02-09,-400.0,-22.0,-46.0,96.0,270.0
1999-01-05,-438.0,3.0,5.0,-107.0,5.0
1999-01-07,-446.0,-196.0,-6.0,-82.0,-50.0
1999-01-06,-536.0,-231.0,-42.0,-174.0,-129.0
```

2015 年 8 月的下跌在第 13 个位置显示出来。显然是一个不常见的事件，尽管有更大的事件发生过。

针对指向上升的后续腿要做的事情对于统计学家和聪明的数学头脑来说要多得多，而对我来说则要少得多。

关于`LegUpDownAnalyzer`的实现细节（在末尾看到整个模块代码）：

+   它在`__init__`中创建指标，就像其他对象一样：`Strategies`，`Indicators`通常是常见的嫌疑人

    这些指标会自动注册到附加了分析器的策略中

+   就像策略一样，`Analyzer` 有 `self.datas`（一个数据数组）和它的别名：`self.data`、`self.data0`、`self.data1` …

+   类似策略：`nexstart` 和 `stop` 钩子（这些在指标中不存在）

    在这种情况下用于：

    +   `nextstart`: 记录策略的初始起始点

    +   `stop`: 进行最终的计算，因为事情已经完成

+   注意：在这种情况下不需要其他方法，如 `start`、`prenext` 和 `next`

+   `LegDownUpAnalyzer` 方法 `print` 已经被重写，不再调用 `pprint` 方法，而是创建计算的 CSV 打印输出

经过许多讨论，因为我们将 `--plot` 加入了混合中 … 图表。

![image](img/810dfdbad965951ec652a05dd8ba9d95.png)

最后是由 `bt-run` 加载的 `legupdown` 模块。

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import itertools
import operator

import six
from six.moves import map, xrange, zip

import backtrader as bt
import backtrader.indicators as btind
from backtrader.utils import OrderedDict

class LegDown(bt.Indicator):
    '''
    Calculates what the current legdown has been using:
      - Current low
      - High from ``period`` bars ago
    '''
    lines = ('legdown',)
    params = (('period', 10),)

    def __init__(self):
        self.lines.legdown = self.data.high(-self.p.period) - self.data.low

class LegUp(bt.Indicator):
    '''
    Calculates what the current legup has been using:
      - Current high
      - Low from ``period`` bars ago

    If param ``writeback`` is True the value will be written
    backwards ``period`` bars ago
    '''
    lines = ('legup',)
    params = (('period', 10), ('writeback', True),)

    def __init__(self):
        self.lu = self.data.high - self.data.low(-self.p.period)
        self.lines.legup = self.lu(self.p.period * self.p.writeback)

class LegDownUpAnalyzer(bt.Analyzer):
    params = (
        # If created indicators have to be plotteda along the data
        ('plotind', True),
        # period to consider for a legdown
        ('ldown', 10),
        # periods for the following legups after a legdown
        ('lups', [5, 10, 15, 20]),
        # How to sort: date-asc, date-desc, legdown-asc, legdown-desc
        ('sort', 'legdown-desc'),
    )

    sort_options = ['date-asc', 'date-des', 'legdown-desc', 'legdown-asc']

    def __init__(self):
        # Create the legdown indicator
        self.ldown = LegDown(self.data, period=self.p.ldown)
        self.ldown.plotinfo.plot = self.p.plotind

        # Create the legup indicators indicator - writeback is not touched
        # so the values will be written back the selected period and therefore
        # be aligned with the end of the legdown
        self.lups = list()
        for lup in self.p.lups:
            legup = LegUp(self.data, period=lup)
            legup.plotinfo.plot = self.p.plotind
            self.lups.append(legup)

    def nextstart(self):
        self.start = len(self.data) - 1

    def stop(self):
        # Calculate start and ending points with values
        start = self.start
        end = len(self.data)
        size = end - start

        # Prepare dates (key in the returned dictionary)
        dtnumslice = self.strategy.data.datetime.getzero(start, size)
        dtslice = map(lambda x: bt.num2date(x).date(), dtnumslice)
        keys = dtslice

        # Prepare the values, a list for each key item
        # leg down
        ldown = self.ldown.legdown.getzero(start, size)
        # as many legs up as requested
        lups = [up.legup.getzero(start, size) for up in self.lups]

        # put legs down/up together and interleave (zip)
        vals = [ldown] + lups
        zvals = zip(*vals)

        # Prepare sorting options
        if self.p.sort == 'date-asc':
            reverse, item = False, 0
        elif self.p.sort == 'date-desc':
            reverse, item = True, 0
        elif self.p.sort == 'legdown-asc':
            reverse, item = False, 1
        elif self.p.sort == 'legdown-desc':
            reverse, item = True, 1
        else:
            # Default ordering - date-asc
            reverse, item = False, 0

        # Prepare a sorted array of 2-tuples
        keyvals_sorted = sorted(zip(keys, zvals),
                                reverse=reverse,
                                key=operator.itemgetter(item))

        # Use it to build an ordereddict
        self.ret = OrderedDict(keyvals_sorted)

    def get_analysis(self):
        return self.ret

    def print(self, *args, **kwargs):
        # Overriden to change default behavior (call pprint)
        # provides a CSV printout of the legs down/up
        header_items = ['Date', 'LegDown']
        header_items.extend(['LegUp_%d' % x for x in self.p.lups])
        header_txt = ','.join(header_items)
        print(header_txt)

        for key, vals in six.iteritems(self.ret):
            keytxt = key.strftime('%Y-%m-%d')
            txt = ','.join(itertools.chain([keytxt], map(str, vals)))
            print(txt)
```
