# 优化改进

> 原文：[`www.backtrader.com/docu/optimization-improvements/`](https://www.backtrader.com/docu/optimization-improvements/)

*backtrader* 版本 `1.8.12.99` 包括了 *数据源* 和 *结果* 在多进程中的管理改进。

注意

对两者的行为进行了调整

这些选项的行为可以通过两个新的 *Cerebro* 参数进行控制：

+   `optdatas`（默认：`True`）

    如果为 `True` 并且正在优化（并且系统可以 `preload` 并使用 `runonce`，数据预加载将仅在主进程中执行一次，以节省时间和资源。

+   `optreturn`（默认：`True`）

    如果为 `True`，则优化结果将不是完整的 `Strategy` 对象（以及所有 *数据*，*指标*，*观察器*等），而是具有以下属性的对象（与 `Strategy` 中相同）：

    +   `params`（或 `p`）执行策略时的参数

    +   `analyzers` 执行的策略

    在大多数情况下，只需要 *analyzers* 和使用哪些 *params* 来评估策略的表现。如果需要对生成的值（例如 *指标*）进行详细分析，请关闭此选项。

## 数据源管理

在 *优化* 场景中，这是 *Cerebro* 参数的可能组合：

+   `preload=True`（默认）

    在运行任何回测代码之前，数据源将被预加载

+   `runonce=True`（默认）

    *指标* 将以批处理模式计算在一个紧密的 *for* 循环中，而不是逐步进行。

如果两个条件都为 `True` 并且 `optdatas=True`，则：

+   在生成新子进程（负责执行 *回测* 的子进程）之前，*数据源* 将在主进程中预加载

## 结果管理

在 *优化* 场景中，评估每个 *策略* 运行时使用的不同参数时，两件事应该起到最重要的作用：

+   `strategy.params`（或 `strategy.p`）

    回测中使用的实际值集

+   `strategy.analyzers`

    负责提供 *策略* 实际表现评估的对象。示例：

    `SharpeRatio_A`（年化 *SharpeRatio*）

当 `optreturn=True` 时，将创建占位符对象，而不是返回完整的 *策略* 实例，这些对象携带上述两个属性，以便进行评估。

这样可以避免传递大量生成的数据，例如 *回测* 期间指标生成的值

如果希望使用 *完整的策略对象*，只需在 cerebro *实例化* 期间或进行 `cerebro.run` 时将 `optreturn=False`。

## 一些测试运行

*backtrader* 源代码中的 *优化* 示例已经扩展，以添加对 `optdatas` 和 `optreturn` 的控制（实际上是禁用它们）

### 单核运行

作为参考，当 CPU 数量限制为 `1` 且未使用 `multiprocessing` 模块时发生了什么：

```py
`$ ./optimization.py --maxcpus 1
==================================================
**************************************************
--------------------------------------------------
OrderedDict([(u'smaperiod', 10), (u'macdperiod1', 12), (u'macdperiod2', 26), (u'macdperiod3', 9)])
**************************************************
--------------------------------------------------
OrderedDict([(u'smaperiod', 10), (u'macdperiod1', 13), (u'macdperiod2', 26), (u'macdperiod3', 9)])
...
...
OrderedDict([(u'smaperiod', 29), (u'macdperiod1', 19), (u'macdperiod2', 29), (u'macdperiod3', 14)])
==================================================
Time used: 184.922727833` 
```

### 多核运行

不限制 CPU 数量时，Python 的 `multiprocessing` 模块将尝试使用所有 CPU。`optdatas` 和 `optreturn` 将被禁用。

#### `optdata`和`optreturn`均处于激活状态

默认行为：

```py
`$ ./optimization.py
...
...
...
==================================================
Time used: 56.5889185394` 
```

多核和*数据提供*以及*结果*改进带来的总改进意味着从`184.92`秒降至`56.58`秒。

请注意，示例使用`252`根柱和指标仅生成长度为`252`点的值。这只是一个例子。

真正的问题是这有多少归因于新行为。

#### `optreturn`已停用

让我们将完整的*策略*对象传递给调用者：

```py
`$ ./optimization.py --no-optreturn
...
...
...
==================================================
Time used: 67.056914007` 
```

执行时间增加了`18.50%`（或加速比为`15.62%`）。

#### `optdatas`已停用

每个子进程被强制加载其自己的*数据提供*值集：

```py
`$ ./optimization.py --no-optdatas
...
...
...
==================================================
Time used: 72.7238112637` 
```

执行时间增加了`28.52%`（或加速比为`22.19%`）。

#### 两者均已停用

仍然使用多核但保持旧的非改进行为：

```py
`$ ./optimization.py --no-optdatas --no-optreturn
...
...
...
==================================================
Time used: 83.6246643786` 
```

执行时间增加了`47.79%`（或加速比为`32.34%`）。

这表明使用多核是时间改进的主要贡献者。

注意

执行是在装有`i7-4710HQ`（4 核/8 逻辑）和 16 GBytes RAM 的 Windows 10 64 位笔记本电脑上进行的。在其他条件下情况可能会有所不同

## 结论

+   优化期间减少时间的最大因素是使用多核

+   使用`optdatas`和`optreturn`的示例运行显示每个的加速比约为`22.19%`和`15.62%`（在测试中两者一起为`32.34%`）

## 示例用法

```py
`$ ./optimization.py --help
usage: optimization.py [-h] [--data DATA] [--fromdate FROMDATE]
                       [--todate TODATE] [--maxcpus MAXCPUS] [--no-runonce]
                       [--exactbars EXACTBARS] [--no-optdatas]
                       [--no-optreturn] [--ma_low MA_LOW] [--ma_high MA_HIGH]
                       [--m1_low M1_LOW] [--m1_high M1_HIGH] [--m2_low M2_LOW]
                       [--m2_high M2_HIGH] [--m3_low M3_LOW]
                       [--m3_high M3_HIGH]

Optimization

optional arguments:
  -h, --help            show this help message and exit
  --data DATA, -d DATA  data to add to the system
  --fromdate FROMDATE, -f FROMDATE
                        Starting date in YYYY-MM-DD format
  --todate TODATE, -t TODATE
                        Starting date in YYYY-MM-DD format
  --maxcpus MAXCPUS, -m MAXCPUS
                        Number of CPUs to use in the optimization
                          - 0 (default): use all available CPUs
                          - 1 -> n: use as many as specified
  --no-runonce          Run in next mode
  --exactbars EXACTBARS
                        Use the specified exactbars still compatible with preload
                          0 No memory savings
                          -1 Moderate memory savings
                          -2 Less moderate memory savings
  --no-optdatas         Do not optimize data preloading in optimization
  --no-optreturn        Do not optimize the returned values to save time
  --ma_low MA_LOW       SMA range low to optimize
  --ma_high MA_HIGH     SMA range high to optimize
  --m1_low M1_LOW       MACD Fast MA range low to optimize
  --m1_high M1_HIGH     MACD Fast MA range high to optimize
  --m2_low M2_LOW       MACD Slow MA range low to optimize
  --m2_high M2_HIGH     MACD Slow MA range high to optimize
  --m3_low M3_LOW       MACD Signal range low to optimize
  --m3_high M3_HIGH     MACD Signal range high to optimize` 
```
