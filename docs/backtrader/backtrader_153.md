# 股票筛选

> 原文：[`www.backtrader.com/blog/posts/2016-08-15-stock-screening/stock-screening/`](https://www.backtrader.com/blog/posts/2016-08-15-stock-screening/stock-screening/)

在寻找其他东西时，我在 *StackOverlow* 家族网站之一上看到了一个问题：*Quantitative Finance*，也就是 *Quant StackExchange*。问题是：

+   [使用技术分析进行股票筛选和扫描的开源软件？](http://quant.stackexchange.com/questions/27559/open-source-software-for-stock-screening-and-scanning-using-technical-analysis)

它被标记为 *Python*，所以值得看看 *backtrader* 是否能胜任这项任务。

## 分析器本身

这个问题似乎适合一个简单的分析器。虽然问题只想要那些高于移动平均线的股票，但我们会保留额外的信息，比如那些不符合条件的股票，以确保谷物确实被分离出来。

```py
class Screener_SMA(bt.Analyzer):
    params = dict(period=10)

    def start(self):
        self.smas = {data: bt.indicators.SMA(data, period=self.p.period)
                     for data in self.datas}

    def stop(self):
        self.rets['over'] = list()
        self.rets['under'] = list()

        for data, sma in self.smas.items():
            node = data._name, data.close[0], sma[0]
            if data > sma:  # if data.close[0] > sma[0]
                self.rets['over'].append(node)
            else:
                self.rets['under'].append(node)
```

注意

当然，还需要 `import backtrader as bt`

这基本上解决了问题。对 *Analyzer* 的分析：

+   将 `period` 作为参数，以便有一个灵活的分析器

+   `start` 方法

    对系统中的每个 *data* 制作一个 *Simple Moving Average*（`SMA`）。

+   `stop` 方法

    查看哪个 *data*（如果没有其他指定，则为 `close`）高于其 *sma*，并将其存储在返回值（`rets`）的键 `over` 下的 *list* 中

    成员 `rets` 在 *analyzers* 中是标准的，是一个 `collections.OrderedDict`。由基类创建。

    将不符合条件的股票保留在 `under` 键下

现在的问题是：让分析器运行起来。

注意

我们假设代码已经放在一个名为 `st-screener.py` 的文件中

## 方法 1

*backtrader* 自从很早以前就包含了一个名为 `btrun` 的自动脚本运行，可以从 Python 模块加载策略、指标、分析器，解析参数，当然还可以绘图。

让我们来运行一下：

```py
$ btrun --format yahoo --data YHOO --data IBM --data NVDA --data TSLA --data ORCL --data AAPL --fromdate 2016-07-15 --todate 2016-08-13 --analyzer st-screener:Screener_SMA --cerebro runonce=0 --writer --nostdstats
===============================================================================
Cerebro:
  -----------------------------------------------------------------------------
  - Datas:
    +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
    - Data0:
      - Name: YHOO
      - Timeframe: Days
      - Compression: 1
    +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
    - Data1:
      - Name: IBM
      - Timeframe: Days
      - Compression: 1
    +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
    - Data2:
      - Name: NVDA
      - Timeframe: Days
      - Compression: 1
    +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
    - Data3:
      - Name: TSLA
      - Timeframe: Days
      - Compression: 1
    +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
    - Data4:
      - Name: ORCL
      - Timeframe: Days
      - Compression: 1
    +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
    - Data5:
      - Name: AAPL
      - Timeframe: Days
      - Compression: 1
  -----------------------------------------------------------------------------
  - Strategies:
    +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
    - Strategy:
      *************************************************************************
      - Params:
      *************************************************************************
      - Indicators:
        .......................................................................
        - SMA:
          - Lines: sma
          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
          - Params:
            - period: 10
      *************************************************************************
      - Observers:
      *************************************************************************
      - Analyzers:
        .......................................................................
        - Value:
          - Begin: 10000.0
          - End: 10000.0
        .......................................................................
        - Screener_SMA:
          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
          - Params:
            - period: 10
          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
          - Analysis:
            - over: ('ORCL', 41.09, 41.032), ('IBM', 161.95, 161.221), ('YHOO', 42.94, 39.629000000000005), ('AAPL', 108.18, 106.926), ('NVDA', 63.04, 58.327)
            - under: ('TSLA', 224.91, 228.423)
```

我们使用了一组众所周知的股票代码：

+   `AAPL`, `IBM`, `NVDA`, `ORCL`, `TSLA`, `YHOO`

唯一一个低于 `10` 天 *Simple Moving Average* 的是 `TSLA`。

让我们尝试一个 `50` 天的周期。是的，这也可以通过 `btrun` 控制。运行（输出缩短）：

```py
$ btrun --format yahoo --data YHOO --data IBM --data NVDA --data TSLA --data ORCL --data AAPL --fromdate 2016-05-15 --todate 2016-08-13 --analyzer st-screener:Screener_SMA:period=50 --cerebro runonce=0 --writer --nostdstats
===============================================================================
Cerebro:
  -----------------------------------------------------------------------------
  - Datas:
    +++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
    - Data0:
...
...
...
        - Screener_SMA:
          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
          - Params:
            - period: 50
          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
          - Analysis:
            - over: ('ORCL', 41.09, 40.339), ('IBM', 161.95, 155.0356), ('YHOO', 42.94, 37.9648), ('TSLA', 224.91, 220.4784), ('AAPL', 108.18, 98.9782), ('NVDA', 63.04, 51.4746)
            - under:
```

注意如何在命令行中指定了 `50` 天的周期：

+   `st-screener:Screener_SMA:period=50`

    在上一次运行中，这是 `st-screener:Screener_SMA`，并且使用了代码中的默认 `10`。

我们还需要调整 `fromdate`，以确保有足够的条形图用于计算 *Simple Moving Averages*

在这种情况下，所有股票的 *50* 天移动平均线都在上面。

## 方法 2

制作一个小脚本（请参见下面的完整代码）以更好地控制我们的操作。但结果是一样的。

核心相当小：

```py
 `cerebro = bt.Cerebro()
    for ticker in args.tickers.split(','):
        data = bt.feeds.YahooFinanceData(dataname=ticker,
                                         fromdate=fromdate, todate=todate)
        cerebro.adddata(data)

    cerebro.addanalyzer(Screener_SMA, period=args.period)
    cerebro.run(runonce=False, stdstats=False, writer=True)
```

其余大部分是关于参数解析的。

对于 `10` 天（再次缩短输出）：

```py
$ ./st-screener.py
===============================================================================
Cerebro:
...
...
...
        - Screener_SMA:
          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
          - Params:
            - period: 10
          ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
          - Analysis:
            - over: (u'NVDA', 63.04, 58.327), (u'AAPL', 108.18, 106.926), (u'YHOO', 42.94, 39.629000000000005), (u'IBM', 161.95, 161.221), (u'ORCL', 41.09, 41.032)
            - under: (u'TSLA', 224.91, 228.423)
```

结果相同。所以让我们避免重复为 `50` 天做这个。

## 结论

*方法 1*的`btrun`和*方法 2*的小脚本都使用完全相同的*analyzer*，因此提供相同的结果。

而*backtrader*已经能够应对又一个小挑战。

两个最后的注意事项：

+   这两种方法都使用内置的*writer*功能来提供输出。

    +   作为`btrun`的参数，带有`--writer`。

    +   作为参数传递给`cerebro.run`时，带有`writer=True`。

+   在两种情况下，`runonce`都已被停用。这是为了确保在线数据保持同步，因为结果可能具有不同的长度（其中一个股票可能交易较少）。

## 脚本用法

```py
$ ./st-screener.py --help
usage: st-screener.py [-h] [--tickers TICKERS] [--period PERIOD]

SMA Stock Screener

optional arguments:
  -h, --help         show this help message and exit
  --tickers TICKERS  Yahoo Tickers to consider, COMMA separated (default:
                     YHOO,IBM,AAPL,TSLA,ORCL,NVDA)
  --period PERIOD    SMA period (default: 10)
```

## 完整的脚本

```py
#!/usr/bin/env python
# -*- coding: utf-8; py-indent-offset:4 -*-
###############################################################################
#
# Copyright (C) 2015, 2016 Daniel Rodriguez
#
# This program is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program.  If not, see <http://www.gnu.org/licenses/>.
#
###############################################################################
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
import datetime

import backtrader as bt

class Screener_SMA(bt.Analyzer):
    params = dict(period=10)

    def start(self):
        self.smas = {data: bt.indicators.SMA(data, period=self.p.period)
                     for data in self.datas}

    def stop(self):
        self.rets['over'] = list()
        self.rets['under'] = list()

        for data, sma in self.smas.items():
            node = data._name, data.close[0], sma[0]
            if data > sma:  # if data.close[0] > sma[0]
                self.rets['over'].append(node)
            else:
                self.rets['under'].append(node)

DEFAULTTICKERS = ['YHOO', 'IBM', 'AAPL', 'TSLA', 'ORCL', 'NVDA']

def run(args=None):
    args = parse_args(args)
    todate = datetime.date.today()
    # Get from date from period +X% for weekeends/bank/holidays: let's double
    fromdate = todate - datetime.timedelta(days=args.period * 2)

    cerebro = bt.Cerebro()
    for ticker in args.tickers.split(','):
        data = bt.feeds.YahooFinanceData(dataname=ticker,
                                         fromdate=fromdate, todate=todate)
        cerebro.adddata(data)

    cerebro.addanalyzer(Screener_SMA, period=args.period)
    cerebro.run(runonce=False, stdstats=False, writer=True)

def parse_args(pargs=None):

    parser = argparse.ArgumentParser(
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
        description='SMA Stock Screener')

    parser.add_argument('--tickers', required=False, action='store',
                        default=','.join(DEFAULTTICKERS),
                        help='Yahoo Tickers to consider, COMMA separated')

    parser.add_argument('--period', required=False, action='store',
                        type=int, default=10,
                        help=('SMA period'))

    if pargs is not None:
        return parser.parse_args(pargs)

    return parser.parse_args()

if __name__ == '__main__':
    run()
```
