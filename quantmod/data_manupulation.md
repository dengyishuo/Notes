## 2 基本数据操作：清洗、转换和抽取

任何数据分析过程都离不开数据操作，金融数据分析也不例外。这里就重点说一说quantmod包中与数据操作相关的一些函数及其用法。quantmod包中的函数大致可以归为这么几类：逻辑判断类、数据提取类、时期转换类、计算类、其它类。下面分类介绍一下这些函数，说明这些函数存在的原因以及具体用法。

### 2.1 逻辑判断函数

为什么要有逻辑判断类呢？因为quantmod包能处理的数据具有格式上的要求，即必须是OHLC、OHLCV、BBO、TBBO、HLC、quantmod、quantmodResults 中的一种，否则的话，函数没法进行正确的计算。这就要求我们在进行其它操作之前，得先判断我们读进来的数据是否属于上述数据中的一种。逻辑判断类的函数就是干这个活的。逻辑判断类的函数又能细分为两类，即 is 类和 has 类。

#### 2.1.1 is 族函数

is类函数用来判断数据是否属于某个特定类型。具体用法如下：

函数                 |用途
---------------------|------------------
is.OHLC(x)           |检查是否是OHLC类型数据
is.OHLCV(x)          |检查是否是OHLCV类型数据
is.BBO(x)            |检查是否是BBO类型数据
is.TBBO(x)           |检查是否是TBBO类型数据
is.HLC(x)            |检查是否是HLC类型数据
is.quantmod(x)       |检查是否是quantmod对象
is.quantmodResults(x)|检查是否是quatmodResults对象

举个例子。我们从网上获取 yahoo 公司的股票数据：

```{r}
> getSymbols("YHOO")
> YHOO["2013-10-01::"]

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

函数                 |用途
---------------------|------------------
has.OHLC(x, which = FALSE)     |检查是否有open,high,low,close列或者字段
has.HLC(x, which = FALSE)      |检查是否有high,low,close列或者字段
has.OHLCV(x, which = FALSE)    |检查是否有open,high,low,close,volume列或者字段
has.Op(x, which = FALSE)       |检查是否有open列或者字段
has.Hi(x, which = FALSE)       |检查是否有high列或者字段
has.Lo(x, which = FALSE)       |检查是否有low列或者字段
has.Cl(x, which = FALSE)       |检查是否有close列或者字段
has.Vo(x, which = FALSE)       |检查是否有volume列或者字段
has.Ad(x, which = FALSE)       |检查是否有adjust列或者字段
has.Ask(x, which = FALSE)      |检查是否有ask列或者字段
has.Bid(x, which = FALSE)      |检查是否有bid列或者字段
has.Price(x, which = FALSE)    |检查是否有price列或者字段
has.Qty(x, which = FALSE)      |检查是否Qty列或者字段
has.Trade(x, which = FALSE)    |检查是否有trade列或者字段

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

### 2.2 数据提取

建模过程中要对数据进行各种转换和计算，而转换和计算的前提是能够先抽取目标数据。quantmod 提供了专门提取列数据和数据子集的函数。

#### 2.2.1 提取列数据

函数     |用途
---------|----------
Op(x)    |提取开盘价
Hi(x)    |提取最高价
Lo(x)    |提取最低价
Cl(x)    |提取收盘价
Vo(x)    |提取交易量
Ad(x)    |提取调整价格
HLC(x)   |提取最高价、最低价和收盘价
OHLC(x)  |提取开盘价、最高价、最低价和收盘价

```{r}
## 提取开盘价
> Op(YHOO)


## 提取最高价
> Hi(YHOO)


## 提取最低价
> Lo(YHOO)


## 提取收盘价
> Cl(YHOO)


## 提取成交量
> Vo(YHOO)


## 提取复权价格
> Ad(YHOO)


## 提取最高价、最低价和收盘价
> HLC(YHOO)


## 提取开盘价、最高价、最低价和收盘价
> OHLC(YHOO)
```

#### 2.2.2 提取子集

* first(x,k)：求前k个
* last(x,k):求后k个
* []

``` {r}
# 截取第一个数据
> first(YHOO)


#截取前5个数据
> first(YHOO,5)


# 截取前三周的数据
> first(YHOO,'3 weeks')


# 截取最后三周的头部数据
> head(last(YHOO,'-3 weeks'))
          

# 截取前两周中最后三天的数据
> last(first(YHOO, '2 weeks'), '3 days')
          

# 截取2007年的数据
> head(YHOO['2007'])


# 截取2009年的数据
> head(YHOO['2009'])
          

# 截取2009年1月的数据
> head(YHOO['2009-01'])
          

# 截取2009年1月至8月的数据
> head(YHOO['2009-01::2009-08'])
     

# 2009年1月以来的数据
> head(YHOO['2009-01::'])
        

```

### 2.3 数据转换

#### 2.3.1 滞后和提前算子

* Lag(x,k):求滞后k期
* Next(x, k = 1)：求k期

lag 函数的作用是将数据滞后 k 期。比如，下面的例子中，2007-01-03 的取值 25.85 被滞后一期到 2007-01-04。或者说，lag(Op(YHOO)) 对象中的 2007-01-04 对应的 是Op(YAHO) 的 2007-01-03 的对象。

``` {r}
> head(Op(YHOO))
       

## 一阶滞后
> head(lag(Op(YHOO)))
        
## 多阶滞后
> head(lag(Op(YHOO),c(1,3,5)))
          
