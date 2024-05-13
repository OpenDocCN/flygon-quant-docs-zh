# 介绍

> 原文：[`gbeced.github.io/pyalgotrade/docs/v0.20/html/intro.html`](https://gbeced.github.io/pyalgotrade/docs/v0.20/html/intro.html)

PyAlgoTrade 是一个支持事件驱动的算法交易 Python 库，支持：

+   使用来自 CSV 文件的历史数据进行回测。

+   使用 *Bitstamp* 实时数据进行模拟交易。

+   在 Bitstamp 上进行真实交易。

它还应该使得使用多台计算机优化策略变得容易。

PyAlgoTrade 是使用 Python 2.7/3.7 开发和测试的，依赖于：

+   NumPy 和 SciPy ([`numpy.scipy.org/`](http://numpy.scipy.org/))。

+   pytz ([`pytz.sourceforge.net/`](http://pytz.sourceforge.net/))。

+   matplotlib ([`matplotlib.sourceforge.net/`](http://matplotlib.sourceforge.net/)) 用于绘图支持。

+   ws4py ([`github.com/Lawouach/WebSocket-for-Python`](https://github.com/Lawouach/WebSocket-for-Python)) 支持 Bitstamp。

+   tornado ([`www.tornadoweb.org/en/stable/`](http://www.tornadoweb.org/en/stable/)) 支持 Bitstamp。

+   tweepy ([`github.com/tweepy/tweepy`](https://github.com/tweepy/tweepy)) 支持 Twitter。

因此，您需要安装这些才能使用此库。

您可以像这样使用 pip 安装 PyAlgoTrade：

```py
pip install pyalgotrade
```

