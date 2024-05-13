# 发布

> 原文：[`zipline.ml4trading.io/releases.html`](https://zipline.ml4trading.io/releases.html)

## 发布 2.0.0rc

发布：

2.0.0rc1

日期：

2021 年 4 月 5 日

### 亮点

此版本更新了 Zipline，使其与 Python >= 3.7 以及当前版本的 Pandas、scikit-learn 等相关的 PyData 库兼容。

[Conda 包](https://anaconda.org/ml4t/repo)现在为[Zipline](https://anaconda.org/ml4t/zipline-reloaded)及其关键依赖项[bcolz](https://anaconda.org/ml4t/bcolz-zipline)和[TA-Lib](https://anaconda.org/ml4t/ta-lib)提供 Python 3.7-3.9 版本，可在‘ml4t’ Anaconda 频道上获取。Linux（Python 3.7-3.9）和 MacOSx（3.7 和 3.8）的二进制轮子可在[PyPi](https://pypi.org/project/zipline-reloaded/)上获取。

作为更新的一部分，移除了基于[Blaze 生态系统](https://blaze.pydata.org/)的`BlazeLoader`功能。不幸的是，相关的三个项目（[Blaze](https://blaze.readthedocs.io/en/latest/index.html)、[Odo](https://odo.readthedocs.io/en/latest/)和[datashape](https://datashape.readthedocs.io/en/latest/)）在过去几年中得到了非常有限的支持。

其他更新包括：

+   [Bcolz](https://github.com/Blosc/bcolz)自 2020 年 9 月以来被标记为不再维护，现在有了一个[新版本](https://github.com/stefan-jansen/bcolz-zipline)，该版本将底层[c-blosc](https://github.com/Blosc/c-blosc)库从 1.14 版更新到了最新的 1.21.0 版。此外，还有适用于 Bcolz 的 conda 包（见上述链接）。

+   [Networkx](https://networkx.org/)现在使用性能更好的 2.0 版。

+   Conda 包为 TA-Lib 0.4.19。

这个新版本还使得在回测时更容易将自定义数据源（如 ML 模型的预测）加载到 Pipeline 中。请参阅书籍[Machine Learning for Trading](https://www.amazon.com/Machine-Learning-Algorithmic-Trading-alternative/dp/1839217715/ref=tmm_pap_swatch_0?_encoding=UTF8&qid=1617658040&sr=8-1-spons)的[Github 仓库](https://github.com/stefan-jansen/machine-learning-for-trading)中的相关示例，例如[这些](https://github.com/stefan-jansen/machine-learning-for-trading/tree/master/08_ml4t_workflow/04_ml4t_workflow_with_zipline)。

### 增强功能

+   自定义 Pipeline 数据的 custom_loader()

+   与最新版本的 Pandas、scikit-learn 和其他相关[PyData](https://pydata.org/)库的兼容性。

### 错误修复

+   更新了大量测试以适应最近的 Python 和依赖版本。

### 性能

+   最新的 blosc 库可能会提高压缩和 I/O 性能

### 维护和重构

+   移除了对 Python 2 的支持

### 构建

+   所有构建都整合在 GitHub Actions CI 上

### 文档

+   扩展了有关 Pipeline 和相关 DataLoaders 的更多信息

## 发布 1.4.1

发布：

1.4.1

日期：

2020 年 10 月 5 日

此版本包含少量错误修复、文档改进和构建/依赖项增强。

zipline 及其依赖项的 conda 包现在可用于 python 3.6 的‘conda-forge’Anaconda 频道。它们也可以在‘Quantopian’频道上获得，但我们最终会停止更新那些。

### 错误修复

+   修复了在没有`benchmark_returns`的情况下调用`run_algorithm`的问题（[2762](https://github.com/stefan-jansen/zipline/issues/2762)）

### 维护和重构

+   支持 empyrical 0.5.3（[2526](https://github.com/stefan-jansen/zipline/issues/2526)）

+   在 py3 环境中移除对 contextlib2 的依赖（[2757](https://github.com/stefan-jansen/zipline/issues/2757)）

+   将默认 bundle 更新为‘quantopian-quandl’，并在更多入口点使用（[2763](https://github.com/stefan-jansen/zipline/issues/2763)）

### 构建

+   使用新的 statsmodels 和 scipy 进行 CI（[2739](https://github.com/stefan-jansen/zipline/issues/2739)）

+   GitHub Actions CI 在 linux 和 macos 上（[2743](https://github.com/stefan-jansen/zipline/issues/2743)，[2767](https://github.com/stefan-jansen/zipline/issues/2767)）

+   为 zipline 及其依赖项添加了 conda-forge 的 conda 打包（[2665](https://github.com/stefan-jansen/zipline/issues/2665)）

### 文档

+   各种文档改进（[2763](https://github.com/stefan-jansen/zipline/issues/2763)，[2771](https://github.com/stefan-jansen/zipline/issues/2771)，[2772](https://github.com/stefan-jansen/zipline/issues/2772)，[2776](https://github.com/stefan-jansen/zipline/issues/2776)，[2780](https://github.com/stefan-jansen/zipline/issues/2780)）

## 发布 1.4.0

发布：

1.4.0

日期：

2020 年 7 月 22 日

### 亮点

#### 移除对基准和国债回报的隐式依赖

以前，如果用户没有提供这些必需的输入，Zipline 会从第三方 API 源隐式获取：国债数据来自美国联邦储备银行的 API，基准来自 IEX。这意味着模拟需要互联网连接和这些数据源的稳定 API，这对于许多用户来说都不是保证的。

我们移除了对国债曲线的依赖，因为它们实际上不再被使用。并且我们用明确的选项替换了隐式下载基准回报：

```py
--benchmark-file                The csv file that contains the benchmark
                                returns (date, returns columns)
--benchmark-symbol              The instrument's symbol to be used as
                                a benchmark.
                                (should exist in the ingested bundle)
--benchmark-sid                 The sid of the instrument to be used as a
                                benchmark.
                                (should exist in the ingested bundle)
--no-benchmark                  This flag is used to set the benchmark to
                                zero. Alpha, beta and benchmark metrics
                                are not calculated 
```

（[2627](https://github.com/stefan-jansen/zipline/issues/2627)，[2642](https://github.com/stefan-jansen/zipline/issues/2642)）

#### 新的内置因子

+   `PercentChange`：计算给定`window_length`的百分比变化。注意：百分比变化计算公式为`(new - old) / abs(old)`。（[2506](https://github.com/stefan-jansen/zipline/issues/2506)）

+   `PeerCount`：给出分类器中每个不同类别的出现次数。（[2509](https://github.com/stefan-jansen/zipline/issues/2509)）

+   `ConstantMixin`：用于创建具有常数值的 Pipeline 项的混合类。（[2697](https://github.com/stefan-jansen/zipline/issues/2697)）

+   `if_else()`：允许用户创建条件表达式，根据两个项的输出之一进行选择。([2697](https://github.com/stefan-jansen/zipline/issues/2697))

+   `fillna()`：允许用户用常量值或其他项的值填充缺失数据。([2697](https://github.com/stefan-jansen/zipline/issues/2697))

+   `clip()`：允许用户将因子值限制在给定范围内。([2708](https://github.com/stefan-jansen/zipline/issues/2708))

+   `mean()`、`stddev()`、`max()`、`min()`、`median()`、`sum()`、`notnull_count()`：将整个域的数据汇总为标量因子。([2697](https://github.com/stefan-jansen/zipline/issues/2697))

### 增强功能

+   新增了国际 Pipelines。([2262](https://github.com/stefan-jansen/zipline/issues/2262))

+   新增了 DataSetFamily（原名 MultiDimensionalDataSet），一种创建共享相同列的常规 DataSet 集合的简写方式。([2402](https://github.com/stefan-jansen/zipline/issues/2402))

+   新增了`get_column()`，用于按名称查找列。([2210](https://github.com/stefan-jansen/zipline/issues/2210))

+   新增了`CheckWindowsClassifier`，允许我们使用 Pipeline 测试分类和字符串列的回溯窗口。([2458](https://github.com/stefan-jansen/zipline/issues/2458))

+   新增了`PipelineHooks`，现在用于显示 Pipline 进度条。([2467](https://github.com/stefan-jansen/zipline/issues/2467))

+   `BoundColumn`比较现在将导致错误。这防止了编写`EquityPricing.volume > 1000`（静默返回错误数据）而不是`EquityPricing.volume.latest > 1000`。([2537](https://github.com/stefan-jansen/zipline/issues/2537))

+   为 Pipeline 增加了货币转换支持。([2586](https://github.com/stefan-jansen/zipline/issues/2586))

+   新增了`--benchmark-file`和`--benchmark-symbol`命令行参数，以便更方便地提供基准测试数据。([2642](https://github.com/stefan-jansen/zipline/issues/2642))

+   增加了对 Python 3.6 的支持。([2643](https://github.com/stefan-jansen/zipline/issues/2643))

+   为 Factor.peer_count 增加了`mask`参数。([2676](https://github.com/stefan-jansen/zipline/issues/2676))

+   新增了`if_else()`和`fillna()`，允许在 Pipelines 中使用条件逻辑。([2691](https://github.com/stefan-jansen/zipline/issues/2691))

+   为 Factor 增加了每日汇总方法，用于收集整个宇宙的汇总统计数据。([2697](https://github.com/stefan-jansen/zipline/issues/2697))

+   新增了`clip()`方法，用于将值剪辑到范围内。([2708](https://github.com/stefan-jansen/zipline/issues/2708))

+   增加了对 Pipeline 项算术运算的支持，可处理超过 32 项。([2727](https://github.com/stefan-jansen/zipline/issues/2727))

### 错误修复

+   修复了对非唯一 sid->exchange 映射的支持。([2289](https://github.com/stefan-jansen/zipline/issues/2289))

+   修复了股息警告导致的崩溃。（[2323](https://github.com/stefan-jansen/zipline/issues/2323)）

+   修复了当周一先于新年时`week_start`的问题。（[2394](https://github.com/stefan-jansen/zipline/issues/2394)）

+   确保在解包空数据框时使用正确的数据类型。（[2444](https://github.com/stefan-jansen/zipline/issues/2444)）

+   修复了一个错误，即当流水线项的`window_length=0`时，在调用`compute()`之前不会复制输入，这可能导致如果输入在流水线中被重用，则结果不正确。（[2723](https://github.com/stefan-jansen/zipline/issues/2723)）

### 性能

+   添加了`HDF5DailyBarWriter`，它以新的 HDF5 文件格式写入每日定价。每个 OHLCV 字段都存储为分块 HDF5 数据集中的二维数组，每行代表一个 sid，每列代表一天。该文件还支持多个国家。添加了`HDF5DailyBarReader`，它实现了 BarReader 接口，并可以读取由 HDF5DailyBarWriter 编写的文件。（[2295](https://github.com/stefan-jansen/zipline/issues/2295)）

+   向量化股息率计算（[2298](https://github.com/stefan-jansen/zipline/issues/2298)）

+   改进了`RollingPearson`和`RollingPearsonOfReturns`流水线因子的性能。（[2071](https://github.com/stefan-jansen/zipline/issues/2071)）

### 维护和重构

+   使`parameter_space()`在运行之间重置实例固定装置（[2433](https://github.com/stefan-jansen/zipline/issues/2433)）

+   移除了未使用的国债曲线数据处理。（[2626](https://github.com/stefan-jansen/zipline/issues/2626)）

### 杂项

#### 国际流水线

流水线现在支持国际数据。

流水线是一种工具，允许您在资产集合和时间段上定义计算。过去，您只能在美国的股票市场上运行流水线。现在，您可以指定流水线应该计算的域。“域”一词指的是数学概念中的“函数的域”，即函数的可能输入集合。在流水线的上下文中，域指定了流水线表达式应该计算的资产集合和相应的交易日历。

例如，以下流水线每天返回所有加拿大股票的最新收盘价和成交量。

```py
pipe = Pipeline(
    columns={
        'price': EquityPricing.close.latest,
        'volume': EquityPricing.volume.latest,
        'mcap': factset.Fundamentals.mkt_val.latest,
    },
    domain=CA_EQUITIES,
) 
```

与货币相关的另一个挑战是，一些交易所不要求股票以当地货币列出。例如，伦敦证券交易所只有大约 75%的上市股票以英镑计价。其余 25%主要以欧元或美元列出。这使得进行跨部门比较变得困难。

为了解决这个问题，大多数人依赖货币转换将基于价格的字段转换为同一货币。管道列现在支持一个 `fx` 方法，用于指定数据应该被视为哪种货币。此方法仅适用于“货币感知”的术语，例如开盘或收盘，但不适用于不关心货币的术语，如成交量。

目前，没有方法将国际数据加载到捆绑包中。我们正在寻找方法，使获取国际数据到 Zipline 变得容易。

([2265](https://github.com/stefan-jansen/zipline/issues/2265), [2262](https://github.com/stefan-jansen/zipline/issues/2262), 以及许多其他)

Zipline 目前支持运行管道的域（使用最新的 [trading-calendars](https://pypi.org/project/trading-calendars/) 包）如下：

+   阿根廷

+   澳大利亚

+   奥地利

+   比利时

+   巴西

+   加拿大

+   智利

+   中国

+   捷克共和国

+   哥伦比亚

+   捷克

+   芬兰

+   法国

+   德国

+   希腊

+   香港

+   匈牙利

+   印度

+   印度尼西亚

+   爱尔兰

+   意大利

+   日本

+   马来西亚

+   墨西哥

+   荷兰

+   新西兰

+   挪威

+   巴基斯坦

+   秘鲁

+   菲律宾

+   波兰

+   葡萄牙

+   俄罗斯

+   新加坡

+   西班牙

+   瑞典

+   台湾

+   泰国

+   土耳其

+   英国

+   美国

+   南非

+   韩国

+   瑞士

([2301](https://github.com/stefan-jansen/zipline/issues/2301), [2333](https://github.com/stefan-jansen/zipline/issues/2333), [2338](https://github.com/stefan-jansen/zipline/issues/2338), [2355](https://github.com/stefan-jansen/zipline/issues/2355), [2369](https://github.com/stefan-jansen/zipline/issues/2369), [2550](https://github.com/stefan-jansen/zipline/issues/2550), [2552](https://github.com/stefan-jansen/zipline/issues/2552), [2559](https://github.com/stefan-jansen/zipline/issues/2559))

#### 数据集家族

> 数据集家族用于表示数据的唯一标识符需要超过资产和日期坐标。一个 `数据集家族` 也可以被认为是一组 `数据集` 对象，每个对象都有相同的列、域和 ndim。
> 
> `数据集家族` 对象是通过一个或多个 `列` 对象定义的，再加上一个额外的字段：`extra_dims`。
> 
> `extra_dims` 字段定义了除资产和日期之外必须固定的坐标，以产生一个逻辑时间序列。列对象决定了家族切片将共享的列。
> 
> `extra_dims` 被表示为一个有序字典，其中键是维度名称，值是该维度上唯一值的集合。
> 
> 要在管道表达式中使用`DataSetFamily`，必须使用`slice()`方法为每个额外的维度选择一个特定的值。例如，给定一个`DataSetFamily`：
> 
> ```py
> class SomeDataSet(DataSetFamily):
>     extra_dims = [
>         ('dimension_0', {'a', 'b', 'c'}),
>         ('dimension_1', {'d', 'e', 'f'}),
>     ]
> 
>     column_0 = Column(float)
>     column_1 = Column(bool) 
> ```
> 
> 这个数据集可能代表一个具有以下列的表格：
> 
> ```py
> sid :: int64
> asof_date :: datetime64[ns]
> timestamp :: datetime64[ns]
> dimension_0 :: str
> dimension_1 :: str
> column_0 :: float64
> column_1 :: bool 
> ```
> 
> 在这里，我们可以看到隐含的`sid`、`asof_date`和`timestamp`列以及额外的维度列。
> 
> 这个`DataSetFamily`可以通过以下方式转换为常规`DataSet`：
> 
> ```py
> DataSetSlice = SomeDataSet.slice(dimension_0='a', dimension_1='e') 
> ```
> 
> 这个切片数据集表示来自更高维度数据集的行，其中`(dimension_0 == 'a') & (dimension_1 == 'e')`。

([2402](https://github.com/stefan-jansen/zipline/issues/2402), [2452](https://github.com/stefan-jansen/zipline/issues/2452), [2456](https://github.com/stefan-jansen/zipline/issues/2456))

## 版本 1.3.0

发布：

1.3.0

日期：

2018 年 7 月 16 日

此版本包括几个增强功能和性能改进，以及少量错误修复。我们建议所有用户升级到此版本。

注意

这可能是 Zipline 1.x 系列的最后一个次要版本。下一个版本将是 Zipline 2.0，它将包括一些小的破坏性更改，以支持国际股票。

### 亮点

#### 支持更新的 Numpy/Pandas 版本

Zipline 在更新 numpy、pandas 和其他“PyData”生态系统软件包的版本时一直非常保守。这种保守主义主要是因为 Zipline 被用作[Quantopian](https://www.quantopian.com/)的回测引擎，这意味着更新软件包版本可能会破坏大量已安装的代码库。当然，许多 Zipline 用户没有 Quantopian 那样的向后兼容性要求，他们希望能够使用最新和最棒的软件包版本。

作为此版本的一部分，我们现在使用两个软件包配置构建和测试 Zipline：

+   “稳定”，使用 numpy 版本 1.11 和 pandas 版本 0.18.1。

+   “最新”，使用 numpy 版本 1.14 和 pandas 版本 0.22.0。

其他组合的 numpy 和 pandas**可能**可以工作，但这些软件包集将在我们的正常开发周期中构建和测试。

向前推进，我们的目标是继续在任何给定时间维护两套软件包的支持。“稳定”软件包集将相对不频繁地更改，并将包含 Quantopian 支持的 numpy 和 pandas 版本。“最新”软件包集将定期更改，并将包含最近发布的 numpy 和 pandas 版本。

我们希望通过这些更改在稳定性和新颖性之间取得平衡，而不通过支持每种可能的包组合来承担过大的维护负担。([2194](https://github.com/stefan-jansen/zipline/issues/2194))

#### 独立的`trading_calendars`模块

Zipline 最受欢迎的功能之一是其交易日历集合，提供有关各种市场假日和交易时间的信息。作为此版本的一部分，Zipline 的日历相关功能已移至单独的[trading-calendars](https://pypi.org/project/trading-calendars/)包，允许只需要访问日历的用户在不承担 Zipline 其余依赖项的情况下使用它们。

为了向后兼容，Zipline 将继续重新导出与日历相关的函数。例如，`zipline.get_calendar()`仍然存在，但现在它是`trading_calendars.get_calendar`的别名。依赖此功能的用户应将其导入更新到`trading_calendars`中的新位置。([2219](https://github.com/stefan-jansen/zipline/issues/2219))

#### 自定义交易记录器

此版本增加了实验性支持，允许使用用户定义的`Blotter`子类运行 Zipline。这一更改的主要动机是使从 Zipline CLI 运行实时算法变得更加容易。

配置自定义交易记录器的两种主要方式：

1.  您可以将`Blotter`的实例作为`blotter`参数传递给`zipline.run_algorithm()`。（此功能之前已经存在，但文档不够完善。）

1.  您可以在`extension.py`中注册一个名为**工厂**的交易记录器，并通过命令行上的`--blotter`标志传递名称。

示例**(2)**的使用可能如下所示：

~/.zipline/extension.py

```py
from zipline.extensions import register
from zipline.finance.blotter import Blotter, SimulationBlotter
from zipline.finance.cancel_policy import EODCancel

@register(Blotter, 'my-blotter')
def my_blotter():
  """Create a SimulationBlotter with a non-default cancel policy.
 """
    return SimulationBlotter(cancel_policy=EODCancel()) 
```

要从命令行使用此工厂运行 zipline，我们将这样调用 zipline：

```py
$ zipline run --blotter my-blotter <...other-args...> 
```

作为此更改的一部分，`Blotter`类已被转换为抽象基类。模拟中使用的默认交易记录器现在名为`zipline.finance.blotter.SimulationBlotter`。

([2210](https://github.com/stefan-jansen/zipline/issues/2210), [2251](https://github.com/stefan-jansen/zipline/issues/2251))

#### 自定义命令行参数

本次发布增加了对`zipline`命令行界面传递自定义参数的支持。自定义命令行参数通过`-x`标志后跟一个`key=value`对来传递。通过这种方式传递的参数可以从 Python 代码（例如，算法或扩展）中通过`zipline.extension_args`的属性来访问。例如，如果 zipline 这样被调用：

```py
$ zipline -x argle=bargle run ... 
```

那么`zipline.extension_args.argle`的结果将是字符串`"bargle"`。

自定义参数可以通过在键中包含`.`字符来分组到命名空间中。例如，如果 zipline 这样被调用：

```py
$ zipline -x argle.bargle=foo 
```

那么`zipline.extension_args.argle`将包含一个具有`bargle`属性的对象，该属性包含字符串`"foo"`。键可以包含多个点来创建嵌套的命名空间。([2210](https://github.com/stefan-jansen/zipline/issues/2210))

### 功能增强

+   增加了对 pandas 0.22 和 numpy 1.14 的支持。详情请参见上文。([2194](https://github.com/stefan-jansen/zipline/issues/2194))

+   将`zipline.utils.calendars`移至一个单独可安装的[trading-calendars](https://pypi.org/project/trading-calendars/)包中。([2219](https://github.com/stefan-jansen/zipline/issues/2219))

+   增加了对使用`-x`标志指定自定义字符串参数的支持。详情请参见上文。([2210](https://github.com/stefan-jansen/zipline/issues/2210))

### 实验性功能

警告

实验性功能可能会发生变化。

+   增加了对注册自定义子类`zipline.finance.blotter.Blotter`的支持。详情请参见上文。([2210](https://github.com/stefan-jansen/zipline/issues/2210), [2251](https://github.com/stefan-jansen/zipline/issues/2251))

### 错误修复

+   修复了在`zipline.pipeline.Factor.winsorize()`中，当确定 winsorization 的截止阈值时，NaN 值被错误地包含在值计数中的错误。([2138](https://github.com/stefan-jansen/zipline/issues/2138))

+   修复了在`zipline.pipeline.Factor.top()`中，当计数为 1 且没有 groupby 时发生的崩溃。([2218](https://github.com/stefan-jansen/zipline/issues/2218))

+   修复了一个错误，即当调用`data.history`时，如果回溯期为负数，则会从未来获取价格。([2164](https://github.com/stefan-jansen/zipline/issues/2164))

+   修复了一个错误，即`StopOrder`、`zipline.finance.execution.LimitOrder`和`zipline.finance.execution.StopLimitOrder`的价格无论资产的 tick 大小如何，都被四舍五入到最接近的便士。现在，价格根据所订购资产的`tick_size`属性进行四舍五入。([2211](https://github.com/stefan-jansen/zipline/issues/2211))

### 性能

+   改进了定期交易的资产获取每分钟价格的性能。（[2108](https://github.com/stefan-jansen/zipline/issues/2108)）

+   通过调整缓存大小，改进了获取许多资产每分钟价格的性能。（[2110](https://github.com/stefan-jansen/zipline/issues/2110)）

### 维护和重构

+   重构了 Zipline 测试套件的大部分内容，使其更容易更改`zipline.algorithm.TradingAlgorithm`的签名。（[2169](https://github.com/stefan-jansen/zipline/issues/2169)，[2168](https://github.com/stefan-jansen/zipline/issues/2168)，[2165](https://github.com/stefan-jansen/zipline/issues/2165)，[2171](https://github.com/stefan-jansen/zipline/issues/2171)）

### 构建

+   增加了对使用 pandas 0.18 和 0.22 运行 travis 构建的支持。（[2194](https://github.com/stefan-jansen/zipline/issues/2194)）

+   在 travis 构建矩阵中添加了 OSX 构建。（[2244](https://github.com/stefan-jansen/zipline/issues/2244)）

## 发布 1.2.0

发布：

1.2.0

日期：

2018 年 4 月 4 日

### 亮点

#### 可扩展的风险和性能指标（[2081](https://github.com/stefan-jansen/zipline/issues/2081)）

风险和性能指标是 Zipline 在运行模拟时计算的值的汇总，例如：回报率或夏普比率。1.1.2 引入了一个新的 API，用于注册用户定义的自定义风险和性能指标。我们还使得在不计算任何指标的情况下运行回测成为可能，以改善调试算法时的反馈循环。

有关更多信息，请参阅 Metrics。

#### 文档、交易日历和基准

Zipline 现在默认使用`quandl`捆绑包，您需要一个 API 密钥，可以在数据捆绑包文档中找到相关信息。

我们添加了许多教程和文档更新，包括如何创建自己的`TradingCalendar`，通过 Zipline CLI 将其传递给您的算法，以及如何使用`csvdir`捆绑包使用自定义 csv 数据。

Zipline 不再为 Python 3.4 进行测试和打包。

Zipline 现在使用[IEX Trading](https://iextrading.com) API 请求 SPY 的数据，SPY 是 Zipline 回测使用的默认基准，不再使用`pandas-datareader`。您可以使用此数据运行长达 5 年的回测。

### 增强功能

+   将分钟文件缓存默认增长，到 1550（[1906](https://github.com/stefan-jansen/zipline/issues/1906)）

+   将默认佣金更改为.001（[1946](https://github.com/stefan-jansen/zipline/issues/1946)）

+   启用计算多个管道的能力（[1974](https://github.com/stefan-jansen/zipline/issues/1974)）

+   允许用户在日历之间切换（[1800](https://github.com/stefan-jansen/zipline/issues/1800)）

+   新过滤器`NoMissingValues`（[1969](https://github.com/stefan-jansen/zipline/issues/1969)）

+   在`AssetFinder(nonexistent_path)`上失败得更好（[2000](https://github.com/stefan-jansen/zipline/issues/2000)）

+   实施 csvdir 捆绑包（[1860](https://github.com/stefan-jansen/zipline/issues/1860)）

+   更新 quandl_bundle 以使用 Quandl API v3（[1990](https://github.com/stefan-jansen/zipline/issues/1990)）

+   添加`FixedBasisPointsSlippage`滑点模型（[2047](https://github.com/stefan-jansen/zipline/issues/2047)）

+   创建 MinLeverage 控制（[2064](https://github.com/stefan-jansen/zipline/issues/2064)）

### 实验性功能

警告

实验性功能可能会更改。

无

### 错误修复

+   `history`调用频率为`1d`时，当使用 Panel 作为分钟数据源时现在可以工作（[1920](https://github.com/stefan-jansen/zipline/issues/1920)）

+   在使用期货每日 bar 阅读器时检查合约是否存在（[1892](https://github.com/stefan-jansen/zipline/issues/1892)）

+   `NoDataBeforeDate`的边缘情况（[1894](https://github.com/stefan-jansen/zipline/issues/1894)）

+   在 Python 2.7.5 中修复 frame 列验证（[1954](https://github.com/stefan-jansen/zipline/issues/1954)）

+   修复分钟面板数据回测的每日历史记录（[1920](https://github.com/stefan-jansen/zipline/issues/1920)）

+   `get_last_traded_dt`期望一个交易日（[2087](https://github.com/stefan-jansen/zipline/issues/2087)）

+   每日调整视角修复（[2089](https://github.com/stefan-jansen/zipline/issues/2089)）

### 性能

+   将算法账户验证从`handle_data`中每分钟发生一次改为仅在每天结束时发生一次（[1884](https://github.com/stefan-jansen/zipline/issues/1884)）

+   Blaze 核心加载器性能改进（[1866](https://github.com/stefan-jansen/zipline/issues/1866)）

+   添加一个仅计算 beta 的新因子（[2021](https://github.com/stefan-jansen/zipline/issues/2021)）

+   减少 Quandl WIKI 价格捆绑包的内存占用（[2053](https://github.com/stefan-jansen/zipline/issues/2053)）

### 维护和重构

+   添加`CachedObject.expired()`（[1881](https://github.com/stefan-jansen/zipline/issues/1881)）

+   将`RollingLinearRegressionOfReturns`因子设置为 window_safe（[1902](https://github.com/stefan-jansen/zipline/issues/1902)）

+   将`RSI`因子设置为 window_safe（[1904](https://github.com/stefan-jansen/zipline/issues/1904)）

+   更新以改善文档生成（[1890](https://github.com/stefan-jansen/zipline/issues/1890)）

+   移除并清零未使用的国债曲线（[1910](https://github.com/stefan-jansen/zipline/issues/1910)）

+   Networkx 2 改变了 out_degree 的行为（[1996](https://github.com/stefan-jansen/zipline/issues/1996)）

+   向`DataPortal`传递日历（[2026](https://github.com/stefan-jansen/zipline/issues/2026)）

+   移除旧的 Yahoo 代码（[2032](https://github.com/stefan-jansen/zipline/issues/2032)）

+   通过最新交易日同步和填充基准（[2044](https://github.com/stefan-jansen/zipline/issues/2044)）

+   当 QUANDL_API_KEY 缺失时提供更好的错误信息（[2078](https://github.com/stefan-jansen/zipline/issues/2078)）

+   改进了管道引擎中日期错位时的错误信息（[2131](https://github.com/stefan-jansen/zipline/issues/2131)）

### 构建

+   更新我们使用的 conda 工具以修复我们的打包问题（[1942](https://github.com/stefan-jansen/zipline/issues/1942)）

+   将 empyrical 升级至 0.3.2 版本（[1983](https://github.com/stefan-jansen/zipline/issues/1983)）

+   更新 conda 工具并移除 Python 3.4 构建（[2009](https://github.com/stefan-jansen/zipline/issues/2009)）

+   将 empyrical 升级至 0.3.3 版本（[2014](https://github.com/stefan-jansen/zipline/issues/2014)）

+   将 empyrical 升级至 0.3.4 版本（[2098](https://github.com/stefan-jansen/zipline/issues/2098)）

+   将 empyrical 升级至 0.4.2 版本（[2125](https://github.com/stefan-jansen/zipline/issues/2125)）

### 文档

+   在 zipline.io 文档中包含`MACDSignal`（[1828](https://github.com/stefan-jansen/zipline/issues/1828)）

+   从入门教程中移除所有提及雅虎的内容（[1845](https://github.com/stefan-jansen/zipline/issues/1845)）

+   在 README 中添加贡献和问题部分（[1889](https://github.com/stefan-jansen/zipline/issues/1889)）

+   添加关于使用 conda 环境进行安装的信息（[1922](https://github.com/stefan-jansen/zipline/issues/1922)）

+   修复入门教程链接（[1932](https://github.com/stefan-jansen/zipline/issues/1932)）

+   添加干净的文档（[1943](https://github.com/stefan-jansen/zipline/issues/1943)）

+   为基准和财政部提取器添加不同的警告（[1971](https://github.com/stefan-jansen/zipline/issues/1971)）

+   添加 CONTRIBUTING.rst 文件（[2033](https://github.com/stefan-jansen/zipline/issues/2033)）

+   添加关于创建自定义`TradingCalendar`的教程（[2035](https://github.com/stefan-jansen/zipline/issues/2035)）

+   更新文档和教程，包括数据摄取、入门和 csvdir（[2073](https://github.com/stefan-jansen/zipline/issues/2073)）

+   记录了新的风险和绩效指标 API（[2081](https://github.com/stefan-jansen/zipline/issues/2081)）。

+   修正了`--bundle-timestamp`描述中的一个拼写错误（[2123](https://github.com/stefan-jansen/zipline/issues/2123)）

### 杂项

无

## 发布 1.1.1

发布：

1.1.1

日期：

2017 年 7 月 5 日

### 亮点

Zipline 现在除了股票外，还广泛支持期货。它也正在为 Python 3.5 进行测试和打包。

由于雅虎更改了其 API 端点，导致用户无法下载用于回测的基准数据，我们也见证了由此产生的重大变化。自那次变更以来，我们已将所有与雅虎相关的基准代码替换为对谷歌财经的引用，并移除了所有已弃用的雅虎代码，包括自定义雅虎包的使用。

### 增强功能

+   为 BarData 添加了一个属性，以了解当前会话的分钟数（[1713](https://github.com/stefan-jansen/zipline/issues/1713)）

+   为不存在的根符号添加了更好的错误信息（[1715](https://github.com/stefan-jansen/zipline/issues/1715)）

+   添加`StaticSids`管道过滤器（[1717](https://github.com/stefan-jansen/zipline/issues/1717)）

+   允许`zipline.data.data_portal.DataPortal.get_spot_value`接受多个资产 ([1719](https://github.com/stefan-jansen/zipline/issues/1719))

+   在`lookup_generic`中添加了`ContinuousFuture` ([1718](https://github.com/stefan-jansen/zipline/issues/1718))

+   在`exchange_calendar_cfe`中添加 CFE Adhoc 假日 ([1698](https://github.com/stefan-jansen/zipline/issues/1698))

+   允许覆盖订单金额的舍入 ([1722](https://github.com/stefan-jansen/zipline/issues/1722))

+   将连续期货调整风格作为参数 ([1726](https://github.com/stefan-jansen/zipline/issues/1726))

+   添加了对期货滑点和佣金模型的初步支持 ([1738](https://github.com/stefan-jansen/zipline/issues/1738))

+   修复了成本基础计算中的一个 bug，并将所有提及的`sid`改为`asset` ([1757](https://github.com/stefan-jansen/zipline/issues/1757))

+   为期货添加滑点和佣金模型 ([1748](https://github.com/stefan-jansen/zipline/issues/1748))

+   在我们的 Dockerfile 中使用 Python 3.5 ([1806](https://github.com/stefan-jansen/zipline/issues/1806))

+   允许以块的形式运行管道 ([1811](https://github.com/stefan-jansen/zipline/issues/1811))

+   为 BenchmarkSource 添加了 get_range 方法 ([1815](https://github.com/stefan-jansen/zipline/issues/1815))

+   添加了对 Pipeline 中重命名分类器的支持 ([1833](https://github.com/stefan-jansen/zipline/issues/1833))

### 实验性功能

警告

实验性功能可能会发生变化。

无

### 错误修复

+   在`zipline.data.minute_bars`中通过使用整数除法而不是浮点除法来修复浮点除法问题 ([1683](https://github.com/stefan-jansen/zipline/issues/1683))

+   在`zipline.pipeline.loaders.blaze.core`中按`asof_date`排序数据以解决时间戳冲突 ([1710](https://github.com/stefan-jansen/zipline/issues/1710))

+   将 Yahoo 替换为 Google Finance 的基准数据 ([1812](https://github.com/stefan-jansen/zipline/issues/1812))

+   黄金和白银期货合约仅在特定月份交易 ([1779](https://github.com/stefan-jansen/zipline/issues/1779))

+   修复了在交易日历初始化时使用时区感知时间时出现的 bug ([1802](https://github.com/stefan-jansen/zipline/issues/1802))

+   修复了期货价格四舍五入时的精度问题 ([1788](https://github.com/stefan-jansen/zipline/issues/1788))

### 性能

+   在获取前向填充的收盘价时避免重复的递归调用 ([1735](https://github.com/stefan-jansen/zipline/issues/1735))

### 维护和重构

+   在调整模块中添加了 linter 建议 ([1712](https://github.com/stefan-jansen/zipline/issues/1712))

+   清理了重采样收盘价中的命名和逻辑 ([1728](https://github.com/stefan-jansen/zipline/issues/1728))

+   对多个连续期货使用三月季度周期 ([1762](https://github.com/stefan-jansen/zipline/issues/1762))

+   改进了 Transaction 对象的 repr 表示 ([1746](https://github.com/stefan-jansen/zipline/issues/1746))

+   缩短了 Asset 对象的 repr（[1786](https://github.com/stefan-jansen/zipline/issues/1786)）

+   移除了对 empyrical 的信息比率的使用（[1854](https://github.com/stefan-jansen/zipline/issues/1854)）

### 构建

+   添加了 Python 3.5 包（[1701](https://github.com/stefan-jansen/zipline/issues/1701)）

+   交换 conda-build 参数，这样我们就不在每次 CI 构建时构建包（[1813](https://github.com/stefan-jansen/zipline/issues/1813)）

### 文档

+   添加了 Zipline 开发指南，供人们阅读如何为 Zipline 做出贡献（[1820](https://github.com/stefan-jansen/zipline/issues/1820)）

+   显示交易所是股票的必需项（[1731](https://github.com/stefan-jansen/zipline/issues/1731)）

+   更新了 Zipline 初学者教程笔记本（[1707](https://github.com/stefan-jansen/zipline/issues/1707)）

+   将 PipelineEngine、pipeline Term、Factors 和其他 pipeline 相关内容添加到文档中（[1826](https://github.com/stefan-jansen/zipline/issues/1826)）

### 杂项

+   使用带有`run_algorithm`的 csv 市场数据，这样我们在测试时就不会尝试下载数据（[1793](https://github.com/stefan-jansen/zipline/issues/1793)）

+   更新 Dockerfile 以使用 Python 3.5

## 发布 1.1.0

发布：

1.1.0

日期：

2017 年 3 月 10 日

此版本旨在为 Zipline 提供对 pandas 0.18 的支持，以及多个错误修复、API 更改和许多性能更改。

### 增强功能

+   如果日期不在日历中，分钟条读取器会捕获 NoDataOnDate 异常。之前，分钟条读取器是向前填充的，但现在它为 OHLC 返回 nan，为 V 返回 0。（[1488](https://github.com/stefan-jansen/zipline/issues/1488)）

+   为`BcolzMinuteBarWriter`添加了`truncate`方法（[1499](https://github.com/stefan-jansen/zipline/issues/1499)）

+   升级到 pandas 0.18.1 和 numpy 1.11.1（[1339](https://github.com/stefan-jansen/zipline/issues/1339)）

+   为 Pipeline 添加了收益估计季度加载器（[1396](https://github.com/stefan-jansen/zipline/issues/1396)）

+   创建了一个受限列表管理器，它在实例化时接收有关受限 sid 的信息并将其存储在内存中（[1487](https://github.com/stefan-jansen/zipline/issues/1487)）

+   为`DataPortal`添加了`last_available{session, minute}`参数（[1528](https://github.com/stefan-jansen/zipline/issues/1528)）

+   添加了`SpecificAssets`过滤器（[1530](https://github.com/stefan-jansen/zipline/issues/1530)）

+   添加了算法请求未来链的当前合约的能力（[1529](https://github.com/stefan-jansen/zipline/issues/1529)）

+   在`DataPortal`和`OrderedContracts`的当前和支持方法中添加了`chain`字段（[1538](https://github.com/stefan-jansen/zipline/issues/1538)）

+   为连续期货添加了历史记录（[1539](https://github.com/stefan-jansen/zipline/issues/1539)）

+   为连续期货添加了调整后的历史记录（[1548](https://github.com/stefan-jansen/zipline/issues/1548)）

+   添加考虑期货合约成交量的滚动风格，特别适用于连续期货（[1556](https://github.com/stefan-jansen/zipline/issues/1556)）

+   在调用 Zipline API 函数时，当不在运行模拟中时，添加更好的错误消息（[1593](https://github.com/stefan-jansen/zipline/issues/1593)）

+   添加`MACDSignal()`、`MovingAverageConvergenceDivergenceSignal()`和`AnnualizedVolatility()`作为内置因子（[1588](https://github.com/stefan-jansen/zipline/issues/1588)）

+   允许在`attach_pipeline`中使用自定义日期块运行管道（[1617](https://github.com/stefan-jansen/zipline/issues/1617)）

+   在交易记录中添加`order_batch`（[1596](https://github.com/stefan-jansen/zipline/issues/1596)）

+   添加矢量化 lookup_symbol（[1627](https://github.com/stefan-jansen/zipline/issues/1627)）

+   为 SlippageModel 类固化相等比较（[1657](https://github.com/stefan-jansen/zipline/issues/1657)）

+   为截尾结果添加因子（[1696](https://github.com/stefan-jansen/zipline/issues/1696)）

### 错误修复

+   将 str 更改为 string_types，以避免在类型检查 unicode 而不是 str 类型时出错（[1315](https://github.com/stefan-jansen/zipline/issues/1315)）

+   当未指定数据源时，算法默认使用 quantopian-quandl 包（[1479](https://github.com/stefan-jansen/zipline/issues/1479)）（[1374](https://github.com/stefan-jansen/zipline/issues/1374)）

+   在计算股息比率时捕获所有缺失数据异常（[1507](https://github.com/stefan-jansen/zipline/issues/1507)）

+   根据有序资产创建调整，而不是根据资产在集合中的位置创建调整（[1547](https://github.com/stefan-jansen/zipline/issues/1547)）

+   修复当用户查询`asof_date`列时，blaze 管道查询的问题（[1608](https://github.com/stefan-jansen/zipline/issues/1608)）

+   应将日期时间转换为 UTC。返回的 DataFrame 正在将整数转换为美国/东部时间戳，可能导致返回的日期变为前一天的日期（[1635](https://github.com/stefan-jansen/zipline/issues/1635)）

+   修复`IchimokuKinkoHyo`因子的默认输入（[1638](https://github.com/stefan-jansen/zipline/issues/1638)）

### 性能

+   移除`get_calendar('NYSE')`的调用，缩短 zipline 导入时间，使 CLI 更加响应迅速，占用更少内存（[1471](https://github.com/stefan-jansen/zipline/issues/1471)）

+   在不再需要时，对管道项进行引用计数和释放（[1484](https://github.com/stefan-jansen/zipline/issues/1484)）

+   将调用 minute_to_session_label 的次数减少高达 75%（[1492](https://github.com/stefan-jansen/zipline/issues/1492)）

+   加快连续会话中分钟数计数速度（[1497](https://github.com/stefan-jansen/zipline/issues/1497)）

+   移除/推迟了对大型索引的 get_loc 调用（[1504](https://github.com/stefan-jansen/zipline/issues/1504)）（[1503](https://github.com/stefan-jansen/zipline/issues/1503)）

+   在`calc_dividend_ratios`中用`get_indexer`替换了`get_loc`调用（[1510](https://github.com/stefan-jansen/zipline/issues/1510)）

+   加快了分钟到会话的抽样速度（[1549](https://github.com/stefan-jansen/zipline/issues/1549)）

+   在`data.current`中添加了一些微优化（[1561](https://github.com/stefan-jansen/zipline/issues/1561)）

+   为管道初始工作区添加了优化（[1521](https://github.com/stefan-jansen/zipline/issues/1521)）

+   更多的内存节省（[1599](https://github.com/stefan-jansen/zipline/issues/1599)）

### 维护和重构

+   更新了杠杆 ETF 列表（[747](https://github.com/stefan-jansen/zipline/issues/747)）（[1434](https://github.com/stefan-jansen/zipline/issues/1434)）

+   为 Order 类的`__getitem__`添加了额外字段（[1483](https://github.com/stefan-jansen/zipline/issues/1483)）

+   为分钟和会话阅读器添加了`BarReader`基类（[1486](https://github.com/stefan-jansen/zipline/issues/1486)）

+   移除了`future_chain` API 方法，将由`data.current_chain`替换（[1502](https://github.com/stefan-jansen/zipline/issues/1502)）

+   将 zipline 重新放回 blaze 主分支（[1505](https://github.com/stefan-jansen/zipline/issues/1505)）

+   在 Dockerfile 中添加了 Tini，并为 numpy、pandas 和 scipy 设置了版本范围（[1514](https://github.com/stefan-jansen/zipline/issues/1514)）

+   弃用了`set_do_not_order_list`（[1487](https://github.com/stefan-jansen/zipline/issues/1487)）

+   使用`Timedelta`代替`DateOffset`（[1487](https://github.com/stefan-jansen/zipline/issues/1487)）

+   更新并固定了更多的开发需求（[1642](https://github.com/stefan-jansen/zipline/issues/1642)）

### 构建

+   为 empyrical 添加了 numpy 的二进制依赖

+   从 Travis 中移除了旧的 numpy/pandas 版本（[1339](https://github.com/stefan-jansen/zipline/issues/1339)）

+   更新了 appveyor.yml 以适应新的 numpy 和 pandas（[1339](https://github.com/stefan-jansen/zipline/issues/1339)）

+   降级到 scipy 0.17（[1339](https://github.com/stefan-jansen/zipline/issues/1339)）

+   将 empyrical 升级到 0.2.2

### 文档

+   为最新的 zipline 单元魔法更新了示例笔记本

+   添加了 ANACONDA_TOKEN 说明（[1589](https://github.com/stefan-jansen/zipline/issues/1589)）

### 杂项

+   更改了`zipline clean`入口点的`--before`短选项。新参数是`-e`。旧参数`-b`与`--bundle`短选项冲突（[1625](https://github.com/stefan-jansen/zipline/issues/1625)）。

## 发布 1.0.2

发布：

1.0.2

日期：

2016 年 9 月 8 日

### 增强功能

+   为 blaze 核心加载器添加了前向填充检查点表。这使得加载器在查询数据时能够更高效地进行前向填充，通过限制必须搜索的最低日期。检查点应该应用新的增量（[1276](https://github.com/stefan-jansen/zipline/issues/1276)）。

+   更新 VagrantFile 以包含所有开发需求并使用更新的镜像（[1310](https://github.com/stefan-jansen/zipline/issues/1310)）。

+   允许通过资产方式计算两个二维因子之间的相关性和回归（[1307](https://github.com/stefan-jansen/zipline/issues/1307)）。

+   过滤器默认已设置为窗口安全。现在它们可以作为参数传递给其他过滤器、因子和分类器（[1338](https://github.com/stefan-jansen/zipline/issues/1338)）。

+   在`rank()`、`top()`和`bottom()`中添加了一个可选的`groupby`参数。（[1349](https://github.com/stefan-jansen/zipline/issues/1349)）。

+   添加了新的流水线过滤器，`All`和`Any`，它接受另一个过滤器，并在资产在前`window_length`天内产生任何/所有天的 True 时返回 True（[1358](https://github.com/stefan-jansen/zipline/issues/1358)）。

+   添加了新的流水线过滤器`AtLeastN`，它接受另一个过滤器和一个整数 N，并在资产在前`window_length`天内产生 N 天或更多天的 True 时返回 True（[1367](https://github.com/stefan-jansen/zipline/issues/1367)）。

+   使用外部库 empyrical 进行风险计算。Empyrical 统一了 pyfolio 和 zipline 之间的风险度量计算。Empyrical 为自定义频率的回报添加了自定义年化选项。（[855](https://github.com/stefan-jansen/zipline/issues/855)）

+   添加阿隆因子。（[1258](https://github.com/stefan-jansen/zipline/issues/1258)）

+   添加快速随机振荡因子。（[1255](https://github.com/stefan-jansen/zipline/issues/1255)）

+   添加 Dockerfile。（[1254](https://github.com/stefan-jansen/zipline/issues/1254)）

+   新增交易日历，支持跨午夜的会话，例如期货交易的 24 小时 6:01PM-6:00PM 会话。zipline.utils.tradingcalendar 现已弃用。（[1138](https://github.com/stefan-jansen/zipline/issues/1138)）（[1312](https://github.com/stefan-jansen/zipline/issues/1312)）

+   允许从因子/过滤器/分类器中切片单个列。（[1267](https://github.com/stefan-jansen/zipline/issues/1267)）

+   提供一目均衡表因子（[1263](https://github.com/stefan-jansen/zipline/issues/1263)）

+   允许在流水线项上设置默认参数。（[1263](https://github.com/stefan-jansen/zipline/issues/1263)）

+   提供变化率百分比因子。（[1324](https://github.com/stefan-jansen/zipline/issues/1324)）

+   提供线性加权移动平均因子。（[1325](https://github.com/stefan-jansen/zipline/issues/1325)）

+   添加`NotNullFilter`。（[1345](https://github.com/stefan-jansen/zipline/issues/1345)）

+   允许通过目标值定义资本变动。（[1337](https://github.com/stefan-jansen/zipline/issues/1337)）

+   添加`TrueRange`因子。([1348](https://github.com/stefan-jansen/zipline/issues/1348))

+   向`assets.db`添加即时查找功能。([1361](https://github.com/stefan-jansen/zipline/issues/1361))

+   使`can_trade`意识到资产的交易所。([1346](https://github.com/stefan-jansen/zipline/issues/1346))

+   向所有可计算项添加`downsample`方法。([1394](https://github.com/stefan-jansen/zipline/issues/1394))

+   添加 QuantopianUSFuturesCalendar。([1414](https://github.com/stefan-jansen/zipline/issues/1414))

+   启用旧版`assets.db`版本的发布。([1430](https://github.com/stefan-jansen/zipline/issues/1430))

+   为期货交易日历启用`schedule_function`。([1442](https://github.com/stefan-jansen/zipline/issues/1442))

+   不允许长度为 1 的回归。([1466](https://github.com/stefan-jansen/zipline/issues/1466))

### 实验性

+   添加对混合期货和股票历史窗口的支持，并通过数据门户启用其他期货数据访问。([1435](https://github.com/stefan-jansen/zipline/issues/1435)) ([1432](https://github.com/stefan-jansen/zipline/issues/1432))

### 错误修复

+   将内置因子`AverageDollarVolume`的更改，将缺失的收盘价或成交量值视为 0。以前，在平均之前简单地丢弃了 NaN，给剩余的值赋予了过多的权重([1309](https://github.com/stefan-jansen/zipline/issues/1309))。

+   从夏普比率计算中移除无风险利率。该比率现在是风险调整后的回报率超过调整后的回报率波动性的平均值。([853](https://github.com/stefan-jansen/zipline/issues/853))

+   当所需回报等于零时，索提诺比率将返回计算结果而不是 np.nan。该比率现在返回风险调整后的回报率超过下行风险的平均值。通过将 mar 转换为下行风险来修复错误标记的 API。([747](https://github.com/stefan-jansen/zipline/issues/747))

+   下行风险现在返回下行差分平方均值的平方根。([747](https://github.com/stefan-jansen/zipline/issues/747))

+   信息比率更新为风险调整后的回报率超过风险调整后的回报率标准差的均值。([1322](https://github.com/stefan-jansen/zipline/issues/1322))

+   Alpha 和夏普比率现已年化。([1322](https://github.com/stefan-jansen/zipline/issues/1322))

+   修复每日条形图`first_trading_day`属性读写时的单位问题。([1245](https://github.com/stefan-jansen/zipline/issues/1245))

+   当缺少可选分派模块时，不再导致 NameError。([1246](https://github.com/stefan-jansen/zipline/issues/1246))

+   当只提供时间规则而没有日期规则时，将`schedule_function`参数视为时间规则。([1221](https://github.com/stefan-jansen/zipline/issues/1221))

+   在 schedule 函数中保护交易日的开始和结束边界条件。([1226](https://github.com/stefan-jansen/zipline/issues/1226))

+   在使用频率为 1d 的历史记录时，将调整应用于前一天。([1256](https://github.com/stefan-jansen/zipline/issues/1256))

+   在尝试访问不存在的列之前，快速失败于无效的管道列。([1280](https://github.com/stefan-jansen/zipline/issues/1280))

+   修复`AverageDollarVolume`处理 NaN 的问题。([1309](https://github.com/stefan-jansen/zipline/issues/1309))

### 性能提升

+   对 blaze 核心加载器的性能改进。([1227](https://github.com/stefan-jansen/zipline/issues/1227))

+   允许并发 blaze 查询。([1323](https://github.com/stefan-jansen/zipline/issues/1323))

+   防止因缺少 bcolz 分钟数据而导致重复进行不必要的查找。([1451](https://github.com/stefan-jansen/zipline/issues/1451))

+   缓存未来链查找。([1455](https://github.com/stefan-jansen/zipline/issues/1455))

### 维护和重构

+   删除了剩余的`add_history`提及。([1287](https://github.com/stefan-jansen/zipline/issues/1287))

### 文档

### 测试

+   添加测试夹具，该夹具从分钟定价数据夹具中获取每日定价数据。([1243](https://github.com/stefan-jansen/zipline/issues/1243))

### 数据格式变更

+   `BcolzDailyBarReader`和`BcolzDailyBarWriter`使用交易日历实例，而不是序列化为`JSON`的交易日。([1330](https://github.com/stefan-jansen/zipline/issues/1330))

+   更改`assets.db`的格式以支持即时查找。([1361](https://github.com/stefan-jansen/zipline/issues/1361))

+   更改`BcolzMinuteBarReader`和`BcolzMinuteBarWriter`以支持不同的最小变动价位。([1428](https://github.com/stefan-jansen/zipline/issues/1428))

## 发布 1.0.1

发布：

1.0.1

日期：

2016 年 5 月 27 日

这是 1.0.0 版本的一个小 bug 修复版本，包含少量 bug 修复和文档改进。

### 增强功能

+   添加了对用户定义的佣金模型的支持。有关实现佣金模型的更多详细信息，请参阅`zipline.finance.commission.CommissionModel`类。([1213](https://github.com/stefan-jansen/zipline/issues/1213))

+   为 Blaze 支持的管道数据集添加了对非浮点列的支持([1201](https://github.com/stefan-jansen/zipline/issues/1201))。

+   新增`zipline.pipeline.slice.Slice`，这是一种新的管道术语，用于从另一个术语中提取单个列。切片可以通过对术语进行索引创建，按资产键入。([1267](https://github.com/stefan-jansen/zipline/issues/1267))

### 错误修复

+   修复了一个 bug，该 bug 导致管道加载器未被`zipline.run_algorithm()`正确初始化。这也影响了从 CLI 调用`zipline run`。

+   修复了导致`%%zipline` IPython 单元魔法失败的 bug ([533233fae43c7ff74abfb0044f046978817cb4e4](https://github.com/stefan-jansen/zipline/commit/533233fae43c7ff74abfb0044f046978817cb4e4))。

+   修复了在`PerTrade`佣金模型中的一个错误，该错误导致佣金被错误地应用于订单的每个部分填充，而不是订单本身，导致在提交大订单时算法被收取过多的佣金。

    `PerTrade`现在正确地按订单基础应用佣金（[1213](https://github.com/stefan-jansen/zipline/issues/1213)）。

+   在定义多个输出的`CustomFactors`上访问属性时，现在将正确返回输出切片，即使输出也是`Factor`方法的名称（[1214](https://github.com/stefan-jansen/zipline/issues/1214)）。

+   替换了对已弃用的`pandas.io.data`的使用，改为使用`pandas_datareader`（[1218](https://github.com/stefan-jansen/zipline/issues/1218)）。

+   修复了一个问题，即`zipline.api`的`.pyi`存根文件意外地被排除在 PyPI 源代码分发之外。Conda 用户应该不受影响（[1230](https://github.com/stefan-jansen/zipline/issues/1230)）。

### 文档

+   新增了一个示例，`zipline.examples.momentum_pipeline`，它使用了 Pipeline API（[1230](https://github.com/stefan-jansen/zipline/issues/1230)）。

## 发布 1.0.0

发布：

1.0.0

日期：

2016 年 5 月 19 日

### 亮点

#### Zipline 1.0 重写（[1105](https://github.com/stefan-jansen/zipline/issues/1105)）

我们对 Zipline 及其基本概念进行了大量重写，以提高运行时性能。同时，我们引入了几个新的 API。

从高层次来看，Zipline 早期版本的模拟从多个数据源的复用流中提取数据，这些数据源通过 heapq 合并。这个流被送入主模拟循环，驱动时钟前进。这种对读取所有数据的强烈依赖使得优化模拟性能变得困难，因为我们在获取的数据量和算法实际使用的数据量之间没有联系。

现在，我们只在算法需要时才获取数据。一个新类，`DataPortal`，将数据请求分派到各种数据源，并返回请求的值。这使得模拟的运行时间更紧密地与算法的复杂性而不是数据源提供的资产数量成比例。

现在，模拟不再由数据流驱动时钟，而是迭代通过一组预先计算的日或分钟时间戳。这些时间戳由`MinuteSimulationClock`和`DailySimulationClock`发出，并由`transform()`中的主循环消费。

我们已弃用`data[sid(N)]`和`history` API，用`BarData`对象上的几个方法取而代之：`current()`、`history()`、`can_trade()`和`is_stale()`。旧的 API 目前仍可使用，但会发出弃用警告。

现在，您可以将调整源传递给`DataPortal`，当我们回顾历史数据时，我们会将调整应用于定价数据。算法在 data.current 中接收到的执行价格和成交量是资产的实际交易价值。

#### 新入口点（[1173](https://github.com/stefan-jansen/zipline/issues/1173)和[1178](https://github.com/stefan-jansen/zipline/issues/1178)）

为了更容易使用 zipline，我们更新了回测的入口点。现在支持的三种运行回测的方式是：

1.  `zipline.run_algo()`

1.  `$ zipline run`

1.  `%zipline`（IPython 魔法）

#### 数据包（[1173](https://github.com/stefan-jansen/zipline/issues/1173)和[1178](https://github.com/stefan-jansen/zipline/issues/1178)）

1.0.0 引入了数据包。数据包是一组应预先加载并用于稍后运行回测的数据。这允许用户无需每次运行算法时都指定他们感兴趣的符号。这也允许我们在运行之间缓存数据。

默认情况下，将使用`quantopian-quandl`包，该包从 Quantopian 的 quandl [WIKI 数据集](https://www.quandl.com/data/WIKI)镜像中提取数据。可以使用`zipline.data.bundles.register()`注册新包，例如：

```py
@zipline.data.bundles.register('my-new-bundle')
def my_new_bundle_ingest(environ,
                         asset_db_writer,
                         minute_bar_writer,
                         daily_bar_writer,
                         adjustment_writer,
                         calendar,
                         cache,
                         show_progress):
    ... 
```

此函数应检索所需数据，然后使用已传递的写入器将该数据写入磁盘上的位置，以便 zipline 稍后可以找到。

通过将名称作为`$ zipline run`的`-b / --bundle`参数传递，或者作为`zipline.run_algorithm()`的`bundle`参数传递，可以在回测中使用此数据。

更多信息请参见数据。

#### 管道中的字符串支持（[1174](https://github.com/stefan-jansen/zipline/issues/1174)）

增加了对 Pipeline 中字符串数据的支持。`zipline.pipeline.data.Column`现在接受`object`作为数据类型，这意味着该列的加载器应该发出基于实验性新`LabelArray`类的窗口迭代器。

还添加了几个新的`Classifier`方法，用于基于字符串操作构建`Filter`实例。新方法包括：

> +   `element_of()`
> +   
> +   `startswith()`
> +   
> +   `endswith()`
> +   
> +   `has_substring()`
> +   
> +   `matches()`
> +   
> `element_of` 为所有分类器定义。剩余的方法仅对字符串数据类型的分类器定义。

### 增强功能

+   使数据加载类的接口更加一致。这包括权益条形图写入器、调整写入器和资产数据库写入器。新的接口是在构造时传递要写入的资源，稍后将数据作为数据帧或数据帧的某些迭代器提供给写入方法。这种模式允许我们将这些写入器对象作为资源传递给其他类和函数以供消费（[1109](https://github.com/stefan-jansen/zipline/issues/1109) 和 [1149](https://github.com/stefan-jansen/zipline/issues/1149)）。

+   为`zipline.pipeline.CustomFactor`添加了掩码。现在可以在实例化时将过滤器传递给自定义因子。这告诉因子只在过滤器返回 True 的股票上计算，而不是总是在整个股票宇宙上计算。（[1095](https://github.com/stefan-jansen/zipline/issues/1095)）

+   添加了`zipline.utils.cache.ExpiringCache`。这是一种缓存，它将条目包装在`zipline.utils.cache.CachedObject`中，该缓存根据 get 方法提供的 dt 管理条目的过期。（[1130](https://github.com/stefan-jansen/zipline/issues/1130)）

+   实现了`zipline.pipeline.factors.RecarrayField`，这是一种新的管道术语，设计为具有多个输出的 CustomFactor 的输出类型。（[1119](https://github.com/stefan-jansen/zipline/issues/1119)）

+   为`zipline.pipeline.CustomFactor`添加了可选的输出参数。现在，自定义因子能够计算并返回多个输出，每个输出本身都是一个因子。（[1119](https://github.com/stefan-jansen/zipline/issues/1119)）

+   增加了对字符串类型管道列的支持。这些列的加载器在遍历时应该生成`zipline.lib.labelarray.LabelArray`实例。字符串列上的`latest()`生成一个字符串类型的`zipline.pipeline.Classifier`。([1174](https://github.com/stefan-jansen/zipline/issues/1174))

+   增加了几种将分类器转换为过滤器的方法。

    新增的方法有：- `element_of()` - `startswith()` - `endswith()` - `has_substring()` - `matches()`

    `element_of` 对所有分类器都定义了。其余方法仅对字符串定义。([1174](https://github.com/stefan-jansen/zipline/issues/1174))

+   新增了`布林带`因子。该因子实现了布林带技术指标：[`zh.wikipedia.org/wiki/布林带`](https://en.wikipedia.org/wiki/Bollinger_Bands) ([1199](https://github.com/stefan-jansen/zipline/issues/1199))。

+   将 Fetcher 从 Quantopian 内部代码移至 Zipline ([1105](https://github.com/stefan-jansen/zipline/issues/1105))。

+   新增了新的内置因子，`RollingPearsonOfReturns`，`RollingSpearmanOfReturns` 和 `RollingLinearRegressionOfReturns` ([1154](https://github.com/stefan-jansen/zipline/issues/1154))

### 实验性功能

警告

实验性功能可能会发生变化。

+   新增了一个用于高效表示和计算 numpy 中字符串数据的`zipline.lib.labelarray.LabelArray`类。这个类在概念上类似于[`pandas.Categorical`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Categorical.html#pandas.Categorical "(in pandas v2.0.3)")，它将字符串数组表示为索引数组，指向一个（较小的）唯一字符串值数组。([1174](https://github.com/stefan-jansen/zipline/issues/1174))

### 错误修复

无

### 性能

无

### 维护和重构

无

### 构建

无

### 文档

+   更新了 API 方法的文档 ([1188](https://github.com/stefan-jansen/zipline/issues/1188))。

+   更新了发布流程，提到文档应该使用 python 3 构建 ([1188](https://github.com/stefan-jansen/zipline/issues/1188))。

### 杂项

+   Zipline 现在为 `zipline.api` 模块提供了一个 [stub 文件](https://www.python.org/dev/peps/pep-0484/#stub-files)。该模块通常是动态创建的，因此 stub 文件为能够消费它的工具提供了一些静态信息，例如 PyCharm ([1208](https://github.com/stefan-jansen/zipline/issues/1208))。

## 发布 0.9.0

发布：

0.9.0

日期：

2016 年 3 月 29 日

### 亮点

+   在流水线中增加了分类器和标准化方法，以及新的数据集和因子。

+   增加了对 Windows 系统的支持，并在 AppVeyor 上进行持续集成。

### 增强功能

+   增加了新的数据集 `CashBuybackAuthorizations` 和 `ShareBuybackAuthorizations`，用于流水线 API。这些数据集提供了一个抽象接口，用于分别添加现金和股票回购授权数据到新的算法中。基于 pandas 的这些数据集的参考实现可以在 `zipline.pipeline.loaders.buyback_auth` 中找到，而基于 blaze 的实验性实现可以在 `zipline.pipeline.loaders.blaze.buyback_auth` 中找到。([1022](https://github.com/stefan-jansen/zipline/issues/1022))。

+   增加了新的数据集 `DividendsByExDate`，`DividendsByPayDate`，和 `DividendsByAnnouncementDate`，用于流水线 API。这些数据集提供了一个抽象接口，用于按除息日、支付日和公告日分别添加股息数据到新的算法中。基于 pandas 的这些数据集的参考实现可以在 `zipline.pipeline.loaders.dividends` 中找到，而基于 blaze 的实验性实现可以在 `zipline.pipeline.loaders.blaze.dividends` 中找到。([1093](https://github.com/stefan-jansen/zipline/issues/1093))。

+   增加了新的内置因子，`zipline.pipeline.factors.BusinessDaysSinceCashBuybackAuth` 和 `zipline.pipeline.factors.BusinessDaysSinceShareBuybackAuth`。这些因子分别使用了新的 `CashBuybackAuthorizations` 和 `ShareBuybackAuthorizations` 数据集。([1022](https://github.com/stefan-jansen/zipline/issues/1022))。

+   增加了新的内置因子，`zipline.pipeline.factors.BusinessDaysSinceDividendAnnouncement`，`zipline.pipeline.factors.BusinessDaysUntilNextExDate`，和 `zipline.pipeline.factors.BusinessDaysSincePreviousExDate`。这些因子分别使用了新的 `DividendsByAnnouncementDate` 和 `DividendsByExDate` 数据集。([1093](https://github.com/stefan-jansen/zipline/issues/1093))。

+   实现了 `zipline.pipeline.Classifier`，一个新的核心流水线 API 术语，代表分组键。分类器主要用于将它们作为 `groupby` 参数传递给因子标准化方法。([1046](https://github.com/stefan-jansen/zipline/issues/1046))

+   增加了因子标准化方法：`zipline.pipeline.Factor.demean()` 和 `zipline.pipeline.Factor.zscore()`。([1046](https://github.com/stefan-jansen/zipline/issues/1046))

+   新增了`zipline.pipeline.Factor.quantiles()`方法，该方法通过将因子划分为等大小的桶来计算分类器。还增加了一些常用分位数的辅助函数（`zipline.pipeline.Factor.quartiles()`、`zipline.pipeline.Factor.quartiles()`和`zipline.pipeline.Factor.deciles()`）（[1075](https://github.com/stefan-jansen/zipline/issues/1075)）。

### 实验性功能

警告

实验性功能可能会发生变化。

无

### 错误修复

+   修复了一个错误，该错误导致在合并两个数值表达式时，如果输入过多，会导致运行管道失败，当合并超过十个因子或过滤器时。（[1072](https://github.com/stefan-jansen/zipline/issues/1072)）

### 性能

无

### 维护和重构

无

### 构建

+   增加了 AppVeyor 用于 Windows 上的持续集成。在 AppVeyor 和 Travis 构建中增加了 zipline 及其依赖的 conda 构建，并将结果上传到 anaconda.org，标记为“ci”。（[981](https://github.com/stefan-jansen/zipline/issues/981)）

### 文档

无

### 杂项

+   增加了`ZiplineTestCase`，它提供了消费测试 fixture 的钩子。fixture 是一些东西，比如：`WithAssetFinder`，它会在你的测试中使`self.asset_finder`可用，并附带一些 mock 数据（[1042](https://github.com/stefan-jansen/zipline/issues/1042)）。

## 发布 0.8.4

发布：

0.8.4

日期：

2016 年 2 月 24 日

### 亮点

+   新增了一个新的`EarningsCalendar`数据集，用于管道 API 的使用。（[905](https://github.com/stefan-jansen/zipline/issues/905)）。

+   `AssetFinder`加速（[830](https://github.com/stefan-jansen/zipline/issues/830)和[817](https://github.com/stefan-jansen/zipline/issues/817)）。

+   改进了对 Pipeline 中非浮点数据类型的支持。最值得注意的是，我们现在支持`Factor`的`datetime64`和`int64`数据类型，并且`BoundColumn.latest`现在在列的数据类型为`bool`时返回一个正确的`Filter`对象。

+   Zipline 现在支持`numpy` 1.10、`pandas` 0.17 和`scipy` 0.16（[969](https://github.com/stefan-jansen/zipline/issues/969)）。

+   批量转换已被弃用，并将在未来的版本中移除。建议使用`history`作为替代方案。

### 增强功能

+   为用户提供了一种方式，可以在执行预定函数（包括`handle_data`）时使用上下文管理器。该上下文管理器将传递给定柱的`BarData`对象，并用于所有预定运行的函数期间。这可以通过关键字参数`create_event_context`传递给`TradingAlgorithm`实现。([828](https://github.com/stefan-jansen/zipline/issues/828))

+   增加了对具有`datetime64[ns]`数据类型的`zipline.pipeline.factors.Factor`实例的支持。([905](https://github.com/stefan-jansen/zipline/issues/905))

+   在 Pipeline API 中新增了一个`EarningsCalendar`数据集。该数据集提供了一个抽象接口，用于向新算法添加收益公告数据。基于 pandas 的该数据集的参考实现可以在`zipline.pipeline.loaders.earnings`中找到，而基于 blaze 的实验性实现可以在`zipline.pipeline.loaders.blaze.earnings`中找到。([905](https://github.com/stefan-jansen/zipline/issues/905))

+   新增了两个内置因子：`zipline.pipeline.factors.BusinessDaysUntilNextEarnings`和`zipline.pipeline.factors.BusinessDaysSincePreviousEarnings`。这些因子使用了新的`EarningsCalendar`数据集。([905](https://github.com/stefan-jansen/zipline/issues/905))

+   为`zipline.pipeline.factors.Factor`添加了`isnan()`、`notnan()`和`isfinite()`方法。([861](https://github.com/stefan-jansen/zipline/issues/861))

+   新增了`zipline.pipeline.factors.Returns`，这是一个内置因子，用于计算给定`window_length`内收盘价的变化百分比。([884](https://github.com/stefan-jansen/zipline/issues/884))

+   新增了一个内置因子：`AverageDollarVolume`。([927](https://github.com/stefan-jansen/zipline/issues/927))

+   新增了`ExponentialWeightedMovingAverage`和`ExponentialWeightedMovingStdDev`因子。([910](https://github.com/stefan-jansen/zipline/issues/910))

+   允许`DataSet`类被继承，其中子类继承了父类的所有列。这些列将成为新的哨兵，因此您可以注册一个自定义加载器。([924](https://github.com/stefan-jansen/zipline/issues/924))

+   新增了`coerce()`方法，用于在将输入传递给函数之前将其从一种类型强制转换为另一种类型。([948](https://github.com/stefan-jansen/zipline/issues/948))

+   增加了`optionally()`来包装其他预处理器函数，明确允许`None`([947](https://github.com/stefan-jansen/zipline/issues/947))。

+   增加了`ensure_timezone()`，允许将字符串参数转换为`datetime.tzinfo`对象。这也允许直接传递`tzinfo`对象([947](https://github.com/stefan-jansen/zipline/issues/947))。

+   为`BlazeLoader`和`BlazeEarningsCalendarLoader`增加了两个可选参数`data_query_time`和`data_query_tz`。这些参数允许用户在从资源加载数据时指定一些截止时间。例如，如果我想模拟在`8:45 US/Eastern`执行我的`before_trading_start`函数，那么我可以将`datetime.time(8, 45)`和`'US/Eastern'`传递给加载器。这意味着在模拟中，在那天之后时间戳为`8:45`的数据将不会被看到。数据将在下一天提供([947](https://github.com/stefan-jansen/zipline/issues/947))。

+   `BoundColumn.latest`现在为`bool`类型的列返回一个`Filter`([962](https://github.com/stefan-jansen/zipline/issues/962))。

+   增加了对具有`int64`数据类型的`Factor`实例的支持。现在，当数据类型为整数时，`Column`需要一个`missing_value`([962](https://github.com/stefan-jansen/zipline/issues/962))。

+   现在还可以为`float`、`datetime`和`bool`管道项指定自定义`missing_value`值([962](https://github.com/stefan-jansen/zipline/issues/962))。

+   增加了对股票的自动关闭支持。任何在股票达到其`auto_close_date`时持有的头寸都将按照股票的最后成交价清算为现金。此外，该股票的任何未结订单都将被取消。期货和股票现在都在其`auto_close_date`的早晨自动关闭，立即在`before_trading_start`之前([982](https://github.com/stefan-jansen/zipline/issues/982))。

### 实验性功能

警告

实验性功能可能会发生变化。

+   增加了对参数化`Factor`子类的支持。因子可以指定`params`作为类级别的属性，其中包含参数名称的元组。这些值随后被构造函数接受，并通过名称转发到因子的`compute`函数。此 API 是实验性的，可能会在将来的版本中更改。

### 错误修复

+   修复了一个问题，该问题会导致每日/每分钟方法缓存改变`SIDData`对象的`len`。这会导致我们认为即使对象为空，对象也不为空([826](https://github.com/stefan-jansen/zipline/issues/826))。

+   修复了在基准数据稀疏时计算 beta 时引发的错误。相反，返回了`numpy.nan`([859](https://github.com/stefan-jansen/zipline/issues/859))。

+   修复了`sentinel()`对象的 pickling 问题（[872](https://github.com/stefan-jansen/zipline/issues/872)）。

+   修复了首次下载国债数据时出现的虚假警告（:issue 922）。

+   修正了在`initialize`函数外部使用`set_commission()`和`set_slippage()`时的错误消息。这些错误调用了`override_*`函数而不是`set_*`函数。同时也将引发的异常类型从`OverrideSlippagePostInit`和`OverrideCommissionPostInit`重命名为`SetSlippagePostInit`和`SetCommissionPostInit`（[923](https://github.com/stefan-jansen/zipline/issues/923)）。

+   修复了 CLI 中的一个问题，该问题会导致资产被添加两次，从而将同一符号映射到两个不同的 sid（[942](https://github.com/stefan-jansen/zipline/issues/942)）。

+   修复了`PerformancePeriod`在创建`Account`时错误报告总持仓价值的问题（[950](https://github.com/stefan-jansen/zipline/issues/950)）。

+   修复了在 32 位 python 上，资产与 int64 比较不正确导致的 KeyError 问题（[959](https://github.com/stefan-jansen/zipline/issues/959)）。

+   修复了`Filter`上布尔运算符未正确实现的一个 bug（[991](https://github.com/stefan-jansen/zipline/issues/991)）。

+   安装 zipline 不再无声且无条件地将 numpy 降级到 1.9.2（[969](https://github.com/stefan-jansen/zipline/issues/969)）。

### 性能

+   通过添加一个扩展`AssetFinderCachedEquities`，将股票加载到字典中，然后指导`lookup_symbol()`使用这些字典来查找匹配的股票，从而加快了`lookup_symbol()`的速度（[830](https://github.com/stefan-jansen/zipline/issues/830)）。

+   通过执行批量查询，提高了`lookup_symbol()`的性能（[817](https://github.com/stefan-jansen/zipline/issues/817)）。

### 维护和重构

+   资产数据库现在包含版本信息，以确保与当前 Zipline 版本的兼容性（[815](https://github.com/stefan-jansen/zipline/issues/815)）。

+   将`requests`版本升级到 2.9.1（[2ee40db](https://github.com/stefan-jansen/zipline/commit/2ee40db)）

+   将`logbook`版本升级到 0.12.5（[11465d9](https://github.com/stefan-jansen/zipline/commit/11465d9)）。

+   将`Cython`版本升级到 0.23.4（[5f49fa2](https://github.com/stefan-jansen/zipline/commit/5f49fa2)）。

### 构建

+   使 zipline 安装要求更加灵活（[825](https://github.com/stefan-jansen/zipline/issues/825)）。

+   使用`versioneer`来管理项目的`__version__`和 setup.py 版本（[829](https://github.com/stefan-jansen/zipline/issues/829)）。

+   修复了 travis 构建上的 coveralls 集成（[840](https://github.com/stefan-jansen/zipline/issues/840)）。

+   修复了 conda 构建，现在使用 git 源作为其源，并使用 setup.py 读取需求，而不是复制它们并让它们不同步（[937](https://github.com/stefan-jansen/zipline/issues/937)）。

+   要求`setuptools` > 18.0（[951](https://github.com/stefan-jansen/zipline/issues/951)）。

### 文档

+   为开发人员记录发布过程（[835](https://github.com/stefan-jansen/zipline/issues/835)）。

+   为 Pipeline API 添加了参考文档（[864](https://github.com/stefan-jansen/zipline/issues/864)）。

+   为资产元数据 API 添加了参考文档（[864](https://github.com/stefan-jansen/zipline/issues/864)）。

+   生成的文档现在包括许多类和函数的源代码链接（[864](https://github.com/stefan-jansen/zipline/issues/864)）。

+   添加了平台特定文档，描述如何找到二进制依赖项（[883](https://github.com/stefan-jansen/zipline/issues/883)）。

### 杂项

+   添加了一个`show_graph()`方法，用于将 Pipeline 渲染为图像（[836](https://github.com/stefan-jansen/zipline/issues/836)）。

+   添加了`subtest()`装饰器，用于创建子测试，而不使用`nose_parameterized.expand()`，这会使测试输出膨胀（[833](https://github.com/stefan-jansen/zipline/issues/833)）。

+   将测试输出中的计时报告限制为最长的 15 个测试（[838](https://github.com/stefan-jansen/zipline/issues/838)）。

+   如果从远程源返回的数据未延伸到预期日期，国库和基准下载现在将等待长达一小时再次下载（[841](https://github.com/stefan-jansen/zipline/issues/841)）。

+   添加了一个工具，用于将资产数据库降级到以前的版本（[941](https://github.com/stefan-jansen/zipline/issues/941)）。

## 发布 0.8.3

发布：

0.8.3

日期：

2015 年 11 月 6 日

注意

我们将版本推进到`0.8.3`以修复 pypi 的源分布问题。此版本没有代码更改。

## 发布 0.8.0

发布：

0.8.0

日期：

2015 年 11 月 6 日

### 亮点

+   新的文档系统，新网站位于[zipline.io](https://www.zipline.io)

+   主要性能改进。

+   动态历史。

+   新用户自定义方法：`before_trading_start`。

+   新 api 函数：`schedule_function()`。

+   新 api 函数：`get_environment()`。

+   新 api 函数：`set_max_leverage()`。

+   新 api 函数：`set_do_not_order_list()`。

+   管道 API。

+   支持交易期货。

### 增强功能

+   账户对象：向上下文添加了一个账户对象，用于跟踪有关交易账户的信息。示例：

    ```py
    context.account.settled_cash 
    ```

    返回存储在账户对象上的结算现金价值。随着算法的运行，这个值会相应更新（[396](https://github.com/stefan-jansen/zipline/issues/396)）。

+   `HistoryContainer`现在可以动态增长，对`history()`的调用现在能够增加历史容器的大小或改变其形状，以便能够服务调用。`add_history()`现在作为性能提示，预先分配容器中足够的空间。这一变化与`history`向后兼容，所有现有的算法应该继续按预期工作（[412](https://github.com/stefan-jansen/zipline/issues/412)）。

+   从 quantopian 移植过来的简单转换并使用历史。`SIDData`现在有了以下方法：

    +   `stddev`

    +   `mavg`

    +   `vwap`

    +   `returns`

    这些方法，除了`returns`，接受一个天数参数。如果你使用分钟数据运行，那么这将计算这些天中的分钟数，考虑到提前收盘和当前时间，并在这些分钟集合上应用转换。`returns`不接受任何参数，将返回给定资产的日回报率。例如：

    ```py
    data[security].stddev(3) 
    ```

    （[429](https://github.com/stefan-jansen/zipline/issues/429)）。

+   性能周期中的新字段。性能周期在`to_dict`的返回值中有了新字段：- 总杠杆 - 净杠杆 - 空头暴露 - 多头暴露 - 空头数量 - 多头数量（[464](https://github.com/stefan-jansen/zipline/issues/464)）。

+   允许`order_percent()`与各种市场价值配合使用（由 Jeremiah Lowin 提供）。

    目前，`order_percent()`和`order_target_percent()`都作为`self.portfolio.portfolio_value`的百分比操作。此 PR 允许它们作为其他重要市场价值的百分比操作。还添加了`context.get_market_value()`，这使得这种功能成为可能。例如：

    ```py
    # this is how it works today (and this still works)
    # put 50% of my portfolio in AAPL
    order_percent('AAPL', 0.5)
    # note that if this were a fully invested portfolio, it would become 150% levered.

    # take half of my available cash and buy AAPL
    order_percent('AAPL', 0.5, percent_of='cash')

    # rebalance my short position, as a percentage of my current short
    book_target_percent('MSFT', 0.1, percent_of='shorts')

    # rebalance within a custom group of stocks
    tech_stocks = ('AAPL', 'MSFT', 'GOOGL')
    tech_filter = lambda p: p.sid in tech_stocks
    for stock in tech_stocks:
        order_target_percent(stock, 1/3, percent_of_fn=tech_filter) 
    ```

    （[477](https://github.com/stefan-jansen/zipline/issues/477)）。

+   命令行选项，用于将算法打印到标准输出（由 Andrea D’Amore 提供）（[545](https://github.com/stefan-jansen/zipline/issues/545)）。

+   新增用户自定义函数`before_trading_start`。用户可以重写此函数，使其在市场开盘前每天调用一次（[389](https://github.com/stefan-jansen/zipline/issues/389)）。

+   新增 api 函数`schedule_function()`。此函数允许用户根据关于日期和时间的更复杂规则来安排函数的调用。例如，在市场收盘前 15 分钟调用该函数，尊重提前收盘（[411](https://github.com/stefan-jansen/zipline/issues/411)）。

+   新增 api 函数`set_do_not_order_list()`。该函数接受一个资产列表，并增加了一个交易保护，防止算法交易这些资产。增加了一个即时的杠杆 ETF 列表，人们可能希望将其标记为“不交易”（[478](https://github.com/stefan-jansen/zipline/issues/478)）。

+   增加了一个表示证券的类。`order()`和其他订单函数现在需要一个`Security`实例，而不是一个整数或字符串（[520](https://github.com/stefan-jansen/zipline/issues/520)）。

+   将`Security`类泛化为 `Asset`。这是为了准备增加对其他资产类型的支持（[535](https://github.com/stefan-jansen/zipline/issues/535)）。

+   新增 api 函数 `get_environment()`。该函数默认返回字符串`'zipline'`。这样算法就可以根据是在 Quantopian 还是本地 zipline 上运行而有不同的行为（[384](https://github.com/stefan-jansen/zipline/issues/384)）。

+   扩展了 `get_environment()` 以向算法暴露更多的环境。该函数现在接受一个参数，该参数是要返回的字段。默认情况下，这是`'platform'`，它返回旧的`'zipline'`值，但是可以请求以下新字段：

    +   `''arena'`：这是实时交易还是回测？

    +   `'data_frequency'`：这是分钟模式还是日模式？

    +   `'start'`：模拟开始日期。

    +   `'end'`：模拟结束日期。

    +   `'capital_base'`：模拟的起始资本。

    +   `'platform'`：算法运行的平台。

    +   `'*'`：包含所有这些字段的字典。

    （[449](https://github.com/stefan-jansen/zipline/issues/449)）。

+   新增 api 函数`set_max_leveraged()`。该方法增加了一个交易保护，防止算法过度杠杆化（[552](https://github.com/stefan-jansen/zipline/issues/552)）。

### 实验性功能

警告

实验性功能可能会发生变化。

+   增加了新的 Pipeline API。Pipeline API 是一个高级的声明性 API，用于表示大型数据集上的尾随窗口计算（[630](https://github.com/stefan-jansen/zipline/issues/630)）。

+   增加了对期货交易的支持（[637](https://github.com/stefan-jansen/zipline/issues/637)）。

+   增加了用于 blaze 表达式的 Pipeline 加载器。这允许用户从任何 blaze 理解的数据格式中提取数据，并在 Pipeline API 中使用它（[775](https://github.com/stefan-jansen/zipline/issues/775)）。

### 错误修复

+   修复了一个错误，该错误导致报告的回报率在随机时间段内急剧下降（[378](https://github.com/stefan-jansen/zipline/issues/378)）。

+   修复了一个阻止调试器解析算法文件的错误（[431](https://github.com/stefan-jansen/zipline/issues/431)）。

+   正确地将参数传递给用户定义的`initialize`函数（[687](https://github.com/stefan-jansen/zipline/issues/687)）。

+   修复了一个 bug，该 bug 会导致在午夜 EST 和财政部数据可用时间之间的每次回测都会重新下载财政部数据（[793](https://github.com/stefan-jansen/zipline/issues/793)）。

+   修复了一个 bug，该 bug 会导致如果将用户定义的`analyze`函数作为关键字参数传递给`TradingAlgorithm`，则不会被调用（[819](https://github.com/stefan-jansen/zipline/issues/819)）。

### 性能

+   对 history 进行了重大性能改进（由 Dale Jung 提供）（[488](https://github.com/stefan-jansen/zipline/issues/488)）。

### 维护和重构

+   删除简单的转换代码。这些现在作为`SIDData`的方法可用（[550](https://github.com/stefan-jansen/zipline/issues/550)）。

### 构建

无

### 文档

+   切换到 sphinx 进行文档编写（[816](https://github.com/stefan-jansen/zipline/issues/816)）。

## 发布 0.7.0

发布：

0.7.0

日期：

2014 年 7 月 25 日

### 亮点

+   直接运行算法的命令行界面。

+   IPython Magic `%%zipline`，它运行在 IPython 笔记本单元格中定义的算法。

+   用于构建防止失控订单和意外空头仓位的安全措施的 API 方法。

+   新增 history()函数，用于获取过去市场数据的移动 DataFrame（取代 BatchTransform）。

+   新增[入门教程](http://nbviewer.ipython.org/github/quantopian/zipline/blob/master/docs/tutorial.ipynb)。

### 增强功能

+   CLI：为 zipline 添加了 CLI 和 IPython 魔法。例如：

    ```py
    python run_algo.py -f dual_moving_avg.py --symbols AAPL --start 2011-1-1 --end 2012-1-1 -o dma.pickle 
    ```

    从雅虎财经获取数据，运行文件 dual_moving_avg.py（并查找`dual_moving_avg_analyze.py`，如果找到，将在算法运行后执行），并将 perf `DataFrame`输出到`dma.pickle`（[325](https://github.com/stefan-jansen/zipline/issues/325)）。

+   IPython 魔法命令（位于 IPython 笔记本单元格顶部）。例如：

    ```py
    %%zipline --symbols AAPL --start 2011-1-1 --end 2012-1-1 -o perf 
    ```

    与上述相同，只是不是执行文件，而是在单元格中查找算法，并且不是将 perf df 输出到文件，而是在命名空间中创建一个名为 perf 的变量（[325](https://github.com/stefan-jansen/zipline/issues/325)）。

+   向算法 API 添加交易控制。

    以下函数现在在`TradingAlgorithm`和算法脚本中可用：

    `set_max_order_size(self, sid=None, max_shares=None, max_notional=None)` 设置一个限制，限制算法为给定 sid 下的任何单个订单的绝对大小，无论是以股份还是总美元价值计算。如果`sid`为 None，则该规则适用于算法下的任何订单。例如：

    ```py
    def initialize(context):
        # Algorithm will raise an exception if we attempt to place an
        # order which would cause us to hold more than 10 shares
        # or 1000 dollars worth of sid(24).
        set_max_order_size(sid(24), max_shares=10, max_notional=1000.0) 
    ```

    `set_max_position_size(self, sid=None, max_shares=None, max_notional=None)` - 设置一个限制，限制算法为给定 sid 持有的任何仓位的绝对大小，无论是以股份还是美元价值计算。如果`sid`为 None，则该规则适用于算法持有的任何仓位。例如：

    ```py
    def initialize(context):
        # Algorithm will raise an exception if we attempt to order more than
        # 10 shares or 1000 dollars worth of sid(24) in a single order.
        set_max_order_size(sid(24), max_shares=10, max_notional=1000.0)

    ``set_max_order_count(self, max_count)``
    Set a limit on the number of orders that can be placed by the algorithm in
    a single trading day.
    Example: 
    ```

    ```py
    def initialize(context):
        # Algorithm will raise an exception if more than 50 orders are placed in a day.
        set_max_order_count(50) 
    ```

    `set_long_only(self)` 设置一条规则，指定算法不得持有空头仓位。例如：

    ```py
    def initialize(context):
        # Algorithm will raise an exception if it attempts to place
        # an order that would cause it to hold a short position.
        set_long_only() 
    ```

    （[329](https://github.com/stefan-jansen/zipline/issues/329)）。

+   在`TradingAlgorithm`上添加了一个`all_api_methods`类方法，该方法返回所有`TradingAlgorithm` API 方法的列表（[333](https://github.com/stefan-jansen/zipline/issues/333)）。

+   扩展了 record()功能以实现动态命名。record()函数现在可以在 kwargs 之前接受位置参数。所有原始用法和功能保持不变，但现在这些额外用法将起作用：

    ```py
    name = 'Dynamically_Generated_String'
    record( name, value, ... )
    record( name, value1, 'name2', value2, name3=value3, name4=value4 ) 
    ```

    要求仅仅是位置参数仅出现在 kwargs 之前（[355](https://github.com/stefan-jansen/zipline/issues/355)）。

+   history()已从 Quantopian 移植到 Zipline，并提供市场数据的移动窗口。history()取代了 BatchTransform。它更快，适用于分钟级数据，并且具有更优越的接口。要使用它，请在`initialize()`内部调用`add_history()`，然后在`handle_data()`内部调用 history()以接收 pandas `DataFrame`。查看[教程](http://nbviewer.ipython.org/github/quantopian/zipline/blob/master/docs/tutorial.ipynb)和[示例](https://github.com/quantopian/zipline/blob/master/zipline/examples/dual_moving_average.py)。（[345](https://github.com/stefan-jansen/zipline/issues/345)和[357](https://github.com/stefan-jansen/zipline/issues/357)）。

+   history()现在支持`1m`窗口长度（[345](https://github.com/stefan-jansen/zipline/issues/345)）。

### Bug Fixes

+   修复交易环境中的交易日和开盘收盘对齐问题（[331](https://github.com/stefan-jansen/zipline/issues/331)）。

+   修复了添加/删除新字段时的 RollingPanel 问题（[349](https://github.com/stefan-jansen/zipline/issues/349)）。

### 性能

无

### 维护和重构

+   移除了未记录且未经测试的 HDF5 和 CSV 数据源（[267](https://github.com/stefan-jansen/zipline/issues/267)）。

+   重构 sim_params（[352](https://github.com/stefan-jansen/zipline/issues/352)）。

+   重构 history（[340](https://github.com/stefan-jansen/zipline/issues/340)）。

### 构建

+   以下依赖项已更新（zipline 可能也与其他版本兼容）：

    ```py
    -pytz==2013.9
    +pytz==2014.4
    +numpy==1.8.1
    -numpy==1.8.0
    +scipy==0.12.0
    +patsy==0.2.1
    +statsmodels==0.5.0
    -six==1.5.2
    +six==1.6.1
    -Cython==0.20
    +Cython==0.20.1
    -TA-Lib==0.4.8
    +--allow-external TA-Lib --allow-unverified TA-Lib TA-Lib==0.4.8
    -requests==2.2.0
    +requests==2.3.0
    -nose==1.3.0
    +nose==1.3.3
    -xlrd==0.9.2
    +xlrd==0.9.3
    -pep8==1.4.6
    +pep8==1.5.7
    -pyflakes==0.7.3
    -pip-tools==0.3.4
    +pyflakes==0.8.1`
    -scipy==0.13.2
    -tornado==3.2
    -pyparsing==2.0.1
    -patsy==0.2.1
    -statsmodels==0.4.3
    +tornado==3.2.1
    +pyparsing==2.0.2
    -Markdown==2.3.1
    +Markdown==2.4.1 
    ```

### 贡献者

以下人员按提交次数为该版本做出了贡献：

```py
38  Scott Sanderson
29  Thomas Wiecki
26  Eddie Hebert
 6  Delaney Granizo-Mackenzie
 3  David Edwards
 3  Richard Frank
 2  Jonathan Kamens
 1  Pankaj Garg
 1  Tony Lambiris
 1  fawce 
```

## 发布 0.6.1

发布：

0.6.1

日期：

2014 年 4 月 23 日

### 亮点

+   对风险计算进行了重大修复，请参阅 Bug Fixes 部分。

+   `history()`函数的移植，请参阅增强功能部分。

+   开始支持 Quantopian 算法脚本语法，请参阅 ENH 部分。

+   conda 包管理器支持，请参阅构建部分。

### 增强功能

+   始终处理新订单，即在`handle_data`未被调用的 bar 上，但存在‘clock’数据，例如一致的基准，处理订单。

+   现在从投资组合容器中过滤掉空仓位。为了帮助防止算法在不在现有股票范围内的仓位上操作。以前，遍历仓位会返回持有零股的股票的仓位。（在算法代码中对`pos.amount != 0`进行显式检查可以防止使用不存在的仓位。）

+   添加 BMF&Bovespa 的交易日历。

+   添加算法脚本支持的开始。

+   开始与 Quantopian 的 IDE 中脚本语法保持一致的路径（[`quantopian.com`](https://quantopian.com)）示例：

    ```py
    from datetime import datetime import pytz
    from zipline import TradingAlgorithm
    from zipline.utils.factory import load_from_yahoo

    from zipline.api import order

    def initialize(context):
        context.test = 10

    def handle_date(context, data):
        order('AAPL', 10)
        print(context.test)

    if __name__ == '__main__':
        import pylab as pl
        start = datetime(2008, 1, 1, 0, 0, 0, 0, pytz.utc)
        end = datetime(2010, 1, 1, 0, 0, 0, 0, pytz.utc)
        data = load_from_yahoo(
            stocks=['AAPL'],
            indexes={},
            start=start,
            end=end)
        data = data.dropna()
        algo = TradingAlgorithm(
            initialize=initialize,
            handle_data=handle_date)
        results = algo.run(data)
        results.portfolio_value.plot()
        pl.show() 
    ```

+   添加 HDF5 和 CSV 数据源。

+   限制`handle_data`在有市场数据的时间调用。为了避免自定义数据类型时间戳不一致的情况，只在市场数据通过时调用`handle_data`。在市场数据之前到达的自定义数据仍会更新数据条，但只有在有可操作的市场数据时才会处理这些数据。

+   扩展每股的佣金方法以允许每笔交易有最低成本。

+   添加符号 API 函数。`symbol()`查找功能已添加到 Quantopian。通过在 zipline 中添加相同的 API 函数，我们可以使将 Zipline 算法复制粘贴到 Quantopian 变得更加容易。

+   添加模拟随机交易源。新增了一个数据源，以用户指定的特定频率（分钟或每日）发出事件。这允许用户在分钟模式下进行回测和调试算法，为通往 Quantopian 提供更清晰的途径。

+   去除对交易日的基准依赖。不再使用基准的索引，现在使用交易日历来填充环境中的交易日。移除`extra_date`字段，因为与基准列表不同，交易日历可以生成未来日期，因此不需要为当前交易日的日期添加。动机：

    +   开盘和收盘/提前收盘日历以及交易日历的来源现在是相同的，这应该有助于防止由于错位而可能出现的问题。

    +   允许配置，其中基准作为基于生成器的数据源提供，无需提供第二个基准列表只是为了填充日期。

+   从 Quantopian 移植`history()` API 方法。打开了之前仅在 Quantopian 平台上可用的`history()`函数的内核。

    历史方法类似于`batch_transform`函数/装饰器，但希望对捕获的前一个条形数据的频率和周期有更精确的规范。示例用法：

    ```py
    from zipline.api import history, add_history

    def initialize(context):
        add_history(bar_count=2, frequency='1d', field='price')

    def handle_data(context, data):
        prices = history(bar_count=2, frequency='1d', field='price')
        context.last_prices = prices 
    ```

    注意：此版本的历史记录缺少允许在第一个条上返回完整 DataFrame 的回填功能。

### 错误修复

+   调整基准事件以匹配市场交易时间（[241](https://github.com/stefan-jansen/zipline/issues/241)）。之前基准事件在相关基准日的 0:00 发出：在“分钟”发射模式下，这意味着基准在任何日内交易处理之前就已经发出。

+   确保为所有交易日生成性能统计数据。当使用每分钟数据时，模拟器会向用户报告它模拟了‘n - 1’天（其中 n 是在模拟参数中指定的天数）。现在报告正确数量的交易日被模拟。

+   修复累积风险指标的 repr。RiskMetricsCumulative 的`__repr__`引用了该类的旧结构，导致打印时出现异常。现在还打印了指标 DataFrame 中的最后值。

+   防止分钟级数据在可用数据结束时崩溃。分钟级数据算法在计算次日数据时，当到达可用数据末尾时会引发错误。现在，当到达可用数据末尾时，不再抛出通用异常，而是抛出一个命名异常并捕获它，以便交易模拟循环可以跳过，因为不需要计算下一个市场收盘。

+   在交易日历中修复 pandas 索引。这也可以归类为性能问题。使用 loc 索引而不是效率低下的 day 索引，然后是 time 索引。

+   防止由于不存在成员导致的 vwap 转换崩溃。WrongDataForTransform 引用了不存在的`self.fields`成员。添加一个 self.fields 成员，设置为`price`和`volume`，并在检查期间使用它进行迭代。

+   修复最大回撤计算。输入到最大回撤的数据不正确，导致结果不佳。即`compounded_log_returns`不是代表算法在给定时间的总回报的值，尽管`calculate_max_drawdown`将其视为如此。现在使用`algorithm_period_returns`序列，它确实提供了总回报。

+   修复成本基础计算。成本基础计算现在考虑了交易的方向。平仓多头或回补空头不应影响成本基础。

+   修复`order()`中的浮点错误。当订单金额接近整数时，可能会意外地被舍入或向上取整（取决于正负）到错误的整数。例如，内部存储为-27.99999 的金额被转换为-27 而不是-28。

+   当仓位因拆分而变动时，更新性能周期状态。否则，`self._position_amounts`将与 position.amount 等不同步。

+   修复使用确切日期时下行系列计算的错位。在使传递给风险模块的回报系列更精确时暴露出的一个奇怪问题，回报与平均回报之间的系列比较不平衡，因为平均回报没有被屏蔽到下行数据点；然而，在大多数情况下，如果不是所有情况下，这都是通过调用`.valid()`来掩盖的，这在此次更改集中被移除了。

+   在使用 self.logger 之前检查它是否存在。`self.logger`被初始化为`None`，不能保证用户已经设置它，所以在尝试向它传递消息之前检查它是否存在。

+   防止性能跟踪器中的不同步市场收盘。在性能跟踪器重置或修补以处理与预热实时数据的状态切换的情况下，性能跟踪器的`market_close`成员可能会与性能跟踪器确定的当前算法时间不同步。症状是股息从未触发，因为收盘检查与当前时间不匹配。通过让交易模拟循环负责在每分钟模式下推进市场收盘，并将该值传递给性能跟踪器来修复，而不是让性能跟踪器也推进市场收盘。

+   修正多个累积和周期风险计算。预期会改变的计算包括：

    +   `cumulative.beta`

    +   `cumulative.alpha`

    +   `cumulative.information`

    +   `cumulative.sharpe`

    +   `period.sortino`

    风险计算如何改变周期性和累积性的风险修正

    下行风险

    使用样本而非总体的标准差。

    添加一个舍入因子，以便在给定 dt 的情况下，如果两个值接近，则它们不计为下行值，这会影响下行差分标准差的分母。

    标准差类型

    全面来看，标准差已经标准化为使用“样本”计算，而以前累积风险主要使用“总体”。使用`ddof=1`与`np.std`计算时，将值视为样本。

    累积风险修正

    贝塔

    使用每日算法回报和基准而非年化平均回报。

    波动性

    使用样本而非总体的标准差。

    波动性是其他计算的输入，因此这一变化影响夏普和信息比率计算。

    信息比率

    基准回报输入从年化基准回报更改为年化平均回报。

    阿尔法

    基准回报输入从年化基准回报更改为年化平均回报。

    周期风险修正

    Sortino

    现在使用每日回报的下行风险与平均算法回报的最低可接受回报，而不是国债回报。

    上述更改需要添加周期风险的平均算法回报计算。

    此外，使用`algorithm_period_returns`和`tresaury_period_return`作为累积 Sortino 所做的，而不是使用算法回报作为 Sortino 计算的两个输入。

### 性能

+   移除了`alias_dt`转换，转而在 SIDData 上添加属性。通过`alias_dt`生成器添加事件的 dt 字段的副本作为 datetime，以便 API 宽容并允许 SIDData 对象上同时存在 datetime 和 dt，这会在 noop 算法上产生明显的开销。不再为每个通过系统传递的事件复制 datetime 值并将其分配给事件对象，而是在 SIDData 上添加一个属性，该属性作为`dt`的别名`datetime`。最终可能会移除对`data['foo'].datetime`的支持，并可能被视为已弃用。

+   移除了从累积回报中删除`null return`的操作。在每个分钟排放的算法运行时，检查 null return 键的存在并在每个单独的条上删除该返回，会增加不必要的 CPU 时间。相反，在开始日期之前的交易日索引处添加 0.0 返回。移除`null return`主要是为了防止周期计算在非日期索引值上崩溃；由于索引为日期，周期回报也可以近似波动性（尽管该波动性具有高噪声信号强度，因为它仅使用两个值作为输入。）

### 维护和重构

+   允许`sim_params`为算法提供数据频率。如果算法的`data_frequency`为 None，则允许`sim_params`提供`data_frequency`。

    此外，如果提供的话，请遵循算法数据频率。

### 构建

+   添加了对通过 conda 构建和发布的支持。对于那些更喜欢使用[`docs.conda.io/en/latest/`](https://docs.conda.io/en/latest/)构建而不是使用 pip 本地编译的人来说。以下应该可以在许多系统上安装 Zipline。

    ```py
    conda install -c quantopian zipline 
    ```

### 贡献者

以下人员为此次发布做出了贡献，按提交次数排序：

```py
49  Eddie Hebert
28  Thomas Wiecki
11  Richard Frank
 2  Jamie Kirkpatrick
 2  Jeremiah Lowin
 1  Colin Alexander
 1  Michael Schatzow
 1  Moises Trovo
 1  Suminda Dharmasena 
```

## 发布 2.0.0rc

发布：

2.0.0rc1

日期：

2021 年 4 月 5 日

### 亮点

此次发布更新了 Zipline，使其与 Python >= 3.7 以及当前版本的 Pandas、scikit-learn 等相关的 PyData 库兼容。

[Conda 包](https://anaconda.org/ml4t/repo)现在为[Zipline](https://anaconda.org/ml4t/zipline-reloaded)及其关键依赖[bcolz](https://anaconda.org/ml4t/bcolz-zipline)和[TA-Lib](https://anaconda.org/ml4t/ta-lib)提供 Python 3.7-3.9 的版本，可在‘ml4t’ Anaconda 频道上获取。Linux（Python 3.7-3.9）和 MacOSx（3.7 和 3.8）的二进制轮子可在[PyPi](https://pypi.org/project/zipline-reloaded/)上获取。

作为更新的一部分，移除了`BlazeLoader`功能。它是基于[Blaze Ecosystem](https://blaze.pydata.org/)构建的。不幸的是，三个相关项目（[Blaze](https://blaze.readthedocs.io/en/latest/index.html)、[Odo](https://odo.readthedocs.io/en/latest/)和[datashape](https://datashape.readthedocs.io/en/latest/)）在过去几年中得到了非常有限的支持。

其他更新包括：

+   [新版本](https://github.com/stefan-jansen/bcolz-zipline)为[Bcolz](https://github.com/Blosc/bcolz)，自 2020 年 9 月起被[作者](https://github.com/Blosc)标记为不再维护。新版本将底层[c-blosc](https://github.com/Blosc/c-blosc)库从 1.14 版更新到最新的 1.21.0 版。还有 Bcolz 的 Conda 包（见上述链接）。

+   [Networkx](https://networkx.org/) 现已采用性能更佳的 2.0 版本。

+   Conda 包为 TA-Lib 0.4.19。

这一新版本还使得在回测时更容易将自定义数据源（如 ML 模型的预测）加载到流水线中。请参阅书籍[《机器学习在交易中的应用》](https://www.amazon.com/Machine-Learning-Algorithmic-Trading-alternative/dp/1839217715/ref=tmm_pap_swatch_0?_encoding=UTF8&qid=1617658040&sr=8-1-spons)的[Github 仓库](https://github.com/stefan-jansen/machine-learning-for-trading)中的相关示例，例如[这些](https://github.com/stefan-jansen/machine-learning-for-trading/tree/master/08_ml4t_workflow/04_ml4t_workflow_with_zipline)。

### 增强功能

+   自定义加载器（custom_loader()）用于自定义流水线数据

+   与最新版本的 Pandas、scikit-learn 和其他相关[PyData](https://pydata.org/)库的兼容性。

### 错误修复

+   为适应最近的 Python 版本和依赖项版本，进行了多项测试更新。

### 性能

+   最新的 blosc 库可能提高压缩和 I/O 性能

### 维护和重构

+   移除了 Python 2 支持

### 构建

+   所有构建已整合到 GitHub Actions CI

### 文档

+   扩展了关于流水线和相关数据加载器的额外信息

### 亮点

此版本更新了 Zipline，使其与 Python >= 3.7 以及当前版本的 Pandas、scikit-learn 等相关的 PyData 库兼容。

[Conda 包](https://anaconda.org/ml4t/repo)为[Zipline](https://anaconda.org/ml4t/zipline-reloaded)及其关键依赖项[bcolz](https://anaconda.org/ml4t/bcolz-zipline)和[TA-Lib](https://anaconda.org/ml4t/ta-lib)，现已在‘ml4t’ Anaconda 频道上为 Python 3.7-3.9 提供。在[PyPi](https://pypi.org/project/zipline-reloaded/)上提供 Linux（Python 3.7-3.9）和 MacOSx（3.7 和 3.8）的二进制轮。

作为更新的一部分，移除了`BlazeLoader`功能。它原本是基于[Blaze 生态系统](https://blaze.pydata.org/)构建的。不幸的是，相关的三个项目（[Blaze](https://blaze.readthedocs.io/en/latest/index.html)、[Odo](https://odo.readthedocs.io/en/latest/)和[datashape](https://datashape.readthedocs.io/en/latest/)）在过去几年中得到的维护非常有限。

其他更新包括：

+   [新版本](https://github.com/stefan-jansen/bcolz-zipline) 的 [Bcolz](https://github.com/Blosc/bcolz) 自 2020 年 9 月以来已被标记为不再维护，由 [作者](https://github.com/Blosc) 发布。新版本将底层 [c-blosc](https://github.com/Blosc/c-blosc) 库从 1.14 版更新到最新的 1.21.0 版。还有 Bcolz 的 conda 包（见上述链接）。

+   [Networkx](https://networkx.org/) 现在使用性能更好的 2.0 版本。

+   TA-Lib 0.4.19 的 conda 包。

这个新版本还使得在回测时更容易将自定义数据源加载到 Pipeline 中（例如 ML 模型的预测）。请参阅 [Github 仓库](https://github.com/stefan-jansen/machine-learning-for-trading) 中的 [Machine Learning for Trading](https://www.amazon.com/Machine-Learning-Algorithmic-Trading-alternative/dp/1839217715/ref=tmm_pap_swatch_0?_encoding=UTF8&qid=1617658040&sr=8-1-spons) 一书的示例，例如 [这些](https://github.com/stefan-jansen/machine-learning-for-trading/tree/master/08_ml4t_workflow/04_ml4t_workflow_with_zipline)。

### 增强功能

+   自定义 Pipeline 数据的 custom_loader()

+   与最新版本的 Pandas、scikit-learn 和其他相关的 [PyData](https://pydata.org/) 库的兼容性。

### 错误修复

+   为了适应最近的 Python 和依赖版本，进行了大量的测试更新。

### 性能

+   最新的 blosc 库可能会提高压缩和 I/O 性能

### 维护和重构

+   移除了对 Python 2 的支持

### 构建

+   所有构建都整合到了 GitHub Actions CI 上

### 文档

+   扩展了有关 Pipeline 和相关 DataLoaders 的更多信息

## 发布 1.4.1

发布：

1.4.1

日期：

2020 年 10 月 5 日

此版本包括少量的错误修复、文档改进和构建/依赖项增强。

zipline 及其依赖项的 conda 包现在可用于 python 3.6 的 ‘conda-forge’ Anaconda 频道。它们也可以在 ‘Quantopian’ 频道上获得，但我们最终将停止更新那些。

### 错误修复

+   修复了在没有 `benchmark_returns` 的情况下调用 `run_algorithm` 的问题 ([2762](https://github.com/stefan-jansen/zipline/issues/2762))

### 维护和重构

+   支持 empyrical 0.5.3 ([2526](https://github.com/stefan-jansen/zipline/issues/2526))

+   在 py3 环境中移除了对 contextlib2 的依赖 ([2757](https://github.com/stefan-jansen/zipline/issues/2757))

+   将默认包更新为 ‘quantopian-quandl’ 并在更多入口点使用 ([2763](https://github.com/stefan-jansen/zipline/issues/2763))

### 构建

+   使用更新的 statsmodels 和 scipy 进行 CI ([2739](https://github.com/stefan-jansen/zipline/issues/2739))

+   GitHub Actions CI 在 linux 和 macos 上的问题 ([2743](https://github.com/stefan-jansen/zipline/issues/2743), [2767](https://github.com/stefan-jansen/zipline/issues/2767))

+   为 zipline 及其依赖项添加了 conda 打包到 conda-forge ([2665](https://github.com/stefan-jansen/zipline/issues/2665))

### 文档

+   各种文档改进（[2763](https://github.com/stefan-jansen/zipline/issues/2763)，[2771](https://github.com/stefan-jansen/zipline/issues/2771)，[2772](https://github.com/stefan-jansen/zipline/issues/2772)，[2776](https://github.com/stefan-jansen/zipline/issues/2776)，[2780](https://github.com/stefan-jansen/zipline/issues/2780)）

### 错误修复

+   修复了在没有`benchmark_returns`的情况下调用`run_algorithm`的问题（[2762](https://github.com/stefan-jansen/zipline/issues/2762)）

### 维护和重构

+   支持 empyrical 0.5.3（[2526](https://github.com/stefan-jansen/zipline/issues/2526)）

+   在 py3 环境中移除了对 contextlib2 的依赖（[2757](https://github.com/stefan-jansen/zipline/issues/2757)）

+   在更多入口点更新默认 bundle 为‘quantopian-quandl’（[2763](https://github.com/stefan-jansen/zipline/issues/2763)）

### 构建

+   使用更新的 statsmodels 和 scipy 进行 CI（[2739](https://github.com/stefan-jansen/zipline/issues/2739)）

+   GitHub Actions CI 在 linux 和 macos 上（[2743](https://github.com/stefan-jansen/zipline/issues/2743)，[2767](https://github.com/stefan-jansen/zipline/issues/2767)）

+   为 zipline 及其依赖项添加了 conda 打包到 conda-forge（[2665](https://github.com/stefan-jansen/zipline/issues/2665)）

### 文档

+   各种文档改进（[2763](https://github.com/stefan-jansen/zipline/issues/2763)，[2771](https://github.com/stefan-jansen/zipline/issues/2771)，[2772](https://github.com/stefan-jansen/zipline/issues/2772)，[2776](https://github.com/stefan-jansen/zipline/issues/2776)，[2780](https://github.com/stefan-jansen/zipline/issues/2780)）

## 发布 1.4.0

发布：

1.4.0

日期：

2020 年 7 月 22 日

### 亮点

#### 移除了对基准和债券收益率的隐式依赖

以前，如果用户没有提供，Zipline 会隐式地从第三方 API 源获取这些必需的输入：来自美国联邦储备银行 API 的债券数据和来自 IEX 的基准。这意味着模拟需要互联网连接和这些数据源的稳定 API，这对许多用户来说都不是保证的。

我们移除了对债券曲线的依赖，因为它们实际上不再被使用。我们用明确的选项替换了基准收益率的隐式下载：

```py
--benchmark-file                The csv file that contains the benchmark
                                returns (date, returns columns)
--benchmark-symbol              The instrument's symbol to be used as
                                a benchmark.
                                (should exist in the ingested bundle)
--benchmark-sid                 The sid of the instrument to be used as a
                                benchmark.
                                (should exist in the ingested bundle)
--no-benchmark                  This flag is used to set the benchmark to
                                zero. Alpha, beta and benchmark metrics
                                are not calculated 
```

（[2627](https://github.com/stefan-jansen/zipline/issues/2627)，[2642](https://github.com/stefan-jansen/zipline/issues/2642)）

#### 新内置因子

+   `PercentChange`：计算给定`window_length`下的百分比变化。注意：百分比变化计算公式为`(new - old) / abs(old)`。（[2506](https://github.com/stefan-jansen/zipline/issues/2506)）

+   `PeerCount`：给出分类器中每个不同类别的出现次数。（[2509](https://github.com/stefan-jansen/zipline/issues/2509)）

+   `ConstantMixin`：用于创建具有常量值的流水线项的混合类。([2697](https://github.com/stefan-jansen/zipline/issues/2697))

+   `if_else()`：允许用户创建条件表达式，根据条件从两个项的输出中选择一个。([2697](https://github.com/stefan-jansen/zipline/issues/2697))

+   `fillna()`：允许用户用常量值或其他项的值填充缺失数据。([2697](https://github.com/stefan-jansen/zipline/issues/2697))

+   `clip()`：允许用户将因子的值限制在给定范围内。([2708](https://github.com/stefan-jansen/zipline/issues/2708))

+   `mean()`、`stddev()`、`max()`、`min()`、`median()`、`sum()`、`notnull_count()`：将整个域的数据汇总为标量因子。([2697](https://github.com/stefan-jansen/zipline/issues/2697))

### 增强功能

+   新增了国际流水线。([2262](https://github.com/stefan-jansen/zipline/issues/2262))

+   新增了 DataSetFamily（原名 MultiDimensionalDataSet），用于创建共享相同列的常规 DataSet 集合的简写方式。([2402](https://github.com/stefan-jansen/zipline/issues/2402))

+   新增了`get_column()`方法，用于按名称查找列。([2210](https://github.com/stefan-jansen/zipline/issues/2210))

+   新增了`CheckWindowsClassifier`，允许我们使用流水线测试分类和字符串列的回溯窗口。([2458](https://github.com/stefan-jansen/zipline/issues/2458))

+   新增了`PipelineHooks`，现在用于显示流水线进度条。([2467](https://github.com/stefan-jansen/zipline/issues/2467))

+   `BoundColumn`比较现在会导致错误。这防止了编写`EquityPricing.volume > 1000`（静默返回错误数据）而不是`EquityPricing.volume.latest > 1000`。([2537](https://github.com/stefan-jansen/zipline/issues/2537))

+   新增了对流水线的货币转换支持。([2586](https://github.com/stefan-jansen/zipline/issues/2586))

+   新增了`--benchmark-file`和`--benchmark-symbol`命令行参数，以便更容易提供基准数据。([2642](https://github.com/stefan-jansen/zipline/issues/2642))

+   新增了对 Python 3.6 的支持。([2643](https://github.com/stefan-jansen/zipline/issues/2643))

+   为 Factor.peer_count 新增了`mask`参数。([2676](https://github.com/stefan-jansen/zipline/issues/2676))

+   新增了`if_else()`和`fillna()`，允许在流水线中使用条件逻辑。([2691](https://github.com/stefan-jansen/zipline/issues/2691))

+   为 Factor 新增了每日汇总方法，用于为整个宇宙收集汇总统计信息。([2697](https://github.com/stefan-jansen/zipline/issues/2697))

+   新增了`clip()`方法，用于将值限制在一定范围内。([2708](https://github.com/stefan-jansen/zipline/issues/2708))

+   新增了对超过 32 个项的流水线项算术运算的支持。([2727](https://github.com/stefan-jansen/zipline/issues/2727))

### 错误修复

+   修复了对非唯一 sid->交易所映射的支持。([2289](https://github.com/stefan-jansen/zipline/issues/2289))

+   修复了在股息警告时的崩溃问题。([2323](https://github.com/stefan-jansen/zipline/issues/2323))

+   修复了当周一在新年前时`week_start`的问题。([2394](https://github.com/stefan-jansen/zipline/issues/2394))

+   确保在解包空数据帧时 dtype 正确。([2444](https://github.com/stefan-jansen/zipline/issues/2444))

+   修复了一个 bug，其中具有`window_length=0`的流水线项在调用`compute()`之前不会复制输入，这可能导致如果输入在流水线中被重用，则结果不正确。([2723](https://github.com/stefan-jansen/zipline/issues/2723))

### 性能

+   添加了`HDF5DailyBarWriter`，它以新的 HDF5 文件格式写入每日定价。每个 OHLCV 字段都存储为分块 HDF5 数据集中的 2D 数组，每行代表一个 sid，每列代表一天。该文件还支持多个国家。添加了`HDF5DailyBarReader`，它实现了 BarReader 接口，并可以读取由 HDF5DailyBarWriter 编写的文件。([2295](https://github.com/stefan-jansen/zipline/issues/2295))

+   矢量化股息比率计算([2298](https://github.com/stefan-jansen/zipline/issues/2298))

+   改进了`RollingPearson`和`RollingPearsonOfReturns`流水线因子的性能。([2071](https://github.com/stefan-jansen/zipline/issues/2071))

### 维护和重构

+   使`parameter_space()`在运行之间重置实例固定装置([2433](https://github.com/stefan-jansen/zipline/issues/2433))

+   移除了未使用的国债曲线数据处理。([2626](https://github.com/stefan-jansen/zipline/issues/2626))

### 杂项

#### 国际流水线

流水线现在支持国际数据。

流水线是一种工具，允许您在资产集合和时间段上定义计算。过去，您只能在美股市场上运行流水线。现在，您可以指定流水线应该计算的域。“域”一词指的是数学概念中的“函数的域”，即函数的可能输入集合。在流水线的上下文中，域指定了流水线表达式应该计算的资产集合和相应的交易日历。

例如，以下流水线返回所有加拿大股票的最新收盘价和成交量，每天。

```py
pipe = Pipeline(
    columns={
        'price': EquityPricing.close.latest,
        'volume': EquityPricing.volume.latest,
        'mcap': factset.Fundamentals.mkt_val.latest,
    },
    domain=CA_EQUITIES,
) 
```

与货币相关的另一个挑战是，一些交易所不要求股票以当地货币列出。例如，伦敦证券交易所只有大约 75%的上市股票以英镑计价。其余 25%主要以欧元或美元计价。这可能使得进行横截面比较变得困难。

为了解决这个问题，大多数人依赖货币转换将基于价格的字段转换为同一货币。流水线列现在支持一个`fx`方法，用于指定数据应该以哪种货币查看。此方法仅适用于“货币感知”的术语，例如开盘或收盘，但不适用于不关心货币的术语，如成交量。

目前，没有办法将国际数据加载到包中。我们正在寻找方法，使将国际数据导入 Zipline 变得容易。

([2265](https://github.com/stefan-jansen/zipline/issues/2265), [2262](https://github.com/stefan-jansen/zipline/issues/2262), 以及许多其他)

Zipline 目前支持运行流水线的域（使用最新的 [trading-calendars](https://pypi.org/project/trading-calendars/) 包）如下：

+   阿根廷

+   澳大利亚

+   奥地利

+   比利时

+   巴西

+   加拿大

+   智利

+   中国

+   捷克共和国

+   哥伦比亚

+   捷克

+   芬兰

+   法国

+   德国

+   希腊

+   香港

+   匈牙利

+   印度

+   印度尼西亚

+   爱尔兰

+   意大利

+   日本

+   马来西亚

+   墨西哥

+   荷兰

+   新西兰

+   挪威

+   巴基斯坦

+   秘鲁

+   菲律宾

+   波兰

+   葡萄牙

+   俄罗斯

+   新加坡

+   西班牙

+   瑞典

+   台湾

+   泰国

+   土耳其

+   英国

+   美国

+   南非

+   韩国

+   瑞士

([2301](https://github.com/stefan-jansen/zipline/issues/2301), [2333](https://github.com/stefan-jansen/zipline/issues/2333), [2338](https://github.com/stefan-jansen/zipline/issues/2338), [2355](https://github.com/stefan-jansen/zipline/issues/2355), [2369](https://github.com/stefan-jansen/zipline/issues/2369), [2550](https://github.com/stefan-jansen/zipline/issues/2550), [2552](https://github.com/stefan-jansen/zipline/issues/2552), [2559](https://github.com/stefan-jansen/zipline/issues/2559))

#### 数据集家族

> 数据集家族用于表示行唯一标识符需要不仅仅是资产和日期坐标的数据。一个 `DataSetFamily` 也可以被认为是一组 `DataSet` 对象的集合，每个对象都有相同的列、域和 ndim。
> 
> `DataSetFamily` 对象是通过一个或多个 `Column` 对象以及一个额外的字段：`extra_dims` 来定义的。
> 
> `extra_dims` 字段定义了除资产和日期之外的坐标，这些坐标必须固定以产生逻辑时间序列。列对象确定将由家族切片共享的列。
> 
> `extra_dims` 被表示为一个有序字典，其中键是维度名称，值是该维度上唯一值的集合。
> 
> 要在管道表达式中使用一个`DataSetFamily`，必须使用`slice()`方法为每个额外的维度选择一个特定的值。例如，给定一个`DataSetFamily`：
> 
> ```py
> class SomeDataSet(DataSetFamily):
>     extra_dims = [
>         ('dimension_0', {'a', 'b', 'c'}),
>         ('dimension_1', {'d', 'e', 'f'}),
>     ]
> 
>     column_0 = Column(float)
>     column_1 = Column(bool) 
> ```
> 
> 这个数据集可能代表一个具有以下列的表：
> 
> ```py
> sid :: int64
> asof_date :: datetime64[ns]
> timestamp :: datetime64[ns]
> dimension_0 :: str
> dimension_1 :: str
> column_0 :: float64
> column_1 :: bool 
> ```
> 
> 这里我们可以看到隐式的`sid`、`asof_date`和`timestamp`列以及额外的维度列。
> 
> 这个`DataSetFamily`可以转换为常规的`DataSet`：
> 
> ```py
> DataSetSlice = SomeDataSet.slice(dimension_0='a', dimension_1='e') 
> ```
> 
> 这个切片数据集代表了在高维数据集中满足`(dimension_0 == 'a') & (dimension_1 == 'e')`条件的行。

([2402](https://github.com/stefan-jansen/zipline/issues/2402), [2452](https://github.com/stefan-jansen/zipline/issues/2452), [2456](https://github.com/stefan-jansen/zipline/issues/2456))

### 亮点

#### 移除了对基准和债券回报的隐式依赖

以前，Zipline 会隐式地从第三方 API 源获取这些必需的输入，如果用户没有提供的话：从美国联邦储备银行的 API 获取国债数据，从 IEX 获取基准数据。这意味着模拟需要互联网连接和这些数据源的稳定 API，而这两者对许多用户来说都无法保证。

我们移除了对国债曲线的依赖，因为它们实际上已经不再被使用。并且我们用明确的选项替换了隐式下载基准回报的做法：

```py
--benchmark-file                The csv file that contains the benchmark
                                returns (date, returns columns)
--benchmark-symbol              The instrument's symbol to be used as
                                a benchmark.
                                (should exist in the ingested bundle)
--benchmark-sid                 The sid of the instrument to be used as a
                                benchmark.
                                (should exist in the ingested bundle)
--no-benchmark                  This flag is used to set the benchmark to
                                zero. Alpha, beta and benchmark metrics
                                are not calculated 
```

([2627](https://github.com/stefan-jansen/zipline/issues/2627), [2642](https://github.com/stefan-jansen/zipline/issues/2642))

#### 新的内置因子

+   `PercentChange`: 计算给定`window_length`下的百分比变化。注意：百分比变化计算为`(new - old) / abs(old)`。([2506](https://github.com/stefan-jansen/zipline/issues/2506))

+   `PeerCount`: 给出分类器中每个不同类别的出现次数。([2509](https://github.com/stefan-jansen/zipline/issues/2509))

+   `ConstantMixin`: 用于创建具有常量值的管道项的混合类。([2697](https://github.com/stefan-jansen/zipline/issues/2697))

+   `if_else()`: 允许用户创建根据条件从两个表达式之一中选择输出的表达式。([2697](https://github.com/stefan-jansen/zipline/issues/2697))

+   `fillna()`：允许用户用常量值或其他项的值填充缺失数据。([2697](https://github.com/stefan-jansen/zipline/issues/2697))

+   `clip()`：允许用户将因子值限制在给定范围内。([2708](https://github.com/stefan-jansen/zipline/issues/2708))

+   `mean()`，`stddev()`，`max()`，`min()`，`median()`，`sum()`，`notnull_count()`：将整个域的数据汇总为标量因子。([2697](https://github.com/stefan-jansen/zipline/issues/2697))

#### 移除了对基准和国债回报的隐式依赖

以前，Zipline 会隐式地从第三方 API 源获取这些必需的输入，如果用户没有提供：来自美国联邦储备银行 API 的国债数据和来自 IEX 的基准数据。这意味着模拟需要互联网连接和这些数据源的稳定 API，这对于许多用户来说都不是保证的。

我们移除了对国债曲线的依赖，因为它们实际上已经不再被使用。并且我们用明确的选项替换了隐式下载基准回报的做法：

```py
--benchmark-file                The csv file that contains the benchmark
                                returns (date, returns columns)
--benchmark-symbol              The instrument's symbol to be used as
                                a benchmark.
                                (should exist in the ingested bundle)
--benchmark-sid                 The sid of the instrument to be used as a
                                benchmark.
                                (should exist in the ingested bundle)
--no-benchmark                  This flag is used to set the benchmark to
                                zero. Alpha, beta and benchmark metrics
                                are not calculated 
```

([2627](https://github.com/stefan-jansen/zipline/issues/2627), [2642](https://github.com/stefan-jansen/zipline/issues/2642))

#### 新的内置因子

+   `PercentChange`：计算给定`window_length`期间的百分比变化。注意：百分比变化计算为`(new - old) / abs(old)`。([2506](https://github.com/stefan-jansen/zipline/issues/2506))

+   `PeerCount`：给出分类器中每个不同类别的出现次数。([2509](https://github.com/stefan-jansen/zipline/issues/2509))

+   `ConstantMixin`：用于创建具有常量值的管道项的混合类。([2697](https://github.com/stefan-jansen/zipline/issues/2697))

+   `if_else()`：允许用户创建条件表达式，根据条件从两个项的输出中选择一个。([2697](https://github.com/stefan-jansen/zipline/issues/2697))

+   `fillna()`：允许用户用常量值或其他项的值填充缺失数据。([2697](https://github.com/stefan-jansen/zipline/issues/2697))

+   `clip()`：允许用户将因子值限制在给定范围内。([2708](https://github.com/stefan-jansen/zipline/issues/2708))

+   `mean()`，`stddev()`，`max()`，`min()`，`median()`，`sum()`，`notnull_count()`：将整个域的数据汇总为标量因子。([2697](https://github.com/stefan-jansen/zipline/issues/2697))

### 增强功能

+   添加了国际管道([2262](https://github.com/stefan-jansen/zipline/issues/2262))

+   添加了 DataSetFamily（前称 MultiDimensionalDataSet）- 一种创建共享相同列的常规数据集集合的简写方式。([2402](https://github.com/stefan-jansen/zipline/issues/2402))

+   新增了`get_column()`方法，用于通过名称查找列（[2210](https://github.com/stefan-jansen/zipline/issues/2210)）

+   添加了`CheckWindowsClassifier`，允许我们使用 Pipeline 测试分类和字符串列的回溯窗口。（[2458](https://github.com/stefan-jansen/zipline/issues/2458)）

+   添加了`PipelineHooks`，现在用于显示 Pipeline 进度条。（[2467](https://github.com/stefan-jansen/zipline/issues/2467)）

+   `BoundColumn`比较现在会导致错误。这防止了编写`EquityPricing.volume > 1000`（静默返回错误数据）而不是`EquityPricing.volume.latest > 1000`。（[2537](https://github.com/stefan-jansen/zipline/issues/2537)）

+   为 Pipeline 添加了货币转换支持。（[2586](https://github.com/stefan-jansen/zipline/issues/2586)）

+   添加了`--benchmark-file`和`--benchmark-symbol`命令行参数，以便更容易提供基准数据。（[2642](https://github.com/stefan-jansen/zipline/issues/2642)）

+   添加了对 Python 3.6 的支持。（[2643](https://github.com/stefan-jansen/zipline/issues/2643)）

+   为 Factor.peer_count 添加了`mask`参数。（[2676](https://github.com/stefan-jansen/zipline/issues/2676)）

+   添加了`if_else()`和`fillna()`，允许在 Pipelines 中使用条件逻辑。（[2691](https://github.com/stefan-jansen/zipline/issues/2691)）

+   为 Factor 添加了每日汇总方法，用于为整个宇宙收集汇总统计数据。（[2697](https://github.com/stefan-jansen/zipline/issues/2697)）

+   添加了`clip()`方法，用于将值裁剪到一定范围内。（[2708](https://github.com/stefan-jansen/zipline/issues/2708)）

+   添加了对 Pipeline 项算术运算超过 32 项的支持。（[2727](https://github.com/stefan-jansen/zipline/issues/2727)）

### 错误修复

+   修复了对非唯一 sid->exchange 映射的支持。（[2289](https://github.com/stefan-jansen/zipline/issues/2289)）

+   修复了股息警告导致的崩溃。（[2323](https://github.com/stefan-jansen/zipline/issues/2323)）

+   修复了周一在新年前的情况下的`week_start`。（[2394](https://github.com/stefan-jansen/zipline/issues/2394)）

+   确保在解包空数据帧时 dtype 正确。（[2444](https://github.com/stefan-jansen/zipline/issues/2444)）

+   修复了一个 bug，其中`window_length=0`的 Pipeline 项在调用`compute()`之前不会复制输入，这可能导致如果输入在 Pipeline 中被重用，则结果不正确。（[2723](https://github.com/stefan-jansen/zipline/issues/2723)）

### 性能

+   添加了`HDF5DailyBarWriter`，它以新的 HDF5 文件格式写入每日定价。每个 OHLCV 字段都存储为块状 HDF5 数据集中的 2D 数组，每行代表一个 sid，每列代表一天。该文件还支持多个国家。添加了`HDF5DailyBarReader`，它实现了 BarReader 接口，并可以读取由 HDF5DailyBarWriter 编写的文件。（[2295](https://github.com/stefan-jansen/zipline/issues/2295)）

+   矢量化股息比率计算（[2298](https://github.com/stefan-jansen/zipline/issues/2298)）

+   改进了`RollingPearson`和`RollingPearsonOfReturns`管道因子的性能。（[2071](https://github.com/stefan-jansen/zipline/issues/2071)）

### 维护和重构

+   使`parameter_space()`在运行之间重置实例固定装置（[2433](https://github.com/stefan-jansen/zipline/issues/2433)）

+   删除了未使用的国债曲线数据处理。（[2626](https://github.com/stefan-jansen/zipline/issues/2626)）

### 杂项

#### 国际管道

管道现在支持国际数据。

管道是一种工具，允许您在资产集合和时间段上定义计算。过去，您只能在美国的股票市场上运行管道。现在，您可以指定一个域，在该域上应该计算管道。“域”一词指的是数学概念中的“函数的域”，即函数的可能输入集合。在管道的上下文中，域指定了一组资产和相应的交易日历，在该日历上应该计算管道的表达式。

例如，以下管道返回所有加拿大股票的最新收盘价和成交量，每天。

```py
pipe = Pipeline(
    columns={
        'price': EquityPricing.close.latest,
        'volume': EquityPricing.volume.latest,
        'mcap': factset.Fundamentals.mkt_val.latest,
    },
    domain=CA_EQUITIES,
) 
```

与货币相关的另一个挑战是，一些交易所不要求股票以当地货币列出。例如，伦敦证券交易所只有大约 75%的上市股票以英镑计价。其余 25%主要以欧元或美元计价。这使得进行跨部门比较变得困难。

为了解决这个问题，大多数人依赖货币转换将基于价格的字段转换为同一货币。管道列现在支持一个`fx`方法，用于指定数据应以哪种货币查看。此方法仅适用于“货币感知”期限，例如开盘或收盘，但不适用于不关心货币的期限，如成交量。

目前，没有办法将国际数据加载到包中。我们正在寻找方法，使将国际数据导入 Zipline 变得容易。

（[2265](https://github.com/stefan-jansen/zipline/issues/2265)，[2262](https://github.com/stefan-jansen/zipline/issues/2262)，以及许多其他）

Zipline 目前支持运行管道的域（使用最新的[trading-calendars](https://pypi.org/project/trading-calendars/)包）如下：

+   阿根廷

+   澳大利亚

+   奥地利

+   比利时

+   巴西

+   加拿大

+   智利

+   中国

+   捷克共和国

+   哥伦比亚

+   捷克共和国

+   芬兰

+   法国

+   德国

+   希腊

+   香港

+   匈牙利

+   印度

+   印度尼西亚

+   爱尔兰

+   意大利

+   日本

+   马来西亚

+   墨西哥

+   荷兰

+   新西兰

+   挪威

+   巴基斯坦

+   秘鲁

+   菲律宾

+   波兰

+   葡萄牙

+   俄罗斯

+   新加坡

+   西班牙

+   瑞典

+   台湾

+   泰国

+   土耳其

+   英国

+   美国

+   南非

+   韩国

+   瑞士

([2301](https://github.com/stefan-jansen/zipline/issues/2301), [2333](https://github.com/stefan-jansen/zipline/issues/2333), [2338](https://github.com/stefan-jansen/zipline/issues/2338), [2355](https://github.com/stefan-jansen/zipline/issues/2355), [2369](https://github.com/stefan-jansen/zipline/issues/2369), [2550](https://github.com/stefan-jansen/zipline/issues/2550), [2552](https://github.com/stefan-jansen/zipline/issues/2552), [2559](https://github.com/stefan-jansen/zipline/issues/2559))

#### DataSetFamily

> 数据集家族用于表示数据，其中行的唯一标识符需要不仅仅是资产和日期坐标。一个 `DataSetFamily` 也可以被认为是一组 `DataSet` 对象，每个对象都有相同的列、域和 ndim。
> 
> `DataSetFamily` 对象是通过一个或多个 `Column` 对象以及一个额外的字段：`extra_dims` 来定义的。
> 
> `extra_dims` 字段定义了除资产和日期之外的坐标，这些坐标必须固定以生成逻辑时间序列。列对象确定将由家族的切片共享的列。
> 
> `extra_dims` 表示为有序字典，其中键是维度名称，值是沿该维度的唯一值的集合。
> 
> 要在流水线表达式中使用 `DataSetFamily`，必须为每个额外的维度选择一个特定的值，使用 `slice()` 方法。例如，给定一个 `DataSetFamily`：
> 
> ```py
> class SomeDataSet(DataSetFamily):
>     extra_dims = [
>         ('dimension_0', {'a', 'b', 'c'}),
>         ('dimension_1', {'d', 'e', 'f'}),
>     ]
> 
>     column_0 = Column(float)
>     column_1 = Column(bool) 
> ```
> 
> 此数据集可能表示具有以下列的表：
> 
> ```py
> sid :: int64
> asof_date :: datetime64[ns]
> timestamp :: datetime64[ns]
> dimension_0 :: str
> dimension_1 :: str
> column_0 :: float64
> column_1 :: bool 
> ```
> 
> 在这里，我们看到了隐含的 `sid`、`asof_date` 和 `timestamp` 列以及额外的维度列。
> 
> 这个 `DataSetFamily` 可以转换为常规的 `DataSet` 使用：
> 
> ```py
> DataSetSlice = SomeDataSet.slice(dimension_0='a', dimension_1='e') 
> ```
> 
> 这个切片数据集表示来自更高维数据集的行，其中 `(dimension_0 == 'a') & (dimension_1 == 'e')`。

([2402](https://github.com/stefan-jansen/zipline/issues/2402), [2452](https://github.com/stefan-jansen/zipline/issues/2452), [2456](https://github.com/stefan-jansen/zipline/issues/2456))

#### 国际管道

流水线现在支持国际数据。

管道是一个工具，允许你在一个资产宇宙和一段时间内定义计算。过去，你只能在美国的股票市场上运行管道。现在，你可以指定一个域，在这个域上应该计算管道。“域”这个名字指的是数学中“函数的域”的概念，即函数可能的输入集合。在管道的上下文中，域指定了资产集合和相应的交易日历，在这个日历上应该计算管道的表达式。

例如，以下管道返回所有加拿大股票的最新收盘价和成交量，每天。

```py
pipe = Pipeline(
    columns={
        'price': EquityPricing.close.latest,
        'volume': EquityPricing.volume.latest,
        'mcap': factset.Fundamentals.mkt_val.latest,
    },
    domain=CA_EQUITIES,
) 
```

与货币相关的另一个挑战是，一些交易所不要求股票以当地货币列出。例如，伦敦证券交易所只有大约 75%的上市股票以英镑计价。其余 25%主要以欧元或美元计价。这使得进行跨部门比较变得困难。

为了解决这个问题，大多数人依赖货币转换将基于价格的字段转换为相同的货币。管道列现在支持一个`fx`方法，用于指定数据应该以哪种货币查看。此方法仅适用于“货币感知”的术语，例如开盘或收盘，但不适用于不关心货币的术语，如成交量。

目前，没有办法将国际数据加载到包中。我们正在寻找方法，使将国际数据导入 Zipline 变得容易。

([2265](https://github.com/stefan-jansen/zipline/issues/2265), [2262](https://github.com/stefan-jansen/zipline/issues/2262), 以及其他许多)

目前，Zipline 支持运行管道的域（使用最新的[trading-calendars](https://pypi.org/project/trading-calendars/)包）如下：

+   阿根廷

+   澳大利亚

+   奥地利

+   比利时

+   巴西

+   加拿大

+   智利

+   中国

+   捷克共和国

+   哥伦比亚

+   捷克

+   芬兰

+   法国

+   德国

+   希腊

+   香港

+   匈牙利

+   印度

+   印度尼西亚

+   爱尔兰

+   意大利

+   日本

+   马来西亚

+   墨西哥

+   荷兰

+   新西兰

+   挪威

+   巴基斯坦

+   秘鲁

+   菲律宾

+   波兰

+   葡萄牙

+   俄罗斯

+   新加坡

+   西班牙

+   瑞典

+   台湾

+   泰国

+   土耳其

+   英国

+   美国

+   南非

+   韩国

+   瑞士

([2301](https://github.com/stefan-jansen/zipline/issues/2301), [2333](https://github.com/stefan-jansen/zipline/issues/2333), [2338](https://github.com/stefan-jansen/zipline/issues/2338), [2355](https://github.com/stefan-jansen/zipline/issues/2355), [2369](https://github.com/stefan-jansen/zipline/issues/2369), [2550](https://github.com/stefan-jansen/zipline/issues/2550), [2552](https://github.com/stefan-jansen/zipline/issues/2552), [2559](https://github.com/stefan-jansen/zipline/issues/2559))

#### 数据集家族

> 数据集家族用于表示行唯一标识符需要超过资产和日期坐标的场景。`DataSetFamily`也可以被视为一组`DataSet`对象，每个对象都有相同的列、域和维度。
> 
> `DataSetFamily`对象是通过一个或多个`Column`对象定义的，再加上一个额外的字段：`extra_dims`。
> 
> `extra_dims`字段定义了除资产和日期之外的坐标，这些坐标必须固定以产生逻辑时间序列。列对象确定将由家族切片共享的列。
> 
> `extra_dims`被表示为一个有序字典，其中键是维度名称，值是沿着该维度的唯一值集合。
> 
> 要在管道表达式中使用`DataSetFamily`，必须使用`slice()`方法为每个额外维度选择一个特定值。例如，给定一个`DataSetFamily`：
> 
> ```py
> class SomeDataSet(DataSetFamily):
>     extra_dims = [
>         ('dimension_0', {'a', 'b', 'c'}),
>         ('dimension_1', {'d', 'e', 'f'}),
>     ]
> 
>     column_0 = Column(float)
>     column_1 = Column(bool) 
> ```
> 
> 这个数据集可能代表具有以下列的表：
> 
> ```py
> sid :: int64
> asof_date :: datetime64[ns]
> timestamp :: datetime64[ns]
> dimension_0 :: str
> dimension_1 :: str
> column_0 :: float64
> column_1 :: bool 
> ```
> 
> 在这里我们可以看到隐含的`sid`、`asof_date`和`timestamp`列，以及额外的维度列。
> 
> 这个`DataSetFamily`可以通过以下方式转换为常规`DataSet`：
> 
> ```py
> DataSetSlice = SomeDataSet.slice(dimension_0='a', dimension_1='e') 
> ```
> 
> 这个切片数据集代表了高维数据集中满足`(dimension_0 == 'a') & (dimension_1 == 'e')`条件的行。

([2402](https://github.com/stefan-jansen/zipline/issues/2402), [2452](https://github.com/stefan-jansen/zipline/issues/2452), [2456](https://github.com/stefan-jansen/zipline/issues/2456))

## 发布版本 1.3.0

发布说明：

1.3.0

日期：

2018 年 7 月 16 日

此版本包含多项增强功能和性能改进，以及少量错误修复。我们建议所有用户升级到此版本。

注意

这很可能是 Zipline 1.x 系列的最后一个次要版本。下一个版本将是 Zipline 2.0，它将包含一些小的破坏性更改，以支持国际股票。

### 亮点

#### 支持更新的 Numpy/Pandas 版本

Zipline 在更新 numpy、pandas 和其他“PyData”生态系统包的版本时一直非常保守。这种保守主要是因为 Zipline 被用作[Quantopian](https://www.quantopian.com/)的回测引擎，这意味着更新包版本可能会破坏大量的已安装代码库。当然，许多 Zipline 用户并没有 Quantopian 那样的向后兼容性要求，他们希望能够使用最新和最棒的包版本。

作为本次发布的一部分，我们现在使用两种包配置构建和测试 Zipline：

+   “稳定版”，使用 numpy 版本 1.11 和 pandas 版本 0.18.1。

+   “最新版”，使用 numpy 版本 1.14 和 pandas 版本 0.22.0。

其他组合的 numpy 和 pandas**可能**可以工作，但这些包集合将在我们的正常开发周期中进行构建和测试。

展望未来，我们的目标是继续在任何给定时间维护两套包的支持。“稳定版”包集合变化相对不频繁，并将包含 Quantopian 支持的 numpy 和 pandas 版本。“最新版”包集合将定期变化，并将包含最近发布的 numpy 和 pandas 版本。

我们希望通过这些变化在稳定性和新颖性之间取得平衡，而不承担支持每种可能的包组合的维护负担。([2194](https://github.com/stefan-jansen/zipline/issues/2194))

#### 独立的`trading_calendars`模块

Zipline 最受欢迎的功能之一是其交易日历集合，提供各种市场的假日和交易时间信息。作为本次发布的一部分，Zipline 的日历相关功能已移至单独的[trading-calendars](https://pypi.org/project/trading-calendars/)包，允许只需要访问日历的用户在不承担 Zipline 其他依赖的情况下使用它们。

为了向后兼容，Zipline 将继续重新导出与日历相关的函数。例如，`zipline.get_calendar()`仍然存在，但现在它是`trading_calendars.get_calendar`的别名。依赖此功能的用户应将其导入更新到`trading_calendars`中的新位置。([2219](https://github.com/stefan-jansen/zipline/issues/2219))

#### 自定义 Blotters

本次发布增加了对使用用户定义的`Blotter`子类的实验性支持。这一变化的主要动机是使从 Zipline CLI 运行实时算法变得更加容易。

配置自定义 blotter 主要有两种方式：

1.  您可以将 `Blotter` 实例作为 `blotter` 参数传递给 `zipline.run_algorithm()`。（此功能之前已存在，但未得到很好的文档记录。）

1.  您可以在 `extension.py` 中注册一个名为 **工厂** 的 blotter，并通过 `--blotter` 标志在命令行上传递名称。

**(2)** 的一个示例用法可能如下所示：

~/.zipline/extension.py

```py
from zipline.extensions import register
from zipline.finance.blotter import Blotter, SimulationBlotter
from zipline.finance.cancel_policy import EODCancel

@register(Blotter, 'my-blotter')
def my_blotter():
  """Create a SimulationBlotter with a non-default cancel policy.
 """
    return SimulationBlotter(cancel_policy=EODCancel()) 
```

要在从命令行运行 zipline 时使用此工厂，我们将以这种方式调用 zipline：

```py
$ zipline run --blotter my-blotter <...other-args...> 
```

作为此更改的一部分，`Blotter` 类已被转换为抽象基类。模拟中使用的默认 blotter 现在名为 `zipline.finance.blotter.SimulationBlotter`。

([2210](https://github.com/stefan-jansen/zipline/issues/2210), [2251](https://github.com/stefan-jansen/zipline/issues/2251))

#### 自定义命令行参数

此版本增加了对向 `zipline` 命令行界面传递自定义参数的支持。自定义命令行参数通过 `-x` 标志后跟一个 `key=value` 对来传递。通过这种方式传递的参数可以从 Python 代码（例如，算法或扩展）通过 `zipline.extension_args` 的属性访问。例如，如果以这种方式调用 zipline：

```py
$ zipline -x argle=bargle run ... 
```

那么 `zipline.extension_args.argle` 的结果将是字符串 `"bargle"`。

自定义参数可以通过在键中包含 `.` 字符来分组到命名空间中。例如，如果以这种方式调用 zipline：

```py
$ zipline -x argle.bargle=foo 
```

那么 `zipline.extension_args.argle` 将包含一个具有 `bargle` 属性的对象，该属性包含字符串 `"foo"`。键可以包含多个点来创建嵌套的命名空间。([2210](https://github.com/stefan-jansen/zipline/issues/2210))

### 增强功能

+   增加了对 pandas 0.22 和 numpy 1.14 的支持。详情请参阅上文。([2194](https://github.com/stefan-jansen/zipline/issues/2194))

+   将 `zipline.utils.calendars` 移至可单独安装的 [trading-calendars](https://pypi.org/project/trading-calendars/) 包中。([2219](https://github.com/stefan-jansen/zipline/issues/2219))

+   增加了对使用 `-x` 标志指定自定义字符串参数的支持。详情请参阅上文。([2210](https://github.com/stefan-jansen/zipline/issues/2210))

### 实验性功能

警告

实验性功能可能会发生变化。

+   增加了对注册 `zipline.finance.blotter.Blotter` 的自定义子类的支持。详情请参阅上文。([2210](https://github.com/stefan-jansen/zipline/issues/2210), [2251](https://github.com/stefan-jansen/zipline/issues/2251))

### 错误修复

+   修复了在 `zipline.pipeline.Factor.winsorize()` 中 NaN 值在确定 winsorization 的截止阈值时被错误地包含在值计数中的 bug。([2138](https://github.com/stefan-jansen/zipline/issues/2138))

+   修复了在 `zipline.pipeline.Factor.top()` 中计数为 1 且没有 groupby 时的崩溃问题。([2218](https://github.com/stefan-jansen/zipline/issues/2218))

+   修复了一个 bug，即在调用 `data.history` 时使用负的回溯会从未来获取价格。([2164](https://github.com/stefan-jansen/zipline/issues/2164))

+   修复了一个 bug，其中 `StopOrder`、`zipline.finance.execution.LimitOrder` 和 `zipline.finance.execution.StopLimitOrder` 的价格被四舍五入到最接近的便士，而不管资产的刻度大小。现在，价格根据所订购资产的 `tick_size` 属性进行四舍五入。([2211](https://github.com/stefan-jansen/zipline/issues/2211))

### 性能

+   改进了定期交易的资产获取每分钟价格的性能。([2108](https://github.com/stefan-jansen/zipline/issues/2108))

+   通过调整缓存大小，改进了获取许多资产的每分钟价格的性能。([2110](https://github.com/stefan-jansen/zipline/issues/2110))

### 维护和重构

+   重构了 Zipline 测试套件的大部分内容，使其更容易更改 `zipline.algorithm.TradingAlgorithm` 的签名。([2169](https://github.com/stefan-jansen/zipline/issues/2169), [2168](https://github.com/stefan-jansen/zipline/issues/2168), [2165](https://github.com/stefan-jansen/zipline/issues/2165), [2171](https://github.com/stefan-jansen/zipline/issues/2171))

### 构建

+   添加了使用 pandas 0.18 和 0.22 运行 travis 构建的支持。([2194](https://github.com/stefan-jansen/zipline/issues/2194))

+   在 travis 构建矩阵中添加了 OSX 构建。([2244](https://github.com/stefan-jansen/zipline/issues/2244))

### 亮点

#### 支持更新的 Numpy/Pandas 版本

在历史上，Zipline 在更新 numpy、pandas 以及其他“PyData”生态系统包的版本时一直非常保守。这种保守主要是因为 Zipline 被用作 [Quantopian](https://www.quantopian.com/) 的回测引擎，这意味着更新包版本可能会破坏大量的已安装代码库。当然，许多 Zipline 用户并没有 Quantopian 那样的向后兼容性要求，他们希望能够使用最新、最棒的包版本。

作为此次发布的一部分，我们现在使用两种包配置构建和测试 Zipline：

+   “稳定版”，使用 numpy 版本 1.11 和 pandas 版本 0.18.1。

+   “最新”，使用 numpy 版本 1.14 和 pandas 版本 0.22.0。

其他 numpy 和 pandas 的组合**可能**有效，但这些包集将在我们的正常开发周期中进行构建和测试。

展望未来，我们的目标是继续在任何给定时间维护对两组包的支持。“稳定”包集将相对不频繁地变化，并将包含 Quantopian 支持的 numpy 和 pandas 版本。“最新”包集将定期变化，并将包含最近发布的 numpy 和 pandas 版本。

我们希望通过这些改变在稳定性和新颖性之间取得平衡，而不承担过大的维护负担，即支持所有可能的包组合。([2194](https://github.com/stefan-jansen/zipline/issues/2194))

#### 独立的`trading_calendars`模块

Zipline 最受欢迎的功能之一是其交易日历集合，这些日历提供了关于各种市场假日和交易时间的信息。作为此版本的一部分，Zipline 的日历相关功能已被移动到一个单独的[trading-calendars](https://pypi.org/project/trading-calendars/)包中，允许只需要访问日历的用户使用它们，而不必承担 Zipline 其余部分的依赖。

为了向后兼容，Zipline 将继续重新导出与日历相关的函数。例如，`zipline.get_calendar()`仍然存在，但现在它是`trading_calendars.get_calendar`的别名。依赖此功能的用户被鼓励更新他们的导入到`trading_calendars`中的新位置。([2219](https://github.com/stefan-jansen/zipline/issues/2219))

#### 自定义交易记录器

此版本增加了对使用用户定义的`Blotter`子类运行 Zipline 的实验性支持。这一变化的主要动机是使从 Zipline CLI 运行实时算法变得更加容易。

配置自定义交易记录器主要有两种方式：

1.  您可以将`Blotter`实例作为`blotter`参数传递给`zipline.run_algorithm()`。（此功能之前已经存在，但文档不够完善。）

1.  您可以在`extension.py`中注册一个名为**工厂**的交易记录器，并通过命令行上的`--blotter`标志传递名称。

对于**(2)**的一个示例用法可能如下所示：

~/.zipline/extension.py

```py
from zipline.extensions import register
from zipline.finance.blotter import Blotter, SimulationBlotter
from zipline.finance.cancel_policy import EODCancel

@register(Blotter, 'my-blotter')
def my_blotter():
  """Create a SimulationBlotter with a non-default cancel policy.
 """
    return SimulationBlotter(cancel_policy=EODCancel()) 
```

要在命令行上运行 zipline 时使用此工厂，我们将这样调用 zipline：

```py
$ zipline run --blotter my-blotter <...other-args...> 
```

作为此次变更的一部分，`Blotter`类已被转换为抽象基类。模拟中使用的默认账本现在名为`zipline.finance.blotter.SimulationBlotter`。

（[2210](https://github.com/stefan-jansen/zipline/issues/2210)，[2251](https://github.com/stefan-jansen/zipline/issues/2251)）

#### 自定义命令行参数

此版本增加了对将自定义参数传递给`zipline`命令行界面的支持。自定义命令行参数通过`-x`标志后跟一个`key=value`对来传递。通过这种方式传递的参数可以从 Python 代码（例如，算法或扩展）通过`zipline.extension_args`的属性访问。例如，如果以这种方式调用 zipline：

```py
$ zipline -x argle=bargle run ... 
```

那么`zipline.extension_args.argle`的结果将是字符串`"bargle"`。

自定义参数可以通过在键中包含`.`字符来分组到命名空间中。例如，如果以这种方式调用 zipline：

```py
$ zipline -x argle.bargle=foo 
```

那么`zipline.extension_args.argle`将包含一个具有`bargle`属性的对象，该属性包含字符串`"foo"`。键可以包含多个点来创建嵌套的命名空间。（[2210](https://github.com/stefan-jansen/zipline/issues/2210)）

#### 支持更新的 Numpy/Pandas 版本

Zipline 在更新 numpy、pandas 和其他“PyData”生态系统包的版本时历来非常保守。这种保守主要是因为 Zipline 被用作[Quantopian](https://www.quantopian.com/)的回测引擎，这意味着更新包版本可能会破坏大量已安装的代码库。当然，许多 Zipline 用户没有 Quantopian 那样的向后兼容性要求，他们希望能够使用最新和最棒的包版本。

作为此次发布的一部分，我们现在使用两种包配置构建和测试 Zipline：

+   “稳定”，使用 numpy 版本 1.11 和 pandas 版本 0.18.1。

+   “最新”，使用 numpy 版本 1.14 和 pandas 版本 0.22.0。

numpy 和 pandas 的其他组合**可能**有效，但这些包组将在我们的正常开发周期中构建和测试。

我们的目标是在任何给定时间继续维护两组包的支持。“稳定”包组相对变化不大，将包含 Quantopian 支持的 numpy 和 pandas 版本。“最新”包组将定期变化，将包含最近发布的 numpy 和 pandas 版本。

我们希望通过这些变化在稳定性和新颖性之间取得平衡，而不承担支持每种可能的包组合的过重维护负担。（[2194](https://github.com/stefan-jansen/zipline/issues/2194)）

#### 独立的`trading_calendars`模块

Zipline 最受欢迎的功能之一是其交易日历集合，这些日历提供了有关各种市场假日和交易时间的信息。作为此版本的一部分，Zipline 的日历相关功能已移至单独的[trading-calendars](https://pypi.org/project/trading-calendars/)包，允许只需要访问日历的用户在不承担 Zipline 其余依赖项的情况下使用它们。

为了向后兼容，Zipline 将继续重新导出与日历相关的函数。例如，`zipline.get_calendar()`仍然存在，但现在它是`trading_calendars.get_calendar`的别名。依赖此功能的用户被鼓励更新他们的导入到`trading_calendars`中的新位置。([2219](https://github.com/stefan-jansen/zipline/issues/2219))

#### 自定义 Blotters

此版本增加了对运行 Zipline 的用户定义的`Blotter`子类的实验性支持。这一变化的主要动机是使从 Zipline CLI 运行实时算法变得更加容易。

配置自定义 blotter 主要有两种方式：

1.  您可以将`Blotter`的实例作为`blotter`参数传递给`zipline.run_algorithm()`。（此功能之前已经存在，但文档不够完善。）

1.  您可以在`extension.py`中注册一个名为**工厂**的 blotter，并通过命令行上的`--blotter`标志传递名称。

对**(2)**的一个示例用法可能看起来像这样：

~/.zipline/extension.py

```py
from zipline.extensions import register
from zipline.finance.blotter import Blotter, SimulationBlotter
from zipline.finance.cancel_policy import EODCancel

@register(Blotter, 'my-blotter')
def my_blotter():
  """Create a SimulationBlotter with a non-default cancel policy.
 """
    return SimulationBlotter(cancel_policy=EODCancel()) 
```

要从命令行运行 zipline 时使用此工厂，我们将这样调用 zipline：

```py
$ zipline run --blotter my-blotter <...other-args...> 
```

作为这一变化的一部分，`Blotter`类已被转换为抽象基类。模拟中使用的默认 blotter 现在名为`zipline.finance.blotter.SimulationBlotter`。

([2210](https://github.com/stefan-jansen/zipline/issues/2210), [2251](https://github.com/stefan-jansen/zipline/issues/2251))

#### 自定义命令行参数

此版本增加了对向`zipline`命令行界面传递自定义参数的支持。自定义命令行参数通过`-x`标志后跟一个`key=value`对传递。通过这种方式传递的参数可以从 Python 代码（例如，算法或扩展）通过`zipline.extension_args`的属性访问。例如，如果这样调用 zipline：

```py
$ zipline -x argle=bargle run ... 
```

那么`zipline.extension_args.argle`的结果将是字符串`"bargle"`。

自定义参数可以通过在键中包含 `.` 字符来分组到命名空间中。例如，如果以这种方式调用 zipline：

```py
$ zipline -x argle.bargle=foo 
```

然后 `zipline.extension_args.argle` 将包含一个具有 `bargle` 属性的对象，该属性包含字符串 `"foo"`。键可以包含多个点来创建嵌套的命名空间。([2210](https://github.com/stefan-jansen/zipline/issues/2210))

### 增强功能

+   增加了对 pandas 0.22 和 numpy 1.14 的支持。详情请参阅上文。([2194](https://github.com/stefan-jansen/zipline/issues/2194))

+   将 `zipline.utils.calendars` 移至一个单独可安装的 [trading-calendars](https://pypi.org/project/trading-calendars/) 包中。([2219](https://github.com/stefan-jansen/zipline/issues/2219))

+   增加了使用 `-x` 标志指定自定义字符串参数的支持。详情请参阅上文。([2210](https://github.com/stefan-jansen/zipline/issues/2210))

### 实验性功能

警告

实验性功能可能会发生变化。

+   增加了注册 `zipline.finance.blotter.Blotter` 的自定义子类的支持。详情请参阅上文。([2210](https://github.com/stefan-jansen/zipline/issues/2210), [2251](https://github.com/stefan-jansen/zipline/issues/2251))

### 错误修复

+   修复了在 `zipline.pipeline.Factor.winsorize()` 中，当确定 winsorization 的截断阈值时，NaN 值被错误地计入值计数中的 bug。([2138](https://github.com/stefan-jansen/zipline/issues/2138))

+   修复了在 `zipline.pipeline.Factor.top()` 中，当计数为 1 且没有 groupby 时发生的崩溃。([2218](https://github.com/stefan-jansen/zipline/issues/2218))

+   修复了调用 `data.history` 时，如果回溯期为负数，则会从未来获取价格的 bug。([2164](https://github.com/stefan-jansen/zipline/issues/2164))

+   修复了 `StopOrder`、`zipline.finance.execution.LimitOrder` 和 `zipline.finance.execution.StopLimitOrder` 的价格无论资产的 tick 大小如何都被四舍五入到最接近的便士的 bug。现在价格根据所订购资产的 `tick_size` 属性进行四舍五入。([2211](https://github.com/stefan-jansen/zipline/issues/2211))

### 性能

+   改进了定期交易的资产获取每分钟价格的性能。([2108](https://github.com/stefan-jansen/zipline/issues/2108))

+   通过调整缓存大小，改进了获取大量资产每分钟价格的性能。([2110](https://github.com/stefan-jansen/zipline/issues/2110))

### 维护和重构

+   重构了 Zipline 测试套件的大部分内容，使其更容易更改`zipline.algorithm.TradingAlgorithm`的签名（[2169](https://github.com/stefan-jansen/zipline/issues/2169)，[2168](https://github.com/stefan-jansen/zipline/issues/2168)，[2165](https://github.com/stefan-jansen/zipline/issues/2165)，[2171](https://github.com/stefan-jansen/zipline/issues/2171)）

### 构建

+   增加了对使用 pandas 0.18 和 0.22 运行 travis 构建的支持（[2194](https://github.com/stefan-jansen/zipline/issues/2194)）

+   在 travis 构建矩阵中增加了 OSX 构建（[2244](https://github.com/stefan-jansen/zipline/issues/2244)）

## 发布 1.2.0

发布：

1.2.0

日期：

2018 年 4 月 4 日

### 亮点

#### 可扩展的风险和性能指标（[2081](https://github.com/stefan-jansen/zipline/issues/2081)）

风险和性能指标是 Zipline 在运行模拟时计算的汇总值，例如：回报率或夏普比率。1.1.2 引入了一个新的 API，用于注册用户定义的自定义风险和性能指标。我们还使得在不计算任何指标的情况下运行回测成为可能，以改善调试算法的反馈循环。

更多信息，请参阅 Metrics。

#### 文档、交易日历和基准

Zipline 现在默认使用`quandl` bundle，您需要一个 API Key，可以在数据包文档中找到相关信息。

我们添加了许多教程和文档更新，包括有关如何创建自己的`TradingCalendar`，通过 Zipline CLI 将其传递给您的算法，以及如何使用`csvdir` bundle 使用自定义 csv 数据的信息。

Zipline 不再为 Python 3.4 进行测试和打包。

Zipline 现在通过[IEX Trading](https://iextrading.com) API 请求 SPY 数据，SPY 是 Zipline 回测使用的默认基准，不再使用`pandas-datareader`。您可以使用此数据运行长达 5 年的回测。

### 增强功能

+   将分钟文件缓存默认增长，默认值为 1550（[1906](https://github.com/stefan-jansen/zipline/issues/1906)）

+   将默认佣金更改为.001（[1946](https://github.com/stefan-jansen/zipline/issues/1946)）

+   启用计算多个管道的能力（[1974](https://github.com/stefan-jansen/zipline/issues/1974)）

+   允许用户在不同日历之间切换（[1800](https://github.com/stefan-jansen/zipline/issues/1800)）

+   新增过滤器`NoMissingValues`（[1969](https://github.com/stefan-jansen/zipline/issues/1969)）

+   在`AssetFinder(nonexistent_path)`上更好地失败（[2000](https://github.com/stefan-jansen/zipline/issues/2000)）

+   实现 csvdir bundle（[1860](https://github.com/stefan-jansen/zipline/issues/1860)）

+   更新 quandl_bundle 以使用 Quandl API v3（[1990](https://github.com/stefan-jansen/zipline/issues/1990)）

+   添加`FixedBasisPointsSlippage`滑点模型（[2047](https://github.com/stefan-jansen/zipline/issues/2047)）

+   创建 MinLeverage 控制（[2064](https://github.com/stefan-jansen/zipline/issues/2064)）

### 实验性功能

警告

实验性功能可能会发生变化。

无

### 错误修复

+   使用 Panel 作为分钟数据源时，频率为`1d`的`history`调用现在可以正常工作（[1920](https://github.com/stefan-jansen/zipline/issues/1920)）

+   在使用期货每日条形阅读器时检查合约是否存在（[1892](https://github.com/stefan-jansen/zipline/issues/1892)）

+   `NoDataBeforeDate`的边缘情况（[1894](https://github.com/stefan-jansen/zipline/issues/1894)）

+   修复 Python 2.7.5 中的框架列验证（[1954](https://github.com/stefan-jansen/zipline/issues/1954)）

+   修复分钟面板数据回测的每日历史记录（[1920](https://github.com/stefan-jansen/zipline/issues/1920)）

+   `get_last_traded_dt`期望一个交易日（[2087](https://github.com/stefan-jansen/zipline/issues/2087)）

+   日常调整视角修复（[2089](https://github.com/stefan-jansen/zipline/issues/2089)）

### 性能

+   将算法账户验证从`handle_data`中每分钟发生一次改为每天结束时仅发生一次（[1884](https://github.com/stefan-jansen/zipline/issues/1884)）

+   Blaze 核心加载器性能改进（[1866](https://github.com/stefan-jansen/zipline/issues/1866)）

+   添加一个仅计算贝塔值的新因子（[2021](https://github.com/stefan-jansen/zipline/issues/2021)）

+   减少 Quandl WIKI 价格包的内存占用（[2053](https://github.com/stefan-jansen/zipline/issues/2053)）

### 维护和重构

+   添加`CachedObject.expired()`（[1881](https://github.com/stefan-jansen/zipline/issues/1881)）

+   将`RollingLinearRegressionOfReturns`因子设置为 window_safe（[1902](https://github.com/stefan-jansen/zipline/issues/1902)）

+   将`RSI`因子设置为 window_safe（[1904](https://github.com/stefan-jansen/zipline/issues/1904)）

+   改进文档生成的更新（[1890](https://github.com/stefan-jansen/zipline/issues/1890)）

+   移除并清空未使用的国债曲线（[1910](https://github.com/stefan-jansen/zipline/issues/1910)）

+   Networkx 2 改变了 out_degree 的行为（[1996](https://github.com/stefan-jansen/zipline/issues/1996)）

+   将日历传递给`DataPortal`（[2026](https://github.com/stefan-jansen/zipline/issues/2026)）

+   移除旧的 Yahoo 代码（[2032](https://github.com/stefan-jansen/zipline/issues/2032)）

+   同步并通过最新交易日填充基准（[2044](https://github.com/stefan-jansen/zipline/issues/2044)）

+   当 QUANDL_API_KEY 缺失时提供更好的错误消息（[2078](https://github.com/stefan-jansen/zipline/issues/2078)）

+   改进管道引擎中日期错位时的错误消息（[2131](https://github.com/stefan-jansen/zipline/issues/2131)）

### 构建

+   更新我们使用的 conda 工具以修复我们的打包问题（[1942](https://github.com/stefan-jansen/zipline/issues/1942)）

+   将 empyrical 升级到 0.3.2（[1983](https://github.com/stefan-jansen/zipline/issues/1983)）

+   更新 conda 工具并移除 Python 3.4 构建（[2009](https://github.com/stefan-jansen/zipline/issues/2009)）

+   将 empyrical 升级到 0.3.3（[2014](https://github.com/stefan-jansen/zipline/issues/2014)）

+   将 empyrical 升级到 0.3.4（[2098](https://github.com/stefan-jansen/zipline/issues/2098)）

+   将 empyrical 升级到 0.4.2（[2125](https://github.com/stefan-jansen/zipline/issues/2125)）

### 文档

+   在 zipline.io 文档中包含`MACDSignal`（[1828](https://github.com/stefan-jansen/zipline/issues/1828)）

+   从入门教程中删除对 Yahoo 的提及（[1845](https://github.com/stefan-jansen/zipline/issues/1845)）

+   在 README 中添加贡献和问题部分（[1889](https://github.com/stefan-jansen/zipline/issues/1889)）

+   添加有关使用 conda 环境进行安装的信息（[1922](https://github.com/stefan-jansen/zipline/issues/1922)）

+   修复入门教程链接（[1932](https://github.com/stefan-jansen/zipline/issues/1932)）

+   添加干净的文档（[1943](https://github.com/stefan-jansen/zipline/issues/1943)）

+   为基准和财政部提取器添加不同的警告（[1971](https://github.com/stefan-jansen/zipline/issues/1971)）

+   添加 CONTRIBUTING.rst（[2033](https://github.com/stefan-jansen/zipline/issues/2033)）

+   添加有关创建自定义`TradingCalendar`的教程（[2035](https://github.com/stefan-jansen/zipline/issues/2035)）

+   文档和教程更新，用于摄取、初学者和 csvdir（[2073](https://github.com/stefan-jansen/zipline/issues/2073)）

+   记录了新的风险和性能指标 API（[2081](https://github.com/stefan-jansen/zipline/issues/2081)）。

+   修复了`--bundle-timestamp`描述中的一个拼写错误（[2123](https://github.com/stefan-jansen/zipline/issues/2123)）

### 杂项

无

### 亮点

#### 可扩展的风险和性能指标（[2081](https://github.com/stefan-jansen/zipline/issues/2081)）

风险和性能指标总结了 Zipline 在运行模拟时计算的值，例如：回报率或夏普比率。1.1.2 引入了一个新的 API，用于注册用户定义的自定义风险和性能指标。我们还使得在不计算任何指标的情况下运行回测成为可能，以改善调试算法时的反馈循环。

有关更多信息，请参阅 Metrics。

#### 文档、交易日历和基准

Zipline 现在默认使用`quandl`捆绑包，您需要一个 API 密钥，可以在数据捆绑包文档中找到相关信息。

我们增加了许多教程和文档更新，包括如何创建自己的`TradingCalendar`，通过 Zipline CLI 将其传递给算法，以及如何使用`csvdir`捆绑包使用自定义 csv 数据。

Zipline 不再为 Python 3.4 进行测试和打包。

Zipline 现在使用[IEX Trading](https://iextrading.com) API 请求 SPY 的数据，这是 Zipline 回测使用的默认基准，不再使用`pandas-datareader`。您可以使用此数据运行长达 5 年的回测。

#### 可扩展的风险和性能指标（[2081](https://github.com/stefan-jansen/zipline/issues/2081)）

风险和性能指标汇总了 Zipline 在运行模拟时计算的值，例如：回报率或夏普比率。1.1.2 版本引入了一个新的 API，用于注册用户定义的自定义风险和性能指标。我们还使得在不计算任何指标的情况下运行回测成为可能，以改善调试算法时的反馈循环。

更多信息，请参阅 Metrics。

#### 文档、交易日历和基准

现在 Zipline 默认使用`quandl`数据包，这需要一个 API 密钥，你可以在数据包文档中找到相关信息。

我们增加了许多教程和文档更新，包括如何创建自己的`TradingCalendar`，通过 Zipline CLI 将其传递给算法，以及如何使用`csvdir`数据包使用自定义 csv 数据。

Zipline 不再为 Python 3.4 进行测试和打包。

Zipline 现在使用[IEX Trading](https://iextrading.com) API 请求 SPY 数据，这是 Zipline 回测默认使用的基准，不再使用`pandas-datareader`。你可以使用这些数据进行长达 5 年的回测。

### 增强功能

+   将分钟文件缓存默认大小增加到 1550（[1906](https://github.com/stefan-jansen/zipline/issues/1906)）

+   将默认佣金改为 0.001（[1946](https://github.com/stefan-jansen/zipline/issues/1946)）

+   启用计算多个管道的能力（[1974](https://github.com/stefan-jansen/zipline/issues/1974)）

+   允许用户在不同日历之间切换（[1800](https://github.com/stefan-jansen/zipline/issues/1800)）

+   新增过滤器`NoMissingValues`（[1969](https://github.com/stefan-jansen/zipline/issues/1969)）

+   在`AssetFinder(nonexistent_path)`上更好地处理失败（[2000](https://github.com/stefan-jansen/zipline/issues/2000)）

+   实现 csvdir 数据包（[1860](https://github.com/stefan-jansen/zipline/issues/1860)）

+   将 quandl_bundle 更新为使用 Quandl API v3（[1990](https://github.com/stefan-jansen/zipline/issues/1990)）

+   添加`FixedBasisPointsSlippage`滑点模型（[2047](https://github.com/stefan-jansen/zipline/issues/2047)）

+   创建最小杠杆控制（[2064](https://github.com/stefan-jansen/zipline/issues/2064)）

### 实验性功能

警告

实验性功能可能会发生变化。

无

### 错误修复

+   当使用 Panel 作为分钟数据源时，`history`调用频率为`1d`现在可以正常工作（[1920](https://github.com/stefan-jansen/zipline/issues/1920)）

+   在使用期货日 K 线阅读器时检查合约是否存在（[1892](https://github.com/stefan-jansen/zipline/issues/1892)）

+   `NoDataBeforeDate`的边缘情况（[1894](https://github.com/stefan-jansen/zipline/issues/1894)）

+   修复 Python 2.7.5 中的帧列验证（[1954](https://github.com/stefan-jansen/zipline/issues/1954)）

+   修复分钟面板数据回测的日历史记录（[1920](https://github.com/stefan-jansen/zipline/issues/1920)）

+   `get_last_traded_dt` 期望得到一个交易日（[2087](https://github.com/stefan-jansen/zipline/issues/2087)）

+   日常调整视角修复（[2089](https://github.com/stefan-jansen/zipline/issues/2089)）

### 性能

+   将算法账户验证从`handle_data`中每分钟发生改为仅在每天结束时发生一次（[1884](https://github.com/stefan-jansen/zipline/issues/1884)）

+   Blaze 核心加载器性能改进（[1866](https://github.com/stefan-jansen/zipline/issues/1866)）

+   添加一个仅计算 beta 的新因子（[2021](https://github.com/stefan-jansen/zipline/issues/2021)）

+   减少 Quandl WIKI 价格包的内存占用（[2053](https://github.com/stefan-jansen/zipline/issues/2053)）

### 维护和重构

+   添加`CachedObject.expired()`（[1881](https://github.com/stefan-jansen/zipline/issues/1881)）

+   将`RollingLinearRegressionOfReturns`因子设置为 window_safe（[1902](https://github.com/stefan-jansen/zipline/issues/1902)）

+   将`RSI`因子设置为 window_safe（[1904](https://github.com/stefan-jansen/zipline/issues/1904)）

+   为更好的文档生成进行更新（[1890](https://github.com/stefan-jansen/zipline/issues/1890)）

+   移除并清零未使用的国债曲线（[1910](https://github.com/stefan-jansen/zipline/issues/1910)）

+   Networkx 2 改变了 out_degree 的行为（[1996](https://github.com/stefan-jansen/zipline/issues/1996)）

+   向`DataPortal`传递日历（[2026](https://github.com/stefan-jansen/zipline/issues/2026)）

+   移除旧的 Yahoo 代码（[2032](https://github.com/stefan-jansen/zipline/issues/2032)）

+   同步并填充至最新交易日的基准（[2044](https://github.com/stefan-jansen/zipline/issues/2044)）

+   当 QUANDL_API_KEY 缺失时提供更好的错误信息（[2078](https://github.com/stefan-jansen/zipline/issues/2078)）

+   改进管道引擎中日期不一致的错误信息（[2131](https://github.com/stefan-jansen/zipline/issues/2131)）

### 构建

+   更新我们使用的 conda 工具以修复我们的打包（[1942](https://github.com/stefan-jansen/zipline/issues/1942)）

+   将 empyrical 升级到 0.3.2（[1983](https://github.com/stefan-jansen/zipline/issues/1983)）

+   更新 conda 工具并移除 Python 3.4 构建（[2009](https://github.com/stefan-jansen/zipline/issues/2009)）

+   将 empyrical 升级到 0.3.3（[2014](https://github.com/stefan-jansen/zipline/issues/2014)）

+   将 empyrical 升级到 0.3.4（[2098](https://github.com/stefan-jansen/zipline/issues/2098)）

+   将 empyrical 升级到 0.4.2（[2125](https://github.com/stefan-jansen/zipline/issues/2125)）

### 文档

+   在 zipline.io 文档中包含`MACDSignal`（[1828](https://github.com/stefan-jansen/zipline/issues/1828)）

+   从入门教程中移除 Yahoo 的提及（[1845](https://github.com/stefan-jansen/zipline/issues/1845)）

+   在 README 中添加贡献和问题部分（[1889](https://github.com/stefan-jansen/zipline/issues/1889)）

+   添加关于使用 conda 环境进行安装的信息（[1922](https://github.com/stefan-jansen/zipline/issues/1922)）

+   修复入门教程链接（[1932](https://github.com/stefan-jansen/zipline/issues/1932)）

+   添加清晰的文档（[1943](https://github.com/stefan-jansen/zipline/issues/1943)）

+   为基准和财政部提取器添加不同的警告（[1971](https://github.com/stefan-jansen/zipline/issues/1971)）

+   添加 CONTRIBUTING.rst（[2033](https://github.com/stefan-jansen/zipline/issues/2033)）

+   添加了关于创建自定义`TradingCalendar`的教程（[2035](https://github.com/stefan-jansen/zipline/issues/2035)）

+   文档和教程更新，包括数据导入、初学者和 csvdir（[2073](https://github.com/stefan-jansen/zipline/issues/2073)）

+   记录了新的风险和绩效指标 API（[2081](https://github.com/stefan-jansen/zipline/issues/2081)）

+   修复了`--bundle-timestamp`描述中的一个拼写错误（[2123](https://github.com/stefan-jansen/zipline/issues/2123)）

### 杂项

无

## 发布 1.1.1

发布：

1.1.1

日期：

2017 年 7 月 5 日

### 亮点

Zipline 现在除了股票外，还广泛支持期货。它也正在为 Python 3.5 进行测试和打包。

我们还看到由于雅虎更改了其 API 端点，导致用户无法下载回测所需的基准数据，从而发生了重大变化。自那次更改以来，我们已经用对谷歌财经的引用来替换与雅虎相关的基准代码，并删除了所有已弃用的雅虎代码，包括对自定义雅虎包的使用。

### 增强功能

+   为 BarData 添加了一个属性，以了解当前会话的分钟数（[1713](https://github.com/stefan-jansen/zipline/issues/1713)）

+   为不存在的根符号添加了更好的错误消息（[1715](https://github.com/stefan-jansen/zipline/issues/1715)）

+   添加了`StaticSids` 流水线过滤器（[1717](https://github.com/stefan-jansen/zipline/issues/1717)）

+   允许`zipline.data.data_portal.DataPortal.get_spot_value`接受多个资产（[1719](https://github.com/stefan-jansen/zipline/issues/1719)）

+   向`lookup_generic`添加了`ContinuousFuture`（[1718](https://github.com/stefan-jansen/zipline/issues/1718)）

+   向`exchange_calendar_cfe`添加 CFE 临时假日（[1698](https://github.com/stefan-jansen/zipline/issues/1698)）

+   允许覆盖订单金额的舍入（[1722](https://github.com/stefan-jansen/zipline/issues/1722)）

+   使连续期货调整风格成为参数（[1726](https://github.com/stefan-jansen/zipline/issues/1726)）

+   添加了对期货滑点和佣金模型的初步支持（[1738](https://github.com/stefan-jansen/zipline/issues/1738)）

+   修复了成本基础计算中的一个错误，并将所有提及的`sid`更改为`asset`（[1757](https://github.com/stefan-jansen/zipline/issues/1757)）

+   添加期货的滑点和佣金模型（[1748](https://github.com/stefan-jansen/zipline/issues/1748)）

+   在我们的 Dockerfile 中使用 Python 3.5（[1806](https://github.com/stefan-jansen/zipline/issues/1806)）

+   允许流水线以块的形式运行（[1811](https://github.com/stefan-jansen/zipline/issues/1811)）

+   向 BenchmarkSource 添加了 get_range（[1815](https://github.com/stefan-jansen/zipline/issues/1815)）

+   添加了对 Pipeline 中重新标记分类器的支持（[1833](https://github.com/stefan-jansen/zipline/issues/1833)）

### 实验性功能

警告

实验性功能可能会发生变化。

无

### 错误修复

+   在`zipline.data.minute_bars`中修复了浮点数除法问题，改为使用整数除法（[1683](https://github.com/stefan-jansen/zipline/issues/1683)）

+   在`zipline.pipeline.loaders.blaze.core`中按`asof_date`排序数据，以解决时间戳冲突（[1710](https://github.com/stefan-jansen/zipline/issues/1710)）

+   将 Yahoo 替换为 Google Finance 的基准数据（[1812](https://github.com/stefan-jansen/zipline/issues/1812)）

+   黄金和白银期货合约只在某些月份交易（[1779](https://github.com/stefan-jansen/zipline/issues/1779)）

+   修复了在使用时区感知的时间时，TradingCalendar 初始化中的错误（[1802](https://github.com/stefan-jansen/zipline/issues/1802)）

+   修复了期货价格四舍五入时的精度问题（[1788](https://github.com/stefan-jansen/zipline/issues/1788)）

### 性能改进

+   在获取前向填充的收盘价时避免重复的递归调用（[1735](https://github.com/stefan-jansen/zipline/issues/1735)）

### 维护和重构

+   将 linter 建议添加到 adjustments 模块（[1712](https://github.com/stefan-jansen/zipline/issues/1712)）

+   清理了 resample close 中的命名和逻辑（[1728](https://github.com/stefan-jansen/zipline/issues/1728)）

+   对几个连续期货使用 3 月季度周期（[1762](https://github.com/stefan-jansen/zipline/issues/1762)）

+   为 Transaction 对象使用更好的 repr（[1746](https://github.com/stefan-jansen/zipline/issues/1746)）

+   缩短 Asset 对象的 repr（[1786](https://github.com/stefan-jansen/zipline/issues/1786)）

+   移除了 empyrical 的信息比率的使用（[1854](https://github.com/stefan-jansen/zipline/issues/1854)）

### 构建

+   添加了 Python 3.5 的包（[1701](https://github.com/stefan-jansen/zipline/issues/1701)）

+   交换 conda-build 参数，这样我们就不会在每次 CI 构建时都构建包（[1813](https://github.com/stefan-jansen/zipline/issues/1813)）

### 文档更新

+   添加了 Zipline 开发指南，供人们阅读如何为 Zipline 做贡献（[1820](https://github.com/stefan-jansen/zipline/issues/1820)）

+   对于股票，要求显示交易所（[1731](https://github.com/stefan-jansen/zipline/issues/1731)）

+   更新了 Zipline 入门教程笔记本（[1707](https://github.com/stefan-jansen/zipline/issues/1707)）

+   将 PipelineEngine、pipeline Term、Factors 等管道相关内容添加到文档中（[1826](https://github.com/stefan-jansen/zipline/issues/1826)）

### 其他

+   使用 csv 市场数据与`run_algorithm`，这样我们就不需要在测试时尝试下载数据（[1793](https://github.com/stefan-jansen/zipline/issues/1793)）

+   更新 Dockerfile 以使用 Python 3.5

### 亮点

Zipline 现在除了股票外，还广泛支持期货。它也正在为 Python 3.5 进行测试和打包。

我们还注意到，由于雅虎更改了他们的 API 端点，导致用户无法下载回测所需的基准数据。自那次更改以来，我们已经将所有与雅虎相关的基准代码替换为对谷歌财经的引用，并删除了所有已弃用的雅虎代码，包括自定义雅虎捆绑包的使用。

### 增强功能

+   为 BarData 添加了一个属性，以了解当前会话的分钟数（[1713](https://github.com/stefan-jansen/zipline/issues/1713)）

+   为不存在的根符号添加了更好的错误消息（[1715](https://github.com/stefan-jansen/zipline/issues/1715)）

+   添加了`StaticSids`管道过滤器（[1717](https://github.com/stefan-jansen/zipline/issues/1717)）

+   允许`zipline.data.data_portal.DataPortal.get_spot_value`接受多个资产（[1719](https://github.com/stefan-jansen/zipline/issues/1719)）

+   将`ContinuousFuture`添加到`lookup_generic`中（[1718](https://github.com/stefan-jansen/zipline/issues/1718)）

+   为`exchange_calendar_cfe`添加了 CFE 临时假日（[1698](https://github.com/stefan-jansen/zipline/issues/1698)）

+   允许覆盖订单金额的舍入（[1722](https://github.com/stefan-jansen/zipline/issues/1722)）

+   将连续期货调整风格作为参数（[1726](https://github.com/stefan-jansen/zipline/issues/1726)）

+   添加了对期货滑点和佣金模型的初步支持（[1738](https://github.com/stefan-jansen/zipline/issues/1738)）

+   修复了成本基础计算中的错误，并将所有提及的`sid`改为`asset`（[1757](https://github.com/stefan-jansen/zipline/issues/1757)）

+   添加了期货的滑点和佣金模型（[1748](https://github.com/stefan-jansen/zipline/issues/1748)）

+   在我们的 Dockerfile 中使用 Python 3.5（[1806](https://github.com/stefan-jansen/zipline/issues/1806)）

+   允许以块的形式运行管道（[1811](https://github.com/stefan-jansen/zipline/issues/1811)）

+   为 BenchmarkSource 添加了 get_range 方法（[1815](https://github.com/stefan-jansen/zipline/issues/1815)）

+   增加了对管道中重新标记分类器的支持（[1833](https://github.com/stefan-jansen/zipline/issues/1833)）

### 实验性功能

警告

实验性功能可能会发生变化。

无

### 错误修复

+   通过使用整数除法而不是浮点除法，修复了`zipline.data.minute_bars`中的浮点除法问题（[1683](https://github.com/stefan-jansen/zipline/issues/1683)）

+   在`asof_date`上对`zipline.pipeline.loaders.blaze.core`中的数据进行排序，以解决时间戳冲突（[1710](https://github.com/stefan-jansen/zipline/issues/1710)）

+   将基准数据从雅虎换成了谷歌财经（[1812](https://github.com/stefan-jansen/zipline/issues/1812)）

+   黄金和白银期货合约只在特定月份交易（[1779](https://github.com/stefan-jansen/zipline/issues/1779)）

+   修复了使用时区感知时间时 TradingCalendar 初始化中的错误（[1802](https://github.com/stefan-jansen/zipline/issues/1802)）

+   修复了期货价格在四舍五入时的精度问题（[1788](https://github.com/stefan-jansen/zipline/issues/1788)）

### 性能

+   在获取前向填充收盘价时避免重复的递归调用（[1735](https://github.com/stefan-jansen/zipline/issues/1735)）

### 维护和重构

+   为调整模块添加 linter 建议（[1712](https://github.com/stefan-jansen/zipline/issues/1712)）

+   清理了 resample close 中的命名和逻辑（[1728](https://github.com/stefan-jansen/zipline/issues/1728)）

+   为多个连续期货使用 3 月季度周期（[1762](https://github.com/stefan-jansen/zipline/issues/1762)）

+   为 Transaction 对象使用更好的 repr（[1746](https://github.com/stefan-jansen/zipline/issues/1746)）

+   缩短 Asset 对象的 repr（[1786](https://github.com/stefan-jansen/zipline/issues/1786)）

+   移除了 empyrical 的信息比率的使用（[1854](https://github.com/stefan-jansen/zipline/issues/1854)）

### 构建

+   添加 Python 3.5 包（[1701](https://github.com/stefan-jansen/zipline/issues/1701)）

+   交换 conda-build 参数，这样我们就不必在每次 CI 构建时构建包（[1813](https://github.com/stefan-jansen/zipline/issues/1813)）

### 文档

+   添加了 Zipline 开发指南，供人们阅读如何为 Zipline 做出贡献（[1820](https://github.com/stefan-jansen/zipline/issues/1820)）

+   显示股票所需的交易所（[1731](https://github.com/stefan-jansen/zipline/issues/1731)）

+   更新了 Zipline 入门教程笔记本（[1707](https://github.com/stefan-jansen/zipline/issues/1707)）

+   包括 PipelineEngine、pipeline Term、Factors 等其他 pipeline 相关内容到文档中（[1826](https://github.com/stefan-jansen/zipline/issues/1826)）

### 杂项

+   使用 csv 市场数据与`run_algorithm`，这样我们就不必为测试下载数据（[1793](https://github.com/stefan-jansen/zipline/issues/1793)）

+   更新 Dockerfile 以使用 Python 3.5

## 发布 1.1.0

发布：

1.1.0

日期：

2017 年 3 月 10 日

此次发布旨在为 pandas 0.18 提供 Zipline 支持，以及多个错误修复、API 变更和许多性能改进。

### 增强功能

+   如果日期不在日历中，使分钟条形图读取捕获 NoDataOnDate 异常。之前，分钟条形图阅读器是前向填充的，但现在它为 OHLC 返回 nan，为 V 返回 0。（[1488](https://github.com/stefan-jansen/zipline/issues/1488)）

+   为`BcolzMinuteBarWriter`添加`truncate`方法（[1499](https://github.com/stefan-jansen/zipline/issues/1499)）

+   升级到 pandas 0.18.1 和 numpy 1.11.1（[1339](https://github.com/stefan-jansen/zipline/issues/1339)）

+   为 Pipeline 添加了收益估计季度加载器（[1396](https://github.com/stefan-jansen/zipline/issues/1396)）

+   创建了一个受限列表管理器，该管理器在实例化时接收有关受限 sid 的信息并将其存储在内存中（[1487](https://github.com/stefan-jansen/zipline/issues/1487)）

+   为`DataPortal`添加`last_available{session, minute}`参数（[1528](https://github.com/stefan-jansen/zipline/issues/1528)）

+   增加了`SpecificAssets`过滤器（[1530](https://github.com/stefan-jansen/zipline/issues/1530)）

+   增加了算法请求期货链当前合约的能力（[1529](https://github.com/stefan-jansen/zipline/issues/1529)）

+   在`DataPortal`和`OrderedContracts`的当前和支持方法中增加了`chain`字段（[1538](https://github.com/stefan-jansen/zipline/issues/1538)）

+   增加了连续期货的历史数据（[1539](https://github.com/stefan-jansen/zipline/issues/1539)）

+   增加了连续期货的调整历史数据（[1548](https://github.com/stefan-jansen/zipline/issues/1548)）

+   增加了考虑期货合约成交量的滚动风格，特别是针对连续期货（[1556](https://github.com/stefan-jansen/zipline/issues/1556)）

+   在非运行模拟中调用 Zipline API 函数时增加了更好的错误信息（[1593](https://github.com/stefan-jansen/zipline/issues/1593)）

+   增加了`MACDSignal()`、`MovingAverageConvergenceDivergenceSignal()`和`AnnualizedVolatility()`作为内置因子（[1588](https://github.com/stefan-jansen/zipline/issues/1588)）

+   允许在`attach_pipeline`中使用自定义日期块运行管道（[1617](https://github.com/stefan-jansen/zipline/issues/1617)）

+   在交易记录中增加了`order_batch`功能（[1596](https://github.com/stefan-jansen/zipline/issues/1596)）

+   增加了向量化查找符号功能（[1627](https://github.com/stefan-jansen/zipline/issues/1627)）

+   加强了 SlippageModel 类之间的等式比较（[1657](https://github.com/stefan-jansen/zipline/issues/1657)）

+   增加了用于缩尾结果的因子（[1696](https://github.com/stefan-jansen/zipline/issues/1696)）

### 错误修复

+   将`str`改为`string_types`以避免在类型检查 unicode 而不是 str 类型时出错（[1315](https://github.com/stefan-jansen/zipline/issues/1315)）

+   当未指定数据源时，算法默认使用 quantopian-quandl 数据包（[1479](https://github.com/stefan-jansen/zipline/issues/1479)）（[1374](https://github.com/stefan-jansen/zipline/issues/1374)）

+   在计算股息比率时捕获所有缺失数据异常（[1507](https://github.com/stefan-jansen/zipline/issues/1507)）

+   根据有序资产而不是集合创建调整。之前，调整是根据资产在集合中的位置而不是有序资产来估计的（[1547](https://github.com/stefan-jansen/zipline/issues/1547)）

+   修复了用户查询`asof_date`列时的 blaze 管道查询问题（[1608](https://github.com/stefan-jansen/zipline/issues/1608)）

+   应将日期时间转换为 UTC 格式。返回的 DataFrame 将整数转换为美国/东部时间戳，可能导致返回的日期变为前一天的日期（[1635](https://github.com/stefan-jansen/zipline/issues/1635)）

+   修复了`IchimokuKinkoHyo`因子的默认输入（[1638](https://github.com/stefan-jansen/zipline/issues/1638)）

### 性能改进

+   移除了`get_calendar('NYSE')`的调用，减少了 zipline 的导入时间，使 CLI 更加响应迅速且使用更少的内存（[1471](https://github.com/stefan-jansen/zipline/issues/1471)）

+   在不再需要时对管道术语进行引用计数和释放（[1484](https://github.com/stefan-jansen/zipline/issues/1484)）

+   最多可减少 75%的 minute_to_session_label 调用次数（[1492](https://github.com/stefan-jansen/zipline/issues/1492)）

+   加快了连续会话中分钟数的计数速度（[1497](https://github.com/stefan-jansen/zipline/issues/1497)）

+   移除/推迟了对大型索引的 get_loc 调用（[1504](https://github.com/stefan-jansen/zipline/issues/1504)）（[1503](https://github.com/stefan-jansen/zipline/issues/1503)）

+   在`calc_dividend_ratios`中用`get_indexer`替换了`get_loc`调用（[1510](https://github.com/stefan-jansen/zipline/issues/1510)）

+   加快了分钟到会话的采样速度（[1549](https://github.com/stefan-jansen/zipline/issues/1549)）

+   在`data.current`中增加了一些微优化（[1561](https://github.com/stefan-jansen/zipline/issues/1561)）

+   为管道初始工作区增加了优化（[1521](https://github.com/stefan-jansen/zipline/issues/1521)）

+   更多的内存节省（[1599](https://github.com/stefan-jansen/zipline/issues/1599)）

### 维护和重构

+   更新了杠杆 ETF 列表（[747](https://github.com/stefan-jansen/zipline/issues/747)）（[1434](https://github.com/stefan-jansen/zipline/issues/1434)）

+   为 Order 类的`__getitem__`增加了额外的字段（[1483](https://github.com/stefan-jansen/zipline/issues/1483)）

+   为分钟和会话阅读器增加了`BarReader`基类（[1486](https://github.com/stefan-jansen/zipline/issues/1486)）

+   移除了`future_chain` API 方法，将由`data.current_chain`替代（[1502](https://github.com/stefan-jansen/zipline/issues/1502)）

+   将 zipline 重新放回 blaze master（[1505](https://github.com/stefan-jansen/zipline/issues/1505)）

+   在 Dockerfile 中添加了 Tini，并设置了 numpy、pandas 和 scipy 的版本范围（[1514](https://github.com/stefan-jansen/zipline/issues/1514)）

+   弃用了`set_do_not_order_list`（[1487](https://github.com/stefan-jansen/zipline/issues/1487)）

+   使用`Timedelta`代替了`DateOffset`（[1487](https://github.com/stefan-jansen/zipline/issues/1487)）

+   更新并固定了更多的开发需求（[1642](https://github.com/stefan-jansen/zipline/issues/1642)）

### 构建

+   为 empyrical 增加了 numpy 的二进制依赖

+   从 Travis 中移除了旧的 numpy/pandas 版本（[1339](https://github.com/stefan-jansen/zipline/issues/1339)）

+   更新了 appveyor.yml 以适配新的 numpy 和 pandas 版本（[1339](https://github.com/stefan-jansen/zipline/issues/1339)）

+   降级到 scipy 0.17（[1339](https://github.com/stefan-jansen/zipline/issues/1339)）

+   将 empyrical 升级到 0.2.2

### 文档

+   更新了最新的 zipline 单元魔法的示例笔记本

+   增加了 ANACONDA_TOKEN 的指引（[1589](https://github.com/stefan-jansen/zipline/issues/1589)）

### 杂项

+   更改了 `zipline clean` 入口点中 `--before` 的短选项。新参数是 `-e`。旧参数 `-b` 与 `--bundle` 短选项冲突。([1625](https://github.com/stefan-jansen/zipline/issues/1625))

### 增强功能

+   如果日期不在日历中，分钟条形图读取会捕获 NoDataOnDate 异常。之前，分钟条形图读取器是向前填充的，但现在它为 OHLC 返回 nan，为 V 返回 0。([1488](https://github.com/stefan-jansen/zipline/issues/1488))

+   为 `BcolzMinuteBarWriter` 增加了 `truncate` 方法。([1499](https://github.com/stefan-jansen/zipline/issues/1499))

+   升级至 pandas 0.18.1 和 numpy 1.11.1。([1339](https://github.com/stefan-jansen/zipline/issues/1339))

+   为 Pipeline 增加了季度盈利预测加载器。([1396](https://github.com/stefan-jansen/zipline/issues/1396))

+   创建了一个受限列表管理器，它在实例化时接收有关受限 sid 的信息并存储在内存中。([1487](https://github.com/stefan-jansen/zipline/issues/1487))

+   在 `DataPortal` 中增加了 `last_available{session, minute}` 参数。([1528](https://github.com/stefan-jansen/zipline/issues/1528))

+   增加了 `SpecificAssets` 过滤器。([1530](https://github.com/stefan-jansen/zipline/issues/1530))

+   增加了算法请求未来链当前合约的能力。([1529](https://github.com/stefan-jansen/zipline/issues/1529))

+   在 `DataPortal` 和 `OrderedContracts` 的当前和支持方法中增加了 `chain` 字段。([1538](https://github.com/stefan-jansen/zipline/issues/1538))

+   增加了连续期货的历史记录。([1539](https://github.com/stefan-jansen/zipline/issues/1539))

+   为连续期货增加了调整后的历史记录。([1548](https://github.com/stefan-jansen/zipline/issues/1548))

+   增加了考虑未来合约成交量的滚动风格，特别针对连续期货。([1556](https://github.com/stefan-jansen/zipline/issues/1556))

+   在非运行模拟中调用 Zipline API 函数时，增加了更好的错误消息。([1593](https://github.com/stefan-jansen/zipline/issues/1593))

+   增加了 `MACDSignal()`、`MovingAverageConvergenceDivergenceSignal()` 和 `AnnualizedVolatility()` 作为内置因子。([1588](https://github.com/stefan-jansen/zipline/issues/1588))

+   允许在 `attach_pipeline` 中使用自定义日期块运行管道。([1617](https://github.com/stefan-jansen/zipline/issues/1617))

+   在交易记录中增加了 `order_batch`。([1596](https://github.com/stefan-jansen/zipline/issues/1596))

+   增加了矢量化的 lookup_symbol。([1627](https://github.com/stefan-jansen/zipline/issues/1627))

+   加强了 SlippageModel 类的相等比较。([1657](https://github.com/stefan-jansen/zipline/issues/1657))

+   增加了截断结果的因素。([1696](https://github.com/stefan-jansen/zipline/issues/1696))

### 错误修复

+   将 str 改为 string_types 以避免在检查 unicode 类型而非 str 类型时出现错误。([1315](https://github.com/stefan-jansen/zipline/issues/1315))

+   当未指定数据源时，算法默认使用 quantopian-quandl 包（[1479](https://github.com/stefan-jansen/zipline/issues/1479)）（[1374](https://github.com/stefan-jansen/zipline/issues/1374)）

+   在计算股息比率时捕获所有缺失数据的异常（[1507](https://github.com/stefan-jansen/zipline/issues/1507)）

+   根据有序资产而不是集合创建调整。之前，调整是根据资产在集合中恰好出现的位置而不是使用有序资产来创建的估计（[1547](https://github.com/stefan-jansen/zipline/issues/1547)）

+   修复了当用户查询`asof_date`列时对 blaze pipeline 查询的修复（[1608](https://github.com/stefan-jansen/zipline/issues/1608)）

+   日期时间应以 utc 转换。返回的 DataFrames 正在将 ints 转换为 US/Eastern 时间戳，可能会将返回的日期更改为前一天的日期（[1635](https://github.com/stefan-jansen/zipline/issues/1635)）

+   修复了`IchimokuKinkoHyo`因子的默认输入（[1638](https://github.com/stefan-jansen/zipline/issues/1638)）

### 性能

+   移除了`get_calendar('NYSE')`的调用，这减少了 zipline 的导入时间，使 CLI 更加响应迅速且使用更少的内存（[1471](https://github.com/stefan-jansen/zipline/issues/1471)）

+   在不再需要时对 pipeline 术语进行引用计数和释放（[1484](https://github.com/stefan-jansen/zipline/issues/1484)）

+   最多节省了 75%的 minute_to_session_label 调用（[1492](https://github.com/stefan-jansen/zipline/issues/1492)）

+   加快了连续会话中分钟数的计数（[1497](https://github.com/stefan-jansen/zipline/issues/1497)）

+   移除或推迟对大型索引的 get_loc 调用（[1504](https://github.com/stefan-jansen/zipline/issues/1504)）（[1503](https://github.com/stefan-jansen/zipline/issues/1503)）

+   在`calc_dividend_ratios`中用`get_indexer`替换`get_loc`调用（[1510](https://github.com/stefan-jansen/zipline/issues/1510)）

+   加快了分钟到会话采样（[1549](https://github.com/stefan-jansen/zipline/issues/1549)）

+   在`data.current`中添加了一些微优化（[1561](https://github.com/stefan-jansen/zipline/issues/1561)）

+   为 pipeline 的初始工作区添加了优化（[1521](https://github.com/stefan-jansen/zipline/issues/1521)）

+   更多的内存节省（[1599](https://github.com/stefan-jansen/zipline/issues/1599)）

### 维护和重构

+   更新了杠杆 ETF 列表（[747](https://github.com/stefan-jansen/zipline/issues/747)）（[1434](https://github.com/stefan-jansen/zipline/issues/1434)）

+   为 Order 类的`__getitem__`添加了其他字段（[1483](https://github.com/stefan-jansen/zipline/issues/1483)）

+   为分钟和会话阅读器添加了`BarReader`基类（[1486](https://github.com/stefan-jansen/zipline/issues/1486)）

+   移除了`future_chain` API 方法，将由`data.current_chain`替换（[1502](https://github.com/stefan-jansen/zipline/issues/1502)）

+   将 zipline 重新放回 blaze 主分支（[1505](https://github.com/stefan-jansen/zipline/issues/1505)）

+   在 Dockerfile 中添加了 Tini，并设置了 numpy、pandas 和 scipy 的版本范围（[1514](https://github.com/stefan-jansen/zipline/issues/1514)）

+   弃用了`set_do_not_order_list`（[1487](https://github.com/stefan-jansen/zipline/issues/1487)）

+   使用`Timedelta`代替`DateOffset`（[1487](https://github.com/stefan-jansen/zipline/issues/1487)）

+   更新并固定更多的开发要求（[1642](https://github.com/stefan-jansen/zipline/issues/1642)）

### 构建

+   为 empyrical 添加了 numpy 的二进制依赖

+   从 Travis 中移除了旧的 numpy/pandas 版本（[1339](https://github.com/stefan-jansen/zipline/issues/1339)）

+   更新了 appveyor.yml 以适应新的 numpy 和 pandas（[1339](https://github.com/stefan-jansen/zipline/issues/1339)）

+   降级到 scipy 0.17（[1339](https://github.com/stefan-jansen/zipline/issues/1339)）

+   将 empyrical 升级到 0.2.2

### 文档

+   更新了最新的 zipline 单元魔法示例笔记本

+   添加了 ANACONDA_TOKEN 指令（[1589](https://github.com/stefan-jansen/zipline/issues/1589)）

### 杂项

+   在`zipline clean`入口点中更改了`--before`的短选项。新的参数是`-e`。旧参数`-b`与`--bundle`短选项冲突（[1625](https://github.com/stefan-jansen/zipline/issues/1625)）。

## 发布 1.0.2

发布：

1.0.2

日期：

2016 年 9 月 8 日

### 增强功能

+   为 blaze 核心加载器添加了前向填充检查点表。这允许加载器更有效地通过限制必须搜索的较低日期来查询数据时进行前向填充。检查点应该应用新的增量（[1276](https://github.com/stefan-jansen/zipline/issues/1276)）。

+   更新了 VagrantFile 以包含所有开发要求并使用更新的镜像（[1310](https://github.com/stefan-jansen/zipline/issues/1310)）。

+   允许在两个 2D 因子之间计算相关性和回归，通过进行资产级别的计算（[1307](https://github.com/stefan-jansen/zipline/issues/1307)）。

+   过滤器默认已设置为窗口安全。现在它们可以作为参数传递给其他过滤器、因子和分类器（[1338](https://github.com/stefan-jansen/zipline/issues/1338)）。

+   为`rank()`、`top()`和`bottom()`添加了可选的`groupby`参数（[1349](https://github.com/stefan-jansen/zipline/issues/1349)）。

+   添加了新的管道过滤器，`All` 和 `Any`，它接受另一个过滤器，并返回 True，如果资产在前`window_length`天内为任何/所有天产生 True（[1358](https://github.com/stefan-jansen/zipline/issues/1358)）。

+   添加了新的管道过滤器`AtLeastN`，它接受另一个过滤器和一个整数 N，如果资产在之前的`window_length`天内产生 True 的天数为 N 或更多，则返回 True。([1367](https://github.com/stefan-jansen/zipline/issues/1367))

+   使用外部库 empyrical 进行风险计算。Empyrical 统一了 pyfolio 和 zipline 之间的风险度量计算。Empyrical 为自定义频率的回报添加了自定义年化选项。([855](https://github.com/stefan-jansen/zipline/issues/855))

+   添加 Aroon 因子。([1258](https://github.com/stefan-jansen/zipline/issues/1258))

+   添加快速随机振荡器因子。([1255](https://github.com/stefan-jansen/zipline/issues/1255))

+   添加 Dockerfile。([1254](https://github.com/stefan-jansen/zipline/issues/1254))

+   新增支持跨午夜时段的交易日历，例如期货交易的 24 小时 6:01PM-6:00PM 时段。zipline.utils.tradingcalendar 现已弃用。([1138](https://github.com/stefan-jansen/zipline/issues/1138)) ([1312](https://github.com/stefan-jansen/zipline/issues/1312))

+   允许从因子/过滤器/分类器中提取单个列。([1267](https://github.com/stefan-jansen/zipline/issues/1267))

+   提供 Ichimoku 云因子。([1263](https://github.com/stefan-jansen/zipline/issues/1263))

+   允许在管道术语上设置默认参数。([1263](https://github.com/stefan-jansen/zipline/issues/1263))

+   提供变化率百分比因子。([1324](https://github.com/stefan-jansen/zipline/issues/1324))

+   提供线性加权移动平均因子。([1325](https://github.com/stefan-jansen/zipline/issues/1325))

+   添加`NotNullFilter`。([1345](https://github.com/stefan-jansen/zipline/issues/1345))

+   允许通过目标值定义资本变动。([1337](https://github.com/stefan-jansen/zipline/issues/1337))

+   添加`TrueRange`因子。([1348](https://github.com/stefan-jansen/zipline/issues/1348))

+   为`assets.db`添加时点查询功能。([1361](https://github.com/stefan-jansen/zipline/issues/1361))

+   使`can_trade`意识到资产的交易所。([1346](https://github.com/stefan-jansen/zipline/issues/1346))

+   为所有可计算术语添加`downsample`方法。([1394](https://github.com/stefan-jansen/zipline/issues/1394))

+   添加 QuantopianUSFuturesCalendar。([1414](https://github.com/stefan-jansen/zipline/issues/1414))

+   允许发布旧版本的`assets.db`。([1430](https://github.com/stefan-jansen/zipline/issues/1430))

+   为期货交易日历启用`schedule_function`。([1442](https://github.com/stefan-jansen/zipline/issues/1442))

+   不允许长度为 1 的回归。([1466](https://github.com/stefan-jansen/zipline/issues/1466))

### 实验性

+   增加对混合期货和股票历史窗口的支持，并通过数据门户启用其他期货数据访问。([1435](https://github.com/stefan-jansen/zipline/issues/1435)) ([1432](https://github.com/stefan-jansen/zipline/issues/1432))

### 错误修复

+   更改内置因子`AverageDollarVolume`以将缺失的收盘价或成交量值视为 0。以前，在平均之前简单地丢弃了 NaNs，给剩余的值赋予了过多的权重([1309](https://github.com/stefan-jansen/zipline/issues/1309))。

+   从夏普比率计算中移除无风险利率。现在，该比率是调整后的回报率波动性的平均值。([853](https://github.com/stefan-jansen/zipline/issues/853))

+   当所需回报率为零时，Sortino 比率将返回计算结果而非 np.nan。现在，该比率返回的是下行风险调整后的回报率的平均值。通过将 mar 转换为 downside_risk，修正了 API 标签错误的问题。([747](https://github.com/stefan-jansen/zipline/issues/747))

+   下行风险现在返回下行差分平方的平均值的平方根。([747](https://github.com/stefan-jansen/zipline/issues/747))

+   信息比率更新为返回风险调整后的回报率的标准差的风险调整后的回报率的平均值。([1322](https://github.com/stefan-jansen/zipline/issues/1322))

+   阿尔法和夏普比率现在已年化。([1322](https://github.com/stefan-jansen/zipline/issues/1322))

+   修复每日条形图`first_trading_day`属性在读写时的单位问题。([1245](https://github.com/stefan-jansen/zipline/issues/1245))

+   当缺少可选分派模块时，不再引发 NameError。([1246](https://github.com/stefan-jansen/zipline/issues/1246))

+   当提供时间规则但没有日期规则时，将`schedule_function`参数视为时间规则。([1221](https://github.com/stefan-jansen/zipline/issues/1221))

+   在 schedule 函数中保护交易日的开始和结束边界条件。([1226](https://github.com/stefan-jansen/zipline/issues/1226))

+   在使用频率为 1d 的历史数据时，将调整应用于前一天。([1256](https://github.com/stefan-jansen/zipline/issues/1256))

+   在尝试访问不存在的列之前，快速失败于无效的管道列。([1280](https://github.com/stefan-jansen/zipline/issues/1280))

+   修复`AverageDollarVolume`处理 NaN 的问题。([1309](https://github.com/stefan-jansen/zipline/issues/1309))

### 性能

+   对 blaze 核心加载器的性能改进。([1227](https://github.com/stefan-jansen/zipline/issues/1227))

+   允许并发 blaze 查询。([1323](https://github.com/stefan-jansen/zipline/issues/1323))

+   防止由于缺少 bcolz 分钟数据而进行重复的不必要查找。([1451](https://github.com/stefan-jansen/zipline/issues/1451))

+   缓存未来链查找。([1455](https://github.com/stefan-jansen/zipline/issues/1455))

### 维护和重构

+   移除剩余的`add_history`提及。([1287](https://github.com/stefan-jansen/zipline/issues/1287))

### 文档

### 测试

+   添加测试装置，该装置从分钟定价数据装置中获取每日定价数据。([1243](https://github.com/stefan-jansen/zipline/issues/1243))

### 数据格式更改

+   `BcolzDailyBarReader`和`BcolzDailyBarWriter`使用交易日历实例，而不是序列化为`JSON`的交易日。([1330](https://github.com/stefan-jansen/zipline/issues/1330))

+   将`assets.db`的格式更改为支持按时间点查找。([1361](https://github.com/stefan-jansen/zipline/issues/1361))

+   更改`BcolzMinuteBarReader`和`BcolzMinuteBarWriter`以支持不同的最小变动价位。([1428](https://github.com/stefan-jansen/zipline/issues/1428))

### 增强功能

+   为 blaze 核心加载器添加前向填充检查点表。这允许加载器通过限制必须搜索数据的较低日期来更有效地前向填充数据。检查点应该应用新的增量([1276](https://github.com/stefan-jansen/zipline/issues/1276))。

+   更新 VagrantFile 以包含所有开发需求并使用更新的镜像([1310](https://github.com/stefan-jansen/zipline/issues/1310))。

+   允许通过逐资产计算来计算两个 2D 因子之间的相关性和回归([1307](https://github.com/stefan-jansen/zipline/issues/1307))。

+   过滤器默认设置为 window_safe。现在它们可以作为参数传递给其他过滤器、因子和分类器([1338](https://github.com/stefan-jansen/zipline/issues/1338))。

+   为`rank()`、`top()`和`bottom()`添加可选的`groupby`参数。([1349](https://github.com/stefan-jansen/zipline/issues/1349))

+   添加新的管道过滤器，`All`和`Any`，它接受另一个过滤器，如果资产在前`window_length`天内对任何/所有天产生 True，则返回 True ([1358](https://github.com/stefan-jansen/zipline/issues/1358))。

+   添加新的管道过滤器`AtLeastN`，它接受另一个过滤器和一个整数 N，如果资产在前`window_length`天内产生 True 的天数为 N 或更多，则返回 True ([1367](https://github.com/stefan-jansen/zipline/issues/1367))。

+   使用外部库 empyrical 进行风险计算。Empyrical 统一了 pyfolio 和 zipline 之间的风险度量计算。Empyrical 为自定义频率的回报添加了自定义年化选项。([855](https://github.com/stefan-jansen/zipline/issues/855))

+   添加 Aroon 因子。([1258](https://github.com/stefan-jansen/zipline/issues/1258))

+   添加快速随机振荡器因子。([1255](https://github.com/stefan-jansen/zipline/issues/1255))

+   添加一个 Dockerfile。([1254](https://github.com/stefan-jansen/zipline/issues/1254))

+   新的交易日历支持跨越午夜的会话，例如期货交易的 24 小时 6:01PM-6:00PM 会话。zipline.utils.tradingcalendar 现已弃用。([1138](https://github.com/stefan-jansen/zipline/issues/1138)) ([1312](https://github.com/stefan-jansen/zipline/issues/1312))

+   允许从因子/筛选器/分类器中提取单个列。([1267](https://github.com/stefan-jansen/zipline/issues/1267))

+   提供 Ichimoku Cloud 因子 ([1263](https://github.com/stefan-jansen/zipline/issues/1263))

+   允许在管道项上使用默认参数。([1263](https://github.com/stefan-jansen/zipline/issues/1263))

+   提供变化率百分比因子。([1324](https://github.com/stefan-jansen/zipline/issues/1324))

+   提供线性加权移动平均因子。([1325](https://github.com/stefan-jansen/zipline/issues/1325))

+   添加`NotNullFilter`。([1345](https://github.com/stefan-jansen/zipline/issues/1345))

+   允许通过目标值定义资本变动。([1337](https://github.com/stefan-jansen/zipline/issues/1337))

+   添加`TrueRange`因子。([1348](https://github.com/stefan-jansen/zipline/issues/1348))

+   为`assets.db`添加实时查找功能。([1361](https://github.com/stefan-jansen/zipline/issues/1361))

+   使`can_trade`意识到资产的交易所。([1346](https://github.com/stefan-jansen/zipline/issues/1346))

+   为所有可计算项添加`downsample`方法。([1394](https://github.com/stefan-jansen/zipline/issues/1394))

+   添加 QuantopianUSFuturesCalendar。([1414](https://github.com/stefan-jansen/zipline/issues/1414))

+   启用旧版`assets.db`版本的发布。([1430](https://github.com/stefan-jansen/zipline/issues/1430))

+   为期货交易日历启用`schedule_function`。([1442](https://github.com/stefan-jansen/zipline/issues/1442))

+   不允许长度为 1 的回归。([1466](https://github.com/stefan-jansen/zipline/issues/1466))

### 实验性功能

+   添加对混合的未来和股票历史窗口的支持，并通过数据门户启用其他未来数据访问。([1435](https://github.com/stefan-jansen/zipline/issues/1435)) ([1432](https://github.com/stefan-jansen/zipline/issues/1432))

### 错误修复

+   将内置因子`AverageDollarVolume`的更改处理缺失的收盘价或成交量值为 0。以前，NaN 值在平均之前被简单丢弃，导致剩余值权重过大 ([1309](https://github.com/stefan-jansen/zipline/issues/1309))。

+   从夏普比率计算中移除无风险利率。现在该比率是风险调整后回报的平均值除以调整后回报的波动性。([853](https://github.com/stefan-jansen/zipline/issues/853))

+   当所需回报等于零时，索提诺比率将返回计算结果而不是 np.nan。现在，该比率返回风险调整后的回报平均值除以下行风险。通过将 mar 转换为 downside_risk 来修复错误标记的 API。([747](https://github.com/stefan-jansen/zipline/issues/747))

+   下行风险现在返回下行差平方的平均值的平方根。([747](https://github.com/stefan-jansen/zipline/issues/747))

+   信息比率更新为返回风险调整后的回报平均值除以风险调整后的回报标准差。([1322](https://github.com/stefan-jansen/zipline/issues/1322))

+   阿尔法和夏普比率现在已年化。([1322](https://github.com/stefan-jansen/zipline/issues/1322))

+   在读写每日栏的`first_trading_day`属性时修复单位。([1245](https://github.com/stefan-jansen/zipline/issues/1245))

+   当缺少可选分派模块时，不再导致 NameError。([1246](https://github.com/stefan-jansen/zipline/issues/1246))

+   当提供时间规则而没有日期规则时，将`schedule_function`参数视为时间规则。([1221](https://github.com/stefan-jansen/zipline/issues/1221))

+   在 schedule 函数中保护交易日的开始和结束边界条件。([1226](https://github.com/stefan-jansen/zipline/issues/1226))

+   使用频率为 1d 的历史记录时，将调整应用于前一天。([1256](https://github.com/stefan-jansen/zipline/issues/1256))

+   在尝试访问不存在的列之前，快速失败无效的管道列。([1280](https://github.com/stefan-jansen/zipline/issues/1280))

+   修复`AverageDollarVolume`的 NaN 处理。([1309](https://github.com/stefan-jansen/zipline/issues/1309))

### 性能

+   对 blaze 核心加载器的性能改进。([1227](https://github.com/stefan-jansen/zipline/issues/1227))

+   允许并发 blaze 查询。([1323](https://github.com/stefan-jansen/zipline/issues/1323))

+   防止缺少领先 bcolz 分钟数据进行重复的不必要查找。([1451](https://github.com/stefan-jansen/zipline/issues/1451))

+   缓存未来链查找。([1455](https://github.com/stefan-jansen/zipline/issues/1455))

### 维护和重构

+   删除了剩余的`add_history`提及。([1287](https://github.com/stefan-jansen/zipline/issues/1287))

### 文档

### 测试

+   添加测试夹具，该夹具从分钟定价数据夹具中获取每日定价数据。([1243](https://github.com/stefan-jansen/zipline/issues/1243))

### 数据格式更改

+   `BcolzDailyBarReader`和`BcolzDailyBarWriter`使用交易日历实例，而不是交易日期序列化为`JSON`。([1330](https://github.com/stefan-jansen/zipline/issues/1330))

+   将`assets.db`的格式更改为支持点对点查找。([1361](https://github.com/stefan-jansen/zipline/issues/1361))

+   将`BcolzMinuteBarReader`和`BcolzMinuteBarWriter`更改为支持不同的刻度大小。([1428](https://github.com/stefan-jansen/zipline/issues/1428))

## 发布 1.0.1

发布：

1.0.1

日期：

2016 年 5 月 27 日

这是从 1.0.0 版本开始的一个小错误修复版本，包括少量错误修复和文档改进。

### 增强功能

+   增加了对用户定义的佣金模型的支持。有关实现佣金模型的更多详细信息，请参阅`zipline.finance.commission.CommissionModel`类（[1213](https://github.com/stefan-jansen/zipline/issues/1213)）。

+   增加了对 Blaze 支持的 Pipeline 数据集中非浮点列的支持（[1201](https://github.com/stefan-jansen/zipline/issues/1201)）。

+   添加了`zipline.pipeline.slice.Slice`，这是一种新的管道术语，旨在从另一个术语中提取单个列。可以通过对术语进行索引并按资产键来创建切片（[1267](https://github.com/stefan-jansen/zipline/issues/1267)）。

### 错误修复

+   修复了一个错误，该错误导致 Pipeline 加载器未被`zipline.run_algorithm()`正确初始化。这也影响了从 CLI 调用`zipline run`。

+   修复了一个导致`%%zipline` IPython 单元格魔法失败的错误（[533233fae43c7ff74abfb0044f046978817cb4e4](https://github.com/stefan-jansen/zipline/commit/533233fae43c7ff74abfb0044f046978817cb4e4)）。

+   修复了在`PerTrade`佣金模型中的一个错误，该错误导致佣金被错误地应用于订单的每个部分填充，而不是订单本身，导致在下单大额订单时算法被收取过多的佣金。

    `PerTrade`现在正确地按订单基础应用佣金（[1213](https://github.com/stefan-jansen/zipline/issues/1213)）。

+   在定义多个输出的`CustomFactors`上访问属性时，现在将正确返回输出切片，即使输出也是`Factor`方法的名称（[1214](https://github.com/stefan-jansen/zipline/issues/1214)）。

+   用`pandas_datareader`替换了已弃用的`pandas.io.data`的使用（[1218](https://github.com/stefan-jansen/zipline/issues/1218)）。

+   修复了一个问题，即`zipline.api`的`.pyi`存根文件意外地从 PyPI 源代码分发中排除。Conda 用户应该不受影响（[1230](https://github.com/stefan-jansen/zipline/issues/1230)）。

### 文档

+   新增了一个示例，`zipline.examples.momentum_pipeline`，该示例展示了 Pipeline API 的使用（[1230](https://github.com/stefan-jansen/zipline/issues/1230)）。

### 增强功能

+   添加了对用户定义的佣金模型的支持。有关实现佣金模型的更多详细信息，请参阅`zipline.finance.commission.CommissionModel`类（[1213](https://github.com/stefan-jansen/zipline/issues/1213)）。

+   为 Blaze 支持的 Pipeline 数据集添加了对非浮点列的支持（[1201](https://github.com/stefan-jansen/zipline/issues/1201)）。

+   添加了`zipline.pipeline.slice.Slice`，这是一种新的 Pipeline 术语，旨在从另一个术语中提取单个列。可以通过对术语进行索引并按资产键来创建切片（[1267](https://github.com/stefan-jansen/zipline/issues/1267)）。

### 错误修复

+   修复了一个错误，该错误导致 Pipeline 加载器未被`zipline.run_algorithm()`正确初始化。这也影响了从 CLI 调用`zipline run`。

+   修复了一个导致`%%zipline` IPython 单元格魔法失败的错误（[533233fae43c7ff74abfb0044f046978817cb4e4](https://github.com/stefan-jansen/zipline/commit/533233fae43c7ff74abfb0044f046978817cb4e4)）。

+   修复了在`PerTrade`佣金模型中的一个错误，该错误导致佣金被错误地应用于订单的每个部分填充，而不是订单本身，导致在下单大额订单时算法被收取过多的佣金。

    `PerTrade`现在正确地按订单收取佣金（[1213](https://github.com/stefan-jansen/zipline/issues/1213)）。

+   在定义多个输出的`CustomFactors`上的属性访问现在将正确返回输出切片，即使输出也是`Factor`方法的名称（[1214](https://github.com/stefan-jansen/zipline/issues/1214)）。

+   将已弃用的`pandas.io.data`替换为`pandas_datareader`（[1218](https://github.com/stefan-jansen/zipline/issues/1218)）。

+   修复了一个问题，即`.pyi`存根文件对于`zipline.api`被意外地从 PyPI 源分发中排除。使用 Conda 的用户应该不受影响（[1230](https://github.com/stefan-jansen/zipline/issues/1230)）。

### 文档

+   添加了一个新示例，`zipline.examples.momentum_pipeline`，该示例使用了 Pipeline API（[1230](https://github.com/stefan-jansen/zipline/issues/1230)）。

## 发布 1.0.0

发布：

1.0.0

日期：

2016 年 5 月 19 日

### 亮点

#### Zipline 1.0 重写（[1105](https://github.com/stefan-jansen/zipline/issues/1105)）

我们对 Zipline 及其基本概念进行了大量重写，以提高运行时性能。同时，我们引入了几个新的 API。

在较高层次上，Zipline 早期版本的模拟从多个数据源的复用流中提取数据，这些数据源通过 heapq 合并。这个数据流被输入到主模拟循环中，推动时钟前进。由于对读取所有数据的强烈依赖，优化模拟性能变得困难，因为我们获取的数据量与算法实际使用的数据量之间没有联系。

现在，我们只在算法需要数据时才获取数据。一个新类，`DataPortal`，将数据请求分派到各种数据源，并返回请求的值。这使得模拟的运行时间更紧密地与算法的复杂性而不是数据源提供的资产数量成比例。

不再是数据流驱动时钟，现在模拟通过预先计算的一组日或分钟时间戳进行迭代。时间戳由`MinuteSimulationClock`和`DailySimulationClock`发出，并由`transform()`中的主循环消费。

我们已弃用`data[sid(N)]`和`history` API，用`BarData`对象上的几个方法替换它们：`current()`，`history()`，`can_trade()`，和`is_stale()`。旧的 API 将继续工作，但现在会发出弃用警告。

现在可以将调整源传递给`DataPortal`，当我们回顾数据时，我们将对定价数据应用调整。执行和呈现给算法的数据的当前价格和成交量是资产的实际交易价值。

#### 新入口点（[1173](https://github.com/stefan-jansen/zipline/issues/1173) 和 [1178](https://github.com/stefan-jansen/zipline/issues/1178)）

为了更容易使用 Zipline，我们更新了回测的入口点。现在支持运行回测的三种方式是：

1.  `zipline.run_algo()`

1.  `$ zipline run`

1.  `%zipline`（IPython 魔法命令）

#### 数据包（[1173](https://github.com/stefan-jansen/zipline/issues/1173) 和 [1178](https://github.com/stefan-jansen/zipline/issues/1178)）

1.0.0 引入了数据包。数据包是一组应该预先加载并在以后用于运行回测的数据。这允许用户不需要每次运行算法时都指定他们感兴趣的标记。这也允许我们在运行之间缓存数据。

默认情况下，将使用 `quantopian-quandl` 包，该包从 Quantopian 的 quandl [WIKI 数据集](https://www.quandl.com/data/WIKI)镜像中提取数据。可以通过 `zipline.data.bundles.register()` 注册新包，例如：

```py
@zipline.data.bundles.register('my-new-bundle')
def my_new_bundle_ingest(environ,
                         asset_db_writer,
                         minute_bar_writer,
                         daily_bar_writer,
                         adjustment_writer,
                         calendar,
                         cache,
                         show_progress):
    ... 
```

该函数应检索所需数据，然后使用已传递的写入器将该数据写入磁盘上的位置，以便 zipline 稍后可以找到。

通过将名称作为 `-b / --bundle` 参数传递给 `$ zipline run` 或作为 `zipline.run_algorithm()` 的 `bundle` 参数，可以在回测中使用此数据。

有关更多信息，请参阅 Data 了解更多信息。

#### 流水线中的字符串支持 ([1174](https://github.com/stefan-jansen/zipline/issues/1174))

增加了对流水线中字符串数据的支持。`zipline.pipeline.data.Column` 现在接受 `object` 作为数据类型，表示该列的加载器应发出实验性新 `LabelArray` 类的窗口迭代器。

还添加了几个新的 `Classifier` 方法，用于构建基于字符串操作的 `Filter` 实例。新方法包括：

> +   `element_of()`
> +   
> +   `startswith()`
> +   
> +   `endswith()`
> +   
> +   `has_substring()`
> +   
> +   `matches()`
> +   
> `element_of` 对所有分类器都进行了定义。其余方法仅对字符串数据类型的分类器进行了定义。

### 增强功能

+   使数据加载类具有更一致的接口。这包括股票条形写入器、调整写入器和资产数据库写入器。新接口是在构造时传递要写入的资源，稍后将数据提供给写入方法，作为数据帧或一些数据帧的迭代器。这种模型允许我们将这些写入器对象作为其他类和函数消耗的资源传递 ([1109](https://github.com/stefan-jansen/zipline/issues/1109) 和 [1149](https://github.com/stefan-jansen/zipline/issues/1149))。

+   为 `zipline.pipeline.CustomFactor` 添加了掩码。自定义因子现在可以在实例化时传递一个过滤器。这告诉因子仅在过滤器返回 True 的股票上计算，而不是始终在整个股票宇宙上计算。([1095](https://github.com/stefan-jansen/zipline/issues/1095))

+   增加了`zipline.utils.cache.ExpiringCache`。这是一个缓存，它将条目包装在`zipline.utils.cache.CachedObject`中，该缓存根据 get 方法提供的 dt 管理条目的过期。（[1130](https://github.com/stefan-jansen/zipline/issues/1130)）

+   实现了`zipline.pipeline.factors.RecarrayField`，这是一种新的管道术语，设计用于自定义因子具有多个输出时的输出类型。（[1119](https://github.com/stefan-jansen/zipline/issues/1119)）

+   在`zipline.pipeline.CustomFactor`中增加了可选的 outputs 参数。现在，自定义因子能够计算并返回多个输出，每个输出本身都是一个因子。（[1119](https://github.com/stefan-jansen/zipline/issues/1119)）

+   增加了对字符串类型数据管道列的支持。这些列的加载器在遍历时应生成`zipline.lib.labelarray.LabelArray`实例。在字符串列上调用`latest()`将产生一个字符串类型的`zipline.pipeline.Classifier`。（[1174](https://github.com/stefan-jansen/zipline/issues/1174)）

+   增加了将分类器转换为过滤器的多种方法。

    新增方法包括：- `element_of()` - `startswith()` - `endswith()` - `has_substring()` - `matches()`

    `element_of` 定义了所有分类器。其余方法仅对字符串定义。（[1174](https://github.com/stefan-jansen/zipline/issues/1174)）

+   增加了`BollingerBands`因子。该因子实现了布林带技术指标：[`en.wikipedia.org/wiki/Bollinger_Bands`](https://en.wikipedia.org/wiki/Bollinger_Bands)（[1199](https://github.com/stefan-jansen/zipline/issues/1199)）。

+   将 Fetcher 从 Quantopian 内部代码迁移到 Zipline 中（[1105](https://github.com/stefan-jansen/zipline/issues/1105)）。

+   增加了新的内置因子，`RollingPearsonOfReturns`，`RollingSpearmanOfReturns`和`RollingLinearRegressionOfReturns`）

### 实验性功能

警告

实验性功能可能会发生变化。

+   新增了`zipline.lib.labelarray.LabelArray`类，用于高效地表示和计算 numpy 中的字符串数据。该类在概念上类似于[`pandas.Categorical`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Categorical.html#pandas.Categorical "(in pandas v2.0.3)")，它将字符串数组表示为索引数组，这些索引指向一个（较小的）唯一字符串值数组。([1174](https://github.com/stefan-jansen/zipline/issues/1174))

### 错误修复

无

### 性能

无

### 维护和重构

无

### 构建

无

### 文档

+   更新了 API 方法的文档 ([1188](https://github.com/stefan-jansen/zipline/issues/1188))。

+   更新了发布流程，提到文档应该使用 python 3 构建 ([1188](https://github.com/stefan-jansen/zipline/issues/1188))。

### 杂项

+   Zipline 现在为`zipline.api`模块提供了一个[存根文件](https://www.python.org/dev/peps/pep-0484/#stub-files)。该模块通常是动态创建的，因此存根文件为可以消费它的实用程序提供了一些静态信息，例如 PyCharm ([1208](https://github.com/stefan-jansen/zipline/issues/1208))。

### 亮点

#### Zipline 1.0 重写 ([1105](https://github.com/stefan-jansen/zipline/issues/1105))

我们对 Zipline 及其基本概念进行了大量重写，以提高运行时性能。同时，我们还引入了几个新的 API。

从高层次来看，早期版本的 Zipline 模拟从多路复用的数据源流中提取数据，这些数据源通过 heapq 合并。这个流被送入主模拟循环，推动时钟前进。这种对读取所有数据的强烈依赖使得优化模拟性能变得困难，因为我们在获取的数据量和算法实际使用的数据量之间没有联系。

现在，我们只在算法需要时才获取数据。一个新类，`DataPortal`，将数据请求分派到各种数据源，并返回请求的值。这使得模拟的运行时间更紧密地与算法的复杂性而不是数据源提供的资产数量成比例。

不再是数据流驱动时钟，现在模拟迭代通过一个预先计算的日或分钟时间戳集合。时间戳由`MinuteSimulationClock`和`DailySimulationClock`发出，并由`transform()`中的主循环消费。

我们已弃用`data[sid(N)]`和`history` API，用`BarData`对象上的几个方法取而代之：`current()`，`history()`，`can_trade()`，和`is_stale()`。旧的 API 目前仍可使用，但会发出弃用警告。

现在可以将调整源传递给`DataPortal`，当我们回顾数据时，我们将对定价数据应用调整。算法在 data.current 中接收到的执行价格和交易量是资产的实际交易价值。

#### 新入口点（[1173](https://github.com/stefan-jansen/zipline/issues/1173)和[1178](https://github.com/stefan-jansen/zipline/issues/1178)）

为了使使用 zipline 更加容易，我们更新了回测的入口点。现在支持的三种运行回测的方式是：

1.  `zipline.run_algo()`

1.  `$ zipline run`

1.  `%zipline`（IPython magic）

#### 数据包（[1173](https://github.com/stefan-jansen/zipline/issues/1173)和[1178](https://github.com/stefan-jansen/zipline/issues/1178)）

1.0.0 引入了数据包。数据包是应预加载并用于运行回测的一组数据。这允许用户无需每次运行算法时都指定他们感兴趣的符号。这也允许我们在运行之间缓存数据。

默认情况下，将使用`quantopian-quandl`包，该包从 Quantopian 的 quandl [WIKI 数据集](https://www.quandl.com/data/WIKI)镜像中提取数据。可以使用`zipline.data.bundles.register()`注册新包，例如：

```py
@zipline.data.bundles.register('my-new-bundle')
def my_new_bundle_ingest(environ,
                         asset_db_writer,
                         minute_bar_writer,
                         daily_bar_writer,
                         adjustment_writer,
                         calendar,
                         cache,
                         show_progress):
    ... 
```

此函数应检索所需数据，然后使用已传递的写入器将该数据写入磁盘上的位置，以便 zipline 稍后可以找到。

通过将名称作为`-b / --bundle`参数传递给`$ zipline run`或作为`zipline.run_algorithm()`的`bundle`参数，可以在回测中使用此数据。

如需了解更多信息，请参阅数据。

#### 流水线中的字符串支持（[1174](https://github.com/stefan-jansen/zipline/issues/1174)）

增加了对 Pipeline 中字符串数据的支持。`zipline.pipeline.data.Column`现在接受`object`作为 dtype，这意味着该列的加载器应该发出实验性的新`LabelArray`类的窗口迭代器。

还添加了几个新的`分类器`方法，用于构建基于字符串操作的`过滤器`实例。新方法包括：

> +   `element_of()`
> +   
> +   `startswith()`
> +   
> +   `endswith()`
> +   
> +   `has_substring()`
> +   
> +   `matches()`
> +   
> `element_of` 为所有分类器定义。剩余的方法仅对字符串类型的分类器定义。

#### Zipline 1.0 重写 ([1105](https://github.com/stefan-jansen/zipline/issues/1105))

我们对 Zipline 及其基本概念进行了大量重写，以提高运行时性能。同时，我们引入了几个新的 API。

从高层次来看，早期版本的 Zipline 模拟从多路复用的数据源流中提取数据，这些数据源通过 heapq 合并。这个流被送入主模拟循环，推动时钟前进。这种对读取所有数据的强烈依赖使得优化模拟性能变得困难，因为我们获取的数据量与算法实际使用的数据量之间没有联系。

现在，我们只在算法需要时获取数据。一个新的类，`数据门户`，将数据请求分发到各种数据源并返回请求的值。这使得模拟运行的时长更紧密地与算法的复杂性而不是数据源提供的资产数量成比例。

现在，模拟不再由数据流驱动时钟，而是迭代通过一组预先计算的日或分钟时间戳。这些时间戳由`MinuteSimulationClock`和`DailySimulationClock`发出，并由`transform()`中的主循环消费。

我们退役了`data[sid(N)]`和`history` API，用`BarData`对象上的几个方法取而代之：`current()`、`history()`、`can_trade()`和`is_stale()`。旧的 API 将继续工作，但现在会发出弃用警告。

现在，您可以将调整源传递给`DataPortal`，我们将对定价数据应用调整，以便在查看数据时向后看。执行和呈现给算法的数据的当前价格和成交量是资产的交易价值。

#### 新入口点（[1173](https://github.com/stefan-jansen/zipline/issues/1173)和[1178](https://github.com/stefan-jansen/zipline/issues/1178)）

为了使 zipline 的使用更加便捷，我们更新了回测的入口点。现在支持的三种运行回测的方式包括：

1.  `zipline.run_algo()`

1.  `$ zipline run`

1.  `%zipline`（IPython magic）

#### 数据包（[1173](https://github.com/stefan-jansen/zipline/issues/1173)和[1178](https://github.com/stefan-jansen/zipline/issues/1178)）

1.0.0 引入了数据包。数据包是应预加载并用于稍后运行回测的数据组。这允许用户无需每次运行算法时指定他们感兴趣的符号。这也允许我们在运行之间缓存数据。

默认情况下，将使用`quantopian-quandl`包，该包从 Quantopian 的 quandl 镜像中提取数据[WIKI 数据集](https://www.quandl.com/data/WIKI)。可以通过`zipline.data.bundles.register()`注册新包，例如：

```py
@zipline.data.bundles.register('my-new-bundle')
def my_new_bundle_ingest(environ,
                         asset_db_writer,
                         minute_bar_writer,
                         daily_bar_writer,
                         adjustment_writer,
                         calendar,
                         cache,
                         show_progress):
    ... 
```

此函数应检索所需数据，然后使用已传递的写入器将该数据写入磁盘上的位置，以便 zipline 稍后可以找到。

通过将名称作为`-b / --bundle`参数传递给 `$ zipline run` 或作为 `bundle` 参数传递给 `zipline.run_algorithm()`，可以在回测中使用此数据。

有关更多信息，请参阅数据了解更多信息。

#### 管道中的字符串支持（[1174](https://github.com/stefan-jansen/zipline/issues/1174)）

增加了对管道中字符串数据的支持。`zipline.pipeline.data.Column`现在接受`object`作为数据类型，这表示该列的加载器应发出实验性新`LabelArray`类的窗口迭代器。

还新增了多个`分类器`方法，用于构建基于字符串操作的`过滤器`实例。新增的方法有：

> +   `element_of()`
> +   
> +   `startswith()`
> +   
> +   `endswith()`
> +   
> +   `has_substring()`
> +   
> +   `matches()`
> +   
> `element_of` 为所有分类器定义。其余方法仅针对字符串数据类型的分类器定义。

### 增强功能

+   使数据加载类具有更一致的接口。这包括股票条形写入器、调整写入器和资产数据库写入器。新的接口是在构造时传递要写入的资源，稍后将数据提供给 write 方法，作为数据帧或数据帧的某些迭代器。这种模式允许我们将这些写入器对象作为其他类和函数可以消费的资源传递（[1109](https://github.com/stefan-jansen/zipline/issues/1109)和[1149](https://github.com/stefan-jansen/zipline/issues/1149)）。

+   为`zipline.pipeline.CustomFactor`添加了掩码功能。自定义因子现在可以在实例化时传递一个过滤器。这告诉因子只计算过滤器返回 True 的股票，而不是总是计算整个股票宇宙。（[1095](https://github.com/stefan-jansen/zipline/issues/1095)）

+   新增了`zipline.utils.cache.ExpiringCache`。这是一个缓存，它将条目包装在`zipline.utils.cache.CachedObject`中，该缓存根据 get 方法提供的 dt 管理条目的过期。（[1130](https://github.com/stefan-jansen/zipline/issues/1130)）

+   实现了`zipline.pipeline.factors.RecarrayField`，这是一个新的管道术语，设计为具有多个输出的自定义因子的输出类型。（[1119](https://github.com/stefan-jansen/zipline/issues/1119)）

+   为`zipline.pipeline.CustomFactor`添加了可选的 outputs 参数。自定义因子现在能够计算并返回多个输出，每个输出本身都是一个因子。（[1119](https://github.com/stefan-jansen/zipline/issues/1119)）

+   增加了对字符串数据类型管道列的支持。这些列的加载器在遍历时应该生成`zipline.lib.labelarray.LabelArray`的实例。字符串列上的`latest()`生成一个字符串数据类型的`zipline.pipeline.Classifier`。（[1174](https://github.com/stefan-jansen/zipline/issues/1174)）

+   增加了几种将分类器转换为过滤器的方法。

    新增的方法包括：- `element_of()` - `startswith()` - `endswith()` - `has_substring()` - `matches()`

    `element_of` 为所有分类器定义。其余方法仅对字符串定义。（[1174](https://github.com/stefan-jansen/zipline/issues/1174)）

+   新增了 `BollingerBands` 因子。该因子实现了布林带技术指标：[`en.wikipedia.org/wiki/Bollinger_Bands`](https://en.wikipedia.org/wiki/Bollinger_Bands) ([1199](https://github.com/stefan-jansen/zipline/issues/1199))。

+   Fetcher 已从 Quantopian 内部代码移至 Zipline ([1105](https://github.com/stefan-jansen/zipline/issues/1105))。

+   新增了新的内置因子，`RollingPearsonOfReturns`、`RollingSpearmanOfReturns` 和 `RollingLinearRegressionOfReturns` ([1154](https://github.com/stefan-jansen/zipline/issues/1154))

### 实验性功能

警告

实验性功能可能会发生变化。

+   新增了一个新的 `zipline.lib.labelarray.LabelArray` 类，用于高效地表示和计算 numpy 中的字符串数据。这个类在概念上类似于 [`pandas.Categorical`](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Categorical.html#pandas.Categorical "(in pandas v2.0.3)")，它将字符串数组表示为索引数组，索引指向一个（较小的）唯一字符串值数组。([1174](https://github.com/stefan-jansen/zipline/issues/1174))

### 错误修复

无

### 性能

无

### 维护和重构

无

### 构建

无

### 文档

+   更新了 API 方法的文档 ([1188](https://github.com/stefan-jansen/zipline/issues/1188))。

+   更新了发布流程，提到文档应该使用 Python 3 构建 ([1188](https://github.com/stefan-jansen/zipline/issues/1188))。

### 杂项

+   Zipline 现在为 `zipline.api` 模块提供了一个 [stub 文件](https://www.python.org/dev/peps/pep-0484/#stub-files)。该模块通常是动态创建的，因此 stub 文件为能够消费它的工具提供了一些静态信息，例如 PyCharm ([1208](https://github.com/stefan-jansen/zipline/issues/1208))。

## 发布 0.9.0

发布：

0.9.0

日期：

2016 年 3 月 29 日

### 亮点

+   为管道增加了分类器和归一化方法，以及新的数据集和因子。

+   增加了对 Windows 的支持，并在 AppVeyor 上进行持续集成。

### 增强功能

+   新增了`CashBuybackAuthorizations`和`ShareBuybackAuthorizations`两个数据集，用于管道 API。这些数据集提供了一个抽象接口，用于分别向新算法添加现金和股票回购授权数据。基于 pandas 的这些数据集的参考实现可以在`zipline.pipeline.loaders.buyback_auth`中找到，而基于 blaze 的实验性实现可以在`zipline.pipeline.loaders.blaze.buyback_auth`中找到。([1022](https://github.com/stefan-jansen/zipline/issues/1022))

+   新增了`DividendsByExDate`、`DividendsByPayDate`和`DividendsByAnnouncementDate`三个数据集，用于管道 API。这些数据集提供了一个抽象接口，用于按除息日、支付日和公告日分别向新算法添加股息数据。基于 pandas 的这些数据集的参考实现可以在`zipline.pipeline.loaders.dividends`中找到，而基于 blaze 的实验性实现可以在`zipline.pipeline.loaders.blaze.dividends`中找到。([1093](https://github.com/stefan-jansen/zipline/issues/1093))

+   新增了内置因子`zipline.pipeline.factors.BusinessDaysSinceCashBuybackAuth`和`zipline.pipeline.factors.BusinessDaysSinceShareBuybackAuth`。这些因子分别使用新的`CashBuybackAuthorizations`和`ShareBuybackAuthorizations`数据集。([1022](https://github.com/stefan-jansen/zipline/issues/1022))

+   新增了内置因子`zipline.pipeline.factors.BusinessDaysSinceDividendAnnouncement`、`zipline.pipeline.factors.BusinessDaysUntilNextExDate`和`zipline.pipeline.factors.BusinessDaysSincePreviousExDate`。这些因子分别使用新的`DividendsByAnnouncementDate`和`DividendsByExDate`数据集。([1093](https://github.com/stefan-jansen/zipline/issues/1093))

+   实现了`zipline.pipeline.Classifier`，这是一个新的核心管道 API 术语，代表分组键。分类器主要通过将它们作为`groupby`参数传递给因子归一化方法来使用。([1046](https://github.com/stefan-jansen/zipline/issues/1046))

+   新增了因子归一化方法：`zipline.pipeline.Factor.demean()`和`zipline.pipeline.Factor.zscore()`。([1046](https://github.com/stefan-jansen/zipline/issues/1046))

+   添加了`zipline.pipeline.Factor.quantiles()`，这是一个方法，用于通过将因子分成等大小的桶来计算一个分类器。还添加了常见分位数大小的辅助方法(`zipline.pipeline.Factor.quartiles()`，`zipline.pipeline.Factor.quartiles()`，和`zipline.pipeline.Factor.deciles()`) ([1075](https://github.com/stefan-jansen/zipline/issues/1075))。

### 实验性功能

警告

实验性功能可能会发生变化。

无

### 错误修复

+   修复了一个 bug，该 bug 导致在输入过多时合并两个数值表达式失败。这导致在合并超过十个因子或过滤器时运行管道失败。([1072](https://github.com/stefan-jansen/zipline/issues/1072))

### 性能

无

### 维护和重构

无

### 构建

+   为 Windows 上的持续集成添加了 AppVeyor。在 AppVeyor 和 Travis 构建中添加了 zipline 及其依赖项的 conda 构建，这些构建将其结果上传到 anaconda.org，并标记为“ci”。([981](https://github.com/stefan-jansen/zipline/issues/981))

### 文档

无

### 杂项

+   添加了`ZiplineTestCase`，它提供了消费测试 fixture 的钩子。Fixture 包括像`WithAssetFinder`这样的东西，它会在你的测试中使`self.asset_finder`可用，并附带一些 mock 数据([1042](https://github.com/stefan-jansen/zipline/issues/1042))。

### 亮点

+   为 pipeline 添加了分类器和归一化方法，以及新的数据集和因子。

+   增加了对 Windows 系统的持续集成支持，使用 AppVeyor 进行集成。

### 增强功能

+   为 Pipeline API 添加了新的数据集`CashBuybackAuthorizations`和`ShareBuybackAuthorizations`。这些数据集提供了一个抽象接口，用于分别为一个新的算法添加现金和股票回购授权数据。这些数据集的基于 pandas 的参考实现可以在`zipline.pipeline.loaders.buyback_auth`中找到，而基于 blaze 的实验性实现可以在`zipline.pipeline.loaders.blaze.buyback_auth`中找到。([1022](https://github.com/stefan-jansen/zipline/issues/1022))

+   为 Pipeline API 添加了新的数据集`DividendsByExDate`、`DividendsByPayDate`和`DividendsByAnnouncementDate`。这些数据集提供了一个抽象接口，用于根据除息日、支付日和公告日分别添加股息数据到一个新的算法中。这些数据集的基于 pandas 的参考实现可以在`zipline.pipeline.loaders.dividends`中找到，而基于 blaze 的实验性实现可以在`zipline.pipeline.loaders.blaze.dividends`中找到。([1093](https://github.com/stefan-jansen/zipline/issues/1093))

+   添加了新的内置因子，`zipline.pipeline.factors.BusinessDaysSinceCashBuybackAuth`和`zipline.pipeline.factors.BusinessDaysSinceShareBuybackAuth`。这些因子分别使用新的`CashBuybackAuthorizations`和`ShareBuybackAuthorizations`数据集。([1022](https://github.com/stefan-jansen/zipline/issues/1022))。

+   添加了新的内置因子，`zipline.pipeline.factors.BusinessDaysSinceDividendAnnouncement`，`zipline.pipeline.factors.BusinessDaysUntilNextExDate`和`zipline.pipeline.factors.BusinessDaysSincePreviousExDate`。这些因子分别使用新的`DividendsByAnnouncementDate`和`DividendsByExDate`数据集。([1093](https://github.com/stefan-jansen/zipline/issues/1093))。

+   实现了`zipline.pipeline.Classifier`，一个新的核心管道 API 术语，代表分组键。分类器主要用于将它们作为`groupby`参数传递给因子归一化方法。([1046](https://github.com/stefan-jansen/zipline/issues/1046))

+   添加了因子归一化方法：`zipline.pipeline.Factor.demean()`和`zipline.pipeline.Factor.zscore()`。([1046](https://github.com/stefan-jansen/zipline/issues/1046))

+   添加了`zipline.pipeline.Factor.quantiles()`方法，该方法通过将因子分成等大小的桶来计算分类器。还添加了常见分位数大小的辅助方法（`zipline.pipeline.Factor.quartiles()`，`zipline.pipeline.Factor.quartiles()`和`zipline.pipeline.Factor.deciles()`）([1075](https://github.com/stefan-jansen/zipline/issues/1075))。

### 实验性功能

警告

实验性功能可能会发生变化。

无

### 错误修复

+   修复了一个 bug，即合并两个数值表达式在输入过多时失败。这导致在合并超过十个因子或过滤器时运行管道失败。([1072](https://github.com/stefan-jansen/zipline/issues/1072))

### 性能

无

### 维护和重构

无

### 构建

+   为 Windows 上的持续集成添加了 AppVeyor。在 AppVeyor 和 Travis 构建中添加了 zipline 及其依赖项的 conda 构建，这些构建将其结果上传到 anaconda.org，并标记为“ci”。([981](https://github.com/stefan-jansen/zipline/issues/981))

### 文档

无

### 杂项

+   添加了`ZiplineTestCase`，它提供了消费测试 fixture 的钩子。fixture 是一些东西，比如：`WithAssetFinder`，它将使`self.asset_finder`在你的测试中可用，并带有一些 mock 数据([1042](https://github.com/stefan-jansen/zipline/issues/1042))。

## 发布 0.8.4

发布：

0.8.4

日期：

2016 年 2 月 24 日

### 亮点

+   新增了一个用于 Pipeline API 的`EarningsCalendar`数据集（[905](https://github.com/stefan-jansen/zipline/issues/905)）。

+   `AssetFinder` 加速（[830](https://github.com/stefan-jansen/zipline/issues/830) 和 [817](https://github.com/stefan-jansen/zipline/issues/817)）。

+   改进了 Pipeline 对非浮点 dtype 的支持。最值得注意的是，我们现在支持`Factor`的`datetime64`和`int64` dtype，以及`BoundColumn.latest`现在在列的 dtype 为`bool`时返回一个正确的`Filter`对象。

+   Zipline 现在支持`numpy` 1.10、`pandas` 0.17 和`scipy` 0.16（[969](https://github.com/stefan-jansen/zipline/issues/969)）。

+   批量转换已被弃用，并将在未来的版本中移除。建议使用`history`作为替代。

### 增强功能

+   为用户提供了一种方法，可以在执行预定函数（包括`handle_data`）时使用上下文管理器。这个上下文管理器将传递给`BarData`对象，并用于所有预定运行的函数期间。这可以通过`TradingAlgorithm`的关键字参数`create_event_context`传递（[828](https://github.com/stefan-jansen/zipline/issues/828)）。

+   增加了对具有`datetime64[ns]` dtype 的`zipline.pipeline.factors.Factor`实例的支持（[905](https://github.com/stefan-jansen/zipline/issues/905)）。

+   新增了一个用于 Pipeline API 的`EarningsCalendar`数据集。这个数据集提供了一个抽象接口，用于向新算法添加收益公告数据。这个数据集的基于 pandas 的参考实现可以在`zipline.pipeline.loaders.earnings`中找到，而基于 blaze 的实验性实现可以在`zipline.pipeline.loaders.blaze.earnings`中找到（[905](https://github.com/stefan-jansen/zipline/issues/905)）。

+   新增了两个内置因子：`zipline.pipeline.factors.BusinessDaysUntilNextEarnings`和`zipline.pipeline.factors.BusinessDaysSincePreviousEarnings`。这些因子使用了新的`EarningsCalendar`数据集（[905](https://github.com/stefan-jansen/zipline/issues/905)）。

+   为`zipline.pipeline.factors.Factor`添加了`isnan()`、`notnan()`和`isfinite()`方法（[861](https://github.com/stefan-jansen/zipline/issues/861)）。

+   新增了`zipline.pipeline.factors.Returns`，这是一个内置因子，用于计算给定窗口长度内收盘价的变化百分比（[884](https://github.com/stefan-jansen/zipline/issues/884)）。

+   新增了一个内置因子：`AverageDollarVolume`（[927](https://github.com/stefan-jansen/zipline/issues/927)）。

+   添加了`ExponentialWeightedMovingAverage`和`ExponentialWeightedMovingStdDev`因子（[910](https://github.com/stefan-jansen/zipline/issues/910)）。

+   允许对`DataSet`类进行子类化，其中子类继承了父类的所有列。这些列将成为新的哨兵，因此您可以注册一个自定义加载器（[924](https://github.com/stefan-jansen/zipline/issues/924)）。

+   添加了`coerce()`函数，用于在将输入传递给函数之前将其从一种类型强制转换为另一种类型（[948](https://github.com/stefan-jansen/zipline/issues/948)）。

+   添加了`optionally()`函数，用于包装其他预处理器函数，明确允许`None`值（[947](https://github.com/stefan-jansen/zipline/issues/947)）。

+   添加了`ensure_timezone()`函数，允许将字符串参数转换为[`datetime.tzinfo`](https://docs.python.org/3/library/datetime.html#datetime.tzinfo "(in Python v3.11)")对象。这也允许直接传递`tzinfo`对象（[947](https://github.com/stefan-jansen/zipline/issues/947)）。

+   为`BlazeLoader`和`BlazeEarningsCalendarLoader`添加了两个可选参数`data_query_time`和`data_query_tz`。这些参数允许用户在从资源加载数据时指定一些截止时间。例如，如果我想模拟在`8:45 US/Eastern`执行我的`before_trading_start`函数，那么我可以将`datetime.time(8, 45)`和`'US/Eastern'`传递给加载器。这意味着在模拟中，在`8:45`之后的时间戳的数据将不会在当天看到。这些数据将在下一天提供（[947](https://github.com/stefan-jansen/zipline/issues/947)）。

+   `BoundColumn.latest`现在为`bool`数据类型的列返回一个`Filter`（[962](https://github.com/stefan-jansen/zipline/issues/962)）。

+   增加了对`Factor`实例使用`int64`数据类型的支持。现在，当数据类型为整数时，`Column`需要一个`missing_value`（[962](https://github.com/stefan-jansen/zipline/issues/962)）。

+   现在还可以为`float`、`datetime`和`bool` Pipeline 项指定自定义的`missing_value`值（[962](https://github.com/stefan-jansen/zipline/issues/962)）。

+   为股票增加了自动关闭支持。任何在股票达到其`auto_close_date`时持有的头寸将根据股票的最后成交价清算为现金。此外，该股票的所有未结订单将被取消。现在，期货和股票都在其`auto_close_date`的早晨自动关闭，即在`before_trading_start`之前立即关闭（[982](https://github.com/stefan-jansen/zipline/issues/982)）。

### 实验性功能

警告

实验性功能可能会发生变化。

+   增加了对参数化`Factor`子类的支持。因子可以指定`params`作为类级别的属性，其中包含参数名的元组。然后，这些值被构造函数接受，并通过名称转发到因子的`compute`函数。此 API 是实验性的，可能会在将来的版本中更改。

### 错误修复

+   修复了一个问题，该问题会导致每日/每分钟方法缓存改变`SIDData`对象的`len`，即使对象为空，也会让我们认为它不为空（[826](https://github.com/stefan-jansen/zipline/issues/826)）。

+   修复了在基准数据稀疏时计算 beta 时引发的错误。现在返回的是[`numpy.nan`](https://numpy.org/doc/stable/reference/constants.html#numpy.nan "(in NumPy v1.25)")（[859](https://github.com/stefan-jansen/zipline/issues/859)）。

+   修复了挑选`sentinel()`对象的问题（[872](https://github.com/stefan-jansen/zipline/issues/872)）。

+   修复了在首次下载国债数据时出现的虚假警告（:issue 922）。

+   更正了在`initialize`函数外部使用`set_commission()`和`set_slippage()`时的错误消息。这些错误将函数称为`override_*`而不是`set_*`。还将引发的异常类型从`OverrideSlippagePostInit`和`OverrideCommissionPostInit`重命名为`SetSlippagePostInit`和`SetCommissionPostInit`（[923](https://github.com/stefan-jansen/zipline/issues/923)）。

+   修复了 CLI 中的一个问题，该问题会导致资产被重复添加，从而将同一符号映射到两个不同的 sid（[942](https://github.com/stefan-jansen/zipline/issues/942)）。

+   修复了一个问题，即`PerformancePeriod`在创建`Account`时错误地报告了总持仓价值（[950](https://github.com/stefan-jansen/zipline/issues/950)）。

+   修复了在 32 位 python 中历史和 BarData 引发的 KeyError 问题，其中 Assets 与 int64s 比较不正确（[959](https://github.com/stefan-jansen/zipline/issues/959)）。

+   修复了一个错误，其中`Filter`上的布尔运算符未正确实现（[991](https://github.com/stefan-jansen/zipline/issues/991)）。

+   安装 zipline 不再无声且无条件地将 numpy 降级到 1.9.2（[969](https://github.com/stefan-jansen/zipline/issues/969)）。

### 性能

+   通过添加一个扩展`AssetFinderCachedEquities`，将股票加载到字典中，然后指导`lookup_symbol()`使用这些字典查找匹配的股票，从而加快了`lookup_symbol()`的速度（[830](https://github.com/stefan-jansen/zipline/issues/830)）。

+   通过执行批量查询，提高了`lookup_symbol()`的性能（[817](https://github.com/stefan-jansen/zipline/issues/817)）。

### 维护和重构

+   资产数据库现在包含版本信息，以确保与当前 Zipline 版本（[815](https://github.com/stefan-jansen/zipline/issues/815)）的兼容性。

+   将`requests`版本升级到 2.9.1（[2ee40db](https://github.com/stefan-jansen/zipline/commit/2ee40db)）

+   将`logbook`版本升级到 0.12.5（[11465d9](https://github.com/stefan-jansen/zipline/commit/11465d9)）。

+   将`Cython`版本升级到 0.23.4（[5f49fa2](https://github.com/stefan-jansen/zipline/commit/5f49fa2)）。

### 构建

+   使 zipline 安装需求更加灵活（[825](https://github.com/stefan-jansen/zipline/issues/825)）。

+   使用`versioneer`管理项目`__version__`和 setup.py 版本（[829](https://github.com/stefan-jansen/zipline/issues/829)）。

+   修复了 travis 构建上的 coveralls 集成（[840](https://github.com/stefan-jansen/zipline/issues/840)）。

+   修复了 conda 构建，现在使用 git 源作为其源，并使用 setup.py 读取需求，而不是复制它们并让它们不同步（[937](https://github.com/stefan-jansen/zipline/issues/937)）。

+   要求`setuptools`版本大于 18.0（[951](https://github.com/stefan-jansen/zipline/issues/951)）。

### 文档

+   为开发者记录发布流程（[835](https://github.com/stefan-jansen/zipline/issues/835)）。

+   为 Pipeline API 添加了参考文档（[864](https://github.com/stefan-jansen/zipline/issues/864)）。

+   为资产元数据 API 添加了参考文档（[864](https://github.com/stefan-jansen/zipline/issues/864)）。

+   生成的文档现在包括许多类和函数的源代码链接（[864](https://github.com/stefan-jansen/zipline/issues/864)）。

+   添加了平台特定的文档，描述如何找到二进制依赖项（[883](https://github.com/stefan-jansen/zipline/issues/883)）。

### 杂项

+   添加了一个`show_graph()`方法，用于将 Pipeline 渲染为图像（[836](https://github.com/stefan-jansen/zipline/issues/836)）。

+   添加了`subtest()`装饰器，用于创建子测试，而不使用`nose_parameterized.expand()`，后者会使测试输出膨胀（[833](https://github.com/stefan-jansen/zipline/issues/833)）。

+   将测试输出中的计时器报告限制为最长的 15 个测试（[838](https://github.com/stefan-jansen/zipline/issues/838)）。

+   国债和基准下载现在将等待长达一小时再次下载，如果从远程源返回的数据没有延伸到预期的日期（[841](https://github.com/stefan-jansen/zipline/issues/841)）。

+   添加了一个工具，用于将资产数据库降级到以前的版本（[941](https://github.com/stefan-jansen/zipline/issues/941)）。

### 亮点

+   为 Pipeline API 添加了一个新的`EarningsCalendar`数据集（[905](https://github.com/stefan-jansen/zipline/issues/905)）。

+   `AssetFinder` 的加速改进（[830](https://github.com/stefan-jansen/zipline/issues/830) 和 [817](https://github.com/stefan-jansen/zipline/issues/817)）。

+   改进了 Pipeline 对非浮点数据类型的支持。最值得注意的是，我们现在支持 `Factor` 的 `datetime64` 和 `int64` 数据类型，以及 `BoundColumn.latest` 现在在列的数据类型为 `bool` 时返回一个正确的 `Filter` 对象。

+   Zipline 现在支持 `numpy` 1.10、`pandas` 0.17 和 `scipy` 0.16（[969](https://github.com/stefan-jansen/zipline/issues/969)）。

+   批量转换已被弃用，并将在未来的版本中移除。建议使用 `history` 作为替代。

### 增强功能

+   为用户提供了一种方法，可以在执行预定函数（包括`handle_data`）时提供一个上下文管理器。该上下文管理器将传递给 `BarData` 对象，并用于所有预定运行的函数期间。这可以通过关键字参数 `create_event_context` 传递给 `TradingAlgorithm`（[828](https://github.com/stefan-jansen/zipline/issues/828)）。

+   增加了对 `zipline.pipeline.factors.Factor` 实例的支持，这些实例具有 `datetime64[ns]` 数据类型（[905](https://github.com/stefan-jansen/zipline/issues/905)）。

+   新增了一个新的 `EarningsCalendar` 数据集，用于 Pipeline API。该数据集提供了一个抽象接口，用于向新算法添加收益公告数据。该数据集的基于 pandas 的参考实现可以在 `zipline.pipeline.loaders.earnings` 中找到，而基于 blaze 的实验性实现可以在 `zipline.pipeline.loaders.blaze.earnings` 中找到（[905](https://github.com/stefan-jansen/zipline/issues/905)）。

+   新增了两个内置因子：`zipline.pipeline.factors.BusinessDaysUntilNextEarnings` 和 `zipline.pipeline.factors.BusinessDaysSincePreviousEarnings`。这些因子使用了新的 `EarningsCalendar` 数据集（[905](https://github.com/stefan-jansen/zipline/issues/905)）。

+   为 `zipline.pipeline.factors.Factor` 添加了 `isnan()`、`notnan()` 和 `isfinite()` 方法（[861](https://github.com/stefan-jansen/zipline/issues/861)）。

+   新增了 `zipline.pipeline.factors.Returns`，这是一个内置因子，用于计算给定窗口长度内收盘价的变化百分比（[884](https://github.com/stefan-jansen/zipline/issues/884)）。

+   新增了一个新的内置因子：`AverageDollarVolume`（[927](https://github.com/stefan-jansen/zipline/issues/927)）。

+   增加了`ExponentialWeightedMovingAverage`和`ExponentialWeightedMovingStdDev`因子（[910](https://github.com/stefan-jansen/zipline/issues/910)）。

+   允许`DataSet`类被继承，其中子类从父类继承所有列。这些列将是新的占位符，因此您可以注册一个自定义加载器（[924](https://github.com/stefan-jansen/zipline/issues/924)）。

+   增加了`coerce()`，在将输入传递给函数之前，将其从一种类型强制转换为另一种类型（[948](https://github.com/stefan-jansen/zipline/issues/948)）。

+   增加了`optionally()`来包装其他预处理函数，明确允许`None`（[947](https://github.com/stefan-jansen/zipline/issues/947)）。

+   增加了`ensure_timezone()`，允许将字符串参数转换为[`datetime.tzinfo`](https://docs.python.org/3/library/datetime.html#datetime.tzinfo "(in Python v3.11)")对象。这也允许直接传递`tzinfo`对象（[947](https://github.com/stefan-jansen/zipline/issues/947)）。

+   在`BlazeLoader`和`BlazeEarningsCalendarLoader`中增加了两个可选参数`data_query_time`和`data_query_tz`。这些参数允许用户在从资源加载数据时指定一些截止时间。例如，如果我想模拟在`8:45 US/Eastern`执行我的`before_trading_start`函数，那么我可以将`datetime.time(8, 45)`和`'US/Eastern'`传递给加载器。这意味着在模拟中，时间戳为`8:45`或之后的数据在那一天将不会被看到。这些数据将在下一天提供（[947](https://github.com/stefan-jansen/zipline/issues/947)）。

+   `BoundColumn.latest`现在为`bool`类型的列返回一个`Filter`（[962](https://github.com/stefan-jansen/zipline/issues/962)）。

+   增加了对`int64` dtype 的`Factor`实例的支持。现在，当 dtype 为整数时，`Column`需要一个`missing_value`（[962](https://github.com/stefan-jansen/zipline/issues/962)）。

+   现在还可以为`float`、`datetime`和`bool` Pipeline 项指定自定义的`missing_value`值（[962](https://github.com/stefan-jansen/zipline/issues/962)）。

+   增加了对股票的自动关闭支持。任何持有到其`auto_close_date`的股票头寸将根据该股票的最后成交价清算为现金。此外，该股票的所有未结订单将被取消。现在，期货和股票都在其`auto_close_date`的早晨自动关闭，即在`before_trading_start`之前立即关闭（[982](https://github.com/stefan-jansen/zipline/issues/982)）。

### 实验性功能

警告

实验性功能可能会发生变化。

+   增加了对参数化`Factor`子类的支持。因子可以指定`params`作为类级别的属性，其中包含参数名称的元组。这些值随后被构造函数接受，并通过名称转发到因子的`compute`函数。此 API 是实验性的，可能会在将来的版本中更改。

### 错误修复

+   修复了一个问题，该问题会导致每日/每分钟方法缓存改变`SIDData`对象的`len`。这会导致我们认为即使对象为空，对象也不为空（[826](https://github.com/stefan-jansen/zipline/issues/826)）。

+   修复了在基准数据稀疏时计算 beta 时引发的错误。改为返回[`numpy.nan`](https://numpy.org/doc/stable/reference/constants.html#numpy.nan "(in NumPy v1.25)")（[859](https://github.com/stefan-jansen/zipline/issues/859)）。

+   修复了在 pickling `sentinel()`对象时出现的问题（[872](https://github.com/stefan-jansen/zipline/issues/872)）。

+   首次下载国库数据时修复了虚假警告问题（:issue 922）。

+   更正了在`initialize`函数外部使用`set_commission()`和`set_slippage()`时的错误消息。这些错误将函数称为`override_*`而不是`set_*`。同时也将引发的异常类型从`OverrideSlippagePostInit`和`OverrideCommissionPostInit`重命名为`SetSlippagePostInit`和`SetCommissionPostInit`（[923](https://github.com/stefan-jansen/zipline/issues/923)）。

+   修复了 CLI 中的一个问题，该问题会导致资产被添加两次。这将同一符号映射到两个不同的 sid（[942](https://github.com/stefan-jansen/zipline/issues/942)）。

+   修复了`PerformancePeriod`在创建`Account`时错误报告总持仓价值的问题（[950](https://github.com/stefan-jansen/zipline/issues/950)）。

+   修复了在 32 位 python 上从历史和 BarData 中出现的 KeyError 问题，其中 Assets 与 int64s 比较不正确（[959](https://github.com/stefan-jansen/zipline/issues/959)）。

+   修复了布尔运算符在`Filter`中未正确实现的问题（[991](https://github.com/stefan-jansen/zipline/issues/991)）。

+   zipline 的安装不再无声且无条件地将 numpy 降级到 1.9.2（[969](https://github.com/stefan-jansen/zipline/issues/969)）。

### 性能

+   通过添加一个扩展`AssetFinderCachedEquities`，将股票加载到字典中，然后指导`lookup_symbol()`使用这些字典来查找匹配的股票，从而加快了`lookup_symbol()`的速度（[830](https://github.com/stefan-jansen/zipline/issues/830)）。

+   通过执行批量查询，提高了`lookup_symbol()`的性能。([817](https://github.com/stefan-jansen/zipline/issues/817))。

### 维护和重构

+   资产数据库现在包含版本信息，以确保与当前 Zipline 版本的兼容性([815](https://github.com/stefan-jansen/zipline/issues/815))。

+   将`requests`版本升级到 2.9.1 ([2ee40db](https://github.com/stefan-jansen/zipline/commit/2ee40db))

+   将`logbook`版本升级到 0.12.5 ([11465d9](https://github.com/stefan-jansen/zipline/commit/11465d9))。

+   将`Cython`版本升级到 0.23.4 ([5f49fa2](https://github.com/stefan-jansen/zipline/commit/5f49fa2))。

### 构建

+   使 zipline 安装需求更加灵活([825](https://github.com/stefan-jansen/zipline/issues/825))。

+   使用`versioneer`管理项目的`__version__`和 setup.py 版本([829](https://github.com/stefan-jansen/zipline/issues/829))。

+   修复了 travis 构建中的 coveralls 集成问题([840](https://github.com/stefan-jansen/zipline/issues/840))。

+   修复了 conda 构建，现在使用 git 源作为其源，并使用 setup.py 读取需求，而不是复制它们并让它们不同步([937](https://github.com/stefan-jansen/zipline/issues/937))。

+   要求`setuptools`版本大于 18.0 ([951](https://github.com/stefan-jansen/zipline/issues/951))。

### 文档

+   为开发者记录了发布流程([835](https://github.com/stefan-jansen/zipline/issues/835))。

+   为 Pipeline API 添加了参考文档。([864](https://github.com/stefan-jansen/zipline/issues/864))。

+   为资产元数据 API 添加了参考文档。([864](https://github.com/stefan-jansen/zipline/issues/864))。

+   生成的文档现在包括许多类和函数的源代码链接。([864](https://github.com/stefan-jansen/zipline/issues/864))。

+   添加了平台特定的文档，描述如何找到二进制依赖项。([883](https://github.com/stefan-jansen/zipline/issues/883))。

### 杂项

+   添加了一个`show_graph()`方法，用于将 Pipeline 渲染为图像([836](https://github.com/stefan-jansen/zipline/issues/836))。

+   添加了`subtest()`装饰器，用于创建子测试，而不使用`nose_parameterized.expand()`，这会使测试输出膨胀([833](https://github.com/stefan-jansen/zipline/issues/833))。

+   将测试输出中的计时器报告限制为最长的 15 个测试([838](https://github.com/stefan-jansen/zipline/issues/838))。

+   国债和基准下载现在如果从远程源返回的数据没有延伸到预期日期，将等待长达一小时再次下载。([841](https://github.com/stefan-jansen/zipline/issues/841))。

+   添加了一个工具，用于将资产数据库降级到以前的版本([941](https://github.com/stefan-jansen/zipline/issues/941))。

## 发布 0.8.3

发布：

0.8.3

日期：

2015 年 11 月 6 日

注意

我们将版本推进到`0.8.3`，以修复 pypi 的源分布问题。这个版本没有代码更改。

## 发布 0.8.0

发布：

0.8.0

日期：

2015 年 11 月 6 日

### 亮点

+   新的文档系统，新网站位于[zipline.io](https://www.zipline.io)。

+   主要性能提升。

+   动态历史。

+   新的用户定义方法：`before_trading_start`。

+   新的 api 函数：`schedule_function()`。

+   新的 api 函数：`get_environment()`。

+   新的 api 函数：`set_max_leverage()`。

+   新的 api 函数：`set_do_not_order_list()`。

+   管道 API。

+   支持交易期货。

### 增强功能

+   账户对象：向上下文中添加一个账户对象，以跟踪有关交易账户的信息。例如：

    ```py
    context.account.settled_cash 
    ```

    返回存储在账户对象上的结算现金价值。随着算法的运行，该值会相应更新（[396](https://github.com/stefan-jansen/zipline/issues/396)）。

+   `HistoryContainer`现在可以动态增长，对`history()`的调用现在能够增加历史容器的大小或改变其形状，以便能够服务调用。`add_history()`现在作为性能提示，预先分配容器中足够的空间。这个变化与`history`向后兼容，所有现有的算法应该继续按预期工作（[412](https://github.com/stefan-jansen/zipline/issues/412)）。

+   从 quantopian 移植的简单转换并使用历史。`SIDData`现在有以下方法：

    +   `stddev`

    +   `mavg`

    +   `vwap`

    +   `returns`

    这些方法，除了`returns`，接受一个天数参数。如果你使用分钟数据运行，那么这将计算那些天中的分钟数，考虑到提前关闭和当前时间，并在这些分钟上应用转换。`returns`不接受参数，将返回给定资产的日回报。例如：

    ```py
    data[security].stddev(3) 
    ```

    （[429](https://github.com/stefan-jansen/zipline/issues/429)）。

+   性能周期中新增字段。性能周期在`to_dict`返回值中新增了可访问的字段：- 总杠杆 - 净杠杆 - 空头暴露 - 多头暴露 - 空头数量 - 多头数量（[464](https://github.com/stefan-jansen/zipline/issues/464)）。

+   允许`order_percent()`与各种市场价值一起工作（由 Jeremiah Lowin 提供）。

    目前，`order_percent()`和`order_target_percent()`都作为`self.portfolio.portfolio_value`的百分比操作。这个 PR 允许它们作为其他重要市场价值的百分比操作。还增加了`context.get_market_value()`，这使得这种功能得以实现。例如：

    ```py
    # this is how it works today (and this still works)
    # put 50% of my portfolio in AAPL
    order_percent('AAPL', 0.5)
    # note that if this were a fully invested portfolio, it would become 150% levered.

    # take half of my available cash and buy AAPL
    order_percent('AAPL', 0.5, percent_of='cash')

    # rebalance my short position, as a percentage of my current short
    book_target_percent('MSFT', 0.1, percent_of='shorts')

    # rebalance within a custom group of stocks
    tech_stocks = ('AAPL', 'MSFT', 'GOOGL')
    tech_filter = lambda p: p.sid in tech_stocks
    for stock in tech_stocks:
        order_target_percent(stock, 1/3, percent_of_fn=tech_filter) 
    ```

    （[477](https://github.com/stefan-jansen/zipline/issues/477)）。

+   命令行选项，用于将算法打印到 stdout（由 Andrea D’Amore 提供）([545](https://github.com/stefan-jansen/zipline/issues/545))。

+   新的用户定义函数`before_trading_start`。用户可以重写该函数，以便在市场开盘前每天调用一次([389](https://github.com/stefan-jansen/zipline/issues/389))。

+   新的 api 函数`schedule_function()`。该函数允许用户根据更复杂的日期和时间规则安排函数调用。例如，在市场关闭前 15 分钟调用该函数，尊重提前关闭([411](https://github.com/stefan-jansen/zipline/issues/411))。

+   新的 api 函数`set_do_not_order_list()`。该函数接受一个资产列表，并添加一个交易保护措施，防止算法交易这些资产。添加了一个实时列表，列出了人们可能想要标记为“不交易”的杠杆 ETF([478](https://github.com/stefan-jansen/zipline/issues/478))。

+   新增表示证券的类。`order()`和其他订单函数现在需要一个`Security`实例，而不是一个整数或字符串([520](https://github.com/stefan-jansen/zipline/issues/520))。

+   将`Security`类泛化为`Asset`。这是为了准备添加对其他资产类型的支持([535](https://github.com/stefan-jansen/zipline/issues/535))。

+   新的 api 函数`get_environment()`。该函数默认返回字符串`'zipline'`。这样算法就可以在 Quantopian 和本地 zipline 上表现出不同的行为([384](https://github.com/stefan-jansen/zipline/issues/384))。

+   扩展`get_environment()`以向算法公开更多的环境。该函数现在接受一个参数，该参数是要返回的字段。默认情况下，这是`'platform'`，它返回旧的`'zipline'`值，但可以请求以下新字段：

    +   `''arena'`：这是实时交易还是回测？

    +   `'data_frequency'`：这是分钟模式还是日模式？

    +   `'start'`：模拟开始日期。

    +   `'end'`：模拟结束日期。

    +   `'capital_base'`：模拟的起始资本。

    +   `'platform'`：算法运行的平台。

    +   `'*'`：包含所有这些字段的字典。

    ([449](https://github.com/stefan-jansen/zipline/issues/449))。

+   新的 api 函数`set_max_leveraged()`。该方法添加了一个交易保护措施，防止算法过度杠杆化自身([552](https://github.com/stefan-jansen/zipline/issues/552))。

### 实验性功能

警告

实验性功能可能会发生变化。

+   添加了新的 Pipeline API。Pipeline API 是一个高级的声明式 API，用于表示大型数据集上的尾随窗口计算（[630](https://github.com/stefan-jansen/zipline/issues/630)）。

+   增加了对期货交易的支持（[637](https://github.com/stefan-jansen/zipline/issues/637)）。

+   为 blaze 表达式添加了 Pipeline 加载器。这允许用户从任何 blaze 理解的数据格式中提取数据，并在 Pipeline API 中使用它（[775](https://github.com/stefan-jansen/zipline/issues/775)）。

### 错误修复

+   修复了一个错误，该错误会导致报告的回报率在随机时间段内急剧下降（[378](https://github.com/stefan-jansen/zipline/issues/378)）。

+   修复了一个阻止调试器解析算法文件的错误（[431](https://github.com/stefan-jansen/zipline/issues/431)）。

+   正确地将参数转发给用户自定义的`initialize`函数（[687](https://github.com/stefan-jansen/zipline/issues/687)）。

+   修复了一个错误，该错误会导致在东部时间午夜和财政部数据可用时间之间的每次回测中重新下载财政部数据（[793](https://github.com/stefan-jansen/zipline/issues/793)）。

+   修复了一个错误，该错误会导致如果将用户自定义的`analyze`函数作为关键字参数传递给`TradingAlgorithm`，则不会被调用（[819](https://github.com/stefan-jansen/zipline/issues/819)）。

### 性能

+   对历史记录进行了重大性能改进（由 Dale Jung 贡献）（[488](https://github.com/stefan-jansen/zipline/issues/488)）。

### 维护和重构

+   移除了简单的转换代码。这些代码作为`SIDData`的方法提供（[550](https://github.com/stefan-jansen/zipline/issues/550)）。

### 构建

无

### 文档

+   切换到 sphinx 进行文档编写（[816](https://github.com/stefan-jansen/zipline/issues/816)）。

### 亮点

+   新的文档系统，新网站位于[zipline.io](https://www.zipline.io)

+   主要性能提升。

+   动态历史。

+   新增用户自定义方法：`before_trading_start`。

+   新增 api 函数：`schedule_function()`。

+   新增 api 函数：`get_environment()`。

+   新增 api 函数：`set_max_leverage()`。

+   新增 api 函数：`set_do_not_order_list()`。

+   Pipeline API。

+   支持交易期货。

### 增强功能

+   账户对象：向上下文中添加一个账户对象，用于跟踪交易账户的信息。例如：

    ```py
    context.account.settled_cash 
    ```

    返回存储在账户对象上的结算现金价值。随着算法的运行，该值会相应更新（[396](https://github.com/stefan-jansen/zipline/issues/396)）。

+   `HistoryContainer`现在可以动态增长，调用`history()`将能够增加历史容器的大小或改变其形状，以满足调用需求。`add_history()`现在作为性能提示，预先分配容器中足够的空间。这一变化与`history`向后兼容，所有现有算法应继续按预期工作（[412](https://github.com/stefan-jansen/zipline/issues/412)）。

+   从 quantopian 移植过来的简单变换，现在`SIDData`拥有以下方法：

    +   `stddev`

    +   `mavg`

    +   `vwap`

    +   `returns`

    除了`returns`，这些方法接受一个天数参数。如果你使用分钟数据运行，那么这将计算那些天数中的分钟数，考虑到提前收盘和当前时间，并在这些分钟集合上应用变换。`returns`不接受任何参数，将返回给定资产的日回报率。例如：

    ```py
    data[security].stddev(3) 
    ```

    ([429](https://github.com/stefan-jansen/zipline/issues/429)）。

+   性能周期中的新字段。性能周期在`to_dict`返回值中具有新字段：- 总杠杆 - 净杠杆 - 空头暴露 - 多头暴露 - 空头数量 - 多头数量（[464](https://github.com/stefan-jansen/zipline/issues/464)）。

+   允许`order_percent()`与各种市场价值一起工作（由 Jeremiah Lowin 提供）。

    目前，`order_percent()`和`order_target_percent()`都作为`self.portfolio.portfolio_value`的百分比操作。此 PR 允许它们作为其他重要市场价值的百分比操作。还添加了`context.get_market_value()`，这使得这种功能得以实现。例如：

    ```py
    # this is how it works today (and this still works)
    # put 50% of my portfolio in AAPL
    order_percent('AAPL', 0.5)
    # note that if this were a fully invested portfolio, it would become 150% levered.

    # take half of my available cash and buy AAPL
    order_percent('AAPL', 0.5, percent_of='cash')

    # rebalance my short position, as a percentage of my current short
    book_target_percent('MSFT', 0.1, percent_of='shorts')

    # rebalance within a custom group of stocks
    tech_stocks = ('AAPL', 'MSFT', 'GOOGL')
    tech_filter = lambda p: p.sid in tech_stocks
    for stock in tech_stocks:
        order_target_percent(stock, 1/3, percent_of_fn=tech_filter) 
    ```

    ([477](https://github.com/stefan-jansen/zipline/issues/477)）。

+   命令行选项，用于将算法打印到 stdout（由 Andrea D’Amore 提供）（[545](https://github.com/stefan-jansen/zipline/issues/545)）。

+   新增用户自定义函数`before_trading_start`。用户可以重写该函数，使其在市场开盘前每天调用一次（[389](https://github.com/stefan-jansen/zipline/issues/389)）。

+   新增 api 函数`schedule_function()`。该函数允许用户根据更复杂的日期和时间规则来安排函数的调用。例如，在市场收盘前 15 分钟调用该函数，同时尊重提前收盘的情况（[411](https://github.com/stefan-jansen/zipline/issues/411)）。

+   新增 api 函数`set_do_not_order_list()`。该函数接受一组资产，并添加一个交易防护，阻止算法交易这些资产。添加了一个时间点列表，列出了人们可能想要标记为“不交易”的杠杆 ETF（[478](https://github.com/stefan-jansen/zipline/issues/478)）。

+   添加了一个表示证券的类。`order()`和其他订单函数现在需要一个`Security`实例，而不是一个整数或字符串（[520](https://github.com/stefan-jansen/zipline/issues/520)）。

+   将`Security`类泛化为`Asset`。这是为了准备添加对其他资产类型的支持（[535](https://github.com/stefan-jansen/zipline/issues/535)）。

+   新的 api 函数`get_environment()`。该函数默认返回字符串`'zipline'`。这是为了让算法在 Quantopian 和本地 zipline 上具有不同的行为（[384](https://github.com/stefan-jansen/zipline/issues/384)）。

+   扩展了`get_environment()`，以向算法暴露更多的环境。该函数现在接受一个参数，该参数是要返回的字段。默认情况下，这是`'platform'`，它返回旧的`'zipline'`值，但可以请求以下新字段：

    +   `''arena'`：这是实时交易还是回测？

    +   `'data_frequency'`：这是分钟模式还是日模式？

    +   `'start'`：模拟开始日期。

    +   `'end'`：模拟结束日期。

    +   `'capital_base'`：模拟的起始资本。

    +   `'platform'`：算法运行的平台。

    +   `'*'`：包含所有这些字段的字典。

    （[449](https://github.com/stefan-jansen/zipline/issues/449)）。

+   新的 api 函数`set_max_leveraged()`。该方法添加了一个交易守卫，防止算法过度杠杆化自己（[552](https://github.com/stefan-jansen/zipline/issues/552)）。

### 实验性功能

警告

实验性功能可能会发生变化。

+   添加了新的 Pipeline API。Pipeline API 是一个高级的声明式 API，用于表示大型数据集上的尾随窗口计算（[630](https://github.com/stefan-jansen/zipline/issues/630)）。

+   添加了对期货交易的支持（[637](https://github.com/stefan-jansen/zipline/issues/637)）。

+   添加了用于 blaze 表达式的 Pipeline 加载器。这允许用户从任何 blaze 理解的数据格式中提取数据，并在 Pipeline API 中使用它（[775](https://github.com/stefan-jansen/zipline/issues/775)）。

### 错误修复

+   修复了一个问题，即报告的回报率会在随机时间段内急剧下降（[378](https://github.com/stefan-jansen/zipline/issues/378)）。

+   修复了一个阻止调试器解析算法文件的错误（[431](https://github.com/stefan-jansen/zipline/issues/431)）。

+   正确地将参数传递给用户定义的`initialize`函数（[687](https://github.com/stefan-jansen/zipline/issues/687)）。

+   修复了一个错误，该错误会导致国债数据在每个回测期间从午夜 EST 到国债数据可用的时间重新下载（[793](https://github.com/stefan-jansen/zipline/issues/793)）。

+   修复了一个 bug，该 bug 会导致如果将用户定义的`analyze`函数作为关键字参数传递给`TradingAlgorithm`，则不会被调用（[819](https://github.com/stefan-jansen/zipline/issues/819)）。

### 性能

+   对历史记录进行了重大性能改进（由 Dale Jung 提供）（[488](https://github.com/stefan-jansen/zipline/issues/488)）。

### 维护和重构

+   移除简单的转换代码。这些作为`SIDData`的方法提供（[550](https://github.com/stefan-jansen/zipline/issues/550)）。

### 构建

无

### 文档

+   切换到 sphinx 进行文档编写（[816](https://github.com/stefan-jansen/zipline/issues/816)）。

## 发布 0.7.0

发布：

0.7.0

日期：

2014 年 7 月 25 日

### 亮点

+   直接运行算法的命令行界面。

+   IPython 魔法命令`%%zipline`，用于在 IPython 笔记本单元格中运行定义的算法。

+   用于构建防止订单失控和意外空头头寸的安全措施的 API 方法。

+   新的 history()函数，用于获取过去市场数据的移动 DataFrame（替换 BatchTransform）。

+   一个新的[入门教程](http://nbviewer.ipython.org/github/quantopian/zipline/blob/master/docs/tutorial.ipynb)。

### 增强功能

+   CLI：为 zipline 添加 CLI 和 IPython 魔法。示例：

    ```py
    python run_algo.py -f dual_moving_avg.py --symbols AAPL --start 2011-1-1 --end 2012-1-1 -o dma.pickle 
    ```

    从雅虎财经获取数据，运行`dual_moving_avg.py`文件（并查找`dual_moving_avg_analyze.py`，如果找到，将在算法运行后执行），并将性能`DataFrame`输出到`dma.pickle`（[325](https://github.com/stefan-jansen/zipline/issues/325)）。

+   IPython 魔法命令（在 IPython 笔记本单元格顶部）。示例：

    ```py
    %%zipline --symbols AAPL --start 2011-1-1 --end 2012-1-1 -o perf 
    ```

    与上述相同，只是不是执行文件，而是查找单元格中的算法，并且不是将性能 df 输出到文件，而是在命名空间中创建一个名为 perf 的变量（[325](https://github.com/stefan-jansen/zipline/issues/325)）。

+   向算法 API 添加交易控制。

    以下函数现在可在`TradingAlgorithm`和算法脚本中使用：

    `set_max_order_size(self, sid=None, max_shares=None, max_notional=None)` 为算法为给定 sid 下的任何单个订单设置绝对大小限制，无论是以股份还是总美元价值计算。如果`sid`为 None，则该规则适用于算法下的任何订单。示例：

    ```py
    def initialize(context):
        # Algorithm will raise an exception if we attempt to place an
        # order which would cause us to hold more than 10 shares
        # or 1000 dollars worth of sid(24).
        set_max_order_size(sid(24), max_shares=10, max_notional=1000.0) 
    ```

    `set_max_position_size(self, sid=None, max_shares=None, max_notional=None)` - 为算法持有的给定 sid 的任何仓位设置绝对大小限制，无论是以股份还是美元价值计算。如果`sid`为 None，则该规则适用于算法持有的任何仓位。示例：

    ```py
    def initialize(context):
        # Algorithm will raise an exception if we attempt to order more than
        # 10 shares or 1000 dollars worth of sid(24) in a single order.
        set_max_order_size(sid(24), max_shares=10, max_notional=1000.0)

    ``set_max_order_count(self, max_count)``
    Set a limit on the number of orders that can be placed by the algorithm in
    a single trading day.
    Example: 
    ```

    ```py
    def initialize(context):
        # Algorithm will raise an exception if more than 50 orders are placed in a day.
        set_max_order_count(50) 
    ```

    `set_long_only(self)` 设置规则，指定算法不得持有空头头寸。示例：

    ```py
    def initialize(context):
        # Algorithm will raise an exception if it attempts to place
        # an order that would cause it to hold a short position.
        set_long_only() 
    ```

    （[329](https://github.com/stefan-jansen/zipline/issues/329)）。

+   在`TradingAlgorithm`上添加了一个`all_api_methods`类方法，该方法返回所有`TradingAlgorithm` API 方法的列表（[333](https://github.com/stefan-jansen/zipline/issues/333)）。

+   扩展了 record()功能以实现动态命名。record()函数现在可以在 kwargs 之前接受 positional args。所有原始用法和功能保持不变，但现在这些额外用法将起作用：

    ```py
    name = 'Dynamically_Generated_String'
    record( name, value, ... )
    record( name, value1, 'name2', value2, name3=value3, name4=value4 ) 
    ```

    要求仅仅是 positional args 只出现在 kwargs 之前（[355](https://github.com/stefan-jansen/zipline/issues/355)）。

+   `history()`已从 Quantopian 移植到 Zipline，并提供市场数据的移动窗口。`history()`取代了 BatchTransform。它更快，适用于分钟级数据，并且具有更优越的接口。要使用它，请在`initialize()`内部调用`add_history()`，然后在`handle_data()`内部通过调用`history()`接收一个 pandas `DataFrame`。查看[教程](http://nbviewer.ipython.org/github/quantopian/zipline/blob/master/docs/tutorial.ipynb)和[示例](https://github.com/quantopian/zipline/blob/master/zipline/examples/dual_moving_average.py)。（[345](https://github.com/stefan-jansen/zipline/issues/345)和[357](https://github.com/stefan-jansen/zipline/issues/357)）。

+   `history()`现在支持`1m`窗口长度（[345](https://github.com/stefan-jansen/zipline/issues/345)）。

### 错误修复

+   修复交易环境中的交易日、开盘和收盘对齐问题（[331](https://github.com/stefan-jansen/zipline/issues/331)）。

+   修复添加/删除新字段时的滚动面板问题（[349](https://github.com/stefan-jansen/zipline/issues/349)）。

### 性能

无

### 维护和重构

+   移除未记录和未测试的 HDF5 和 CSV 数据源（[267](https://github.com/stefan-jansen/zipline/issues/267)）。

+   重构 sim_params（[352](https://github.com/stefan-jansen/zipline/issues/352)）。

+   重构历史记录（[340](https://github.com/stefan-jansen/zipline/issues/340)）。

### 构建

+   以下依赖项已更新（zipline 可能也与其他版本兼容）：

    ```py
    -pytz==2013.9
    +pytz==2014.4
    +numpy==1.8.1
    -numpy==1.8.0
    +scipy==0.12.0
    +patsy==0.2.1
    +statsmodels==0.5.0
    -six==1.5.2
    +six==1.6.1
    -Cython==0.20
    +Cython==0.20.1
    -TA-Lib==0.4.8
    +--allow-external TA-Lib --allow-unverified TA-Lib TA-Lib==0.4.8
    -requests==2.2.0
    +requests==2.3.0
    -nose==1.3.0
    +nose==1.3.3
    -xlrd==0.9.2
    +xlrd==0.9.3
    -pep8==1.4.6
    +pep8==1.5.7
    -pyflakes==0.7.3
    -pip-tools==0.3.4
    +pyflakes==0.8.1`
    -scipy==0.13.2
    -tornado==3.2
    -pyparsing==2.0.1
    -patsy==0.2.1
    -statsmodels==0.4.3
    +tornado==3.2.1
    +pyparsing==2.0.2
    -Markdown==2.3.1
    +Markdown==2.4.1 
    ```

### 贡献者

以下按提交次数排序的人员为本次发布做出了贡献：

```py
38  Scott Sanderson
29  Thomas Wiecki
26  Eddie Hebert
 6  Delaney Granizo-Mackenzie
 3  David Edwards
 3  Richard Frank
 2  Jonathan Kamens
 1  Pankaj Garg
 1  Tony Lambiris
 1  fawce 
```

### 亮点

+   直接运行算法的命令行界面。

+   IPython 魔法命令`%%zipline`，用于在 IPython 笔记本单元格中运行定义的算法。

+   构建防止失控订单和不需要的空头头寸的安全措施的 API 方法。

+   新增`history()`函数以获取过去市场数据的移动`DataFrame`（取代 BatchTransform）。

+   新增[入门教程](http://nbviewer.ipython.org/github/quantopian/zipline/blob/master/docs/tutorial.ipynb)。

### 增强功能

+   CLI：添加了 zipline 的 CLI 和 IPython 魔法。示例：

    ```py
    python run_algo.py -f dual_moving_avg.py --symbols AAPL --start 2011-1-1 --end 2012-1-1 -o dma.pickle 
    ```

    从雅虎财经获取数据，运行`dual_moving_avg.py`文件（并查找`dual_moving_avg_analyze.py`，如果找到，将在算法运行后执行），并将性能`DataFrame`输出到`dma.pickle`（[325](https://github.com/stefan-jansen/zipline/issues/325)）。

+   IPython 魔法命令（在 IPython 笔记本单元格顶部）。示例：

    ```py
    %%zipline --symbols AAPL --start 2011-1-1 --end 2012-1-1 -o perf 
    ```

    与上述相同，只是不是执行文件，而是查找单元格中的算法，并且不是将性能数据框输出到文件，而是在命名空间中创建一个名为 perf 的变量（[325](https://github.com/stefan-jansen/zipline/issues/325)）。

+   向算法 API 添加了交易控制。

    以下函数现在在`TradingAlgorithm`和算法脚本中可用：

    `set_max_order_size(self, sid=None, max_shares=None, max_notional=None)` 为该算法为特定 sid 下的任何单个订单设置绝对量级限制，单位为股份和/或总美元价值。如果`sid`为 None，则该规则适用于该算法下的任何订单。示例：

    ```py
    def initialize(context):
        # Algorithm will raise an exception if we attempt to place an
        # order which would cause us to hold more than 10 shares
        # or 1000 dollars worth of sid(24).
        set_max_order_size(sid(24), max_shares=10, max_notional=1000.0) 
    ```

    `set_max_position_size(self, sid=None, max_shares=None, max_notional=None)` - 为该算法为特定 sid 下的任何持仓设置绝对量级限制，单位为股份或美元价值。如果`sid`为 None，则该规则适用于该算法下的任何持仓。示例：

    ```py
    def initialize(context):
        # Algorithm will raise an exception if we attempt to order more than
        # 10 shares or 1000 dollars worth of sid(24) in a single order.
        set_max_order_size(sid(24), max_shares=10, max_notional=1000.0)

    ``set_max_order_count(self, max_count)``
    Set a limit on the number of orders that can be placed by the algorithm in
    a single trading day.
    Example: 
    ```

    ```py
    def initialize(context):
        # Algorithm will raise an exception if more than 50 orders are placed in a day.
        set_max_order_count(50) 
    ```

    `set_long_only(self)` 设置一条规则，指定算法不得持有空头仓位。示例：

    ```py
    def initialize(context):
        # Algorithm will raise an exception if it attempts to place
        # an order that would cause it to hold a short position.
        set_long_only() 
    ```

    （[329](https://github.com/stefan-jansen/zipline/issues/329)）。

+   在`TradingAlgorithm`上添加了一个`all_api_methods`类方法，该方法返回所有`TradingAlgorithm` API 方法的列表（[333](https://github.com/stefan-jansen/zipline/issues/333)）。

+   扩展了 record()函数的动态命名功能。record()函数现在可以接受位置参数出现在关键字参数之前。所有原始用法和功能保持不变，但现在这些额外的用法将有效：

    ```py
    name = 'Dynamically_Generated_String'
    record( name, value, ... )
    record( name, value1, 'name2', value2, name3=value3, name4=value4 ) 
    ```

    要求很简单，即位置参数必须出现在关键字参数之前（[355](https://github.com/stefan-jansen/zipline/issues/355)）。

+   history()已从 Quantopian 移植到 Zipline，并提供市场数据的移动窗口。history()取代了 BatchTransform。它更快，适用于分钟级别的数据，并且具有更优越的接口。要使用它，请在`initialize()`内部调用`add_history()`，然后在`handle_data()`内部调用 history()以接收一个 pandas `DataFrame`。查看[教程](http://nbviewer.ipython.org/github/quantopian/zipline/blob/master/docs/tutorial.ipynb)和[示例](https://github.com/quantopian/zipline/blob/master/zipline/examples/dual_moving_average.py)。（[345](https://github.com/stefan-jansen/zipline/issues/345)和[357](https://github.com/stefan-jansen/zipline/issues/357)）。

+   history()现在支持`1m`窗口长度（[345](https://github.com/stefan-jansen/zipline/issues/345)）。

### 错误修复

+   修复了交易日的对齐以及交易环境中的开盘和收盘问题（[331](https://github.com/stefan-jansen/zipline/issues/331)）。

+   修复了添加/删除新字段时的 RollingPanel 问题（[349](https://github.com/stefan-jansen/zipline/issues/349)）。

### 性能

无

### 维护和重构

+   删除了未记录和未测试的 HDF5 和 CSV 数据源（[267](https://github.com/stefan-jansen/zipline/issues/267)）。

+   重构 sim_params（[352](https://github.com/stefan-jansen/zipline/issues/352)）。

+   重构历史记录（[340](https://github.com/stefan-jansen/zipline/issues/340)）。

### 构建

+   以下依赖项已更新（zipline 可能与其他版本也兼容）：

    ```py
    -pytz==2013.9
    +pytz==2014.4
    +numpy==1.8.1
    -numpy==1.8.0
    +scipy==0.12.0
    +patsy==0.2.1
    +statsmodels==0.5.0
    -six==1.5.2
    +six==1.6.1
    -Cython==0.20
    +Cython==0.20.1
    -TA-Lib==0.4.8
    +--allow-external TA-Lib --allow-unverified TA-Lib TA-Lib==0.4.8
    -requests==2.2.0
    +requests==2.3.0
    -nose==1.3.0
    +nose==1.3.3
    -xlrd==0.9.2
    +xlrd==0.9.3
    -pep8==1.4.6
    +pep8==1.5.7
    -pyflakes==0.7.3
    -pip-tools==0.3.4
    +pyflakes==0.8.1`
    -scipy==0.13.2
    -tornado==3.2
    -pyparsing==2.0.1
    -patsy==0.2.1
    -statsmodels==0.4.3
    +tornado==3.2.1
    +pyparsing==2.0.2
    -Markdown==2.3.1
    +Markdown==2.4.1 
    ```

### 贡献者

以下人员对此次发布做出了贡献，按提交次数排序：

```py
38  Scott Sanderson
29  Thomas Wiecki
26  Eddie Hebert
 6  Delaney Granizo-Mackenzie
 3  David Edwards
 3  Richard Frank
 2  Jonathan Kamens
 1  Pankaj Garg
 1  Tony Lambiris
 1  fawce 
```

## 发布 0.6.1

发布：

0.6.1

日期：

2014 年 4 月 23 日

### 亮点

+   对风险计算进行了重大修复，参见 Bug Fixes 部分。

+   移植`history()`函数，参见 Enhancements 部分。

+   开始支持 Quantopian 算法脚本语法，参见 ENH 部分。

+   conda 包管理器支持，参见 Build 部分。

### 增强功能

+   始终处理新订单，即在`handle_data`未被调用的条上，但有“时钟”数据，例如一致的基准，处理订单。

+   现在，投资组合容器中的空位已被过滤掉，以帮助防止算法对不在现有股票池中的股票进行操作。以前，遍历仓位会返回持有零股股票的仓位。（在算法代码中进行显式检查，如`pos.amount != 0`，可以防止使用不存在的仓位。）

+   添加 BMF&Bovespa 交易日历。

+   添加算法脚本支持的开始。

+   开始与 Quantopian 的 IDE 中的脚本语法实现路径对等，[`quantopian.com`](https://quantopian.com) 示例：

    ```py
    from datetime import datetime import pytz
    from zipline import TradingAlgorithm
    from zipline.utils.factory import load_from_yahoo

    from zipline.api import order

    def initialize(context):
        context.test = 10

    def handle_date(context, data):
        order('AAPL', 10)
        print(context.test)

    if __name__ == '__main__':
        import pylab as pl
        start = datetime(2008, 1, 1, 0, 0, 0, 0, pytz.utc)
        end = datetime(2010, 1, 1, 0, 0, 0, 0, pytz.utc)
        data = load_from_yahoo(
            stocks=['AAPL'],
            indexes={},
            start=start,
            end=end)
        data = data.dropna()
        algo = TradingAlgorithm(
            initialize=initialize,
            handle_data=handle_date)
        results = algo.run(data)
        results.portfolio_value.plot()
        pl.show() 
    ```

+   添加 HDF5 和 CSV 数据源。

+   限制`handle_data`在有市场数据时调用。为了防止自定义数据类型的时间戳未对齐的情况，只在市场数据通过时调用`handle_data`。在市场数据之前到达的自定义数据仍会更新数据条，但处理该数据的操作只会在有可操作市场数据时进行。

+   扩展每股的佣金方法，允许每笔交易有最低成本。

+   添加符号 API 函数。Quantopian 添加了一个`symbol()`查找功能。通过在 zipline 中添加相同的 API 函数，我们可以使将 Zipline 算法复制粘贴到 Quantopian 变得更加容易。

+   添加模拟随机交易数据源。添加了一个新的数据源，该数据源以特定的用户指定频率（分钟或每日）发出事件。这允许用户在分钟模式下进行回测和调试算法，为通往 Quantopian 提供了一条更清晰的途径。

+   移除对基准的依赖以获取交易日历。交易日历现在用于填充环境中的交易日，而不是基准的索引。移除`extra_date`字段，因为与基准列表不同，交易日历可以生成未来日期，因此不需要为当天的交易添加日期。动机：

    +   开盘和收盘/提前收盘日历以及交易日历的来源现在是相同的，这应该有助于防止由于错位而可能出现的问题。

    +   允许配置，其中基准由基于生成器的数据源提供，无需为了填充日期而提供第二个基准列表。

+   从 Quantopian 移植`history()` API 方法。开放了之前仅在 Quantopian 平台上可用的`history()`函数的核心部分。

    历史方法类似于`batch_transform`函数/装饰器，但希望对捕获的前一根 K 线数据的频率和周期有更精确的规范。示例用法：

    ```py
    from zipline.api import history, add_history

    def initialize(context):
        add_history(bar_count=2, frequency='1d', field='price')

    def handle_data(context, data):
        prices = history(bar_count=2, frequency='1d', field='price')
        context.last_prices = prices 
    ```

    注意：此版本的历史方法缺少回填功能，该功能允许在第一根 K 线上返回完整的 DataFrame。

### 错误修复

+   调整基准事件以匹配市场时间（[241](https://github.com/stefan-jansen/zipline/issues/241)）。以前基准事件是在与基准相关的日期的 0:00 发出的：在“分钟”排放模式下，这意味着基准在处理任何日内交易之前发出。

+   确保为所有交易日生成性能统计数据。当使用每分钟排放时，模拟器会向用户报告它模拟了“n - 1”天（其中 n 是在模拟参数中指定的天数）。现在报告了正确数量的交易日被模拟。

+   修正累积风险指标的 repr。RiskMetricsCumulative 的`__repr__`引用了该类的旧结构，导致打印时出现异常。此外，现在打印了指标 DataFrame 中的最后值。

+   防止在可用数据结束时分钟排放崩溃。下一天的计算在分钟排放算法到达可用数据结束时导致错误。当到达可用数据时，不再抛出通用异常，而是抛出一个命名的异常，以便交易模拟循环可以跳过，因为不需要下一个市场收盘。

+   修正交易日历中的 pandas 索引。这也可以归档在性能问题下。使用 loc 索引代替效率低下的按日索引，然后再按时间索引。

+   防止由于不存在的成员导致的 vwap 转换崩溃。WrongDataForTransform 正在引用一个不存在的`self.fields`成员。添加一个`self.fields`成员并设置为`price`和`volume`，并在检查期间使用它进行迭代。

+   修正最大回撤计算。输入到最大回撤的数据不正确，导致结果不佳。即`compounded_log_returns`不是代表算法在给定时间的总回报的值，尽管`calculate_max_drawdown`将这些值视为如此。现在使用`algorithm_period_returns`系列，它确实提供了总回报。

+   修正成本基础计算。成本基础计算现在考虑了交易的方向。平仓多头或回补空头不应影响成本基础。

+   修复`order()`中的浮点错误。在订单数量接近整数时，可能会意外地被舍入或向上取整（取决于正负）到错误的整数。例如，内部存储为-27.99999 的数量被转换为-27 而不是-28。

+   当持仓因拆分而变化时，更新绩效周期状态。否则，`self._position_amounts`将与 position.amount 等不同步。

+   修正使用确切日期时下行系列计算的错位。在使传递给风险模块的回报系列更精确的工作中暴露出的一个奇怪现象是，回报和平均回报之间的系列比较是不平衡的，因为平均回报没有被屏蔽到下行数据点；然而，在大多数情况下，如果不是所有情况下，这都被`.valid()`调用所掩盖，这在这次变更集中被移除了。

+   在使用之前检查`self.logger`是否存在。`self.logger`被初始化为`None`，不能保证用户已经设置它，所以在尝试向它传递消息之前检查它是否存在。

+   防止绩效跟踪器中的市场收盘不同步。在绩效跟踪器被重置或修补以处理与预热实时数据的状态切换的情况下，绩效跟踪器的`market_close`成员可能会与绩效跟踪器确定的当前算法时间不同步。症状是股息从未触发，因为收盘检查与当前时间不匹配。通过让交易模拟循环负责在每分钟/分钟模式下推进市场收盘，并将该值传递给绩效跟踪器，而不是让绩效跟踪器也推进市场收盘来修复。

+   修正多个累积和周期风险计算。预期会改变的计算包括：

    +   `cumulative.beta`

    +   `cumulative.alpha`

    +   `cumulative.information`

    +   `cumulative.sharpe`

    +   `period.sortino`

    风险计算如何变化：周期和累积风险修正

    下行风险

    使用样本代替总体来计算标准差。

    添加一个舍入因子，以便在给定的时间间隔内，如果两个值接近，它们不会被计为下行值，这会干扰下行差异标准差的分母。

    标准差类型

    全面地将标准差计算标准化为使用“样本”计算，而在此之前，累积风险主要使用“总体”。使用`ddof=1`与`np.std`计算，就像值是样本一样。

    累积风险修正

    贝塔

    使用每日算法回报和基准，而不是年化平均回报。

    波动率

    使用样本标准差代替总体标准差。

    波动率是其他计算的输入，因此这个变化影响夏普和信息比率计算。

    信息比率

    基准回报输入从年化基准回报更改为年化平均回报。

    阿尔法

    基准回报输入从年化基准回报更改为年化平均回报。

    周期风险修正

    Sortino

    现在使用每日回报相对于平均算法回报的下行风险，而不是国债回报作为最低可接受回报。

    上述内容需要添加计算周期风险的平均算法返回。

    此外，使用`algorithm_period_returns`和`tresaury_period_return`作为累积 Sortino 的输入，而不是使用算法返回作为 Sortino 计算的两个输入。

### 性能

+   移除了`alias_dt`转换，转而在 SIDData 上添加属性。通过`alias_dt`生成器将事件的 dt 字段复制为 datetime，以便 API 宽容并允许 SIDData 对象上同时存在 datetime 和 dt，这产生了明显的开销，即使在 noop 算法中也是如此。不再为每个通过系统传递的事件复制 datetime 值并将其分配给事件对象而产生成本，而是在 SIDData 上添加一个属性，该属性作为`dt`的别名`datetime`。最终可能会移除对`data['foo'].datetime`的支持，并可能被视为已弃用。

+   从累积返回中移除了“空返回”的丢弃。在每个条形图上检查空返回键的存在并丢弃该返回会增加不必要的 CPU 时间，当算法以分钟排放运行时。相反，在开始日期之前的交易日索引处添加 0.0 返回。移除`空返回`主要是为了防止周期计算在非日期索引值上崩溃；由于索引为日期，周期返回也可以近似波动性（尽管该波动性具有高噪声信号强度，因为它仅使用两个值作为输入。）

### 维护和重构

+   允许`sim_params`为算法提供数据频率。如果算法的`data_frequency`为 None，则允许`sim_params`提供`data_frequency`。

    此外，如果提供了算法的数据频率，则推迟到该频率。

### 构建

+   增加了通过 conda 进行构建和发布的支持。对于那些更喜欢使用[`docs.conda.io/en/latest/`](https://docs.conda.io/en/latest/)而不是使用 pip 本地编译的人来说，以下应该可以在许多系统上安装 Zipline。

    ```py
    conda install -c quantopian zipline 
    ```

### 贡献者

以下人员为本次发布做出了贡献，按提交次数排序：

```py
49  Eddie Hebert
28  Thomas Wiecki
11  Richard Frank
 2  Jamie Kirkpatrick
 2  Jeremiah Lowin
 1  Colin Alexander
 1  Michael Schatzow
 1  Moises Trovo
 1  Suminda Dharmasena 
```

### 亮点

+   对风险计算进行了重大修复，请参阅错误修复部分。

+   移植`history()`函数，请参阅增强功能部分

+   开始支持 Quantopian 算法脚本语法，请参阅 ENH 部分。

+   conda 包管理器支持，请参阅构建部分。

### 增强功能

+   始终处理新订单，即在`handle_data`未被调用的条形图上，但存在“时钟”数据（例如，一致的基准），处理订单。

+   现在从投资组合容器中过滤掉空仓位。这有助于防止算法对不在现有股票范围内的仓位进行操作。以前，遍历仓位会返回持有零股的股票仓位。（通过在算法代码中显式检查`pos.amount != 0`可以防止使用不存在的仓位。）

+   添加 BMF&Bovespa 的交易日历。

+   添加算法脚本支持的开始。

+   开始与 Quantopian 的 IDE 中的脚本语法保持一致的路径（[`quantopian.com`](https://quantopian.com)）示例：

    ```py
    from datetime import datetime import pytz
    from zipline import TradingAlgorithm
    from zipline.utils.factory import load_from_yahoo

    from zipline.api import order

    def initialize(context):
        context.test = 10

    def handle_date(context, data):
        order('AAPL', 10)
        print(context.test)

    if __name__ == '__main__':
        import pylab as pl
        start = datetime(2008, 1, 1, 0, 0, 0, 0, pytz.utc)
        end = datetime(2010, 1, 1, 0, 0, 0, 0, pytz.utc)
        data = load_from_yahoo(
            stocks=['AAPL'],
            indexes={},
            start=start,
            end=end)
        data = data.dropna()
        algo = TradingAlgorithm(
            initialize=initialize,
            handle_data=handle_date)
        results = algo.run(data)
        results.portfolio_value.plot()
        pl.show() 
    ```

+   添加 HDF5 和 CSV 数据源。

+   限制`handle_data`仅在市场数据时间调用。为了避免自定义数据类型的时间戳未对齐的情况，仅在市场数据通过时调用`handle_data`。在市场数据之前到达的自定义数据仍会更新数据条，但只有在有可操作的市场数据时才会处理这些数据。

+   扩展每股的佣金方法以允许每笔交易的最小成本。

+   添加符号 API 函数。Quantopian 添加了一个`symbol()`查找功能。通过在 zipline 中添加相同的 API 函数，我们可以使将 Zipline 算法复制粘贴到 Quantopian 变得更加容易。

+   添加模拟随机交易源。添加了一个新的数据源，该数据源以用户指定的特定频率（分钟或每日）发出事件。这允许用户在分钟模式下进行回测和调试算法，为通往 Quantopian 提供了一条更清晰的途径。

+   去除对基准的依赖以获取交易日历。现在不再使用基准的索引，而是使用交易日历来填充环境的交易日。移除`extra_date`字段，因为与基准列表不同，交易日历可以生成未来日期，因此不需要添加当前交易日的日期。动机：

    +   开盘和收盘/提前收盘日历以及交易日历的来源现在是相同的，这应该有助于防止由于错位而可能出现的问题。

    +   允许配置，其中基准作为基于生成器的数据源提供，无需提供第二个基准列表来填充日期。

+   从 Quantopian 移植`history()` API 方法。打开了之前仅在 Quantopian 平台上可用的`history()`函数的核心。

    历史方法类似于`batch_transform`函数/装饰器，但希望对捕获的前一个条形数据的频率和周期有更精确的规范。示例用法：

    ```py
    from zipline.api import history, add_history

    def initialize(context):
        add_history(bar_count=2, frequency='1d', field='price')

    def handle_data(context, data):
        prices = history(bar_count=2, frequency='1d', field='price')
        context.last_prices = prices 
    ```

    注意：此版本的历史记录缺少回填功能，该功能允许在第一个条形上返回完整的数据框。

### 错误修复

+   调整基准事件以匹配市场时间（[241](https://github.com/stefan-jansen/zipline/issues/241)）。以前基准事件在基准相关日期的 0:00 发出：在“分钟”发射模式下，这意味着基准在处理任何日内交易之前发出。

+   确保为所有交易日生成性能统计数据。在以每分钟发射运行时，模拟器会向用户报告它模拟了“n - 1”天（其中 n 是模拟参数中指定的天数）。现在报告模拟了正确数量的交易日。

+   修复累积风险度量的 repr。RiskMetricsCumulative 的`__repr__`之前引用的是该类的旧结构，导致打印时出现异常。现在还会打印度量数据框中的最后一个值。

+   防止分钟级数据发射在数据可用性结束时崩溃。下一天的计算在分钟级数据发射算法到达数据可用性结束时引发了错误。现在不再是在数据可用性结束时抛出通用异常，而是抛出一个命名的异常并捕获它，以便交易模拟循环可以跳过，因为在结束时不需要下一个市场收盘。

+   修正交易日历中的 pandas 索引。这也可以归类为性能问题。使用 loc 进行索引，而不是效率低下的逐日索引，然后再按时间索引。

+   防止由于不存在成员导致的 vwap 转换崩溃。WrongDataForTransform 引用了不存在的`self.fields`成员。添加一个`self.fields`成员，设置为`price`和`volume`，并在检查过程中使用它进行迭代。

+   修正最大回撤计算。输入到最大回撤的值不正确，导致结果不佳。即`compounded_log_returns`并不代表算法在特定时间的总回报，尽管`calculate_max_drawdown`将其视为如此。现在改用`algorithm_period_returns`序列，它确实提供了总回报。

+   修正成本基础计算。成本基础计算现在考虑了交易的方向。关闭多头头寸或平仓空头头寸不应影响成本基础。

+   修正`order()`中的浮点错误。当订单金额接近整数时，可能会意外地被向下取整或向上取整（取决于正负）到错误的整数。例如，内部存储为-27.99999 的金额被转换为-27 而不是-28。

+   当持仓因拆分而改变时更新性能周期状态。否则，`self._position_amounts`将与 position.amount 等不同步。

+   修正使用精确日期时的下行系列计算错位。在努力使传递给风险模块的回报系列更加精确时暴露出的一个奇怪问题，回报与平均回报之间的系列比较不平衡，因为平均回报没有被屏蔽到下行数据点；然而，在大多数情况下，如果不是所有情况下，这都是通过调用`.valid()`来掩盖的，这在此次更改集中被移除了。

+   在使用 self.logger 之前检查它是否存在。`self.logger`被初始化为`None`，不能保证用户已经设置它，所以在尝试向它传递消息之前检查它是否存在。

+   防止性能跟踪器中的不同步市场关闭。在性能跟踪器被重置或修补以处理与预热实时数据的状态切换的情况下，性能跟踪器的`market_close`成员可能会与性能跟踪器确定的当前算法时间不同步。症状是股息从未触发，因为收盘检查不会与当前时间匹配。通过让交易模拟循环负责在每分钟模式下推进市场关闭，并将该值传递给性能跟踪器来修复，而不是让性能跟踪器也推进市场关闭。

+   修复多个累积和期间风险计算。预期会发生变化的计算包括：

    +   `cumulative.beta`

    +   `cumulative.alpha`

    +   `cumulative.information`

    +   `cumulative.sharpe`

    +   `period.sortino`

    风险计算如何变化，为期间和累积风险修复

    下行风险

    使用样本而非总体来计算标准差。

    添加一个四舍五入因子，以便在给定的时间间隔内，如果两个值接近，它们不会被计为下行值，这会影响下行差异标准差的分母。

    标准差类型

    全面地将标准差标准化为使用“样本”计算，而之前累积风险主要使用“总体”。使用`ddof=1`与`np.std`计算，就像值是样本一样。

    累积风险修复

    贝塔

    使用每日算法回报和基准而非年化平均回报。

    波动性

    使用样本而非总体来计算标准差。

    波动性是其他计算的输入，因此这一变化影响夏普比率和信息比率计算。

    信息比率

    基准回报输入已从年化基准回报更改为年化平均回报。

    阿尔法

    基准回报输入已从年化基准回报更改为年化平均回报。

    期间风险修复

    索提诺比率

    现在使用每日回报与平均算法回报的下行风险来计算最低可接受回报，而不是使用国债回报。

    上述更改需要添加计算期间风险的平均算法回报。

    此外，使用`algorithm_period_returns`和`tresaury_period_return`作为累积索提诺比率，而不是在索提诺计算中使用算法回报作为两个输入。

### 性能

+   移除了`alias_dt`转换，转而在 SIDData 上添加属性。通过`alias_dt`生成器将事件的 dt 字段复制为 datetime，以便 API 宽容并允许 SIDData 对象上同时存在 datetime 和 dt，这即使在 noop 算法中也产生了明显的开销。不再为每个通过系统传递的事件对象复制 datetime 值并分配给它，而是在 SIDData 上添加一个属性，该属性作为`dt`的别名`datetime`。最终可能会移除对`data['foo'].datetime`的支持，并可能被视为已弃用。

+   从累积回报中移除了“空回报”的丢弃。在每个单独的 bar 上检查空回报键的存在并丢弃该回报，在算法运行时增加了不必要的 CPU 时间。相反，在开始日期之前的交易日索引中添加 0.0 回报。移除`空回报`主要是为了确保周期计算不会因为非日期索引值而崩溃；由于索引为日期，周期回报也可以近似波动性（尽管该波动性具有高噪声信号强度，因为它仅使用两个值作为输入。）

### 维护和重构

+   允许`sim_params`为算法提供数据频率。如果算法的`data_frequency`为 None，允许`sim_params`提供`data_frequency`。

    此外，如果提供了算法的数据频率，则推迟到该频率。

### 构建

+   增加了通过 conda 进行构建和发布的支持。对于那些更喜欢使用[`docs.conda.io/en/latest/`](https://docs.conda.io/en/latest/)而不是使用 pip 本地编译的人来说。以下应该可以在许多系统上安装 Zipline。

    ```py
    conda install -c quantopian zipline 
    ```

### 贡献者

以下人员按提交次数顺序为本次发布做出了贡献：

```py
49  Eddie Hebert
28  Thomas Wiecki
11  Richard Frank
 2  Jamie Kirkpatrick
 2  Jeremiah Lowin
 1  Colin Alexander
 1  Michael Schatzow
 1  Moises Trovo
 1  Suminda Dharmasena 
```
