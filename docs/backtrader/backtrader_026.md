# 写入器

> 原文：[`www.backtrader.com/docu/writer/`](https://www.backtrader.com/docu/writer/)

将以下内容写入流：

+   包含数据源、策略、指标和观察器的 csv 流

    可以通过每个对象的 `csv` 属性控制哪些对象实际进入 csv 流（对于 `数据源` 和 `观察器` 默认为 True / 对于 `指标` 默认为 False）

+   属性摘要

    +   数据源

    +   策略（行数和参数）

    +   指标/观察器：（行数和参数）

    +   分析器：（参数和分析结果）

只定义了一个称为 `WriterFile` 的写入器，可以添加到系统中：

+   通过将 cerebro 的 `writer` 参数设置为 True

    将实例化标准的 `WriterFile`

+   通过调用 `Cerebro.addwriter(writerclass, **kwargs)`

    `writerclass` 将在回测执行期间使用给定的 `kwargs` 实例化

    鉴于标准的 `WriterFile` 不会默认输出 `csv`，以下 `addwriter` 调用将负责处理：

    ```py
    cerebro.addwriter(bt.WriterFile, csv=True)` 
    ```

## 参考文献

#### backtrader.WriterFile 类

系统范围的写入器类。

可以用以下方式进行参数化：

+   `out`（默认：`sys.stdout`）：要写入的输出流

    如果传递了字符串，则将使用参数内容的文件名

+   `close_out`（默认：`False`）

    如果 `out` 是一个流，则是否需要写入器显式关闭它

+   `csv`（默认：`False`）

    如果在执行期间需要将数据源、策略、观察器和指标的 csv 流写入流

    可以通过每个对象的 `csv` 属性控制哪些对象实际进入 csv 流（对于 `数据源` 和 `观察器` 默认为 True / 对于 `指标` 默认为 False）

+   `csv_filternan`（默认：`True`）是否需要将 `nan` 值从 csv 流中清除（替换为空字段）

+   `csv_counter`（默认：`True`）如果写入器应该保留并打印实际输出的行数计数器

+   `indent`（默认：`2`）每个级别的缩进空格数

+   `separators`（默认：`['=', '-', '+', '*', '.', '~', '"', '^', '#']`）

    用于各个部分/子（子）部分的行分隔符使用的字符

+   `seplen`（默认：`79`）

    包括缩进在内的一行分隔符的总长度

+   `rounding`（默认：`None`）

    要将浮点数舍入到的小数位数。使用 `None` 时不执行舍入
