# ARIMAモデル/SARIMAモデル



## ARIMA$(p,d,q)$モデルとは
- $Y_t:=(1-B)^d X_t$が, causal ARMA$(p,q)$となる確率過程$X_t$ ($d$は非負整数), すなわち,


$$ \phi(B)Y_t = \theta(B) Z_t \tag{2}$$

- Backward shift operator $B$
- AR多項式 $\phi(z)=1 - \phi_1 z - \cdots - \phi_p z^p$
- MA多項式 $\theta(z)=1 + \theta_1 z + \cdots + \theta_q z^q$

## SARIMA$(p,d,q)\times (P,D,Q)_s$モデル (周期$s$)とは
- $Y_t:=(1-B)^d (1-B^s)^D X_t$が, 以下で定義されるcausal ARMAとなる確率過程$X_t$ ($d,D$は非負整数)

$$ \phi(B)\Phi(B^s) Y_t = \theta(B) \Theta(B^s) Z_t \tag{2}$$

- Backward shift operator $B$
- AR多項式 $\phi(z)=1 - \phi_1 z - \cdots - \phi_p z^p$
- MA多項式 $\theta(z)=1 + \theta_1 z + \cdots + \theta_q z^q$
- SAR多項式 $\Phi(z)=1 - \Phi_1 z - \cdots - \Phi_p z^P$
- SMA多項式 $\Theta(z)=1 + \Theta_1 z + \cdots + \Theta_q z^Q$

## SARIMAモデルのパス発生 ({sarima}パッケージの利用)
- "SAR(1)"モデル (s=12)


```r
require(sarima)
```

```
## Loading required package: sarima
```

```
## Loading required package: stats4
```

```
## 
## Attaching package: 'sarima'
```

```
## The following object is masked from 'package:stats':
## 
##     spectrum
```

```r
#par(mfrow=c(3,1))
Seedv = 10
Tlen = 144
set.seed(Seedv)
#x <- sim_sarima(n = 144, model = list(ar = c(rep(0,11), 0.8)))  # 12  seasons
x <- sim_sarima(n = 144, model = list(sar = 0.8, nseasons = 12, sigma2 = 1))  # 12 seasons

ts.plot(x, type = "l") # 時系列プロット
```

<img src="036-SARIMA_files/figure-html/SAR1-1.png" width="672" />

```r
par(mfrow = c(1,2))
acf(x) # 自己相関(ACF)
pacf(x)	# 偏自己相関(PACF)
```

<img src="036-SARIMA_files/figure-html/SAR1-2.png" width="672" />

- "SMA(1)"モデル

```r
set.seed(Seedv)
#x <- sim_sarima(n = 144, model = list(ma = c(rep(0,11), 0.8)))  # 12 seasons
x <- sim_sarima(n = 144,model = list(sma = 0.8, nseasons = 12, sigma2 = 1))  # 12 seasons
ts.plot(x, type = "l") # 時系列プロット
```

<img src="036-SARIMA_files/figure-html/SMA1-1.png" width="672" />

```r
par(mfrow = c(1,2))
acf(x) # 自己相関(ACF)
pacf(x)	# 偏自己相関(PACF)
```

<img src="036-SARIMA_files/figure-html/SMA1-2.png" width="672" />

- I(1) モデル ("Random Walk")

```r
set.seed(Seedv)
x <- sim_sarima(n = 144, model = list(iorder = 1, sigma2 = 1)) # (1-B)X_t = e_t (random walk)
ts.plot(x, type="l") # 時系列プロット
```

<img src="036-SARIMA_files/figure-html/I1-1.png" width="672" />

```r
par(mfrow = c(1,2))
acf(x) # 自己相関(ACF)
pacf(x)	# 偏自己相関(PACF)
```

<img src="036-SARIMA_files/figure-html/I1-2.png" width="672" />

- SARIMA$(0,0,0) \times (0,1,0)_{12}$

```r
set.seed(Seedv)
x <- sim_sarima(n = 144, model = list(siorder = 1,nseasons = 12, sigma2 = 1)) # (1-B)^{12} X_t = e_t
ts.plot(x, type="l") # 時系列プロット
```

<img src="036-SARIMA_files/figure-html/SARIMA_1-1.png" width="672" />

```r
par(mfrow = c(1,2))
acf(x) # 自己相関(ACF)
pacf(x)	# 偏自己相関(PACF)
```

<img src="036-SARIMA_files/figure-html/SARIMA_1-2.png" width="672" />

- SARIMA$(0,1,0) \times (0,1,0)_{12}$

```r
set.seed(Seedv)
x <- sim_sarima(n = 144, model = list(iorder = 1, siorder = 1, nseasons = 12, sigma2 = 1))
ts.plot(x, type = "l") # 時系列プロット
```

<img src="036-SARIMA_files/figure-html/SARIMA_2-1.png" width="672" />

```r
par(mfrow = c(1,2))
acf(x) # 自己相関(ACF)
pacf(x)	# 偏自己相関(PACF)
```

<img src="036-SARIMA_files/figure-html/SARIMA_2-2.png" width="672" />

