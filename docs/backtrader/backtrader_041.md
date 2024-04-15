# 数据源参考

> 译文：[`www.backtrader.com/docu/dataautoref/`](https://www.backtrader.com/docu/dataautoref/)

### AbstractDataBase

行：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)` 
```

### BacktraderCSVData

解析用于测试的自定义 CSV 数据。

特定参数：

+   `dataname`：要解析的文件名或类似文件的对象

行：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)

* headers (True)

* separator (,)` 
```

### CSVDataBase

实现 CSV 数据源的类的基类

该类负责打开文件，读取行并对其进行标记

子类只需要覆盖：

+   _loadline(tokens)

`_loadline`的返回值（True/False）将是由此基类覆盖的`_load`的返回值

行：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)

* headers (True)

* separator (,)` 
```

### Chainer

链接数据的类

行：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)` 
```

### DataClone

行：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)` 
```

### DataFiller

此类将使用来自基础数据源的以下信息位填充源数据中的间隙

+   时间框架和压缩以确定输出条的维度

+   sessionstart 和 sessionend

如果数据源在 10:31 和 10:34 之间缺少���，且时间框架为分钟，则输出将使用最后一条条的收盘价（10:31）填充 10:32 和 10:33 分钟的条

条可能会缺失，因为其他原因

参数：

```py
`* `fill_price` (def: None): if None (or evaluates to False),the
  closing price will be used, else the passed value (which can be
  for example ‘NaN’ to have a missing bar in terms of evaluation but
  present in terms of time

* `fill_vol` (def: NaN): used to fill the volume of missing bars

* `fill_oi` (def: NaN): used to fill the openinterest of missing bars` 
```

行：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)

* fill_price (None)

* fill_vol (nan)

* fill_oi (nan)` 
```

### DataFilter

此类从给定数据源中过滤条。除了 DataBase 的标准参数外，它还接受一个`funcfilter`参数，该参数可以是任何可调用对象

逻辑：

+   `funcfilter`将与基础数据源一起调用

    它可以是任何可调用对象

    +   返回值 `True`：将使用当前数据源的条形值

    +   返回值 `False`：将丢弃当前数据源的条形值

行：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)

* funcfilter (None)` 
```

### GenericCSVData

根据参数定义的顺序和字段存在性解析 CSV 文件

特定参数（或特定含义）：

+   `dataname`：要解析的文件名或类似文件的对象

+   行参数（日期时间，开盘价，最高价...）采用数值

    -1 的值表示 CSV 源中该字段的缺失

+   如果`time`存在（参数 time >=0），则源包含分开的日期和时间字段，这些字段将被组合

+   `nullvalue`

    如果应该存在的值缺失（CSV 字段为空），将使用的值

+   `dtformat`：用于解析日期时间 CSV 字段的格式。有关格式，请参阅 python strptime/strftime 文档。

    如果指定了数值，则将按以下方式解释

    +   `1`：值是`int`类型的 Unix 时间戳，表示自 1970 年 1 月 1 日起的秒数

    +   `2`：值是`float`类型的 Unix 时间戳

    如果传递了**可调用**对象

    +   它将接受一个字符串并返回一个 datetime.datetime 的 python 实例

+   `tmformat`：如果“存在”，则用于解析时间 CSV 字段的格式（“时间”CSV 字段的默认值是不存在）

行：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)

* headers (True)

* separator (,)

* nullvalue (nan)

* dtformat (%Y-%m-%d %H:%M:%S)

* tmformat (%H:%M:%S)

* datetime (0)

* time (-1)

* open (1)

* high (2)

* low (3)

* close (4)

* volume (5)

* openinterest (6)` 
```

### IBData

交互经纪人数据源。

支持参数`dataname`中的以下合同规范：

+   TICKER # 股票类型和 SMART 交易所

+   TICKER-STK # 股票和 SMART 交易所

+   TICKER-STK-EXCHANGE # 股票

+   TICKER-STK-EXCHANGE-CURRENCY # 股票

+   TICKER-CFD # 差价合约和 SMART 交易所

+   TICKER-CFD-EXCHANGE # 差价合约

+   TICKER-CDF-EXCHANGE-CURRENCY # 股票

+   TICKER-IND-EXCHANGE # 指数

+   TICKER-IND-EXCHANGE-CURRENCY # 指数

