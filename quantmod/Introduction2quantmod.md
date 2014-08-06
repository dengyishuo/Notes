## 0 quantmod简介

### 0.1 quantmod是什么？

quantmod是R语言中的金融量化投资分析包，提供量化投资分析一体化解决方案，能够帮助用户完成提取数据、数据重整、金融建模、交易回测和模型可视化等诸多环节。

## 0.2 quantmod能做什么？

* 提取数据

```
> getSymbols("CHL");
[1] "CHL"
> head(CHL);
           CHL.Open CHL.High CHL.Low CHL.Close CHL.Volume CHL.Adjusted
2007-01-03    45.45    46.84   45.45     46.14    3538300        35.40
2007-01-04    44.25    45.05   43.62     44.43    3210000        34.09
2007-01-05    44.99    44.99   43.12     43.24    2036300        33.17
2007-01-08    43.69    44.07   43.16     43.90    1230200        33.68
2007-01-09    42.98    42.98   41.56     41.85    2566100        32.11
2007-01-10    41.41    42.12   40.86     41.96    1987000        32.19

```

* 数据重整

```
> getSymbols("GS") #Goldman OHLC from yahoo
[1] "GS"
> is.OHLC(GS) # does the data contain at least OHL and C?
> has.Vo(GS) # how about volume?
> Op(GS) # just the Open column please. 
> seriesHi(GS) # where and what was the high point 
```

* 金融数据可视化

```
> getSymbols("GS") #Goldman OHLC from yahoo
[1] "GS"
> chartSeries(GS) 
> candleChart(GS,subset='2007-12::2008')
> candleChart(GS,theme='white', type='candles')
> reChart(major.ticks='months',subset='first 16 weeks') 
> chartSeries(GS, theme="white",TA="addVo();addBBands();addCCI()") 
```

### 0.3 更多知识


