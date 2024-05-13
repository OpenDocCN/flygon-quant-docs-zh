# plotter – 策略绘图器

> 原文：[`gbeced.github.io/pyalgotrade/docs/v0.20/html/plotter.html`](https://gbeced.github.io/pyalgotrade/docs/v0.20/html/plotter.html)

*class* `pyalgotrade.plotter.``Subplot`

基类: `object`

`addCallback`(*label*, *callback*, *defaultClass=<class 'pyalgotrade.plotter.LineMarker'>*)

添加一个在每个 bar 上调用的回调。

| 参数: |
| --- |

+   **label** (*string.*) – 系列值的名称。

+   **callback** – 一个接收`pyalgotrade.bar.Bars`实例作为参数并返回一个数字或 None 的函数。

|

`addDataSeries`(*label*, *dataSeries*, *defaultClass=<class 'pyalgotrade.plotter.LineMarker'>*)

将一个 DataSeries 添加到 subplot 中。

| 参数: |
| --- |

+   **label** (*string.*) – DataSeries 值的名称。

+   **dataSeries** (`pyalgotrade.dataseries.DataSeries`.) – 要添加的 DataSeries。

|

`addLine`(*label*, *level*)

将水平线添加到绘图中。

| 参数: |
| --- |

+   **label** (*string.*) – 一个标签。

+   **level** (*int/float.*) – 线的位置。

|

*class* `pyalgotrade.plotter.``InstrumentSubplot`(*instrument*, *plotBuySell*)

基类: `pyalgotrade.plotter.Subplot`

负责绘制一个仪器的 Subplot。

*class* `pyalgotrade.plotter.``StrategyPlotter`(*strat*, *plotAllInstruments=True*, *plotBuySell=True*, *plotPortfolio=True*)

基类: `object`

负责绘制策略执行的类。

| 参数: |
| --- |

+   **strat** (`pyalgotrade.strategy.BaseStrategy`.) – 要绘制的策略。

+   **plotAllInstruments** (*boolean.*) – 设置为 True 以获取每个可用仪器的子图。

+   **plotBuySell** (*boolean.*) – 设置为 True 以绘制每个可用仪器的买入/卖出事件。

+   **plotPortfolio** (*boolean.*) – 设置为 True 以绘制投资组合价值（股票+现金）。

|

`buildFigureAndSubplots`(*fromDateTime=None*, *toDateTime=None*, *postPlotFun=<function _post_plot_fun at 0x108da0b18>*)

使用子图构建一个 matplotlib.figure.Figure。必须在运行策略后调用。

| 参数: |
| --- |

+   **fromDateTime** (*datetime.datetime*) – 可选的开始 datetime.datetime。之前的所有内容都不会被绘制。

+   **toDateTime** (*datetime.datetime*) – 可选的结束 datetime.datetime。之后的所有内容都不会被绘制。

|

| 返回类型: | 一个包含 matplotlib.figure.Figure 和 subplots 的二元组。 |
| --- | --- |

`getInstrumentSubplot`(*instrument*)

返回给定仪器的 InstrumentSubplot

| 返回类型: | `InstrumentSubplot`. |
| --- | --- |

`getOrCreateSubplot`(*name*)

根据名称返回 Subplot。如果子图不存在，则会创建。

| 参数: | **name** (*字符串.*) – 要获取或创建的子图的名称。 |
| --- | --- |
| 返回类型: | `子图`. |

`getPortfolioSubplot`()

返回投资组合价值被绘制的子图。

| 返回类型: | `子图`. |
| --- | --- |

`plot`(*fromDateTime=None*, *toDateTime=None*, *postPlotFun=<function _post_plot_fun at 0x108da0b18>*)

绘制策略执行。必须在运行策略后调用。

| 参数: |
| --- |

+   **fromDateTime** (*datetime.datetime*) – 可选的起始日期时间。该日期时间之前的所有内容都不会被绘制。

+   **toDateTime** (*datetime.datetime*) – 可选的结束日期时间。该日期时间之后的所有内容都不会被绘制。

|

`savePlot`(*filename*, *dpi=None*, *format='png'*, *fromDateTime=None*, *toDateTime=None*)

将策略执行绘制到文件中。必须在运行策略后调用。

| 参数: |
| --- |

+   **filename** – 文件名。

+   **dpi** – 每英寸点数（dots per inch）的分辨率。

+   **format** – 文件扩展名。

+   **fromDateTime** (*datetime.datetime*) – 可选的起始日期时间。该日期时间之前的所有内容都不会被绘制。

+   **toDateTime** (*datetime.datetime*) – 可选的结束日期时间。该日期时间之后的所有内容都不会被绘制。

|

#### 上一个主题

策略分析器 – Strategy analyzers

#### 下一个主题

优化器 – 并行优化器

### 此页面

+   显示源代码

### 快速搜索

输入搜索词或模块、类或函数名称。

### 导航

+   索引

+   模块 |

+   下一个 |

+   上一个 |

+   PyAlgoTrade 0.20 文档 »

+   代码文档 »
