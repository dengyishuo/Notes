## 4 金融投资建模

### 4.1 预处理数据

buildData()

``` r
getSymbols("^SSEC");
buildData(Next(OpCl(SSEC)) ~ Lag(OpCl(SSEC)) + I(Lag(OpHi(SSEC))^2))
buildData(Next(OpCl(SSEC)) ~ Lag(OpCl(SSEC)), na.rm=FALSE)
buildData(Next(OpCl(SSEC)) ~ Lag(OpCl(SSEC)), na.rm=FALSE, return.class="ts")
```



### 4.2 设定模型形式

```
specifyModel(formula, na.rm=TRUE)
specifyModel(Next(OpCl(SSEC))~Lag(OpHi(SSEC),0:3)+Hi(SSEC))
q.model = specifyModel(Next(OpCl(SSEC)) ~ Lag(OpHi(SSEC),0:3))
```

### 4.3 估计模型参数

```
buildModel(x, method, training.per, ...)
```

```
bM <- buildModel(q.model,method='lm',training.per=c('2006-08-01','2006-09-30'))
```
### 4.4 模型结果提取

``` r
getModelData(x, na.rm = TRUE)
modelData(m)
```