```

提前函数的作用于lag函数的作用刚好相反。看下面的例子：

``` {r}
## 提前函数
> Next(Op(YHOO))

```

#### 2.3.2 变动比率的计算

Delt 函数的作用是计算变化率或者变动比率。基本用法如下：

``` {r}
Delt(x1, x2 = NULL, k = 0, type = c("arithmetic", "log")) 
```

```{r}
Stock.Open <- c(102.25,102.87,102.25,100.87,103.44,103.87,103.00)
Stock.Close <- c(102.12,102.62,100.12,103.00,103.87,103.12,105.12)

## 按开盘价计算收益率

> Delt(Stock.Open)

```

或者:

```{r}
> Delt(Stock.Open,k=1)

```

等同于：

``` {r}
> diff(Stock.Open)/Stock.Open[1:6]

```

也即是计算（T日收盘价-T-1日收盘价）/T-1日收盘价，对应的金融意义是算术收益率。当然，也可以计算几何收益率或者对数收益率，只需将 type 参数设定为 log 即可。

``` {r}
> Delt(Stock.Open,type='log')

```

等同于：

``` {r}
> diff(log(Stock.Open))

```

除此之外还可以两列数据之间的变动比例。比如：

``` {r}
> Delt(Stock.Open,Stock.Close)
  
```

等同于:

``` {r}
> (Stock.Open-Stock.Close)/Stock.Open

```

对应的经济学意义是股票收盘价相对于开盘价的变动幅度。

计算开盘价与下一期收盘价的差

``` {r}
> Delt(Stock.Open,Stock.Close,k=1)

```

等同于：

``` {r}
> (Stock.Open[1:6]-Stock.Close[2:7])/Stock.Open[1:6]

```

#### 2.3.3 列计算

函数   |用途说明
-------|-----------------
OpCl(x)|等同于Delt(Op(x), Cl(x))
ClCl(x)|等同于Delt(Cl(x)),等价于diff(Cl(x))/lag(Cl(x))
HiCl(x)|等同于Delt(Hi(x),Cl(x))
LoCl(x)|等同于Delt(Lo(x),Cl(x))
LoHi(x)|等同于Delt(Lo(x),Hi(x))
OpHi(x)|等同于Delt(Op(x),Hi(x))
OpLo(x)|等同于Delt(OP(x),Lo(x))
OpOp(x)|等同于Delt(Op(x))

```{r}
> head(YHOO)
        

## 计算涨跌
> head(OpCl(YHOO))


## 计算收益率
> head(ClCl(YHOO))
      

## 计算收盘价对最高价的变动比率
> head(HiCl(YHOO))


## 计算收盘价对最低价的变动比率
> head(LoCl(YHOO))


## 计算振幅
> head(LoHi(YHOO))


## 计算最高价对收盘价的变动比率
> head(OpHi(YHOO))


## 计算最低价对开盘价的变动比率
> head(OpLo(YHOO))


## 计算两日收盘价的变动比率
> head(OpOp(YHOO))

```

### 2.3.4 收益率计算

计算收益率的主要函数有：

函数              |用途
------------------|------------
periodReturn()    |计算阶段收益率
dailyReturn()     |计算日收益率
weeklyReturn)     |计算周收益率
monthlyReturn()   |计算月收益率
quarterlyReturn() |计算季度收益率
annualReturn()    |计算年度收益率
yearlyReturn()    |计算年收益率
allReturns()      |计算各期收益率

举几个例子：

```{r}
#求日收益率
> periodReturn(YHOO,period='daily')

```

或者

``` {r]
> dailyReturn(YHOO)

```

```{r}
#求周收益率
> periodReturn(YHOO,period='weekly')

```

或者

``` {r}
> weeklyReturn(YHOO)


```

```{r}
#求月收益率
periodReturn(YHOO,period='monthly')

```

或者

```{r}
monthlyReturn(YHOO)

```

``` {r}
#求季度度收益率
periodReturn(YHOO,period='quarterly')

```

或者

``` {r}
> quarterlyReturn(YHOO)

```
### 2.3.5 其它函数

### 2.5.5 日期转换函数

* to.weekly()：将OHLC数据转化为周数据
* to.monthly():将PHLC数据转化为月数据
* periodicity()：返回数据的日期范围

```{r}
#例子

> periodicity(YHOO)

> tail(to.weekly(YHOO))

> tail(to.monthly(YHOO))

> endpoints(YHOO,on="months")

> YHOO[endpoints(YHOO,on="months")]

```

### 2.5 其它常用函数

#### 2.5.1 峰值、峰谷函数

* findPeaks(x, thresh=0)：找峰值
* findValleys(x, thresh=0)：找谷底值

```{r}
## 返回峰值对应的序号
> head(findPeaks(Op(YHOO)))

> head(findValleys(Op(YHOO)))
```
#### 2.5.2 series族函数

* seriesHi(x)：提取一段时序的最高价
* seriesLo(x):提取一段时序的最低价

```{r}
> seriesHi(Op(YHOO))


> seriesLo(Op(YHOO))

```

#### 2.5.3 涨跌判断函数

* seriesIncr(x, thresh=0, diff.=1L)：上涨
* seriesDecr(x, thresh=0, diff.=1L)：下跌
* endpoints()：寻找节点

``` {r}
> head(seriesIncr(Op(YHOO)))

> head(seriesDecr(Op(YHOO)))

```

