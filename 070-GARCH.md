# GARCHモデル

## GARCHモデル
### GARCH(p,q)モデルのサンプルパス生成
- ボラティリティ方程式 $$ \sigma_t^2=\omega+\alpha_1 u_{t-1}^2+\cdots+\alpha_p u_{t-p}^2+\beta_1 \sigma_{t-1}^2+\cdots+\beta_q \sigma_{t-q}^2$$
- パッケージ**fGarch**
- https://www.rdocumentation.org/packages/fGarch/versions/3042.83.2/topics/garchSim

#### ARCH(p)モデル
- パラメータ設定例
  - ボラティリティ方程式 ($p=2, q=0$): $\omega=10^{-6}$ (デフォルト), $\alpha_1=0.5, \alpha_2=0.1$

```r
Seedv <- 10
Tlen <- 300
library(fGarch)
```

```
## NOTE: Packages 'fBasics', 'timeDate', and 'timeSeries' are no longer
## attached to the search() path when 'fGarch' is attached.
## 
## If needed attach them yourself in your R script by e.g.,
##         require("timeSeries")
```

```r
# ARCH(2) - use default omega and specify alpha, set beta=0!
spec <- garchSpec(model = list(alpha = c(0.5, 0.1), beta = 0))
set.seed(Seedv)
x <- garchSim(spec, n = Tlen)
plot(x)
```

<img src="070-GARCH_files/figure-html/ARCH2-1.png" width="672" />

```r
par(mfrow = c(1, 2))
acf(x); pacf(x)
```

<img src="070-GARCH_files/figure-html/ARCH2-2.png" width="672" />

```r
acf(x ^ 2); pacf(x ^ 2)
```

<img src="070-GARCH_files/figure-html/ARCH2-3.png" width="672" />

```r
acf(abs(x)); pacf(abs(x))
```

<img src="070-GARCH_files/figure-html/ARCH2-4.png" width="672" />

#### GARCH(p,q)モデル
- パラメータ設定例
  - ボラティリティ方程式 (次数$p=2,q=1$): $\omega=10^{-6}$(デフォルト), $\alpha_1=0.5, \alpha_2=0.1, \beta_1=0.35$

```r
spec <- garchSpec(model = list(alpha = c(0.5, 0.1), beta = 0.35))
set.seed(Seedv)
x <- garchSim(spec, n = Tlen)
plot(x)
```

<img src="070-GARCH_files/figure-html/GARCH1.1-1.png" width="672" />

```r
par(mfrow = c(1, 2))
acf(x); pacf(x)
```

<img src="070-GARCH_files/figure-html/GARCH1.1-2.png" width="672" />

```r
acf(x ^ 2); pacf(x ^ 2)
```

<img src="070-GARCH_files/figure-html/GARCH1.1-3.png" width="672" />

```r
acf(abs(x)); pacf(abs(x))
```

<img src="070-GARCH_files/figure-html/GARCH1.1-4.png" width="672" />

#### ARMA(p',q')-GARCH(p,q)モデル
- パラメータ設定例
  - 平均方程式 ($p'=1,q'=2$): $\phi_1=0.7,\theta_1=0.3,\theta_2=-0.3$
  - ボラティリティ方程式 ($p=1,q=1$): $\omega=10^{-6}$(デフォルト), $\alpha_1=0.5, \alpha_2=0.1, \beta_1=0.35$

```r
spec <- garchSpec(model = list(ar = 0.7, ma = c(0.3, -0.3), 
                               alpha = c(0.5, 0.1), beta = 0.35))
set.seed(Seedv)
x <- garchSim(spec, n = Tlen)
plot(x)
```

<img src="070-GARCH_files/figure-html/ARMA1.2_GARCH1.1-1.png" width="672" />

```r
par(mfrow = c(1, 2))
acf(x); pacf(x)
```

<img src="070-GARCH_files/figure-html/ARMA1.2_GARCH1.1-2.png" width="672" />

```r
acf(x ^ 2); pacf(x ^ 2)
```

<img src="070-GARCH_files/figure-html/ARMA1.2_GARCH1.1-3.png" width="672" />

```r
acf(abs(x)); pacf(abs(x))
```

<img src="070-GARCH_files/figure-html/ARMA1.2_GARCH1.1-4.png" width="672" />