+   TICKER-YYYYMM-EXCHANGE # 期货

+   TICKER-YYYYMM-EXCHANGE-CURRENCY # 期货

+   TICKER-YYYYMM-EXCHANGE-CURRENCY-MULT # 期货

+   TICKER-FUT-EXCHANGE-CURRENCY-YYYYMM-MULT # 期货

+   TICKER-YYYYMM-EXCHANGE-CURRENCY-STRIKE-RIGHT # 期权

+   TICKER-YYYYMM-EXCHANGE-CURRENCY-STRIKE-RIGHT-MULT # 期权

+   TICKER-FOP-EXCHANGE-CURRENCY-YYYYMM-STRIKE-RIGHT # 期权

+   TICKER-FOP-EXCHANGE-CURRENCY-YYYYMM-STRIKE-RIGHT-MULT # 期权

+   CUR1.CUR2-CASH-IDEALPRO # 外汇

+   TICKER-YYYYMMDD-EXCHANGE-CURRENCY-STRIKE-RIGHT # 期权

+   TICKER-YYYYMMDD-EXCHANGE-CURRENCY-STRIKE-RIGHT-MULT # 期权

+   TICKER-OPT-EXCHANGE-CURRENCY-YYYYMMDD-STRIKE-RIGHT # 期权

+   TICKER-OPT-EXCHANGE-CURRENCY-YYYYMMDD-STRIKE-RIGHT-MULT # 期权

参数：

+   `sectype`（默认值：`STK`）

    如果在`dataname`规范中未提供，应用的默认值为*security type*

+   `exchange`（默认值：`SMART`）

    如果在`dataname`规范中未提供，应用的默认值为*exchange*

+   `currency`（默认值：`''`）

    如果在`dataname`规范中未提供，应用的默认值为*currency*

+   `historical`（默认值：`False`）

    如果设置为`True`，数据源将在首次下载数据后停止。

    标准数据源参数`fromdate`和`todate`将被用作参考。

    如果请求的持续时间大于 IB 根据所选数据的时间框架/压缩允许的持续时间，则数据源将进行多次请求。

+   `what`（默认值：`None`）

    如果为`None`，则将为历史数据请求使用不同资产类型的默认值：

    +   对于现金资产‘BID’

    +   对于任何其他‘交易’

    如果希望使用其他值，请查阅 IB API 文档

+   `rtbar`（默认值：`False`）

    如果设置为`True`，则使用 Interactive Brokers 提供的`5 秒实时数据条`作为最小跳动。根据文档，它们对应实时值（一旦被 IB 收集和整理）

    如果为`False`，则将使用基于接收到的跳动的`RTVolume`价格。在`CASH`资产的情况下（例如 EUR.JPY），将始终使用`RTVolume`，并从中获取`bid`价格（根据散布在互联网上的文献，这是 IB 的行业事实标准）

    即使设置为`True`，如果数据被重新采样/保持到低于 Seconds/5 的时间框架/压缩，将不会使用实时条，因为 IB 不会在该水平以下提供它们

+   `qcheck`（默认值：`0.5`）

    以秒为单位的时间，如果未收到数据，则唤醒以正确重新采样/回放数据包并将通知传递给链路上游的机会

+   `backfill_start`（默认值：`True`）

    在开始时执行回填。将在单个请求中获取最大可能的历史数据。

+   `backfill`（默认值：`True`）

    在断开/重新连接周期后执行回填。间隙持续时间将用于下载尽可能少的数据量

+   `backfill_from`（默认值：`None`）

    可以传递一个额外的数据源来进行初始层次的回填。一旦数据源耗尽并且如果需要，将从 IB 进行回填。理想情况下，这是为了从已存储的源（如磁盘上的文件）回填，但不限于此。

+   `latethrough`（默认：`False`）

    如果数据源被重新采样/回放，一些 ticks 可能会因为已经交付的重新采样/回放的 bar 而来得太晚。如果这是 `True`，那些 ticks 将无论如何都会被放过。

    检查 Resampler 文档以查看如何考虑这些 ticks。

    如果在 `IBStore` 实例中设置 `timeoffset` 为 `False`，并且 TWS 服务器时间与本地计算机时间不同步，则可能会发生这种情况。

