# twitter – Twitter feed reference

> 原文：[`gbeced.github.io/pyalgotrade/docs/v0.20/html/twitter_ref.html`](https://gbeced.github.io/pyalgotrade/docs/v0.20/html/twitter_ref.html)

## Feed

*类* `pyalgotrade.twitter.feed.``TwitterFeed`(*consumerKey*, *consumerSecret*, *accessToken*, *accessTokenSecret*, *track=*[], *follow=*[], *languages=*[])

基类: `pyalgotrade.observer.Subject`

负责连接到 Twitter 的公共流 API 并分发事件的类。有关更多信息，请查看[`dev.twitter.com/docs/streaming-apis/streams/public`](https://dev.twitter.com/docs/streaming-apis/streams/public)。

| 参数: |
| --- |

+   **consumerKey** (*字符串.*) – 消费者密钥。

+   **consumerSecret** (*字符串.*) – 消费者密钥。

+   **accessToken** (*字符串.*) – 访问令牌。

+   **accessTokenSecret** (*字符串.*) – 访问令牌密钥。

+   **track** (*列表.*) – 用于确定将在流上发布哪些推文的短语列表。一个短语可以是一个或多个由空格分隔的术语，并且如果短语中的所有术语都出现在推文中，则匹配短语，无论顺序如何并且忽略大小写。

+   **follow** (*列表.*) – 一个用户 ID 列表，表示应该在流上发布推文的用户。不支持关注受保护的用户。

+   **languages** (*列表.*) – 语言 ID 列表，定义在[`tools.ietf.org/html/bcp47`](http://tools.ietf.org/html/bcp47)中。

|

注

+   转到[`dev.twitter.com`](http://dev.twitter.com)并创建一个应用程序。之后，消费者密钥和密钥将为您生成。

+   在“您的访问令牌”部分创建一个访问令牌。

+   至少**track**或**follow**必须设置。

`subscribe`(*回调*)

订阅 Twitter 事件。事件处理程序将接收一个与[`dev.twitter.com/docs/streaming-apis/messages#Public_stream_messages`](https://dev.twitter.com/docs/streaming-apis/messages#Public_stream_messages)中定义的数据相符的字典。

### 目录

+   twitter – Twitter feed reference

    +   Feed

