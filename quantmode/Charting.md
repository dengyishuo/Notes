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
