# dataseries – 基本数据系列类

> 原文：[`gbeced.github.io/pyalgotrade/docs/v0.20/html/dataseries.html`](https://gbeced.github.io/pyalgotrade/docs/v0.20/html/dataseries.html)

数据系列是用于管理时间序列数据的抽象。

*class* `pyalgotrade.dataseries.``DataSeries`

基类：`object`

数据系列的基类。

注意

这是一个基类，不应直接使用。

`__getitem__`(*key*)

返回给定位置/切片的值。如果位置无效，则引发 IndexError，如果键类型无效，则引发 TypeError。

`__len__`()

返回数据系列中的元素数量。

`getDateTimes`()

返回与每个值关联的 `datetime.datetime` 列表。

*class* `pyalgotrade.dataseries.``SequenceDataSeries`(*maxLen=None*)

基类：`pyalgotrade.dataseries.DataSeries`

一个在内存中按顺序保存值的 DataSeries。

| 参数： | **maxLen** (*int.*) – 要保存的最大值数量。一旦有界长度已满，当添加新项时，将从相反端丢弃相应数量的项。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。 |
| --- | --- |

`append`(*value*)

追加一个值。

`appendWithDateTime`(*dateTime*, *value*)

追加一个带有关联日期时间的值。

注意

如果 dateTime 不为 None，则必须大于最后一个。

`getMaxLen`()

返回要保存的最大值数量。

`setMaxLen`(*maxLen*)

设置要保存的最大值数量，并在必要时调整大小。

`pyalgotrade.dataseries.aligned.``datetime_aligned`(*ds1*, *ds2*, *maxLen=None*)

返回两个 DataSeries，其中仅包含两个 DataSeries 中都存在的日期时间的值。

| 参数： |
| --- |

+   **ds1** (`DataSeries`.) – 一个 DataSeries 实例。

+   **ds2** (`DataSeries`.) – 一个 DataSeries 实例。

+   **maxLen** (*int.*) – 返回的 `DataSeries` 可以容纳的最大值数量。一旦有界长度已满，当添加新项时，将从相反端丢弃相应数量的项。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

*class* `pyalgotrade.dataseries.bards.``BarDataSeries`(*maxLen=None*)

基类：`pyalgotrade.dataseries.SequenceDataSeries`

一个由 `pyalgotrade.bar.Bar` 实例组成的 DataSeries。

| 参数： | **maxLen** (*int.*) – 要保存的最大值数量。一旦有界长度已满，当添加新项时，将从相反端丢弃相应数量的项。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。 |
| --- | --- |

`getAdjCloseDataSeries`()

返回一个具有调整后的收盘价格的 `pyalgotrade.dataseries.DataSeries`。

`getCloseDataSeries`()

返回一个`pyalgotrade.dataseries.DataSeries`，其中包含收盘价格。

`getExtraDataSeries`（*name*）

返回一个`pyalgotrade.dataseries.DataSeries`，用于额外的列。

`getHighDataSeries`（）

返回一个`pyalgotrade.dataseries.DataSeries`，其中包含最高价格。

`getLowDataSeries`（）

返回一个`pyalgotrade.dataseries.DataSeries`，其中包含最低价格。

`getOpenDataSeries`（）

返回一个`pyalgotrade.dataseries.DataSeries`，其中包含开盘价格。

`getPriceDataSeries`（）

返回一个`pyalgotrade.dataseries.DataSeries`，其中包含收盘或调整后的收盘价格。

`getVolumeDataSeries`（）

返回一个`pyalgotrade.dataseries.DataSeries`，其中包含交易量。

*class* `pyalgotrade.dataseries.resampled.` `ResampledBarDataSeries`（*dataSeries*，*frequency*，*maxLen=None*）

基础：`pyalgotrade.dataseries.bards.BarDataSeries`，`pyalgotrade.dataseries.resampled.DSResampler`

一个 BarDataSeries，将建立在另一个更高频率的 BarDataSeries 之上。随着新值被推送到被重新采样的数据系列中，重新采样将会发生。

| 参数： |
| --- |

+   **dataSeries**（`pyalgotrade.dataseries.bards.BarDataSeries`） - 正在重新采样的 DataSeries 实例。

+   **frequency** - 以秒为单位的分组频率。必须大于 0。

+   **maxLen**（*int.*） - 最大保存值的数量。一旦有限长度已满，当添加新项目时，将从另一端丢弃相应数量的项目。

|

注：

+   支持的重新采样频率包括：

    +   小于 bar.Frequency.DAY

    +   bar.Frequency.DAY

    +   bar.Frequency.MONTH

`checkNow`（*dateTime*）

强制进行重新采样检查。根据重新采样频率和当前日期时间，可能会生成一个新值。

| 参数： | **dateTime**（`datetime.datetime`） - 当前日期时间。 |
| --- | --- |

