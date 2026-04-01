# NG — Monthly Diagnostics

## Data

- Observations: **288**  
- Date range: **2001-01-01 — 2024-12-01**
- Average monthly volume: **70,539**  
- Minimum monthly volume: **9,241**


## Exploratory Analysis

**Summary of monthly returns**
 count    mean      std    skew  kurtosis_excess       min       q25    median    q75      max
   288 -0.0172 0.140664 0.50743         1.294989 -0.345665 -0.107307 -0.019821 0.0612 0.522744


**Month vs. Rest-of-Year (Welch two-sample t-tests)**

 month  n(m)  n(rest)   mean(m)  mean(rest)  mean(m) − mean(rest)    t_stat  p_value sig
01-Jan    24      264 -0.043328   -0.014825             -0.028503 -0.839308 0.408890    
02-Feb    24      264 -0.012125   -0.017662              0.005536  0.172478 0.864363    
03-Mar    24      264  0.002718   -0.019011              0.021729  0.700849 0.489390    
04-Apr    24      264  0.021663   -0.020733              0.042396  2.033904 0.049801  **
05-May    24      264 -0.013759   -0.017513              0.003755  0.183401 0.855549    
06-Jun    24      264 -0.016320   -0.017280              0.000961  0.033101 0.973831    
07-Jul    24      264 -0.008372   -0.018003              0.009631  0.290020 0.774068    
08-Aug    24      264 -0.015959   -0.017313              0.001354  0.037038 0.970741    
09-Sep    24      264  0.002138   -0.018958              0.021096  0.651689 0.520192    
10-Oct    24      264 -0.020198   -0.016928             -0.003270 -0.140589 0.889095    
11-Nov    24      264 -0.042489   -0.014901             -0.027588 -0.923639 0.363737    
12-Dec    24      264 -0.060373   -0.013276             -0.047098 -1.369143 0.182632    

_Interpretation:_ For each row, we test whether the average return in that month differs from the average return in all the other months. Small p-values (e.g., < 0.05) indicate the month’s average return is significantly different from the rest of the year.


## Stationarity

- **ADF**: stat = -4.573, p = 0.0001 ***, lags used = 10, n = 277  
  critical values: 1%=-3.454, 5%=-2.872, 10%=-2.572

- **KPSS (level)**: stat = 0.054, p = 0.1000 , lags used = 4  
  critical values: 10%=0.347, 5%=0.463, 2.5%=0.574, 1%=0.739

- **Joint read**: ADF rejects & KPSS does not reject → returns look stationary.


## Homoskedasticity (constant error variance)

- **Breusch–Pagan**: LM p = 0.4937, F p = 0.5012  
- **White**: LM p = 0.4937, F p = 0.5012
  *Interpretation:* **small p-values (<0.05)** suggest **heteroskedasticity conditional on the regressors** (here: month dummies).

- **ARCH LM (lags=12)**: LM p = 0.0061, F p = 0.0049  
  *Interpretation:* **small p-values (<0.05)** indicate **conditional heteroskedasticity** (volatility clustering) in residuals.

**Conclusion:** Evidence of **heteroskedasticity**. Report **robust standard errors** (HC3; and **HAC/Newey–West** if serial correlation is present).


## Structural Breaks

- **CUSUM (parameter stability test)**: p = 0.8640. CUSUM tracks cumulative sums of recursive residuals; **small p-values (< 0.05)** indicate **time-varying coefficients** (instability) rather than constant effects over the sample.

  **Interpretation:** No evidence against stability — parameters appear stable.

- **Chow (rolling break search)**: F = 1.93, p = 0.0314, break at index 47 (2004-12-01), left n = 47, right n = 241

  *What it does:* For each admissible split point, the test compares a **single-regime fit** to a **two-regime fit** (pre-break vs post-break). **Small p-values (< 0.05)** indicate a **structural break** at (or near) the reported split.

  **Interpretation:** Break suggested at the reported date; parameter shifts before/after that point.


## Outliers

**Regression influence (top 10 by Cook’s D)**
      date  year  month    return  stud_resid  leverage  cooks_d  flag_studentized>|3|  flag_leverage>2k/n  flag_cooks>4/n
2003-02-01  2003      2  0.522744    3.955919  0.041667 0.053843                  True               False            True
2022-07-01  2022      7  0.469727    3.515984  0.041667 0.043019                  True               False            True
2005-08-01  2005      8  0.434000    3.300581  0.041667 0.038104                  True               False            True
2018-11-01  2018     11  0.387485    3.148571  0.041667 0.034795                  True               False            True
2022-01-01  2022      1  0.371412    3.033219  0.041667 0.032373                  True               False            True
2002-03-01  2002      3  0.353814    2.555667  0.041667 0.023200                 False               False            True
2022-06-01  2022      6 -0.342545   -2.370786  0.041667 0.020029                 False               False            True
2020-08-01  2020      8  0.309109    2.362206  0.041667 0.019887                 False               False            True
2021-09-01  2021      9  0.317243    2.288404  0.041667 0.018687                 False               False            True
2003-12-01  2003     12  0.254103    2.283752  0.041667 0.018613                 False               False            True


**Univariate return outliers (MAD-z)**
      date  year  month    return     MAD_z  flag_|MADz|>3.5
2003-02-01  2003      2  0.522744  4.337543             True
2022-07-01  2022      7  0.469727  3.913693             True
2005-08-01  2005      8  0.434000  3.628075             True
2018-11-01  2018     11  0.387485  3.256210            False
2022-01-01  2022      1  0.371412  3.127719            False
2002-03-01  2002      3  0.353814  2.987031            False
2021-09-01  2021      9  0.317243  2.694659            False
2020-08-01  2020      8  0.309109  2.629633            False
2022-12-01  2022     12 -0.345665 -2.604964            False
2022-06-01  2022      6 -0.342545 -2.580028            False
