## quantmod简介

### 1 quantmod是什么？


`quantmod`是`R`语言中的金融量化投资分析包，它提供了量化投资分析一体化解决方案，能够帮助用户完成提取数据、数据重整、金融建模、交易回测和模型可视化等诸多环节。


### 2 quantmod能做什么？

#### 2.1  提取数据

运行下面的代码可以获得`中国移动`的日交易数据：

``` {r}
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

类似方法可以获取`谷歌`(GOOL)、`特斯拉`(TESL)等公司的日交易数据。

#### 2.2 数据预处理

获取数据之后，可能需要对数据进行预处理，`quantmod`封装了一系列对建模数据预处理的函数。下面是一个简单的例子：

``` {r}
# 获取高盛公司的交易数据
> getSymbols("GS") #Goldman OHLC from yahoo
[1] "GS"
# 判断数据是否包含open\high\low等数据列
> is.OHLC(GS) 
# 判断数据是否包含交易量
> has.Vo(GS)
# 提取收盘价
> Op(GS) 
# 提取局部高点
> seriesHi(GS) 
```

#### 2.3 金融数据可视化

`quantmod`包提供了一系列对金融数据进行可视化的函数，举几个例子：

```{r}
# 获取高盛日交易数据
> getSymbols("GS") #Goldman OHLC from yahoo
[1] "GS"
#绘制序列图
> chartSeries(GS) 
#绘制蜡烛图，并调整序列图的时间窗口
> candleChart(GS,subset='2007-12::2008')
#定制蜡烛图的样式
> candleChart(GS,theme='white', type='candles')
#调整蜡烛图的时间跨度
> reChart(major.ticks='months',subset='first 16 weeks') 
#向序列图填制技术指标
> chartSeries(GS, theme="white",TA="addVo();addBBands();addCCI()") 
```

#### 2.4 金融建模

```{r}
getSymbols(c("^SSEC","CHL"))
q.model <- specifyModel(Next(OpCl(CHL)) ~ Lag(OpHi(CHL),0:3) + Hi(SSEC))
q.model
head(modelData(q.model))
bD=buildData(Next(OpCl(CHL)) ~ Lag(OpHi(CHL),0:3) + Hi(SSEC))
bM <- buildModel(q.model,method='lm',training.per=c('2013-08-01','2013-09-30'))
summary(bM)
tradeModel(bM)
```
### 3 更多知识

* 学习quantmod提取数据:点击[Get data](https://github.com/dengyishuo/Notes/blob/master/quantmod/get_data.md)
* 学习quantmod数据操作:点击[Data manupulation](https://github.com/dengyishuo/Notes/blob/master/quantmod/data_manupulation.md)
* 学习quatnmod金融绘图:点击[Charting](https://github.com/dengyishuo/Notes/blob/master/quantmod/charting.md)
* 学习quantmod金融建模:点击[Modelling](https://github.com/dengyishuo/Notes/blob/master/quantmod/modelling.md)
