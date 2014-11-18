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

buildData()函数可以直接生成模型对应的数据集，其作用相当于是specifyModel+modelData。我们可以一步完成上面的过程。

``` r
> bD=buildData(Next(OpCl(CHL)) ~ Lag(OpHi(CHL),0:3) + Hi(SSEC))
> head(bD)
           Next.OpCl.CHL Lag.OpHi.CHL.0.3.Lag.0 Lag.OpHi.CHL.0.3.Lag.1 Lag.OpHi.CHL.0.3.Lag.2 Lag.OpHi.CHL.0.3.Lag.3 Hi.SSEC
2007-01-09   0.013281816            0.000000000            0.008697642            0.000000000            0.018079096 2809.39
2007-01-10   0.011694511            0.017145617            0.000000000            0.008697642            0.000000000 2841.74
2007-01-11   0.006616257            0.020286396            0.017145617            0.000000000            0.008697642 2841.18
2007-01-12   0.007111723            0.006852552            0.020286396            0.017145617            0.000000000 2782.02
2007-01-16  -0.002941842            0.011699931            0.006852552            0.020286396            0.017145617 2830.80
2007-01-17  -0.005594093            0.002036660            0.011699931            0.006852552            0.020286396 2870.42
```

### 4.3 估计模型参数

有了模型和数据之后，接下来的事是训练模型。训练模型的函数是buildModel。

```
buildModel(x, method, training.per, ...)
```

从最简单的线型模型开始。

``` r
## 简单的线型模型
> bM <- buildModel(q.model,method='lm',training.per=c('2013-08-01','2013-09-30'))
## 显示结果
> bM

quantmod object:   lm1416303379.71439   Build date:  2014-11-18 17:36:19 

Model Specified: 
     Next(OpCl(CHL)) ~ Lag(OpHi(CHL), 0:3) + Hi(SSEC) 

Model Target:  Next.OpCl.CHL             Product:  CHL 
Model Inputs:  Lag.OpHi.CHL.0.3.Lag.0, Lag.OpHi.CHL.0.3.Lag.1, Lag.OpHi.CHL.0.3.Lag.2, Lag.OpHi.CHL.0.3.Lag.3, Hi.SSEC 

Fitted Model: 

        Modelling procedure:  lm 
        Training window:  40  observations from  2013-08-01 to 2013-09-30

Call:
lm(formula = quantmod@model.formula, data = training.data)

Coefficients:
           (Intercept)  Lag.OpHi.CHL.0.3.Lag.0  Lag.OpHi.CHL.0.3.Lag.1  Lag.OpHi.CHL.0.3.Lag.2  Lag.OpHi.CHL.0.3.Lag.3                 Hi.SSEC  
             1.825e-02               1.871e-01              -2.178e-02               1.103e-01               4.478e-02              -8.534e-06  
# 查看更详细的结果
> summary(bM)

quantmod object:   lm1416303379.71439   Build date:  2014-11-18 17:36:19 

Model Specified: 
     Next(OpCl(CHL)) ~ Lag(OpHi(CHL), 0:3) + Hi(SSEC) 

Model Target:  Next.OpCl.CHL             Product:  CHL 
Model Inputs:  Lag.OpHi.CHL.0.3.Lag.0, Lag.OpHi.CHL.0.3.Lag.1, Lag.OpHi.CHL.0.3.Lag.2, Lag.OpHi.CHL.0.3.Lag.3, Hi.SSEC 

Fitted Model: 

        Modelling procedure:  lm 
        Training window:  40  observations from  2013-08-01 to 2013-09-30

Call:
lm(formula = quantmod@model.formula, data = training.data)

Residuals:
      Min        1Q    Median        3Q       Max 
-0.009211 -0.003834 -0.001039  0.002711  0.014566 

Coefficients:
                         Estimate Std. Error t value Pr(>|t|)
(Intercept)             1.825e-02  3.267e-02   0.559    0.580
Lag.OpHi.CHL.0.3.Lag.0  1.871e-01  2.392e-01   0.782    0.440
Lag.OpHi.CHL.0.3.Lag.1 -2.178e-02  2.456e-01  -0.089    0.930
Lag.OpHi.CHL.0.3.Lag.2  1.103e-01  2.525e-01   0.437    0.665
Lag.OpHi.CHL.0.3.Lag.3  4.478e-02  2.628e-01   0.170    0.866
Hi.SSEC                -8.534e-06  1.565e-05  -0.545    0.589

Residual standard error: 0.006079 on 34 degrees of freedom
Multiple R-squared:  0.02997,   Adjusted R-squared:  -0.1127 
F-statistic: 0.2101 on 5 and 34 DF,  p-value: 0.9559
```

### 4.5 模型结果提取

``` r
getModelData(x, na.rm = TRUE)
```

### 4.6 模型回测

tradeModel(bM)

``` e
> tradeModel(bM)

  Model:  lm1416303379.71439 

  C.A.G.R.:  -3.19%     H.P.R.:  -30.86% 

  Returns by period summary:

             weekly monthly quarterly  yearly
    Max.      9.56%   9.70%    20.43%  14.66%
    3rd Qu.   0.96%   2.84%     1.45%  -1.29%
    Mean     -0.06%  -0.29%    -0.86%  -3.89%
    Median   -0.22%  -0.31%    -0.99%  -5.40%
    2rd Qu.  -1.14%  -3.37%    -4.12% -11.30%
    Min.    -10.05% -12.64%   -13.47% -15.08%

  Period to date returns:

             weekly monthly quarterly yearly
             -0.26%  -2.85%    -3.40% -4.76%
```
