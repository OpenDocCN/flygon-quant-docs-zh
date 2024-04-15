# 定时器

> 原文：[`www.backtrader.com/blog/posts/2017-05-03-timers/timers/`](https://www.backtrader.com/blog/posts/2017-05-03-timers/timers/)

发布 `1.9.44.116` 在 *backtrader* 可用工具中添加了 *timers* 功能。此功能允许在指定时间点收到对 `notify_timer` 的回调（在 `Cerebro` 和 `Strategy` 中可用），并且有细粒度的最终用户控制。

注意

在 `1.9.46.116` 中进行了一些更正

## 选项

+   基于绝对时间输入或与会话开始/结束时间相关的定时器

+   时区规范为时间规范，无论是直接还是通过 *pytz* 兼容对象，还是通过数据源会话结束时间

+   与指定时间相关的起始偏移量

+   重复间隔

+   工作日筛选器（带有延续选项）

+   月日筛选器（带有延续选项）

+   自定义回调筛选器

## 使用模式

在 `Cerebro` 和 `Strategy` 子类中，定时器回调将在以下方法中收到。

```py
`def notify_timer(self, timer, when, *args, **kwargs):
    '''Receives a timer notification where ``timer`` is the timer which was
    returned by ``add_timer``, and ``when`` is the calling time. ``args``
    and ``kwargs`` are any additional arguments passed to ``add_timer``

    The actual ``when`` time can be later, but the system may have not be
    able to call the timer before. This value is the timer value and not the
    system time.
    '''` 
```

### 添加定时器 - 通过 Strategy

使用该方法完成

```py
`def add_timer(self, when,
              offset=datetime.timedelta(), repeat=datetime.timedelta(),
              weekdays=[], weekcarry=False,
              monthdays=[], monthcarry=True,
              allow=None,
              tzdata=None, cheat=False,
              *args, **kwargs):
    '''` 
```

返回创建的 `Timer` 实例。

有关参数解释，请参见下文。

### 添加定时器 - 通过 Cerebro

使用相同的方法完成，只需添加参数 `strats`。如果设置为 `True`，则定时器不仅将通知给 *cerebro*，还将通知给系统中运行的所有策略。

```py
`def add_timer(self, when,
              offset=datetime.timedelta(), repeat=datetime.timedelta(),
              weekdays=[], weekcarry=False,
              monthdays=[], monthcarry=True,
              allow=None,
              tzdata=None, cheat=False, strats=False,
              *args, **kwargs):
    '''` 
```

返回创建的 `Timer` 实例。

## 定时器何时被调用

### 如果 `cheat=False`

这是默认设置。在这种情况下，定时器将被调用：

+   在数据源加载了当前柱形图的新值之后

+   在经纪人评估订单并重新计算组合价值之后

+   在指标重新计算之前（因为这是由策略触发的）

+   在任何策略的 `next` 方法被调用之前

### 如果 `cheat=True`

在这种情况下，将调用定时器：

+   在数据源加载了当前柱形图的新值之后

+   **在** 经纪人评估订单并重新计算组合价值之前

+   因此在指标被重新计算之前和任何策略的 `next` 方法被调用之前

这允许例如以下场景与每日柱形图：

+   在经纪人评估新柱形图之前，定时器被调用

+   指标具有前一天收盘价的值，可以用于生成入场/出场信号（或在上次评估 `next` 时可能已设置了标志）

+   因为新价格可用，所以可以使用开盘价计算赌注。这假设例如通过观察开盘竞价可以得到关于 `open` 的良好指示。

## 使用每日柱形图运行

示例 `scheduled.py` 默认使用 *backtrader* 发行版中提供的标准每日柱形图运行。策略的参数

```py
`class St(bt.Strategy):
    params = dict(
        when=bt.timer.SESSION_START,
        timer=True,
        cheat=False,
        offset=datetime.timedelta(),
        repeat=datetime.timedelta(),
        weekdays=[],
    )` 
```

数据具有以下会话时间：

+   开始：09:00

+   结束：17:30

仅使用时间运行

```py
`$ ./scheduled.py --strat when='datetime.time(15,30)'

strategy notify_timer with tid 0, when 2005-01-03 15:30:00 cheat False
1, 2005-01-03 17:30:00, Week 1, Day 1, O 2952.29, H 2989.61, L 2946.8, C 2970.02
strategy notify_timer with tid 0, when 2005-01-04 15:30:00 cheat False
2, 2005-01-04 17:30:00, Week 1, Day 2, O 2969.78, H 2979.88, L 2961.14, C 2971.12
strategy notify_timer with tid 0, when 2005-01-05 15:30:00 cheat False
3, 2005-01-05 17:30:00, Week 1, Day 3, O 2969.0, H 2969.0, L 2942.69, C 2947.19
strategy notify_timer with tid 0, when 2005-01-06 15:30:00 cheat False
...` 
```

如指定的，定时器在 `15:30` 滴答。没有意外。让我们添加一个偏移量为 30 分钟。

```py
`$ ./scheduled.py --strat when='datetime.time(15,30)',offset='datetime.timedelta(minutes=30)'

strategy notify_timer with tid 0, when 2005-01-03 16:00:00 cheat False
1, 2005-01-03 17:30:00, Week 1, Day 1, O 2952.29, H 2989.61, L 2946.8, C 2970.02
strategy notify_timer with tid 0, when 2005-01-04 16:00:00 cheat False
2, 2005-01-04 17:30:00, Week 1, Day 2, O 2969.78, H 2979.88, L 2961.14, C 2971.12
strategy notify_timer with tid 0, when 2005-01-05 16:00:00 cheat False
...` 
```

时间从`15:30`更改为`16:00`。没有什么惊喜。让我们做同样的事情，但是引用会话开始。

```py
`$ ./scheduled.py --strat when='bt.timer.SESSION_START',offset='datetime.timedelta(minutes=30)'

strategy notify_timer with tid 0, when 2005-01-03 09:30:00 cheat False
1, 2005-01-03 17:30:00, Week 1, Day 1, O 2952.29, H 2989.61, L 2946.8, C 2970.02
strategy notify_timer with tid 0, when 2005-01-04 09:30:00 cheat False
2, 2005-01-04 17:30:00, Week 1, Day 2, O 2969.78, H 2979.88, L 2961.14, C 2971.12
...` 
```

Et voilá！回调调用的时间是`09:30`。并且会话开始，见上文，是`09:00`。这使得可以简单地说希望在会话开始后*30 分钟*执行某个动作。

让我们添加一个重复：

```py
`$ ./scheduled.py --strat when='bt.timer.SESSION_START',offset='datetime.timedelta(minutes=30)',repeat='datetime.timedelta(minutes=30)'

strategy notify_timer with tid 0, when 2005-01-03 09:30:00 cheat False
1, 2005-01-03 17:30:00, Week 1, Day 1, O 2952.29, H 2989.61, L 2946.8, C 2970.02
strategy notify_timer with tid 0, when 2005-01-04 09:30:00 cheat False
2, 2005-01-04 17:30:00, Week 1, Day 2, O 2969.78, H 2979.88, L 2961.14, C 2971.12
strategy notify_timer with tid 0, when 2005-01-05 09:30:00 cheat False
...` 
```

**没有重复**。原因是价格的分辨率是每日的。计时器像前一个示例一样在`09:30`首次调用。但是当系统获取下一批价格时，它们发生在下一天。显然，计时器只能被调用一次。需要更低的分辨率。

但在转向更低的分辨率之前，让我们通过在会话结束之前调用计时器来作弊。

```py
`$ ./scheduled.py --strat when='bt.timer.SESSION_START',cheat=True

strategy notify_timer with tid 1, when 2005-01-03 09:00:00 cheat True
-- 2005-01-03 Create buy order
strategy notify_timer with tid 0, when 2005-01-03 09:00:00 cheat False
1, 2005-01-03 17:30:00, Week 1, Day 1, O 2952.29, H 2989.61, L 2946.8, C 2970.02
strategy notify_timer with tid 1, when 2005-01-04 09:00:00 cheat True
strategy notify_timer with tid 0, when 2005-01-04 09:00:00 cheat False
-- 2005-01-04 Buy Exec @ 2969.78
2, 2005-01-04 17:30:00, Week 1, Day 2, O 2969.78, H 2979.88, L 2961.14, C 2971.12
strategy notify_timer with tid 1, when 2005-01-05 09:00:00 cheat True
strategy notify_timer with tid 0, when 2005-01-05 09:00:00 cheat False
...` 
```

策略添加了一个带有`cheat=True`的第 2 个计时器。这是添加的第 2 个，因此将接收到第 2 个`tid`（*计时器 id*），即`1`（请参见上述示例，分配的`tid`为`0`）

并且`1`在`0`之前被调用，因为该计时器正在*作弊*，并且在系统中的许多事件发生之前被调用（请参见上文的解释）

由于价格的*每日*分辨率，这并没有太大的区别，除了：

+   该策略还在开盘前发出订单……并且在第二天与开盘价匹配。

    这，即使在开盘前作弊，仍然是正常的行为，因为经纪人也没有激活*cheating-on-open*。

经纪人的`coo=True`相同。

```py
`$ ./scheduled.py --strat when='bt.timer.SESSION_START',cheat=True --broker coo=True

strategy notify_timer with tid 1, when 2005-01-03 09:00:00 cheat True
-- 2005-01-03 Create buy order
strategy notify_timer with tid 0, when 2005-01-03 09:00:00 cheat False
-- 2005-01-03 Buy Exec @ 2952.29
1, 2005-01-03 17:30:00, Week 1, Day 1, O 2952.29, H 2989.61, L 2946.8, C 2970.02
strategy notify_timer with tid 1, when 2005-01-04 09:00:00 cheat True
strategy notify_timer with tid 0, when 2005-01-04 09:00:00 cheat False
2, 2005-01-04 17:30:00, Week 1, Day 2, O 2969.78, H 2979.88, L 2961.14, C 2971.12
strategy notify_timer with tid 1, when 2005-01-05 09:00:00 cheat True
strategy notify_timer with tid 0, when 2005-01-05 09:00:00 cheat False
...` 
```

有些事情发生了变化。

+   订单在作弊计时器中在`2005-01-03`发出

+   订单在`2005-01-03`以开盘价执行

    实际上就像在市场真正开盘前几秒钟就已经根据开盘竞价行动了一样。

## 使用 5 分钟的条形图运行

示例`scheduled-min.py`默认使用*backtrader*发行的标准 5 分钟条形图运行。策略的参数被扩展以包括`monthdays`和*carry*选项

```py
`class St(bt.Strategy):
    params = dict(
        when=bt.timer.SESSION_START,
        timer=True,
        cheat=False,
        offset=datetime.timedelta(),
        repeat=datetime.timedelta(),
        weekdays=[],
        weekcarry=False,
        monthdays=[],
        monthcarry=True,
    )` 
```

数据具有相同的交易时间：

+   开始时间：09:00

+   结束时间：17:30

让我们进行一些实验。首先是一个单一的计时器。

```py
`$ ./scheduled-min.py --strat when='datetime.time(15, 30)'

1, 2006-01-02 09:05:00, Week 1, Day 1, O 3578.73, H 3587.88, L 3578.73, C 3582.99
2, 2006-01-02 09:10:00, Week 1, Day 1, O 3583.01, H 3588.4, L 3583.01, C 3588.03
...
77, 2006-01-02 15:25:00, Week 1, Day 1, O 3599.07, H 3599.68, L 3598.47, C 3599.68
strategy notify_timer with tid 0, when 2006-01-02 15:30:00 cheat False
78, 2006-01-02 15:30:00, Week 1, Day 1, O 3599.64, H 3599.73, L 3599.0, C 3599.67
...
179, 2006-01-03 15:25:00, Week 1, Day 2, O 3634.72, H 3635.0, L 3634.06, C 3634.87
strategy notify_timer with tid 0, when 2006-01-03 15:30:00 cheat False
180, 2006-01-03 15:30:00, Week 1, Day 2, O 3634.81, H 3634.89, L 3634.04, C 3634.23
...` 
```

计时器按要求在`15:30`开始工作。日志显示了它在前两天如何工作。

将`15 分钟`的`repeat`添加到混合中

```py
`$ ./scheduled-min.py --strat when='datetime.time(15, 30)',repeat='datetime.timedelta(minutes=15)'

...
74, 2006-01-02 15:10:00, Week 1, Day 1, O 3596.12, H 3596.63, L 3595.92, C 3596.63
75, 2006-01-02 15:15:00, Week 1, Day 1, O 3596.36, H 3596.65, L 3596.19, C 3596.65
76, 2006-01-02 15:20:00, Week 1, Day 1, O 3596.53, H 3599.13, L 3596.12, C 3598.9
77, 2006-01-02 15:25:00, Week 1, Day 1, O 3599.07, H 3599.68, L 3598.47, C 3599.68
strategy notify_timer with tid 0, when 2006-01-02 15:30:00 cheat False
78, 2006-01-02 15:30:00, Week 1, Day 1, O 3599.64, H 3599.73, L 3599.0, C 3599.67
79, 2006-01-02 15:35:00, Week 1, Day 1, O 3599.61, H 3600.29, L 3599.52, C 3599.92
80, 2006-01-02 15:40:00, Week 1, Day 1, O 3599.96, H 3602.06, L 3599.76, C 3602.05
strategy notify_timer with tid 0, when 2006-01-02 15:45:00 cheat False
81, 2006-01-02 15:45:00, Week 1, Day 1, O 3601.97, H 3602.07, L 3601.45, C 3601.83
82, 2006-01-02 15:50:00, Week 1, Day 1, O 3601.74, H 3602.8, L 3601.63, C 3602.8
83, 2006-01-02 15:55:00, Week 1, Day 1, O 3602.53, H 3602.74, L 3602.33, C 3602.61
strategy notify_timer with tid 0, when 2006-01-02 16:00:00 cheat False
84, 2006-01-02 16:00:00, Week 1, Day 1, O 3602.58, H 3602.75, L 3601.81, C 3602.14
85, 2006-01-02 16:05:00, Week 1, Day 1, O 3602.16, H 3602.16, L 3600.86, C 3600.96
86, 2006-01-02 16:10:00, Week 1, Day 1, O 3601.2, H 3601.49, L 3600.94, C 3601.27
...
strategy notify_timer with tid 0, when 2006-01-02 17:15:00 cheat False
99, 2006-01-02 17:15:00, Week 1, Day 1, O 3603.96, H 3603.96, L 3602.89, C 3603.79
100, 2006-01-02 17:20:00, Week 1, Day 1, O 3603.94, H 3605.95, L 3603.87, C 3603.91
101, 2006-01-02 17:25:00, Week 1, Day 1, O 3604.0, H 3604.76, L 3603.85, C 3604.64
strategy notify_timer with tid 0, when 2006-01-02 17:30:00 cheat False
102, 2006-01-02 17:30:00, Week 1, Day 1, O 3604.06, H 3604.41, L 3603.95, C 3604.33
103, 2006-01-03 09:05:00, Week 1, Day 2, O 3604.08, H 3609.6, L 3604.08, C 3609.6
104, 2006-01-03 09:10:00, Week 1, Day 2, O 3610.34, H 3617.31, L 3610.34, C 3617.31
105, 2006-01-03 09:15:00, Week 1, Day 2, O 3617.61, H 3617.87, L 3616.03, C 3617.51
106, 2006-01-03 09:20:00, Week 1, Day 2, O 3617.24, H 3618.86, L 3616.09, C 3618.42
...
179, 2006-01-03 15:25:00, Week 1, Day 2, O 3634.72, H 3635.0, L 3634.06, C 3634.87
strategy notify_timer with tid 0, when 2006-01-03 15:30:00 cheat False
180, 2006-01-03 15:30:00, Week 1, Day 2, O 3634.81, H 3634.89, L 3634.04, C 3634.23
...` 
```

预期的是第 1 次调用在`15:30`触发，然后每隔 15 分钟重复一次，直到`17:30`会话结束。新会话开始时，计时器再次重置为`15:30`。

现在在会话开始之前作弊

```py
`$ ./scheduled-min.py --strat when='bt.timer.SESSION_START',cheat=True

strategy notify_timer with tid 1, when 2006-01-02 09:00:00 cheat True
-- 2006-01-02 09:05:00 Create buy order
strategy notify_timer with tid 0, when 2006-01-02 09:00:00 cheat False
1, 2006-01-02 09:05:00, Week 1, Day 1, O 3578.73, H 3587.88, L 3578.73, C 3582.99
-- 2006-01-02 09:10:00 Buy Exec @ 3583.01
2, 2006-01-02 09:10:00, Week 1, Day 1, O 3583.01, H 3588.4, L 3583.01, C 3588.03
...` 
```

订单创建时间为`09:05:00`，执行时间为`09:10:00`，因为经纪人不处于*欺骗开盘*模式。让我们设置它……

```py
`$ ./scheduled-min.py --strat when='bt.timer.SESSION_START',cheat=True --broker coo=True

strategy notify_timer with tid 1, when 2006-01-02 09:00:00 cheat True
-- 2006-01-02 09:05:00 Create buy order
strategy notify_timer with tid 0, when 2006-01-02 09:00:00 cheat False
-- 2006-01-02 09:05:00 Buy Exec @ 3578.73
1, 2006-01-02 09:05:00, Week 1, Day 1, O 3578.73, H 3587.88, L 3578.73, C 3582.99
2, 2006-01-02 09:10:00, Week 1, Day 1, O 3583.01, H 3588.4, L 3583.01, C 3588.03
...` 
```

发行时间和执行时间为`09:05:00`，执行价格为`09:05:00`的开盘价。

## 其他情景

计时器允许通过传递一个日期列表（按照 iso 规范，其中 Mon=1 且 Sun=7 的整数）来指定它们必须执行的日期，如

+   `weekdays=[5]`，这将要求计时器仅在星期五有效

    如果星期五是非交易日，而计时器应在下一个交易日启动，则可以添加 `weekcarry=True`

类似于此，可以决定在每个月的第 15 天采取行动：

+   `monthdays=[15]`

    如果第 15 天恰好是非交易日，而计时器应在下一个交易日启动，则可以添加 `monthcarry=True`

对于诸如：*三月、六月、九月和十二月的第 3 个星期五*（期货/期权到期日）之类的事情并没有实现，但是可以通过传递来实现规则：

+   `allow=callable`，其中可调用接受 `datetime.date` 实例。请注意，这不是 `datetime.datetime` 实例，因为 *allow* 可调用仅用于确定给定日期是否适合用于计时器。

    要实现类似上面规则的内容：

    ```py
    `class FutOpExp(object):
        def __init__(self):
            self.fridays = 0
            self.curmonth = -1

        def __call__(self, d):
            _, _, isowkday = d.isocalendar()

            if d.month != self.curmonth:
                self.curmonth = d.month
                self.fridays = 0

            # Mon=1 ... Sun=7
            if isowkday == 5 and self.curmonth in [3, 6, 9, 12]:
                self.fridays += 1

                if self.friday == 3:  # 3rd Friday
                    return True  # timer allowed

            return False  # timer disallowed` 
    ```

    并且一个将 `allow=FutOpeExp()` 传递给计时器的创建

    这将允许计时器在这些月份的第 3 个星期五启动，并且可能在期货到期前平仓。

## `add_timer` 的参数

```py
`- `when`: can be

  - `datetime.time` instance (see below `tzdata`)

  - `bt.timer.SESSION_START` to reference a session start

  - `bt.timer.SESSION_END` to reference a session end` 
```

+   `offset` 必须是一个 `datetime.timedelta` 实例

    用于偏移值 `when` 的值。它与 `SESSION_START` 和 `SESSION_END` 结合使用时具有有意义的用途，例如指示计时器在会话开始后`15 分钟`被调用。

    +   `repeat` 必须是一个 `datetime.timedelta` 实例

    表示在第 1 次调用后，进一步的调用是否将在同一个会话中按计划的`repeat`间隔内安排

    一旦计时器超过会话结束，它将被重置为 `when` 的原始值

    +   `weekdays`：一个**排序的**可迭代对象，其中包含指示实际可以调用计时器的日期（ISO 代码，星期一为 1，星期日为 7）的整数

    如果未指定，计时器将在所有日期上活动

    +   `weekcarry`（默认：`False`）。如果为 `True` 并且没有看到工作日（例如：交易假日），则计时器将在下一天执行（即使是在新的一周）

    +   `monthdays`：一个**排序的**可迭代对象，其中包含指示月份中哪些日期要执行计时器的整数。例如，总是在每个月的第 *15* 天

    如果未指定，计时器将在所有日期上活动

    +   `monthcarry`（默认：`True`）。如果没有看到该天（周末，交易假日），则计时器将在下一个可用日期执行。

    +   `allow`（默认：`None`）。一个回调函数，接收一个 `datetime.date` 实例，并返回 `True`（如果日期允许计时器）或返回 `False`

    +   `tzdata` 可以是 `None`（默认值），一个 `pytz` 实例或一个 `数据源` 实例。

    `None`：`when` 被按字面值解释（即使它不是，也要处理它为 UTC）

    `pytz` 实例：`when` 将被解释为由时区实例指定的本地时间。

    `data feed`实例：`when`将被解释为在数据源实例的`tz`参数指定的本地时间中指定。

    **注意**：如果`when`是`SESSION_START`或者

    ```py
     ``SESSION_END` and `tzdata` is `None`, the 1st *data feed*
      in the system (aka `self.data0`) will be used as the reference
      to find out the session times.` 
    ```

    +   `strats`（默认为`False`）还会调用策略的`notify_timer`

    +   `cheat`（默认为`False`）如果为`True`，则计时器将在经纪人有机会评估订单之前被调用。这就打开了在会话开始之前例如根据开盘价发布订单的机会

    +   `\*args`：任何额外的参数将传递给`notify_timer`

    +   `\*\*kwargs`：任何额外的关键字参数将传递给`notify_timer`

## 示例用法`scheduled.py`

```py
`$ ./scheduled.py --help
usage: scheduled.py [-h] [--data0 DATA0] [--fromdate FROMDATE]
                    [--todate TODATE] [--cerebro kwargs] [--broker kwargs]
                    [--sizer kwargs] [--strat kwargs] [--plot [kwargs]]

Sample Skeleton

optional arguments:
  -h, --help           show this help message and exit
  --data0 DATA0        Data to read in (default:
                       ../../datas/2005-2006-day-001.txt)
  --fromdate FROMDATE  Date[time] in YYYY-MM-DD[THH:MM:SS] format (default: )
  --todate TODATE      Date[time] in YYYY-MM-DD[THH:MM:SS] format (default: )
  --cerebro kwargs     kwargs in key=value format (default: )
  --broker kwargs      kwargs in key=value format (default: )
  --sizer kwargs       kwargs in key=value format (default: )
  --strat kwargs       kwargs in key=value format (default: )
  --plot [kwargs]      kwargs in key=value format (default: )` 
```

## 示例用法`scheduled-min.py`

```py
`$ ./scheduled-min.py --help
usage: scheduled-min.py [-h] [--data0 DATA0] [--fromdate FROMDATE]
                        [--todate TODATE] [--cerebro kwargs] [--broker kwargs]
                        [--sizer kwargs] [--strat kwargs] [--plot [kwargs]]

Timer Test Intraday

optional arguments:
  -h, --help           show this help message and exit
  --data0 DATA0        Data to read in (default: ../../datas/2006-min-005.txt)
  --fromdate FROMDATE  Date[time] in YYYY-MM-DD[THH:MM:SS] format (default: )
  --todate TODATE      Date[time] in YYYY-MM-DD[THH:MM:SS] format (default: )
  --cerebro kwargs     kwargs in key=value format (default: )
  --broker kwargs      kwargs in key=value format (default: )
  --sizer kwargs       kwargs in key=value format (default: )
  --strat kwargs       kwargs in key=value format (default: )
  --plot [kwargs]      kwargs in key=value format (default: )` 
```

## 示例源代码`scheduled.py`

```py
`from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
import datetime

import backtrader as bt

class St(bt.Strategy):
    params = dict(
        when=bt.timer.SESSION_START,
        timer=True,
        cheat=False,
        offset=datetime.timedelta(),
        repeat=datetime.timedelta(),
        weekdays=[],
    )

    def __init__(self):
        bt.ind.SMA()
        if self.p.timer:
            self.add_timer(
                when=self.p.when,
                offset=self.p.offset,
                repeat=self.p.repeat,
                weekdays=self.p.weekdays,
            )
        if self.p.cheat:
            self.add_timer(
                when=self.p.when,
                offset=self.p.offset,
                repeat=self.p.repeat,
                cheat=True,
            )

        self.order = None

    def prenext(self):
        self.next()

    def next(self):
        _, isowk, isowkday = self.datetime.date().isocalendar()
        txt = '{}, {}, Week {}, Day {}, O {}, H {}, L {}, C {}'.format(
            len(self), self.datetime.datetime(),
            isowk, isowkday,
            self.data.open[0], self.data.high[0],
            self.data.low[0], self.data.close[0])

        print(txt)

    def notify_timer(self, timer, when, *args, **kwargs):
        print('strategy notify_timer with tid {}, when {} cheat {}'.
              format(timer.p.tid, when, timer.p.cheat))

        if self.order is None and timer.p.cheat:
            print('-- {} Create buy order'.format(self.data.datetime.date()))
            self.order = self.buy()

    def notify_order(self, order):
        if order.status == order.Completed:
            print('-- {} Buy Exec @ {}'.format(
                self.data.datetime.date(), order.executed.price))

def runstrat(args=None):
    args = parse_args(args)

    cerebro = bt.Cerebro()

    # Data feed kwargs
    kwargs = dict(
        timeframe=bt.TimeFrame.Days,
        compression=1,
        sessionstart=datetime.time(9, 0),
        sessionend=datetime.time(17, 30),
    )

    # Parse from/to-date
    dtfmt, tmfmt = '%Y-%m-%d', 'T%H:%M:%S'
    for a, d in ((getattr(args, x), x) for x in ['fromdate', 'todate']):
        if a:
            strpfmt = dtfmt + tmfmt * ('T' in a)
            kwargs[d] = datetime.datetime.strptime(a, strpfmt)

    # Data feed
    data0 = bt.feeds.BacktraderCSVData(dataname=args.data0, **kwargs)
    cerebro.adddata(data0)

    # Broker
    cerebro.broker = bt.brokers.BackBroker(**eval('dict(' + args.broker + ')'))

    # Sizer
    cerebro.addsizer(bt.sizers.FixedSize, **eval('dict(' + args.sizer + ')'))

    # Strategy
    cerebro.addstrategy(St, **eval('dict(' + args.strat + ')'))

    # Execute
    cerebro.run(**eval('dict(' + args.cerebro + ')'))

    if args.plot:  # Plot if requested to
        cerebro.plot(**eval('dict(' + args.plot + ')'))

def parse_args(pargs=None):
    parser = argparse.ArgumentParser(
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
        description=(
            'Sample Skeleton'
        )
    )

    parser.add_argument('--data0', default='../../datas/2005-2006-day-001.txt',
                        required=False, help='Data to read in')

    # Defaults for dates
    parser.add_argument('--fromdate', required=False, default='',
                        help='Date[time] in YYYY-MM-DD[THH:MM:SS] format')

    parser.add_argument('--todate', required=False, default='',
                        help='Date[time] in YYYY-MM-DD[THH:MM:SS] format')

    parser.add_argument('--cerebro', required=False, default='',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--broker', required=False, default='',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--sizer', required=False, default='',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--strat', required=False, default='',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--plot', required=False, default='',
                        nargs='?', const='{}',
                        metavar='kwargs', help='kwargs in key=value format')

    return parser.parse_args(pargs)

if __name__ == '__main__':
    runstrat()` 
```

## 示例源代码`scheduled-min.py`

```py
`from __future__ import (absolute_import, division, print_function,
                        unicode_literals)

import argparse
import datetime

import backtrader as bt

class St(bt.Strategy):
    params = dict(
        when=bt.timer.SESSION_START,
        timer=True,
        cheat=False,
        offset=datetime.timedelta(),
        repeat=datetime.timedelta(),
        weekdays=[],
        weekcarry=False,
        monthdays=[],
        monthcarry=True,
    )

    def __init__(self):
        bt.ind.SMA()
        if self.p.timer:
            self.add_timer(
                when=self.p.when,
                offset=self.p.offset,
                repeat=self.p.repeat,
                weekdays=self.p.weekdays,
                weekcarry=self.p.weekcarry,
                monthdays=self.p.monthdays,
                monthcarry=self.p.monthcarry,
                # tzdata=self.data0,
            )
        if self.p.cheat:
            self.add_timer(
                when=self.p.when,
                offset=self.p.offset,
                repeat=self.p.repeat,
                weekdays=self.p.weekdays,
                weekcarry=self.p.weekcarry,
                monthdays=self.p.monthdays,
                monthcarry=self.p.monthcarry,
                tzdata=self.data0,
                cheat=True,
            )

        self.order = None

    def prenext(self):
        self.next()

    def next(self):
        _, isowk, isowkday = self.datetime.date().isocalendar()
        txt = '{}, {}, Week {}, Day {}, O {}, H {}, L {}, C {}'.format(
            len(self), self.datetime.datetime(),
            isowk, isowkday,
            self.data.open[0], self.data.high[0],
            self.data.low[0], self.data.close[0])

        print(txt)

    def notify_timer(self, timer, when, *args, **kwargs):
        print('strategy notify_timer with tid {}, when {} cheat {}'.
              format(timer.p.tid, when, timer.p.cheat))

        if self.order is None and timer.params.cheat:
            print('-- {} Create buy order'.format(
                self.data.datetime.datetime()))
            self.order = self.buy()

    def notify_order(self, order):
        if order.status == order.Completed:
            print('-- {} Buy Exec @ {}'.format(
                self.data.datetime.datetime(), order.executed.price))

def runstrat(args=None):
    args = parse_args(args)
    cerebro = bt.Cerebro()

    # Data feed kwargs
    kwargs = dict(
        timeframe=bt.TimeFrame.Minutes,
        compression=5,
        sessionstart=datetime.time(9, 0),
        sessionend=datetime.time(17, 30),
    )

    # Parse from/to-date
    dtfmt, tmfmt = '%Y-%m-%d', 'T%H:%M:%S'
    for a, d in ((getattr(args, x), x) for x in ['fromdate', 'todate']):
        if a:
            strpfmt = dtfmt + tmfmt * ('T' in a)
            kwargs[d] = datetime.datetime.strptime(a, strpfmt)

    # Data feed
    data0 = bt.feeds.BacktraderCSVData(dataname=args.data0, **kwargs)
    cerebro.adddata(data0)

    # Broker
    cerebro.broker = bt.brokers.BackBroker(**eval('dict(' + args.broker + ')'))

    # Sizer
    cerebro.addsizer(bt.sizers.FixedSize, **eval('dict(' + args.sizer + ')'))

    # Strategy
    cerebro.addstrategy(St, **eval('dict(' + args.strat + ')'))

    # Execute
    cerebro.run(**eval('dict(' + args.cerebro + ')'))

    if args.plot:  # Plot if requested to
        cerebro.plot(**eval('dict(' + args.plot + ')'))

def parse_args(pargs=None):
    parser = argparse.ArgumentParser(
        formatter_class=argparse.ArgumentDefaultsHelpFormatter,
        description=(
            'Timer Test Intraday'
        )
    )

    parser.add_argument('--data0', default='../../datas/2006-min-005.txt',
                        required=False, help='Data to read in')

    # Defaults for dates
    parser.add_argument('--fromdate', required=False, default='',
                        help='Date[time] in YYYY-MM-DD[THH:MM:SS] format')

    parser.add_argument('--todate', required=False, default='',
                        help='Date[time] in YYYY-MM-DD[THH:MM:SS] format')

    parser.add_argument('--cerebro', required=False, default='',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--broker', required=False, default='',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--sizer', required=False, default='',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--strat', required=False, default='',
                        metavar='kwargs', help='kwargs in key=value format')

    parser.add_argument('--plot', required=False, default='',
                        nargs='?', const='{}',
                        metavar='kwargs', help='kwargs in key=value format')

    return parser.parse_args(pargs)

if __name__ == '__main__':
    runstrat()` 
```
