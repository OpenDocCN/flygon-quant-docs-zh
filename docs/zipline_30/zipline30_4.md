# 日历

> 原文：[`zipline.ml4trading.io/trading-calendars.html`](https://zipline.ml4trading.io/trading-calendars.html)

## 什么是交易日历？

交易日历代表单个市场交易所的时间信息。时间信息由两部分组成：时段和开/闭市时间。这由 Zipline 的`TradingCalendar`类表示，并作为所有新的`TradingCalendar`类的父类。

一个时段代表一组连续的分钟，并且有一个标签，该标签是 UTC 午夜。重要的是要注意，时段标签不应该被视为一个特定的时间点，而 UTC 午夜只是为了方便而使用。

对于[纽约证券交易所](https://www.nyse.com/index)的普通交易日，市场在上午 9:30 开市，下午 4:00 闭市。交易时段可能会根据交易所、一年中的某一天等因素而变化。

## 为什么你应该关注交易日历？

假设你想在周二买入某只股票，然后在周六卖出。如果你交易的那个股票所在的交易所周六不开放，那么在现实中你将无法在那个时间交易那只股票，你将不得不等到周六之后的某个其他天数。既然你无法在现实中进行交易，那么你的回测在周六进行交易也是不合理的。

为了让你能够回测你的策略，你的[数据包](https://zipline.ml4trading.io/bundles.html)中的日期和你的`TradingCalendar`中的日期应该匹配；如果日期不匹配，那么你将会在过程中遇到一些错误。这对分钟级和日级数据都适用。

## `TradingCalendar`类

`TradingCalendar`类有许多属性，如果我们需要为自己的交易所构建一个`TradingCalendar`，我们应该考虑这些属性。这些属性包括：

> +   交易所名称
> +   
> +   时区
> +   
> +   开市时间
> +   
> +   闭市时间
> +   
> +   常规与临时假日
> +   
> +   特殊开市与闭市

以及其他一些。如果你想查看`TradingCalendar` API 提供的所有属性和方法，请查看[API 参考](https://ml4t.zipline.io/api_reference.html#trading-calendar-api)。

现在我们将以伦敦证券交易所日历`LSEExchangeCalendar`为例进行说明：

```py
class LSEExchangeCalendar(TradingCalendar):
  """
 Exchange calendar for the London Stock Exchange

 Open Time: 8:00 AM, GMT
 Close Time: 4:30 PM, GMT

 Regularly-Observed Holidays:
 - New Years Day (observed on first business day on/after)
 - Good Friday
 - Easter Monday
 - Early May Bank Holiday (first Monday in May)
 - Spring Bank Holiday (last Monday in May)
 - Summer Bank Holiday (last Monday in May)
 - Christmas Day
 - Dec. 27th (if Christmas is on a weekend)
 - Boxing Day
 - Dec. 28th (if Boxing Day is on a weekend)
 """

  @property
  def name(self):
    return "LSE"

  @property
  def tz(self):
    return timezone('Europe/London')

  @property
  def open_time(self):
    return time(8, 1)

  @property
  def close_time(self):
    return time(16, 30)

  @property
  def regular_holidays(self):
    return HolidayCalendar([
      LSENewYearsDay,
      GoodFriday,
      EasterMonday,
      MayBank,
      SpringBank,
      SummerBank,
      Christmas,
      WeekendChristmas,
      BoxingDay,
      WeekendBoxingDay
    ]) 
```

你可以使用[pandas](https://pandas.pydata.org/pandas-docs/stable/)模块的`pandas.tseries.holiday.Holiday`来创建在`def regular_holidays(self)`中提到的`Holiday`对象。

以上面的[LSEExchangeCalendar](https://github.com/quantopian/zipline/blob/master/zipline/utils/calendars/exchange_calendar_lse.py)代码为例，同时也请查看下面的代码片段。

```py
from pandas.tseries.holiday import (
    Holiday,
    DateOffset,
    MO
)

SomeSpecialDay = Holiday(
    "Some Special Day",
    month=1,
    day=9,
    offset=DateOffSet(weekday=MO(-1))
) 
```

## 构建自定义交易日历

现在我们将构建我们自己的自定义交易日历。这个日历将用于交易可以在 24/7 交易所日历上交易的资产。这意味着它将在周一、周二、周三、周四、周五、周六和周日开放，交易所将在 12AM 开放，并在 11:59PM 关闭。我们将使用的时区是 UTC。

首先，我们将开始导入一些对我们有用的模块。

```py
# for setting our open and close times
from datetime import time
# for setting our start and end sessions
import pandas as pd
# for setting which days of the week we trade on
from pandas.tseries.offsets import CustomBusinessDay
# for setting our timezone
from pytz import timezone

# for creating and registering our calendar
from zipline.utils.calendar_utils import register_calendar, TradingCalendar
from zipline.utils.memoize import lazyval 
```

现在我们将实际构建这个日历，我们将其称为`TFSExchangeCalendar`。

```py
class TFSExchangeCalendar(TradingCalendar):
  """
 An exchange calendar for trading assets 24/7.

 Open Time: 12AM, UTC
 Close Time: 11:59PM, UTC
 """

  @property
  def name(self):
  """
 The name of the exchange, which Zipline will look for
 when we run our algorithm and pass TFS to
 the --trading-calendar CLI flag.
 """
    return "TFS"

  @property
  def tz(self):
  """
 The timezone in which we'll be running our algorithm.
 """
    return timezone("UTC")

  @property
  def open_time(self):
  """
 The time in which our exchange will open each day.
 """
    return time(0, 0)

  @property
  def close_time(self):
  """
 The time in which our exchange will close each day.
 """
    return time(23, 59)

  @lazyval
  def day(self):
  """
 The days on which our exchange will be open.
 """
    weekmask = "Mon Tue Wed Thu Fri Sat Sun"
    return CustomBusinessDay(
      weekmask=weekmask
    ) 
```

## 结论

为了让你能够使用这个日历运行你的算法，你需要有一个数据包，其中你的资产的日期涵盖了一周的所有天数。你可以在本文档的编写新包部分了解如何创建自己的数据包，或者使用[csvdir 包](https://github.com/stefan-jansen/zipline-reloaded/blob/master/zipline/data/bundles/csvdir.py)中的代码从 CSV 文件创建包。

## 什么是交易日历？

交易日历代表单个市场交易所的时间信息。时间信息由两部分组成：会话和开/关。这由 Zipline 的`TradingCalendar`类表示，并作为所有新的`TradingCalendar`类的父类使用。

一个会话代表一组连续的分钟，并且有一个标签是 UTC 午夜。重要的是要注意，会话标签不应该被视为一个特定的时间点，而 UTC 午夜只是为了方便而使用。

对于[纽约证券交易所](https://www.nyse.com/index)的普通交易日，市场在 9:30AM 开放，在 4PM 关闭。交易时段可能会根据交易所、一年中的某一天等而变化。

## 为什么你应该关心交易日历？

假设你想在周二购买某只股票的股份，然后在周六卖出。如果你交易的那个股票所在的交易所周六不开放，那么实际上在那个时间交易那只股票是不可能的，你将不得不等到周六之后的其他几天。由于你不能在现实中进行交易，因此你的回测在周六进行交易也是不合理的。

为了让你能够回测你的策略，你的[数据包](https://zipline.ml4trading.io/bundles.html)中的日期和你的`TradingCalendar`中的日期应该匹配；如果日期不匹配，那么你将会在过程中遇到一些错误。这对分钟数据和日数据都适用。

## TradingCalendar 类

`TradingCalendar`类有许多属性，如果我们想为交易所构建自己的`TradingCalendar`，我们应该考虑这些属性。这些属性包括：

> +   交易所名称
> +   
> +   时区
> +   
> +   开放时间
> +   
> +   关闭时间
> +   
> +   常规和特别假日
> +   
> +   特别开放和关闭

以及其他几个。如果您想查看`TradingCalendar` API 提供的所有属性和方法，请查看[API 参考](https://ml4t.zipline.io/api_reference.html#trading-calendar-api)。

现在我们将以下面的伦敦证券交易所日历`LSEExchangeCalendar`为例：

```py
class LSEExchangeCalendar(TradingCalendar):
  """
 Exchange calendar for the London Stock Exchange

 Open Time: 8:00 AM, GMT
 Close Time: 4:30 PM, GMT

 Regularly-Observed Holidays:
 - New Years Day (observed on first business day on/after)
 - Good Friday
 - Easter Monday
 - Early May Bank Holiday (first Monday in May)
 - Spring Bank Holiday (last Monday in May)
 - Summer Bank Holiday (last Monday in May)
 - Christmas Day
 - Dec. 27th (if Christmas is on a weekend)
 - Boxing Day
 - Dec. 28th (if Boxing Day is on a weekend)
 """

  @property
  def name(self):
    return "LSE"

  @property
  def tz(self):
    return timezone('Europe/London')

  @property
  def open_time(self):
    return time(8, 1)

  @property
  def close_time(self):
    return time(16, 30)

  @property
  def regular_holidays(self):
    return HolidayCalendar([
      LSENewYearsDay,
      GoodFriday,
      EasterMonday,
      MayBank,
      SpringBank,
      SummerBank,
      Christmas,
      WeekendChristmas,
      BoxingDay,
      WeekendBoxingDay
    ]) 
```

您可以使用[pandas](https://pandas.pydata.org/pandas-docs/stable/)模块`pandas.tseries.holiday.Holiday`创建在`def regular_holidays(self)`中提到的`Holiday`对象。

请查看上面的[LSEExchangeCalendar](https://github.com/quantopian/zipline/blob/master/zipline/utils/calendars/exchange_calendar_lse.py)代码作为示例，以及下面的代码片段。

```py
from pandas.tseries.holiday import (
    Holiday,
    DateOffset,
    MO
)

SomeSpecialDay = Holiday(
    "Some Special Day",
    month=1,
    day=9,
    offset=DateOffSet(weekday=MO(-1))
) 
```

## 构建自定义交易日历

现在，我们将构建我们自己的自定义交易日历。该日历将用于交易可以在 24/7 交易平台上交易的资产。这意味着它将在周一、周二、周三、周四、周五、周六和周日开放，交易平台将在凌晨 12 点开放，晚上 11:59 关闭。我们将使用的时区是 UTC。

首先，我们将导入一些对我们有用的模块。

```py
# for setting our open and close times
from datetime import time
# for setting our start and end sessions
import pandas as pd
# for setting which days of the week we trade on
from pandas.tseries.offsets import CustomBusinessDay
# for setting our timezone
from pytz import timezone

# for creating and registering our calendar
from zipline.utils.calendar_utils import register_calendar, TradingCalendar
from zipline.utils.memoize import lazyval 
```

现在我们将实际构建这个日历，我们将其称为`TFSExchangeCalendar`：

```py
class TFSExchangeCalendar(TradingCalendar):
  """
 An exchange calendar for trading assets 24/7.

 Open Time: 12AM, UTC
 Close Time: 11:59PM, UTC
 """

  @property
  def name(self):
  """
 The name of the exchange, which Zipline will look for
 when we run our algorithm and pass TFS to
 the --trading-calendar CLI flag.
 """
    return "TFS"

  @property
  def tz(self):
  """
 The timezone in which we'll be running our algorithm.
 """
    return timezone("UTC")

  @property
  def open_time(self):
  """
 The time in which our exchange will open each day.
 """
    return time(0, 0)

  @property
  def close_time(self):
  """
 The time in which our exchange will close each day.
 """
    return time(23, 59)

  @lazyval
  def day(self):
  """
 The days on which our exchange will be open.
 """
    weekmask = "Mon Tue Wed Thu Fri Sat Sun"
    return CustomBusinessDay(
      weekmask=weekmask
    ) 
```

## 结论

为了使您的算法能够使用此日历运行，您需要拥有一个数据包，其中您的资产日期涵盖了一周的所有天数。您可以在本文档的编写新包部分了解如何创建自己的数据包，或者使用[csvdir 包](https://github.com/stefan-jansen/zipline-reloaded/blob/master/zipline/data/bundles/csvdir.py)中的代码从 CSV 文件创建包。
