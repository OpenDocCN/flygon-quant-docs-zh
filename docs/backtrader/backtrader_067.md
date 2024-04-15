# 交易

> 原文：[`www.backtrader.com/docu/trade/`](https://www.backtrader.com/docu/trade/)

交易的定义：

+   当仪器中的持仓从 0 变为大小 X 时，交易就开始了，大小可以是正的/负的，分别表示多头/空头仓位）

+   当仓位从 X 变为 0 时，交易关闭。

以下两个动作：

+   正至负

+   负至正

实际上被视为：

1.  一个交易已经关闭（仓位从 X 变为 0）

1.  一个新交易已经打开（仓位从 0 变为 Y）

交易仅用于信息提供，没有用户可调用的方法。

## 参考：交易

#### 类 backtrader.trade.Trade(data=None, tradeid=0, historyon=False, size=0, price=0.0, value=0.0, commission=0.0)

跟踪交易的生命周期：大小、价格、佣金（和价值？）

一个交易从 0 开始，可以增加和减少，并且如果回到 0 可以被视为关闭。

交易可以是多头（正大小）或空头（负大小）

一个交易不打算被反转（逻辑上不支持）

成员属性：

+   `ref`: 唯一的交易标识符

+   `status` (`int`): Created、Open、Closed 中的一个

+   `tradeid`: 在创建订单时传递给订单的交易 id 分组，默认为 0

+   `size` (`int`): 交易的当前大小

+   `price` (`float`): 交易的当前价格

+   `value` (`float`): 交易的当前价值

+   `commission` (`float`): 当前累积佣金

+   `pnl` (`float`): 交易的当前利润和损失（毛利润）

+   `pnlcomm` (`float`): 交易的当前利润和损失减去佣金（净利润）

+   `isclosed` (`bool`): 记录最后一个更新是否关闭了交易（将大小设置为 null）

+   `isopen` (`bool`): 记录是否有任何更新打开了交易

+   `justopened` (`bool`): 如果交易刚刚开启

+   `baropen` (`int`): 开启此交易的柱

+   `dtopen` (`float`): 交易开启的浮点编码日期时间

    +   使用方法 `open_datetime` 获取 Python datetime.datetime 或使用平台提供的 `num2date` 方法

+   `barclose` (`int`): 关闭此交易的柱

+   `dtclose` (`float`): 交易关闭的浮点编码日期时间

    +   使用方法 `close_datetime` 获取 Python datetime.datetime 或使用平台提供的 `num2date` 方法

+   `barlen` (`int`): 此交易持续的周期数

+   `historyon` (`bool`): 是否记录历史记录

+   `history` (`list`): 每个“update”事件更新的列表，包含更新后的状态和更新中使用的参数

    历史记录中的第一个条目是开仓事件，最后一个条目是关闭事件
