## 4 金融投资建模

### 4.1 预处理数据

buildData()

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
specifyModel(formula, na.rm=TRUE)
specifyModel(Next(OpCL(CHL))~Lag(OpHi(CHL),0:3)+Hi(CHL))
q.model = specifyModel(Next(OpCl(QQQQ)) ~ Lag(OpHi(QQQQ),0:3))
```

### 4.3 估计模型参数

```
buildModel(x, method, training.per, ...)
```

```
buildModel(q.model,method='lm',training.per=c('2006-08-01','2006-09-30'))
```
