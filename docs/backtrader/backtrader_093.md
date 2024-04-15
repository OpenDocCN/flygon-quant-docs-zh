# 在同一轴上绘制

> 原文：[`www.backtrader.com/docu/plotting/sameaxis/plot-sameaxis/`](https://www.backtrader.com/docu/plotting/sameaxis/plot-sameaxis/)

前一篇帖子中的未来点，是在同一空间上绘制了原始数据和略微（随机）修改的数据，但没有在同一轴上。

从那篇帖子中恢复第一张图片。

![image](img/edcb266900eb3bb87615de0c21365c4a.png)

人们可以看到：

+   图表的左右两侧有不同的比例尺

+   当观察摆动的红线（随机数据）围绕原始数据振荡 `+- 50` 点时，这一点最为明显。

    在图表上的视觉印象是，这些随机数据大多数时候都在原始数据之上。这只是由于不同的比例尺造成的视觉印象。

尽管发行版 `1.9.32.116` 已经初步支持在同一轴上完全绘制，但图例标签会重复（只有标签，没有数据），这真的很令人困惑。

发行版 `1.9.33.116` 解决了这个效果，并允许完全在同一轴上绘制。用法模式与决定与哪些其他数据一起绘制的模式相似。从前一篇帖子中。

```py
import backtrader as bt

cerebro = bt.Cerebro()

data0 = bt.feeds.MyFavouriteDataFeed(dataname='futurename')
cerebro.adddata(data0)

data1 = bt.feeds.MyFavouriteDataFeed(dataname='spotname')
data1.compensate(data0)  # let the system know ops on data1 affect data0
data1.plotinfo.plotmaster = data0
data1.plotinfo.sameaxis = True
cerebro.adddata(data1)

...

cerebro.run()
```

`data1` 获得一些 `plotinfo` 值以：

+   在与 `plotmaster`（即 `data0`）相同的空间中绘制

+   获得使用 `sameaxis` 的指示

    这种指示的原因是平台无法预先知道每个数据的比例尺是否兼容。这就是为什么它会在独立的比例尺上绘制它们的原因。

前面的示例获取了一个额外的选项，以在 `sameaxis` 上绘制。一个示例执行：

```py
$ ./future-spot.py --sameaxis
```

以及产生的图表

![image](img/beb74141e9a847b2bac618c28c5a37cf.png)

要注意：

+   右侧只有一个比例尺

+   现在随机数据似乎明显围绕着原始数据振荡，这是预期的视觉行为

## 示例用法

```py
$ ./future-spot.py --help
usage: future-spot.py [-h] [--no-comp] [--sameaxis]

Compensation example

optional arguments:
  -h, --help  show this help message and exit
  --no-comp
  --sameaxis
```

## 示例代码

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
import random
import backtrader as bt

# The filter which changes the close price
def close_changer(data, *args, **kwargs):
    data.close[0] += 50.0 * random.randint(-1, 1)
    return False  # length of stream is unchanged

# override the standard markers
class BuySellArrows(bt.observers.BuySell):
    plotlines = dict(buy=dict(marker='$\u21E7$', markersize=12.0),
                     sell=dict(marker='$\u21E9$', markersize=12.0))

class St(bt.Strategy):
    def __init__(self):
        bt.obs.BuySell(self.data0, barplot=True)  # done here for
        BuySellArrows(self.data1, barplot=True)  # different markers per data

    def next(self):
        if not self.position:
            if random.randint(0, 1):
                self.buy(data=self.data0)
                self.entered = len(self)

        else:  # in the market
            if (len(self) - self.entered) >= 10:
                self.sell(data=self.data1)

def runstrat(args=None):
    args = parse_args(args)
    cerebro = bt.Cerebro()

    dataname = '../../datas/2006-day-001.txt'  # data feed

    data0 = bt.feeds.BacktraderCSVData(dataname=dataname, name='data0')
    cerebro.adddata(data0)

    data1 = bt.feeds.BacktraderCSVData(dataname=dataname, name='data1')
    data1.addfilter(close_changer)
    if not args.no_comp:
        data1.compensate(data0)
    data1.plotinfo.plotmaster = data0
    if args.sameaxis:
        data1.plotinfo.sameaxis = True
    cerebro.adddata(data1)

    cerebro.addstrategy(St)  # sample strategy

    cerebro.addobserver(bt.obs.Broker)  # removed below with stdstats=False
    cerebro.addobserver(bt.obs.Trades)  # removed below with stdstats=False

    cerebro.broker.set_coc(True)
    cerebro.run(stdstats=False)  # execute
    cerebro.plot(volume=False)  # and plot

def parse_args(pargs=None):
    parser = argparse.ArgumentParser(
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
        description=('Compensation example'))

    parser.add_argument('--no-comp', required=False, action='store_true')
    parser.add_argument('--sameaxis', required=False, action='store_true')
    return parser.parse_args(pargs)

if __name__ == '__main__':
    runstrat()
```
