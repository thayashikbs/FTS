# 単位根過程と単位根検定



## 単位根過程と見せかけの回帰
### 単位根過程
- ランダムウォークモデル (I(1))のパス生成

```r
#require(sarima)
Tlen <- 100
Seedv <- 1
#
set.seed(Seedv)
#x <- sim_sarima(n = Tlen, model = list(iorder = 1, sigma2 = 1)) # (1-B)X_t = e_t (random walk)
#y <- sim_sarima(n = Tlen, model = list(iorder = 1, sigma2 = 1))
x <- arima.sim(n = Tlen, list(order = c(0, 1, 0)))
y <- arima.sim(n = Tlen, list(order = c(0, 1, 0)))
#
#par(mfrow=c(1,2))
#acf(x)   # 自己相関(ACF)
#pacf(x)	# 偏自己相関(PACF)
#matplot(cbind(x, y), type = "l")
ts.plot(cbind(x, y), lty = 1:2)
```

<img src="040-unitroot_files/figure-html/sim_path-1.png" width="672" />

### 見せかけの回帰

```r
reslm <- lm(y ~ x)
summary(reslm)
```

```
## 
## Call:
## lm(formula = y ~ x)
## 
## Residuals:
##    Min     1Q Median     3Q    Max 
## -9.049 -2.862  1.383  3.172  5.519 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  0.42205    0.68668   0.615     0.54    
## x           -0.51303    0.09909  -5.178 1.18e-06 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 3.752 on 99 degrees of freedom
## Multiple R-squared:  0.2131,	Adjusted R-squared:  0.2051 
## F-statistic: 26.81 on 1 and 99 DF,  p-value: 1.182e-06
```

```r
plot(x, y)
```

<img src="040-unitroot_files/figure-html/spur_reg-1.png" width="672" />

```r
plot(as.numeric(x), as.numeric(y))
abline(reslm)
```

<img src="040-unitroot_files/figure-html/spur_reg-2.png" width="672" />

### 見せかけの回帰の判定

```r
library(lmtest)
```

```
## Loading required package: zoo
```

```
## 
## Attaching package: 'zoo'
```

```
## The following objects are masked from 'package:base':
## 
##     as.Date, as.Date.numeric
```

```r
dwtest(reslm)
```

```
## 
## 	Durbin-Watson test
## 
## data:  reslm
## DW = 0.080227, p-value < 2.2e-16
## alternative hypothesis: true autocorrelation is greater than 0
```
- Durbin-Watson検定 (簡便法)
  - 回帰残差の系列相関の有無を検定
  - $DW \approx 2(1-\rho)$
  - $0<DW<4$.  無相関 $\Leftrightarrow DW=2$
  - 見せかけの回帰の場合. DWが小さい傾向 (正の系列相関)

- 代替法: Engle-Granger検定
- 「見せかけの回帰」については後日扱う

## 主な単位根検定法
- ADF検定: **tseries**内, adf.test(); **fUnitRoots**内, unitrootTest(), adfTest()	
- Phillips-Perron(PP)検定: **urca**内, ur.pp(); **tseries**内, pp.test()
  - PP検定は, 沖本, pp.118--120参照
- KPSS検定: **urca**内, ur.kpss(); **tseries**内, kpss.test()
  - KPSS検定は, 福地・伊藤, pp.139--140参照
  
### Augmented Dicky-Fuller (ADF) 検定の実行例
- **fUnitRoots**パッケージ内, unitrootTest()使用

```
adfTest(): Banerjee's et al.(93)による検定統計量の計算
unitrootTest(): McKinnons(96) のアプローチによる検定統計量の計算
- type: "nc"(定数項・時間トレンド項共なし), "c"(定数項のみ有), "ct"(定数項・時間トレンド項共有)
- lags: 誤差項の持つ最大ラグ数
```

- 上記シミュレーションデータに対して, ADF検定を実行

```r
library(fUnitRoots)
adfTest(x, type = "nc", lags = 1)
```

```
## 
## Title:
##  Augmented Dickey-Fuller Test
## 
## Test Results:
##   PARAMETER:
##     Lag Order: 1
##   STATISTIC:
##     Dickey-Fuller: 0.282
##   P VALUE:
##     0.703 
## 
## Description:
##  Thu Nov 30 12:12:23 2023 by user:
```

```r
unitrootTest(x, type = "nc", lags = 1)
```

```
## 
## Title:
##  Augmented Dickey-Fuller Test
## 
## Test Results:
##   PARAMETER:
##     Lag Order: 1
##   STATISTIC:
##     DF: 0.282
##   P VALUE:
##     t: 0.7657 
##     n: 0.7506 
## 
## Description:
##  Thu Nov 30 12:12:23 2023 by user:
```
- 二つの関数の結果を比較せよ.


