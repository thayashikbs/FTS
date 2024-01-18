## 補足: 分散共分散行列のコレスキー分解

直交化インパルス応答や予測誤差分散分解では, 事前に外生性の高い順に変数を配置しておく必要がある.
直交化する際には, VAR(p)モデルの撹乱項の分散共分散行列に対してCholeskey分解が利用される.

- 分散共分散行列に対するコレスキー (Choleskey) 分解の計算例
  - 以下, 簡単のため, VAR(p)モデルは使わない

<!--
- msci日次収益率データの読み込み
  - データ入手先: https://www.asakura.co.jp/detail.php?book_code=12792 -->




```r
library(vars)
```

```
## Loading required package: MASS
```

```
## Loading required package: strucchange
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

```
## Loading required package: sandwich
```

```
## Loading required package: urca
```

```
## Loading required package: lmtest
```

```r
library(quantmod)
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
library(tidyverse)
```

```
## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
## ✔ dplyr     1.1.2     ✔ readr     2.1.4
## ✔ forcats   1.0.0     ✔ stringr   1.5.0
## ✔ ggplot2   3.4.2     ✔ tibble    3.2.1
## ✔ lubridate 1.9.2     ✔ tidyr     1.3.0
## ✔ purrr     1.0.2
```

```
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## ✖ stringr::boundary() masks strucchange::boundary()
## ✖ dplyr::filter()     masks stats::filter()
## ✖ dplyr::first()      masks xts::first()
## ✖ dplyr::lag()        masks stats::lag()
## ✖ dplyr::last()       masks xts::last()
## ✖ dplyr::select()     masks MASS::select()
## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors
```

```r
gspc <- getSymbols('^GSPC', periodicity = "monthly", from = '1999-12-31', auto.assign = FALSE)
ftse <- getSymbols('^FTSE', periodicity = "monthly", from = '1999-12-31', auto.assign = FALSE)
n225 <- getSymbols('^N225', periodicity = "monthly", from = '1999-12-31', auto.assign = FALSE)

us <- diff(log(gspc$GSPC.Adjusted))[-1]
uk <- diff(log(ftse$FTSE.Adjusted))[-1]
jp <- diff(log(n225$N225.Adjusted))[-1]
mkt <- merge.xts(us, uk, jp, all = T)

idx <- complete.cases(mkt) # 欠損値(NA)のあるレコードの除去
mkt <- mkt[idx]
colnames(mkt) <- c("us", "uk", "jp")

plot(cumsum(mkt), type = "l", col = c("black", "blue", "red"),
     main = "Market indices: S&P500 (black), FTSE (blue), NK225 (red)")
```

<img src="094_Chol_files/figure-html/unnamed-chunk-1-1.png" width="672" />

- 分散共分散行列${\bf \Sigma}$のCholeskey分解
$$ {\bf \Sigma} = {\bf P}{\bf P'} $$
但し, ${\bf P}$は下三角行列.

- 米英日の日次収益率に対して分散共分散行列を計算 → Choleskey分解

```r
# アメリカ、イギリス、日本市場
usukjp <- data.frame(mkt$us, mkt$uk, mkt$jp) 
head(usukjp, 5) 
```

```
##                     us           uk          jp
## 2000-02-01 -0.02031300 -0.005743495  0.02125795
## 2000-03-01  0.09232375  0.048174179  0.01875144
## 2000-04-01 -0.03127991 -0.033078382 -0.12354810
## 2000-05-01 -0.02215875  0.005044624 -0.09575560
## 2000-06-01  0.02365163 -0.007370510  0.06395117
```

```r
# 分散共分散行列: Sigma
cov_usukjp <- cov(usukjp) %>% print(digits = 4)
```

```
##          us       uk       jp
## us 0.002014 0.001397 0.001619
## uk 0.001397 0.001545 0.001244
## jp 0.001619 0.001244 0.003014
```

```r
# Choleskey分解: Sigma = PP'
P <- chol(cov_usukjp) %>% t() %>% print(digits = 4) # 下三角行列P
```

```
##         us       uk      jp
## us 0.04488 0.000000 0.00000
## uk 0.03113 0.023990 0.00000
## jp 0.03607 0.005039 0.04108
```

```r
# Sigmaの復元 (確認用)
P %*% t(P) %>% print(digits = 4)
```

```
##          us       uk       jp
## us 0.002014 0.001397 0.001619
## uk 0.001397 0.001545 0.001244
## jp 0.001619 0.001244 0.003014
```

- 米日英の分散共分散行列を計算 → Choleskey分解

```r
# アメリカ、日本, 英国市場
usjpuk <- data.frame(mkt$us, mkt$jp, mkt$uk) 
head(usjpuk, 5) 
```

```
##                     us          jp           uk
## 2000-02-01 -0.02031300  0.02125795 -0.005743495
## 2000-03-01  0.09232375  0.01875144  0.048174179
## 2000-04-01 -0.03127991 -0.12354810 -0.033078382
## 2000-05-01 -0.02215875 -0.09575560  0.005044624
## 2000-06-01  0.02365163  0.06395117 -0.007370510
```

```r
# 分散共分散行列: Sigma
cov_usjpuk <- cov(usjpuk) %>% print(digits = 4)
```

```
##          us       jp       uk
## us 0.002014 0.001619 0.001397
## jp 0.001619 0.003014 0.001244
## uk 0.001397 0.001244 0.001545
```

```r
# Choleskey分解: Sigma = PP'
P <- chol(cov_usjpuk) %>% t() %>% print(digits = 4) # 下三角行列P
```

```
##         us       jp      uk
## us 0.04488 0.000000 0.00000
## jp 0.03607 0.041386 0.00000
## uk 0.03113 0.002921 0.02381
```

```r
# Sigmaの復元 (確認用)
P %*% t(P) %>% print(digits = 4)
```

```
##          us       jp       uk
## us 0.002014 0.001619 0.001397
## jp 0.001619 0.003014 0.001244
## uk 0.001397 0.001244 0.001545
```