## GARCHモデルの推定
- garchFit関数: 1変量ARMA-GARCHモデルを適合
  - 最尤法
  - ARMAおよびGARCHのモデル次数を引数に与える (デフォルト, GARCH(1,1))
- 参考:
  - 福地・伊藤(2011). Rによる計量経済分析. Sec.8.3
    - https://sites.google.com/site/econometricsr/
  - Tsay, Ch.4

```r
data("dem2gbp") # DEM/GBP日次対数リターン,Jan/31984〜Dec/31/1991 (1974件)
x <- dem2gbp; Tlen = nrow(dem2gbp)
ts.plot(x)
```

<img src="070-GARCH_files/figure-html/est.1-1.png" width="672" />

```r
par(mfrow = c(1, 2))
acf(x); pacf(x, main = "dem2gbp")
```

<img src="070-GARCH_files/figure-html/est.1-2.png" width="672" />

```r
acf(x ^ 2, main = "dem2gbp^2"); pacf(x ^ 2, main = "dem2gbp^2")
```

<img src="070-GARCH_files/figure-html/est.1-3.png" width="672" />

- ARCH効果の検定
  - 例. ラグ$p=5$を指定した場合

```r
library("FinTS")
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
ArchTest(unlist(x), lags = 5, demean = TRUE) # xは数値ベクトルであること
```

```
## 
## 	ARCH LM-test; Null hypothesis: no ARCH effects
## 
## data:  unlist(x)
## Chi-squared = 182.43, df = 5, p-value < 2.2e-16
```

→  帰無仮説 ($\alpha_1=  \ldots=\alpha_{5}=0$) を棄却

- 例. ARCH(5)モデルの適合

```r
fit.ARCH <- garchFit(formula = ~ garch(5, 0), data = x,
                    trace = FALSE, include.mean = TRUE)
summary(fit.ARCH)
```

```
## 
## Title:
##  GARCH Modelling 
## 
## Call:
##  garchFit(formula = ~garch(5, 0), data = x, include.mean = TRUE, 
##     trace = FALSE) 
## 
## Mean and Variance Equation:
##  data ~ garch(5, 0)
## <environment: 0x119da6240>
##  [data = x]
## 
## Conditional Distribution:
##  norm 
## 
## Coefficient(s):
##          mu        omega       alpha1       alpha2       alpha3       alpha4  
## -0.00056115   0.07923997   0.24684994   0.14580250   0.08569016   0.08462344  
##      alpha5  
##  0.12554184  
## 
## Std. Errors:
##  based on Hessian 
## 
## Error Analysis:
##          Estimate  Std. Error  t value Pr(>|t|)    
## mu     -0.0005612   0.0087236   -0.064  0.94871    
## omega   0.0792400   0.0065155   12.162  < 2e-16 ***
## alpha1  0.2468499   0.0344795    7.159 8.11e-13 ***
## alpha2  0.1458025   0.0332822    4.381 1.18e-05 ***
## alpha3  0.0856902   0.0261306    3.279  0.00104 ** 
## alpha4  0.0846234   0.0280618    3.016  0.00256 ** 
## alpha5  0.1255418   0.0286409    4.383 1.17e-05 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Log Likelihood:
##  -1118.366    normalized:  -0.5665483 
## 
## Description:
##  Thu Dec 21 22:47:18 2023 by user:  
## 
## 
## Standardised Residuals Tests:
##                                 Statistic p-Value  
##  Jarque-Bera Test   R    Chi^2  817.1006  0        
##  Shapiro-Wilk Test  R    W      0.9662239 0        
##  Ljung-Box Test     R    Q(10)  10.09528  0.432174 
##  Ljung-Box Test     R    Q(15)  17.39533  0.2957857
##  Ljung-Box Test     R    Q(20)  19.36413  0.498283 
##  Ljung-Box Test     R^2  Q(10)  11.95177  0.2882973
##  Ljung-Box Test     R^2  Q(15)  20.96312  0.1380119
##  Ljung-Box Test     R^2  Q(20)  23.97484  0.2434931
##  LM Arch Test       R    TR^2   11.548    0.4826274
## 
## Information Criterion Statistics:
##      AIC      BIC      SIC     HQIC 
## 1.140189 1.160004 1.140164 1.147469
```

