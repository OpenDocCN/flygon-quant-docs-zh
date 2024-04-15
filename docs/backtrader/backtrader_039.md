# 雅虎数据源注意事项

> 原文：[`www.backtrader.com/docu/datayahoo/`](https://www.backtrader.com/docu/datayahoo/)

2017 年 5 月，雅虎停止了用于历史数据下载的现有 *csv* 格式的 API。

一个新的 API（此处命名为 `v7`）迅速被*标准化*并已实施。

这也带来了实际 CSV 下载格式的更改。

## 使用 v7 API/格式

从版本 `1.9.49.116` 开始，这是默认行为。只需简单选择

+   `YahooFinanceData` 用于在线下载

+   `YahooFinanceCSVData` 用于离线下载的文件

## 使用传统的 API/格式

要使用旧的 API/格式

1.  将在线雅虎数据源实例化为：

    ```py
    `data = bt.feeds.YahooFinanceData(
        ...
        version='',
        ...
    )` 
    ```

    离线雅虎数据源为：

    ```py
    `data = bt.feeds.YahooFinanceCSVData(
        ...
        version='',
        ...
    )` 
    ```

    可能在线服务会回来（该服务没有任何公告地*停止*了… 也有可能会回来）

或者

1.  仅对在更改发生之前下载的离线文件，也可以进行以下操作：

    ```py
    `data = bt.feeds.YahooLegacyCSV(
        ...
        ...
    )` 
    ```

    新的 `YahooLegacyCSV` 简单地使用 `version=''` 进行自动化。
