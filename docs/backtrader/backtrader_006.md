# 谁在使用它

> 原文：[`www.backtrader.com/home/references/who-is-using-it/`](https://www.backtrader.com/home/references/who-is-using-it/)

请参阅后面的章节了解使用情况的参考资料。

## 旧网站参考资料

原始网站说明*backtrader*至少被以下机构使用：

+   2 家 Eurostoxx50 银行

+   6 家量化交易公司

那是很久以前的事了，作者已经知道更多了，包括其他类型的交易公司，如“能源交易”等行业。

## 为什么没有提供名称？

1.  我从未问过

1.  他们从未问过

## 那你能支撑这些说法吗？

在[Reddit - [Question] How popular is Backtrader on this sub?](https://www.reddit.com/r/algotrading/comments/9k51kt/question_how_popular_is_backtrader_on_this_sub/)中已经问过（并得到了回答）。

让我们引用那个帖子的回答。

引用

不，没有清单。这实际上已经过时了：银行的数量仍然是`2`（可能还有更多，但我不知道），但有超过 6 家公司在内部使用它，包括在能源市场工作的公司，因为`compensation`功能允许购买和销售不同的资产来相互补偿（这可能在`zipline`中不可用），从而允许使用现货和期货价格进行建模（这是能源市场的一个特点，以避免将实际商品交付给您）

这里的关键是必须定义**使用情况**。例如，我可以引用一位来自其中一家银行的人告诉我的话：“我们使用 backtrader 快速原型设计我们的想法并对其进行回测。如果它们被证明符合我们的预期，并经过进一步完善，它们将被重写为 Java 并放入我们的生产系统中”。

实际上，这是一个量化公司（我亲自访问过的）使用的相同方案：在*backtrader*中进行原型设计，然后在*Java*中进行生产。

正如您可以想象的那样，我不追踪那些使用*backtrader*的人的生活，所以也许一些银行和公司决定不再使用*backtrader*。

我也猜测一些银行和量化公司使用`zipline`遵循相同的方案。
