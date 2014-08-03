## 2 基本数据操作

任何数据分析过程都离不开数据操作，金融数据分析也不例外。这里就重点说一说`quantmod`包中与数据操作相关的一些函数及其用法。在谋划如何介绍这些包中的函数时，我很纠结，因为集中对函数进行学习很容易堕入函数和参数陷阱。会学习的十分难受。首先，按什么顺序来学习这些函数呢？按字母顺序来学习的话，显然是个颇为狗血的决定。因为字母顺序强硬地打破了各个函数之间承接关系。鉴于此，我决定将包中的函数按照用途进行分组。

`quantmod` 包中的函数大致可以归为这么几类：逻辑判断类、数据提取类、时期转换类、计算类、其它类。下面分类介绍一下这些函数，说明这些函数存在的原因以及具体用法。

### 2.1 逻辑判断函数

为什么要有逻辑判断类呢？因为`quantmod`包能处理的数据具有格式上的要求，即必须是 `OHLC` / `OHLCV` / `BBO` / `TBBO` / `HLC` / `quantmod` / `quantmodResults` 中的一种，否则的话，函数没法进行正确的计算。这就要求我们在进行其它操作之前，得先判断我们读进来的数据是否属于上述数据中的一种。逻辑判断类的函数就是干这个活计的。逻辑判断类的函数又能细分为两类，即 `is.*` 类和 `has.*` 类。

#### 2.1.1 is 族函数

`is.*`类函数用来判断数据是否属于某个特定类型。具体用法如下：

* is.OHLC(x):检查是否是OHLC类型数据
* is.OHLCV(x)：检查是否是OHLCV类型数据
* is.BBO(x)：检查是否是BBO类型数据
* is.TBBO(x)：检查是否是TBBO类型数据
* is.HLC(x):检查是否是HLC类型数据
* is.quantmod(x):   
* is.quantmodResults(x):  

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
> is.OHLC(YHOO)
[1] TRUE
> is.OHLCV(YHOO)
[1] TRUE
> is.HLC(YHOO)
[1] TRUE
> is.BBO(YHOO)
[1] FALSE
> is.quantmod(YHOO)
[1] FALSE FALSE
```

#### 2.1.2 has 族函数

has 族函数用来判断数据中是否包含某个维度。

* has.OHLC(x, which = FALSE) 
* has.HLC(x, which = FALSE)
* has.OHLCV(x, which = FALSE)
* has.Op(x, which = FALSE)
* has.Hi(x, which = FALSE)
* has.Lo(x, which = FALSE)
* has.Cl(x, which = FALSE)
* has.Vo(x, which = FALSE)
* has.Ad(x, which = FALSE)
* has.Ask(x, which = FALSE)
* has.Bid(x, which = FALSE)
* has.Price(x, which = FALSE)
* has.Qty(x, which = FALSE)
* has.Trade(x, which = FALSE)

```{r}
> has.OHLC(YHOO)
[1] TRUE TRUE TRUE TRUE
> has.HLC(YHOO)
[1] TRUE TRUE TRUE
> has.OHLCV(YHOO)
[1] TRUE TRUE TRUE TRUE TRUE
```

### 2.2 提取数据的函数
#### 2.2.1 列名函数
* Op(x):提取开盘价
* Hi(x):提取最高价
* Lo(x):提取最低价
* Cl(x):提取收盘价
* Vo(x):提取交易量
* Ad(x):提取调整价格
* HLC(x):提取最高价、最低价和收盘价
* OHLC(x):提取开盘价、最高价、最低价和收盘价

#### 2.2.2 series族函数

* seriesHi(x)：提取开盘价

* seriesLo(x):提取最低价

### 2.3 简单的计算函数
#### 2.3.1 Delt 函数

计算变化率
Delt(x1, x2 = NULL, k = 0, type = c("arithmetic", "log")) 

Stock.Open <- c(102.25,102.87,102.25,100.87,103.44,103.87,103.00)
Stock.Close <- c(102.12,102.62,100.12,103.00,103.87,103.12,105.12)

按开盘价计算收益率

Delt(Stock.Open)

或者

Delt(Stock.Open,k=1)

或者

diff(Stock.Open)/Stock.Open[1:6]

按开盘价计算几何收益率

Delt(Stock.Open,type='log')

计算开盘价与收盘价的差

Delt(Stock.Open,Stock.Close)

等同于

(Stock.Open-Stock.Close)/Stock.Open

计算开盘价与下一期收盘价的差

Delt(Stock.Open,Stock.Close,K=1)

等同于

(Stock.Open[1:6]-Stock.Close[2:7])/Stock.Open[1:6]

#### 2.3.2 其它列计算函数

OpCl(x):等同于Delt(Op(x), Cl(x))

ClCl(x):等同于Delt(Cl(x));等价于diff(Cl(x))/lag(Cl(x))

HiCl(x):等同于Delt(Hi(x),Cl(x))

LoCl(x):等同于Delt(Lo(x),Cl(x))

LoHi(x):等同于Delt(Lo(x),Hi(x))

OpHi(x):等同于Delt(Op(x),Hi(x))

OpLo(x):等同于Delt(OP(x),Lo(x))

OpOp(x):等同于Delt(Op(x))

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

#### 2.5.3 差分阈值函数

* seriesIncr(x, thresh=0, diff.=1L)：差分后大于限值的点
* seriesDecr(x, thresh=0, diff.=1L)：差分后小于限值的点
* endpoints()：寻找节点

### 2.5.4 日期转换函数

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

