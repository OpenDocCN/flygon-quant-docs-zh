# CSV 数据源开发

> 原文：[`www.backtrader.com/blog/posts/2015-08-06-csv-data-feed-development/csv-data-feed-development/`](https://www.backtrader.com/blog/posts/2015-08-06-csv-data-feed-development/csv-data-feed-development/)

backtrader 已经提供了一个通用的 CSV 数据源和一些特定的 CSV 数据源。总结如下：

+   GenericCSVData

+   VisualChartCSVData

+   YahooFinanceData（用于在线下载）

+   YahooFinanceCSVData（用于已下载的数据）

+   BacktraderCSVData（内部…用于测试目的，但可以使用）

但即使如此，最终用户可能希望为特定的 CSV 数据源开发支持。

通常的格言是：“说起来容易做起来难”。实际上，结构设计得很简单。

步骤：

+   继承自`backtrader.CSVDataBase`

+   如果需要，定义任何`params`

+   在`start`方法中进行任何初始化

+   在`stop`方法中进行任何清理

+   定义一个`_loadline`方法，其中实际工作发生

    此方法接收一个参数：linetokens。

    正如名称所示，这包含了在根据`separator`参数（从基类继承）分割当前行后的令牌。

    如果在完成其工作后有新数据... 填充相应的行并返回`True`

    如果没有可用的数据，因此解析已经结束：返回`False`

    如果在幕后读取文件行的代码发现没有更多可解析的行，则可能甚至不需要返回`False`。

已经考虑到的事项：

+   打开文件（或接收类似文件的对象）

+   如果存在，则跳过标题行

+   读取行

+   对行进行标记

+   预加载支持（一次性将整个数据源加载到内存中）

通常，一个例子胜过千言万语的要求描述。让我们使用从`BacktraderCSVData`定义的简化版本的内部定义的 CSV 解析代码。这个不需要初始化或清理（例如，可以稍后打开套接字并关闭）。

注意

`backtrader`数据源包含常见的行业标准数据源，这些是需要填充的。即：

+   日期时间

+   打开

+   高

+   低

+   关闭

+   成交量

+   持仓量

如果您的策略/算法或简单的数据查阅只需要，例如收盘价，您可以将其他内容保持不变（每次迭代都会在最终用户代码有机会执行任何操作之前自动使用 float（'NaN'）值填充它们。

在本例中仅支持每日格式：

```py
import itertools
...
import backtrader import bt

class MyCSVData(bt.CSVDataBase):

    def start(self):
        # Nothing to do for this data feed type
        pass

    def stop(self):
        # Nothing to do for this data feed type
        pass

    def _loadline(self, linetokens):
        i = itertools.count(0)

        dttxt = linetokens[next(i)]
        # Format is YYYY-MM-DD
        y = int(dttxt[0:4])
        m = int(dttxt[5:7])
        d = int(dttxt[8:10])

        dt = datetime.datetime(y, m, d)
        dtnum = date2num(dt)

        self.lines.datetime[0] = dtnum
        self.lines.open[0] = float(linetokens[next(i)])
        self.lines.high[0] = float(linetokens[next(i)])
        self.lines.low[0] = float(linetokens[next(i)])
        self.lines.close[0] = float(linetokens[next(i)])
        self.lines.volume[0] = float(linetokens[next(i)])
        self.lines.openinterest[0] = float(linetokens[next(i)])

        return True
```

代码期望所有字段都就位，并且可转换为浮点数，除了日期时间之外，它具有固定的 YYYY-MM-DD 格式，并且可以在不使用`datetime.datetime.strptime`的情况下解析。

只需添加几行代码即可满足更复杂的需求，以处理空值，日期格式解析。`GenericCSVData`就是这样做的。

## 买方注意事项

使用`GenericCSVData`现有的数据源和继承可以实现很多支持格式的功能。

让我们为[Sierra Chart](https://www.sierrachart.com)的每日格式添加支持（该格式始终以 CSV 格式存储）。

定义（通过查看一个**‘.dly’**数据文件）：

+   **字段**：日期、开盘价、最高价、最低价、收盘价、成交量、持仓量

    行业标准和已由 `GenericCSVData` 支持的那些文件，按相同顺序（这也是行业标准）

+   **分隔符**：,

+   **日期格式**：YYYY/MM/DD

针对这些文件的解析器：

```py
class SierraChartCSVData(backtrader.feeds.GenericCSVData):

    params = (('dtformat', '%Y/%m/%d'),)
```

`params` 的定义只是重新定义基类中的一个现有参数。在这种情况下，只需更改日期的格式化字符串。

哎呀……**Sierra Chart** 的解析器完成了。

这里是 `GenericCSVData` 的参数定义作为提醒：

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
