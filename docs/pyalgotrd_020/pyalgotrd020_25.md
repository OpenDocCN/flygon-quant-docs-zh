# Twitter 示例

> 原文：[`gbeced.github.io/pyalgotrade/docs/v0.20/html/twitter_example.html`](https://gbeced.github.io/pyalgotrade/docs/v0.20/html/twitter_example.html)

这个简单示例的目标是向你展示如何将所有部分组合起来，将 Twitter 事件纳入策略中。我们将使用 Bitstamp 的实时反馈，因为不支持使用 Twitter 进行回测，请在继续之前查看*Bitstamp 示例* 部分。

要连接到 Twitter 的 API，您需要：

+   使用者密钥

+   使用者密钥

+   访问令牌

+   访问令牌密钥

转到 [`dev.twitter.com`](http://dev.twitter.com) 并创建一个应用程序。然后，会为您生成使用者密钥和密钥。然后，您需要在“您的访问令牌”部分下创建一个访问令牌。

强调的关键点是：

> 1.  我们使用`pyalgotrade.strategy.BaseStrategy` 而不是 `pyalgotrade.strategy.BacktestingStrategy` 作为基类。这不是一次回测。
> 1.  
> 1.  在运行策略之前，必须将`pyalgotrade.twitter.feed.TwitterFeed` 实例包含在策略事件分派循环中。

```py
from pyalgotrade import strategy
from pyalgotrade.bitstamp import barfeed
from pyalgotrade.bitstamp import broker
from pyalgotrade.twitter import feed as twitterfeed

class Strategy(strategy.BaseStrategy):
    def __init__(self, feed, brk, twitterFeed):
        super(Strategy, self).__init__(feed, brk)
        self.__instrument = "BTC"

        # Subscribe to Twitter events.
        twitterFeed.subscribe(self.__onTweet)

    def __onTweet(self, data):
        # Refer to https://dev.twitter.com/docs/streaming-apis/messages#Public_stream_messages for
        # the information available in data.
        try:
            self.info("Twitter: %s" % (data["text"]))
        except KeyError:
            pass

    def onBars(self, bars):
        bar = bars[self.__instrument]
        self.info("Price: %s. Volume: %s." % (bar.getClose(), bar.getVolume()))

def main():
    # Go to http://dev.twitter.com and create an app.
    # The consumer key and secret will be generated for you after that.
    consumer_key = "<YOUR-CONSUMER-KEY-HERE>"
    consumer_secret = "<YOUR-CONSUMER-SECRET-HERE>"

    # After the step above, you will be redirected to your app's page.
    # Create an access token under the the "Your access token" section
    access_token = "<YOUR-ACCESS-TOKEN-HERE>"
    access_token_secret = "<YOUR-ACCESS-TOKEN-SECRET-HERE>"

    # Create a twitter feed to track BitCoin related events.
    track = ["bitcoin", "btc", "mtgox", "bitstamp", "xapo"]
    follow = []
    languages = ["en"]
    twitterFeed = twitterfeed.TwitterFeed(consumer_key, consumer_secret, access_token, access_token_secret, track, follow, languages)

    barFeed = barfeed.LiveTradeFeed()
    brk = broker.PaperTradingBroker(1000, barFeed)
    strat = Strategy(barFeed, brk, twitterFeed)

    # It is VERY important to add twitterFeed to the event dispatch loop before running the strategy.
    strat.getDispatcher().addSubject(twitterFeed)

    strat.run()

if __name__ == "__main__":
    main() 
```

输出应如下所示：

```py
2014-03-15 02:42:07,696 bitstamp [INFO] Initializing client.
2014-03-15 02:42:08,174 bitstamp [INFO] Connection established.
2014-03-15 02:42:08,175 bitstamp [INFO] Initialization ok.
2014-03-15 02:42:08,175 twitter [INFO] Initializing client.
2014-03-15 02:42:09,238 twitter [INFO] Connected.
2014-03-15 02:42:15,107 strategy [INFO] Twitter: Warren Buffett Urges Investors to ‘Stay Away’ from Bitcoin http://t.co/WWRCr3rfob
2014-03-15 02:42:22,926 strategy [INFO] Twitter: FundingUnion Inc. Bitcoin MLM Social Build  - The Currently in BETA version - Join Today : http://t.co/bxcfoBV0I4 #Bitcoin #MLM #SocialMedia
2014-03-15 02:42:23,577 strategy [INFO] Twitter: #News Alleged Bitcoin creator denies he's the one http://t.co/gmilO85smQ #DailyNews
2014-03-15 02:42:29,378 strategy [INFO] Twitter: RT @Tom_Cruis3: #News Alleged Bitcoin creator denies he's the one http://t.co/gmilO85smQ #DailyNews
2014-03-15 02:42:38,012 strategy [INFO] Twitter: Need for speed dulu at BTC XXI
2014-03-15 02:42:40,162 strategy [INFO] Price: 632.97\. Volume: 0.51896614.
2014-03-15 02:42:45,867 strategy [INFO] Twitter: Analysis of the leaked MTGOX database http://t.co/6ty8EupGh9 via @Reddit
2014-03-15 02:42:46,761 strategy [INFO] Twitter: [ANN] After MtGox implosion, this is the first exchange to implement transparent cold storage http://t.co/EyafH5jXsM #reddit #bitcoin
2014-03-15 02:42:46,825 strategy [INFO] Twitter: @abatalion @naval @cdixon you guys remember CentMail? http://t.co/iihgCS4OwJ would be interesting with a BTC twist.
2014-03-15 02:42:47,285 strategy [INFO] Twitter: Help me convince my Dad to mine Bitcoins! http://t.co/fxboBooINu #reddit #bitcoin
2014-03-15 02:42:47,492 strategy [INFO] Twitter: RT @VentureBeat: Warren Buffett: Bitcoin is a 'mirage' http://t.co/B1FcvZKJQS by @xBarryLevine
2014-03-15 02:42:47,625 strategy [INFO] Twitter: I believe in bitcoin as not only a easy exchange of value but an 'anti bank movement' with occupy wall street, environmentalists, cons...
2014-03-15 02:42:47,979 strategy [INFO] Twitter: Bitcoin protocol/algorithm is being managed by a bunch of people. Isn't that a vulnerability? http://t.co/HJaCkgC6zT #reddit #bitcoin
2014-03-15 02:42:48,388 strategy [INFO] Twitter: Does anyone still think Dorian Nakomoto is Satoshi Nakamoto? http://t.co/v2N8SEZzxC #reddit #bitcoin
2014-03-15 02:42:53,313 strategy [INFO] Twitter: Last sale price at #Bitstamp was $632.99 per #Bitcoin - Free SMS price alerts from 10 exchanges at http://t.co/jsM3JqlvZd
2014-03-15 02:42:54,058 strategy [INFO] Twitter: Last sale price at #BTCe was $623.00 per #Bitcoin - Free SMS price alerts from 10 exchanges at http://t.co/jsM3JqlvZd
2014-03-15 02:42:54,059 strategy [INFO] Twitter: Last sale price at #Coinbase was $632.99 per #Bitcoin - Free SMS price alerts from 10 exchanges at http://t.co/jsM3JqlvZd
2014-03-15 02:42:57,148 strategy [INFO] Twitter: GET PAID WITH USD OR BITCOIN TO PROMOTE LINKS!!!... http://t.co/HKANbuZQHM
2014-03-15 02:43:00,845 strategy [INFO] Twitter: We're back! Bitcoin expert Pete Watson explaining just how the incredible #cryptocurrency works #bitcoin #HongKong http://t.co/CJpTA6RujJ
2014-03-15 02:43:28,001 strategy [INFO] Twitter: Bitcoin's COO Explains What #Bitcoin Is http://t.co/cQ1rVxYSUJ via @YouTube
2014-03-15 02:43:31,888 strategy [INFO] Twitter: #BINARY-OPTIONS! and BITCOIN! Trade Area! With fantastic profits of up to 1,000% per trade and even more. @ http://t.co/R9c9J4Q5VA
2014-03-15 02:43:42,148 strategy [INFO] Twitter: GET PAID WITH USD OR BITCOIN TO PROMOTE LINKS!!!... http://t.co/bD6cacFKTs

```