```r
resd = residuals(fit.ARCH, standardize = T)
par(mfcol = c(3, 1))
ts.plot(resd)
acf(resd); pacf(resd ^ 2)
```

<img src="070-GARCH_files/figure-html/est.3-1.png" width="672" />

- 例. GARCH(1,1)モデルの適合

```r
fit.GARCH <- garchFit(formula = ~ garch(1, 1), data = x,
                     trace = FALSE, include.mean = TRUE)
summary(fit.GARCH)
```

```
## 
## Title:
##  GARCH Modelling 
## 
## Call:
##  garchFit(formula = ~garch(1, 1), data = x, include.mean = TRUE, 
##     trace = FALSE) 
## 
## Mean and Variance Equation:
##  data ~ garch(1, 1)
## <environment: 0x106ed0ec0>
##  [data = x]
## 
## Conditional Distribution:
##  norm 
## 
## Coefficient(s):
##         mu       omega      alpha1       beta1  
## -0.0061903   0.0107614   0.1531341   0.8059737  
## 
## Std. Errors:
##  based on Hessian 
## 
## Error Analysis:
##         Estimate  Std. Error  t value Pr(>|t|)    
## mu     -0.006190    0.008462   -0.732 0.464447    
## omega   0.010761    0.002838    3.793 0.000149 ***
## alpha1  0.153134    0.026422    5.796  6.8e-09 ***
## beta1   0.805974    0.033381   24.144  < 2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Log Likelihood:
##  -1106.608    normalized:  -0.5605916 
## 
## Description:
##  Thu Dec 21 22:47:18 2023 by user:  
## 
## 
## Standardised Residuals Tests:
##                                 Statistic p-Value  
##  Jarque-Bera Test   R    Chi^2  1059.851  0        
##  Shapiro-Wilk Test  R    W      0.9622848 0        
##  Ljung-Box Test     R    Q(10)  10.12141  0.4299066
##  Ljung-Box Test     R    Q(15)  17.04349  0.3162711
##  Ljung-Box Test     R    Q(20)  19.29764  0.5025619
##  Ljung-Box Test     R^2  Q(10)  9.062555  0.5261773
##  Ljung-Box Test     R^2  Q(15)  16.07769  0.3769072
##  Ljung-Box Test     R^2  Q(20)  17.50715  0.6198388
##  LM Arch Test       R    TR^2   9.771217  0.6360238
## 
## Information Criterion Statistics:
##      AIC      BIC      SIC     HQIC 
## 1.125236 1.136559 1.125228 1.129396
```

```r
coef(fit.GARCH)
```

```
##           mu        omega       alpha1        beta1 
## -0.006190317  0.010761385  0.153134063  0.805973739
```

```r
resd <- residuals(fit.GARCH, standardize = T)  # 標準化残差
tail(resd, 10)
```

```
##  [1]  1.39586970 -0.78568121 -0.11839598  0.34278825 -1.35175439 -1.06335161
##  [7] -0.06248781 -0.30590821 -0.65122451  1.57675563
```

```r
par(mfcol = c(3, 1))
ts.plot(resd)
acf(resd); pacf(resd ^ 2)
```

<img src="070-GARCH_files/figure-html/est.4-1.png" width="672" />

- → 残差の系列相関は概ね消滅, 非正規性残る→ 改善余地有り
  - Tsay, Ch.4を参照のこと

- ボラティリティ推定値 (内挿予測)

```r
# ボラティリティ推定値
vola <- volatility(fit.GARCH)  # 標準偏差表示
tail(vola, 10)
```

```
##  [1] 0.3659949 0.3983608 0.3919991 0.3673416 0.3492097 0.3783785 0.3885138
##  [8] 0.3640161 0.3456269 0.3388205
```

```r
par(mfcol = c(2, 1))
ts.plot(x); ts.plot(vola)
```

<img src="070-GARCH_files/figure-html/est.5-1.png" width="672" />

- $l$期先予測 (外挿予測)

```r
# n.ahead(=20)期先予測
# nx:表示する観測データ数
par(mfrow = c(1,1))
# 収益率系列xの予測 (& 95%信頼区間) 
x.pred <- predict(fit.GARCH, n.ahead = 20, plot = TRUE, conf = .95, nx = 300) 
```

<img src="070-GARCH_files/figure-html/est.6-1.png" width="672" />

