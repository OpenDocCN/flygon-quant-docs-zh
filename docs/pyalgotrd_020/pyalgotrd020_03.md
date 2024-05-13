# 条形 – 工具价格

> 原文：[`gbeced.github.io/pyalgotrade/docs/v0.20/html/bar.html`](https://gbeced.github.io/pyalgotrade/docs/v0.20/html/bar.html)

*class* `pyalgotrade.bar.``Frequency`

基类: `object`

枚举类似于条形频率。有效值为：

+   **Frequency.TRADE**: 每个条形代表一次交易。

+   **Frequency.SECOND**: 每个条形总结了一秒的交易活动。

+   **Frequency.MINUTE**: 每个条形总结了一分钟的交易活动。

+   **Frequency.HOUR**: 每个条形总结了一小时的交易活动。

+   **Frequency.DAY**: 每个条形总结了一天的交易活动。

+   **Frequency.WEEK**: 每个条形总结了一周的交易活动。

+   **Frequency.MONTH**: 每个条形总结了一月的交易活动。

*class* `pyalgotrade.bar.``Bar`

基类: `object`

条形是给定期间内安全性交易活动的摘要。

注意

这是一个基类，不应直接使用。

`getDateTime`()

返回`datetime.datetime`。

`getOpen`(*adjusted=False*)

返回开盘价。

`getHigh`(*adjusted=False*)

返回最高价格。

`getLow`(*adjusted=False*)

返回最低价格。

`getClose`(*adjusted=False*)

返回收盘价。

`getVolume`()

返回成交量。

`getAdjClose`()

返回调整后的收盘价。

`getFrequency`()

条形的周期。

`getTypicalPrice`()

返回典型价格。

`getPrice`()

返回收盘或调整后的收盘价。

*class* `pyalgotrade.bar.``Bars`(*barDict*)

基类: `object`

一组`Bar`对象。

| 参数: | **barDict** (*map.*) – 工具到`Bar`对象的映射。 |
| --- | --- |

注意

所有条形必须具有相同的日期时间。

`__getitem__`(*instrument*)

返回给定工具的`pyalgotrade.bar.Bar`。如果未找到该工具，则会引发异常。

`__contains__`(*instrument*)

如果给定工具的`pyalgotrade.bar.Bar`可用，则返回 True。

`getInstruments`()

返回工具符号。

`getDateTime`()

返回此条形的`datetime.datetime`。

`getBar`(*instrument*)

返回给定工具的`pyalgotrade.bar.Bar`或者如果未找到该工具则返回 None。

