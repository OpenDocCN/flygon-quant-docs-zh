# 计算投资 Part I

> 原文：[`gbeced.github.io/pyalgotrade/docs/v0.20/html/compinvpart1.html`](https://gbeced.github.io/pyalgotrade/docs/v0.20/html/compinvpart1.html)

2012 年我参加了 [计算投资 Part I](https://class.coursera.org/compinvesting1-2012-001/class) 课程，我必须完成一系列的作业，其中之一我使用了 PyAlgoTrade。

## 作业 1

对于这个作业，我必须选择 4 只股票，在 2011 年投资总额为 $1000000，并计算：

> +   最终投资组合价值
> +   
> +   年度回报
> +   
> +   平均每日回报
> +   
> +   每日回报的标准差
> +   
> +   夏普比率

使用以下命令下载数据：

```py
python -m "pyalgotrade.tools.quandl" --source-code="WIKI" --table-code="IBM" --from-year=2011 --to-year=2011 --storage=. --force-download --frequency=daily
python -m "pyalgotrade.tools.quandl" --source-code="WIKI" --table-code="AES" --from-year=2011 --to-year=2011 --storage=. --force-download --frequency=daily
python -m "pyalgotrade.tools.quandl" --source-code="WIKI" --table-code="AIG" --from-year=2011 --to-year=2011 --storage=. --force-download --frequency=daily
python -m "pyalgotrade.tools.quandl" --source-code="WIKI" --table-code="ORCL" --from-year=2011 --to-year=2011 --storage=. --force-download --frequency=daily
```

尽管交付成果是一个 Excel 电子表格，但我使用了这段代码验证了结果：

```py
from __future__ import print_function

from pyalgotrade import strategy
from pyalgotrade.barfeed import quandlfeed
from pyalgotrade.stratanalyzer import returns
from pyalgotrade.stratanalyzer import sharpe
from pyalgotrade.utils import stats

class MyStrategy(strategy.BacktestingStrategy):
    def __init__(self, feed):
        super(MyStrategy, self).__init__(feed, 1000000)

        # We wan't to use adjusted close prices instead of close.
        self.setUseAdjustedValues(True)

        # Place the orders to get them processed on the first bar.
        orders = {
            "ibm": 1996,
            "aes": 22565,
            "aig": 5445,
            "orcl": 8582,
        }
        for instrument, quantity in orders.items():
            self.marketOrder(instrument, quantity, onClose=True, allOrNone=True)

    def onBars(self, bars):
        pass

# Load the bar feed from the CSV file
feed = quandlfeed.Feed()
feed.addBarsFromCSV("ibm", "WIKI-IBM-2011-quandl.csv")
feed.addBarsFromCSV("aes", "WIKI-AES-2011-quandl.csv")
feed.addBarsFromCSV("aig", "WIKI-AIG-2011-quandl.csv")
feed.addBarsFromCSV("orcl", "WIKI-ORCL-2011-quandl.csv")

# Evaluate the strategy with the feed's bars.
myStrategy = MyStrategy(feed)

# Attach returns and sharpe ratio analyzers.
retAnalyzer = returns.Returns()
myStrategy.attachAnalyzer(retAnalyzer)
sharpeRatioAnalyzer = sharpe.SharpeRatio()
myStrategy.attachAnalyzer(sharpeRatioAnalyzer)

# Run the strategy
myStrategy.run()

# Print the results.
print("Final portfolio value: $%.2f" % myStrategy.getResult())
print("Anual return: %.2f  %%" % (retAnalyzer.getCumulativeReturns()[-1] * 100))
print("Average daily return: %.2f  %%" % (stats.mean(retAnalyzer.getReturns()) * 100))
print("Std. dev. daily return: %.4f" % (stats.stddev(retAnalyzer.getReturns())))
print("Sharpe ratio: %.2f" % (sharpeRatioAnalyzer.getSharpeRatio(0))) 
```

结果如下：

```py
Final portfolio value: $876350.83
Anual return: -12.36 %
Average daily return: -0.04 %
Std. dev. daily return: 0.0176
Sharpe ratio: -0.33

```

### 目录 

+   计算投资 Part I

    +   作业 1