```r
# ボラティリティのl期先予測値 (標準偏差表示)
(v.pred <- x.pred[, "standardDeviation"])
```

```
##  [1] 0.3833961 0.3895421 0.3953471 0.4008358 0.4060303 0.4109507 0.4156151
##  [8] 0.4200402 0.4242410 0.4282312 0.4320237 0.4356301 0.4390612 0.4423269
## [15] 0.4454366 0.4483989 0.4512218 0.4539128 0.4564788 0.4589264
```

```r
# プロット (実績値&予測値)
v.pred <- c(vola[(Tlen - 99):Tlen], v.pred) #; length(v.pred)
plot(v.pred, type = "l", ylab = "vola")
abline(v = 101, col = "red", lty = "dotted")
```

<img src="070-GARCH_files/figure-html/est.6-2.png" width="672" />

- GARCH(1,1)モデルの$l$-期先ボラティリティ予測値
  - $T$: データ期間 (モデル推定に使用) の最終時点 (予測の起点)
  - $u_T, \sigma_T^2$: モデル適合による時点$T$における推定値 (所与)
  - $l=1$のケース: $$\sigma_T^2 (1) = \omega + \alpha_1 u_T^2 + \beta_1 \sigma_T^2$$
  - $l\ge2$のケース: $$\sigma_T^2 (l) = \omega \frac{1-(\alpha_1 + \beta_1)^{l-1}}{1-(\alpha_1 + \beta_1)} + (\alpha_1 + \beta_1)^{l-1} \sigma_T^2 (1)$$
  - 参考: Tsay, pp.200-201

- 代替パッケージ: **rugarch**

```r
library(rugarch)
```

```
## Loading required package: parallel
```

```
## 
## Attaching package: 'rugarch'
```

```
## The following object is masked from 'package:stats':
## 
##     sigma
```

```r
spec <- ugarchspec(variance.model = list(garchOrder = c(1, 1))) # モデルの特定
fit.GARCH <- ugarchfit(data = x, spec = spec)                   # モデルの適合
(x.pred <- ugarchforecast(fitORspec = fit.GARCH, n.ahead = 20)) #  収益率系列, ボラティリティの予測 (→ x.predに2系列を格納)
```

```
## 
## *------------------------------------*
## *       GARCH Model Forecast         *
## *------------------------------------*
## Model: sGARCH
## Horizon: 20
## Roll Steps: 0
## Out of Sample: 0
## 
## 0-roll forecast [T0=1975-05-29 09:00:00]:
##          Series  Sigma
## T+1   0.0272008 0.3850
## T+2  -0.0197455 0.3915
## T+3  -0.0005006 0.3976
## T+4  -0.0083897 0.4034
## T+5  -0.0051557 0.4088
## T+6  -0.0064815 0.4139
## T+7  -0.0059380 0.4187
## T+8  -0.0061608 0.4233
## T+9  -0.0060694 0.4276
## T+10 -0.0061069 0.4317
## T+11 -0.0060915 0.4356
## T+12 -0.0060978 0.4393
## T+13 -0.0060952 0.4427
## T+14 -0.0060963 0.4460
## T+15 -0.0060959 0.4492
## T+16 -0.0060960 0.4522
## T+17 -0.0060960 0.4550
## T+18 -0.0060960 0.4577
## T+19 -0.0060960 0.4603
## T+20 -0.0060960 0.4627
```

```r
# 別々に表示
#fitted(x.pred)            # 収益率系列の予測値
#x.pred@forecast$seriesFor  # 同
sigma(x.pred)             # ボラティリティ(条件付標準偏差)の予測値
```

```
##      1975-05-29 09:00:00
## T+1            0.3850257
## T+2            0.3915075
## T+3            0.3976065
## T+4            0.4033520
## T+5            0.4087702
## T+6            0.4138848
## T+7            0.4187168
## T+8            0.4232855
## T+9            0.4276084
## T+10           0.4317014
## T+11           0.4355791
## T+12           0.4392549
## T+13           0.4427411
## T+14           0.4460491
## T+15           0.4491894
## T+16           0.4521716
## T+17           0.4550049
## T+18           0.4576977
## T+19           0.4602578
## T+20           0.4626924
```

```r
#x.pred@forecast$sigmaFor # 同
```
