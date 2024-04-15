# Pyfolio 集成

> 原文：[`www.backtrader.com/blog/posts/2016-07-17-pyfolio-integration/pyfolio-integration/`](https://www.backtrader.com/blog/posts/2016-07-17-pyfolio-integration/pyfolio-integration/)

注意

**2017 年 2 月**

`pyfolio`的 API 已更改，`create_full_tear_sheet`不再具有`gross_lev`作为命名参数。

因此，下面的示例不起作用

初次查看教程时，由于 zipline 和 pyfolio 之间的紧密集成，被认为很难，但是 pyfolio 提供的用于其他用途的样本*测试*数据实际上非常有用，可以解码幕后运行的内容，从而实现集成的奇迹。

一个名为`pyfolio`的*投资组合*工具的集成是在[Ticket #108](https://github.com/mementum/backtrader/issues/108)中提出的。

大部分组件已经就位在*backtrader*中：

+   分析器基础设施

+   子分析器

+   一个 TimeReturn 分析器

只需要一个主要的`PyFolio`分析器和 3 个简单的*子*分析器。再加上一个依赖于`pyfolio`已经需要的`pandas`的方法。

最具挑战性的部分是...“确保所有依赖项正确”。

+   更新`pandas`

+   更新`numpy`

+   更新`scikit-lean`

+   更新`seaborn`

在类 Unix 环境中使用*C*编译器，一切都是关于时间的。在 Windows 下，即使安装了特定的*Microsoft*编译器（在本例中是*Python 2.7*的链），事情也会失败。但是一个收集最新软件包的著名网站对*Windows*有所帮助。如果您需要，请访问：

+   [`www.lfd.uci.edu/~gohlke/pythonlibs/`](http://www.lfd.uci.edu/~gohlke/pythonlibs/)

如果没有经过测试，集成将不完整，这就是为什么通常的示例总是存在的原因。

## 没有 PyFolio

该示例使用`random.randint`来决定何时*买入*/*卖出*，因此这只是一个检查事情是否正常运行的方法：

```py
$ ./pyfoliotest.py --printout --no-pyfolio --plot
```

输出：

```py
Len,Datetime,Open,High,Low,Close,Volume,OpenInterest
0001,2005-01-03T23:59:59,38.36,38.90,37.65,38.18,25482800.00,0.00
BUY  1000 @%23.58
0002,2005-01-04T23:59:59,38.45,38.54,36.46,36.58,26625300.00,0.00
BUY  1000 @%36.58
SELL 500 @%22.47
0003,2005-01-05T23:59:59,36.69,36.98,36.06,36.13,18469100.00,0.00
...
SELL 500 @%37.51
0502,2006-12-28T23:59:59,25.62,25.72,25.30,25.36,11908400.00,0.00
0503,2006-12-29T23:59:59,25.42,25.82,25.33,25.54,16297800.00,0.00
SELL 250 @%17.14
SELL 250 @%37.01
```

![image](img/2065abb1e634b5adc14e6c863f425683.png)

有 3 个数据和几个*买入*和*卖出*操作是随机选择并分散在测试运行的默认 2 年寿命中

## 一个 PyFolio 运行

当在*Jupyter Notebook*中运行时，`pyfolio`的功能运行良好，包括内联绘图。这是*笔记本*

注意

`runstrat`在此处获取[]作为参数以使用默认参数运行，并跳过由*笔记本*本身传递的参数

```py
%matplotlib inline
```

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
import datetime
import random

import backtrader as bt

class St(bt.Strategy):
    params = (
        ('printout', False),
        ('stake', 1000),
    )

    def __init__(self):
        pass

    def start(self):
        if self.p.printout:
            txtfields = list()
            txtfields.append('Len')
            txtfields.append('Datetime')
            txtfields.append('Open')
            txtfields.append('High')
            txtfields.append('Low')
            txtfields.append('Close')
            txtfields.append('Volume')
            txtfields.append('OpenInterest')
            print(','.join(txtfields))

    def next(self):
        if self.p.printout:
            # Print only 1st data ... is just a check that things are running
            txtfields = list()
            txtfields.append('%04d' % len(self))
            txtfields.append(self.data.datetime.datetime(0).isoformat())
            txtfields.append('%.2f' % self.data0.open[0])
            txtfields.append('%.2f' % self.data0.high[0])
            txtfields.append('%.2f' % self.data0.low[0])
            txtfields.append('%.2f' % self.data0.close[0])
            txtfields.append('%.2f' % self.data0.volume[0])
            txtfields.append('%.2f' % self.data0.openinterest[0])
            print(','.join(txtfields))

        # Data 0
        for data in self.datas:
            toss = random.randint(1, 10)
            curpos = self.getposition(data)
            if curpos.size:
                if toss > 5:
                    size = curpos.size // 2
                    self.sell(data=data, size=size)
                    if self.p.printout:
                        print('SELL {} @%{}'.format(size, data.close[0]))

            elif toss < 5:
                self.buy(data=data, size=self.p.stake)
                if self.p.printout:
                    print('BUY {} @%{}'.format(self.p.stake, data.close[0]))

def runstrat(args=None):
    args = parse_args(args)

    cerebro = bt.Cerebro()
    cerebro.broker.set_cash(args.cash)

    dkwargs = dict()
    if args.fromdate:
        fromdate = datetime.datetime.strptime(args.fromdate, '%Y-%m-%d')
        dkwargs['fromdate'] = fromdate

    if args.todate:
        todate = datetime.datetime.strptime(args.todate, '%Y-%m-%d')
        dkwargs['todate'] = todate

    data0 = bt.feeds.BacktraderCSVData(dataname=args.data0, **dkwargs)
    cerebro.adddata(data0, name='Data0')

    data1 = bt.feeds.BacktraderCSVData(dataname=args.data1, **dkwargs)
    cerebro.adddata(data1, name='Data1')

    data2 = bt.feeds.BacktraderCSVData(dataname=args.data2, **dkwargs)
    cerebro.adddata(data2, name='Data2')

    cerebro.addstrategy(St, printout=args.printout)
    if not args.no_pyfolio:
        cerebro.addanalyzer(bt.analyzers.PyFolio, _name='pyfolio')

    results = cerebro.run()
    if not args.no_pyfolio:
        strat = results[0]
        pyfoliozer = strat.analyzers.getbyname('pyfolio')

        returns, positions, transactions, gross_lev = pyfoliozer.get_pf_items()
        if args.printout:
            print('-- RETURNS')
            print(returns)
            print('-- POSITIONS')
            print(positions)
            print('-- TRANSACTIONS')
            print(transactions)
            print('-- GROSS LEVERAGE')
            print(gross_lev)

        import pyfolio as pf
        pf.create_full_tear_sheet(
            returns,
            positions=positions,
            transactions=transactions,
            gross_lev=gross_lev,
            live_start_date='2005-05-01',
            round_trips=True)

    if args.plot:
        cerebro.plot(style=args.plot_style)

def parse_args(args=None):

    parser = argparse.ArgumentParser(
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
        description='Sample for pivot point and cross plotting')

    parser.add_argument('--data0', required=False,
                        default='../../datas/yhoo-1996-2015.txt',
                        help='Data to be read in')

    parser.add_argument('--data1', required=False,
                        default='../../datas/orcl-1995-2014.txt',
                        help='Data to be read in')

    parser.add_argument('--data2', required=False,
                        default='../../datas/nvda-1999-2014.txt',
                        help='Data to be read in')

    parser.add_argument('--fromdate', required=False,
                        default='2005-01-01',
                        help='Starting date in YYYY-MM-DD format')

    parser.add_argument('--todate', required=False,
                        default='2006-12-31',
                        help='Ending date in YYYY-MM-DD format')

    parser.add_argument('--printout', required=False, action='store_true',
                        help=('Print data lines'))

    parser.add_argument('--cash', required=False, action='store',
                        type=float, default=50000,
                        help=('Cash to start with'))

    parser.add_argument('--plot', required=False, action='store_true',
                        help=('Plot the result'))

    parser.add_argument('--plot-style', required=False, action='store',
                        default='bar', choices=['bar', 'candle', 'line'],
                        help=('Plot style'))

    parser.add_argument('--no-pyfolio', required=False, action='store_true',
                        help=('Do not do pyfolio things'))

    import sys
    aargs = args if args is not None else sys.argv[1:]
    return parser.parse_args(aargs)
```

```py
runstrat([])
```

```py
Entire data start date: 2005-01-03
Entire data end date: 2006-12-29

Out-of-Sample Months: 20
Backtest Months: 3
```

```py
[-0.012 -0.025]
```

![image](img/9e907c78e596f4812d1a645f7945f889.png)

![image](img/a34f8f7cacb12651e6a372290e4fef8e.png)

![image](img/8452c2408ba1795af50c3642991c3838.png)

```py
D:drobinWinPython-64bit-2.7.10.3python-2.7.10.amd64libsite-packagespyfolioplotting.py:1210: FutureWarning: .resample() is now a deferred operation
use .resample(...).mean() instead of .resample(...)
  **kwargs)
```

![image](img/f2cc25932652c1c71d99dd12b9020b3c.png)

```py
<matplotlib.figure.Figure at 0x23982b70>
```

![image](img/306275e8d7f851520c93bc570295e1d5.png)

使用示例：

```py
$ ./pyfoliotest.py --help
usage: pyfoliotest.py [-h] [--data0 DATA0] [--data1 DATA1] [--data2 DATA2]
                      [--fromdate FROMDATE] [--todate TODATE] [--printout]
                      [--cash CASH] [--plot] [--plot-style {bar,candle,line}]
                      [--no-pyfolio]

Sample for pivot point and cross plotting

optional arguments:
  -h, --help            show this help message and exit
  --data0 DATA0         Data to be read in (default:
                        ../../datas/yhoo-1996-2015.txt)
  --data1 DATA1         Data to be read in (default:
                        ../../datas/orcl-1995-2014.txt)
  --data2 DATA2         Data to be read in (default:
                        ../../datas/nvda-1999-2014.txt)
  --fromdate FROMDATE   Starting date in YYYY-MM-DD format (default:
                        2005-01-01)
  --todate TODATE       Ending date in YYYY-MM-DD format (default: 2006-12-31)
  --printout            Print data lines (default: False)
  --cash CASH           Cash to start with (default: 50000)
  --plot                Plot the result (default: False)
  --plot-style {bar,candle,line}
                        Plot style (default: bar)
  --no-pyfolio          Do not do pyfolio things (default: False)
```
