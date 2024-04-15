# 变异性加权回报（或 VWR）

> 原文：[`www.backtrader.com/blog/posts/2016-09-06-vwr/vwr/`](https://www.backtrader.com/blog/posts/2016-09-06-vwr/vwr/)

在一些关于*改进*SharpeRatio 的提示之后，*backtrader*将这个*分析器*添加到其工具库中。

文献位于：

+   [`www.crystalbull.com/sharpe-ratio-better-with-log-returns/`](https://www.crystalbull.com/sharpe-ratio-better-with-log-returns/)

从对数收益的好处开始，然后讨论将*标准差*放在 SharpeRatio 方程的分母中的副作用，文档展开了这个*分析器*的公式和期望。

最重要的特性之一可能是：

+   *在时间框架上一致的数值*

`SharpeRatio`使用超额收益与无风险利率/资产的算术平均值除以超额收益与无风险利率/资产的*标准差*。这使得最终数值取决于样本数量和标准差，甚至可能为`0`。在这种情况下，`SharpeRatio`将为无穷大。

*backtrader*包含一个用于测试`SharpeRatio`的样本，使用包含`2005`和`2006`价格的样本数据。不同*时间框架*的返回值为：

+   `TimeFrame.Years`: `11.6473`

+   `TimeFrame.Months`: `0.5425`

+   `TimeFrame.Weeks`: `0.457`

+   `TimeFrame.Days`: `0.4274`

注意

为了保持一致性，比率是年化的。`sharpe-timereturn`样本并使用以下方式执行：

```py
--annualize --timeframe xxx
```

`xxx`代表`天`、`周`、`月`或`年`（默认）

在这个样本中有一点很明显：

+   *时间框架*越小，`SharpeRatio`值越小

这是由于较小*时间框架*的样本数量较大，增加了*变异性*，从而增加了`SharpeRatio`方程中的分母——*标准差*。

对*标准差*的变化非常敏感

正是这一点，`VWR`试图通过在*时间框架*上提供一致的数值来解决。同一策略提供以下数值：

+   `TimeFrame.Years`: `1.5368`

+   `TimeFrame.Months`: `1.5163`

+   `TimeFrame.Weeks`: `1.5383`

+   `TimeFrame.Days`: `1.5221`

注意

`VWR`（遵循文献）始终以年化形式返回。样本使用以下方式执行：

```py
--timeframe xxx
```

`xxx`代表`天`、`周`、`月`或`年`

默认值为`None`，使用数据的基础*时间框架*为`天`

一致的数值表明，当涉及提供一致回报时，策略的表现可以在任何时间框架上进行评估。

注意

理论上，数值应该是相同的，但这需要对`tann`参数（年化期数）进行微调，以匹配确切的交易期。这里没有这样做，因为目的只是看一致性。

## 结论

为用户提供了一种独立于时间框架的策略评估方法的新工具

## 样本用法

```py
$ ./vwr.py --help
usage: vwr.py [-h] [--data DATA] [--cash CASH] [--fromdate FROMDATE]
              [--todate TODATE] [--writercsv]
              [--tframe {weeks,months,days,years}] [--sigma-max SIGMA_MAX]
              [--tau TAU] [--tann TANN] [--stddev-sample] [--plot [kwargs]]

TimeReturns and VWR

optional arguments:
  -h, --help            show this help message and exit
  --data DATA, -d DATA  data to add to the system (default:
                        ../../datas/2005-2006-day-001.txt)
  --cash CASH           Starting Cash (default: None)
  --fromdate FROMDATE, -f FROMDATE
                        Starting date in YYYY-MM-DD format (default: None)
  --todate TODATE, -t TODATE
                        Starting date in YYYY-MM-DD format (default: None)
  --writercsv, -wcsv    Tell the writer to produce a csv stream (default:
                        False)
  --tframe {weeks,months,days,years}, --timeframe {weeks,months,days,years}
                        TimeFrame for the Returns/Sharpe calculations
                        (default: None)
  --sigma-max SIGMA_MAX
                        VWR Sigma Max (default: None)
  --tau TAU             VWR tau factor (default: None)
  --tann TANN           Annualization factor (default: None)
  --stddev-sample       Consider Bessels correction for stddeviation (default:
                        False)
  --plot [kwargs], -p [kwargs]
                        Plot the read data applying any kwargs passed For
                        example: --plot style="candle" (to plot candles)
                        (default: None)
```

## 样本代码

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
import datetime

import backtrader as bt

TFRAMES = dict(
    days=bt.TimeFrame.Days,
    weeks=bt.TimeFrame.Weeks,
    months=bt.TimeFrame.Months,
    years=bt.TimeFrame.Years)

def runstrat(pargs=None):
    args = parse_args(pargs)

    # Create a cerebro
    cerebro = bt.Cerebro()

    if args.cash is not None:
        cerebro.broker.set_cash(args.cash)

    dkwargs = dict()
    # Get the dates from the args
    if args.fromdate is not None:
        fromdate = datetime.datetime.strptime(args.fromdate, '%Y-%m-%d')
        dkwargs['fromdate'] = fromdate
    if args.todate is not None:
        todate = datetime.datetime.strptime(args.todate, '%Y-%m-%d')
        dkwargs['todate'] = todate

    # Create the 1st data
    data = bt.feeds.BacktraderCSVData(dataname=args.data, **dkwargs)
    cerebro.adddata(data)  # Add the data to cerebro

    cerebro.addstrategy(bt.strategies.SMA_CrossOver)  # Add the strategy

    lrkwargs = dict()
    if args.tframe is not None:
        lrkwargs['timeframe'] = TFRAMES[args.tframe]

    if args.tann is not None:
        lrkwargs['tann'] = args.tann

    cerebro.addanalyzer(bt.analyzers.Returns, **lrkwargs)  # Returns

    vwrkwargs = dict()
    if args.tframe is not None:
        vwrkwargs['timeframe'] = TFRAMES[args.tframe]

    if args.tann is not None:
        vwrkwargs['tann'] = args.tann

    if args.sigma_max is not None:
        vwrkwargs['sigma_max'] = args.sigma_max

    if args.tau is not None:
        vwrkwargs['tau'] = args.tau

    cerebro.addanalyzer(bt.analyzers.VWR, **vwrkwargs)  # VWR Analyzer

    # Add a writer to get output
    cerebro.addwriter(bt.WriterFile, csv=args.writercsv, rounding=4)

    cerebro.run()  # And run it

    # Plot if requested
    if args.plot:
        pkwargs = dict(style='bar')
        if args.plot is not True:  # evals to True but is not True
            npkwargs = eval('dict(' + args.plot + ')')  # args were passed
            pkwargs.update(npkwargs)

        cerebro.plot(**pkwargs)

def parse_args(pargs=None):
    parser = argparse.ArgumentParser(
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
        description='TimeReturns and SharpeRatio')

    parser.add_argument('--data', '-d',
                        default='../../datas/2005-2006-day-001.txt',
                        help='data to add to the system')

    parser.add_argument('--cash', default=None, type=float, required=False,
                        help='Starting Cash')

    parser.add_argument('--fromdate', '-f',
                        default=None,
                        help='Starting date in YYYY-MM-DD format')

    parser.add_argument('--todate', '-t',
                        default=None,
                        help='Starting date in YYYY-MM-DD format')

    parser.add_argument('--writercsv', '-wcsv', action='store_true',
                        help='Tell the writer to produce a csv stream')

    parser.add_argument('--tframe', '--timeframe', default=None,
                        required=False, choices=TFRAMES.keys(),
                        help='TimeFrame for the Returns/Sharpe calculations')

    parser.add_argument('--sigma-max', required=False, action='store',
                        type=float, default=None,
                        help='VWR Sigma Max')

    parser.add_argument('--tau', required=False, action='store',
                        type=float, default=None,
                        help='VWR tau factor')

    parser.add_argument('--tann', required=False, action='store',
                        type=float, default=None,
                        help=('Annualization factor'))

    parser.add_argument('--stddev-sample', required=False, action='store_true',
                        help='Consider Bessels correction for stddeviation')

    # Plot options
    parser.add_argument('--plot', '-p', nargs='?', required=False,
                        metavar='kwargs', const=True,
                        help=('Plot the read data applying any kwargs passed\n'
                              '\n'
                              'For example:\n'
                              '\n'
                              '  --plot style="candle" (to plot candles)\n'))

    if pargs is not None:
        return parser.parse_args(pargs)

    return parser.parse_args()

if __name__ == '__main__':
    runstrat()
```
