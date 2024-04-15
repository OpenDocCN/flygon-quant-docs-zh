# Pandas DataFeed 支持

> 原文：[`www.backtrader.com/blog/posts/2015-08-21-pandas-datafeed/pandas-datafeed/`](https://www.backtrader.com/blog/posts/2015-08-21-pandas-datafeed/pandas-datafeed/)

在一些小的增强和一些有序字典调整以更好地支持 Python 2.6 的情况下，backtrader 的最新版本增加了对从 Pandas Dataframe 或时间序列分析数据的支持。

注意

显然必须安装 `pandas` 及其依赖项。

这似乎引起了很多人的关注，他们依赖于已经可用的用于不同数据源（包括 CSV）的解析代码。

```py
`class PandasData(feed.DataBase):
    '''
    The ``dataname`` parameter inherited from ``feed.DataBase``  is the pandas
    Time Series
    '''

    params = (
        # Possible values for datetime (must always be present)
        #  None : datetime is the "index" in the Pandas Dataframe
        #  -1 : autodetect position or case-wise equal name
        #  >= 0 : numeric index to the colum in the pandas dataframe
        #  string : column name (as index) in the pandas dataframe
        ('datetime', None),

        # Possible values below:
        #  None : column not present
        #  -1 : autodetect position or case-wise equal name
        #  >= 0 : numeric index to the colum in the pandas dataframe
        #  string : column name (as index) in the pandas dataframe
        ('open', -1),
        ('high', -1),
        ('low', -1),
        ('close', -1),
        ('volume', -1),
        ('openinterest', -1),
    )` 
```

上述从 `PandasData` 类中摘录的片段显示了键：

+   实例化期间 `dataname` 参数对应 Pandas Dataframe

    此参数继承自基类 `feed.DataBase`

+   新参数使用了 `DataSeries` 中常规字段的名称，并遵循这些约定

    +   `datetime`（默认：无）

    +   无：datetime 是 Pandas Dataframe 中的“index”

    +   -1：自动检测位置或大小写相等的名称

    +   > = 0：Pandas 数据框中列的数值索引

    +   string：Pandas 数据框中的列名（作为索引）

    +   `open`, `high`, `low`, `high`, `close`, `volume`, `openinterest`（默认：全部为 -1）

    +   无：列不存在

    +   -1：自动检测位置或大小写相等的名称

    +   > = 0：Pandas 数据框中列的数值索引

    +   string：Pandas 数据框中的列名（作为索引）

一个小的样本应该能够加载标准 2006 样本，已被 `Pandas` 解析，而不是直接由 `backtrader` 解析。

运行示例以使用 CSV 数据中的现有“headers”：

```py
`$ ./panda-test.py
--------------------------------------------------
               Open     High      Low    Close  Volume  OpenInterest
Date
2006-01-02  3578.73  3605.95  3578.73  3604.33       0             0
2006-01-03  3604.08  3638.42  3601.84  3614.34       0             0
2006-01-04  3615.23  3652.46  3615.23  3652.46       0             0` 
```

相同，但告诉脚本跳过标题：

```py
`$ ./panda-test.py --noheaders
--------------------------------------------------
                  1        2        3        4  5  6
0
2006-01-02  3578.73  3605.95  3578.73  3604.33  0  0
2006-01-03  3604.08  3638.42  3601.84  3614.34  0  0
2006-01-04  3615.23  3652.46  3615.23  3652.46  0  0` 
```

第二次运行是使用 tells `pandas.read_csv`：

+   跳过第一个输入行（`skiprows` 关键字参数设置为 1）

+   不要寻找标题行（`header` 关键字参数设置为 None）

backtrader 对 Pandas 的支持尝试自动检测是否已使用列名，否则使用数值索引，并相应地采取行动，尝试提供最佳匹配。

以下图表是成功的致敬。Pandas Dataframe 已被正确加载（在两种情况下）。

![image](img/6b486ca11d70278e98d124393346cf13.png)

测试的示例代码。

```py
`from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse

import backtrader as bt
import backtrader.feeds as btfeeds

import pandas

def runstrat():
    args = parse_args()

    # Create a cerebro entity
    cerebro = bt.Cerebro(stdstats=False)

    # Add a strategy
    cerebro.addstrategy(bt.Strategy)

    # Get a pandas dataframe
    datapath = ('../datas/sample/2006-day-001.txt')

    # Simulate the header row isn't there if noheaders requested
    skiprows = 1 if args.noheaders else 0
    header = None if args.noheaders else 0

    dataframe = pandas.read_csv(datapath,
                                skiprows=skiprows,
                                header=header,
                                parse_dates=True,
                                index_col=0)

    if not args.noprint:
        print('--------------------------------------------------')
        print(dataframe)
        print('--------------------------------------------------')

    # Pass it to the backtrader datafeed and add it to the cerebro
    data = bt.feeds.PandasData(dataname=dataframe)

    cerebro.adddata(data)

    # Run over everything
    cerebro.run()

    # Plot the result
    cerebro.plot(style='bar')

def parse_args():
    parser = argparse.ArgumentParser(
        description='Pandas test script')

    parser.add_argument('--noheaders', action='store_true', default=False,
                        required=False,
                        help='Do not use header rows')

    parser.add_argument('--noprint', action='store_true', default=False,
                        help='Print the dataframe')

    return parser.parse_args()

if __name__ == '__main__':
    runstrat()` 
```
