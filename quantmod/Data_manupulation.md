## 2 基本数据操作

任何数据分析过程都离不开数据操作，金融数据分析也不例外。这里就重点说一说quantmod包中与数据操作相关的一些函数及其用法。quantmod包中的函数大致可以归为这么几类：逻辑判断类、数据提取类、时期转换类、计算类、其它类。下面分类介绍一下这些函数，说明这些函数存在的原因以及具体用法。

### 2.1 逻辑判断函数

为什么要有逻辑判断类呢？因为quantmod包能处理的数据具有格式上的要求，即必须是OHLC、OHLCV、BBO、TBBO、HLC、quantmod、quantmodResults 中的一种，否则的话，函数没法进行正确的计算。这就要求我们在进行其它操作之前，得先判断我们读进来的数据是否属于上述数据中的一种。逻辑判断类的函数就是干这个活的。逻辑判断类的函数又能细分为两类，即 is 类和 has 类。

#### 2.1.1 is 族函数

is类函数用来判断数据是否属于某个特定类型。具体用法如下：

* is.OHLC(x):检查是否是OHLC类型数据
* is.OHLCV(x)：检查是否是OHLCV类型数据
* is.BBO(x)：检查是否是BBO类型数据
* is.TBBO(x)：检查是否是TBBO类型数据
* is.HLC(x):检查是否是HLC类型数据
* is.quantmod(x): 检查是否是quantmod对象
* is.quantmodResults(x):  检查是否是quatmodResults对象

举个例子。我们从网上获取 yahoo 公司的股票数据：

```{r}
> getSymbols("YHOO")
> YHOO["2013-10-01::"]
           YHOO.Open YHOO.High YHOO.Low YHOO.Close YHOO.Volume YHOO.Adjusted
2013-10-01     33.36     34.44    33.30      34.31    28153200         34.31
2013-10-02     34.15     34.70    33.90      34.14    21609200         34.14
2013-10-03     34.32     34.36    33.20      33.88    23252300         33.88
2013-10-04     33.96     35.06    33.96      34.89    23937800         34.89
2013-10-07     34.46     34.69    34.08      34.14    15432800         34.14
2013-10-08     34.46     34.50    32.10      32.93    42872200         32.93
2013-10-09     33.07     33.33    31.79      33.01    33491800         33.01
2013-10-10     33.49     33.91    33.33      33.87    23417200         33.87
2013-10-11     33.67     34.37    33.61      34.15    17006000         34.15
2013-10-14     33.80     34.10    33.68      34.00    17594900         34.00
2013-10-15     34.20     34.32    33.06      33.38    42360300         33.38
## 判断是否是OHLC对象
> is.OHLC(YHOO)
[1] TRUE
## 判断是否是OHLCV对象
> is.OHLCV(YHOO)
[1] TRUE
## 判断是否是HLC对象
> is.HLC(YHOO)
[1] TRUE
## 判断是否是BBO对象
> is.BBO(YHOO)
[1] FALSE
## 判断是否是quantmod对象
> is.quantmod(YHOO)
[1] FALSE FALSE
```

其它的用法类似，不再赘述。

#### 2.1.2 has 族函数

has 族函数用来判断数据中是否包含某个列或者字段。

* has.OHLC(x, which = FALSE) ：检查是否有open,high,low,close列或者字段
* has.HLC(x, which = FALSE)：检查是否有high,low,close列或者字段
* has.OHLCV(x, which = FALSE)：检查是否有open,high,low,close,volume列或者字段
* has.Op(x, which = FALSE)：检查是否有open列或者字段
* has.Hi(x, which = FALSE)：检查是否有high列或者字段
* has.Lo(x, which = FALSE)：检查是否有low列或者字段
* has.Cl(x, which = FALSE)：检查是否有close列或者字段
* has.Vo(x, which = FALSE)：检查是否有volume列或者字段
* has.Ad(x, which = FALSE)：检查是否有adjust列或者字段
* has.Ask(x, which = FALSE)：检查是否有ask列或者字段
* has.Bid(x, which = FALSE)：检查是否有bid列或者字段
* has.Price(x, which = FALSE)：检查是否有price列或者字段
* has.Qty(x, which = FALSE)：检查是否Qty列或者字段
* has.Trade(x, which = FALSE)：检查是否有trade列或者字段

```{r}
# 检查是否有open,high,low,close列或者字段
> has.OHLC(YHOO)
[1] TRUE TRUE TRUE TRUE
# 检查是否有high,low,close列或者字段
> has.HLC(YHOO)
[1] TRUE TRUE TRUE
# 检查是否有open,high,low,close,volume列或者字段
> has.OHLCV(YHOO)
[1] TRUE TRUE TRUE TRUE TRUE
```

