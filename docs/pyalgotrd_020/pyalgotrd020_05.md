# 数据源 - 基本数据源

> 原文：[`gbeced.github.io/pyalgotrade/docs/v0.20/html/feed.html`](https://gbeced.github.io/pyalgotrade/docs/v0.20/html/feed.html)

数据源是提供抽象的时间序列数据。当这些数据源包含在事件分派循环中时，它们会在新数据可用时发出事件。数据源还负责更新与数据源提供的每个数据相关联的`pyalgotrade.dataseries.DataSeries`。

**该软件包具有基本数据源。有关 K 线数据源，请参阅** *barfeed – K 线数据源* **部分。**

*class* `pyalgotrade.feed.``BaseFeed`(*maxLen*)

基类：`pyalgotrade.observer.Subject`

数据源的基类。

| 参数: | **maxLen** (*int.*) – 每个`pyalgotrade.dataseries.DataSeries`将保留的最大值数量。一旦有界长度已满，当添加新项目时，相应数量的项目将从另一端丢弃。 |
| --- | --- |

注

这是一个基类，不应直接使用。

`__contains__`(*key*)

如果给定键的`pyalgotrade.dataseries.DataSeries`可用，则返回 True。

`__getitem__`(*key*)

返回给定键的`pyalgotrade.dataseries.DataSeries`。

`getNewValuesEvent`()

返回在新值可用时将发出的事件。要订阅，您需要传入一个可调用对象，该对象接收两个参数:

1.  一个`datetime.datetime`实例。

1.  新值。

## CSV 支持

*class* `pyalgotrade.feed.csvfeed.``Feed`(*dateTimeColumn*, *dateTimeFormat*, *converter=None*, *delimiter='*, *'*, *timezone=None*, *maxLen=None*)

基类：`pyalgotrade.feed.csvfeed.BaseFeed`

一个从 CSV 格式文件加载值的数据源。

| 参数: |
| --- |

+   **dateTimeColumn** (*string.*) – 具有日期时间信息的列的名称。

+   **dateTimeFormat** (*string.*) – 日期时间格式。将使用 datetime.datetime.strptime 来解析列。

+   **converter** (*function.*) – 具有两个参数（列名和值）的函数，用于将字符串值转换为其他值。默认转换器将尝试将值转换为浮点数。如果失败，则返回原始字符串。

+   **delimiter** (*string.*) – 用于分隔值的字符串。

+   **timezone** (*一个 pytz 时区.*) – 用于本地化日期时间的时区。检查 `pyalgotrade.marketsession`。

+   **maxLen** (*int.*) – 每个 `pyalgotrade.dataseries.DataSeries` 将保存的值的最大数量。一旦有界长度已满，当添加新项时，相应数量的项将从另一端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

`addValuesFromCSV`(*path*)

从文件中加载值。

| 参数： | **path** (*string.*) – CSV 文件的路径。 |
| --- | --- |

## CSV 支持示例

以下是格式如下的文件

```py
Date,USD,GBP,EUR
2013-09-29,1333.0,831.203,986.75
2013-09-22,1349.25,842.755,997.671
2013-09-15,1318.5,831.546,993.969
2013-09-08,1387.0,886.885,1052.911
.
.
.
```

可以这样加载：

```py
from __future__ import print_function

from pyalgotrade.feed import csvfeed

feed = csvfeed.Feed("Date", "%Y-%m-%d")
feed.addValuesFromCSV("quandl_gold_2.csv")
for dateTime, value in feed:
    print(dateTime, value) 
```

输出应该是这样的：

```py
1968-04-07 00:00:00 {'USD': 37.0, 'GBP': 15.3875, 'EUR': ''}
1968-04-14 00:00:00 {'USD': 38.0, 'GBP': 15.8208, 'EUR': ''}
1968-04-21 00:00:00 {'USD': 37.65, 'GBP': 15.6833, 'EUR': ''}
1968-04-28 00:00:00 {'USD': 38.65, 'GBP': 16.1271, 'EUR': ''}
1968-05-05 00:00:00 {'USD': 39.1, 'GBP': 16.3188, 'EUR': ''}
1968-05-12 00:00:00 {'USD': 39.6, 'GBP': 16.5625, 'EUR': ''}
1968-05-19 00:00:00 {'USD': 41.5, 'GBP': 17.3958, 'EUR': ''}
1968-05-26 00:00:00 {'USD': 41.75, 'GBP': 17.5104, 'EUR': ''}
1968-06-02 00:00:00 {'USD': 41.95, 'GBP': 17.6, 'EUR': ''}
1968-06-09 00:00:00 {'USD': 41.25, 'GBP': 17.3042, 'EUR': ''}
.
.
.
2013-07-28 00:00:00 {'USD': 1331.0, 'GBP': 864.23, 'EUR': 1001.505}
2013-08-04 00:00:00 {'USD': 1309.25, 'GBP': 858.637, 'EUR': 986.921}
2013-08-11 00:00:00 {'USD': 1309.0, 'GBP': 843.156, 'EUR': 979.79}
2013-08-18 00:00:00 {'USD': 1369.25, 'GBP': 875.424, 'EUR': 1024.964}
2013-08-25 00:00:00 {'USD': 1377.5, 'GBP': 885.738, 'EUR': 1030.6}
2013-09-01 00:00:00 {'USD': 1394.75, 'GBP': 901.292, 'EUR': 1055.749}
2013-09-08 00:00:00 {'USD': 1387.0, 'GBP': 886.885, 'EUR': 1052.911}
2013-09-15 00:00:00 {'USD': 1318.5, 'GBP': 831.546, 'EUR': 993.969}
2013-09-22 00:00:00 {'USD': 1349.25, 'GBP': 842.755, 'EUR': 997.671}
2013-09-29 00:00:00 {'USD': 1333.0, 'GBP': 831.203, 'EUR': 986.75}

```

### 目录

+   feed – 基本 feeds

    +   CSV 支持

    +   CSV 支持示例