+   `tradename`（默认：`None`）对某些特定情况（如 `CFD`，其中价格由一种资产提供，交易发生在另一种资产上）很有用

    +   SPY-STK-SMART-USD -> SP500 ETF（将被指定为 `dataname`）

    +   SPY-CFD-SMART-USD -> 这是对应的 CFD，它提供的不是价格跟踪，而是在这种情况下将是交易资产（指定为 `tradename`）

参数中的默认值是为了允许类似 `\`TICKER` 这样的东西，其中参数 `sectype`（默认：`STK`）和 `exchange`（默认：`SMART`）被应用。

一些资产如 `AAPL` 需要包括 `currency` 的完整规范（默认：‘’），而像 `TWTR` 这样的其他资产可以直接传递。

+   `AAPL-STK-SMART-USD` 是 dataname 的完整规范。

    或者：`IBData` 作为 `IBData(dataname='AAPL', currency='USD')`，它使用默认值（`STK` 和 `SMART`）并覆盖货币为 `USD`

Lines:

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.5)

* calendar (None)

* sectype (STK)

* exchange (SMART)

* currency ()

* rtbar (False)

* historical (False)

* what (None)

* useRTH (False)

* backfill_start (True)

* backfill (True)

* backfill_from (None)

* latethrough (False)

* tradename (None)` 
```

### InfluxDB

Lines:

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)

* host (127.0.0.1)

* port (8086)

* username (None)

* password (None)

* database (None)

* startdate (None)

* high (high_p)

* low (low_p)

* open (open_p)

* close (close_p)

* volume (volume)

* ointerest (oi)` 
```

### MT4CSVData

解析 [Metatrader4](https://www.metaquotes.net/en/metatrader4) 历史中心的 CSV 导出文件。

特定参数（或特定含义）：

+   `dataname`：要解析的文件名或文件对象

+   使用 GenericCSVData 并简单修改参数

Lines:

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)

* headers (True)

* separator (,)

* nullvalue (nan)

* dtformat (%Y.%m.%d)

* tmformat (%H:%M)

* datetime (0)

* time (1)

* open (2)

* high (3)

* low (4)

* close (5)

* volume (6)

* openinterest (-1)` 
```

### OandaData

Oanda 数据源。

参数：

+   `qcheck`（默认：`0.5`）

    在未收到数据时唤醒的时间，以便充分重新采样/回放数据包并将通知传递给上游链。

+   `historical`（默认：`False`）

    如果设置为 `True`，数据源在完成首次数据下载后将停止。

    标准数据源参数 `fromdate` 和 `todate` 将作为参考。

    如果所请求的持续时间大于 IB 根据数据所选的时间框架/压缩允许的持续时间，则数据源将发出多个请求。

+   `backfill_start`（默认：`True`）

    在开始时执行回填。将在单个请求中获取最大可能的历史数据。

+   `backfill`（默认：`True`）

    在断开/重新连接周期后执行回填。将使用间隙持续时间来下载可能的最小数据量

+   `backfill_from`（默认：`None`）

    可以传递附加数据源来执行初始层的补充。一旦数据源耗尽并且如果需要，将从 IB 进行补充。这最理想地用于从已存储的源（如磁盘上的文件）进行补充，但不限于此。

+   `bidask`（默认：`True`）

    如果为`True`，则历史/补偿请求将从服务器请求买入/卖出价格

    如果为`False`，则将请求*midpoint*

+   `useask`（默认：`False`）

    如果为`True`，则将使用*bidask*价格的*ask*部分，而不是默认使用*bid*

+   `includeFirst`（默认：`True`）

    通过直接将参数直接设置为 Oanda API 调用来影响历史/补偿请求的第 1 个栏条的交付

+   `reconnect`（默认：`True`）

    当网络连接中断时重新连接

+   `reconnections`（默认：`-1`）

    重新连接尝试次数：`-1` 表示永远

+   `reconntimeout`（默认：`5.0`）

    重新连接尝试之间等待的秒数

此数据源仅支持与 OANDA API 开发人员指南中的定义相符的`timeframe`和`compression`的此映射：

