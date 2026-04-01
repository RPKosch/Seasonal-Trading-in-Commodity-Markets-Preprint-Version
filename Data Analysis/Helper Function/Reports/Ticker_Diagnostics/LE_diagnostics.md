# LE — Monthly Diagnostics

## Data

- Observations: **288**  
- Date range: **2001-01-01 — 2024-12-01**
- Average monthly volume: **18,534**  
- Minimum monthly volume: **3,970**


## Exploratory Analysis

**Summary of monthly returns**
 count     mean      std      skew  kurtosis_excess       min       q25   median      q75      max
   288 0.000044 0.041465 -0.597898         1.926446 -0.208131 -0.022979 0.002041 0.026405 0.118602


**Month vs. Rest-of-Year (Welch two-sample t-tests)**

 month  n(m)  n(rest)   mean(m)  mean(rest)  mean(m) − mean(rest)    t_stat  p_value sig
01-Jan    24      264 -0.005035    0.000505             -0.005541 -0.789711 0.435727    
02-Feb    24      264  0.001763   -0.000113              0.001876  0.240441 0.811681    
03-Mar    24      264 -0.013098    0.001238             -0.014337 -1.319754 0.198601    
04-Apr    24      264 -0.005431    0.000541             -0.005972 -0.650118 0.521117    
05-May    24      264  0.004039   -0.000320              0.004358  0.480254 0.634902    
06-Jun    24      264  0.012383   -0.001078              0.013461  1.650039 0.109997    
07-Jul    24      264  0.010492   -0.000906              0.011398  1.537679 0.134693    
08-Aug    24      264 -0.014875    0.001400             -0.016275 -2.232557 0.033192  **
09-Sep    24      264  0.002076   -0.000141              0.002217  0.208556 0.836435    
10-Oct    24      264  0.007648   -0.000648              0.008295  0.928649 0.361226    
11-Nov    24      264  0.003856   -0.000303              0.004158  0.487154 0.629973    
12-Dec    24      264 -0.003293    0.000347             -0.003640 -0.361673 0.720501    

_Interpretation:_ For each row, we test whether the average return in that month differs from the average return in all the other months. Small p-values (e.g., < 0.05) indicate the month’s average return is significantly different from the rest of the year.


## Stationarity

- **ADF**: stat = -16.341, p = 0.0000 ***, lags used = 0, n = 287  
  critical values: 1%=-3.453, 5%=-2.872, 10%=-2.572

- **KPSS (level)**: stat = 0.056, p = 0.1000 , lags used = 5  
  critical values: 10%=0.347, 5%=0.463, 2.5%=0.574, 1%=0.739

- **Joint read**: ADF rejects & KPSS does not reject → returns look stationary.


## Homoskedasticity (constant error variance)

- **Breusch–Pagan**: LM p = 0.7779, F p = 0.7861  
- **White**: LM p = 0.7779, F p = 0.7861
  *Interpretation:* **small p-values (<0.05)** suggest **heteroskedasticity conditional on the regressors** (here: month dummies).

- **ARCH LM (lags=12)**: LM p = 0.2607, F p = 0.2628  
  *Interpretation:* **small p-values (<0.05)** indicate **conditional heteroskedasticity** (volatility clustering) in residuals.

**Conclusion:** Tests **do not suggest heteroskedasticity** at conventional levels. A homoskedastic assumption appears reasonable; **robust SEs** are still a conservative default in finance.


## Structural Breaks

- **CUSUM (parameter stability test)**: p = 0.9667. CUSUM tracks cumulative sums of recursive residuals; **small p-values (< 0.05)** indicate **time-varying coefficients** (instability) rather than constant effects over the sample.

  **Interpretation:** No evidence against stability — parameters appear stable.

- **Chow (rolling break search)**: F = 1.49, p = 0.1271, break at index 54 (2005-07-01), left n = 54, right n = 234

  *What it does:* For each admissible split point, the test compares a **single-regime fit** to a **two-regime fit** (pre-break vs post-break). **Small p-values (< 0.05)** indicate a **structural break** at (or near) the reported split.

  **Interpretation:** No strong evidence of a single dominant break.


## Outliers

**Regression influence (top 10 by Cook’s D)**
      date  year  month    return  stud_resid  leverage  cooks_d  flag_studentized>|3|  flag_leverage>2k/n  flag_cooks>4/n
2003-12-01  2003     12 -0.208131   -5.291515  0.041667 0.092410                  True               False            True
2017-04-01  2017      4  0.118602    3.105523  0.041667 0.033882                  True               False            True
2020-02-01  2020      2 -0.101107   -2.561682  0.041667 0.023306                 False               False            True
2018-03-01  2018      3 -0.113823   -2.507012  0.041667 0.022344                 False               False            True
2015-09-01  2015      9 -0.096913   -2.462864  0.041667 0.021581                 False               False            True
2001-09-01  2001      9 -0.094486   -2.401179  0.041667 0.020535                 False               False            True
2011-05-01  2011      5 -0.090222   -2.342814  0.041667 0.019569                 False               False            True
2006-03-01  2006      3 -0.102595   -2.222214  0.041667 0.017640                 False               False            True
2003-07-01  2003      7  0.097458    2.158288  0.041667 0.016657                 False               False            True
2008-10-01  2008     10 -0.078529   -2.138370  0.041667 0.016356                 False               False            True


**Univariate return outliers (MAD-z)**
      date  year  month    return     MAD_z  flag_|MADz|>3.5
2003-12-01  2003     12 -0.208131 -5.682540             True
2017-04-01  2017      4  0.118602  3.151506            False
2018-03-01  2018      3 -0.113823 -3.132676            False
2006-03-01  2006      3 -0.102595 -2.829104            False
2020-02-01  2020      2 -0.101107 -2.788875            False
2020-03-01  2020      3 -0.097304 -2.686045            False
2015-09-01  2015      9 -0.096913 -2.675482            False
2001-09-01  2001      9 -0.094486 -2.609843            False
2003-07-01  2003      7  0.097458  2.579815            False
2011-05-01  2011      5 -0.090222 -2.494576            False
