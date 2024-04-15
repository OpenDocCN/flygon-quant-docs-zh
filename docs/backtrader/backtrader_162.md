# 以步骤方式交易一天

> 原文：[`www.backtrader.com/blog/posts/2016-07-13-day-in-steps/day-in-steps/`](https://www.backtrader.com/blog/posts/2016-07-13-day-in-steps/day-in-steps/)

看起来世界上的某个地方存在一种可以总结如下的兴趣：

+   *使用每日柱状图但使用开盘价引入订单*

这来自于票证交流中的对话[#105 Order execution logic with current day data](https://github.com/mementum/backtrader/issues/105)和[#101 Dynamic stake calculation](https://github.com/mementum/backtrader/issues/101)

*backtrader*在处理*每日柱状图*时尽可能保持真实，并且在使用*每日柱状图*时适用以下前提：

+   当评估每日柱状图时，柱状图已经结束

这是有道理的，因为所有价格（*open/high/low/close*）组件都是已知的。当已知`close`价格时允许在`open`价格上采取行动似乎是不合逻辑的。

这个问题的明显解决方案是使用*日内*数据，在已知开盘价时进入。但是似乎*日内*数据并不那么普遍。

这就是在数据源中添加*过滤器*可以帮助的地方。一个过滤器：

+   *将每日数据转换为类似日内数据的数据*

碧海蓝天！！！好奇的读者会立即指出，例如从`Minutes`到`Days`的*上采样*是合乎逻辑且有效的，但是*从`Days`到`Minutes`的下采样*是不可能的。

而且百分百正确。下面呈现的过滤器不会尝试这样做，但是一个更加谦卑和简单的目标：

+   将每日柱状图分解为 2 部分

    1.  一个只有开盘价而没有成交量的柱状图

    1.  一个是正常的每日柱状图的副本

这仍然可以被视为一种合乎逻辑的方法：

+   看到*开盘*价时，交易员可以采取行动

+   订单在一天的其余时间匹配（实际上可能匹配也可能不匹配，取决于执行类型和价格限制）

下面呈现了完整的代码。让我们看一个使用`255`个*每日*柱状图的众所周知的数据的示例运行：

```py
$ ./daysteps.py --data ../../datas/2006-day-001.txt
```

输出：

```py
Calls,Len Strat,Len Data,Datetime,Open,High,Low,Close,Volume,OpenInterest
0001,0001,0001,2006-01-02T23:59:59,3578.73,3578.73,3578.73,3578.73,0.00,0.00
- I could issue a buy order during the Opening
0002,0001,0001,2006-01-02T23:59:59,3578.73,3605.95,3578.73,3604.33,0.00,0.00
0003,0002,0002,2006-01-03T23:59:59,3604.08,3604.08,3604.08,3604.08,0.00,0.00
- I could issue a buy order during the Opening
0004,0002,0002,2006-01-03T23:59:59,3604.08,3638.42,3601.84,3614.34,0.00,0.00
0005,0003,0003,2006-01-04T23:59:59,3615.23,3615.23,3615.23,3615.23,0.00,0.00
- I could issue a buy order during the Opening
0006,0003,0003,2006-01-04T23:59:59,3615.23,3652.46,3615.23,3652.46,0.00,0.00
...
...
0505,0253,0253,2006-12-27T23:59:59,4079.70,4079.70,4079.70,4079.70,0.00,0.00
- I could issue a buy order during the Opening
0506,0253,0253,2006-12-27T23:59:59,4079.70,4134.86,4079.70,4134.86,0.00,0.00
0507,0254,0254,2006-12-28T23:59:59,4137.44,4137.44,4137.44,4137.44,0.00,0.00
- I could issue a buy order during the Opening
0508,0254,0254,2006-12-28T23:59:59,4137.44,4142.06,4125.14,4130.66,0.00,0.00
0509,0255,0255,2006-12-29T23:59:59,4130.12,4130.12,4130.12,4130.12,0.00,0.00
- I could issue a buy order during the Opening
0510,0255,0255,2006-12-29T23:59:59,4130.12,4142.01,4119.94,4119.94,0.00,0.00
```

以下情况发生：

+   `next`被调用：`510 次`，即`255 x 2`

+   *策略*和*数据*的`len`总共达到了`255`，这是预期的：**数据只有这么多根柱状图**

+   每当*数据*的`len`增加时，4 个价格组件具有相同的值，即`open`价格

    这里打印出一条备注，指示在这个*开盘*阶段可以采取行动，例如购买。

实际上：

+   每日数据源正在使用每天 2 步*重播*，在`open`和其余价格组件之间提供操作选项

过滤器将在下一个版本中添加到*backtrader*的默认分发中。

包括过滤器的示例代码。

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
from datetime import datetime, time

import backtrader as bt

class DayStepsFilter(object):
    def __init__(self, data):
        self.pendingbar = None

    def __call__(self, data):
        # Make a copy of the new bar and remove it from stream
        newbar = [data.lines[i][0] for i in range(data.size())]
        data.backwards()  # remove the copied bar from stream

        openbar = newbar[:]  # Make an open only bar
        o = newbar[data.Open]
        for field_idx in [data.High, data.Low, data.Close]:
            openbar[field_idx] = o

        # Nullify Volume/OpenInteres at the open
        openbar[data.Volume] = 0.0
        openbar[data.OpenInterest] = 0.0

        # Overwrite the new data bar with our pending data - except start point
        if self.pendingbar is not None:
            data._updatebar(self.pendingbar)

        self.pendingbar = newbar  # update the pending bar to the new bar
        data._add2stack(openbar)  # Add the openbar to the stack for processing

        return False  # the length of the stream was not changed

    def last(self, data):
        '''Called when the data is no longer producing bars
        Can be called multiple times. It has the chance to (for example)
        produce extra bars'''
        if self.pendingbar is not None:
            data.backwards()  # remove delivered open bar
            data._add2stack(self.pendingbar)  # add remaining
            self.pendingbar = None  # No further action
            return True  # something delivered

        return False  # nothing delivered here

class St(bt.Strategy):
    params = ()

    def __init__(self):
        pass

    def start(self):
        self.callcounter = 0
        txtfields = list()
        txtfields.append('Calls')
        txtfields.append('Len Strat')
        txtfields.append('Len Data')
        txtfields.append('Datetime')
        txtfields.append('Open')
        txtfields.append('High')
        txtfields.append('Low')
        txtfields.append('Close')
        txtfields.append('Volume')
        txtfields.append('OpenInterest')
        print(','.join(txtfields))

        self.lcontrol = 0

    def next(self):
        self.callcounter += 1

        txtfields = list()
        txtfields.append('%04d' % self.callcounter)
        txtfields.append('%04d' % len(self))
        txtfields.append('%04d' % len(self.data0))
        txtfields.append(self.data.datetime.datetime(0).isoformat())
        txtfields.append('%.2f' % self.data0.open[0])
        txtfields.append('%.2f' % self.data0.high[0])
        txtfields.append('%.2f' % self.data0.low[0])
        txtfields.append('%.2f' % self.data0.close[0])
        txtfields.append('%.2f' % self.data0.volume[0])
        txtfields.append('%.2f' % self.data0.openinterest[0])
        print(','.join(txtfields))

        if len(self.data) > self.lcontrol:
            print('- I could issue a buy order during the Opening')

        self.lcontrol = len(self.data)

def runstrat():
    args = parse_args()

    cerebro = bt.Cerebro()
    data = bt.feeds.BacktraderCSVData(dataname=args.data)

    data.addfilter(DayStepsFilter)
    cerebro.adddata(data)

    cerebro.addstrategy(St)

    cerebro.run(stdstats=False, runonce=False, preload=False)
    if args.plot:
        cerebro.plot(style='bar')

def parse_args():
    parser = argparse.ArgumentParser(
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
        description='Sample for pivot point and cross plotting')

    parser.add_argument('--data', required=False,
                        default='../../datas/2005-2006-day-001.txt',
                        help='Data to be read in')

    parser.add_argument('--plot', required=False, action='store_true',
                        help=('Plot the result'))

    return parser.parse_args()

if __name__ == '__main__':
    runstrat()
```