```py
`(TimeFrame.Seconds, 5): 'S5',
(TimeFrame.Seconds, 10): 'S10',
(TimeFrame.Seconds, 15): 'S15',
(TimeFrame.Seconds, 30): 'S30',
(TimeFrame.Minutes, 1): 'M1',
(TimeFrame.Minutes, 2): 'M3',
(TimeFrame.Minutes, 3): 'M3',
(TimeFrame.Minutes, 4): 'M4',
(TimeFrame.Minutes, 5): 'M5',
(TimeFrame.Minutes, 10): 'M10',
(TimeFrame.Minutes, 15): 'M15',
(TimeFrame.Minutes, 30): 'M30',
(TimeFrame.Minutes, 60): 'H1',
(TimeFrame.Minutes, 120): 'H2',
(TimeFrame.Minutes, 180): 'H3',
(TimeFrame.Minutes, 240): 'H4',
(TimeFrame.Minutes, 360): 'H6',
(TimeFrame.Minutes, 480): 'H8',
(TimeFrame.Days, 1): 'D',
(TimeFrame.Weeks, 1): 'W',
(TimeFrame.Months, 1): 'M',` 
```

任何其他组合都将被拒绝

行：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.5)

* calendar (None)

* historical (False)

* backfill_start (True)

* backfill (True)

* backfill_from (None)

* bidask (True)

* useask (False)

* includeFirst (True)

* reconnect (True)

* reconnections (-1)

* reconntimeout (5.0)` 
```

### PandasData

使用 Pandas DataFrame 作为数据源，使用列名的索引（可以是“数字”）

这意味着所有与行相关的参数都必须具有数值作为元组的索引

参数：

+   `nocase` （默认 *True*）不区分列名大小写的匹配

注：

+   `dataname`参数是一个 Pandas DataFrame

+   可用于日期时间的值

    +   None：索引包含日期时间

    +   -1：无索引，自动检测列

    +   > = 0 或字符串：具体的列标识符

+   对于其他行参数

    +   None：列不存在

    +   -1：自动检测

    +   > = 0 或字符串：具体的列标识符

行：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)

* nocase (True)

* datetime (None)

* open (-1)

* high (-1)

* low (-1)

* close (-1)

* volume (-1)

* openinterest (-1)` 
```

### PandasDirectData

使用 Pandas DataFrame 作为数据源，直接迭代“itertuples”返回的元组。

这意味着所有与行相关的参数都必须具有数值作为元组的索引

注：

+   `dataname`参数是一个 Pandas DataFrame

+   数据行中任何参数的负值表示它不存在于 DataFrame 中

行：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)

* datetime (0)

* open (1)

* high (2)

* low (3)

* close (4)

* volume (5)

* openinterest (6)` 
```

### Quandl

对给定时间范围内的数据执行从 Quandl 服务器的直接下载。

特定参数（或特定含义）：

+   `dataname`

    要下载的股票代码（例如‘YHOO’）

+   `baseurl`

    服务器 URL。未来可能有人决定开放与 Quandl 兼容的服务。

