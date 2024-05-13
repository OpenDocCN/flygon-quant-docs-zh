# 回测您的交易策略

> 原文：[`zipline.ml4trading.io/index.html`](https://zipline.ml4trading.io/index.html)

Zipline 是一个用于回测的 Pythonic 事件驱动系统，由[众包投资基金 Quantopian](https://www.bizjournals.com/boston/news/2020/11/10/quantopian-shuts-down-cofounders-head-elsewhere.html)开发和使用，作为回测和实时交易引擎。自 2020 年底关闭以来，托管这些文档的域名已过期。该库在[Stefan Jansen](https://www.linkedin.com/in/applied-ai/)所著的[《机器学习算法交易》](https://ml4trading.io)一书中被广泛使用，他正努力保持该库的更新，并使其对读者和更广泛的 Python 算法交易社区可用。

+   [加入我们的社区！](https://exchange.ml4trading.io)

+   [文档](https://zipline.ml4trading.io)

## 功能

+   **易用性：** Zipline 试图不干扰您，以便您可以专注于算法开发。下面是一个代码示例。

+   **即用型：** 许多常见的统计数据，如移动平均和线性回归，可以直接从用户编写的算法中访问。

+   **PyData 集成：** 历史数据的输入和性能统计的输出基于 Pandas DataFrames，以便与现有的 PyData 生态系统良好集成。

+   **统计和机器学习库：** 您可以使用 matplotlib、scipy、statsmodels 和 scikit-learn 等库来支持开发、分析和可视化最先进的交易系统。

> **注意：** 版本 3.0 更新了 Zipline 以使用[pandas](https://pandas.pydata.org/pandas-docs/stable/whatsnew/v2.0.0.html) >= 2.0 和[SQLAlchemy](https://docs.sqlalchemy.org/en/20/) > 2.0。这些是重大版本更新，可能会破坏现有代码；请查看链接的文档。
> 
> **注意：** 版本 2.4 更新了 Zipline 以使用[exchange_calendars](https://github.com/gerrymanoim/exchange_calendars) >= 4.2。这是一个重大版本更新，可能会破坏现有代码（我们已尽力避免，但不能保证）。请在此处查看更改：[`github.com/gerrymanoim/exchange_calendars/issues/61`](https://github.com/gerrymanoim/exchange_calendars/issues/61)。

## 安装

Zipline 支持 Python >= 3.8，并与当前版本的[NumFOCUS](https://numfocus.org/sponsored-projects?_sft_project_category=python-interface)相关库兼容，包括[pandas](https://pandas.pydata.org/)和[scikit-learn](https://scikit-learn.org/stable/index.html)。

### 使用`pip`

如果您的系统满足[安装说明](https://zipline.ml4trading.io/install.html)中描述的先决条件，您可以使用`pip`通过运行以下命令来安装 Zipline：

```py
pip  install  zipline-reloaded 
```

### 使用`conda`

如果您使用的是[Anaconda](https://www.anaconda.com/products/individual)或[miniconda](https://docs.conda.io/en/latest/miniconda.html)发行版，您可以从`conda-forge`频道这样安装`zipline-reloaded`：

```py
conda  install  -c  conda-forge  zipline-reloaded 
```

您还可以通过在`.condarc`中列出它来[启用](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-channels.html) `conda-forge`。

如果您在安装`zipline-reloaded`时与其他包一起安装并遇到[冲突错误](https://github.com/conda/conda/issues/9707)，请考虑使用[mamba](https://github.com/mamba-org/mamba)代替。

如需更详细的安装说明，请参阅文档的[安装](https://zipline.ml4trading.io/install.html)部分，以及相应的[conda-forge 网站](https://github.com/conda-forge/zipline-reloaded-feedstock)。

## 快速入门

请参阅我们的[入门教程](https://zipline.ml4trading.io/beginner-tutorial)。

以下代码实现了一个简单的双移动平均算法。

```py
from zipline.api import order_target, record, symbol

def initialize(context):
    context.i = 0
    context.asset = symbol('AAPL')

def handle_data(context, data):
    # Skip first 300 days to get full windows
    context.i += 1
    if context.i < 300:
        return

    # Compute averages
    # data.history() has to be called with the same params
    # from above and returns a pandas dataframe.
    short_mavg = data.history(context.asset, 'price', bar_count=100, frequency="1d").mean()
    long_mavg = data.history(context.asset, 'price', bar_count=300, frequency="1d").mean()

    # Trading logic
    if short_mavg > long_mavg:
        # order_target orders as many shares as needed to
        # achieve the desired number of shares.
        order_target(context.asset, 100)
    elif short_mavg < long_mavg:
        order_target(context.asset, 0)

    # Save values for later inspection
    record(AAPL=data.current(context.asset, 'price'),
           short_mavg=short_mavg,
           long_mavg=long_mavg) 
```

您可以使用 Zipline CLI 运行此算法。但首先，您需要下载一些包含历史价格和交易量的市场数据：

```py
$  zipline  ingest  -b  quandl
$  zipline  run  -f  dual_moving_average.py  --start  2014-1-1  --end  2018-1-1  -o  dma.pickle  --no-benchmark 
```

这将下载来自[Quandl](https://www.quandl.com/databases/WIKIP/documentation?anchor=companies)（自[收购](https://www.nasdaq.com/about/press-center/nasdaq-acquires-quandl-advance-use-alternative-data)后由 NASDAQ 托管）的资产定价数据，并在指定的时间范围内通过算法流式传输。然后，将生成的性能 DataFrame 保存为`dma.pickle`，您可以从 Python 加载并分析它。

您可以在[zipline/examples](https://github.com/stefan-jansen/zipline-reloaded/tree/main/src/zipline/examples)目录中找到其他示例。

## 有问题、建议或发现错误？

如果您发现错误或对库有其他问题，请随时[打开一个问题](https://github.com/stefan-jansen/zipline/issues/new)并填写模板。

+   安装

+   教程

+   数据

+   日历

+   指标

+   开发

+   API

+   版本

## 功能

+   **易用性：**Zipline 试图不干扰您，以便您可以专注于算法开发。请参见下面的代码示例。

+   **内置功能：**许多常见统计方法，如移动平均和线性回归，都可以在用户编写的算法中直接访问。

+   **PyData 集成：**历史数据的输入和性能统计的输出基于 Pandas DataFrames，以便与现有的 PyData 生态系统很好地集成。

+   **统计和机器学习库：**您可以使用 matplotlib、scipy、statsmodels 和 scikit-learn 等库来支持开发、分析和可视化最先进的交易系统。

> **注意：**版本 3.0 更新了 Zipline 以使用[pandas](https://pandas.pydata.org/pandas-docs/stable/whatsnew/v2.0.0.html) >= 2.0 和[SQLAlchemy](https://docs.sqlalchemy.org/en/20/) > 2.0。这些都是重大版本更新，可能会破坏现有代码；请查看链接的文档。
> 
> **注意：** 2.4 版本更新了 Zipline，使其使用 [exchange_calendars](https://github.com/gerrymanoim/exchange_calendars) >= 4.2。这是一个重大版本更新，可能会破坏现有代码（我们已尽力避免，但不能保证）。请在此处查看更改内容：[这里](https://github.com/gerrymanoim/exchange_calendars/issues/61)。

## 安装

Zipline 支持 Python >= 3.8，并与当前版本的[NumFOCUS](https://numfocus.org/sponsored-projects?_sft_project_category=python-interface)相关库兼容，包括[pandas](https://pandas.pydata.org/)和[scikit-learn](https://scikit-learn.org/stable/index.html)。

### 使用`pip`

如果您的系统满足[安装说明](https://zipline.ml4trading.io/install.html)中描述的先决条件，您可以通过运行以下命令使用`pip`安装 Zipline：

```py
pip  install  zipline-reloaded 
```

### 使用`conda`

如果您使用的是[Anaconda](https://www.anaconda.com/products/individual)或[miniconda](https://docs.conda.io/en/latest/miniconda.html)发行版，您可以从`conda-forge`频道这样安装`zipline-reloaded`：

```py
conda  install  -c  conda-forge  zipline-reloaded 
```

您还可以通过在您的`.condarc`中列出它来[启用](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-channels.html) `conda-forge`。

如果您在安装`zipline-reloaded`时与其他包一起安装并遇到[冲突错误](https://github.com/conda/conda/issues/9707)，请考虑改用[mamba](https://github.com/mamba-org/mamba)。

请参阅文档的[安装](https://zipline.ml4trading.io/install.html)部分，了解更详细的说明以及相应的[conda-forge 站点](https://github.com/conda-forge/zipline-reloaded-feedstock)。

### 使用`pip`

如果您的系统满足[安装说明](https://zipline.ml4trading.io/install.html)中描述的先决条件，您可以通过运行以下命令使用`pip`安装 Zipline：

```py
pip  install  zipline-reloaded 
```

### 使用`conda`

如果您使用的是[Anaconda](https://www.anaconda.com/products/individual)或[miniconda](https://docs.conda.io/en/latest/miniconda.html)发行版，您可以从`conda-forge`频道这样安装`zipline-reloaded`：

```py
conda  install  -c  conda-forge  zipline-reloaded 
```

您还可以通过在您的`.condarc`中列出它来[启用](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-channels.html) `conda-forge`。

如果您在安装`zipline-reloaded`时与其他包一起安装并遇到[冲突错误](https://github.com/conda/conda/issues/9707)，请考虑改用[mamba](https://github.com/mamba-org/mamba)。

请参阅文档的[安装](https://zipline.ml4trading.io/install.html)部分，了解更详细的说明以及相应的[conda-forge 站点](https://github.com/conda-forge/zipline-reloaded-feedstock)。

## 快速入门

请参阅我们的[入门教程](https://zipline.ml4trading.io/beginner-tutorial)。

以下代码实现了一个简单的双移动平均算法。

```py
from zipline.api import order_target, record, symbol

def initialize(context):
    context.i = 0
    context.asset = symbol('AAPL')

def handle_data(context, data):
    # Skip first 300 days to get full windows
    context.i += 1
    if context.i < 300:
        return

    # Compute averages
    # data.history() has to be called with the same params
    # from above and returns a pandas dataframe.
    short_mavg = data.history(context.asset, 'price', bar_count=100, frequency="1d").mean()
    long_mavg = data.history(context.asset, 'price', bar_count=300, frequency="1d").mean()

    # Trading logic
    if short_mavg > long_mavg:
        # order_target orders as many shares as needed to
        # achieve the desired number of shares.
        order_target(context.asset, 100)
    elif short_mavg < long_mavg:
        order_target(context.asset, 0)

    # Save values for later inspection
    record(AAPL=data.current(context.asset, 'price'),
           short_mavg=short_mavg,
           long_mavg=long_mavg) 
```

然后，您可以使用 Zipline CLI 运行此算法。但首先，您需要下载一些具有历史价格和交易量的市场数据：

```py
$  zipline  ingest  -b  quandl
$  zipline  run  -f  dual_moving_average.py  --start  2014-1-1  --end  2018-1-1  -o  dma.pickle  --no-benchmark 
```

这将下载来自[Quandl](https://www.quandl.com/databases/WIKIP/documentation?anchor=companies)（自[收购](https://www.nasdaq.com/about/press-center/nasdaq-acquires-quandl-advance-use-alternative-data)后由纳斯达克托管）的资产定价数据，并在指定的时间范围内通过算法流式传输。然后，将生成的性能 DataFrame 保存为`dma.pickle`，您可以从 Python 加载并分析它。

您可以在[zipline/examples](https://github.com/stefan-jansen/zipline-reloaded/tree/main/src/zipline/examples)目录中找到其他示例。

## 有问题、建议、错误吗？

如果您发现错误或有关于库的其他问题，请随时[打开一个问题](https://github.com/stefan-jansen/zipline/issues/new)并填写模板。

+   安装

+   教程

+   数据

+   日历

+   指标

+   开发

+   API

+   发布
