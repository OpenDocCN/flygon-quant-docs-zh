# 数据 - 多个时间框架

> 原文：[`www.backtrader.com/blog/posts/2015-08-24-data-multitimeframe/data-multitimeframe/`](https://www.backtrader.com/blog/posts/2015-08-24-data-multitimeframe/data-multitimeframe/)

有时，使用不同的时间框架进行投资决策：

+   周线用于评估趋势

+   每日执行进入

或者 5 分钟对比 60 分钟。

这意味着在 backtrader 中需要组合多个时间框架的数据以支持这样的组合。

本地支持已经内置。最终用户只需遵循以下规则：

+   具有最小时间框架的数据（因此是较大数量的条）必须是添加到 Cerebro 实例的第一个数据

+   数据必须正确地对齐日期时间，以便平台能够理解它们的任何含义

此外，最终用户可以自由地在较短/较大的时间框架上应用指标。当然：

+   应用于较大时间框架的指标将产生较少的条

平台也将考虑以下内容

+   较大时间框架的最小周期

最小周期可能会导致在策略添加到 Cerebro 之前需要消耗几个数量级的较小时间框架的数据。

内置的`DataResampler`将用于创建较大的时间框架。

一些示例如下，但首先是测试脚本的来源。

```py
 `# Load the Data
    datapath = args.dataname or '../datas/sample/2006-day-001.txt'
    data = btfeeds.BacktraderCSVData(
        dataname=datapath)

    tframes = dict(
        daily=bt.TimeFrame.Days,
        weekly=bt.TimeFrame.Weeks,
        monthly=bt.TimeFrame.Months)

    # Handy dictionary for the argument timeframe conversion
    # Resample the data
    if args.noresample:
        datapath = args.dataname2 or '../datas/sample/2006-week-001.txt'
        data2 = btfeeds.BacktraderCSVData(
            dataname=datapath)
    else:
        data2 = bt.DataResampler(
            dataname=data,
            timeframe=tframes[args.timeframe],
            compression=args.compression)` 
```

步骤：

+   加载数据

+   根据用户指定的参数重新采样它

    脚本还允许加载第二个数据

+   将数据添加到 cerebro

+   将重新采样的数据（较大的时间框架）添加到 cerebro

+   运行

## 示例 1 - 每日和每周

脚本的调用：

```py
`$ ./data-multitimeframe.py --timeframe weekly --compression 1` 
```

和输出图表：

![image](img/cbbf6c75d90c0e12f1f6240477becc81.png)

## 示例 2 - 日间和日间压缩（2 根变成 1 根）

脚本的调用：

```py
`$ ./data-multitimeframe.py --timeframe daily --compression 2` 
```

和输出图表：

![image](img/096f133f671bb9ca74ee1c03d1d1731c.png)

## 示例 3 - 带有 SMA 的策略

虽然绘图很好，但这里的关键问题是显示较大的时间框架如何影响系统，特别是当涉及到起始点时

脚本可以采用`--indicators`来添加一个策略，该策略在较小时间框架和较大时间框架的数据上创建**10 周期**的简单移动平均线。

如果只考虑较小的时间框架：

+   `next`将在 10 个条之后首先被调用，这是简单移动平均需要产生值的时间

    注意

    请记住，策略监视创建的指标，并且只有在所有指标都产生值时才调用`next`。理由是最终用户已经添加了指标以在逻辑中使用它们，因此如果指标尚未产生值，则不应进行任何逻辑

但在这种情况下，较大的时间框架（每周）会延迟调用`next`，直到每周数据的简单移动平均产生值为止，这需要... 10 周。

脚本覆盖了`nextstart`，它只被调用一次，默认调用`next`以显示首次调用的时间。

### 调用 1：

只有较小的时间框架，即每日，才有一个简单移动平均值。

命令行和输出

```py
`$ ./data-multitimeframe.py --timeframe weekly --compression 1 --indicators --onlydaily
--------------------------------------------------
nextstart called with len 10
--------------------------------------------------` 
```

以及图表。

![图片](img/9788762f422a669cd82b44aeb072e0ac.png)

### 调用 2：

两个时间框架都有一个简单移动平均。

命令行：

```py
`$ ./data-multitimeframe.py --timeframe weekly --compression 1 --indicators
--------------------------------------------------
nextstart called with len 50
--------------------------------------------------
--------------------------------------------------
nextstart called with len 51
--------------------------------------------------
--------------------------------------------------
nextstart called with len 52
--------------------------------------------------
--------------------------------------------------
nextstart called with len 53
--------------------------------------------------
--------------------------------------------------
nextstart called with len 54
--------------------------------------------------` 
```

注意这里的两件事：

+   不是在**10**个周期之后被调用，而是在 50 个周期之后第一次被调用。

    这是因为在较大（周）时间框架上应用简单移动平均值后产生了一个值，... 这是 10 周* 5 天/周... 50 天。

+   `nextstart`被调用了 5 次，而不是仅 1 次。

    这是将时间框架混合并（在这种情况下仅有一个）指标应用于较大时间框架的自然副作用。

    较大时间框架的简单移动平均值在消耗 5 个日间条时产生 5 倍相同的值。

    由于周期的开始由较大的时间框架控制，`nextstart`被调用了 5 次。

以及图表。

![图片](img/d7e56d0e27f93d43fc62a0dfc6e0ace5.png)

## 结论

多时间框架数据可以在`backtrader`中使用，无需特殊对象或调整：只需先添加较小的时间框架。

测试脚本。

```py
`from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse

import backtrader as bt
import backtrader.feeds as btfeeds
import backtrader.indicators as btind

class SMAStrategy(bt.Strategy):
    params = (
        ('period', 10),
        ('onlydaily', False),
    )

    def __init__(self):
        self.sma_small_tf = btind.SMA(self.data, period=self.p.period)
        if not self.p.onlydaily:
            self.sma_large_tf = btind.SMA(self.data1, period=self.p.period)

    def nextstart(self):
        print('--------------------------------------------------')
        print('nextstart called with len', len(self))
        print('--------------------------------------------------')

        super(SMAStrategy, self).nextstart()

def runstrat():
    args = parse_args()

    # Create a cerebro entity
    cerebro = bt.Cerebro(stdstats=False)

    # Add a strategy
    if not args.indicators:
        cerebro.addstrategy(bt.Strategy)
    else:
        cerebro.addstrategy(
            SMAStrategy,

            # args for the strategy
            period=args.period,
            onlydaily=args.onlydaily,
        )

    # Load the Data
    datapath = args.dataname or '../datas/sample/2006-day-001.txt'
    data = btfeeds.BacktraderCSVData(
        dataname=datapath)

    tframes = dict(
        daily=bt.TimeFrame.Days,
        weekly=bt.TimeFrame.Weeks,
        monthly=bt.TimeFrame.Months)

    # Handy dictionary for the argument timeframe conversion
    # Resample the data
    if args.noresample:
        datapath = args.dataname2 or '../datas/sample/2006-week-001.txt'
        data2 = btfeeds.BacktraderCSVData(
            dataname=datapath)
    else:
        data2 = bt.DataResampler(
            dataname=data,
            timeframe=tframes[args.timeframe],
            compression=args.compression)

    # First add the original data - smaller timeframe
    cerebro.adddata(data)

    # And then the large timeframe
    cerebro.adddata(data2)

    # Run over everything
    cerebro.run()

    # Plot the result
    cerebro.plot(style='bar')

def parse_args():
    parser = argparse.ArgumentParser(
        description='Pandas test script')

    parser.add_argument('--dataname', default='', required=False,
                        help='File Data to Load')

    parser.add_argument('--dataname2', default='', required=False,
                        help='Larger timeframe file to load')

    parser.add_argument('--noresample', action='store_true',
                        help='Do not resample, rather load larger timeframe')

    parser.add_argument('--timeframe', default='weekly', required=False,
                        choices=['daily', 'weekly', 'monhtly'],
                        help='Timeframe to resample to')

    parser.add_argument('--compression', default=1, required=False, type=int,
                        help='Compress n bars into 1')

    parser.add_argument('--indicators', action='store_true',
                        help='Wether to apply Strategy with indicators')

    parser.add_argument('--onlydaily', action='store_true',
                        help='Indicator only to be applied to daily timeframe')

    parser.add_argument('--period', default=10, required=False, type=int,
                        help='Period to apply to indicator')

    return parser.parse_args()

if __name__ == '__main__':
    runstrat()` 
```
