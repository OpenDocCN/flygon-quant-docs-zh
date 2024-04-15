# 分析器

> 原文：[`www.backtrader.com/docu/analyzers/analyzers/`](https://www.backtrader.com/docu/analyzers/analyzers/)

无论是回测还是交易，能够分析交易系统的性能对于了解是否仅仅获得了利润以及是否存在过多风险或者与参考资产（或无风险资产）相比是否真的值得努力至关重要。

这就是 `Analyzer` 对象族的作用：提供已发生情况或实际正在发生情况的分析。

## 分析器的性质

接口模仿了 *Lines* 对象的接口，例如包含一个 `next` 方法，但有一个主要区别：

+   `Analyzers` 不保存线条。

    这意味着它们在内存方面并不昂贵，因为即使在分析了成千上万个价格条之后，它们仍然可能只保存单个结果在内存中。

## 生态系统中的位置

`Analyzer` 对象（像 *strategies*、*observers* 和 *datas* 一样）通过 `cerebro` 实例添加到系统中：

+   `addanalyzer(ancls, *args, **kwargs)`

但是当在 `cerebro.run` 过程中进行操作时，对于系统中每个 *策略*，将会发生以下情况

+   `ancls` 将在 `cerebro.run` 过程中以 `*args` 和 `**kwargs` 实例化。

+   `ancls` 实例将被附加到策略上。

这意味着：

+   如果回测运行包含例如 *3 个策略*，那么将会创建 *3 个 `ancls` 实例*，并且每个实例都将附加到不同的策略上。

底线是：*分析器分析单个策略的性能*，而不是整个系统的性能

### 额外的位置

一些 `Analyzer` 对象实际上可能使用其他分析器来完成其工作。例如：`SharpeRatio` 使用 `TimeReturn` 的输出进行计算。

这些 *子分析器* 或 *从属分析器* 也将被插入到创建它们的同一策略中。但对用户来说完全看不见。

## 属性

为了执行预期的工作，`Analyzer` 对象提供了一些默认属性，这些属性被自动传递和设置在实例中以便使用：

+   `self.strategy`：策略子类的引用，分析器对象正在操作其中。任何 *strategy* 可访问的内容也可以被 *analyzer* 访问。

+   `self.datas[x]`：策略中存在的数据源数组。尽管这可以通过 *strategy* 引用访问，但这个快捷方式使工作更加方便。

+   `self.data`：为了额外的便利而设置的快捷方式。

+   `self.dataX`：快捷方式到不同的 `self.datas[x]`

还提供了一些其他别名，尽管它们可能是多余的：

```py
* `self.dataX_Y` where X is a reference to `self.datas[X]` and `Y`
  refers to the line, finally pointing to: `self.datas[X].lines[Y]`
```

如果线条有名称，还可以获得以下内容：

```py
* `self.dataX_Name` which resolves to `self.datas[X].Name` returning
  the line by name rather than by index
```

对于第一个数据，最后两个快捷方式也可用，无需初始的 `X` 数字引用。例如：

```py
* `self.data_2` refers to `self.datas[0].lines[2]`
```

和

```py
* `self.data_close` refers to `self.datas[0].close`
```

### 返回分析结果

*Analyzer* 基类创建一个 `self.rets`（类型为 `collections.OrderedDict`）成员属性来返回分析结果。 这是在方法 `create_analysis` 中完成的，如果创建自定义分析器，子类可以覆盖此方法。

## 操作模式

虽然 `Analyzer` 对象不是 *Lines* 对象，因此不会迭代线条，但它们被设计为遵循相同的操作模式。

1.  在系统启动之前实例化（因此调用 `__init__`）

1.  使用 `start` 标志操作的开始

1.  将调用 `prenext` / `nextstart` / `next`，遵循 *策略* 正在运行的最短周期的计算结果。

    `prenext` 和 `nextstart` 的默认行为是调用 next，因为分析器可能从系统启动的第一刻就开始分析。

    在 *Lines* 对象中调用 `len(self)` 来检查实际的条数可能是习惯的。 这在 `Analyzers` 中也适用，通过为 `self.strategy` 返回值。

1.  订单和交易将像对策略一样通过 `notify_order` 和 `notify_trade` 进行通知

1.  现金和价值也将像对策略一样通过 `notify_cashvalue` 方法进行通知

1.  现金、价值、基金价值和基金份额也将像对策略一样通过 `notify_fund` 方法进行通知

1.  `stop`将被调用以信号操作结束

一旦常规操作周期完成，*分析器* 就会提供用于提取/输出信息的附加方法

+   `get_analysis`：理想情况下（不强制要求）返回一个类似于 `dict` 的对象，其中包含分析结果。

+   `print` 使用标准的 `backtrader.WriterFile`（除非被覆盖）来从 `get_analysis` 写入分析结果。

+   `pprint`（*漂亮打印*）使用 Python `pprint` 模块打印 `get_analysis` 的结果。

最后：

+   `get_analysis` 创建一个成员属性 `self.ret`（类型为 `collections.OrderedDict`），分析器将分析结果写入其中。

    *Analyzer* 的子类可以重写此方法以更改此行为

## 分析器模式

在 `backtrader` 平台上开发 *Analyzer* 对象揭示了两种不同的用法模式用于生成分析：

1.  通过在 `notify_xxx` 和 `next` 方法中收集信息并在 `next` 中生成分析的当前信息进行执行

    例如，`TradeAnalyzer` 只使用 `notify_trade` 方法生成统计信息。

1.  在`stop`方法期间一次性生成分析结果，收集（或不收集）如上信息

    `SQN`（*系统质量数*）在 `notify_trade` 期间收集交易信息，但在 `stop` 方法中生成统计信息

## 一个快速的示例

尽可能简单：

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import datetime

import backtrader as bt
import backtrader.analyzers as btanalyzers
import backtrader.feeds as btfeeds
import backtrader.strategies as btstrats

cerebro = bt.Cerebro()

# data
dataname = '../datas/sample/2005-2006-day-001.txt'
data = btfeeds.BacktraderCSVData(dataname=dataname)

cerebro.adddata(data)

# strategy
cerebro.addstrategy(btstrats.SMA_CrossOver)

# Analyzer
cerebro.addanalyzer(btanalyzers.SharpeRatio, _name='mysharpe')

thestrats = cerebro.run()
thestrat = thestrats[0]

print('Sharpe Ratio:', thestrat.analyzers.mysharpe.get_analysis())
```

执行它（已将其存储在 `analyzer-test.py` 中：

```py
$ ./analyzer-test.py
Sharpe Ratio: {'sharperatio': 11.647332609673256}
```

没有绘图，因为 `SharpeRatio` 是在计算结束时的单个值。

## 分析器的法证分析

让我们重申一下，`分析器`不是线对象，但为了无缝地将它们整合到`backtrader`生态系统中，遵循了几个线对象的内部 API 约定（实际上是一个**混合**）

注意

`SharpeRatio`的代码已经发展到例如考虑年度化，这里的版本应该仅作为参考。

请查看分析器参考资料

此外还有一个`SharpeRatio_A`，无论所需的时间范围如何，都会直接提供年化形式的值

`SharpeRatio`的代码作为基础（简化版）

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import operator

from backtrader.utils.py3 import map
from backtrader import Analyzer, TimeFrame
from backtrader.mathsupport import average, standarddev
from backtrader.analyzers import AnnualReturn

class SharpeRatio(Analyzer):
    params = (('timeframe', TimeFrame.Years), ('riskfreerate', 0.01),)

    def __init__(self):
        super(SharpeRatio, self).__init__()
        self.anret = AnnualReturn()

    def start(self):
        # Not needed ... but could be used
        pass

    def next(self):
        # Not needed ... but could be used
        pass

    def stop(self):
        retfree = [self.p.riskfreerate] * len(self.anret.rets)
        retavg = average(list(map(operator.sub, self.anret.rets, retfree)))
        retdev = standarddev(self.anret.rets)

        self.ratio = retavg / retdev

    def get_analysis(self):
        return dict(sharperatio=self.ratio)
```

代码可以分解为：

+   `params` 声明

    尽管声明的变量没有被使用（仅作为示例），像大多数`backtrader`中的其他对象一样，*分析器*也支持参数

+   `__init__` 方法

    就像*策略*在`__init__`中声明*指标*一样，分析器也是使用支持对象的。

    在这种情况下：使用**年度收益**计算`夏普比率`。计算将自动进行，并且将对`夏普比率`进行自己的计算。

    注意

    `夏普比率`的实际实现使用了更通用和后来开发的`TimeReturn`分析器

+   `next` 方法

    `夏普比率`不需要它，但是此方法将在每次调用父策略的`next`后调用

+   `start` 方法

    在回测开始之前调用。可用于额外的初始化任务。`夏普比率`不需要它

+   `stop` 方法

    在回测结束后立即调用。像`SharpeRatio`一样，它可用于完成/进行计算

+   `get_analysis` 方法（返回一个字典）

    外部调用者对生成的分析的访问

    返回：带有分析结果的字典。

## 参考资料

#### 类 `backtrader.Analyzer()`

分析器基类。所有分析器都是此类的子类

分析器实例在策略的框架内运行，并为该策略提供分析。

自动设置成员属性：

+   `self.strategy`（提供对*策略*及其可访问的任何内容的访问）

+   `self.datas[x]` 提供对系统中存在的数据源数组的访问，也可以通过策略引用访问

+   `self.data`，提供对`self.datas[0]`的访问

+   `self.dataX` -> `self.datas[X]`

+   `self.dataX_Y` -> `self.datas[X].lines[Y]`

+   `self.dataX_name` -> `self.datas[X].name`

+   `self.data_name` -> `self.datas[0].name`

+   `self.data_Y` -> `self.datas[0].lines[Y]`

这不是一个*线*对象，但是方法和操作遵循相同的设计

+   在实例化和初始设置期间的`__init__`

+   `start` / `stop` 用于信号开始和结束操作

+   遵循与策略中相同方法调用后的`prenext` / `nextstart` / `next`方法系列

+   `notify_trade` / `notify_order` / `notify_cashvalue` / `notify_fund`，它们接收与策略的等效方法相同的通知

操作模式是开放的，没有首选模式。因此，分析可以通过`next`调用，在`stop`期间的操作结束时甚至通过单个方法`notify_trade`生成。

重要的是要重写`get_analysis`以返回包含分析结果的*类似于字典*的对象（实际格式取决于实现）。

#### start()

表示开始操作，使分析器有时间设置所需的东西。

#### stop()

表示结束操作，使分析器有时间关闭所需的东西。

#### prenext()

对策略的每次 prenext 调用都会调用，直到策略的最小周期已达到。

分析器的默认行为是调用`next`。

#### nextstart()

为下一次策略的 nextstart 调用精确调用一次，当首次达到最小周期时。

#### next()

对策略的每次 next 调用进行调用，一旦策略的最小周期已达到。

#### notify_cashvalue(cash, value)

在每次下一周期之前接收现金/价值通知。

#### notify_fund(cash, value, fundvalue, shares)

在每次下一周期之前接收当前现金、价值、基金价值和基金份额。

#### notify_order(order)

在每次下一周期之前接收订单通知。

#### notify_trade(trade)

在每次下一周期之前接收交易通知。

#### get_analysis()

返回一个*类似于字典*的对象，其中包含分析结果。

字典中分析结果的键和格式取决于具体实现。

甚至不强制结果是*类似于字典对象*，只是约定。

默认实现返回由默认的`create_analysis`方法创建的默认`OrderedDict``rets`。

#### create_analysis()

应由子类重写。给予创建保存分析的结构的机会。

默认行为是创建一个名为`rets`的`OrderedDict`。

#### print(*args, **kwargs)

通过标准的`Writerfile`对象打印`get_analysis`返回的结果，默认情况下将其写入标准输出。

#### pprint(*args, **kwargs)

使用 Python 的漂亮打印模块（*pprint*）打印`get_analysis`返回的结果。

#### **len**()

通过实际返回分析器所操作的策略的当前长度来支持对分析器进行`len`调用。
