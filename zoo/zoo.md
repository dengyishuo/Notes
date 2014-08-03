### zoo包笔记

Zeileis在编写strucchange包时需要用到一种不依赖索引类型、并严格排序的时序对象，于是他着手构建了zoo对象和zoo包。这个包的名字来自于Z's ordered observations的首字母。

* 创建zoo对象
* 画图
* 数据合并
* 基本运算
* 提取数据或索引
* 强制转换
* 缺失值处理


时序类型|对象类型|索引类型|R包
---------------|------------|-----------|------------
规则时间序列   |ts          |Date       |stat包
不规则时间序列 |timeSeries  |timeDate   |timeSeries(fSeries)
不规则时间序列 |its         |POSIXct    |its包
不规则时间序列 |irts        |POSIXct    |tseries包
各种           |xts         |           |xts包
各种           |zoo         |           |zoo包

### zoo简介

#### zoo对象的创建

创建zoo对象的命令是；

```
zoo(x,order.by)
```

x是一个包含观测值的向量或者矩阵，order.by是x对应的排序后的索引。索引的长度必须跟x的长度或者行数一致。上述命令得到的zoo对象跟x看起来一样，不同的地方在于多了一个index属性，这个属性保存了索引信息。

理论上，x和order.by的可以是任意类型，但x是数值型的情况比较常见，数值型情况下便于进行数学运算、建模及可视化。

```
library(zoo)
set.seed(1071)

z1.index<-ISOdatetime(2014,rep(1:2,5),sample(28,10),0,0,0);
z1.data<-rnorm(10)
z1<-zoo(z1.data,z1.index)


z2.index<-as.POSIXct(paste(2014,rep(1:2,5),sample(1:28,10),sep="-"))
z2.data<-sin(2*1:10/pi)
z2<-zoo(z2.data,z2.index)

Z.index<-as.Date(sample(12450:12500,10)); 
Z.data<-matrix(rnorm(30),ncol=3);
colnames(Z.data)<-c("Aa","Bb","Cc");
Z<-zoo(Z.data,Z.index);
```

[1] 上面的这个as.Date是zoo包中的，等同于base包中的as.Date(origin="1970-01-01")

上面的创建方法都是举例。而真正操作数据时，通常要处理的数据已经包含了索引，用不着单独创建。



#### zoo对象的结构及显示方式

