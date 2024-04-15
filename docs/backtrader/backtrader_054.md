# 订单。

> 译文：[`www.backtrader.com/docu/order/`](https://www.backtrader.com/docu/order/)

`Cerebro` 是 `backtrader` 中的关键控制系统，而 `Strategy`（一个子类）是最终用户的关键控制点。后者需要一种方法将系统的其他部分链接起来，这就是**订单**发挥关键作用的地方。

*订单*将 `Strategy` 中的逻辑决策转化为适合 `Broker` 执行操作的消息。这是通过以下方式完成的：

+   *创建*。

    通过 Strategy 的方法：`buy\``,`sell` 和 `close`（Strategy），它们返回一个`order`实例作为参考。

+   *取消*。

    通过 Strategy 的方法：`cancel`（Strategy），它接受一个订单实例进行操作。

*订单*也作为一种向用户传达信息的方法，通知经纪人运行情况。

+   *通知*。

    对 Strategy 方法：`notify_order`（Strategy），报告一个`order`实例。

## 订单创建。

在调用 `buy`、`sell` 和 `close` 时，以下参数适用于创建：

+   `data`（默认值：`None`）。

    要创建订单的数据。如果为 `None`，则系统中的第一个数据，`self.datas[0] 或 self.data0`（又名 `self.data`），将被使用。

+   `size`（默认值：`None`）。

    要使用的单位数据的大小（正数）用于订单。

    如果为 `None`，则将使用通过 `getsizer` 检索到的 `sizer` 实例来确定大小。

+   `price`（默认值：`None`）。

    要使用的价格（实时经纪人可能对实际格式施加限制，如果不符合最小跳动要求）。

    `None` 对于 `Market` 和 `Close` 订单是有效的（市场确定价格）。

    对于 `Limit`、`Stop` 和 `StopLimit` 订单，此值确定触发点（在 `Limit` 的情况下，触发显然是订单应该匹配的价格）。

+   `plimit`（默认值：`None`）。

    仅适用于 `StopLimit` 订单。这是在*Stop*触发后设置隐式*限价*订单的价格（其中使用了`price`）。

+   `exectype`（默认值：`None`）。

    可能的值：

    +   `Order.Market` 或 `None`。市价订单将以下一个可用价格执行。在回测中，它将是下一个柱的开盘价。

    +   `Order.Limit`。只能以给定的 `price` 或更好的价格执行的订单。

    +   `Order.Stop`。在 `price` 触发并像 `Order.Market` 订单一样执行的订单。

    +   `Order.StopLimit`。在 `price` 触发并作为隐式*限价*订单执行的订单，其价格由 `pricelimit` 给出。

+   `valid`（默认值：`None`）。

    可能的值：

    +   `None`：这将生成一个不会过期的订单（也称为*有效直至取消*），并保持在市场上直到匹配或取消。实际上，经纪人往往会强加时间限制，但通常时间跨度很长，可以视为不会过期。

    +   `datetime.datetime` 或 `datetime.date` 实例：日期将用于生成一个在给定日期时间之前有效的订单（也称为*有效截止日期*）。

    +   `Order.DAY`或`0`或`timedelta()`: 将生成一个直到*Session 结束*（又称*day*订单）有效的订单

    +   `numeric value`: 假定这是与`matplotlib`编码中的日期时间相对应的值（`backtrader`使用的编码），并将用于生成直到那个时间点有效的订单（*good till date*）。

+   `tradeid`（默认：`0`）

    这是`backtrader`用来跟踪同一资产上重叠交易的内部值。当订单状态发生变化时，该`tradeid`会发送回*策略*。

+   `**kwargs`：其他经纪人实现可能支持额外的参数。`backtrader`将*kwargs*传递给创建的订单对象

    例如：如果`backtrader`直接支持的 4 种订单执行类型不够用，例如*Interactive Brokers*的情况下，可以将以下内容作为*kwargs*传递：

    ```py
    `orderType='LIT', lmtPrice=10.0, auxPrice=9.8` 
    ```

    这将覆盖由`backtrader`创建的设置，并生成具有*touched*价格为 9.8 和*limit*价格为 10.0 的`LIMIT IF TOUCHED`订单。

注意

`close`方法将检查当前仓位，并相应地使用`buy`或`sell`来有效地**close**该仓位。 `size`也将自动计算，除非参数是来自用户的输入，在这种情况下可以实现部分*close*或*reversal*。

## 订单通知

要接收通知，用户子类的`Strategy`必须重写`notify_order`方法（默认行为是什么也不做）。以下内容适用于这些通知：

+   在调用策略的`next`方法之前发出

+   可能（并且将）在相同的*next*周期内多次发生对于相同或不同状态的相同*order*。

    *订单*可能在`next`再次被调用之前提交给*经纪人*并*接受*并且其执行*完成*。

    在这种情况下，至少会有 3 个通知，其`status`值如下：

    +   `Order.Submitted`因为订单已发送给*经纪人*

    +   `Order.Accepted`因为订单已被*经纪人*接受并等待可能的执行。

    +   `Order.Completed`因为在示例中它被迅速匹配和完全填充（这在`Market`订单通常情况下可能是发生的）。

即使在相同的状态下，通知也可能多次发生在`Order.Partial`的情况下。这个状态不会在*backtesting*经纪人（不考虑匹配时的成交量）中看到，但是肯定会被真实的经纪人设置。

真实的经纪人可能在更新仓位之前发出一个或多个执行，这组执行将组成`Order.Partial`通知。

实际执行数据在属性中：`order.executed`，它是`OrderData`类型的对象（参见下文的引用），具有常见的字段如`size`和`price`

创建时的值存储在`order.created`中，在`order`的整个生命周期内保持不变。

## 订单状态值

以下内容已定义：

+   `Order.Created`：在创建`Order`实例时设置。 除非`订单`实例是手动创建而不是通过`买入`，`卖出`和`关闭`，否则永远不会被最终用户看到。

+   `Order.Submitted`：在`订单`实例已传输到`经纪人`时设置。 这只意味着它已经*发送*。 在*回测*模式下，这将是一个立即的动作，但是在实际的*时间*中，这可能需要一段时间才能与真正的经纪人完成，这可能会接收订单，只有在转发到交易所时才会首次通知。

+   `Order.Accepted`：`broker`已接受订单并且已经在系统中（或已经在交易所中）等待根据设置的参数（如执行类型，大小，价格和有效性）执行

+   `Order.Partial`：订单已部分执行。 `order.executed`包含当前填充的`size`和平均价格。

    `order.executed.exbits`包含一个详细的`ExecutionBits`列表，详细说明了部分填充情况

+   `Order.Complete`：订单已完全填充的平均价格。

+   `Order.Rejected`：经纪人已拒绝订单。 一个参数（例如确定其生存期的`valid`）可能不被经纪人接受，订单将无法被接受。

    原因将通过`strategy`的`notify_store`方法通知。 尽管这可能看起来很奇怪，但原因是真实生活的经纪人将通过事件通知这一点，该事件可能与订单直接相关也可能与订单无关。 但是来自经纪人的通知仍然可以在`notify_store`中看到。

    此状态将不会在*回测*经纪人中看到

+   `Order.Margin`：订单执行将意味着保证金调用，并且先前接受的订单已从系统中移除

+   `Order.Cancelled`（或`Order.Canceled`）：用户请求取消的确认

    必须考虑到通过策略的`cancel`方法取消订单的请求不是取消的保证。 订单可能已经被执行，但是经纪人可能尚未通知，和/或通知可能尚未传递给策略。

+   `Order.Expired`：先前接受的*订单*其具有时间有效性已过期并已从系统中移除

## 参考：订单和相关类

这些对象是`backtrader`生态系统中的通用类。 在与其他经纪人操作时，它们可以被扩展和/或包含额外的嵌入信息。 请参阅适当经纪人的参考资料。

#### 类`backtrader.order.Order()`

持有创建/执行数据和订单类型的类。

订单可能具有以下状态：

+   已提交：已发送到经纪人并等待确认

+   已接受：经纪人接受的

+   部分：部分执行

+   已完成：已完全执行

+   已取消：用户取消的已取消

+   已过期：已过期

+   保证金：没有足够的现金执行订单。

+   被拒绝：被经纪人拒绝

    这可能发生在订单提交时（因此订单不会达到已接受的状态）或在执行之前，每个新的条价格因为现金已经被其他来源提取（类似期货的工具可能会减少现金或订单已经被执行）

成员属性：

+   ref：唯一的订单标识符

+   created：持有创建数据的 OrderData

+   executed：持有执行数据的订单数据

+   info：通过方法 `addinfo()` 传递的自定义信息。它以 OrderedDict 的形式保留，已经被子类化，因此还可以使用‘.’符号指定键

用户方法：

+   isbuy()：返回一个布尔值，指示订单是否购买

+   issell()：返回一个布尔值，指示订单是否卖出

+   alive()：如果订单处于部分或已接受状态，则返回布尔值

#### 类 backtrader.order.OrderData(dt=None, size=0, price=0.0, pricelimit=0.0, remsize=0, pclose=0.0, trailamount=0.0, trailpercent=0.0)

包含创建和执行的实际订单数据。

在创建时进行的请求，在执行时进行的实际结果。

成员属性：

+   exbits：此 OrderData 的 OrderExecutionBits 的可迭代对象

+   dt：日期时间（浮点数）创建/执行时间

+   size：请求/执行大小

+   price：执行价格注意：如果未给出价格且未给出价格限制，则将在订单创建时使用当前收盘价作为参考

+   pricelimit：保存 StopLimit 的价格限制（先触发）

+   trailamount：追踪止损的绝对价格距离

+   trailpercent：追踪止损的百分比价格距离

+   value：整个比特大小的市场价值

+   comm：整个比特执行的佣金

+   pnl：由此比特生成的 pnl（如果有东西被关闭）

+   margin：订单所产生的保证金（如果有）

+   psize：当前开放位置大小

+   pprice：当前开放位置价格

#### 类 backtrader.order.OrderExecutionBit(dt=None, size=0, price=0.0, closed=0, closedvalue=0.0, closedcomm=0.0, opened=0, openedvalue=0.0, openedcomm=0.0, pnl=0.0, psize=0, pprice=0.0)

用于保存订单执行信息。 “比特” 不确定订单是否已完全/部分执行，它只是保存信息。

成员属性：

+   dt：日期时间（浮点数）执行时间

+   size：执行了多少

+   price：执行价格

+   closed：执行关闭了现有的位置多少

+   opened：执行打开了多少新的位置

+   openedvalue：打开部分的市值

+   closedvalue：关闭部分的市值

+   closedcomm：关闭部分的佣金

+   openedcomm：打开部分的佣金

+   value：整个比特大小的市场价值

+   comm：整个比特执行的佣金

+   pnl：由此比特生成的 pnl（如果有东西被关闭）

+   psize：当前开放位置大小

+   pprice：当前开放位置价格
