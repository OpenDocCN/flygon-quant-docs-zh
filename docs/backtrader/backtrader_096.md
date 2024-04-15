# 计时器

> 原文：[`www.backtrader.com/docu/timers/timers/`](https://www.backtrader.com/docu/timers/timers/)

发布`1.9.44.116`添加了*计时器*到*backtrader*可用工具的工具库中。此功能允许在给定时间点获得对`notify_timer`（在`Cerebro`和`Strategy`中可用）的回调，用户可以对其进行细粒度的控制。

注意

在`1.9.46.116`中进行了一些更正

## 选项

+   基于绝对时间输入或与会话开始/结束时间相关的计时器

+   时区规范用于时间规范，无论是直接还是通过*pytz*兼容对象或通过数据源会话结束时间

+   与指定时间相关的起始偏移量

+   重复间隔

+   星期过滤器（带有继续选项）

+   月份过滤器（带有继续选项）

+   自定义回调过滤器

## 使用模式

在`Cerebro`和`Strategy`子类中，计时器回调将在以下方法中收到。

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

### 添加计时器 - 通过策略

用这种方法完成

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

它返回创建的`Timer`实例。

有关参数的解释，请参见下文。

### 添加计时器 - 通过 Cerebro

与相同的方法完成，并只添加参数`strats`。如果设置为`True`，则不仅将通知计时器给*cerebro*，还将通知给系统中运行的所有策略。

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

它返回创建的`Timer`实例。

## 计时器何时被调用

### 如果`cheat=False`

这是默认情况。在这种情况下将调用计时器：

+   在数据源加载了当前条的新值后

+   在经纪人评估订单并重新计算投资组合价值之后

+   在指标重新计算之前（因为这是由策略触发的）

+   在调用任何策略的`next`方法之前

### 如果`cheat=True`

在这种情况下将调用计时器：

+   在数据源加载了当前条的新值后

+   **在**经纪人评估订单并重新计算投资组合价值之前

+   因此在指标重新计算之前和调用任何策略的`next`方法之前

这允许例如以下具有每日条的情景：

+   在经纪人评估新条之前调用计时器

+   指标具有前一天收盘时的值，并可用于生成入场/出场信号（或者在上次`next`评估期间可能已经设置了标志）

+   因为新价格已经可用，所以可以使用开盘价来计算股票。这假定例如通过观察开盘竞价可以得到关于`open`的良好指示。

## 使用每日条运行

示例`scheduled.py`默认使用*backtrader*发行版中提供的标准每日条运行。策略的参数

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

数据的会话时间如下：

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

如指定的，计时器在`15:30`时在跳动。没有什么意外。让我们添加一个偏移量为 30 分钟。

```py
`$ ./scheduled.py --strat when='datetime.time(15,30)',offset='datetime.timedelta(minutes=30)'

strategy notify_timer with tid 0, when 2005-01-03 16:00:00 cheat False
1, 2005-01-03 17:30:00, Week 1, Day 1, O 2952.29, H 2989.61, L 2946.8, C 2970.02
strategy notify_timer with tid 0, when 2005-01-04 16:00:00 cheat False
2, 2005-01-04 17:30:00, Week 1, Day 2, O 2969.78, H 2979.88, L 2961.14, C 2971.12
strategy notify_timer with tid 0, when 2005-01-05 16:00:00 cheat False
...` 
```

时间已从`15:30`更改为`16:00`以进行计时。没有什么意外。让我们做同样的事情，但参考会话开始。

```py
`$ ./scheduled.py --strat when='bt.timer.SESSION_START',offset='datetime.timedelta(minutes=30)'

strategy notify_timer with tid 0, when 2005-01-03 09:30:00 cheat False
1, 2005-01-03 17:30:00, Week 1, Day 1, O 2952.29, H 2989.61, L 2946.8, C 2970.02
strategy notify_timer with tid 0, when 2005-01-04 09:30:00 cheat False
2, 2005-01-04 17:30:00, Week 1, Day 2, O 2969.78, H 2979.88, L 2961.14, C 2971.12
...` 
```

Et voilá！回调被调用的时间是`09:30`。而会话开始，见上文，是`09:00`。这使得我们能够简单地说，在会话开始后*30 分钟*执行某个动作。

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

**没有重复**。原因是价格的分辨率是每日的。计时器像在先前的例子中那样在`09:30`第 1 次被调用。但当系统获取下一批价格时，它们发生在下一天。显然，计时器只能被调用一次。需要更低的分辨率。

但在转到较低分辨率之前，让我们通过在会话结束前调用计时器来欺骗一下。

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

策略添加了一个带有`cheat=True`的第 2 个计时器。这是第二个添加的，因此将收到第二个*tid*（计时器 id），即`1`（请在上面的示例中查看分配的*tid*为`0`）

而`1`在`0`之前被调用，因为该计时器是*作弊*的，且在系统中许多事件发生之前被调用（有关说明，请参见上文）

由于价格的*每日*分辨率，这并没有太大的区别，除了：

+   策略还在开盘前发布了订单...并且它与次日的开盘价匹配

    即使在开盘前作弊，这仍然是正常行为，因为经纪人中也没有激活*开盘欺骗*。

相同，但经纪人处于`coo=True`模式

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

有些事情已经改变了。

+   订单在欺骗计时器中于`2005-01-03`发布

+   订单在`2005-01-03`以开盘价执行

    事实上，就像在市场真正开放之前几秒钟就已经行动一样。

## 使用 5 分钟柱形图运行

示例`scheduled-min.py`默认以*backtrader*分发的标准 5 分钟柱形图运行。策略的参数被扩展以包括`monthdays`和*carry*选项

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

数据具有相同的会话时间：

+   开始：09:00

+   结束：17:30

让我们做一些实验。首先是单个计时器。

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

计时器按要求在`15:30`启动。日志显示它在前两天的第 1 次中如何做到这一点。

将`15 分钟`的*重复*加入混合中

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

如预期的那样，第 1 次调用在`15:30`触发，然后每 15 分钟重复一次，直到会话结束于`17:30`。当新会话开始时，计时器再次被重置为`15:30`。

现在在会话开始前作弊

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

订单创建于`09:05:00`，执行于`09:10:00`，因为经纪人不处于*开盘欺骗*模式。让我们设置它...

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

下达时间和执行时间为`09:05:00`，执行价格为`09:05:00`的开盘价。

## 额外的场景

定时器允许通过传递一周的日期列表（遵循 iso 规范的整数，其中星期一为 1，星期日为 7）来指定它们应该在哪些日期执行，如下所示：

+   `weekdays=[5]`，这将要求定时器仅在星期五有效

    如果星期五是非交易日，并且定时器应在下一个交易日触发，则可以添加 `weekcarry=True`

类似于它，可以决定每月的第 15 天采取行动：

+   `monthdays=[15]`

    如果第 15 天碰巧是非交易日，并且定时器应在下一个交易日触发，则可以添加 `monthcarry=True`

没有实现像 *三月、六月、九月和十二月的第 3 个星期五*（期货/期权到期日）的规则，但可以通过传递实现规则的可能性来实现：

+   `allow=callable`，可调用的接受 `datetime.date` 实例。请注意，这不是 `datetime.datetime` 实例，因为 *allow* 可调用仅用于决定某一天是否适合用于定时器。

    要实现类似上述规则的东西：

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

    并且可以将 `allow=FutOpeExp()` 传递给定时器的创建

    这将允许定时器在这些月份的第 3 个星期五触发，并在期货到期前可能平仓。

## `add_timer` 的参数

```py
`* `when`: can be

  * `datetime.time` instance (see below `tzdata`)

  * `bt.timer.SESSION_START` to reference a session start

  * `bt.timer.SESSION_END` to reference a session end` 
```

+   `offset` 必须是 `datetime.timedelta` 实例

    用于偏移值 `when`。它在与 `SESSION_START` 和 `SESSION_END` 结合使用时有实际用途，以指示定时器在会话开始后 `15 分钟` 被调用。

    +   `repeat` 必须是 `datetime.timedelta` 实例

    在第 1 次调用之后指示是否在同一会话中按照预定的 `repeat` 间隔安排进一步调用

    一旦定时器超过会话结束，它将被重置为 `when` 的原始值

    +   `weekdays`：一个**排序的**整数迭代器，指示定时器实际上可以在哪些日期（iso 代码，星期一为 1，星期日为 7）被调用

    如果未指定，定时器将在所有日期上都活动

    +   `weekcarry`（默认值：`False`）。如果为 `True` 并且未见到工作日（例如：交易假期），则定时器将在下一天执行（即使在新的一周中）

    +   `monthdays`：一个**排序的**整数迭代器，指示定时器应在每月的哪些日子执行。例如，总是在月份的 *15* 号执行

    如果未指定，定时器将在所有日期上都活动

    +   `monthcarry`（默认值：`True`）。如果当天没有见过（周末，交易假日），则定时器将在下一个可用的日期执行。

    +   `allow`（默认值：`None`）。一个回调，接收 `datetime.date` 实例并返回 `True`（如果日期适用于定时器）或返回 `False`

    +   `tzdata` 可以是 `None`（默认值），一个 `pytz` 实例或一个 `data feed` 实例。

    `None`：`when` 按照字面值解释（即使不是），这意味着将其视为 UTC 处理

    `pytz` 实例：`when` 将被解释为指定时区实例指定的本地时间。

    `数据源` 实例：`when` 将被解释为在数据源实例的 `tz` 参数指定的本地时间。

    !!! 注意

    ```py
     `If `when` is either `SESSION_START` or `SESSION_END` and `tzdata` is
      `None`, the 1st *data feed* in the system (aka `self.data0`) will be
      used as the reference to find out the session times.` 
    ```

    +   `strats`（默认为：`False`）还要调用策略的 `notify_timer`

    +   `cheat`（默认为 `False`）如果设为 `True`，则计时器将在经纪人有机会评估订单之前被调用。这样可以在会话开始之前，例如根据开盘价发出订单的机会。

    +   `*args`：任何额外的参数都将传递给 `notify_timer`

    +   `**kwargs`：任何额外的关键字参数都将传递给 `notify_timer`

## 示例用法 `scheduled.py`

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

## 示例用法 `scheduled-min.py`

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

## 示例源 `scheduled.py`

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

## 示例源 `scheduled-min.py`

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
                # tzdata=self.data0,
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
