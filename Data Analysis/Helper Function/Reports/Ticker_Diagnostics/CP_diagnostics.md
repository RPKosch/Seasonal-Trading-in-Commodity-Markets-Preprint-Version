# CP — Monthly Diagnostics

## Data

- Observations: **288**  
- Date range: **2001-01-01 — 2024-12-01**
- Average monthly volume: **33,015**  
- Minimum monthly volume: **1,007**


## Exploratory Analysis

**Summary of monthly returns**
 count     mean      std      skew  kurtosis_excess       min       q25   median      q75      max
   288 0.007482 0.073547 -0.096422          2.97013 -0.367347 -0.040128 0.002816 0.051851 0.318526


**Month vs. Rest-of-Year (Welch two-sample t-tests)**

 month  n(m)  n(rest)   mean(m)  mean(rest)  mean(m) − mean(rest)    t_stat  p_value sig
01-Jan    24      264  0.015901    0.006717              0.009184  0.589929 0.560065    
02-Feb    24      264  0.031518    0.005297              0.026221  1.885242 0.069513   *
03-Mar    24      264  0.009597    0.007290              0.002308  0.151459 0.880711    
04-Apr    24      264  0.021084    0.006245              0.014838  0.782019 0.441345    
05-May    24      264 -0.006390    0.008743             -0.015134 -1.139151 0.263774    
06-Jun    24      264 -0.006672    0.008769             -0.015441 -1.136485 0.264984    
07-Jul    24      264  0.021158    0.006239              0.014919  0.993129 0.329200    
08-Aug    24      264 -0.011081    0.009170             -0.020250 -2.088726 0.043390  **
09-Sep    24      264 -0.003570    0.008487             -0.012056 -0.721763 0.476708    
10-Oct    24      264 -0.003323    0.008464             -0.011787 -0.584090 0.564326    
11-Nov    24      264  0.019486    0.006391              0.013095  0.728498 0.472793    
12-Dec    24      264  0.002077    0.007973             -0.005896 -0.376265 0.709621    

_Interpretation:_ For each row, we test whether the average return in that month differs from the average return in all the other months. Small p-values (e.g., < 0.05) indicate the month’s average return is significantly different from the rest of the year.


## Stationarity

- **ADF**: stat = -9.542, p = 0.0000 ***, lags used = 1, n = 286  
  critical values: 1%=-3.453, 5%=-2.872, 10%=-2.572

- **KPSS (level)**: stat = 0.197, p = 0.1000 , lags used = 5  
  critical values: 10%=0.347, 5%=0.463, 2.5%=0.574, 1%=0.739

- **Joint read**: ADF rejects & KPSS does not reject → returns look stationary.


## Homoskedasticity (constant error variance)

- **Breusch–Pagan**: LM p = 0.6757, F p = 0.6846  
- **White**: LM p = 0.6757, F p = 0.6846
  *Interpretation:* **small p-values (<0.05)** suggest **heteroskedasticity conditional on the regressors** (here: month dummies).

- **ARCH LM (lags=12)**: LM p = 0.2781, F p = 0.2806  
  *Interpretation:* **small p-values (<0.05)** indicate **conditional heteroskedasticity** (volatility clustering) in residuals.

**Conclusion:** Tests **do not suggest heteroskedasticity** at conventional levels. A homoskedastic assumption appears reasonable; **robust SEs** are still a conservative default in finance.


## Structural Breaks

- **CUSUM (parameter stability test)**: p = 0.2168. CUSUM tracks cumulative sums of recursive residuals; **small p-values (< 0.05)** indicate **time-varying coefficients** (instability) rather than constant effects over the sample.

  **Interpretation:** No evidence against stability — parameters appear stable.

- **Chow (rolling break search)**: F = 1.56, p = 0.1027, break at index 30 (2003-07-01), left n = 30, right n = 258

  *What it does:* For each admissible split point, the test compares a **single-regime fit** to a **two-regime fit** (pre-break vs post-break). **Small p-values (< 0.05)** indicate a **structural break** at (or near) the reported split.

  **Interpretation:** No strong evidence of a single dominant break.


## Outliers

**Regression influence (top 10 by Cook’s D)**
      date  year  month    return  stud_resid  leverage  cooks_d  flag_studentized>|3|  flag_leverage>2k/n  flag_cooks>4/n
2008-10-01  2008     10 -0.367347   -5.283925  0.041667 0.092169                  True               False            True
2006-04-01  2006      4  0.318526    4.246416  0.041667 0.061536                  True               False            True
2011-09-01  2011      9 -0.249434   -3.474182  0.041667 0.042045                  True               False            True
2009-03-01  2009      3  0.190384    2.529229  0.041667 0.022733                 False               False            True
2016-11-01  2016     11  0.189519    2.375602  0.041667 0.020109                 False               False            True
2010-12-01  2010     12  0.167805    2.314260  0.041667 0.019104                 False               False            True
2011-10-01  2011     10  0.156688    2.232951  0.041667 0.017808                 False               False            True
2008-12-01  2008     12 -0.148808   -2.103488  0.041667 0.015835                 False               False            True
2003-10-01  2003     10  0.145910    2.080087  0.041667 0.015490                 False               False            True
2004-02-01  2004      2  0.176573    2.020996  0.041667 0.014635                 False               False            True


**Univariate return outliers (MAD-z)**
      date  year  month    return     MAD_z  flag_|MADz|>3.5
2008-10-01  2008     10 -0.367347 -5.283343             True
2006-04-01  2006      4  0.318526  4.506140             True
2011-09-01  2011      9 -0.249434 -3.600376             True
2009-03-01  2009      3  0.190384  2.677164            False
2016-11-01  2016     11  0.189519  2.664816            False
2004-02-01  2004      2  0.176573  2.480046            False
2010-12-01  2010     12  0.167805  2.354888            False
2008-02-01  2008      2  0.165885  2.327495            False
2001-11-01  2001     11  0.158228  2.218199            False
2011-10-01  2011     10  0.156688  2.196219            False
