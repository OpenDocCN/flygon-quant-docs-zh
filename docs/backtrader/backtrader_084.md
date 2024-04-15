# 调整器参考

> [`www.backtrader.com/docu/sizers-reference/`](https://www.backtrader.com/docu/sizers-reference/)原文：

## FixedSize

#### 类 backtrader.sizers.FixedSize()

这个调整器只是为任何操作返回一个固定的大小。大小可以通过系统希望使用的分期数量来控制，方法是通过指定`tranches`参数来缩放到交易中。

参数：

```py
`* `stake` (default: `1`)

* `tranches` (default: `1`)` 
```

## FixedReverser

#### 类 backtrader.sizers.FixedReverser()

这个调整器返回需要的固定大小以反转开仓位置或开仓大小。

+   开仓位置：返回参数`stake`。

+   反转仓位：返回 2 * `stake`。

参数：

```py
`* `stake` (default: `1`)` 
```

## PercentSizer

#### 类 backtrader.sizers.PercentSizer()

这个调整器返回可用现金的百分比。

参数：

```py
`* `percents` (default: `20`)` 
```

## AllInSizer

#### 类 backtrader.sizers.AllInSizer()

这个调整器返回经纪人的所有可用现金。

参数：

```py
`* `percents` (default: `100`)` 
```

## PercentSizerInt

#### 类 backtrader.sizers.PercentSizerInt()

这个调整器以截断为整数的形式返回可用现金的百分比。

参数：

```py
`* `percents` (default: `20`)` 
```

## AllInSizerInt

#### 类 backtrader.sizers.AllInSizerInt()

这个调整器将经纪人的所有可用现金返回，并将大小截断为整数。

参数：

```py
 `* `percents` (default: `100`)` 
```
