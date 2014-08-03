## quantmod笔记
# 1 获取数据

要想用[quantmod](http://www.quantmod.com)进行数据分析，第一步自然得把数据导入到R里面。将数据导入[R](http://r-project.org)里面的著名的教程是[数据导入与导出](http://cran.r-project.org/doc/manuals/r-release/R-data.pdf)。里面对大部分关于导入和导出数据的内容都讲的很明晰，读者可以直接去看，这里不再赘述。下面只说一下针对[quantmod](http://www.quantmod.com)特有的一些数据导入和导出的做法。

[quantmod](http://www.quantmod.com)中从外部获取数据的途径有三种：

* 从网络上获取数据
* 从数据库读入数据
* 基于`read.table`、`read.csv`等函数读取数据并转化为[quantmod](http://www.quantmod.com)可适应的格式

## 1.1 从网络获取数据

[quantmod](http://www.quantmod.com )包中的`getSymbols()`函数可以从网络上获取数据。我们用`args()`函数看一下它的基本用法。


```r
# 安装并载入quantmod包
install.packages("quantmod")
require(quantmod)
```

```r
> args(getSymbols)
function (Symbols = NULL, env = parent.frame(), reload.Symbols = FALSE, 
    verbose = FALSE, warnings = TRUE, src = "yahoo", symbol.lookup = TRUE, 
    auto.assign = getOption("getSymbols.auto.assign", TRUE), 
    ...) 
NULL
```

`getSymbols()`函数各主要参数的意义如下：

参数|用途
-------|-------
Symbols|指定股票符号或者代码。
env|指定对象的创建位置。
reload.Symbols|是否在指定环境中重新载入数据，缺省设置为FALSE。
warnings|是否输出警告信息，缺省设置为TRUE。
src|指定抓取数据的网址，缺省设置为yahoo，也可以选择google。
symbol.lookup|从外部查找检索股票代码的路径。
auto.assign|是否将函数结构自动载入到工作环境。
file.path|指定文件路径的字符串。
...|其它参数。 

基于`getSymbols()`函数，我们可以从网络上常用的金融数据库中抓取各种金融数据，。目前`getSymbols()`函数支持的数据库包括：

* yahoo
* google
* FRED
* oanda

从上面几个数据库中，我们可以获取上市公司的股票日交易数据、股息数据、拆股数据、财务报表数据、汇市数据、重金属交易数据以及美联储官网公布的一些经济数据。

### 1.1.1 获取股票日交易数据

基于`getSymbols()`函数从很容易从前文提到的几个数据库中获取上市公司股票的日交易数据。比如，我们想获取中国移动通讯公司的日交易数据，我们可以输入下面的代码：

``` r
#例：获取中国移动公司数据
getSymbols("CHL")
```

代码中的`CHL`是中国移动通讯公司在美国市场上的股票缩写码。同样的，我们可以获取苹果公司的日交易数据：

```
#例：获取苹果公司的日交易数据
getSymbols("AAPL")
```

最近，《中国合伙人》很火，这部电影的原型据说是新东方，新东方是在美国纳斯达克上市的一家公司。我们看看新东方的日交易数据：

```
#例：获取新东方公司的日交易数据
getSymbols("EDU")
```

除了上面列举的个股日交易数据之外，我们也可以获取各种指数的日数据。比如，我们可以获取一下上证指数的日数据。上证指数的符号是`^SSEC`，因此获取上证综指可用如下代码：

```
getSymbols("^SSEC")
```

上面几个例子中，我们在获取股票日交易数据之前，得先知道上市公司股票或者指数的缩写码。如果不知道，怎么办？比如，我们想获取上证 A 股指数的日交易数据，或者，我们想要获取代码为 `600635` 的股票的日交易数据，怎么办？此时，我们可以令 `getSymbols()` 函数中的 `symbol="600635.ss"` 即可。

```
# 例子
getSymbols("600635.ss")
```

代码中的`.ss`表示该公司的股票从属于上交所。

上证指数的代码为`000001.ss`。因此，获取上证指数的代码如下：

```
getSymbols("000001.ss")
```

同样，我们可以获取上证 A 股指数（代码为 000002.ss ）、上证B股指数（代码为 000003.ss ）、上证综合指数（代码为 000008.ss ）、沪深300指数（代码为 000300.ss ）、深圳成指（代码为 399001.sz ）、三一重工（代码为 600030.ss ）等。

```
#例：获取上证A股指数
#上证A股指数的代码为 000002.ss
getSymbols("000002.ss")
```

```
#例：获取上证B股指数
#上证B股指数的代码为 000003.ss
getSymbols("000003.ss")
```

```
#例：获取上证综合指数
#上证综合指数的代码为 000008.ss
getSymbols("000008.ss")
```

```
#例：获取沪深300指数
#沪深300指数的代码为000300.ss。
getSymbols("000300.ss")
```

```
#例：深圳成指
#深证成指的代码为399001.sz，其中.sz代表该指数从属于深交所。
getSymbols("399001.sz")
```

```
#例：获取三一重工数据
#三一重工的股票代码为600030.ss。
getSymbols("600030.ss")
```

### 1.1.2 日交易数据的重命名

用前文说过的方法可以方便地获取各种指数和上市公司的日交易数据。然而，由于将 `symbols` 参数等于股票或者指数代码的时候，没有给数据命名。因此，对其引用时十分困难。为了使用数据的方便，我们可以借助`setSymbolLookup()`函数对数据进行重命名。

```r
#例子
setSymbolLookup(A.Share.index=list(name="000002.ss",src="yahoo"))
getSymbols("A.Share.index")
```

```r
#例子
setSymbolLookup(B.Share.index=list(name="000003.ss",src="yahoo"))
getSymbols("B.Share.index")
```

```r
#例子
setSymbolLookup(Conglomerate.index=list(name="000008.ss",src="yahoo"))
getSymbols("Conglomerate.index")
```

```r
#例子
setSymbolLookup(CSI300=list(name="000300.ss",src="yahoo"))
getSymbols("CSI300")
```

```r
#例子
setSymbolLookup(component.index=list(name="399001.sz",src="yahoo"))
getSymbols("component.index")
component.index
```

```r
#例子
setSymbolLookup(SANY.HEAVY=list(name="600030.ss",src="yahoo"))
getSymbols("SANY.HEAVY")
```


### 1.1.3 获取上市公司股息数据

`getDividends`函数可以获取上市公司的股息数据。

```{r}
getDividends("CHL")
```

根据股息调整股票价格

`adjustOHLC`函数可以对股票数据进行除息调整。

```{r}
getSymbols("CHL", from="1990-01-01", src="yahoo")
head(CHL)
head(CHLL.a <- adjustOHLC(CHL))
head(CHL.uA <- adjustOHLC(CHL, use.Adjusted=TRUE))
```

### 1.1.4 获取上市公司的拆股数据

使用`getSplits()`函数获取上市公司的拆股数据：

```
getSplits("MSFT")
```

### 1.1.5 获取上市公司期权交易数据

利用`getOptionChain()`函数可以获取上市公司的期权交易数据：

```
#例：获取苹果公司的期权交易数据
AAPL.OPT <- getOptionChain("AAPL")
AAPL.OPTS <- getOptionChain("AAPL", NULL)
```

与此相关的还有`options.expiry()`函数和`futures.expiry()`函数：

```
#例：
getSymbols("AAPL")
options.expiry(AAPL)
futures.expiry(AAPL)
AAPL[options.expiry(AAPL)]
```

### 1.1.6 获取和查看上市公司的财务报表

[quantmod](http://www.quantmod.com)中`getFinancials()`函数和`getFin()`函数可以获取上市公司的财务报表数据。看看两个函数的用法：
```
args(getFinancials)
args(getFin)
```

例子：获取中国移动公司的财务数据

```
getFinancials('CHL')
```

获取数据之后，可以通过`view.Fin`函数查看财务报表数据:

```
view.Fin(CHL.f)
```

### 1.1.7 从网络获取汇市数据

`getFX()`函数可以帮助我们从[oanda](http://www.onada.com)获取汇率数据。

```
getFX("USD/JPY")
getFX("EUR/USD",from="2005-01-01")
```

也可以：

```
getSymbols("USD/EUR",src="oanda")
getSymbols("USD/EUR",src="oanda",from="2005-01-01")
```
### 1.1.8 获取重金属交易数据

getMetals()函数可以获取重金属的交易数据。

```
getFX(c("gold","XPD"))
getFX("plat",from="2005-01-01")
```

### 1.1.9 获取美联储经济数据

getSymbols.FRED()函数可以获取美联储主页上的美国经济数据。
```
getSymbols('CPIAUCNS',src='FRED')
```

或者：
```
setSymbolLookup(CPIAUCNS='FRED')
getSymbols('CPIAUCNS')
```
## 1.2 从数据库获取股票数据

[quantmod] (http://www.quantmod.com) 除了支持从网络数据库直接抓取数据外，当然也支持从本地数据库读入数据。目前，能支持的数据库类型包括：

* MySQL
* SQLite
* csv
* RData

对应的函数有以下几个：

* getSymbols.MySQL()：从 MySQL 数据库读取数据
* getSymbols.SQLite()：从 SQLite 数据库读取数据
* getSymbols.csv()：从 csv 文件读取 OHLC 数据
* getSymbols.rda()：读取以 .r 格式存储的数据


## 1.3 查看和移除股票数据

### 1.3.1 查看股票数据
```
getSymbols("CHL","000023.ss")
showSymbols(env=.GlobalEnv)
```
结果如下：

### 1.3.2 移除股票数据
```
RemoveSymbols("CHL")
showSymbols(env=.GlobalEnv)
```

```
getQuote("AAPL")
getQuote("QQQQ;SPY;^VXN",what=yahooQF(c("Bid","Ask")))
standardQuote()
yahooQF()
```

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

## 3.图形分析

### 3.1 基本画图函数

#### chartSeries

`chartSeries()`函数是`quantmod`包的主绘图函数。
`
args(chart_Series)
`
该函数的主要参数有：

* x：时序数据
* type:画图的类型
* TA：技术分析指标
* pars:图形参数
* theme:主题

#### reChart

reChart()是一个重新绘图函数。

### 3.2 三种基本图形
#### 条形图
```
barChart(CHL)
barChart(CHL,theme="white")
```
#### 蜡烛图
```
candleChart(CHL)
candleChart(CHL,multi.col=T,theme="white")
```
#### 线图
```
lineChart(CHL)
lineChart(CHL,theme="white")
```

### 3.3 技术分析图
```
require(TTR)
```
* 平均趋向指标ADX

```
addADX()
```

* 平均真实波幅指标ATR

```
addATR()
```

* 布林线指标BBands

```
addBBands()
```

* 顺势指标CCI

```
addCCI()
```

* Chaikin资金流量指标CMF

```
addCMF()
```

* Chande动量摆动指标CMO

```
addCMO()
```

* 指数平均数指标EMA

```
addEMA()
```

* 包络线指标Envelope

```
addEnvelope()
```

* 弹性成交量加权移动平均线指标EVWMA

```
addEVWMA ()
```

* 移动平均收敛发散指标MACD

```
addMACD ()
```

* 动量指标Momentum

```
addMomentum ()
```

* 合约终止线Expiry

```
addExpiry()
```

* 抛物线指标SAR

```
addSAR()
```

* 简单移动平均指标SMA

```
addSMA()
```

* 随机动量指标SMI

```
addSMI()
```

* 双指数移动平均指标DEMA

```
addDEMA()
```

* 区间震荡线DPO

```
addDPO()
```

* 变动率指标ROC

```
addROC ()
```

* 相对强弱指标RSI

```
addRSI()
```

* 交易量指标Vo

```
addVo()
```

* 加权移动平均线指标WMA

```
addWMA()
```

* 威廉指标WPR

```
addWPR()
```

* 零滞后指数移动平均ZLEMA

```
addZLEMA()
```

* 辅助函数
  * addTA()
  * newTA()
  * setTA(type = c("chartSeries", "barChart", "candleChart"))
  * listTA(dev)

### 3.4 修饰图形

#### 设置背景颜色

`chartTheme()`函数可以设定图形颜色：

* fg.col:foreground color
* bg.col:background color
* grid.col:grid color
* border:border color
* minor.tick:minor tickmark color
* major.tick:major tickmark color
* up.col:up bar/candle color
* dn.col:down bar/candle color
* up.up.col:up after up bar/candle color
* up.dn.col:up after down bar/candle color
* dn.dn.col:down after down bar/candle color
* dn.up.col:down after up bar/candle color
* up.border:up bar/candle border color
* dn.border:down bar/candle border color
* up.up.border:up after up bar/candle border color
* up.dn.border:up after down bar/candle border color
* dn.dn.border:down after down bar/candle border color
* dn.up.border:down after up bar/candle border color 

### 3.5 图形缩放

```
zooom(n=1, eps=2)
zoomChart(subset, yrange=NULL)
```

### 3.6  图形存储

```
getSymbols("AAPL")
chartSeries(AAPL)
require(TTR)
addBBands()
saveChart('pdf')
saveChart('pdf', width=13)
```

## 4 金融建模

### 4.1 建模准备

`buildData()`

``` r
buildData(Next(OpCl(DIA)) ~ Lag(TBILL) + I(Lag(OpHi(DIA))^2))
buildData(Next(OpCl(DIA)) ~ Lag(TBILL), na.rm=FALSE)
buildData(Next(OpCl(DIA)) ~ Lag(TBILL), na.rm=FALSE, return.class="ts")
```

``` r
getModelData(x, na.rm = TRUE)
modelData(m)
```

### 4.2 设定模型形式
```
buildModel(x, method, training.per, ...)
```

```
getSymbols('QQQQ',src='yahoo')
q.model = specifyModel(Next(OpCl(QQQQ)) ~ Lag(OpHi(QQQQ),0:3))
buildModel(q.model,method='lm',training.per=c('2006-08-01','2006-09-30'))
```

```
specifyModel(formula, na.rm=TRUE)
specifyModel(Next(OpCL(CHL))~Lag(OpHi(CHL),0:3)+Hi(CHL))
```

### 4.3 估计模型参数


attachSymbols()
```
