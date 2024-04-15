# 经纪人

> 原文：[`www.backtrader.com/docu/broker/`](https://www.backtrader.com/docu/broker/)

## 参考

### 类`backtrader.brokers.BackBroker()`

经纪人模拟器

该模拟支持不同的订单类型，检查提交订单的现金需求与当前现金的情况，跟踪每次`cerebro`迭代的现金和价值，并在不同的数据上保持当前位置。

*cash*根据仪器进行每次迭代调整，如`期货`

```py
which a price change implies in real brokers the addition/substracion of
cash.
```

支持的订单类型：

+   `Market`：在下一个柱子的第 1^(st)个 tick（即`open`价格）执行

+   `Close`：用于一日内，在该订单以会话最后一个柱的收盘价执行

+   `Limit`：在会话期间看到给定的限价时执行

+   `Stop`：如果看到给定的停止价格，则执行`Market`订单

+   `StopLimit`：如果看到给定的停止价格，则启动`Limit`订单

因为经纪人是由`Cerebro`实例化的，而且应该（大多数情况下）没有理由替换经纪人，所以实例的参数不受用户控制。要更改此参数，有两种选择：

1.  手动创建此类的实例，并使用`cerebro.broker = instance`将实例设置为`run`执行的经纪人

1.  使用`set_xxx`通过`cerebro.broker.set_xxx`来设置值，其中`\`xxx`代表要设置的参数的名称

注意

`cerebro.broker`是`Cerebro`的`getbroker`和`setbroker`方法支持的*property*

参数：

+   `cash`（默认值：`10000`）：起始现金

+   `commission`（默认值：`CommInfoBase(percabs=True)`）适用于所有资产的基础佣金方案

+   `checksubmit`（默认值：`True`）：在将订单接受到系统之前检查保证金/现金

+   `eosbar`（默认值：`False`）：对于一日内柱，如果某个柱具有与会话结束相同的`time`，则将其视为会话结束。这通常不是情况，因为一些柱（最终拍卖）会在会话结束后几分钟内由许多交易所为许多产品产生

+   `eosbar`（默认值：`False`）：对于一日内柱，如果某个柱具有与会话结束相同的`time`，则将其视为会话结束。这通常不是情况，因为一些柱（最终拍卖）会在会话结束后几分钟内由许多交易所为许多产品产生

+   `filler`（默认值：`None`）

    一个带有签名的可调用函数：`callable(order, price, ago)`

    +   `order`：显然是执行的订单。这提供了对*数据*（以及*ohlc*和*volume*值）、*执行类型*、剩余大小（`order.executed.remsize`）和其他内容的访问。

        请检查`Order`文档和参考，以了解`Order`实例中可用的内容

    +   `price`：订单将在`ago`柱中执行的价格

    +   `ago`：用于与`order.data`一起使用以提取*ohlc*和*volume*价格的索引。在大多数情况下，这将是`0`，但对于`Close`订单的一个特殊情况，这将是`-1`。

        为了获得柱形体积（例如）：`volume = order.data.voluume[ago]`

    可调用函数必须返回*执行大小*（大于等于 0 的值）

    可调用函数当然可以是一个与前述签名匹配的对象`__call__`

    默认值为`None`时，订单将在一次性完全执行

+   `slip_perc`（默认值：`0.0`）应该使用的百分比绝对值（和正数）使买入/卖出订单的价格上涨/下跌

    注意：

    +   `0.01`是`1%`

    +   `0.001`是`0.1%`

+   `slip_fixed`（默认值：`0.0`）应使用的以单位（和正数）表示的百分比来使价格上涨/下跌以进行买入/卖出订单滑点

    注意：如果`slip_perc`不为零，则它优先于此。

+   `slip_open`（默认值：`False`）是否为订单执行滑点价格，其将特定使用*下一个*条的*开盘*价格。一个例子是使用下一个可用的刻度执行的`Market`订单，即：该条的开盘价。

    这也适用于一些其他执行，因为逻辑试图检测*开盘*价格是否会与移动到新条时请求的价格/执行类型匹配。

+   `slip_match`（默认值：`True`）

    如果设置为`True`，则经纪人将通过将滑点限制在`高/低`价格上来提供匹配，以防超出。

    如果为`False`，则经纪人不会以当前价格匹配订单，并将尝试在下一次迭代期间执行

+   `slip_limit`（默认值：`True`）

    `Limit`订单，给定所请求的确切匹配价格，即使`slip_match`为`False`，也将匹配。

    此选项控制该行为。

    如果为`True`，则`Limit`订单将通过将价格限制在`限价`/`高/低`价格上来匹配

    如果`False`且滑点超过上限，则不会匹配

+   `slip_out`（默认值：`False`）

    即使价格跌出`高`-`低`范围，也要提供*滑点*。

+   `coc`（默认值：`False`）

    *Cheat-On-Close* 将其设置为`True`并使用`set_coc`启用

    ```py
    `matching a `Market` order to the closing price of the bar in which
    the order was issued. This is actually *cheating*, because the bar
    is *closed* and any order should first be matched against the prices
    in the next bar` 
    ```

+   `coo`（默认值：`False`）

    *Cheat-On-Open* 将其设置为`True`并使用`set_coo`启用

    ```py
    `matching a `Market` order to the opening price, by for example
    using a timer with `cheat` set to `True`, because such a timer
    gets executed before the broker has evaluated` 
    ```

+   `int2pnl`（默认值：`True`）

    将生成的利息（如果有）分配给减少持仓的操作的损益。可能存在这种情况：不同的策略正在竞争，利息将以不确定的方式分配给其中任何一个。

+   `shortcash`（默认值：`True`）

    如果为 True，则在对类似股票的资产进行空头操作时会增加现金，并且计算出的资产值将为负。

    如果为`False`，则现金将被扣除为操作成本，并且计算出的值将为正，以保持相同数量

+   `fundstartval`（默认值：`100.0`）

    此参数控制以类似基金的方式测量绩效的起始值，即：可以添加和扣除现金以增加股份数量。绩效不是使用投资组合的净资产值来衡量的，而是使用基金的价值来衡量。

+   `fundmode`（默认值：`False`）

    如果设置为 `True`，分析器如 `TimeReturn` 可以根据基金价值而不是总净资产自动计算回报率

### `set_cash(cash)`

设置 `cash` 参数（别名：`setcash`）

### `get_cash()`

返回当前现金（别名：`getcash`）

### `get_value(datas=None, mkt=False, lever=False)`

返回给定数据的投资组合价值（如果数据为 `None`，则将返回总投资组合价值）（别名：`getvalue`）

### `set_eosbar(eosbar)`

设置 `eosbar` 参数（别名：`seteosbar`）

### `set_checksubmit(checksubmit)`

设置 `checksubmit` 参数

### `set_filler(filler)`

为成交量填充执行设置一个成交量填充器

### `set_coc(coc)`

配置“在订单栏上购买收盘价”的“Cheat-On-Close”方法

### `set_coo(coo)`

配置“在订单栏上购买开盘价”的“Cheat-On-Open”方法

### `set_int2pnl(int2pnl)`

配置利息分配给损益

### `set_fundstartval(fundstartval)` 

设置基金样式表现追踪器的起始值

### `set_slippage_perc(perc, slip_open=True, slip_limit=True, slip_match=True, slip_out=False)`

配置滑点以百分比为基础

### `set_slippage_fixed(fixed, slip_open=True, slip_limit=True, slip_match=True, slip_out=False)`

配置滑点以基于固定点进行

### `get_orders_open(safe=False)`

返回仍然打开的订单的可迭代对象（未执行或部分执行）

返回的订单不得被更改。

如果需要订单操作，请将参数 `safe` 设置为 `True`

### `getcommissioninfo(data)`

检索与给定 `data` 相关联的 `CommissionInfo` 方案

### `setcommission(commission=0.0, margin=None, mult=1.0, commtype=None, percabs=True, stocklike=False, interest=0.0, interest_long=False, leverage=1.0, automargin=False, name=None)`

此方法使用参数为经纪人管理的资产设置 `` CommissionInfo`` 对象。请查阅 `CommInfoBase` 的参考资料

如果名称为 `None`，则对于找不到其他 `CommissionInfo` 方案的资产，这将是默认值

### `addcommissioninfo(comminfo, name=None)`

如果 `name` 为 `None`，则添加一个 `CommissionInfo` 对象，如果 `name` 为 `None`，则为所有资产设置默认值

### `getposition(data)`

返回给定 `data` 的当前持仓状态（`Position` 实例）

### `get_fundshares()`

返回基金模式中当前股份数量

### `get_fundvalue()`

返回基金样式股票价值

### `add_cash(cash)`

向系统添加/移除现金（使用负值来移除）