其它类似，不予赘述。

### 2.2 抽取数据的函数

建模过程中要对数据进行各种转换和计算，而转换和计算的前提是能够先抽取目标数据。quantmod中抽取数据的函数主要包括：

* Op(x):提取开盘价
* Hi(x):提取最高价
* Lo(x):提取最低价
* Cl(x):提取收盘价
* Vo(x):提取交易量
* Ad(x):提取调整价格
* HLC(x):提取最高价、最低价和收盘价
* OHLC(x):提取开盘价、最高价、最低价和收盘价

```{r}
## 提取开盘价
> Op(YHOO)
           YHOO.Open
2007-01-03     25.85
2007-01-04     25.64
2007-01-05     26.70
2007-01-08     27.70
2007-01-09     28.00
2007-01-10     27.48
## 提取最高价
> Hi(YHOO)
           YHOO.High
2007-01-03     26.26
2007-01-04     26.92
2007-01-05     27.87
2007-01-08     28.04
2007-01-09     28.05
2007-01-10     28.92
## 提取最低价
> Lo(YHOO)
           YHOO.Low
2007-01-03    25.26
2007-01-04    25.52
2007-01-05    26.66
2007-01-08    27.43
2007-01-09    27.41
2007-01-10    27.44
## 提取收盘价
> Cl(YHOO)
    YHOO.Close
2007-01-03      25.61
2007-01-04      26.85
2007-01-05      27.74
2007-01-08      27.92
2007-01-09      27.58
2007-01-10      28.70
## 提取成交量
> Vo(YHOO)
          YHOO.Volume
2007-01-03    26352700
2007-01-04    32512200
2007-01-05    64264600
2007-01-08    25713700
2007-01-09    25621500
2007-01-10    40240000
## 提取调整价格
> Ad(YHOO)
          YHOO.Adjusted
2007-01-03         25.61
2007-01-04         26.85
2007-01-05         27.74
2007-01-08         27.92
2007-01-09         27.58
2007-01-10         28.70
## 提取最高价、最低价和收盘价
> HLC(YHOO)
        YHOO.High YHOO.Low YHOO.Close
2007-01-03     26.26    25.26      25.61
2007-01-04     26.92    25.52      26.85
2007-01-05     27.87    26.66      27.74
2007-01-08     28.04    27.43      27.92
2007-01-09     28.05    27.41      27.58
2007-01-10     28.92    27.44      28.70
## 提取开盘价、最高价、最低价和收盘价
> OHLC(YHOO)
YHOO.Open YHOO.High YHOO.Low YHOO.Close
2007-01-03     25.85     26.26    25.26      25.61
2007-01-04     25.64     26.92    25.52      26.85
2007-01-05     26.70     27.87    26.66      27.74
2007-01-08     27.70     28.04    27.43      27.92
2007-01-09     28.00     28.05    27.41      27.58
2007-01-10     27.48     28.92    27.44      28.70
```

### 2.3 简单的计算函数

#### 2.3.1 Delt 函数

Delt 函数的作用是计算变化率或者变动比率。基本用法如下：

```
Delt(x1, x2 = NULL, k = 0, type = c("arithmetic", "log")) 
```

``` r
Stock.Open <- c(102.25,102.87,102.25,100.87,103.44,103.87,103.00)
Stock.Close <- c(102.12,102.62,100.12,103.00,103.87,103.12,105.12)
## 按开盘价计算收益率
> Delt(Stock.Open)
 Delt.1.arithmetic
[1,]                NA
[2,]       0.006063570
[3,]      -0.006027024
[4,]      -0.013496333
[5,]       0.025478338
[6,]       0.004156999
[7,]      -0.008375854
```

或者:

```
> Delt(Stock.Open,k=1)
   Delt.1.arithmetic
[1,]                NA
[2,]       0.006063570
[3,]      -0.006027024
[4,]      -0.013496333
[5,]       0.025478338
[6,]       0.004156999
[7,]      -0.008375854
```

等同于：

``` r
> diff(Stock.Open)/Stock.Open[1:6]
0.006063570 -0.006027024 -0.013496333  0.025478338  0.004156999 -0.008375854
```

也即是计算（T日收盘价-T-1日收盘价）/T-1日收盘价，对应的金融意义是算术收益率。当然，也可以计算几何收益率或者对数收益率，只需将 type 参数设定为 log 即可。

``` r
> Delt(Stock.Open,type='log')
      Delt.1.log
[1,]           NA
[2,]  0.006045260
[3,] -0.006045260
[4,] -0.013588236
[5,]  0.025159175
[6,]  0.004148383
[7,] -0.008411129
```

