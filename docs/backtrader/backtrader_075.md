# PyFolio 概述

> 原文：[`www.backtrader.com/docu/analyzers/pyfolio/`](https://www.backtrader.com/docu/analyzers/pyfolio/)

注意

截至至少 2017-07-25，`pyfolio`的 API 已更改，`create_full_tear_sheet`不再具有`gross_lev`作为命名参数。

因此，集成的示例无法运行

引用主要`pyfolio`页面上的内容[`quantopian.github.io/pyfolio/`](http://quantopian.github.io/pyfolio/)：

```py
pyfolio is a Python library for performance and risk analysis of financial
portfolios developed by Quantopian Inc. It works well with the Zipline open
source backtesting library
```

现在它也与*backtrader*很好地配合。需要什么：

+   显然是`pyfolio`

+   以及它的依赖项（例如`pandas`，`seaborn` …）

    注意

    在与版本`0.5.1`集成期间，需要更新依赖项的最新软件包，例如从先前安装的`0.7.0-dev`到`0.7.1`的`seaborn`，显然是由于缺少`swarmplot`方法

## 用法

1.  将`PyFolio`分析器添加到`cerebro`混合中：

    ```py
    cerebro.addanalyzer(bt.analyzers.PyFolio)` 
    ```

1.  运行并检索第 1 个策略：

    ```py
    strats = cerebro.run()
    strat0 = strats[0]` 
    ```

1.  使用您指定的名称或默认名称`pyfolio`检索分析器。例如：

    ```py
    pyfolio = strats.analyzers.getbyname('pyfolio')` 
    ```

1.  使用分析器方法`get_pf_items`检索后续需要用于`pyfolio`的 4 个组件：

    ```py
    returns, positions, transactions, gross_lev = pyfoliozer.get_pf_items()` 
    ```

    !!! 注意

    ```py
    The integration was done looking at test samples available with
    `pyfolio` and the same headers (or absence of) has been replicated` 
    ```

1.  使用`pyfolio`（这已经超出了*backtrader*生态系统）

一些与*backtrader*无直接关系的使用说明

+   `pyfolio`自动绘图功能在*Jupyter Notebook*之外也可以工作，但**在内部效果最佳**。

+   `pyfolio`数据表的输出似乎在*Jupyter Notebook*之外几乎无法工作。它在*Notebook*内部工作

如果希望使用`pyfolio`，结论很简单：**在 Jupyter Notebook 内部工作**

## 示例代码

代码如下所示：

```py
...
cerebro.addanalyzer(bt.analyzers.PyFolio, _name='pyfolio')
...
results = cerebro.run()
strat = results[0]
pyfoliozer = strat.analyzers.getbyname('pyfolio')
returns, positions, transactions, gross_lev = pyfoliozer.get_pf_items()
...
...
# pyfolio showtime
import pyfolio as pf
pf.create_full_tear_sheet(
    returns,
    positions=positions,
    transactions=transactions,
    gross_lev=gross_lev,
    live_start_date='2005-05-01',  # This date is sample specific
    round_trips=True)

# At this point tables and chart will show up
```

### 参考

查看`PyFolio`分析器的参考资料以及它内部使用的分析器
