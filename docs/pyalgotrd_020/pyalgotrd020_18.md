# bitstamp – Bitstamp 参考

> 原文：[`gbeced.github.io/pyalgotrade/docs/v0.20/html/bitstamp_ref.html`](https://gbeced.github.io/pyalgotrade/docs/v0.20/html/bitstamp_ref.html)

## WebSocket

此包包含由 Bitstamp 的流媒体服务发出的事件的类。有关更多信息，请查看[`www.bitstamp.net/websocket/`](https://www.bitstamp.net/websocket/)。

*类* `pyalgotrade.bitstamp.wsclient.``OrderBookUpdate`(*dateTime*, *eventDict*)

基类：`pyalgotrade.websocket.pusher.Event`

一个订单簿更新事件。

`getAskPrices`()

返回一个包含前 20 个卖价的列表。

`getAskVolumes`() 

返回一个包含前 20 个卖量的列表。

`getBidPrices`()

返回一个包含前 20 个买价的列表。

`getBidVolumes`()

返回一个包含前 20 个买量的列表。

`getDateTime`()

返回此事件被接收的`datetime.datetime`。

*类* `pyalgotrade.bitstamp.wsclient.``Trade`(*dateTime*, *eventDict*)

基类：`pyalgotrade.websocket.pusher.Event`

一个交易事件。

`getAmount`()

返回交易金额。

`getDateTime`()

返回此事件被接收的`datetime.datetime`。

`getId`()

返回交易 ID。

`getPrice`()

返回交易价格。

`isBuy`()

如果交易是买入，则返回 True。

`isSell`()

如果交易是卖出，则返回 True。

*类* `pyalgotrade.bitstamp.wsclient.``WebSocketClient`(*queue*)

基类：`pyalgotrade.websocket.pusher.WebSocketClient`

此 websocket 客户端类设计为在单独的线程中运行，因此事件被推送到队列中。

*类* `pyalgotrade.bitstamp.wsclient.``WebSocketClientThread`

基类：`pyalgotrade.websocket.client.WebSocketClientThreadBase`

此线程类负责运行 WebSocketClient。  ## 数据源

*类* `pyalgotrade.bitstamp.barfeed.``LiveTradeFeed`(*maxLen=None*)

基类：`pyalgotrade.barfeed.BaseBarFeed`

从实时交易构建条形图的实时 BarFeed。

| 参数： | **maxLen** (*int.*) – `pyalgotrade.dataseries.bards.BarDataSeries`将保存的值的最大数量。一旦有界长度已满，当添加新项时，相应数量的项将从相反的一端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。 |
| --- | --- |

注意

请注意，每笔交易都将创建一个 Bar，因此开盘价、最高价、最低价和收盘价将全部相同。

`getOrderBookUpdateEvent`()

返回当订单簿更新时将发出的事件。

事件处理程序应接收一个参数：

1.  一个`pyalgotrade.bitstamp.wsclient.OrderBookUpdate`实例。

| 返回类型： | `pyalgotrade.observer.Event`。 |  ## 经纪商

*类* `pyalgotrade.bitstamp.broker.``PaperTradingBroker`(*cash*, *barFeed*, *fee=0.0025*)

Bases: `pyalgotrade.bitstamp.broker.BacktestingBroker`

Bitstamp 模拟交易经纪人。

| 参数: |
| --- |

+   **cash** (*int/float.*) – 初始现金金额。

+   **barFeed** (`pyalgotrade.barfeed.BarFeed`) – 提供 K 线图的数据源。

+   **fee** (*float.*) – 每笔订单的手续费百分比。默认为 0.5%。

|

注意

+   仅支持限价订单。

+   订单自动设置为**goodTillCanceled=True**和**allOrNone=False**。

+   BUY_TO_COVER 订单映射到 BUY 订单。

+   SELL_SHORT 订单映射到 SELL 订单。

*class* `pyalgotrade.bitstamp.broker.``LiveBroker`(*clientId*, *key*, *secret*)

Bases: `pyalgotrade.broker.Broker`

Bitstamp 实时交易经纪人。

| 参数: |
| --- |

+   **clientId** (*string.*) – 客户端 ID。

+   **key** (*string.*) – API 密钥。

+   **secret** (*string.*) – API 密钥。

|

注意

+   仅支持限价订单。

+   订单自动设置为**goodTillCanceled=True**和**allOrNone=False**。

+   BUY_TO_COVER 订单映射到 BUY 订单。

+   SELL_SHORT 订单映射到 SELL 订单。

+   API 访问权限应包括:

    +   账户余额

    +   挂单

    +   买入限价单

    +   用户交易

    +   取消订单

    +   卖出限价单

`refreshAccountBalance`()

刷新现金和 BTC 余额。

### 目录

+   bitstamp – Bitstamp 参考

    +   WebSocket

    +   数据源

    +   经纪人

#### 前一个主题

Bitstamp 支持

#### 下一个主题

Bitstamp 示例

### 本页

+   显示源代码

### 快速搜索

输入搜索词或模块、类或函数名。

### 导航

+   索引

+   模块 |

+   下一页 |

+   上一页 |

+   PyAlgoTrade 0.20 文档 »

+   比特币 »

+   Bitstamp 支持 »
