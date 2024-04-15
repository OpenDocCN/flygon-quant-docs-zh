# 过滤器

> 原文：[`www.backtrader.com/docu/filters/`](https://www.backtrader.com/docu/filters/)

此功能是相对较晚添加到*backtrader*中的，并且必须适应已经存在的内部结构。这使得它不像希望的那样灵活和 100%功能齐全，但在许多情况下仍然可以实现目的。

尽管实现尝试允许即插即用的过滤器链接，但是现有的内部结构使得很难确保总是可以实现。因此，一些过滤器可能被链接，而另一些则可能没有。

## 目的

+   转换由*数据源*提供的值以提供不同的*数据源*

实现是为了简化可以通过*cerebro* API 直接使用的两个明显过滤器的实现。这些是：

+   *重新采样*（`cerebro.resampledata`）

    在这里，过滤器转换了传入*数据源*的`时间框架`和`压缩`。例如：

    ```py
    `(Seconds, 1) -> (Days, 1)` 
    ```

    这意味着原始数据提供的是分辨率为*1 秒*的柱状图。 *重新采样*过滤器拦截数据并将其缓冲，直到可以提供*1 天*柱状图。当看到下一天的*1 秒*柱状图时，将会发生这种情况。

+   *重播*（`cerebro.replaydata`）

    对于上述相同的时间段，该过滤器将使用*1 秒*分辨率的柱状图来重建*1 天*柱状图。

    这意味着*1 天*柱状图会被提供与看到的*1 秒*柱状图一样多次，并更新以包含最新信息。

    例如，这模拟了实际交易日的发展方式。

    注意

    数据的长度，`len(data)`以及策略的长度在*天*不变的情况下保持不变。

## 过滤器工作中

给定现有的数据源，您可以使用数据源的`addfilter`方法：

```py
`data = MyDataFeed(dataname=myname)
data.addfilter(filter, *args, **kwargs)
cerebro.addata(data)` 
```

即使它恰好与*重新采样/重播*过滤器兼容，也可以执行以下操作：

```py
`data = MyDataFeed(dataname=myname)
data.addfilter(filter, *args, **kwargs)
cerebro.replaydata(data)` 
```

## 过滤器接口

一个`filter`必须符合给定的接口，即：

+   一个可调用的函数，接受此签名：

    ```py
    `callable(data, *args, **kwargs)` 
    ```

或者

+   一个可以*实例化*和*调用*的类

    +   在实例化期间，`__init__`方法必须支持此签名：

    ```py
    `def __init__(self, data, *args, **kwargs)` 
    ```

    +   `__call__`方法具有以下签名：

    ```py
    `def __call__(self, data, *args, **kwargs)` 
    ```

    对于来自*数据源*的每个新输入值，将调用该实例。 `\*args`和`\*kwargs`与`__init__`传递的相同。

    **返回值**：

    ```py
    `* `True`: the inner data fetching loop of the data feed must retry
      fetching data from the feed, becaue the length of the stream was
      manipulated

    * `False` even if data may have been edited (example: changed
      `close` price), the length of the stream has remain untouched` 
    ```

    在基于类的过滤器的情况下，可以实现 2 个附加方法

    +   具有以下签名的`last`：

    ```py
    `def last(self, data, *args, **kwargs)` 
    ```

    当*数据源*结束时，将调用此方法，允许过滤器传递它可能已经缓冲的数据。一个典型的情况是*重新采样*，因为柱状图被缓冲，直到看到下一个时间段的数据。当数据源结束时，没有新数据来推送缓冲的数据出去。

    `last`提供了将缓冲数据推送出去的机会。

注意

很明显，如果*过滤器*根本不支持任何参数，并且将添加而无任何参数，则签名可以简化为：

```py
`def __init__(self, data, *args, **kwargs) -> def __init__(self, data)` 
```

## 一个示例过滤器

一个非常快速的过滤器实现：

```py
`class SessionFilter(object):
    def __init__(self, data):
        pass

    def __call__(self, data):
        if data.p.sessionstart <= data.datetime.time() <= data.p.sessionend:
            # bar is in the session
            return False  # tell outer data loop the bar can be processed

        # bar outside of the regular session times
        data.backwards()  # remove bar from data stack
        return True  # tell outer data loop to fetch a new bar` 
```

这个过滤器：

+   使用`data.p.sessionstart`和`data.p.sessionend`（标准数据源参数）来判断条是否在会话中。

+   如果*在会话中*，返回值为`False`，以指示未执行任何操作，可以继续处理当前条目

+   如果*不在会话中*，则从流中移除该条，并返回`True`以指示必须获取新条。

    注意

    `data.backwards()`利用`LineBuffer`接口。 这深入到*backtrader*的内部。

这种过滤器的用法：

+   一些数据源包含*非常规交易时间外*的数据，这些数据可能不受交易者的关注。 使用此过滤器仅将*会话内*条考虑在内。

## 用于过滤器的数据伪-API

在上述示例中，已经展示了过滤器如何调用`data.backwards()`以从流中移除当前条。 数据源对象的有用调用，旨在作为*过滤器的伪-API*：

+   `data.backwards(size=1, force=False)`：通过将逻辑指针向后移动来从数据流中删除*size*条（默认为`1`）。 如果`force=True`，则物理存储也将被删除。

    移除物理存储是一项细致的操作，仅用作内部操作的黑客方法。

+   `data.forward(value=float('NaN'), size=1)`：将*size*条移动到存储区向前移动，必要时增加物理存储并用`value`填充。

+   `data._addtostack(bar, stash=False)`：将`bar`添加到堆栈以供以后处理。 `bar`是一个包含与数据源的`lines`一样多的值的可迭代对象。

    如果`stash=False`，则添加到堆栈的条将立即被系统消耗，在下一次迭代开始时。

    如果`stash=True`，则条将经历整个循环处理，包括可能被过滤器重新解析

+   `data._save2stack(erase=False, force=False)`：将当前数据条保存到堆栈以供以后处理。 如果`erase=True`，则将调用`data.backwards`并将接收参数`force`。

+   `data._updatebar(bar, forward=False, ago=0)`：使用可迭代对象`bar`中的值来覆盖数据流中`ago`位置的值。 默认情况下，`ago=0`将更新当前条。 如果是`-1`，则是前一个。

## 另一个示例：粉鱼过滤器

这是一个可以链接的过滤器的示例，并且旨在如此，以连接到另一个过滤器，即*replay 过滤器*。 *Pinkfish*名称来自于其主页描述该想法的库：使用每日数据执行仅在分时数据中才可能的操作。

要实现效果：

+   每日条将分解为 2 个组件：`OHL`然后是`C`。

+   这两个部分通过*重播*链接以在流中发生以下情况：

    ```py
    `With Len X     -> OHL
    With Len X     -> OHLC
    With Len X + 1 -> OHL
    With Len X + 1 -> OHLC
    With Len X + 2 -> OHL
    With Len X + 2 -> OHLC
    ...` 
    ```

逻辑：

+   收到`OHLC`条时，将其复制到一个可迭代对象中，并分解为以下内容：

    +   一个`OHL`条。 由于这个概念实际上并不存在，*收盘*价格被替换为*开盘*价格以真正形成一个`OHLO`条。

    +   一个不存在的 `C` 条，实际上会被传递为一个刻度 `CCCC`

    +   成交量在这两部分之间分配

    +   当前条被从流中移除

    +   `OHLO` 部分被放入堆栈中进行即时处理

    +   `CCCC` 部分被放入存储区，在下一轮处理中处理

    +   因为堆栈中有一些需要立即处理的内容，过滤器可以返回 `False` 来指示这一点。

这个过滤器与以下内容一起工作：

+   *重播* 过滤器将 `OHLO` 和 `CCCC` 部分组合在一起，最终生成一个 `OHLC` 条。

使用案例：

+   看到像是如果今天的最大值是过去 20 个交易日中的最高最大值，就发出一个 `Close` 订单，该订单在第 2 个刻度执行。

代码：

```py
`class DaySplitter_Close(bt.with_metaclass(bt.MetaParams, object)):
  '''
 Splits a daily bar in two parts simulating 2 ticks which will be used to
 replay the data:

 - First tick: ``OHLX``

 The ``Close`` will be replaced by the *average* of ``Open``, ``High``
 and ``Low``

 The session opening time is used for this tick

 and

 - Second tick: ``CCCC``

 The ``Close`` price will be used for the four components of the price

 The session closing time is used for this tick

 The volume will be split amongst the 2 ticks using the parameters:

 - ``closevol`` (default: ``0.5``) The value indicate which percentage, in
 absolute terms from 0.0 to 1.0, has to be assigned to the *closing*
 tick. The rest will be assigned to the ``OHLX`` tick.

 **This filter is meant to be used together with** ``cerebro.replaydata``

 '''
    params = (
        ('closevol', 0.5),  # 0 -> 1 amount of volume to keep for close
    )

    # replaying = True

    def __init__(self, data):
        self.lastdt = None

    def __call__(self, data):
        # Make a copy of the new bar and remove it from stream
        datadt = data.datetime.date()  # keep the date

        if self.lastdt == datadt:
            return False  # skip bars that come again in the filter

        self.lastdt = datadt  # keep ref to last seen bar

        # Make a copy of current data for ohlbar
        ohlbar = [data.lines[i][0] for i in range(data.size())]
        closebar = ohlbar[:]  # Make a copy for the close

        # replace close price with o-h-l average
        ohlprice = ohlbar[data.Open] + ohlbar[data.High] + ohlbar[data.Low]
        ohlbar[data.Close] = ohlprice / 3.0

        vol = ohlbar[data.Volume]  # adjust volume
        ohlbar[data.Volume] = vohl = int(vol * (1.0 - self.p.closevol))

        oi = ohlbar[data.OpenInterest]  # adjust open interst
        ohlbar[data.OpenInterest] = 0

        # Adjust times
        dt = datetime.datetime.combine(datadt, data.p.sessionstart)
        ohlbar[data.DateTime] = data.date2num(dt)

        # Ajust closebar to generate a single tick -> close price
        closebar[data.Open] = cprice = closebar[data.Close]
        closebar[data.High] = cprice
        closebar[data.Low] = cprice
        closebar[data.Volume] = vol - vohl
        ohlbar[data.OpenInterest] = oi

        # Adjust times
        dt = datetime.datetime.combine(datadt, data.p.sessionend)
        closebar[data.DateTime] = data.date2num(dt)

        # Update stream
        data.backwards(force=True)  # remove the copied bar from stream
        data._add2stack(ohlbar)  # add ohlbar to stack
        # Add 2nd part to stash to delay processing to next round
        data._add2stack(closebar, stash=True)

        return False  # initial tick can be further processed from stack` 
```