```r
adfTest(y, type = "nc", lags = 1)
```

```
## 
## Title:
##  Augmented Dickey-Fuller Test
## 
## Test Results:
##   PARAMETER:
##     Lag Order: 1
##   STATISTIC:
##     Dickey-Fuller: -0.7206
##   P VALUE:
##     0.3835 
## 
## Description:
##  Thu Nov 30 12:12:23 2023 by user:
```

```r
unitrootTest(y, type = "nc", lags = 1)
```

```
## 
## Title:
##  Augmented Dickey-Fuller Test
## 
## Test Results:
##   PARAMETER:
##     Lag Order: 1
##   STATISTIC:
##     DF: -0.7206
##   P VALUE:
##     t: 0.4021 
##     n: 0.522 
## 
## Description:
##  Thu Nov 30 12:12:23 2023 by user:
```
- → いずれも, 帰無仮説(phi_1=1)を棄却せず(単位根有り)


```r
adfTest(x, type = "c", lags = 1)
```

```
## 
## Title:
##  Augmented Dickey-Fuller Test
## 
## Test Results:
##   PARAMETER:
##     Lag Order: 1
##   STATISTIC:
##     Dickey-Fuller: -1.4551
##   P VALUE:
##     0.5166 
## 
## Description:
##  Thu Nov 30 12:12:23 2023 by user:
```

```r
#unitrootTest(x, type = "c", lags = 1)
adfTest(y, type = "c", lags = 1)
```

```
## 
## Title:
##  Augmented Dickey-Fuller Test
## 
## Test Results:
##   PARAMETER:
##     Lag Order: 1
##   STATISTIC:
##     Dickey-Fuller: -1.0635
##   P VALUE:
##     0.6617 
## 
## Description:
##  Thu Nov 30 12:12:23 2023 by user:
```

```r
#unitrootTest(y, type = "c", lags = 1)
```


```r
adfTest(x, type = "c", lags = 1)
```

```
## 
## Title:
##  Augmented Dickey-Fuller Test
## 
## Test Results:
##   PARAMETER:
##     Lag Order: 1
##   STATISTIC:
##     Dickey-Fuller: -1.4551
##   P VALUE:
##     0.5166 
## 
## Description:
##  Thu Nov 30 12:12:23 2023 by user:
```

```r
#unitrootTest(x, type = "ct", lags = 1)
adfTest(y, type = "c", lags = 1)
```

```
## 
## Title:
##  Augmented Dickey-Fuller Test
## 
## Test Results:
##   PARAMETER:
##     Lag Order: 1
##   STATISTIC:
##     Dickey-Fuller: -1.0635
##   P VALUE:
##     0.6617 
## 
## Description:
##  Thu Nov 30 12:12:23 2023 by user:
```

```r
#unitrootTest(y, type = "ct", lags = 1)
```

- 株価データ(**quantmod**パッケージにより取得)
- **tseries**パッケージ内, adf.test()使用

```
adf.test(x, alternative = c("stationary", "explosive"),
    k = trunc((length(x)-1)^(1/3)))
- k: number of lags in the regression.
- default: trunc((length(x)-1)^(1/3)): the suggested upper bound on the rate
- (to grow with the sample size for the general ARMA(p,q) setup)
- alternative: "stationary"(デフォルト) or "explosive"	# 対立仮説の指定
```


```r
library('quantmod')
```

```
## Loading required package: xts
```

```
## Loading required package: TTR
```

```
## Registered S3 method overwritten by 'quantmod':
##   method            from
##   as.zoo.data.frame zoo
```

```r
yj8411 <- getSymbols('8411.T',from = '2020-10-01', 
                     to = '2023-09-29', src = "yahoo", auto.assign = FALSE)	
# 注) R/RStudioや, guantmodのバージョンによっては, 動かないことがある
p8411 <- Ad(yj8411) # 調整後株価
#lnp8411 <- log(Ad(yj8411)) # 同対数値 (← 期間が長い場合など)
#
plot(p8411)
```

<img src="040-unitroot_files/figure-html/unnamed-chunk-3-1.png" width="672" />

```r
#plot(lnp8411)
acf(p8411)
```

<img src="040-unitroot_files/figure-html/unnamed-chunk-3-2.png" width="672" />

```r
pacf(p8411)
```

<img src="040-unitroot_files/figure-html/unnamed-chunk-3-3.png" width="672" />

```r
#acf(diff(p8411), na.action = na.omit)
#pacf(diff(p8411),na.action = na.omit)
```