+   `proxies`

    一个字典，指示下载要经过的代理，如{‘http’: ‘[`myproxy.com`](http://myproxy.com)’}或{‘http’: ‘[`127.0.0.1:8080`](http://127.0.0.1:8080)’}

+   `buffered`

    如果为 True，则在解析开始前，整个套接字连接将在本地缓冲。

+   `reverse`

    Quandl 以降序返回值（最新的先）。如果这是`True`（默认值），请求将告诉 Quandl 以升序（从旧到新）格式返回

+   `adjclose`

    是否使用股息/拆分调整后的收盘价，并根据其调整所有值。

+   `apikey`

    在可能需要时的 apikey 标识

+   `dataset`

    标识要查询的数据集的字符串。默认为 `WIKI`

行：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)

* headers (True)

* separator (,)

* reverse (True)

* adjclose (True)

* round (False)

* decimals (2)

* baseurl ([`www.quandl.com/api/v3/datasets`](https://www.quandl.com/api/v3/datasets))

* proxies ({})

* buffered (True)

* apikey (None)

* dataset (WIKI)` 
```

### QuandlCSV

解析预先下载的 Quandl CSV 数据源（或者如果它们符合 Quandl 格式则是本地生成的）

特定参数：

+   `dataname`：要解析的文件名或类似文件的对象

+   `reverse`（默认值：`False`）

    假定本地存储的文件已在下载过程中进行了反转

+   `adjclose`（默认值：`True`）

    是否使用股息/拆分调整后的收盘价，并根据其调整所有值。

+   `round`（默认值：`False`）

    是否在调整收盘价后将值四舍五入到特定小数位数

+   `decimals`（默认值：`2`）

    要四舍五入的小数位数

行：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)

* headers (True)

* separator (,)

* reverse (False)

* adjclose (True)

* round (False)

* decimals (2)` 
```

### RollOver

当满足条件时转到下一个未来的类

参数：

+   `checkdate`（默认值：`None`）

    这必须是一个*可调用对象*，具有以下签名：

    ```py
    `checkdate(dt, d):` 
    ```

    在哪里：

    +   `dt` 是一个 `datetime.datetime` 对象

    +   `d` 是当前活动期货的当前数据源

    期望返回值：

    +   `True`：只要可调用返回此值，就可以切换到下一个未来

如果商品在三月的第三个星期五到期，`checkdate` 可能会在到期周的整个周返回 `True`。

```py
`* `False`: the expiration cannot take place` 
```

+   `checkcondition`（默认值：`None`）

    **注意**：仅当 `checkdate` 返回 `True` 时才会调用此函数

    如果为 `None`，则会在内部评估为 `True`（执行滚动）

    否则，这必须是一个*可调用对象*，具有以下签名：

    ```py
    `checkcondition(d0, d1)` 
    ```

    在哪里：

    +   `d0` 是当前活动期货的当前数据源

    +   `d1` 是下一个到期的数据源

    期望返回值：

    +   `True`：切换到下一个未来

跟随 `checkdate` 的示例，这可能表示仅当 `d0` 中的 *volume* 已经小于 `d1` 中的 volume 时，才能发生滚动

```py
`* `False`: the expiration cannot take place` 
```

行：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)

* checkdate (None)

* checkcondition (None)` 
```

### SierraChartCSVData

解析 [SierraChart](http://www.sierrachart.com) 导出的 CSV 文件。

特定参数（或特定含义）：

+   `dataname`：要解析的文件名或类似文件的对象

+   使用 GenericCSVData 并简单修改日期格式（dtformat）为

行：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)

* headers (True)

* separator (,)

* nullvalue (nan)

* dtformat (%Y/%m/%d)

* tmformat (%H:%M:%S)

* datetime (0)

* time (-1)

* open (1)

* high (2)

* low (3)

* close (4)

* volume (5)

* openinterest (6)` 
```

### VCData

VisualChart 数据源。

参数：

+   `qcheck`（默认值：`0.5`）唤醒重新采样器/重播器检查当前柱状图是否可交付的默认超时时间

    仅当在数据中插入了重新采样/重播过滤器时才使用该值

+   `historical`（默认值：`False`）如果没有提供 `todate` 参数（在基类中定义），则设置为 `True` 将强制进行历史下载

    如果提供了 `todate`，则会实现相同的效果

+   `milliseconds`（默认值：`True`）由 *Visual Chart* 构造的柱状图如下所示：HH:MM:59.999000

    如果此参数为 `True`，则会添加一毫秒到此时间，使其看起来像这样：HH::MM + 1:00.000000

+   `tradename`（默认：`None`）连续期货不能交易，但非常适合数据跟踪。如果提供了此参数，则它将是当前期货的名称，这将是交易资产。例如：

    +   001ES -> ES 迷你连续作为`dataname`提供

    +   ESU16 -> ES 迷你 2016-09\. 如果在`tradename`中提供，它将是交易资产。

+   `usetimezones`（默认：`True`）对于大多数市场，*Visual Chart*提供的时间偏移信息允许将日期时间转换为市场时间（*backtrader*表示的选择）

    一些市场是特殊的（`096`），需要特殊的内部覆盖和时区支持，以显示用户期望的市场时间。

    如果将此参数设置为`True`，将尝试导入`pytz`以使用时区（默认）

    禁用它将删除时区使用（如果负载过大可能有所帮助）

行数：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.5)

* calendar (None)

* historical (False)

* millisecond (True)

* tradename (None)

* usetimezones (True)` 
```

### VChartCSVData

解析[VisualChart](http://www.visualchart.com)导出的 CSV 文件。

特定参数（或特定含义）：

+   `dataname`：要解析的文件名或文件类对象

行数：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)

* headers (True)

* separator (,)` 
```

