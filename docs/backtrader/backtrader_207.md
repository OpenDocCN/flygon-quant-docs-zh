# 资金流动指标

> 原文：[`www.backtrader.com/recipes/indicators/mfi/mfi/`](https://www.backtrader.com/recipes/indicators/mfi/mfi/)

参考

+   [`school.stockcharts.com/doku.php?id=technical_indicators:money_flow_index_mfi`](https://school.stockcharts.com/doku.php?id=technical_indicators:money_flow_index_mfi)

```py
class MFI(bt.Indicator):
    lines = ('mfi',)
    params = dict(period=14)

    alias = ('MoneyFlowIndicator',)

    def __init__(self):
        tprice = (self.data.close + self.data.low + self.data.high) / 3.0
        mfraw = tprice * self.data.volume

        flowpos = bt.ind.SumN(mfraw * (tprice > tprice(-1)), period=self.p.period)
        flowneg = bt.ind.SumN(mfraw * (tprice < tprice(-1)), period=self.p.period)

        mfiratio = bt.ind.DivByZero(flowpos, flowneg, zero=100.0)
        self.l.mfi = 100.0 - 100.0 / (1.0 + mfiratio)
```

这是指标运作的一个视角

![MFI 视图](img/20ca39918a52217810921637153b451c.png)