- SARIMA$(0,1,0) \times (0,1,0)_{12}$に, 初期値xを指定したシミュレーション

```r
x <- sim_sarima(n = 144, model = list(iorder = 1, siorder = 1, nseasons = 12, sigma2 = 1), x = list(init=AirPassengers[1:13]))
ts.plot(x, type = "l") # 時系列プロット
```

<img src="036-SARIMA_files/figure-html/SARIMA_2-3-1.png" width="672" />

```r
par(mfrow = c(1,2))
acf(x) # 自己相関(ACF)
pacf(x)	# 偏自己相関(PACF)
```

<img src="036-SARIMA_files/figure-html/SARIMA_2-3-2.png" width="672" />

- SARIMA$(0,0,0) \times (1,0,1)_{12}$

```r
set.seed(Seedv)
x <- sim_sarima(n = 144, model = list(sar = 0.4, sma = 0.5, iorder = 0, siorder = 0, nseasons = 12)) 
ts.plot(x, type = "l") # 時系列プロット
```

<img src="036-SARIMA_files/figure-html/SARIMA_3-1.png" width="672" />

```r
par(mfrow = c(1,2))
acf(x) # 自己相関(ACF)
pacf(x)	# 偏自己相関(PACF)
```

<img src="036-SARIMA_files/figure-html/SARIMA_3-2.png" width="672" />

- SARIMA$(0,0,1) \times (1,0,1)_{12}$

```r
set.seed(Seedv)
x <- sim_sarima(n = 144, model = list(ma = 0.7, sar = 0.4, sma = 0.5, iorder = 0, siorder = 0, nseasons = 12)) 
ts.plot(x, type = "l") # 時系列プロット
```

<img src="036-SARIMA_files/figure-html/SARIMA_4-1.png" width="672" />

```r
par(mfrow = c(1,2))
acf(x) # 自己相関(ACF)
pacf(x)	# 偏自己相関(PACF)
```

<img src="036-SARIMA_files/figure-html/SARIMA_4-2.png" width="672" />

- SARIMA$(2,1,1) \times (1,1,1)_{12}$

```r
set.seed(Seedv)
x <- sim_sarima(n = 144, model = list(ar = c(1.2, -0.8), ma = 0.4, sar = 0.3, sma = 0.7, iorder = 1, siorder = 1, nseasons = 12))
ts.plot(x, type = "l") # 時系列プロット
```

<img src="036-SARIMA_files/figure-html/SARIMA_5-1.png" width="672" />

```r
par(mfrow = c(1,2))
acf(x) # 自己相関(ACF)
pacf(x)	# 偏自己相関(PACF)
```

<img src="036-SARIMA_files/figure-html/SARIMA_5-2.png" width="672" />

## SARIMAモデルの推定・診断
- arima関数は, SARIMAモデルの推定が可能 (一方, arima.sim関数はSARIMAモデルのパスを生成できない)
- 仮に$(p,d,q)=(2,1,1), (P,D,Q)=(1,1,1), s=12$を正しく選んだとすると,

```r
(x.fit = arima(x, order = c(2,1,1), seasonal = list(order = c(1,1,1), period = 12))) 
```

```
## 
## Call:
## arima(x = x, order = c(2, 1, 1), seasonal = list(order = c(1, 1, 1), period = 12))
## 
## Coefficients:
##          ar1      ar2     ma1    sar1    sma1
##       1.2462  -0.8486  0.3504  0.2220  1.0000
## s.e.  0.0589   0.0558  0.0842  0.0909  0.2598
## 
## sigma^2 estimated as 0.7217:  log likelihood = -183.18,  aic = 378.35
```

```r
tsdiag(x.fit, gof = 20)  # モデル診断
```

<img src="036-SARIMA_files/figure-html/SARIMA_est-1.png" width="672" />

```r
Box.test(x.fit$residuals,lag = 20,type = 'Ljung')  # Ljung-Box検定
```

```
## 
## 	Box-Ljung test
## 
## data:  x.fit$residuals
## X-squared = 12.586, df = 20, p-value = 0.8944
```
※ ARIMAモデル指定の場合には, 定数項パラメータは推定されない (include.meanオプションは無視される)

## パッケージ{forecast}の利用
- モデルの自動選択・推定
- forecast::auto.arima関数は, seasonalオプションによりSARIMAモデルを推定可能 (デフォルトは, seasonal = T)

```r
require(forecast)
```

```
## Loading required package: forecast
```

```
## Registered S3 method overwritten by 'quantmod':
##   method            from
##   as.zoo.data.frame zoo
```

```r
(x.fit3 = auto.arima(x)) # AIC/AICc(デフォルト)/BICによりモデルを自動選択&推定
```

```
## Series: x 
## ARIMA(4,1,1) with drift 
## 
## Coefficients:
##          ar1      ar2     ar3      ar4      ma1    drift
##       2.2193  -2.3467  1.3199  -0.4084  -0.7300  -3.0955
## s.e.  0.0898   0.1786  0.1721   0.0780   0.0598   0.4031
## 
## sigma^2 = 14.72:  log likelihood = -393.94
## AIC=801.88   AICc=802.71   BIC=822.62
```
→  モデルは正しく推定はされてはいない. 定数項は大きな値


