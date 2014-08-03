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

