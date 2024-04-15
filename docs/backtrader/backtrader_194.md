# 通用 CSV 数据源

> 原文：[`www.backtrader.com/blog/posts/2015-08-04-generic-csv-datafeed/generic-csv-datafeed/`](https://www.backtrader.com/blog/posts/2015-08-04-generic-csv-datafeed/generic-csv-datafeed/)

一个问题导致实现了**GenericCSVData**，可用于解析不同的 CSV 格式。

GitHub 上的问题，[Issue #6](https://github.com/mementum/backtrader/issues/6)清楚地显示需要有能够处理任何传入 CSV 数据源的东西。

参数声明中包含关键信息：

```py
class GenericCSVData(feed.CSVDataBase):
    params = (
        ('nullvalue', float('NaN')),
        ('dtformat', '%Y-%m-%d %H:%M:%S'),
        ('tmformat', '%H:%M:%S'),

        ('datetime', 0),
        ('time', -1),
        ('open', 1),
        ('high', 2),
        ('low', 3),
        ('close', 4),
        ('volume', 5),
        ('openinterest', 6),
    )
```

因为该类继承自 CSVDataBase，一些标准参数可用：

+   `fromdate`（接受日期时间对象以限制起始日期）

+   `todate`（接受日期时间对象）以限制结束日期）

+   `headers`（默认值：True，指示 CSV 数据是否有标题行）

+   `separator`（默认值：“,”，分隔字段的字符）

+   `dataname`（包含 CSV 数据的文件名或类似文件的对象）

其他一些参数如`name`，`compression`和`timeframe`仅供参考，除非您计划执行重新采样。

当然更重要的是，新定义参数的含义：

+   `datetime`（默认值：0）列包含日期（或日期时间）字段

+   `time`（默认值：-1）列包含时间字段，如果与日期时间字段分开（-1 表示不存在）

+   `open`（默认值：1），`high`（默认值：2），`low`（默认值：3），`close`（默认值：4），`volume`（默认值：5），`openinterest`（默认值：6）

    包含相应字段的列的索引

    如果传递负值（例如：-1），表示 CSV 数据中不存在该字段

+   `nullvalue`（默认值：float（'NaN'））

    如果应该存在的值缺失（CSV 字段为空），将使用的值

+   `dtformat`（默认值：%Y-%m-%d %H:%M:%S）

    用于解析日期时间 CSV 字段的格式

+   `tmformat`（默认值：%H:%M:%S）

    用于解析时间 CSV 字段的格式（如果“存在”）（“时间”CSV 字段的默认值是不存在）

这可能足以涵盖许多不同的 CSV 格式和值的缺失。

涵盖以下要求的示例用法：

+   限制输入至 2000 年

+   HLOC 顺序而不是 OHLC

+   缺失值将被替换为零（0.0）

+   提供每日 K 线数据，日期时间格式为 YYYY-MM-DD

+   没有`openinterest`列存在

代码：

```py
import datetime
import backtrader as bt
import backtrader.feeds as btfeed

...
...

data = btfeed.GenericCSVData(
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
```

稍微修改的要求：

+   限制输入至 2000 年

+   HLOC 顺序而不是 OHLC

+   缺失值将被替换为零（0.0）

+   提供分钟级 K 线数据，具有单独的日期和时间列

    +   日期格式为 YYYY-MM-DD

    +   时间格式为 HH.MM.SS

+   没有`openinterest`列存在

代码：

```py
import datetime
import backtrader as bt
import backtrader.feeds as btfeed

...
...

data = btfeed.GenericCSVData(
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
