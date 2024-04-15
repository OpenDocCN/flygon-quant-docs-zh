# 异常

> 原文：[`www.backtrader.com/docu/exceptions/`](https://www.backtrader.com/docu/exceptions/)

设计目标之一是尽早退出，并让用户完全透明地了解错误的发生情况。目标是迫使自己拥有会在异常情况下中断并强制重新访问受影响部分的代码。

但时机已经成熟，一些异常可能会慢慢添加到平台中。

## 层次结构

所有异常的基类是`BacktraderError`（它是`Exception`的直接子类）

## 位置

1.  在模块`errors`内，例如可以通过以下方式访问：

    ```py
    import backtrader as bt

    class Strategy(bt.Strategy):

        def __init__(self):
            if something_goes_wrong():
                raise bt.errors.StrategySkipError` 
    ```

1.  直接来自`backtrader`，如下所示：

    ```py
    import backtrader as bt

    class Strategy(bt.Strategy):

        def __init__(self):
            if something_goes_wrong():
                raise bt.StrategySkipError` 
    ```

## 异常

### `StrategySkipError`

请求平台跳过此策略进行回测。在实例化（`__init__`）阶段引发
