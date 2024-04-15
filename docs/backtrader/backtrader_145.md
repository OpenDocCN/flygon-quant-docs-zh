# 笔记本 - 自动内联绘图

> 原文：[`www.backtrader.com/blog/posts/2016-09-17-notebook-inline/notebook-inline/`](https://www.backtrader.com/blog/posts/2016-09-17-notebook-inline/notebook-inline/)

发行版 1.9.1.99 在*Jupyter Notebook*内运行时添加了自动内联绘图。

关于 backtrader 的一些问题显示，人们在*笔记本*中使用该平台，并支持此操作并将其设置为默认行为应该使事情保持一致。

如果希望保留先前的行为并且图形必须独立绘制，请简单执行：

```py
`import backtrader as bt

...

cerebro.run()

...

cerebro.plot(iplot=False)` 
```

当然，如果从脚本或交互式运行，`matplotlib`的默认绘图后端将像以前一样使用，这将在单独的窗口中绘制图表。