等同于：

``` r
> diff(log(Stock.Open))
[1]  0.006045260 -0.006045260 -0.013588236  0.025159175  0.004148383 -0.008411129
> 
```

除此之外还可以两列数据之间的变动比例。比如：

``` r
> Delt(Stock.Open,Stock.Close)
     Delt.0.arithmetic
[1,]      -0.001271394
[2,]      -0.002430252
[3,]      -0.020831296
[4,]       0.021116288
[5,]       0.004156999
[6,]      -0.007220564
[7,]       0.020582524
```

等同于:

``` r
> (Stock.Open-Stock.Close)/Stock.Open
[1]  0.001271394  0.002430252  0.020831296 -0.021116288 -0.004156999  0.007220564 -0.020582524
```

对应的经济学意义是股票收盘价相对于开盘价的变动幅度。

计算开盘价与下一期收盘价的差

``` r
> Delt(Stock.Open,Stock.Close,k=1)
     Delt.1.arithmetic
[1,]                NA
[2,]       0.003618582
[3,]      -0.026732770
[4,]       0.007334963
[5,]       0.029741251
[6,]      -0.003093581
[7,]       0.012034274
```

等同于：

``` r
> (Stock.Open[1:6]-Stock.Close[2:7])/Stock.Open[1:6]
[1] -0.003618582  0.026732770 -0.007334963 -0.029741251  0.003093581 -0.012034274
```

#### 2.3.2 其它列计算函数

* OpCl(x):等同于Delt(Op(x), Cl(x))
* ClCl(x):等同于Delt(Cl(x)),等价于diff(Cl(x))/lag(Cl(x))
* HiCl(x):等同于Delt(Hi(x),Cl(x))
* LoCl(x):等同于Delt(Lo(x),Cl(x))
* LoHi(x):等同于Delt(Lo(x),Hi(x))
* OpHi(x):等同于Delt(Op(x),Hi(x))
* OpLo(x):等同于Delt(OP(x),Lo(x))
* OpOp(x):等同于Delt(Op(x))

### 2.4 计算收益率的函数

计算收益率的主要函数有：

* periodReturn()
* dailyReturn()
* weeklyReturn)
* monthlyReturn()
* quarterlyReturn()
* annualReturn()
* yearlyReturn()
* allReturns()

```{r}
#求日收益率
periodReturn(x,period='daily')
#或者
dailyReturn(x)
```

```{r}
#求周收益率
periodReturn(x,period='weekly')
#或者
weeklyReturn(x)
```

```{r}
#求月收益率
periodReturn(x,period='monthly')
#或者
monthlyReturn(x)
```

```
#求季度度收益率
periodReturn(x,period='quarterly')
#或者
quarterlyReturn(x)
```
### 2.5 其它函数

#### 2.5.1 跨期变换函数

* Lag(x,k):求滞后k期
* Next(x, k = 1)：求k期后
* first(x,k)：求前k个
* last(x,k):求后k个

#### 2.5.2 峰值、峰谷函数

* findPeaks(x, thresh=0)：找峰值
* findValleys(x, thresh=0)：找谷底值

#### 2.5.3 series族函数

* seriesHi(x)：提取一段时序的最高价
* seriesLo(x):提取一段时序的最低价

```{r}

```

#### 2.5.4 差分阈值函数

* seriesIncr(x, thresh=0, diff.=1L)：差分后大于限值的点
* seriesDecr(x, thresh=0, diff.=1L)：差分后小于限值的点
* endpoints()：寻找节点

### 2.5.5 日期转换函数

* to.weekly()：将OHLC数据转化为周数据
* to.monthly():将PHLC数据转化为月数据
* periodicity()：返回数据的日期范围

```{r}
#例子
Lag(Op(CHL))
Lag(Op(CHL),c(1,3,5))
Next(Cl(CHL))
Delt(Op(CHL),Cl(CHL),k=1:3)

CHL['2007']
CHL['2009']
CHL['2009-01']
CHL['2009-01::2009-08']
CHL['2009-01::']

first(CHL)
first(CHL,5)
first(CHL,'3 weeks')
last(CHL,'-3 weeks')
last(first(CHL, '2 weeks'), '3 days')

periodicity(CHL)
unclass(periodicity(CHL))
to.weekly(CHL)
to.monthly(CHL)

periodicity(to.monthly(CHL))
endpoints(CHL,on="months")
apply.weekly(CHL,FUN=function(x) { max(Cl(x)) } )
period.apply(CHL,endpoints(CHL,on='weeks'),FUN=function(x) { max(Cl(x)) } )
as.numeric(period.max(Cl(CHL),endpoints(CHL,on='weeks')))
```

