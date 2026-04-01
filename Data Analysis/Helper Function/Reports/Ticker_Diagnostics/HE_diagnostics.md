# HE — Monthly Diagnostics

## Data

- Observations: **288**  
- Date range: **2001-01-01 — 2024-12-01**
- Average monthly volume: **14,121**  
- Minimum monthly volume: **2,182**


## Exploratory Analysis

**Summary of monthly returns**
 count      mean     std      skew  kurtosis_excess       min       q25    median      q75      max
   288 -0.005954 0.07447 -0.080868         0.255516 -0.244186 -0.058943 -0.001389 0.041997 0.197285


**Month vs. Rest-of-Year (Welch two-sample t-tests)**

 month  n(m)  n(rest)   mean(m)  mean(rest)  mean(m) − mean(rest)    t_stat  p_value sig
01-Jan    24      264 -0.005083   -0.006033              0.000950  0.053982 0.957356    
02-Feb    24      264 -0.001604   -0.006349              0.004745  0.337372 0.738273    
03-Mar    24      264 -0.012463   -0.005362             -0.007102 -0.376581 0.709559    
04-Apr    24      264 -0.008173   -0.005752             -0.002421 -0.183109 0.855947    
05-May    24      264 -0.014686   -0.005160             -0.009526 -0.781845 0.440132    
06-Jun    24      264 -0.018423   -0.004820             -0.013603 -0.918162 0.366327    
07-Jul    24      264 -0.002801   -0.006240              0.003439  0.200144 0.842891    
08-Aug    24      264 -0.022055   -0.004490             -0.017565 -0.978949 0.336555    
09-Sep    24      264  0.017063   -0.008046              0.025109  1.378659 0.179712    
10-Oct    24      264 -0.000303   -0.006467              0.006165  0.320048 0.751519    
11-Nov    24      264  0.011018   -0.007497              0.018515  1.553396 0.130154    
12-Dec    24      264 -0.013934   -0.005228             -0.008706 -0.610794 0.546140    

_Interpretation:_ For each row, we test whether the average return in that month differs from the average return in all the other months. Small p-values (e.g., < 0.05) indicate the month’s average return is significantly different from the rest of the year.


## Stationarity

- **ADF**: stat = -11.101, p = 0.0000 ***, lags used = 2, n = 285  
  critical values: 1%=-3.454, 5%=-2.872, 10%=-2.572

- **KPSS (level)**: stat = 0.059, p = 0.1000 , lags used = 7  
  critical values: 10%=0.347, 5%=0.463, 2.5%=0.574, 1%=0.739

- **Joint read**: ADF rejects & KPSS does not reject → returns look stationary.


## Homoskedasticity (constant error variance)

- **Breusch–Pagan**: LM p = 0.2181, F p = 0.2190  
- **White**: LM p = 0.2181, F p = 0.2190
  *Interpretation:* **small p-values (<0.05)** suggest **heteroskedasticity conditional on the regressors** (here: month dummies).

- **ARCH LM (lags=12)**: LM p = 0.5121, F p = 0.5206  
  *Interpretation:* **small p-values (<0.05)** indicate **conditional heteroskedasticity** (volatility clustering) in residuals.

**Conclusion:** Tests **do not suggest heteroskedasticity** at conventional levels. A homoskedastic assumption appears reasonable; **robust SEs** are still a conservative default in finance.


## Structural Breaks

- **CUSUM (parameter stability test)**: p = 0.5037. CUSUM tracks cumulative sums of recursive residuals; **small p-values (< 0.05)** indicate **time-varying coefficients** (instability) rather than constant effects over the sample.

  **Interpretation:** No evidence against stability — parameters appear stable.

- **Chow (rolling break search)**: F = 1.30, p = 0.2155, break at index 252 (2022-01-01), left n = 252, right n = 36

  *What it does:* For each admissible split point, the test compares a **single-regime fit** to a **two-regime fit** (pre-break vs post-break). **Small p-values (< 0.05)** indicate a **structural break** at (or near) the reported split.

  **Interpretation:** No strong evidence of a single dominant break.


## Outliers

**Regression influence (top 10 by Cook’s D)**
      date  year  month    return  stud_resid  leverage  cooks_d  flag_studentized>|3|  flag_leverage>2k/n  flag_cooks>4/n
2016-09-01  2016      9 -0.235217   -3.501417  0.041667 0.042679                  True               False            True
2002-08-01  2002      8 -0.244186   -3.067642  0.041667 0.033087                  True               False            True
2016-12-01  2016     12  0.197285    2.912168  0.041667 0.029916                 False               False            True
2020-03-01  2020      3 -0.221613   -2.882772  0.041667 0.029333                 False               False            True
2020-01-01  2020      1 -0.211772   -2.847860  0.041667 0.028647                 False               False            True
2019-03-01  2019      3  0.166667    2.459123  0.041667 0.021517                 False               False            True
2007-07-01  2007      7  0.174541    2.434053  0.041667 0.021090                 False               False            True
2016-07-01  2016      7 -0.177003   -2.390047  0.041667 0.020349                 False               False            True
2002-09-01  2002      9  0.170274    2.097119  0.041667 0.015741                 False               False            True
2008-10-01  2008     10 -0.150058   -2.049094  0.041667 0.015039                 False               False            True


**Univariate return outliers (MAD-z)**
      date  year  month    return     MAD_z  flag_|MADz|>3.5
2002-08-01  2002      8 -0.244186 -3.279033            False
2016-09-01  2016      9 -0.235217 -3.157910            False
2020-03-01  2020      3 -0.221613 -2.974178            False
2020-01-01  2020      1 -0.211772 -2.841278            False
2016-12-01  2016     12  0.197285  2.683132            False
2007-07-01  2007      7  0.174541  2.375971            False
2016-07-01  2016      7 -0.177003 -2.371718            False
2002-09-01  2002      9  0.170274  2.318344            False
2019-03-01  2019      3  0.166667  2.269624            False
2009-10-01  2009     10  0.148936  2.030169            False
