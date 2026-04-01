# SU — Monthly Diagnostics

## Data

- Observations: **288**  
- Date range: **2001-01-01 — 2024-12-01**
- Average monthly volume: **47,906**  
- Minimum monthly volume: **8,101**


## Exploratory Analysis

**Summary of monthly returns**
 count     mean      std     skew  kurtosis_excess       min       q25   median      q75      max
   288 0.002502 0.087633 0.119695         0.703324 -0.297034 -0.056782 0.000875 0.054401 0.303581


**Month vs. Rest-of-Year (Welch two-sample t-tests)**

 month  n(m)  n(rest)   mean(m)  mean(rest)  mean(m) − mean(rest)    t_stat  p_value sig
01-Jan    24      264  0.026008    0.000365              0.025643  1.249652 0.222377    
02-Feb    24      264 -0.001786    0.002892             -0.004678 -0.300628 0.765777    
03-Mar    24      264 -0.058316    0.008031             -0.066347 -2.972386 0.006345 ***
04-Apr    24      264 -0.000252    0.002753             -0.003005 -0.164701 0.870378    
05-May    24      264 -0.013050    0.003916             -0.016967 -1.133348 0.265862    
06-Jun    24      264  0.029577    0.000041              0.029536  1.666112 0.106870    
07-Jul    24      264  0.020321    0.000882              0.019438  0.930966 0.360337    
08-Aug    24      264 -0.012457    0.003862             -0.016319 -0.802848 0.429195    
09-Sep    24      264  0.016965    0.001187              0.015778  0.891473 0.380276    
10-Oct    24      264  0.014789    0.001385              0.013404  0.745039 0.462503    
11-Nov    24      264  0.003019    0.002455              0.000564  0.041275 0.967327    
12-Dec    24      264  0.005208    0.002256              0.002952  0.153776 0.878926    

_Interpretation:_ For each row, we test whether the average return in that month differs from the average return in all the other months. Small p-values (e.g., < 0.05) indicate the month’s average return is significantly different from the rest of the year.


## Stationarity

- **ADF**: stat = -15.263, p = 0.0000 ***, lags used = 0, n = 287  
  critical values: 1%=-3.453, 5%=-2.872, 10%=-2.572

- **KPSS (level)**: stat = 0.081, p = 0.1000 , lags used = 3  
  critical values: 10%=0.347, 5%=0.463, 2.5%=0.574, 1%=0.739

- **Joint read**: ADF rejects & KPSS does not reject → returns look stationary.


## Homoskedasticity (constant error variance)

- **Breusch–Pagan**: LM p = 0.5378, F p = 0.5459  
- **White**: LM p = 0.5378, F p = 0.5459
  *Interpretation:* **small p-values (<0.05)** suggest **heteroskedasticity conditional on the regressors** (here: month dummies).

- **ARCH LM (lags=12)**: LM p = 0.0301, F p = 0.0275  
  *Interpretation:* **small p-values (<0.05)** indicate **conditional heteroskedasticity** (volatility clustering) in residuals.

**Conclusion:** Evidence of **heteroskedasticity**. Report **robust standard errors** (HC3; and **HAC/Newey–West** if serial correlation is present).


## Structural Breaks

- **CUSUM (parameter stability test)**: p = 0.5249. CUSUM tracks cumulative sums of recursive residuals; **small p-values (< 0.05)** indicate **time-varying coefficients** (instability) rather than constant effects over the sample.

  **Interpretation:** No evidence against stability — parameters appear stable.

- **Chow (rolling break search)**: F = 1.78, p = 0.0517, break at index 243 (2021-04-01), left n = 243, right n = 45

  *What it does:* For each admissible split point, the test compares a **single-regime fit** to a **two-regime fit** (pre-break vs post-break). **Small p-values (< 0.05)** indicate a **structural break** at (or near) the reported split.

  **Interpretation:** Weak or borderline evidence of a break.


## Outliers

**Regression influence (top 10 by Cook’s D)**
      date  year  month    return  stud_resid  leverage  cooks_d  flag_studentized>|3|  flag_leverage>2k/n  flag_cooks>4/n
2009-08-01  2009      8  0.303581    3.831896  0.041667 0.050688                  True               False            True
2010-03-01  2010      3 -0.297034   -2.861790  0.041667 0.028920                 False               False            True
2023-12-01  2023     12 -0.213002   -2.609553  0.041667 0.024164                 False               False            True
2010-10-01  2010     10  0.227139    2.537798  0.041667 0.022884                 False               False            True
2020-03-01  2020      3 -0.268772   -2.514653  0.041667 0.022478                 False               False            True
2006-01-01  2006      1  0.234247    2.487552  0.041667 0.022006                 False               False            True
2010-07-01  2010      7  0.226959    2.468015  0.041667 0.021669                 False               False            True
2023-04-01  2023      4  0.203196    2.429094  0.041667 0.021006                 False               False            True
2006-08-01  2006      8 -0.210702   -2.365691  0.041667 0.019945                 False               False            True
2010-09-01  2010      9  0.212810    2.336464  0.041667 0.019465                 False               False            True


**Univariate return outliers (MAD-z)**
      date  year  month    return     MAD_z  flag_|MADz|>3.5
2009-08-01  2009      8  0.303581  3.674429             True
2010-03-01  2010      3 -0.297034 -3.616211             True
2020-03-01  2020      3 -0.268772 -3.273149            False
2006-01-01  2006      1  0.234247  2.832805            False
2010-10-01  2010     10  0.227139  2.746524            False
2010-07-01  2010      7  0.226959  2.744347            False
2023-12-01  2023     12 -0.213002 -2.596178            False
2010-09-01  2010      9  0.212810  2.572593            False
2006-08-01  2006      8 -0.210702 -2.568264            False
2008-03-01  2008      3 -0.202048 -2.463210            False
