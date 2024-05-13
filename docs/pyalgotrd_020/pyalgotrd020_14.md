# 工具

> 原文：[`gbeced.github.io/pyalgotrade/docs/v0.20/html/tools.html`](https://gbeced.github.io/pyalgotrade/docs/v0.20/html/tools.html)

## Quandl

`pyalgotrade.tools.quandl.``build_feed`（*sourceCode*，*tableCodes*，*fromYear*，*toYear*，*storage*，*frequency=86400*，*timezone=None*，*skipErrors=False*，*authToken=None*，*columnNames={}*，*forceDownload=False*，*skipMalformedBars=False*）

使用从 Quandl 下载的 CSV 文件构建和加载 `pyalgotrade.barfeed.quandlfeed.Feed`。如果以前未下载过 CSV 文件，则会下载它们。

| 参数: |
| --- |

+   **sourceCode**（*字符串.*）- 数据集来源代码。

+   **tableCodes**（*列表.*）- 数据集表代码。

+   **fromYear**（*整数.*）- 第一年。

+   **toYear**（*整数.*）- 最后一年。

+   **storage**（*字符串.*）- 文件加载或下载的路径。

+   **frequency** – bars 的频率。仅支持 **pyalgotrade.bar.Frequency.DAY** 或 **pyalgotrade.bar.Frequency.WEEK**。

+   **timezone**（*pytz 时区.*）- 用于本地化 bars 的默认时区。请检查 `pyalgotrade.marketsession`。

+   **skipErrors**（*布尔值.*）- 如果发生错误，继续加载/下载文件。

+   **authToken**（*字符串.*）- 可选。如果每天的调用次数超过 50 次，则需要身份验证令牌。

+   **columnNames**（*字典.*）-

    可选。一个字典，用于映射列名。有效的键值为：

    +   datetime

    +   open

    +   high

    +   low

    +   close

    +   volume

    +   adj_close

+   **skipMalformedBars**（*布尔值.*）- 如果在解析 bars 时出现错误，则为 True。

|

| 返回类型: | `pyalgotrade.barfeed.quandlfeed.Feed`。 |
| --- | --- |

`pyalgotrade.tools.quandl.``download_daily_bars`（*sourceCode*，*tableCode*，*year*，*csvFile*，*authToken=None*）

从 Quandl 下载给定年份的每日 bars。

| 参数: |
| --- |

+   **sourceCode**（*字符串.*）- 数据集的来源代码。

+   **tableCode**（*字符串.*）- 数据集的表代码。

+   **year**（*整数.*）- 年份。

+   **csvFile**（*字符串.*）- 要写入的 CSV 文件的路径。

+   **authToken**（*字符串.*）- 可选。如果每天的调用次数超过 50 次，则需要身份验证令牌。

|

`pyalgotrade.tools.quandl.``download_weekly_bars`（*sourceCode*，*tableCode*，*year*，*csvFile*，*authToken=None*）

从 Quandl 下载给定年份的每周 bars。

| 参数: |
| --- |

+   **sourceCode**（*字符串.*）- 数据集的来源代码。

+   **tableCode**（*字符串.*）- 数据集的表代码。

+   **year**（*整数.*）- 年份。

+   **csvFile**（*字符串.*）- 要写入的 CSV 文件的路径。

+   **authToken**（*字符串.*）- 可选。如果每天的调用次数超过 50 次，则需要身份验证令牌。

|  ## BarFeed 重新采样

`pyalgotrade.tools.resample.``resample_to_csv`（*barFeed*，*frequency*，*csvFile*）

将 BarFeed 重采样为 CSV 文件，按照一定频率对 bars 进行分组。生成的文件可以使用 `pyalgotrade.barfeed.csvfeed.GenericBarFeed` 进行加载。CSV 文件的格式如下：

```py
Date Time,Open,High,Low,Close,Volume,Adj Close
2013-01-01 00:00:00,13.51001,13.56,13.51,13.56,273.88014126,13.51001
```

| 参数: |
| --- |

+   **barFeed** (`pyalgotrade.barfeed.BarFeed`) – 将提供数据的 bar feed。它应该只包含单个工具的数据。

+   **frequency** – 分组的频率，单位为秒。必须 > 0。

+   **csvFile** (*字符串.*) – 要写入的 CSV 文件路径。

|

注意

+   时间将不包含时区信息。

+   如果输入的 bar feed 没有该信息，则**Adj Close** 列可能为空。

+   支持的重采样频率包括：

    +   少于 bar.Frequency.DAY

    +   bar.Frequency.DAY

    +   bar.Frequency.MONTH

### 目录

+   工具

    +   Quandl

    +   BarFeed 重采样

