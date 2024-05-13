# 优化器 – 并行优化器

> 原文：[`gbeced.github.io/pyalgotrade/docs/v0.20/html/optimizer.html`](https://gbeced.github.io/pyalgotrade/docs/v0.20/html/optimizer.html)

*类* `pyalgotrade.optimizer.server.``Results`（*parameters*, *result*）

基类：`object`

策略执行的结果。

`getParameters`()

返回参数值序列。

`getResult`() 

返回给定参数集的结果。

`pyalgotrade.optimizer.server.``serve`(*barFeed*, *strategyParameters*, *address*, *port*, *batchSize=200*)

执行一个服务器，该服务器将为工作者提供 K 线数据和策略参数。

| 参数： |
| --- |

+   **barFeed**（`pyalgotrade.barfeed.BarFeed`）– 每个工作者将用于回测策略的 K 线数据源。

+   **strategyParameters** – 用于回测的参数集。一个可迭代对象，其中**每个元素都是一个保存参数值的元组**。

+   **address**（*string.*）– 监听传入工作连接的地址。

+   **端口**（*int.*）– 监听传入工作连接的端口。

+   **batchSize**（*int.*）– 交付给每个工作者的策略执行数量。

|

| 返回类型： | 一个`Results`实例，其中包含找到的最佳结果，如果没有获得结果，则为 None。 |
| --- | --- |

`pyalgotrade.optimizer.worker.``run`(*strategyClass*, *address*, *port*, *workerCount=None*, *workerName=None*)

执行一个或多个工作进程，这些进程将使用服务器提供的 K 线数据和参数运行策略。

| 参数： |
| --- |

+   **strategyClass** – 策略类。

+   **address**（*string.*）– 服务器地址。

+   **port**（*int.*）– 服务器监听传入连接的端口。

+   **workerCount**（*int.*）– 要运行的工作者进程数。如果为 None，则使用与 CPU 数量相同的工作者。

+   **workerName**（*string.*）– 工作者的名称。标识工作者的名称。如果为 None，则使用主机名。

|

`pyalgotrade.optimizer.local.``run`(*strategyClass*, *barFeed*, *strategyParameters*, *workerCount=None*, *logLevel=40*, *batchSize=200*)

并行执行多个策略实例，并找到产生最佳结果的参数。

| 参数： |
| --- |

+   **strategyClass** – 策略类。

+   **barFeed**（`pyalgotrade.barfeed.BarFeed`）– 用于回测策略的 K 线数据源。

+   **strategyParameters** – 用于回测的参数集。一个可迭代对象，其中**每个元素都是一个保存参数值的元组**。

+   **workerCount**（*int.*）– 并行运行的策略数量。如果为 None，则使用与 CPU 数量相同的工作者。

+   **logLevel** – 日志级别。默认为**logging.ERROR**。

+   **batchSize**（*int.*）– 交付给每个工作者的策略执行数量。

|

| 返回类型： | 一个`Results`实例，其中包含找到的最佳结果。 |
| --- | --- |

注意

+   服务器组件将策略执行分成不同的块，并分配给不同的工作器。您可以通过将**batchSize**传递给**pyalgotrade.optimizer.xmlrpcserver.Server**的构造函数来选择块大小。

+   `pyalgotrade.strategy.BaseStrategy.getResult()`方法用于选择最佳的策略执行。您可以重写该方法以使用不同的标准对执行进行排名。

#### 上一个主题

绘图器 – 策略绘图器

#### 下一个主题

市场会话 – 市场会话

### 本页

+   显示源码

### 快速搜索

输入搜索词或模块、类或函数名称。

### 导航

+   索引

+   模块 |

+   下一章节 |

+   上一个 |

+   PyAlgoTrade 0.20 文档 »

+   代码文档 »
