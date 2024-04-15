# 数据源

> 原文：[`www.backtrader.com/docu/datafeed/`](https://www.backtrader.com/docu/datafeed/)

`backtrader`配备了一组数据源解析器（在撰写本文时全部基于 CSV），让您可以从不同来源加载数据。

+   Yahoo（在线或已保存到文件中）

+   VisualChart（请参阅[www.visualchart.com](http://www.visualchart.com)

+   Backtrader CSV（用于测试的自有格式）

+   通用 CSV 支持

从快速入门指南中应清楚地了解到，您可以将数据源添加到`Cerebro`实例中。稍后，不同策略将可以在以下位置访问数据源：

+   一个数组 self.datas（插入顺序）

+   数组对象的别名：

    +   self.data 和 self.data0 指向第一个元素

    +   self.dataX 指向数组中索引为 X 的元素

有关插入方式的快速提醒：

```py
import backtrader as bt
import backtrader.feeds as btfeeds

data = btfeeds.YahooFinanceCSVData(dataname='wheremydatacsvis.csv')

cerebro = bt.Cerebro()

cerebro.adddata(data)  # a 'name' parameter can be passed for plotting purposes
```

## 数据源常见参数

此数据源可以直接从 Yahoo 下载数据并馈送到系统中。

参数：

+   `dataname`（默认值：None）必须提供

    其含义随数据源类型而异（文件位置、股票代码等）

+   `name`（默认值：‘’）

    用于绘图的装饰目的。如果未指定，可能会从`dataname`派生（例如：文件路径的最后部分）

+   `fromdate`（默认值：mindate）

    Python 日期时间对象，指示应忽略任何早于此日期时间的日期时间

+   `todate`（默认值：maxdate）

    Python 日期时间对象，指示应忽略任何晚于此日期时间的日期时间

+   `timeframe`（默认值：TimeFrame.Days）

    潜在值：`Ticks`，`Seconds`，`Minutes`，`Days`，`Weeks`，`Months`和`Years`

+   `compression`（默认值：1）

    每根柱子的实际条数。信息性的。仅在数据重采样/重播中有效。

+   `sessionstart`（默认值：None）

    数据的会话开始时间指示。可能被类用于重新采样等目的

+   `sessionend`（默认值：None）

    数据的会话结束时间指示。可能被类用于重新采样等目的

## CSV 数据源常见参数

参数（除了常见的之外）：

+   `headers`（默认值：True）

    表示传递的数据是否具有初始标题行

+   `separator`（默认值：“,”）

    考虑到每个 CSV 行的标记分隔符

## GenericCSVData

该类提供了一个通用接口，允许解析几乎每种 CSV 文件格式。

根据参数定义的顺序和字段存在性解析 CSV 文件

特定参数（或特定含义）：

+   `dataname`

    要解析的文件名或类似文件的对象

+   `datetime`（默认值：0）包含日期（或日期时间）字段的列

+   `time`（默认值：-1）包含时间字段的列，如果与日期时间字段分开（-1 表示不存在）

+   `open`（默认值：1），`high`（默认值：2），`low`（默认值：3），`close`（默认值：4），`volume`（默认值：5），`openinterest`（默认值：6）

    包含相应字段的列的索引

    如果传递了负值（例如：-1），表示 CSV 数据中不存在该字段

+   `nullvalue`（默认值：float（‘NaN’））

    如果应该有一个值缺失（CSV 字段为空），则使用的值

+   `dtformat`（默认：%Y-%m-%d %H:%M:%S）

    用于解析 datetime CSV 字段的格式

+   `tmformat`（默认：%H:%M:%S）

    如果“存在”，则用于解析时间 CSV 字段的格式（“时间” CSV 字段的默认设置是不存在）

一个覆盖以下要求的示例用法：

+   限制输入至 2000 年

+   HLOC 顺序而不是 OHLC

+   缺失值将被替换为零（0.0）

+   提供日线数据，日期时间仅为格式为 YYYY-MM-DD 的日期

+   没有 `openinterest` 列

代码：

```py
import datetime
import backtrader as bt
import backtrader.feeds as btfeeds

...
...

data = btfeeds.GenericCSVData(
    dataname='mydata.csv',

    fromdate=datetime.datetime(2000, 1, 1),
    todate=datetime.datetime(2000, 12, 31),

    nullvalue=0.0,

    dtformat=('%Y-%m-%d'),

    datetime=0,
    high=1,
    low=2,
    open=3,
    close=4,
    volume=5,
    openinterest=-1
)

...
```

稍作修改的要求：

+   限制输入至 2000 年

+   HLOC 顺序而不是 OHLC

+   缺失值将被替换为零（0.0）

+   提供分钟线数据，带有单独的日期和时间列

    +   日期的格式为 YYYY-MM-DD

    +   时间的格式为 HH.MM.SS（而不是通常的 HH:MM:SS）

+   没有 `openinterest` 列

代码：

```py
import datetime
import backtrader as bt
import backtrader.feeds as btfeed

...
...

data = btfeeds.GenericCSVData(
    dataname='mydata.csv',

    fromdate=datetime.datetime(2000, 1, 1),
    todate=datetime.datetime(2000, 12, 31),

    nullvalue=0.0,

    dtformat=('%Y-%m-%d'),
    tmformat=('%H.%M.%S'),

    datetime=0,
    time=1,
    high=2,
    low=3,
    open=4,
    close=5,
    volume=6,
    openinterest=-1
)
```

这也可以通过子类化来*永久*实现：

```py
import datetime
import backtrader.feeds as btfeed

class MyHLOC(btfreeds.GenericCSVData):

  params = (
    ('fromdate', datetime.datetime(2000, 1, 1)),
    ('todate', datetime.datetime(2000, 12, 31)),
    ('nullvalue', 0.0),
    ('dtformat', ('%Y-%m-%d')),
    ('tmformat', ('%H.%M.%S')),

    ('datetime', 0),
    ('time', 1),
    ('high', 2),
    ('low', 3),
    ('open', 4),
    ('close', 5),
    ('volume', 6),
    ('openinterest', -1)
)
```

现在只需提供 `dataname`，就可以重用这个新类：

```py
data = btfeeds.MyHLOC(dataname='mydata.csv')
```