```r
tsdiag(x.fit3, gof = 20)  # モデル診断
```

<img src="036-SARIMA_files/figure-html/SARIMA__2-1.png" width="672" />

```r
Box.test(x.fit3$residuals, lag = 20, type = 'Ljung')  # Ljung-Box検定
```

```
## 
## 	Box-Ljung test
## 
## data:  x.fit3$residuals
## X-squared = 145.11, df = 20, p-value < 2.2e-16
```
→  季節性も残留

- 推定モデルを使った予測

```r
(x.pred = forecast(x.fit3, h = 20))
```

```
##     Point Forecast     Lo 80     Hi 80     Lo 95     Hi 95
## 145      -436.1359 -441.0524 -431.2195 -443.6550 -428.6169
## 146      -445.6132 -458.8023 -432.4240 -465.7842 -425.4421
## 147      -452.7936 -474.2717 -431.3155 -485.6416 -419.9457
## 148      -455.4497 -482.6672 -428.2321 -497.0753 -413.8240
## 149      -454.4453 -484.5206 -424.3700 -500.4415 -408.4491
## 150      -452.2585 -483.3464 -421.1706 -499.8034 -404.7136
## 151      -451.0034 -482.3361 -419.6708 -498.9226 -403.0843
## 152      -451.6076 -482.9808 -420.2343 -499.5887 -403.6264
## 153      -454.0855 -485.4704 -422.7006 -502.0846 -406.0864
## 154      -458.0720 -489.4894 -426.6545 -506.1208 -410.0231
## 155      -463.0825 -494.6429 -431.5221 -511.3499 -414.8151
## 156      -468.5396 -500.5108 -436.5684 -517.4353 -419.6439
## 157      -473.8106 -506.5744 -441.0469 -523.9185 -423.7028
## 158      -478.3561 -512.2327 -444.4796 -530.1658 -426.5464
## 159      -481.8994 -516.9893 -446.8096 -535.5647 -428.2341
## 160      -484.4929 -520.6666 -448.3192 -539.8159 -429.1700
## 161      -486.4490 -523.4555 -449.4425 -543.0455 -429.8524
## 162      -488.1924 -525.7803 -450.6045 -545.6781 -430.7066
## 163      -490.1157 -528.1020 -452.1293 -548.2108 -432.0206
## 164      -492.4836 -530.7678 -454.1994 -551.0343 -433.9330
```

```r
plot(x.pred)　# 
```

<img src="036-SARIMA_files/figure-html/SARIMA___-1.png" width="672" />

- 参考. 任意トレンドを加えてのシミュレーション (xinterceptオプション使用)

```r
set.seed(Seedv)
x = sim_sarima(n = 144, model = list(sma = 0.4, ma = 0.4, sar = 0.8, ar = 0.5, 
    nseasons = 12, sigma2 = 1), xintercept = (1:144) * 0.05)
ts.plot(x, type = "l") # 時系列プロット
```

<img src="036-SARIMA_files/figure-html/SARIMA____-1.png" width="672" />

```r
par(mfrow = c(1,2))
acf(x) # 自己相関(ACF)
pacf(x)	# 偏自己相関(PACF)
```

<img src="036-SARIMA_files/figure-html/SARIMA____-2.png" width="672" />

- モデル次数, 線形トレンドを正しく指定した場合のarimaによる推定

```r
(x.fit = arima(x,order = c(1,0,1), seasonal = list(order = c(1,0,1), period = 12), xreg = 1:144)) 
```

```
## 
## Call:
## arima(x = x, order = c(1, 0, 1), seasonal = list(order = c(1, 0, 1), period = 12), 
##     xreg = 1:144)
## 
## Coefficients:
##          ar1     ma1    sar1    sma1  intercept   1:144
##       0.7606  0.1744  0.7531  0.6228    -5.2907  0.3521
## s.e.  0.0656  0.1134  0.0576  0.0957     3.0592  0.0310
## 
## sigma^2 estimated as 0.9208:  log likelihood = -211.59,  aic = 437.19
```

```r
tsdiag(x.fit, gof = 20)  # モデル診断
```

<img src="036-SARIMA_files/figure-html/SARIMA__est1-1.png" width="672" />

```r
Box.test(x.fit$residuals, lag = 20, type = 'Ljung')  # Ljung-Box検定
```

```
## 
## 	Box-Ljung test
## 
## data:  x.fit$residuals
## X-squared = 11.261, df = 20, p-value = 0.9392
```

- 線形トレンドを正しく指定した場合のauto.arimaによる推定

```r
require(forecast)
(x.fit3 = auto.arima(x, xreg = 1:144)) # AIC/AICc(デフォルト)/BICによりモデルを自動選択&推定
```

```
## Series: x 
## Regression with ARIMA(1,0,0) errors 
## 
## Coefficients:
##          ar1  intercept    xreg
##       0.8291    -8.3410  0.3737
## s.e.  0.0485     1.9888  0.0233
## 
## sigma^2 = 4.862:  log likelihood = -317.26
## AIC=642.53   AICc=642.81   BIC=654.41
```
