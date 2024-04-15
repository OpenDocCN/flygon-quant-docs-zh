# 日期时间管理

> 原文：[`www.backtrader.com/docu/timemgmt/`](https://www.backtrader.com/docu/timemgmt/)

直到*1.5.0*版本发布之前，*backtrader*使用直接方法管理时间，即*数据源*计算的任何日期时间都会直接使用。

并且对于任何用户输入，例如*参数* `fromdate`（或`sessionstart`），都可以提供给任何*数据源*

在进行回测时，直接控制冻结数据源是可以的。在数据进入系统之前，很容易假设输入的日期时间已经得到处理。

但是在 1.5.0 版本中，**实时** *数据源*被支持了，这就需要考虑**日期时间管理**。如果以下情况始终为*true*，则不需要进行此类管理：

+   纽约的交易员交易 ES-Mini。时区为`US/Eastern`（或其中一个别名）

+   柏林的交易员交易 DAX 期货。在这种情况下，适用于`CET`（或`Europe/Berling`）时区

上述的直接输入输出日期时间方法将奏效，例如柏林的交易员可以始终这样做：

```py
`class Strategy(bt.Strategy):

    def next(self):

        # The DAX future opens at 08:00 CET
        if self.data.datetime.time() < datetime.time(8, 30):
            # don't operate until the market has been running 30 minutes
            return  #` 
```

直接方法的问题在于当同一位柏林的交易员决定交易`ES-Mini`时会出现。因为夏令时的变化在一年中的不同时间发生，这导致一年中有几周的时间差异不同步。以下情况不会总是奏效：

```py
`class Strategy(bt.Strategy):

    def next(self):

        # The SPX opens at 09:30 US/Eastern all year long
        # This is most of the year 15:30 CET
        # But it is sometimes 16:30 CET or 14:30 CET if a DST switch on-off
        # has happened in the USA and not in Europe

        # That's why the code below is unreliable

        if self.data.datetime.time() < datetime.time(16, 0):
            # don't operate until the market has been running 30 minutes
            return  #` 
```

## 时区操作

为了解决上述情况并仍然与直接输入输出时间方法兼容，`backtrader`为最终用户提供了以下选项

### 日期时间输入

+   作为默认情况下，平台不会修改数据源提供的*日期时间*

    +   最终用户可以通过以下方式覆盖此输入：

    +   为数据源提供一个`tzinput`参数。这必须是与`datetime.tzinfo`接口兼容的对象。最有可能用户会提供一个`pytz.timezone`实例

    做出这个决定后，`backtrader`内部使用的时间被认为是以`UTC-like`格式，即：

    +   如果数据源已经以`UTC`格式存储它

    +   通过`tzinput`转换后

    +   它并不真正是`UTC`，但它是用户的参考，因此是`UTC-like`

### 日期时间输出

+   如果数据源可以自动确定输出的时区，则这将是默认值

    这在直播信息源的情况下是有意义的，尤其是在柏林（`CET`时区）交易与`US/Eastern`时区的产品的情况下。

    因为交易员始终获得正确的时间，在上面的示例中，*开盘*时间保持恒定为`09:30 US/Eastern`，而不是大部分时间为`15:30 CET`，但有时为`16:30 CET`，有时为`14:30 CET`。

+   如果无法确定，则输出将是在输入时确定的任何内容（`UTC-like`）时间

+   最终用户可以覆盖并确定输出的实际时区

    +   为数据源提供一个`tz`参数。这必须是一个与`datetime.tzinfo`接口兼容的对象。最有可能的是用户会提供一个`pytz.timezone`实例。

注意

用户输入，比如例如`fromdate`或`sessionstart`参数，预计会与实际的`tz`同步，无论是由*数据源*自动计算、用户提供还是保持默认值（`None`，这意味着*datetime*的直接输入输出）。

考虑到这一切，让我们回想一下在`US/Eastern`时区进行交易的柏林交易员：

```py
`import pytz

import bt

data = bt.feeds.MyFeed('ES-Mini', tz=pytz.timezone('US/Eastern'))

class Strategy(bt.Strategy):

    def next(self):

        # This will work all year round.
        # The data source will return in the frame of the 'US/Eastern' time
        # zone and the user is quoting '10:00' as reference time
        # Because in the 'US/Eastern' timezone the SPX index always starts
        # trading at 09:30, this will always work

        if self.data.datetime.time() < datetime.time(10, 0):
            # don't operate until the market has been running 30 minutes
            return  #` 
```

对于*数据源*可以自动确定输出时区的情况：

```py
`import bt

data = bt.feeds.MyFeedAutoTZ('ES-Mini')

class Strategy(bt.Strategy):

    def next(self):

        # This will work all year round.
        # The data source will return in the frame of the 'US/Eastern' time
        # zone and the user is quoting '10:00' as reference time
        # Because in the 'US/Eastern' timezone the SPX index always starts
        # trading at 09:30, this will always work

        if self.data.datetime.time() < datetime.time(10, 0):
            # don't operate until the market has been running 30 minutes
            return  #` 
```

比以上工作还要少。

显然，上面示例中的`MyFeed`和`MyFeedAuto`只是虚拟名称。

注意

在撰写本文时，分发中唯一能够自动确定时区的数据源是连接到*交互经纪商*的那个。
