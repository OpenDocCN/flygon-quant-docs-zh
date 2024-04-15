# 发布版本 1.2.1.88

> 原文：[`www.backtrader.com/blog/posts/2016-03-07-release-1.2.1.88/release-1.2.1.88/`](https://www.backtrader.com/blog/posts/2016-03-07-release-1.2.1.88/release-1.2.1.88/)

将次版本号从 1 更改为 2 花费了一些时间，但旧的 DataResampler 和 DataReplayer 的弃用导致了这一变化。

**readthedocs**上的文档

[文档](http://backtrader.readthedocs.org/en/latest/)已更新，只引用了现代化的`resampling`和`replaying`方式。操作如下：

```py
`...
data = backtrader.feeds.BacktraderCSVData(dataname='mydata.csv')  # daily bars
cerebro.resampledata(data, timeframe=backtrader.TimeFrame.Weeks) # to weeks
...` 
```

对于*replaying*只需将`resampledata`更改为`replaydata`。还有其他方法可以做到，但这是最直接的接口，可能是唯一会被任何人使用的接口。

根据[Ticket #60](https://github.com/mementum/backtrader/issues/60)，很明显扩展机制允许向数据源（实际上是任何基于*lines*的对象）添加额外行不足以支持票号中建议的内容。

因此，实现了一个额外的*parameter*到*lines*对象，允许完全重新定义行层次结构（**逃离 OHLC 领域**可能是一个合适的电影标题）

已经添加了一个名为*data-bid-ask*的示例到数据源中。从这个示例中：

```py
`class BidAskCSV(btfeeds.GenericCSVData):
    linesoverride = True  # discard usual OHLC structure
    # datetime must be present and last
    lines = ('bid', 'ask', 'datetime')
    # datetime (always 1st) and then the desired order for
    params = (
        ('dtformat', '%m/%d/%Y %H:%M:%S'),

        ('datetime', 0),  # field pos 0
        ('bid', 1),  # default field pos 1
        ('ask', 2),  # defult field pos 2
    )` 
```

通过指定`linesoverride`，常规的*lines*继承机制被绕过，对象中定义的行将取代任何先前的行。

此版本可从*pypi*获取，并可通过常规方式安装：

```py
`pip install backtrader` 
```

或者如果更新：

```py
`pip install backtrader --upgrade` 
```
