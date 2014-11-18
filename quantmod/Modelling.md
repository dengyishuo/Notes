## 4 金融投资建模

### 4.1 设定模型形式

specifyModel函数可以设定统计量化模型的模型形式。

```
specifyModel(formula, na.rm=TRUE)
```

``` r
> getSymbols(c("^SSEC","CHL"))
[1] "SSEC" "CHL" 
> q.model <- specifyModel(Next(OpCl(CHL)) ~ Lag(OpHi(CHL),0:3) + Hi(SSEC))
> q.model

quantmod object:        Build date:   

Model Specified: 
     Next(OpCl(CHL)) ~ Lag(OpHi(CHL), 0:3) + Hi(SSEC) 

Model Target:  Next.OpCl.CHL             Product:  CHL 
Model Inputs:   

Fitted Model: 

        None Fitted
```

### 4.2 查看模型数据

modelData函数可以查看模型设定过程中生成的新数据集。该函数共有三个参数，分别是x,data.window 和 exclude.training。其中，x是specifyModel函数的结果，data.window用来设定数据集的历史区间，exclude.training用来移除训练期间。

比如，可以查看q.model对应的数据集。

```r
> head(modelData(q.model))
           Next.OpCl.CHL Lag.OpHi.CHL.0.3.Lag.0 Lag.OpHi.CHL.0.3.Lag.1 Lag.OpHi.CHL.0.3.Lag.2 Lag.OpHi.CHL.0.3.Lag.3 Hi.SSEC
2007-01-09   0.013281816            0.000000000            0.008697642            0.000000000            0.018079096 2809.39
2007-01-10   0.011694511            0.017145617            0.000000000            0.008697642            0.000000000 2841.74
2007-01-11   0.006616257            0.020286396            0.017145617            0.000000000            0.008697642 2841.18
2007-01-12   0.007111723            0.006852552            0.020286396            0.017145617            0.000000000 2782.02
2007-01-16  -0.002941842            0.011699931            0.006852552            0.020286396            0.017145617 2830.80
2007-01-17  -0.005594093            0.002036660            0.011699931            0.006852552            0.020286396 2870.42
```

### 4.3 构建模型数据

buildData()

``` r
getSymbols("^SSEC");
buildData(Next(OpCl(SSEC)) ~ Lag(OpCl(SSEC)) + I(Lag(OpHi(SSEC))^2))
buildData(Next(OpCl(SSEC)) ~ Lag(OpCl(SSEC)), na.rm=FALSE)
buildData(Next(OpCl(SSEC)) ~ Lag(OpCl(SSEC)), na.rm=FALSE, return.class="ts")
```

### 4.4 估计模型参数

```
buildModel(x, method, training.per, ...)
```

```
bM <- buildModel(q.model,method='lm',training.per=c('2013-08-01','2013-09-30'))
```
### 4.5 模型结果提取

``` r
getModelData(x, na.rm = TRUE)
```

### 4.6 模型回测

tradeModel(bM)