### VChartData

支持[Visual Chart](https://www.visualchart.com)的二进制磁盘文件，包括每日和日内格式。

注意：

+   `dataname`：文件或打开的类文件对象

    如果传递了类文件对象，将使用`timeframe`参数来确定实际时间范围。

    否则，将使用文件扩展名（`.fd`表示每日，`.min`表示日内）。

行数：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)` 
```

### VChartFile

支持[Visual Chart](https://www.visualchart.com)的二进制磁盘文件，包括每日和日内格式。

注意：

+   `dataname`：由 Visual Chart 显示的市场代码。例如：015ES 代表 EuroStoxx 50 连续期货

行数：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)` 
```

### YahooFinanceCSVData

解析预先下载的 Yahoo CSV 数据源（或者如果它们符合 Yahoo 格式，可以是本地生成的）。

特定参数：

+   `dataname`：要解析的文件名或文件类对象

+   `reverse`（默认：`False`）

    假设在下载过程中本地存储的文件已经被反转。

+   `adjclose`（默认：`True`）

    是否使用股息/拆分调整后的收盘价，并根据其调整所有值。

+   `adjvolume`（默认：`True`）

    如果`adjclose`也为`True`，还需调整`volume`。

+   `round`（默认：`True`）

    在调整收盘价后，是否要将值四舍五入到特定的小数位数。

+   `roundvolume`（默认：`0`）

    调整后，将结果体积四舍五入到指定的小数位数。

+   `decimals`（默认：`2`）

    要四舍五入的小数位数

+   `swapcloses`（默认：`False`）

    [2018-11-16] 看起来*close*和*adjusted close*的顺序现在已经固定。参数被保留，以防需要再次交换列。

行数：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime

* adjclose` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)

* headers (True)

* separator (,)

* reverse (False)

* adjclose (True)

* adjvolume (True)

* round (True)

* decimals (2)

* roundvolume (False)

* swapcloses (False)` 
```

### YahooFinanceData

对给定时间范围从 Yahoo 服务器直接下载数据。

特定参数（或特定含义）：

+   `dataname`

    要下载的股票代码（‘YHOO’用于 Yahoo 自有股票报价）

+   `proxies`

    一个字典，指示要通过的代理，如{‘http’: ‘[`myproxy.com`](http://myproxy.com)’}或{‘http’: ‘[`127.0.0.1:8080`](http://127.0.0.1:8080)’}

+   `period`

    下载数据的时间范围。传递‘w’表示每周，‘m’表示每月。

+   `reverse`

    [2018-11-16] 雅虎在线下载的最新版本按正确顺序返回数据。因此，在线下载的`reverse`默认值设置为`False`。

+   `adjclose`

    是否使用股息/拆分调整后的收盘价，并根据其调整所有值。

+   `urlhist`

    用于获取下载的`crumb`授权 cookie 的雅虎财经历史行情的 URL

+   `urldown`

    实际下载服务器的 URL

+   `retries`

    尝试获取`crumb` cookie 并下载数据的次数（每次）

行：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime

* adjclose` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)

* headers (True)

* separator (,)

* reverse (False)

* adjclose (True)

* adjvolume (True)

* round (True)

* decimals (2)

* roundvolume (False)

* swapcloses (False)

* proxies ({})

* period (d)

* urlhist ([`finance.yahoo.com/quote`](https://finance.yahoo.com/quote)/{}/history)

* urldown ([`query1.finance.yahoo.com/v7/finance/download`](https://query1.finance.yahoo.com/v7/finance/download))

* retries (3)` 
```

### YahooLegacyCSV

这旨在加载在 2017 年 5 月雅虎停止原始服务之前下载的文件。

行：

```py
`* close

* low

* high

* open

* volume

* openinterest

* datetime

* adjclose` 
```

参数：

```py
`* dataname (None)

* name ()

* compression (1)

* timeframe (5)

* fromdate (None)

* todate (None)

* sessionstart (None)

* sessionend (None)

* filters ([])

* tz (None)

* tzinput (None)

* qcheck (0.0)

* calendar (None)

* headers (True)

* separator (,)

* reverse (False)

* adjclose (True)

* adjvolume (True)

* round (True)

* decimals (2)

* roundvolume (False)

* swapcloses (False)

* version ()` 
```