R中的很多函数，比如print，summary，str，head，tail以及[ 能够对zoo对象进行操作。

要注意的是：向量型的zoo对象默认显示为行向量，而矩阵型的zoo对象默认为上下排列。

```
summary(z1)

summary(Z)
```

#### 创建zooreg对象

zoo对象本身就可以存储规则时间序列，而且stat包中本身也有存储规则时间序列的ts对象。为什么还要创建一个zooreg类型呢？有两个原因：一个是在将zoo对象转为ts对象时需要存储规则时间序列的频率，有了zooreg，可以在zoo对象和ts对象之间自由转换。再一个是因为对于ts对象，一旦出现缺失值，则其不再为规则时间序列，而zooreg可以存储有缺失值的规则时间序列。

zooreg对象是zoo的子对象，它继承了zoo对象的所有特性，并增加了frequency特性。

```
zoo(x,order.by,frequency)
zooreg(data,start,end,frequency,deltat,ts.eps,order.by)
```

在zoo中使用frequency参数时，函数会自动检查frequency跟order.by是否匹配，如果匹配，就返回一个zooreg对象。

zooreg()函数的参数跟ts()函数的参数完全一致。而zooreg(order.by)相当于是zoo(x,order.by,frequency)。

zooreg对象的索引类型有相对严格的要求，其必须能转化为numeric，因为函数要靠转化后的numeric对象来判断索引的规则与否。而且索引必须是1/frequency的整数倍。

```
zr1 <- zooreg(sin(1:9),start=2000,frequency=4)
zr2 <- zoo(sin(1:9),seq(2000,2002,by=1/4),4)
zr1
zr2
```

zooreg对象也能处理非数值型的索引，当然你得保证后续增加的观测值对应的索引类型也是非numerec，否则，索引类型将被强制转换。

```
zooreg(1:5,start=as.Date("2005-01-01"))
```

可以用frequency，deltat，cycle，is.regular(x,stric=FALSE)函数来查看zooreg对象的信息。

```
frequency(zr1)
deltat(zr1)
cycle(zr1)
is.regular(zr1)
is.regular(zr1,strict=TRUE)
```

这些函数对规则的zoo对象也适用。

```
zr1<- as.zoo(zr1)
zr1
class(zr1)
frequency(zr1)
```

当这种时间序列虽然看起来规则，但使用的时候难保不会出现幺蛾子。所以，能保存为zooreg就不要保存为zoo，不作死就不会死。

上面说过，zooreg对象的优点是即使删除一个缺失值，仍然保持规则时间序列的性质。比如：

```
zr1 <- zr1[-c(3,5)]
zr1
class(zr1)
frequency(zr1)
```

与ts对象相比，zooreg在处理缺失值或者时间序列回归方面更胜一筹。像这种有缺失的规则时间序列叫弱规则时间序列，我们将这种时间序列强制转化为ts对象时，缺失值将表示为NA。而规则的时间序列，如果索引刚好是数值型，那么，我们可以在zoo和ts两者之间进行自由转换。

```
as.ts(zr1)
identical(zr2,as.zoo(as.ts(zr2)))
```

因而，我们可以直接对zooreg对象使用acf，arima，stl等函数。

#### zoo对象的绘图

依赖于plot和lines函数。

```{r}
plot(Z)
```

横轴是索引，纵轴是时间序列对应的取值。

也可以将多条时间序列画到同一个面板上。

```{r}
plot(Z,plot.type="single",col=2:4)
```

我们可以指定col，pch等参数。这儿有一个不同之处，即zoo允许我们通过list指定这些参数，并且提供了自动补全功能。比如：

```{r}
plot(Z,type="b",lty=1:3,pch=list(Aa=1,Bb=2,Cc=4),col=list(Bb=2,4),plot.type="single")
```

#### 数据合并

可用rbind函数来对zoo对象进行行合并，此时，合并前的对象必须有相同的列数，并且索引不能有重合。

```
rbind(z1[2:3],z1[5:10])  # 正常行合并

rbind(z1[2:5],z1[5:10])  # 索引重合会报错
```

c函数跟rbind函数的作用相同。
```
c(z1[2:3],z1[5:10])
```
cbind函数可以对zoo对象进行列合并。
```
cbind(z1,z2)
```
这个函数实质上是以两个对象的索引的并集作为结果对象的索引，这样一来会出现缺失值，当然它们的位置会填充为NA。下面的命令与上面的命令结果相同；
```
merge(z1,z2,all=TRUE)
```
但merge可以提供更多的可用参数。比如，可以指定缺失值的填充方式，可以调整合并对象的列名，也可以指定返回结果的类型。

如果merge的对象中有zooreg对象，则merge的结果将是zooreg对象。在这个过程中，merge会在幕后默默的判断时间序列的频率，并检查时间序列规则与否。如果merge的对象中有非zoo对象，那么，merge会毫不犹豫地把zoo对象的索引加到其它对象身上。

如果merge的对象中，索引的类型不一样，R一般会给出警告，并尝试将索引强制转换为一致。我们不鼓励用merge合并索引类型不同的对象，否则，后果自负。

#### 汇总描述

aggregate可以对zoo对象进行汇总。说人话就是zoo会根据某一列的取值将zoo对象切割为几个独立的子集。再对每个子集执行指定的函数。

对于Z来说，我们可以按月份对其其均值。这个过程需要两部，第一步是将Z的索引划分为月份。

```
firstofmonth <- function(x){as.Date(sub("..$","01",format(x)))} 
index(Z)<-firstofmonth(index(Z))    # 将日期统一为当月首日日期
aggregate(Z,index(Z),mean)

aggregate(Z,firstofmonth,head,1)
```

与汇总相反的是数据还原，通常是借助插值来实现。

Nile是ts类型的年度数据，我们可以将其转换为zoo类型的季度数据，并用na.approx，na.locf和na.spline来进行差值。

```
Nile.na<-merge(as.zoo(Nile),zoo(,seq(start(Nile)[1],end(Nile)[1],1/4)))
head(as.zoo(Nile))

head(na.approx(Nile.na))

head(na.locf(Nile.na))

head(na.spline(Nile.na))
```
#### 基本运算

对于维度一样的对象，直接计算即可。对于维度不一样的对象，会先计算对象索引的交集，在根据交集进行相关运算。
```
z1+z2

z1<z2
```
cumsum、cumprod、cummin、cummax等都是对列进行操作。
```
cumsum(Z)
```
也可以计算zoo对象的滞后和差分。

```
lag(z2,k=-1)
merge(z1,lag(z1,k=1))
diff(z1)
```

#### 数据及索引的提取和替换

coredata函数可以提取zoo对象的数据。类似于its包中的core函数及tseries包中的value函数。

```
coredata(z1)

cordata(z1) <- 1:10
```

index函数可以提取zoo对象的索引。

```
index(z2)

index(z2) <- index(z1)
```

time函数与index函数类似。

```
time(z1)
```

start和end函数可以提取第一个和最后一个索引。


抽取zoo对象的子集可以通过[或者window来进行。


```
Z[1:3,2:3]

```

也可以用[加索引来选取Z的子集。

```
z1[ISOdatetime(2004,1,c(14,25),0,0,0)]
```

如果索引的类型是numeric的话，在代码用需要用I来保护，此时相应的代码为Z[I(i)]。或者用window。

window函数的用法如下：

```
window(x,index,start,end)
```

x是zoo对象，index是索引的子集，end和start用来进一步约束索引的子集。

```
window(Z,start=as.Date("2004-03-01"))  # 选出2004-3-1以来的所有观测值
window(Z,index=index(Z)[5:8],end=as.Date("2004-03-01"))  # 选出第5至第8个观测值中，不晚于2004-3-1的观测值
```

也可以用来替换观测值。

```
window(z1,end=as.POSIXct("2004-02-01"))<-9:5
```

#### 类型转化

R中的很多对象都可以跟zoo进行自由转换。尤其是ts，irts，its等对象，很容易就可以转化为zoo对象。如果索引正确，还可以将zoo对象转化回去。zoo对象和zooreg对象之间的转化更方便，无非是增加或者删除frequency属性。除此之外，zoo对象还可以转化为向量、矩阵、列表和数据框，要注意的是转为数据框时索引属性会丢掉，但实质内容还是保留完整的。

```
as.data.frame(Z)
```

#### 缺失值处理

na.omit，na.contiguous，na.approx，na.locf，na.omit和na.spline都能来处理缺失值。na.omit的作用是剔除缺失值。

```
z1[sample(1:10,3)] <- NA
z1

na.omit(z1)
```

na.contiguous函数是返回对象中没有不包含缺失值的最长片段。

```
na.contiguous(z1)
```

na.approx函数是用approx函数对缺失值进行线性差值

```
na.approx(z1)
```

默认情况下na.approx函数基于时间序列的时间跨度进行插值。它跟下面的情况有所不同。

```
na.approx(z1,1:NROW(z1))
```

设置na.approx的第二个参数，可以按照需要对缺失值进行插值。

na.lcof是用前值替代法进行插值。

```
na.locf(z1)
```

#### 滚动计算

处理时间序列是经常用到滚动计算，比如，要滚动计算任意n天的均值、方差等等。zoo包里面提供了rollapply函数来完成这个操作。基本用法是；

```
rollapply(data, width, FUN)
```

这里的data是操作对象，width是滚动的窗口宽度，FUN是对每一个窗口的数据执行的具体函数。比如：

```
rollapply(Z,5,sd)
```

有些观测值被删除了，因为那些观测值没法占满窗口。我们可以通过设置na.pad=TRUE用NA来填充这些位置。

```
rollapply(Z,5,sd,na.pad=TRUE,align="left")
```

填补方式分为左填补、右填补和居中填补。

```
rollapply(Z,5,sd,na.pad=TRUe,align="TRUE")
```

对于一些常用的滚动计算，比如，rollapply(data,width,mean)，rollapply(data,width,max)等，zoo提供了rollmean(x,k)、rollmax(x,k)，rollmedian(x,k)等函数。

```
rollmean(z2,5,na.pad=TRUE)
```

### zoo与其它包的协同

#### strucchange:实证波动过程

strucchange包是用来检验、计量数据或者模型尤其是线性回归模型中的结构变异点的一个包。在检验过程中通常要看模型参数是否随着某个排序变量（通常是时间）的变化而有所改变。在这个过程中，需要用到严格排序的带索引的时间序列。
