# Data

> 原文：[`zipline.ml4trading.io/bundles.html`](https://zipline.ml4trading.io/bundles.html)

数据包是一组定价数据、调整数据和资产数据库的集合。数据包使我们能够预加载运行回测所需的所有数据，并将数据存储起来以备将来使用。

## 发现可用的数据包

Zipline 自带一个默认数据包，并允许注册新的数据包。要查看哪些数据包可能可用，我们可以运行 `bundles` 命令，例如：

```py
$  zipline  bundles
my-custom-bundle  2016-05-05  20:35:19.809398
my-custom-bundle  2016-05-05  20:34:53.654082
my-custom-bundle  2016-05-05  20:34:48.401767
quandl  2016-05-05  20:06:40.894956 
```

这里的输出显示有 3 个数据包可用：

+   `my-custom-bundle`（用户添加）

+   `quandl`（由 Zipline 提供，默认数据包）

名称旁边的日期和时间显示了该数据包数据导入的时间。我们已经为 `my-custom-bundle` 运行了三次不同的导入。我们从未为 `quandl` 数据包导入任何数据，因此它只显示 `<no ingestions>`。

**注意**：Quantopian 曾经提供了一个重新打包的 `quandl` 数据包版本，名为 `quantopian-quandl`，截至 2021 年 4 月仍然可用。虽然它导入速度更快，但它没有该库后来要求的国别代码，而当前的 Zipline 版本为 `quandl` 数据包插入了这些代码。如果你想使用 `quantopian-quandl`，请使用[这个解决方案](https://github.com/quantopian/zipline/issues/2517)手动更新数据库。 ## 数据导入

使用数据包的第一步是导入数据。导入过程将调用一些自定义数据包命令，然后将数据写入 Zipline 可以找到的标准位置。默认情况下，导入的数据将写入的位置是 `$ZIPLINE_ROOT/data/<bundle>`，默认情况下 `ZIPLINE_ROOT=~/.zipline`。导入步骤可能需要一些时间，因为它可能涉及下载和处理大量数据。要导入数据包，请运行：

```py
$  zipline  ingest  [-b  <bundle>] 
```

其中 `<bundle>` 是要导入的数据包的名称，默认为 `quandl`。

## Old Data

`ingest`命令使用时，它会将新数据写入 `$ZIPLINE_ROOT/data/<bundle>` 的子目录，该子目录以当前日期命名。这使得查看旧数据或甚至使用旧副本运行回测成为可能。使用旧的导入数据运行回测使得稍后重现回测结果变得更加容易。

默认情况下保存所有数据的缺点是，即使您不想使用这些数据，数据目录也可能变得非常大。如前所述，我们可以使用 bundles 命令 列出所有导入。为了解决旧数据泄露的问题，还有一个命令：`clean`，它将根据某些时间约束清除数据包。

例如：

```py
# clean everything older than <date>
$  zipline  clean  [-b  <bundle>]  --before  <date>

# clean everything newer than <date>
$  zipline  clean  [-b  <bundle>]  --after  <date>

# keep everything in the range of [before, after] and delete the rest
$  zipline  clean  [-b  <bundle>]  --before  <date>  --after  <after>

# clean all but the last <int> runs
$  zipline  clean  [-b  <bundle>]  --keep-last  <int> 
```

## 使用数据包运行回测

现在数据已经导入，我们可以使用它来运行回测，使用 `run` 命令。可以使用 `--bundle` 选项指定要使用的数据包，例如：

```py
$  zipline  run  --bundle  <bundle>  --algofile  algo.py  ... 
```

我们还可以使用`--bundle-timestamp`选项指定查找捆绑数据所用的日期。设置`--bundle-timestamp`将导致`run`使用小于或等于`bundle-timestamp`的最新的捆绑数据摄取。这就是我们如何使用旧数据进行回测。`bundle-timestamp`使用小于或等于的关系，因此我们可以指定运行旧回测的日期，并获取该日期对我们可用的相同数据。`bundle-timestamp`默认设置为当前日期，以使用最新的数据。

## 默认数据捆绑包

### Quandl WIKI 捆绑包

默认情况下，Zipline 附带了`quandl`数据捆绑包，该捆绑包使用 Quandl 的[WIKI 数据集](https://www.quandl.com/data/WIKI)。Quandl 数据捆绑包包括每日定价数据、拆分、现金股息和资产元数据。要摄取`quandl`数据捆绑包，请运行以下任一命令：

```py
$  zipline  ingest  -b  quandl
$  zipline  ingest 
```

任一命令下载和处理数据应该只需要几分钟。

注意

Quandl 在 2018 年初停止了这个数据集的更新，不再更新。尽管如此，它仍然是一个有用的起点，可以在不设置自己的数据集的情况下尝试 Zipline。## 编写新的捆绑包

数据捆绑包的存在是为了方便使用不同的数据源与 Zipline。要添加新的捆绑包，必须实现一个`ingest`函数。

`ingest`函数负责将数据加载到内存中，并将其传递给一组由 Zipline 提供的写入器对象，以将数据转换为 Zipline 的内部格式。`ingest`函数可以通过从远程位置（如`quandl`捆绑包）下载数据或仅加载机器上已有的文件来工作。该函数提供了将数据写入正确位置的写入器。如果摄取部分失败，捆绑包将不会处于不完整状态。

`ingest`函数的签名应为：

```py
ingest(environ,
       asset_db_writer,
       minute_bar_writer,
       daily_bar_writer,
       adjustment_writer,
       calendar,
       start_session,
       end_session,
       cache,
       show_progress,
       output_dir) 
```

### `environ`

`environ`是一个映射，表示要使用的环境变量。这是传递任何摄取所需的定制参数的地方，例如：`quandl`捆绑包使用环境传递 API 密钥和下载重试尝试次数。

### `asset_db_writer`

`asset_db_writer`是`AssetDBWriter`的一个实例。这是资产元数据的写入器，提供资产生命周期和符号到资产 ID（sid）的映射。这可能还包括资产名称、交易所和其他一些列。要写入数据，请使用各种元数据的数据框调用`write()`。有关数据格式的更多信息，请参阅 write 的文档。

### `minute_bar_writer`

`分钟条形写入器` 是 `BcolzMinuteBarWriter` 的一个实例。该写入器用于将数据转换为 Zipline 的内部 bcolz 格式，以便稍后由 `BcolzMinuteBarReader` 读取。如果提供分钟数据，用户应该使用 (sid, 数据框) 元组的可迭代对象调用 `write()`。`show_progress` 参数也应该传递给此方法。如果数据源不提供分钟级数据，则无需调用写入方法。向 `write()` 传递一个空的迭代器也是可接受的，以表明没有分钟数据。

注意

传递给 `write()` 的数据可以是惰性迭代器或生成器，以避免一次性将所有分钟数据加载到内存中。只要日期严格递增，给定的 sid 也可以在数据中出现多次。

### `日条形写入器`

`日条形写入器` 是 `BcolzDailyBarWriter` 的一个实例。该写入器用于将数据转换为 Zipline 的内部 bcolz 格式，以便稍后由 `BcolzDailyBarReader` 读取。如果提供日数据，用户应该使用可迭代对象的 (sid, 数据框) 元组调用 `write()`。`show_progress` 参数也应该传递给此方法。如果数据源不提供日数据，则无需调用写入方法。向 `write()` 传递一个空的可迭代对象也是可接受的，以表明没有日数据。如果没有提供日数据但提供了分钟数据，则会进行日汇总以满足日历史请求。

注意

与 `分钟条形写入器` 类似，传递给 `write()` 的数据可以是惰性可迭代对象或生成器，以避免一次性将所有数据加载到内存中。与 `分钟条形写入器` 不同的是，sid 在数据可迭代对象中只能出现一次。

### `调整写入器`

`调整写入器`是 `SQLiteAdjustmentWriter` 的一个实例。该写入器用于存储拆分、合并、股息和股票股息。数据应以数据框的形式提供，并传递给 `write()`。这些字段都是可选的，但写入器可以接受您拥有的尽可能多的数据。

### `日历`

`日历` 是 `zipline.utils.calendars.TradingCalendar` 的一个实例。提供日历是为了帮助一些捆绑包生成所需日期的查询。

### `开始会话`

`开始会话` 是一个 [`pandas.Timestamp`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html#pandas.Timestamp "(in pandas v2.0.3)") 对象，指示捆绑包应该加载数据的第一天。

### `结束会话`

`结束会话`是一个[`pandas.Timestamp`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html#pandas.Timestamp "(in pandas v2.0.3)")对象，表示包应加载数据的最后一天。

### `缓存`

`缓存`是`dataframe_cache`的一个实例。这个对象是一个从字符串到数据框的映射。这个对象是在摄取过程中崩溃时提供的。其想法是，摄取函数应该检查缓存中是否存在原始数据，如果不存在，则应该获取它，然后将其存储在缓存中。然后它可以解析并写入数据。只有在成功加载后，缓存才会被清除，这可以防止摄取函数在解析中出现错误时需要重新下载所有数据。如果获取数据非常快，例如如果它来自另一个本地文件，则不需要使用此缓存。

### `显示进度`

`显示进度`是一个布尔值，表示用户希望接收关于摄取函数获取和写入数据的进度的反馈。例如，显示已下载的文件数量占所需总量的百分比，或者显示数据转换的进度。实现循环中`显示进度`的一个有用工具是`maybe_show_progress`。这个参数应该总是被转发到`minute_bar_writer.write`和`daily_bar_writer.write`。

### `输出目录`

`输出目录`是一个字符串，表示所有数据将被写入的文件路径。`输出目录`将是`$ZIPLINE_ROOT`的某个子目录，并将包含当前摄取的开始时间。如果由于某些原因您的摄取函数可以不使用写入器而直接产生输出，则可以直接将资源移动到这里。例如，`quantopian:quandl`包使用这个直接将包解压到`输出目录`。

## 从.csv 文件摄取数据

Zipline 提供了一个名为`csvdir`的包，允许用户从`.csv`文件中摄取数据。文件格式应为 OHLCV 格式，包含日期、股息和拆分。下面提供了一个示例。在`zipline/tests/resources/csvdir_samples`中还有其他用于测试目的的示例。

```py
date,open,high,low,close,volume,dividend,split
2012-01-03,58.485714,58.92857,58.42857,58.747143,75555200,0.0,1.0
2012-01-04,58.57143,59.240002,58.468571,59.062859,65005500,0.0,1.0
2012-01-05,59.278572,59.792858,58.952858,59.718571,67817400,0.0,1.0
2012-01-06,59.967144,60.392857,59.888573,60.342857,79573200,0.0,1.0
2012-01-09,60.785713,61.107143,60.192856,60.247143,98506100,0.0,1.0
2012-01-10,60.844284,60.857143,60.214287,60.462856,64549100,0.0,1.0
2012-01-11,60.382858,60.407143,59.901428,60.364285,53771200,0.0,1.0 
```

一旦您的数据格式正确，您可以编辑`~/.zipline/extension.py`文件中的`extension.py`文件，并导入 csvdir 包和`pandas`。

```py
import pandas as pd

from zipline.data.bundles import register
from zipline.data.bundles.csvdir import csvdir_equities 
```

然后，我们希望指定我们的包数据的开始和结束会话：

```py
start_session = pd.Timestamp('2016-1-1', tz='utc')
end_session = pd.Timestamp('2018-1-1', tz='utc') 
```

然后我们可以`注册()`我们的包，并传递我们的`.csv`文件所在的目录位置：

```py
register(
    'custom-csvdir-bundle',
    csvdir_equities(
        ['daily'],
        '/path/to/your/csvs',
    ),
    calendar_name='NYSE', # US equities
    start_session=start_session,
    end_session=end_session
) 
```

最后，我们可以运行以下命令来摄取我们的数据：

```py
$  zipline  ingest  -b  custom-csvdir-bundle
Loading  custom  pricing  data:  [############------------------------]   33% | FAKE: sid 0
Loading  custom  pricing  data:  [########################------------]   66% | FAKE1: sid 1
Loading  custom  pricing  data:  [####################################]  100% | FAKE2: sid 2
Loading  custom  pricing  data:  [####################################]  100%
Merging  daily  equity  files:  [####################################]

# optionally, we can pass the location of our csvs via the command line
$  CSVDIR=/path/to/your/csvs  zipline  ingest  -b  custom-csvdir-bundle 
```

如果你想使用不在 NYSE 日历或现有 Zipline 日历中的股票，你可以查看`Trading Calendar Tutorial`来构建一个自定义交易日历，然后你可以将名称传递给`register()`。

## 实用示例

请参阅[Algoseek](https://www.algoseek.com/)的[分钟数据](https://github.com/stefan-jansen/machine-learning-for-trading/tree/master/08_ml4t_workflow/04_ml4t_workflow_with_zipline/01_custom_bundles)和[日本股票](https://github.com/stefan-jansen/machine-learning-for-trading/tree/master/11_decision_trees_random_forests/00_custom_bundle)的每日频率示例，来自书籍[机器学习交易](https://www.amazon.com/Machine-Learning-Algorithmic-Trading-alternative/dp/1839217715?pf_rd_r=GZH2XZ35GB3BET09PCCA&pf_rd_p=c5b6893a-24f2-4a59-9d4b-aff5065c90ec&pd_rd_r=91a679c7-f069-4a6e-bdbb-a2b3f548f0c8&pd_rd_w=2B0Q0&pd_rd_wg=GMY5S&ref_=pd_gw_ci_mcx_mr_hp_d)。

## 发现可用的捆绑包

Zipline 自带一个默认捆绑包，以及注册新捆绑包的能力。要查看哪些捆绑包可能可用，我们可以运行`bundles`命令，例如：

```py
$  zipline  bundles
my-custom-bundle  2016-05-05  20:35:19.809398
my-custom-bundle  2016-05-05  20:34:53.654082
my-custom-bundle  2016-05-05  20:34:48.401767
quandl  2016-05-05  20:06:40.894956 
```

这里的输出显示有 3 个可用的捆绑包：

+   `my-custom-bundle`（由用户添加）

+   `quandl`（由 Zipline 提供，默认捆绑包）

名称旁边的日期和时间显示了该捆绑包数据被摄取的时间。我们为`my-custom-bundle`进行了三次不同的数据摄取。我们从未为`quandl`捆绑包摄取过任何数据，因此它只显示`<no ingestions>`。

**注意**：Quantopian 曾经提供了一个重新打包的`quandl`捆绑包版本，名为`quantopian-quandl`，截至 2021 年 4 月仍然可用。虽然它摄取速度更快，但它没有库后来要求的国别代码，而当前的 Zipline 版本为`quandl`捆绑包插入了国别代码。如果你想使用`quantopian-quandl`，请使用[这个解决方法](https://github.com/quantopian/zipline/issues/2517)手动更新数据库。

## 摄取数据

使用数据捆绑包的第一步是摄取数据。摄取过程将调用一些自定义捆绑包命令，然后将数据写入 Zipline 可以找到的标准位置。默认情况下，摄取的数据将被写入的位置是`$ZIPLINE_ROOT/data/<bundle>`，默认情况下`ZIPLINE_ROOT=~/.zipline`。摄取步骤可能需要一些时间，因为它可能涉及下载和处理大量数据。要摄取捆绑包，请运行：

```py
$  zipline  ingest  [-b  <bundle>] 
```

其中`<bundle>`是要摄取的捆绑包的名称，默认为`quandl`。

## 旧数据

当使用`ingest`命令时，它会将新数据写入到以当前日期命名的`$ZIPLINE_ROOT/data/<bundle>`的子目录中。这使得查看旧数据或甚至使用旧副本运行回测成为可能。使用旧的数据摄取运行回测使得稍后更容易重现回测结果。

默认情况下保存所有数据的缺点是，即使您不想使用数据，数据目录也可能变得很大。如前所述，我们可以使用 bundles 命令 列出所有导入。为了解决旧数据泄露的问题，还有另一个命令：`clean`，它将根据某些时间约束清除数据包。

例如：

```py
# clean everything older than <date>
$  zipline  clean  [-b  <bundle>]  --before  <date>

# clean everything newer than <date>
$  zipline  clean  [-b  <bundle>]  --after  <date>

# keep everything in the range of [before, after] and delete the rest
$  zipline  clean  [-b  <bundle>]  --before  <date>  --after  <after>

# clean all but the last <int> runs
$  zipline  clean  [-b  <bundle>]  --keep-last  <int> 
```

## 使用数据包运行回测

现在数据已经导入，我们可以使用它来运行回测，使用 `run` 命令。可以使用 `--bundle` 选项指定要使用的数据包，例如：

```py
$  zipline  run  --bundle  <bundle>  --algofile  algo.py  ... 
```

我们还可以使用 `--bundle-timestamp` 选项指定要查找数据包数据的日期。设置 `--bundle-timestamp` 将导致 `run` 使用不大于或等于 `bundle-timestamp` 的最新数据包导入。这就是我们如何使用旧数据运行回测。`bundle-timestamp` 使用小于或等于的关系，因此我们可以指定运行旧回测的日期，并获取该日期对我们可用的相同数据。`bundle-timestamp` 默认为当前日期，以使用最新数据。

## 默认数据包

### Quandl WIKI 数据包

默认情况下，Zipline 附带了使用 Quandl 的 [WIKI 数据集](https://www.quandl.com/data/WIKI) 的 `quandl` 数据包。Quandl 数据包包括每日价格数据、拆分、现金股息和资产元数据。要导入 `quandl` 数据包，请运行以下任一命令：

```py
$  zipline  ingest  -b  quandl
$  zipline  ingest 
```

任一命令下载和处理数据的时间应该只需几分钟。

注意

Quandl 已于 2018 年初停止更新此数据集。尽管如此，它仍然是一个有用的起点，可以在不设置自己的数据集的情况下尝试 Zipline。### Quandl WIKI 数据包

默认情况下，Zipline 附带了使用 Quandl 的 [WIKI 数据集](https://www.quandl.com/data/WIKI) 的 `quandl` 数据包。Quandl 数据包包括每日价格数据、拆分、现金股息和资产元数据。要导入 `quandl` 数据包，请运行以下任一命令：

```py
$  zipline  ingest  -b  quandl
$  zipline  ingest 
```

任一命令下载和处理数据的时间应该只需几分钟。

注意

Quandl 已于 2018 年初停止更新此数据集。尽管如此，它仍然是一个有用的起点，可以在不设置自己的数据集的情况下尝试 Zipline。

## 编写新的数据包

数据包的存在是为了方便使用不同的数据源与 Zipline。要添加新的数据包，必须实现一个 `ingest` 函数。

`ingest` 函数负责将数据加载到内存中，并将其传递给 Zipline 提供的一组写入器对象，以将数据转换为 Zipline 的内部格式。ingest 函数可以通过下载远程位置的数据来工作，例如 `quandl` 包，或者只是加载已经在机器上的文件。函数会提供写入器，将数据写入正确的位置。如果部分 ingestion 失败，包将不会处于不完整状态。

ingest 函数的签名应该是：

```py
ingest(environ,
       asset_db_writer,
       minute_bar_writer,
       daily_bar_writer,
       adjustment_writer,
       calendar,
       start_session,
       end_session,
       cache,
       show_progress,
       output_dir) 
```

### `environ`

`environ` 是一个映射，表示要使用的环境变量。这是传递任何自定义参数的地方，例如：`quandl` 包使用环境变量传递 API 密钥和下载重试次数。

### `asset_db_writer`

`asset_db_writer` 是 `AssetDBWriter` 的一个实例。它是用于资产元数据的写入器，提供资产生命周期和符号到资产 ID（sid）的映射。它还可能包含资产名称、交易所和其他一些列。要写入数据，请调用 `write()` 方法，并传入各个元数据的数据框。关于数据格式的更多信息，请参阅 write 的文档。

### `minute_bar_writer`

`minute_bar_writer` 是 `BcolzMinuteBarWriter` 的一个实例。这个写入器用于将数据转换为 Zipline 内部 bcolz 格式，以便稍后由 `BcolzMinuteBarReader` 读取。如果提供了分钟数据，用户应该调用 `write()` 方法，并传入一个 (sid, dataframe) 元组的迭代器。`show_progress` 参数也应该传递给这个方法。如果数据源不提供分钟级数据，则无需调用 write 方法。向 `write()` 传递一个空迭代器也是可接受的，以表示没有分钟级数据。

注意

`write()` 方法接收的数据可以是惰性迭代器或生成器，以避免一次性将所有分钟数据加载到内存中。只要日期严格递增，一个给定的 sid 也可以在数据中出现多次。

### `daily_bar_writer`

`daily_bar_writer` 是 `BcolzDailyBarWriter` 的一个实例。该写入器用于将数据转换为 Zipline 的内部 bcolz 格式，以便稍后由 `BcolzDailyBarReader` 读取。如果提供了每日数据，用户应该使用可迭代对象的 (sid, dataframe) 元组调用 `write()`。`show_progress` 参数也应该转发给此方法。如果数据源不提供每日数据，则无需调用 write 方法。向 `write()` 传递一个空的可迭代对象以表示没有每日数据也是可接受的。如果没有提供每日数据但提供了分钟数据，则会进行每日汇总以服务每日历史请求。

Note

与 `minute_bar_writer` 类似，传递给 `write()` 的数据可以是惰性可迭代对象或生成器，以避免一次性将所有数据加载到内存中。与 `minute_bar_writer` 不同的是，sid 在数据可迭代对象中只能出现一次。

### `adjustment_writer`

`adjustment_writer` 是 `SQLiteAdjustmentWriter` 的一个实例。该写入器用于存储拆分、合并、股息和股票股息。数据应以数据框的形式提供，并传递给 `write()`。这些字段都是可选的，但写入器可以接受您拥有的尽可能多的数据。

### `calendar`

`calendar` 是 `zipline.utils.calendars.TradingCalendar` 的一个实例。提供日历是为了帮助某些捆绑包生成所需日期的查询。

### `start_session`

`start_session` 是一个 [`pandas.Timestamp`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html#pandas.Timestamp "(in pandas v2.0.3)") 对象，表示该捆绑包应该加载数据的第一天。

### `end_session`

`end_session` 是一个 [`pandas.Timestamp`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html#pandas.Timestamp "(in pandas v2.0.3)") 对象，表示该捆绑包应该加载数据的最后一天。

### `cache`

`cache` 是 `dataframe_cache` 的一个实例。这个对象是一个字符串到数据框的映射。如果摄取过程中发生崩溃，这个对象会被提供。其想法是，摄取函数应该检查缓存中是否存在原始数据，如果不存在，则应该获取它并将其存储在缓存中。然后它可以解析并写入数据。只有在成功加载后，缓存才会被清除，这可以防止摄取函数在解析中出现错误时需要重新下载所有数据。如果获取数据非常快，例如如果它来自另一个本地文件，则不需要使用此缓存。

### `show_progress`

`show_progress` 是一个布尔值，表示用户希望接收关于摄取函数在获取和写入数据方面的进度反馈。例如，显示已下载的文件数量占所需总数的百分比，或者数据转换的进度。实现 `show_progress` 的一个有用工具是 `maybe_show_progress`。这个参数应该始终传递给 `minute_bar_writer.write` 和 `daily_bar_writer.write`。

### `output_dir`

`output_dir` 是一个字符串，表示所有数据将被写入的文件路径。`output_dir` 将是 `$ZIPLINE_ROOT` 的某个子目录，并包含当前摄取开始的时间。如果您的摄取函数可以直接生成输出而不需要写入器，则可以直接将资源移动到这里。例如，`quantopian:quandl` 包使用这个直接将包解压到 `output_dir`。

### `environ`

`environ` 是一个映射，代表要使用的环境变量。这是传递任何摄取所需的定制参数的地方，例如：`quandl` 包使用环境变量传递 API 密钥和下载重试次数。

### `asset_db_writer`

`asset_db_writer` 是 `AssetDBWriter` 的一个实例。它是用于资产元数据的写入器，提供资产生命周期和符号到资产 ID（sid）的映射。它还可能包含资产名称、交易所和其他一些列。要写入数据，请调用 `write()` 方法，并传入各个元数据的数据框。关于数据格式的更多信息，请参阅 write 的文档。

### `minute_bar_writer`

`minute_bar_writer`是`BcolzMinuteBarWriter`的一个实例。该写入器用于将数据转换为 Zipline 的内部 bcolz 格式，以便稍后由`BcolzMinuteBarReader`读取。如果提供了分钟数据，用户应该使用可迭代的(sid, dataframe)元组调用`write()`。`show_progress`参数也应该传递给此方法。如果数据源不提供分钟级数据，则无需调用 write 方法。向`write()`传递一个空迭代器也是可接受的，以表明没有分钟数据。

注意

传递给`write()`的数据可以是惰性迭代器或生成器，以避免一次性将所有分钟数据加载到内存中。只要日期严格递增，一个给定的 sid 也可以在数据中出现多次。

### `daily_bar_writer`

`daily_bar_writer`是`BcolzDailyBarWriter`的一个实例。该写入器用于将数据转换为 Zipline 的内部 bcolz 格式，以便稍后由`BcolzDailyBarReader`读取。如果提供了每日数据，用户应该使用可迭代的(sid, dataframe)元组调用`write()`。`show_progress`参数也应该传递给此方法。如果数据源不提供每日数据，则无需调用 write 方法。向`write()`传递一个空迭代器也是可接受的，以表明没有每日数据。如果没有提供每日数据但提供了分钟数据，则会进行每日汇总以服务每日历史请求。

注意

与`minute_bar_writer`类似，传递给`write()`的数据可以是惰性迭代器或生成器，以避免一次性将所有数据加载到内存中。与`minute_bar_writer`不同的是，一个 sid 只能在数据迭代器中出现一次。

### `adjustment_writer`

`adjustment_writer`是`SQLiteAdjustmentWriter`的一个实例。该写入器用于存储拆分、合并、股息和股票股息。数据应以数据框的形式提供，并传递给`write()`。这些字段中的每一个都是可选的，但写入器可以接受您拥有的尽可能多的数据。

### `calendar`

`calendar`是`zipline.utils.calendars.TradingCalendar`的一个实例。日历提供给一些捆绑包，以帮助生成所需日期的查询。

### `start_session`

`start_session`是一个[`pandas.Timestamp`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html#pandas.Timestamp "(in pandas v2.0.3)")对象，指示捆绑包应该加载数据的第一天。

### `end_session`

`end_session` 是一个 [`pandas.Timestamp`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html#pandas.Timestamp "(in pandas v2.0.3)") 对象，表示包应该加载数据的最后一天。

### `cache`

`cache` 是 `dataframe_cache` 的一个实例。这个对象是一个从字符串到数据框的映射。如果摄取过程中途崩溃，这个对象会被提供。其想法是，摄取函数应该检查缓存中是否存在原始数据，如果不存在，则应该获取它，然后将其存储在缓存中。然后它可以解析并写入数据。只有在成功加载后，缓存才会被清除，这可以防止摄取函数在解析中出现错误时需要重新下载所有数据。如果获取数据非常快，例如如果数据来自另一个本地文件，则不需要使用此缓存。

### `show_progress`

`show_progress` 是一个布尔值，表示用户希望接收关于摄取函数获取和写入数据进度的反馈。例如，显示已下载的文件数量占所需总数的比例，或者摄取函数在进行某些数据转换时的进度。实现 `show_progress` 循环的一个有用工具是 `maybe_show_progress`。这个参数应该始终传递给 `minute_bar_writer.write` 和 `daily_bar_writer.write`。

### `output_dir`

`output_dir` 是一个字符串，表示所有数据将被写入的文件路径。`output_dir` 将是 `$ZIPLINE_ROOT` 的某个子目录，并且将包含当前摄取开始的时间。如果由于某种原因，您的摄取函数可以不通过写入器产生自己的输出，那么可以直接将资源移动到这里。例如，`quantopian:quandl` 包使用这个功能直接将包解压到 `output_dir` 中。

## 从 .csv 文件中摄取数据

Zipline 提供了一个名为 `csvdir` 的包，允许用户从 `.csv` 文件中摄取数据。文件格式应为 OHLCV 格式，包含日期、股息和拆分。下面提供了一个示例。在 `zipline/tests/resources/csvdir_samples` 中还有其他用于测试目的的示例。

```py
date,open,high,low,close,volume,dividend,split
2012-01-03,58.485714,58.92857,58.42857,58.747143,75555200,0.0,1.0
2012-01-04,58.57143,59.240002,58.468571,59.062859,65005500,0.0,1.0
2012-01-05,59.278572,59.792858,58.952858,59.718571,67817400,0.0,1.0
2012-01-06,59.967144,60.392857,59.888573,60.342857,79573200,0.0,1.0
2012-01-09,60.785713,61.107143,60.192856,60.247143,98506100,0.0,1.0
2012-01-10,60.844284,60.857143,60.214287,60.462856,64549100,0.0,1.0
2012-01-11,60.382858,60.407143,59.901428,60.364285,53771200,0.0,1.0 
```

一旦您的数据格式正确，您可以编辑 `~/.zipline/extension.py` 文件中的 `extension.py` 文件，并导入 csvdir 包以及 `pandas`。

```py
import pandas as pd

from zipline.data.bundles import register
from zipline.data.bundles.csvdir import csvdir_equities 
```

然后，我们想要指定我们的包数据的开始和结束会话：

```py
start_session = pd.Timestamp('2016-1-1', tz='utc')
end_session = pd.Timestamp('2018-1-1', tz='utc') 
```

然后我们可以 `register()` 我们的包，并传递我们的 `.csv` 文件所在的目录位置：

```py
register(
    'custom-csvdir-bundle',
    csvdir_equities(
        ['daily'],
        '/path/to/your/csvs',
    ),
    calendar_name='NYSE', # US equities
    start_session=start_session,
    end_session=end_session
) 
```

最后，我们可以运行以下命令来摄取我们的数据：

```py
$  zipline  ingest  -b  custom-csvdir-bundle
Loading  custom  pricing  data:  [############------------------------]   33% | FAKE: sid 0
Loading  custom  pricing  data:  [########################------------]   66% | FAKE1: sid 1
Loading  custom  pricing  data:  [####################################]  100% | FAKE2: sid 2
Loading  custom  pricing  data:  [####################################]  100%
Merging  daily  equity  files:  [####################################]

# optionally, we can pass the location of our csvs via the command line
$  CSVDIR=/path/to/your/csvs  zipline  ingest  -b  custom-csvdir-bundle 
```

如果你想使用不在纽约证券交易所日历或现有 Zipline 日历中的股票，你可以查看`Trading Calendar Tutorial`来构建一个自定义交易日历，然后将其名称传递给`register()`。

## 实用示例

查看书籍[机器学习在交易中的应用](https://www.amazon.com/Machine-Learning-Algorithmic-Trading-alternative/dp/1839217715?pf_rd_r=GZH2XZ35GB3BET09PCCA&pf_rd_p=c5b6893a-24f2-4a59-9d4b-aff5065c90ec&pd_rd_r=91a679c7-f069-4a6e-bdbb-a2b3f548f0c8&pd_rd_w=2B0Q0&pd_rd_wg=GMY5S&ref_=pd_gw_ci_mcx_mr_hp_d)中的示例，包括[Algoseek](https://www.algoseek.com/)的[分钟数据](https://github.com/stefan-jansen/machine-learning-for-trading/tree/master/08_ml4t_workflow/04_ml4t_workflow_with_zipline/01_custom_bundles)和[日本股票](https://github.com/stefan-jansen/machine-learning-for-trading/tree/master/11_decision_trees_random_forests/00_custom_bundle)的日频数据。