- 株価データに対してADF検定を実行

```r
# ADF検定 (Augmentd Dicky-Fuller test)
# (H0: x has unit root)
library(tseries)
adf.test(p8411)
adf.test(p8411, k = 1)
#
adfTest(p8411, type ="ct", lags = 1)
# → 同一の結果
#
adf.test(p8411, k = 1, alternative = "explosive")
# <-- H0 と H1を入替 (→ 同一のDF値. 上行のp値 = 1-下行のp値)
```

```
## 
## 	Augmented Dickey-Fuller Test
## 
## data:  p8411
## Dickey-Fuller = -0.72097, Lag order = 9, p-value = 0.9687
## alternative hypothesis: stationary
## 
## 
## 	Augmented Dickey-Fuller Test
## 
## data:  p8411
## Dickey-Fuller = -0.88565, Lag order = 1, p-value = 0.954
## alternative hypothesis: stationary
## 
## 
## Title:
##  Augmented Dickey-Fuller Test
## 
## Test Results:
##   PARAMETER:
##     Lag Order: 1
##   STATISTIC:
##     Dickey-Fuller: -0.8856
##   P VALUE:
##     0.954 
## 
## Description:
##  Thu Nov 30 12:12:23 2023 by user: 
## 
## 
## 	Augmented Dickey-Fuller Test
## 
## data:  p8411
## Dickey-Fuller = -0.88565, Lag order = 1, p-value = 0.04604
## alternative hypothesis: explosive
```

### ADF検定以外の検定法
- Phillips-Perron検定の実行例

```r
#library(tseries)
pp.test(p8411, type = "Z(alpha)", lshort = T)	# デフォルト
```

```
## 
## 	Phillips-Perron Unit Root Test
## 
## data:  p8411
## Dickey-Fuller Z(alpha) = -3.5464, Truncation lag parameter = 6, p-value
## = 0.9096
## alternative hypothesis: stationary
```

```r
pp.test(p8411, type = "Z(alpha)", lshort = F)
```

```
## 
## 	Phillips-Perron Unit Root Test
## 
## data:  p8411
## Dickey-Fuller Z(alpha) = -2.4057, Truncation lag parameter = 19,
## p-value = 0.957
## alternative hypothesis: stationary
```

```r
pp.test(p8411, type = "Z(t_alpha)")
```

```
## 
## 	Phillips-Perron Unit Root Test
## 
## data:  p8411
## Dickey-Fuller Z(t_alpha) = -0.93464, Truncation lag parameter = 6,
## p-value = 0.9493
## alternative hypothesis: stationary
```

```r
#pp.test(p8411, alternative = "explosive")
```


```r
library(urca)
```

```
## 
## Attaching package: 'urca'
```

```
## The following objects are masked from 'package:fUnitRoots':
## 
##     punitroot, qunitroot, unitrootTable
```

```r
#lags: ラグの長さの指定. 4(T/100)^(1/4) or 12(T/100)^(1/4)
#model: トレンドを持つ("trend"), 定数項を持つ("constant")
ur.pp(p8411, type = "Z-alpha", model = "trend", lags = "short")
```

```
## 
## ################################################## 
## # Phillips-Perron Unit Root / Cointegration Test # 
## ################################################## 
## 
## The value of the test statistic is: -3.5414
```

```r
ur.pp(p8411, type = "Z-alpha", model = "trend", lags = "long")
```

```
## 
## ################################################## 
## # Phillips-Perron Unit Root / Cointegration Test # 
## ################################################## 
## 
## The value of the test statistic is: -2.4118
```

```r
ur.pp(p8411, type = "Z-tau", model = "trend", lags = "long")
```

```
## 
## ################################################## 
## # Phillips-Perron Unit Root / Cointegration Test # 
## ################################################## 
## 
## The value of the test statistic is: -0.6917
```

```r
ur.pp(p8411, type = "Z-tau", model = "constant")
```

```
## 
## ################################################## 
## # Phillips-Perron Unit Root / Cointegration Test # 
## ################################################## 
## 
## The value of the test statistic is: 0.8101
```

- KPSS検定の実行例

```r
ur.kpss(p8411, type = "mu", lags = "long")
```

```
## 
## ####################################### 
## # KPSS Unit Root / Cointegration Test # 
## ####################################### 
## 
## The value of the test statistic is: 2.9445
```

```r
ur.kpss(p8411, type = "tau", lags = "long")
```

```
## 
## ####################################### 
## # KPSS Unit Root / Cointegration Test # 
## ####################################### 
## 
## The value of the test statistic is: 0.6989
```
