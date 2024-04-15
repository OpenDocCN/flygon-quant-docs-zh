# 简介

> 原文：[`www.backtrader.com/recipes/indicators/intro/`](https://www.backtrader.com/recipes/indicators/intro/)

本节收集了一些*指标*实现，它们不是*backtrader*核心的一部分。

使用它们：

+   将代码复制到你选择的文件中或直接放在你的策略上方

+   如果复制到文件中，请从文件中导入它

    ```py
    `from myfile import TheIndicator` 
    ```

+   并将其纳入你的策略中

    ```py
    `class MyStrategy(bt.Strategy):
        def __init__(self):
            self.myind = TheIndicator(self.data, param1=value1, ...)` 
    ```
