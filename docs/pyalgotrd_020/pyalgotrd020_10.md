# stratanalyzer – 策略分析器

> 原文：[`gbeced.github.io/pyalgotrade/docs/v0.20/html/stratanalyzer.html`](https://gbeced.github.io/pyalgotrade/docs/v0.20/html/stratanalyzer.html)

策略分析器提供了一种可扩展的方式，用于将不同的计算附加到策略执行上。

*类* `pyalgotrade.stratanalyzer.``StrategyAnalyzer`

基类: `object`

策略分析器的基类。

注意

这是一个基类，不应直接使用。

## 返回

*类* `pyalgotrade.stratanalyzer.returns.``Returns`(*maxLen=None*)

基类: `pyalgotrade.stratanalyzer.StrategyAnalyzer`

一个 `pyalgotrade.stratanalyzer.StrategyAnalyzer` ，用于计算整个投资组合的时间加权收益。

| 参数: | **maxLen** (*整数.*) – 在净和累积收益数据序列中保留的最大值数。一旦有界长度已满，当添加新项时，相应数量的项将从相反端丢弃。如果为 None，则使用 dataseries.DEFAULT_MAX_LEN。 |
| --- | --- |

`getCumulativeReturns`()

返回一个`pyalgotrade.dataseries.DataSeries` ，其中包含每个条的累积收益。

`getReturns`()

返回一个`pyalgotrade.dataseries.DataSeries` ，其中包含每个条的收益。  ## 夏普比率

*类* `pyalgotrade.stratanalyzer.sharpe.``SharpeRatio`(*useDailyReturns=True*)

基类: `pyalgotrade.stratanalyzer.StrategyAnalyzer`

一个 `pyalgotrade.stratanalyzer.StrategyAnalyzer` ，用于计算整个投资组合的夏普比率。

| 参数: | **useDailyReturns** (*布尔类型.*) – 如果应使用每日收益而不是每个条的收益，则为 True。 |
| --- | --- |

`getSharpeRatio`(*riskFreeRate*, *annualized=True*)

返回策略执行的夏普比率。如果波动率为 0，则返回 0。

| 参数: |
| --- |

+   **riskFreeRate** (*整数/浮点数.*) – 每年的无风险利率。

+   **annualized** (*布尔类型.*) – 如果夏普比率应年化，则为 True。

|  ## 最大回撤

*类* `pyalgotrade.stratanalyzer.drawdown.``DrawDown`

基类: `pyalgotrade.stratanalyzer.StrategyAnalyzer`

一个 `pyalgotrade.stratanalyzer.StrategyAnalyzer` ，用于计算投资组合的最大回撤和最长回撤持续时间。

`getLongestDrawDownDuration`()

返回最长回撤的持续时间。

| 返回类型: | `datetime.timedelta`. |
| --- | --- |

注意

请注意，这是最长回撤的持续时间，不一定是最深的回撤。

`getMaxDrawDown`()

返回最大（最深）回撤。## 交易

*类* `pyalgotrade.stratanalyzer.trades.``Trades`

基类：`pyalgotrade.stratanalyzer.StrategyAnalyzer`

一个`pyalgotrade.stratanalyzer.StrategyAnalyzer`，记录每笔已完成交易的利润/损失和回报。

注意

此分析器对单个已完成交易进行操作。例如，假设您从$1000 现金开始，然后以$10 购买 XYZ 的 1 股，后来以$20 出售：

> +   该交易的利润为$10。
> +   
> +   即使您的整个投资组合从$1000 增加到$1020，该交易的回报率为 100%。

`getCount`()

返回交易总数。

`getProfitableCount`()

返回盈利交易的数量。

`getUnprofitableCount`()

返回不盈利交易的数量。

`getEvenCount`()

返回净利润为 0 的交易数量。

`getAll`()

返回一个 numpy.array，其中包含每个交易的利润/损失。

`getProfits`()

返回一个 numpy.array，其中包含每个盈利交易的利润。

`getLosses`()

返回一个 numpy.array，其中包含每个亏损交易的损失。

`getAllReturns`()

返回一个 numpy.array，其中包含每个交易的回报。

`getPositiveReturns`()

返回一个 numpy.array，其中包含每个交易的正回报。

`getNegativeReturns`()

返回一个 numpy.array，其中包含每个交易的负回报。

`getCommissionsForAllTrades`()

返回一个 numpy.array，其中包含每个交易的佣金。

`getCommissionsForProfitableTrades`()

返回一个 numpy.array，其中包含每个盈利交易的佣金。

`getCommissionsForUnprofitableTrades`()

返回一个 numpy.array，其中包含每个亏损交易的佣金。

`getCommissionsForEvenTrades`()

返回一个 numpy.array，其中包含净利润为 0 的每个交易的佣金。

## 示例

将此代码保存为 sma_crossover.py：

```py
from pyalgotrade import strategy
from pyalgotrade.technical import ma
from pyalgotrade.technical import cross

class SMACrossOver(strategy.BacktestingStrategy):
    def __init__(self, feed, instrument, smaPeriod):
        super(SMACrossOver, self).__init__(feed)
        self.__instrument = instrument
        self.__position = None
        # We'll use adjusted close values instead of regular close values.
        self.setUseAdjustedValues(True)
        self.__prices = feed[instrument].getPriceDataSeries()
        self.__sma = ma.SMA(self.__prices, smaPeriod)

    def getSMA(self):
        return self.__sma

    def onEnterCanceled(self, position):
        self.__position = None

    def onExitOk(self, position):
        self.__position = None

    def onExitCanceled(self, position):
        # If the exit was canceled, re-submit it.
        self.__position.exitMarket()

    def onBars(self, bars):
        # If a position was not opened, check if we should enter a long position.
        if self.__position is None:
            if cross.cross_above(self.__prices, self.__sma) > 0:
                shares = int(self.getBroker().getCash() * 0.9 / bars[self.__instrument].getPrice())
                # Enter a buy market order. The order is good till canceled.
                self.__position = self.enterLong(self.__instrument, shares, True)
        # Check if we have to exit the position.
        elif not self.__position.exitActive() and cross.cross_below(self.__prices, self.__sma) > 0:
            self.__position.exitMarket() 
```

并将此代码保存在不同的文件中：

```py
from __future__ import print_function

from pyalgotrade.barfeed import yahoofeed
from pyalgotrade.stratanalyzer import returns
from pyalgotrade.stratanalyzer import sharpe
from pyalgotrade.stratanalyzer import drawdown
from pyalgotrade.stratanalyzer import trades

from . import sma_crossover

# Load the bars. This file was manually downloaded from Yahoo Finance.
feed = yahoofeed.Feed()
feed.addBarsFromCSV("orcl", "orcl-2000-yahoofinance.csv")

# Evaluate the strategy with the feed's bars.
myStrategy = sma_crossover.SMACrossOver(feed, "orcl", 20)

# Attach different analyzers to a strategy before executing it.
retAnalyzer = returns.Returns()
myStrategy.attachAnalyzer(retAnalyzer)
sharpeRatioAnalyzer = sharpe.SharpeRatio()
myStrategy.attachAnalyzer(sharpeRatioAnalyzer)
drawDownAnalyzer = drawdown.DrawDown()
myStrategy.attachAnalyzer(drawDownAnalyzer)
tradesAnalyzer = trades.Trades()
myStrategy.attachAnalyzer(tradesAnalyzer)

# Run the strategy.
myStrategy.run()

print("Final portfolio value: $%.2f" % myStrategy.getResult())
print("Cumulative returns: %.2f  %%" % (retAnalyzer.getCumulativeReturns()[-1] * 100))
print("Sharpe ratio: %.2f" % (sharpeRatioAnalyzer.getSharpeRatio(0.05)))
print("Max. drawdown: %.2f  %%" % (drawDownAnalyzer.getMaxDrawDown() * 100))
print("Longest drawdown duration: %s" % (drawDownAnalyzer.getLongestDrawDownDuration()))

print("")
print("Total trades: %d" % (tradesAnalyzer.getCount()))
if tradesAnalyzer.getCount() > 0:
    profits = tradesAnalyzer.getAll()
    print("Avg. profit: $%2.f" % (profits.mean()))
    print("Profits std. dev.: $%2.f" % (profits.std()))
    print("Max. profit: $%2.f" % (profits.max()))
    print("Min. profit: $%2.f" % (profits.min()))
    returns = tradesAnalyzer.getAllReturns()
    print("Avg. return: %2.f %%" % (returns.mean() * 100))
    print("Returns std. dev.: %2.f %%" % (returns.std() * 100))
    print("Max. return: %2.f %%" % (returns.max() * 100))
    print("Min. return: %2.f %%" % (returns.min() * 100))

print("")
print("Profitable trades: %d" % (tradesAnalyzer.getProfitableCount()))
if tradesAnalyzer.getProfitableCount() > 0:
    profits = tradesAnalyzer.getProfits()
    print("Avg. profit: $%2.f" % (profits.mean()))
    print("Profits std. dev.: $%2.f" % (profits.std()))
    print("Max. profit: $%2.f" % (profits.max()))
    print("Min. profit: $%2.f" % (profits.min()))
    returns = tradesAnalyzer.getPositiveReturns()
    print("Avg. return: %2.f %%" % (returns.mean() * 100))
    print("Returns std. dev.: %2.f %%" % (returns.std() * 100))
    print("Max. return: %2.f %%" % (returns.max() * 100))
    print("Min. return: %2.f %%" % (returns.min() * 100))

print("")
print("Unprofitable trades: %d" % (tradesAnalyzer.getUnprofitableCount()))
if tradesAnalyzer.getUnprofitableCount() > 0:
    losses = tradesAnalyzer.getLosses()
    print("Avg. loss: $%2.f" % (losses.mean()))
    print("Losses std. dev.: $%2.f" % (losses.std()))
    print("Max. loss: $%2.f" % (losses.min()))
    print("Min. loss: $%2.f" % (losses.max()))
    returns = tradesAnalyzer.getNegativeReturns()
    print("Avg. return: %2.f %%" % (returns.mean() * 100))
    print("Returns std. dev.: %2.f %%" % (returns.std() * 100))
    print("Max. return: %2.f %%" % (returns.max() * 100))
    print("Min. return: %2.f %%" % (returns.min() * 100)) 
```

输出应如下所示：

```py
Final portfolio value: $1295462.60
Cumulative returns: 29.55 %
Sharpe ratio: 0.70
Max. drawdown: 24.58 %
Longest drawdown duration: 277 days, 0:00:00

Total trades: 13
Avg. profit: $14391
Profits std. dev.: $127520
Max. profit: $420782
Min. profit: $-89317
Avg. return:  2 %
Returns std. dev.: 13 %
Max. return: 46 %
Min. return: -7 %

Profitable trades: 3
Avg. profit: $196972
Profits std. dev.: $158985
Max. profit: $420782
Min. profit: $66466
Avg. return: 21 %
Returns std. dev.: 18 %
Max. return: 46 %
Min. return:  6 %

Unprofitable trades: 10
Avg. loss: $-40383
Losses std. dev.: $23579
Max. loss: $-89317
Min. loss: $-4702
Avg. return: -3 %
Returns std. dev.:  2 %
Max. return: -0 %
Min. return: -7 %

```

### 目录的表

+   策略分析器 – 策略分析器

    +   回报

    +   夏普比率

    +   回撤

    +   交易

    +   示例

