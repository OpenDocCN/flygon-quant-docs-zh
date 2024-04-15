# 数据源开发

> 原文：[`www.backtrader.com/blog/posts/2015-08-11-datafeed-development/datafeed-development/`](https://www.backtrader.com/blog/posts/2015-08-11-datafeed-development/datafeed-development/)

添加一个基于 CSV 的新数据源很容易。现有的基类 CSVDataBase 提供了框架，大部分工作都由子类完成，这在大多数情况下可以简单地完成：

```py
def _loadline(self, linetokens):

  # parse the linetokens here and put them in self.lines.close,
  # self.lines.high, etc

  return True # if data was parsed, else ... return False
```

这在 CSV 数据源开发中已经展示过了。

基类负责参数、初始化、文件打开、读取行、将行拆分为标记以及跳过不符合用户定义的日期范围（`fromdate`、`todate`）的行等其他事项。

开发非 CSV 数据源遵循相同的模式，而不需要深入到已拆分的行标记。

要做的事情：

+   源自 backtrader.feed.DataBase

+   添加任何可能需要的参数

+   如果需要初始化，请重写`__init__(self)`和/或`start(self)`

+   如果需要任何清理代码，请重写`stop(self)`

+   工作发生在必须始终被重写的方法内：`_load(self)`

让我们使用`backtrader.feed.DataBase`已经提供的参数：

```py
class DataBase(six.with_metaclass(MetaDataBase, dataseries.OHLCDateTime)):

    params = (('dataname', None),
        ('fromdate', datetime.datetime.min),
        ('todate', datetime.datetime.max),
        ('name', ''),
        ('compression', 1),
        ('timeframe', TimeFrame.Days),
        ('sessionend', None))
```

具有���下含义：

+   `dataname`是允许数据源识别如何获取数据的内容。在`CSVDataBase`的情况下，此参数应该是文件的路径或已经是类似文件的对象。

+   `fromdate`和`todate`定义了将传递给策略的日期范围。数据源提供的任何超出此范围的值将被忽略

+   `name`是用于绘图目的的装饰性内容

+   `timeframe`和`compression`是装饰性和信息性的。它们在数据重采样和数据重播中真正发挥作用。

+   如果传递了`sessionend`（一个 datetime.time 对象），它将被添加到数据源的`datetime`行中，从而可以识别会话结束的时间

## 示例二进制数据源

`backtrader`已经为[VisualChart](https://www.visualchart.com)的导出定义了一个 CSV 数据源（`VChartCSVData`），但也可以直接读取二进制数据文件。

让我们开始吧（完整的数据源代码可以在底部找到）

### 初始化

二进制 VisualChart 数据文件可以包含每日数据（.fd 扩展名）或分钟数据（.min 扩展名）。这里，信息性参数`timeframe`将用于区分正在读取的文件类型。

在`__init__`期间，为每种类型设置不同的常量。

```py
 def __init__(self):
        super(VChartData, self).__init__()

        # Use the informative "timeframe" parameter to understand if the
        # code passed as "dataname" refers to an intraday or daily feed
        if self.p.timeframe >= TimeFrame.Days:
            self.barsize = 28
            self.dtsize = 1
            self.barfmt = 'IffffII'
        else:
            self.dtsize = 2
            self.barsize = 32
            self.barfmt = 'IIffffII'
```

### 开始

当开始回测时，数据源将*启动*（在优化过程中实际上可以启动多次）

在`start`方法中，除非传递了类似文件的对象，否则将打开二进制文件。

```py
 def start(self):
        # the feed must start ... get the file open (or see if it was open)
        self.f = None
        if hasattr(self.p.dataname, 'read'):
            # A file has been passed in (ex: from a GUI)
            self.f = self.p.dataname
        else:
            # Let an exception propagate
            self.f = open(self.p.dataname, 'rb')
```

### 停止

当回测完成时调用。

如果文件已打开，则将关闭

```py
 def stop(self):
        # Close the file if any
        if self.f is not None:
            self.f.close()
            self.f = None
```

### 实际加载

实际工作是在`_load`中完成的。调用以加载下一组数据，此处为下一个：日期时间、开盘价、最高价、最低价、收盘价、成交量、持仓量。在`backtrader`中，“实际”时刻对应于索引 0。

一定数量的字节将从打开的文件中读取（由`__init__`期间设置的常量确定），使用`struct`模块解析，如果需要进一步处理（例如使用 divmod 操作处理日期和时间），则存储在数据源的`lines`中：日期时间、开盘价、最高价、最低价、收盘价、成交量、持仓量。

如果无法从文件中读取任何数据，则假定已达到文件结尾（EOF）。

+   返回`False`表示无更多数据可用的事实

否则，如果数据已加载并解析：

+   返回`True`表示数据集加载成功

```py
 def _load(self):
        if self.f is None:
            # if no file ... no parsing
            return False

        # Read the needed amount of binary data
        bardata = self.f.read(self.barsize)
        if not bardata:
            # if no data was read ... game over say "False"
            return False

        # use struct to unpack the data
        bdata = struct.unpack(self.barfmt, bardata)

        # Years are stored as if they had 500 days
        y, md = divmod(bdata[0], 500)
        # Months are stored as if they had 32 days
        m, d = divmod(md, 32)
        # put y, m, d in a datetime
        dt = datetime.datetime(y, m, d)

        if self.dtsize > 1:  # Minute Bars
            # Daily Time is stored in seconds
            hhmm, ss = divmod(bdata[1], 60)
            hh, mm = divmod(hhmm, 60)
            # add the time to the existing atetime
            dt = dt.replace(hour=hh, minute=mm, second=ss)

        self.lines.datetime[0] = date2num(dt)

        # Get the rest of the unpacked data
        o, h, l, c, v, oi = bdata[self.dtsize:]
        self.lines.open[0] = o
        self.lines.high[0] = h
        self.lines.low[0] = l
        self.lines.close[0] = c
        self.lines.volume[0] = v
        self.lines.openinterest[0] = oi

        # Say success
        return True
```

## 其他二进制格式

相同的模型可以应用于任何其他二进制源：

+   数据库

+   分层数据存储

+   在线数据源

步骤再次说明：

+   `__init__` -> 实例的任何初始化代码，仅执行一次

+   `start` -> 回测的开始（如果将进行优化，则可能发生一次或多次）

    例如，这将打开与数据库的连接或与在线服务的套接字连接。

+   `stop` -> 清理工作，如关闭数据库连接或打开套接字

+   `_load` -> 查询数据库或在线数据源以获取下一组数据，并将其加载到对象的`lines`中。标准字段包括：日期时间、开盘价、最高价、最低价、收盘价、成交量、持仓量

## VChartData 测试

`VCharData` 从本地“.fd”文件加载谷歌 2006 年的数据。

这只涉及加载数据，因此甚至不需要`Strategy`的子类。

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import datetime

import backtrader as bt
from vchart import VChartData

if __name__ == '__main__':
    # Create a cerebro entity
    cerebro = bt.Cerebro(stdstats=False)

    # Add a strategy
    cerebro.addstrategy(bt.Strategy)

    # Create a Data Feed
    datapath = '../datas/goog.fd'
    data = VChartData(
        dataname=datapath,
        fromdate=datetime.datetime(2006, 1, 1),
        todate=datetime.datetime(2006, 12, 31),
        timeframe=bt.TimeFrame.Days
    )

    # Add the Data Feed to Cerebro
    cerebro.adddata(data)

    # Run over everything
    cerebro.run()

    # Plot the result
    cerebro.plot(style='bar')
```

![image](img/b05b400a8f4c0282916d6f8d86a551e0.png)

## VChartData 完整代码

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import datetime
import struct

from backtrader.feed import DataBase
from backtrader import date2num
from backtrader import TimeFrame

class VChartData(DataBase):
    def __init__(self):
        super(VChartData, self).__init__()

        # Use the informative "timeframe" parameter to understand if the
        # code passed as "dataname" refers to an intraday or daily feed
        if self.p.timeframe >= TimeFrame.Days:
            self.barsize = 28
            self.dtsize = 1
            self.barfmt = 'IffffII'
        else:
            self.dtsize = 2
            self.barsize = 32
            self.barfmt = 'IIffffII'

    def start(self):
        # the feed must start ... get the file open (or see if it was open)
        self.f = None
        if hasattr(self.p.dataname, 'read'):
            # A file has been passed in (ex: from a GUI)
            self.f = self.p.dataname
        else:
            # Let an exception propagate
            self.f = open(self.p.dataname, 'rb')

    def stop(self):
        # Close the file if any
        if self.f is not None:
            self.f.close()
            self.f = None

    def _load(self):
        if self.f is None:
            # if no file ... no parsing
            return False

        # Read the needed amount of binary data
        bardata = self.f.read(self.barsize)
        if not bardata:
            # if no data was read ... game over say "False"
            return False

        # use struct to unpack the data
        bdata = struct.unpack(self.barfmt, bardata)

        # Years are stored as if they had 500 days
        y, md = divmod(bdata[0], 500)
        # Months are stored as if they had 32 days
        m, d = divmod(md, 32)
        # put y, m, d in a datetime
        dt = datetime.datetime(y, m, d)

        if self.dtsize > 1:  # Minute Bars
            # Daily Time is stored in seconds
            hhmm, ss = divmod(bdata[1], 60)
            hh, mm = divmod(hhmm, 60)
            # add the time to the existing atetime
            dt = dt.replace(hour=hh, minute=mm, second=ss)

        self.lines.datetime[0] = date2num(dt)

        # Get the rest of the unpacked data
        o, h, l, c, v, oi = bdata[self.dtsize:]
        self.lines.open[0] = o
        self.lines.high[0] = h
        self.lines.low[0] = l
        self.lines.close[0] = c
        self.lines.volume[0] = v
        self.lines.openinterest[0] = oi

        # Say success
        return True
```
