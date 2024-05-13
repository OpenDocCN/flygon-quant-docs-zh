# barfeed – 条形提供者

> 原文：[`gbeced.github.io/pyalgotrade/docs/v0.20/html/barfeed.html`](https://gbeced.github.io/pyalgotrade/docs/v0.20/html/barfeed.html)

*class* `pyalgotrade.barfeed.` `BaseBarFeed`(*frequency*, *maxLen=None*)

基类：`pyalgotrade.feed.BaseFeed`

提供源的 `pyalgotrade.bar.Bar` 的基类。

| 参数: |
| --- |

+   **frequency** – 条的频率。在`pyalgotrade.bar.Frequency`中定义的有效值。

+   **maxLen** (*int.*) – `pyalgotrade.dataseries.bards.BarDataSeries` 将保存的值的最大数量。一旦有限长度满了，当添加新项目时，相应数量的项目将从对立端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

注

这是一个基类，不应直接使用。

`getNextBars`()

重写以返回源中的下一个 `pyalgotrade.bar.Bars` ，如果没有条则返回 None。

注

这是给 BaseBarFeed 子类的，不应直接调用。

`getCurrentBars`()

返回当前的`pyalgotrade.bar.Bars`。

`getLastBar`(*instrument*)

返回给定仪器的最后一个 `pyalgotrade.bar.Bar` ，或 None。

`getDefaultInstrument`()

返回最后一个注册的仪器。

`getRegisteredInstruments`()

返回注册的仪器名称列表。

`getDataSeries`(*instrument=None*)

返回给定仪器的 `pyalgotrade.dataseries.bards.BarDataSeries`。

| 参数: | **instrument** (*string.*) – 仪器标识符。如果为 None，则返回默认仪器。 |
| --- | --- |
| 返回类型: | `pyalgotrade.dataseries.bards.BarDataSeries`。 |

## CSV

*class* `pyalgotrade.barfeed.csvfeed.` `BarFeed`(*frequency*, *maxLen=None*)

基类：`pyalgotrade.barfeed.membf.BarFeed`

基类为基于 CSV 文件的 `pyalgotrade.barfeed.BarFeed`。

注

这是一个基类，不应直接使用。

*class* `pyalgotrade.barfeed.csvfeed.` `GenericBarFeed`(*frequency*, *timezone=None*, *maxLen=None*)

基类：`pyalgotrade.barfeed.csvfeed.BarFeed`

一个从 CSV 文件加载条的 BarFeed，格式如下：

```py
Date Time,Open,High,Low,Close,Volume,Adj Close
2013-01-01 13:59:00,13.51001,13.56,13.51,13.56,273.88014126,13.51001
```

| 参数: |
| --- |

+   **frequency** – 条的频率。检查`pyalgotrade.bar.Frequency`。

+   **timezone** (*A pytz timezone.*) – 用于本地化条形图的默认时区。请检查 `pyalgotrade.marketsession`。

+   **maxLen** (*int.*) – `pyalgotrade.dataseries.bards.BarDataSeries` 将保存的值的最大数量。一旦有界长度满了，当添加新项时，相应数量的项将从另一端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

注意

+   CSV 文件**必须**在第一行中具有列名。

+   如果**Adj Close**列为空，也没关系。

+   当使用多个工具时：

> +   如果加载的所有工具都位于相同的时区，则可能不需要指定时区参数。
> +   
> +   如果加载的任何工具位于不同的时区，则应设置时区参数。

`addBarsFromCSV`(*instrument*, *path*, *timezone=None*, *skipMalformedBars=False*)

从 CSV 格式文件中加载给定工具的条形图。该工具将在条形图数据源中注册。

| 参数： |
| --- |

+   **instrument** (*string.*) – 工具标识符。

+   **path** (*string.*) – CSV 文件的路径。

+   **timezone** (*A pytz timezone.*) – 用于本地化条形图的时区。请检查 `pyalgotrade.marketsession`。

+   **skipMalformedBars** (*boolean.*) – 设置为 True 以跳过解析条形图时的错误。

|

`setDateTimeFormat`(*dateTimeFormat*)

设置要与 strptime 一起使用的格式字符串以解析日期时间列。## 雅虎财经

*class* `pyalgotrade.barfeed.yahoofeed.``Feed`(*frequency=86400*, *timezone=None*, *maxLen=None*)

基类：`pyalgotrade.barfeed.csvfeed.BarFeed`

从从雅虎财经下载的 CSV 文件中加载条形图的`pyalgotrade.barfeed.csvfeed.BarFeed`。

| 参数： |
| --- |

+   **frequency** – 条形图的频率。仅支持 **pyalgotrade.bar.Frequency.DAY** 或 **pyalgotrade.bar.Frequency.WEEK**。

+   **timezone** (*A pytz timezone.*) – 用于本地化条形图的默认时区。请检查 `pyalgotrade.marketsession`。

+   **maxLen** (*int.*) – `pyalgotrade.dataseries.bards.BarDataSeries` 将保存的值的最大数量。一旦有界长度满了，当添加新项时，相应数量的项将从另一端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

注意

雅虎财经 CSV 文件缺乏时区信息。当使用多个工具时：

> +   如果加载的所有仪器都在同一个时区，则可能不需要指定时区参数。
> +   
> +   如果加载的任何仪器处于不同的时区，则必须设置时区参数。

`addBarsFromCSV`(*instrument*, *path*, *timezone=None*)

从 CSV 格式文件中加载给定仪器的条形图。该仪器将在条形图 feed 中注册。

| 参数: |
| --- |

+   **instrument** (*string.*) – 仪器标识符。

+   **path** (*string.*) – CSV 文件的路径。

+   **timezone** (*一个 pytz 时区.*) – 用于本地化条形图的时区。查看`pyalgotrade.marketsession`。

|  ## Google Finance

*class* `pyalgotrade.barfeed.googlefeed.``Feed`(*frequency=86400*, *timezone=None*, *maxLen=None*)

Bases: `pyalgotrade.barfeed.csvfeed.BarFeed`

从 Google Finance 下载的 CSV 文件加载条形图的`pyalgotrade.barfeed.csvfeed.BarFeed`。

| 参数: |
| --- |

+   **frequency** – 条形图的频率。目前仅支持**pyalgotrade.bar.Frequency.DAY**。

+   **timezone** (*一个 pytz 时区.*) – 用于本地化条形图的默认时区。查看`pyalgotrade.marketsession`。

+   **maxLen** (*int.*) – `pyalgotrade.dataseries.bards.BarDataSeries`将保存的值的最大数量。一旦达到有界长度，当添加新项时，相应数量的项将从另一端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

注意

Google Finance csv 文件缺少时区信息。在处理多个仪器时:

> +   如果加载的所有仪器都在同一个时区，则可能不需要指定时区参数。
> +   
> +   如果加载的任何仪器处于不同的时区，则必须设置时区参数。

`addBarsFromCSV`(*instrument*, *path*, *timezone=None*, *skipMalformedBars=False*)

从 CSV 格式文件中加载给定仪器的条形图。该仪器将在条形图 feed 中注册。

| 参数: |
| --- |

+   **instrument** (*string.*) – 仪器标识符。

+   **path** (*string.*) – CSV 文件的路径。

+   **timezone** (*一个 pytz 时区.*) – 用于本地化条形图的时区。查看`pyalgotrade.marketsession`。

+   **skipMalformedBars** (*boolean.*) – 如果在解析条形图时跳过错误，则为 True。

|  ## Quandl

*class* `pyalgotrade.barfeed.quandlfeed.``Feed`(*frequency=86400*, *timezone=None*, *maxLen=None*)

Bases: `pyalgotrade.barfeed.csvfeed.GenericBarFeed`

从 Quandl 下载的 CSV 文件加载 bars 的 `pyalgotrade.barfeed.csvfeed.BarFeed`。

| 参数： |
| --- |

+   **frequency** – bars 的频率。仅支持 **pyalgotrade.bar.Frequency.DAY** 或 **pyalgotrade.bar.Frequency.WEEK**。

+   **timezone**（*一个 pytz 时区。*）– 用于本地化 bars 的默认时区。请查看 `pyalgotrade.marketsession`。

+   **maxLen**（*整数。*）– `pyalgotrade.dataseries.bards.BarDataSeries` 将保存的值的最大数量。一旦有限长度满了，当添加新项时，将从另一端丢弃相应数量的项。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。 

|

注意

在处理多个仪器时：

> +   如果加载的所有仪器都位于同一时区，则可能不需要指定时区参数。
> +   
> +   如果加载的任何仪器位于不同的时区，则必须设置时区参数。 ## Ninja Trader

*类* `pyalgotrade.barfeed.ninjatraderfeed.` `Feed`（*frequency*，*timezone=None*，*maxLen=None*）

基类：`pyalgotrade.barfeed.csvfeed.BarFeed`

一个从 NinjaTrader 导出的 CSV 文件加载 bars 的 `pyalgotrade.barfeed.csvfeed.BarFeed`。

| 参数： |
| --- |

+   **frequency** – bars 的频率。仅支持 **pyalgotrade.bar.Frequency.MINUTE** 或 **pyalgotrade.bar.Frequency.DAY**。

+   **timezone**（*一个 pytz 时区。*）– 用于本地化 bars 的默认时区。请查看 `pyalgotrade.marketsession`。

+   **maxLen**（*整数。*）– `pyalgotrade.dataseries.bards.BarDataSeries` 将保存的值的最大数量。一旦有限长度满了，当添加新项时，将从另一端丢弃相应数量的项。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。

|

`addBarsFromCSV`（*instrument*，*path*，*timezone=None*）

从 CSV 格式文件加载给定仪器的 bars。该仪器将在 bar feed 中注册。

| 参数： |
| --- |

+   **instrument**（*字符串。*）– 仪器标识符。

+   **path**（*字符串。*）– 文件的路径。

+   **timezone**（*一个 pytz 时区。*）– 用于本地化 bars 的时区。请查看 `pyalgotrade.marketsession`。

|

### 目录

+   barfeed – Bar providers

    +   CSV

    +   Yahoo! Finance

    +   谷歌财经

    +   Quandl

    +   忍者交易员

#### 上一个主题

feed – 基本数据源

#### 下一个主题

技术指标 – 技术指标

### 此页

+   显示源代码

### 快速搜索

输入搜索词或模块、类或函数名称。

### 导航

+   索引

+   模块 |

+   下一章 |

+   上一章 |

+   PyAlgoTrade 0.20 文档 »

+   代码文档 »
