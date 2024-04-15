# 订单管理和执行

> 原文：[`www.backtrader.com/blog/posts/2015-08-08-order-creation-execution/order-creation-execution/`](https://www.backtrader.com/blog/posts/2015-08-08-order-creation-execution/order-creation-execution/)

回测，因此 backtrader，如果不能模拟订单，将不完整。为此，平台提供了以下功能。

对于订单管理有 3 个原语：

+   `购买`

+   `卖出`

+   `取消`

注意

`update`原语显然是一种逻辑，但常识告诉我们，这种方法主要由使用判断性交易方法的手动操作员使用。

对于订单执行逻辑，以下执行类型：

+   `市价`

+   `关闭`

+   `限价`

+   `停止`

+   `StopLimit`

## 订单管理

主要目标是易于使用，因此进行订单管理的最直接（和简单）的方法是从策略本身开始。

`buy`和`self`原语具有以下签名作为`Strategy`方法：

+   def buy(self, data=None, size=None, price=None, plimit=None, exectype=None, valid=None):

+   **def buy(self, data=None, size=None, price=None, exectype=None, valid=None)**

    +   `data` -> 数据源引用，用于购买的资产

    如果传递`None`，则策略的主要数据将用作目标

    +   `size` -> 确定要应用的股份的 int/long

    如果传递`None`，则策略中可用的`Sizer`将被用于自动确定股份。默认的`Sizer`使用固定状态**1**

    +   `price` -> 对于`Market`将被忽略并且可以留空，但对于其他订单类型必须是浮点数。如果留空，则将使用当前收盘价

    +   `plimit` -> 在`StopLimit`订单中的限价，其中`price`将被用作触发价格

    如果留空，则`price`将被用作限价（触发和限价相同）

    +   `exectype` -> 订单执行类型之一。如果传递`None`，则将假定为`Market`

    执行类型在`Order`中被枚举。例如：`Order.Limit`

    +   `valid` -> 从 date2num（或数据源）获取的浮点值或 datetime.datetime Python 对象

    **注意**：`Market`订单将在不考虑`valid`参数的情况下执行

    返回值：一个`Order`实例

+   **def sell(self, data=None, size=None, price=None, exectype=None, valid=None)**

因为取消订单只需要`buy`或`self`返回的`order`引用，所以经纪人的原语可以使用（见下文）

一些例子：

```py
# buy the main date, with sizer default stake, Market order
order = self.buy()

# Market order - valid will be "IGNORED"
order = self.buy(valid=datetime.datetime.now() + datetime.timedelta(days=3))

# Market order - price will be IGNORED
order = self.buy(price=self.data.close[0] * 1.02)

# Market order - manual stake
order = self.buy(size=25)

# Limit order - want to set the price and can set a validity
order = self.buy(exectype=Order.Limit,
                 price=self.data.close[0] * 1.02,
                 valid=datetime.datetime.now() + datetime.timedelta(days=3)))

# StopLimit order - want to set the price, price limit
order = self.buy(exectype=Order.StopLimit,
                 price=self.data.close[0] * 1.02,
                 plimit=self.data.close[0] * 1.07)

# Canceling an existing order
self.broker.cancel(order)
```

注意

所有订单类型都可以通过创建`Order`实例（或其子类之一），然后通过以下方式传递给经纪人：

```py
order = self.broker.submit(order)
```

注意

`broker`本身有`buy`和`sell`原语，但对于默认参数要求更严格。

## 订单执行逻辑

`broker`使用 2 个主要准则（假设？）进行订单执行。

+   当前数据已经发生，不能用于执行订单。

    如果策略中的逻辑如下：

    ```py
     `if self.data.close > self.sma:  # where sma is a Simple Moving Average
         self.buy()

    The expectation CANNOT be that the order will be executed with the
    ``close`` price which is being examined in the logic BECAUSE it has already
    happened.

    The order CAN BE 1st EXECUTED withing the bounds of the next set of
    Open/High/Low/Close price points (and the conditions set forth herein by
    the order)` 
    ```

+   交易量不起作用

    实际上，在真实交易中，如果交易员选择非流动性资产，或者精确地击中了价格条的极值（高/低），订单将被取消。

    但是击中高/低点是个罕见的事件（如果你这么做...你就不需要`backtrader`了），并且所选择的资产将有足够的流动性来吸收任何常规交易的订单

### 市场

执行：

下一个一组 Open/High/Low/Close 价格（通常称为*bar*）的开盘价

理由：

如果逻辑在时间点 X 执行并发出了`Market`订单，则接下来会发生的价格点是即将到来的`open`价格

注意

这个订单总是执行，忽略任何用于创建它的`price`和`valid`参数

### 关闭

执行：

当下一个价格条实际上关闭时，使用下一个价格条的`close`价格

理由：

大多数**回测**数据源已经包含了**已关闭**的价格条，订单将立即执行，使用下一个价格条的`close`价格。每日数据源是最常见的例子。

但是系统可以被喂入“tick”价格，实际的条（按时间/日期）正在不断地随着新的 ticks 更新，而实际上并没有移动到**下一个**条（因为时间和/或日期没有改变）

只有当时间或日期改变时，条才会实际上被关闭，订单才会执行

### 限价

执行：

订单创建时设置的`price`，如果`data`触及它，则从下一个价格条开始。

如果设置了`valid`并且到达了时间点，订单将被取消

价格匹配：

`backtrader`试图为`Limit`订单提供**最真实的执行价格**。

使用 4 个价格点（Open/High/Low/Close），可以部分推断请求的`price`是否可以改善。

对于`Buy`订单

```py
- Case 1:

  If the `open` price of the bar is below the limit price the order
  executes immediately with the `open` price. The order has been swept
  during the opening phase of the session

- Case 2:

  If the `open` price has not penetrated below the limit price but the
  `low` price is below the limit price, then the limit price has been
  seen during the session and the order can be executed
```

对于`Sell`订单，逻辑显然是相反的。

### 止损

执行：

订单创建时设置的触发器`price`，如果`data`触及它，则从下一个价格条开始。

如果设置了`valid`并且到达了时间点，订单将被取消

价格匹配：

`backtrader`试图为`Stop`订单提供**最真实的触发价格**。

使用 4 个价格点（Open/High/Low/Close），可以部分推断请求的`price`是否可以改善。

对于`\`Stop`orders which`Buy`

```py
- Case 1:

  If the `open` price of the bar is above the stop price the order is
  executed immediately with the `open` price.

  Intended to stop a loss if the price is moving upwards against an
  existing short position

- Case 2:

  If the `open` price has not penetrated above the stop price but the
  `high` price is above the stop price, then the stop price has been
  seen during the session and the order can be executed
```

对于`Sell`订单，逻辑显然是相反的。

### 止损限价

执行：

触发器`price`设置了订单的启动，从下一个价格条开始。

价格匹配：

+   **触发器**：使用`Stop`匹配逻辑（但仅触发并将订单转换为`Limit`订单）

+   **限制**：使用`Limit`价格匹配逻辑

## 一些样本

像往常一样，图片（带代码）价值数百万长的解释。请注意，代码片段集中在订单创建部分。完整代码在底部。

一个*价格高于/低于简单移动平均线*策略将用于生成买入/卖出信号

信号在图表底部可见：使用交叉指标的`CrossOver`。

仅保留对生成的“买入”订单的引用，以允许系统中最多只有一个同时订单。

### 执行类型：市价

请在图表中查看订单如何在生成信号后一根棒棒后以开盘价执行。

```py
 `if self.p.exectype == 'Market':
                self.buy(exectype=bt.Order.Market)  # default if not given

                self.log('BUY CREATE, exectype Market, price %.2f' %
                         self.data.close[0])
```

输出图表。

![图像](img/75493f6cd926c5e6f5e940b5d038dc09.png)

命令行和输出：

```py
$ ./order-execution-samples.py --exectype Market
2006-01-26T23:59:59+00:00, BUY CREATE, exectype Market, price 3641.42
2006-01-26T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-01-27T23:59:59+00:00, BUY EXECUTED, Price: 3643.35, Cost: 3643.35, Comm 0.00
2006-03-02T23:59:59+00:00, SELL CREATE, 3763.73
2006-03-02T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-03-03T23:59:59+00:00, SELL EXECUTED, Price: 3763.95, Cost: 3763.95, Comm 0.00
...
...
2006-12-11T23:59:59+00:00, BUY CREATE, exectype Market, price 4052.89
2006-12-11T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-12-12T23:59:59+00:00, BUY EXECUTED, Price: 4052.55, Cost: 4052.55, Comm 0.00
```

### 执行类型：收盘

注意

在[Issue＃11](https://github.com/mementum/backtrader/issues/11)之后，创建了一个[开发](https://github.com/mementum/backtrader/commit/2a3be8fcaf9cd0a7577dc2cbd31899fbd8a53143)分支，更新了图表和输出。错误的收盘价格被使用。

现在订单也是在信号后一根棒棒后执行，但是使用的是收盘价。

```py
 `elif self.p.exectype == 'Close':
                self.buy(exectype=bt.Order.Close)

                self.log('BUY CREATE, exectype Close, price %.2f' %
                         self.data.close[0])
```

输出图表。

![图像](img/204a997b73b6ef7a404d4540bee383b3.png)

命令行和输出：

```py
$ ./order-execution-samples.py --exectype Close
2006-01-26T23:59:59+00:00, BUY CREATE, exectype Close, price 3641.42
2006-01-26T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-01-27T23:59:59+00:00, BUY EXECUTED, Price: 3685.48, Cost: 3685.48, Comm 0.00
2006-03-02T23:59:59+00:00, SELL CREATE, 3763.73
2006-03-02T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-03-03T23:59:59+00:00, SELL EXECUTED, Price: 3763.95, Cost: 3763.95, Comm 0.00
...
...
2006-11-06T23:59:59+00:00, BUY CREATE, exectype Close, price 4045.22
2006-11-06T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-11-07T23:59:59+00:00, BUY EXECUTED, Price: 4072.86, Cost: 4072.86, Comm 0.00
2006-11-24T23:59:59+00:00, SELL CREATE, 4048.16
2006-11-24T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-11-27T23:59:59+00:00, SELL EXECUTED, Price: 4045.05, Cost: 4045.05, Comm 0.00
2006-12-11T23:59:59+00:00, BUY CREATE, exectype Close, price 4052.89
2006-12-11T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-12-12T23:59:59+00:00, BUY EXECUTED, Price: 4059.74, Cost: 4059.74, Comm 0.00
```

### 执行类型：限价

在几行之前计算有效性以防已作为参数传递。

```py
 `if self.p.valid:
                valid = self.data.datetime.date(0) + \
                        datetime.timedelta(days=self.p.valid)
            else:
                valid = None
```

设置了信号生成价格（信号条的收盘价）下跌 1％的限价。请注意，这阻止了上面许多订单的执行。

```py
 `elif self.p.exectype == 'Limit':
                price = self.data.close * (1.0 - self.p.perc1 / 100.0)

                self.buy(exectype=bt.Order.Limit, price=price, valid=valid)

                if self.p.valid:
                    txt = 'BUY CREATE, exectype Limit, price %.2f, valid: %s'
                    self.log(txt % (price, valid.strftime('%Y-%m-%d')))
                else:
                    txt = 'BUY CREATE, exectype Limit, price %.2f'
                    self.log(txt % price)
```

输出图表。

![图像](img/a1956e920eda173a6839a818bd36a59f.png)

仅发布了 4 个订单。尝试捕捉小幅度下跌的价格完全改变了输出。

命令行和输出：

```py
$ ./order-execution-samples.py --exectype Limit --perc1 1
2006-01-26T23:59:59+00:00, BUY CREATE, exectype Limit, price 3605.01
2006-01-26T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-05-18T23:59:59+00:00, BUY EXECUTED, Price: 3605.01, Cost: 3605.01, Comm 0.00
2006-06-05T23:59:59+00:00, SELL CREATE, 3604.33
2006-06-05T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-06-06T23:59:59+00:00, SELL EXECUTED, Price: 3598.58, Cost: 3598.58, Comm 0.00
2006-06-21T23:59:59+00:00, BUY CREATE, exectype Limit, price 3491.57
2006-06-21T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-06-28T23:59:59+00:00, BUY EXECUTED, Price: 3491.57, Cost: 3491.57, Comm 0.00
2006-07-13T23:59:59+00:00, SELL CREATE, 3562.56
2006-07-13T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-07-14T23:59:59+00:00, SELL EXECUTED, Price: 3545.92, Cost: 3545.92, Comm 0.00
2006-07-24T23:59:59+00:00, BUY CREATE, exectype Limit, price 3596.60
2006-07-24T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
```

### 执行类型：限价与有效期

为了不会永远等待一个限价订单，该订单仅在 4（日历）天内有效，该订单可能只有在价格对“买入”订单不利时才执行。

输出图表。

![图像](img/e2e5e259e8f724b8af597bb386e7f0aa.png)

更多订单已生成，但除了一个“买入”订单过期外，其他所有订单都过期了，进一步限制了操作数量。

命令行和输出：

```py
$ ./order-execution-samples.py --exectype Limit --perc1 1 --valid 4
2006-01-26T23:59:59+00:00, BUY CREATE, exectype Limit, price 3605.01, valid: 2006-01-30
2006-01-26T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-01-30T23:59:59+00:00, BUY EXPIRED
2006-03-10T23:59:59+00:00, BUY CREATE, exectype Limit, price 3760.48, valid: 2006-03-14
2006-03-10T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-03-14T23:59:59+00:00, BUY EXPIRED
2006-03-30T23:59:59+00:00, BUY CREATE, exectype Limit, price 3835.86, valid: 2006-04-03
2006-03-30T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-04-03T23:59:59+00:00, BUY EXPIRED
2006-04-20T23:59:59+00:00, BUY CREATE, exectype Limit, price 3821.40, valid: 2006-04-24
2006-04-20T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-04-24T23:59:59+00:00, BUY EXPIRED
2006-05-04T23:59:59+00:00, BUY CREATE, exectype Limit, price 3804.65, valid: 2006-05-08
2006-05-04T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-05-08T23:59:59+00:00, BUY EXPIRED
2006-06-01T23:59:59+00:00, BUY CREATE, exectype Limit, price 3611.85, valid: 2006-06-05
2006-06-01T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-06-05T23:59:59+00:00, BUY EXPIRED
2006-06-21T23:59:59+00:00, BUY CREATE, exectype Limit, price 3491.57, valid: 2006-06-25
2006-06-21T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-06-26T23:59:59+00:00, BUY EXPIRED
2006-07-24T23:59:59+00:00, BUY CREATE, exectype Limit, price 3596.60, valid: 2006-07-28
2006-07-24T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-07-28T23:59:59+00:00, BUY EXPIRED
2006-09-12T23:59:59+00:00, BUY CREATE, exectype Limit, price 3751.07, valid: 2006-09-16
2006-09-12T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-09-18T23:59:59+00:00, BUY EXPIRED
2006-09-20T23:59:59+00:00, BUY CREATE, exectype Limit, price 3802.90, valid: 2006-09-24
2006-09-20T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-09-22T23:59:59+00:00, BUY EXECUTED, Price: 3802.90, Cost: 3802.90, Comm 0.00
2006-11-02T23:59:59+00:00, SELL CREATE, 3974.62
2006-11-02T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-11-03T23:59:59+00:00, SELL EXECUTED, Price: 3979.73, Cost: 3979.73, Comm 0.00
2006-11-06T23:59:59+00:00, BUY CREATE, exectype Limit, price 4004.77, valid: 2006-11-10
2006-11-06T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-11-10T23:59:59+00:00, BUY EXPIRED
2006-12-11T23:59:59+00:00, BUY CREATE, exectype Limit, price 4012.36, valid: 2006-12-15
2006-12-11T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-12-15T23:59:59+00:00, BUY EXPIRED
```

### 执行类型：止损

设置了信号价格上涨 1％的止损价。这意味着策略仅在生成信号并价格继续上涨时购买，这可以被解释为强势信号。

这完全改变了执行情况。

```py
 `elif self.p.exectype == 'Stop':
                price = self.data.close * (1.0 + self.p.perc1 / 100.0)

                self.buy(exectype=bt.Order.Stop, price=price, valid=valid)

                if self.p.valid:
                    txt = 'BUY CREATE, exectype Stop, price %.2f, valid: %s'
                    self.log(txt % (price, valid.strftime('%Y-%m-%d')))
                else:
                    txt = 'BUY CREATE, exectype Stop, price %.2f'
                    self.log(txt % price)
```

输出图表。

![图像](img/89ebb54a70045e1d20f02c350126f3e5.png)

命令行和输出：

```py
$ ./order-execution-samples.py --exectype Stop --perc1 1
2006-01-26T23:59:59+00:00, BUY CREATE, exectype Stop, price 3677.83
2006-01-26T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-01-27T23:59:59+00:00, BUY EXECUTED, Price: 3677.83, Cost: 3677.83, Comm 0.00
2006-03-02T23:59:59+00:00, SELL CREATE, 3763.73
2006-03-02T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-03-03T23:59:59+00:00, SELL EXECUTED, Price: 3763.95, Cost: 3763.95, Comm 0.00
2006-03-10T23:59:59+00:00, BUY CREATE, exectype Stop, price 3836.44
2006-03-10T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-03-15T23:59:59+00:00, BUY EXECUTED, Price: 3836.44, Cost: 3836.44, Comm 0.00
2006-03-28T23:59:59+00:00, SELL CREATE, 3811.45
2006-03-28T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-03-29T23:59:59+00:00, SELL EXECUTED, Price: 3811.85, Cost: 3811.85, Comm 0.00
2006-03-30T23:59:59+00:00, BUY CREATE, exectype Stop, price 3913.36
2006-03-30T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-09-29T23:59:59+00:00, BUY EXECUTED, Price: 3913.36, Cost: 3913.36, Comm 0.00
2006-11-02T23:59:59+00:00, SELL CREATE, 3974.62
2006-11-02T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-11-03T23:59:59+00:00, SELL EXECUTED, Price: 3979.73, Cost: 3979.73, Comm 0.00
2006-11-06T23:59:59+00:00, BUY CREATE, exectype Stop, price 4085.67
2006-11-06T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-11-13T23:59:59+00:00, BUY EXECUTED, Price: 4085.67, Cost: 4085.67, Comm 0.00
2006-11-24T23:59:59+00:00, SELL CREATE, 4048.16
2006-11-24T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-11-27T23:59:59+00:00, SELL EXECUTED, Price: 4045.05, Cost: 4045.05, Comm 0.00
2006-12-11T23:59:59+00:00, BUY CREATE, exectype Stop, price 4093.42
2006-12-11T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-12-13T23:59:59+00:00, BUY EXECUTED, Price: 4093.42, Cost: 4093.42, Comm 0.00
```

### 执行类型：止损限价

设置了信号价格上涨 1％的止损价。但限价设置在信号（收盘）价格上涨 0.5％，这可以解释为：等待力量展现，但不要买入高峰。等待下跌。

有效期限制为 20（日历）天

```py
 `elif self.p.exectype == 'StopLimit':
                price = self.data.close * (1.0 + self.p.perc1 / 100.0)

                plimit = self.data.close * (1.0 + self.p.perc2 / 100.0)

                self.buy(exectype=bt.Order.StopLimit, price=price, valid=valid,
                         plimit=plimit)

                if self.p.valid:
                    txt = ('BUY CREATE, exectype StopLimit, price %.2f,'
                           ' valid: %s, pricelimit: %.2f')
                    self.log(txt % (price, valid.strftime('%Y-%m-%d'), plimit))
                else:
                    txt = ('BUY CREATE, exectype StopLimit, price %.2f,'
                           ' pricelimit: %.2f')
                    self.log(txt % (price, plimit))
```

输出图表。

![图像](img/f7562c66811abf29fb0869dfefa3fcf3.png)

命令行和输出：

```py
$ ./order-execution-samples.py --exectype StopLimit --perc1 1 --perc2 0.5 --valid 20
2006-01-26T23:59:59+00:00, BUY CREATE, exectype StopLimit, price 3677.83, valid: 2006-02-15, pricelimit: 3659.63
2006-01-26T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-02-03T23:59:59+00:00, BUY EXECUTED, Price: 3659.63, Cost: 3659.63, Comm 0.00
2006-03-02T23:59:59+00:00, SELL CREATE, 3763.73
2006-03-02T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-03-03T23:59:59+00:00, SELL EXECUTED, Price: 3763.95, Cost: 3763.95, Comm 0.00
2006-03-10T23:59:59+00:00, BUY CREATE, exectype StopLimit, price 3836.44, valid: 2006-03-30, pricelimit: 3817.45
2006-03-10T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-03-21T23:59:59+00:00, BUY EXECUTED, Price: 3817.45, Cost: 3817.45, Comm 0.00
2006-03-28T23:59:59+00:00, SELL CREATE, 3811.45
2006-03-28T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-03-29T23:59:59+00:00, SELL EXECUTED, Price: 3811.85, Cost: 3811.85, Comm 0.00
2006-03-30T23:59:59+00:00, BUY CREATE, exectype StopLimit, price 3913.36, valid: 2006-04-19, pricelimit: 3893.98
2006-03-30T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-04-19T23:59:59+00:00, BUY EXPIRED
...
...
2006-12-11T23:59:59+00:00, BUY CREATE, exectype StopLimit, price 4093.42, valid: 2006-12-31, pricelimit: 4073.15
2006-12-11T23:59:59+00:00, ORDER ACCEPTED/SUBMITTED
2006-12-22T23:59:59+00:00, BUY EXECUTED, Price: 4073.15, Cost: 4073.15, Comm 0.00
```

### 测试脚本执行

在命令行的`help`中详细说明：

```py
$ ./order-execution-samples.py --help
usage: order-execution-samples.py [-h] [--infile INFILE]
                                  [--csvformat {bt,visualchart,sierrachart,yahoo,yahoo_unreversed}]
                                  [--fromdate FROMDATE] [--todate TODATE]
                                  [--plot] [--plotstyle {bar,line,candle}]
                                  [--numfigs NUMFIGS] [--smaperiod SMAPERIOD]
                                  [--exectype EXECTYPE] [--valid VALID]
                                  [--perc1 PERC1] [--perc2 PERC2]

Showcase for Order Execution Types

optional arguments:
  -h, --help            show this help message and exit
  --infile INFILE, -i INFILE
                        File to be read in
  --csvformat {bt,visualchart,sierrachart,yahoo,yahoo_unreversed},
  -c {bt,visualchart,sierrachart,yahoo,yahoo_unreversed}
                        CSV Format
  --fromdate FROMDATE, -f FROMDATE
                        Starting date in YYYY-MM-DD format
  --todate TODATE, -t TODATE
                        Ending date in YYYY-MM-DD format
  --plot, -p            Plot the read data
  --plotstyle {bar,line,candle}, -ps {bar,line,candle}
                        Plot the read data
  --numfigs NUMFIGS, -n NUMFIGS
                        Plot using n figures
  --smaperiod SMAPERIOD, -s SMAPERIOD
                      Simple Moving Average Period
  --exectype EXECTYPE, -e EXECTYPE
                        Execution Type: Market (default), Close, Limit,
                        Stop, StopLimit
  --valid VALID, -v VALID
                        Validity for Limit sample: default 0 days
  --perc1 PERC1, -p1 PERC1
                        % distance from close price at order creation time for
                        the limit/trigger price in Limit/Stop orders
  --perc2 PERC2, -p2 PERC2
                        % distance from close price at order creation time for
                        the limit price in StopLimit orders
```

### 完整代码

```py
from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
import datetime
import os.path
import time
import sys

import backtrader as bt
import backtrader.feeds as btfeeds
import backtrader.indicators as btind

class OrderExecutionStrategy(bt.Strategy):
    params = (
        ('smaperiod', 15),
        ('exectype', 'Market'),
        ('perc1', 3),
        ('perc2', 1),
        ('valid', 4),
    )

    def log(self, txt, dt=None):
        ''' Logging function fot this strategy'''
        dt = dt or self.data.datetime[0]
        if isinstance(dt, float):
            dt = bt.num2date(dt)
        print('%s, %s' % (dt.isoformat(), txt))

    def notify_order(self, order):
        if order.status in [order.Submitted, order.Accepted]:
            # Buy/Sell order submitted/accepted to/by broker - Nothing to do
            self.log('ORDER ACCEPTED/SUBMITTED', dt=order.created.dt)
            self.order = order
            return

        if order.status in [order.Expired]:
            self.log('BUY EXPIRED')

        elif order.status in [order.Completed]:
            if order.isbuy():
                self.log(
                    'BUY EXECUTED, Price: %.2f, Cost: %.2f, Comm %.2f' %
                    (order.executed.price,
                     order.executed.value,
                     order.executed.comm))

            else:  # Sell
                self.log('SELL EXECUTED, Price: %.2f, Cost: %.2f, Comm %.2f' %
                         (order.executed.price,
                          order.executed.value,
                          order.executed.comm))

        # Sentinel to None: new orders allowed
        self.order = None

    def __init__(self):
        # SimpleMovingAverage on main data
        # Equivalent to -> sma = btind.SMA(self.data, period=self.p.smaperiod)
        sma = btind.SMA(period=self.p.smaperiod)

        # CrossOver (1: up, -1: down) close / sma
        self.buysell = btind.CrossOver(self.data.close, sma, plot=True)

        # Sentinel to None: new ordersa allowed
        self.order = None

    def next(self):
        if self.order:
            # An order is pending ... nothing can be done
            return

        # Check if we are in the market
        if self.position:
            # In the maerket - check if it's the time to sell
            if self.buysell < 0:
                self.log('SELL CREATE, %.2f' % self.data.close[0])
                self.sell()

        elif self.buysell > 0:
            if self.p.valid:
                valid = self.data.datetime.date(0) + \
                        datetime.timedelta(days=self.p.valid)
            else:
                valid = None

            # Not in the market and signal to buy
            if self.p.exectype == 'Market':
                self.buy(exectype=bt.Order.Market)  # default if not given

                self.log('BUY CREATE, exectype Market, price %.2f' %
                         self.data.close[0])

            elif self.p.exectype == 'Close':
                self.buy(exectype=bt.Order.Close)

                self.log('BUY CREATE, exectype Close, price %.2f' %
                         self.data.close[0])

            elif self.p.exectype == 'Limit':
                price = self.data.close * (1.0 - self.p.perc1 / 100.0)

                self.buy(exectype=bt.Order.Limit, price=price, valid=valid)

                if self.p.valid:
                    txt = 'BUY CREATE, exectype Limit, price %.2f, valid: %s'
                    self.log(txt % (price, valid.strftime('%Y-%m-%d')))
                else:
                    txt = 'BUY CREATE, exectype Limit, price %.2f'
                    self.log(txt % price)

            elif self.p.exectype == 'Stop':
                price = self.data.close * (1.0 + self.p.perc1 / 100.0)

                self.buy(exectype=bt.Order.Stop, price=price, valid=valid)

                if self.p.valid:
                    txt = 'BUY CREATE, exectype Stop, price %.2f, valid: %s'
                    self.log(txt % (price, valid.strftime('%Y-%m-%d')))
                else:
                    txt = 'BUY CREATE, exectype Stop, price %.2f'
                    self.log(txt % price)

            elif self.p.exectype == 'StopLimit':
                price = self.data.close * (1.0 + self.p.perc1 / 100.0)

                plimit = self.data.close * (1.0 + self.p.perc2 / 100.0)

                self.buy(exectype=bt.Order.StopLimit, price=price, valid=valid,
                         plimit=plimit)

                if self.p.valid:
                    txt = ('BUY CREATE, exectype StopLimit, price %.2f,'
                           ' valid: %s, pricelimit: %.2f')
                    self.log(txt % (price, valid.strftime('%Y-%m-%d'), plimit))
                else:
                    txt = ('BUY CREATE, exectype StopLimit, price %.2f,'
                           ' pricelimit: %.2f')
                    self.log(txt % (price, plimit))

def runstrat():
    args = parse_args()

    cerebro = bt.Cerebro()

    data = getdata(args)
    cerebro.adddata(data)

    cerebro.addstrategy(
        OrderExecutionStrategy,
        exectype=args.exectype,
        perc1=args.perc1,
        perc2=args.perc2,
        valid=args.valid,
        smaperiod=args.smaperiod
    )
    cerebro.run()

    if args.plot:
        cerebro.plot(numfigs=args.numfigs, style=args.plotstyle)

def getdata(args):

    dataformat = dict(
        bt=btfeeds.BacktraderCSVData,
        visualchart=btfeeds.VChartCSVData,
        sierrachart=btfeeds.SierraChartCSVData,
        yahoo=btfeeds.YahooFinanceCSVData,
        yahoo_unreversed=btfeeds.YahooFinanceCSVData
    )

    dfkwargs = dict()
    if args.csvformat == 'yahoo_unreversed':
        dfkwargs['reverse'] = True

    if args.fromdate:
        fromdate = datetime.datetime.strptime(args.fromdate, '%Y-%m-%d')
        dfkwargs['fromdate'] = fromdate

    if args.todate:
        fromdate = datetime.datetime.strptime(args.todate, '%Y-%m-%d')
        dfkwargs['todate'] = todate

    dfkwargs['dataname'] = args.infile

    dfcls = dataformat[args.csvformat]

    return dfcls(**dfkwargs)

def parse_args():
    parser = argparse.ArgumentParser(
        description='Showcase for Order Execution Types')

    parser.add_argument('--infile', '-i', required=False,
                        default='../datas/2006-day-001.txt',
                        help='File to be read in')

    parser.add_argument('--csvformat', '-c', required=False, default='bt',
                        choices=['bt', 'visualchart', 'sierrachart',
                                 'yahoo', 'yahoo_unreversed'],
                        help='CSV Format')

    parser.add_argument('--fromdate', '-f', required=False, default=None,
                        help='Starting date in YYYY-MM-DD format')

    parser.add_argument('--todate', '-t', required=False, default=None,
                        help='Ending date in YYYY-MM-DD format')

    parser.add_argument('--plot', '-p', action='store_false', required=False,
                        help='Plot the read data')

    parser.add_argument('--plotstyle', '-ps', required=False, default='bar',
                        choices=['bar', 'line', 'candle'],
                        help='Plot the read data')

    parser.add_argument('--numfigs', '-n', required=False, default=1,
                        help='Plot using n figures')

    parser.add_argument('--smaperiod', '-s', required=False, default=15,
                        help='Simple Moving Average Period')

    parser.add_argument('--exectype', '-e', required=False, default='Market',
                        help=('Execution Type: Market (default), Close, Limit,'
                              ' Stop, StopLimit'))

    parser.add_argument('--valid', '-v', required=False, default=0, type=int,
                        help='Validity for Limit sample: default 0 days')

    parser.add_argument('--perc1', '-p1', required=False, default=0.0,
                        type=float,
                        help=('%% distance from close price at order creation'
                              ' time for the limit/trigger price in Limit/Stop'
                              ' orders'))

    parser.add_argument('--perc2', '-p2', required=False, default=0.0,
                        type=float,
                        help=('%% distance from close price at order creation'
                              ' time for the limit price in StopLimit orders'))

    return parser.parse_args()

if __name__ == '__main__':
    runstrat()
```
