# SV — Monthly Diagnostics

## Data

- Observations: **288**  
- Date range: **2001-01-01 — 2024-12-01**
- Average monthly volume: **32,004**  
- Minimum monthly volume: **1,531**


## Exploratory Analysis

**Summary of monthly returns**
 count     mean     std     skew  kurtosis_excess       min       q25    median      q75      max
   288 0.006922 0.08782 0.252954         0.773671 -0.276068 -0.050385 -0.002182 0.060646 0.305332


**Month vs. Rest-of-Year (Welch two-sample t-tests)**

 month  n(m)  n(rest)   mean(m)  mean(rest)  mean(m) − mean(rest)    t_stat  p_value sig
01-Jan    24      264  0.026356    0.005156              0.021201  1.421383 0.165275    
02-Feb    24      264  0.014540    0.006230              0.008310  0.460429 0.648791    
03-Mar    24      264  0.006334    0.006976             -0.000642 -0.034422 0.972790    
04-Apr    24      264  0.006107    0.006996             -0.000890 -0.040009 0.968393    
05-May    24      264  0.005135    0.007085             -0.001950 -0.088063 0.930505    
06-Jun    24      264 -0.030012    0.010280             -0.040292 -2.582584 0.014971  **
07-Jul    24      264  0.033552    0.004501              0.029051  1.659458 0.108139    
08-Aug    24      264  0.003665    0.007218             -0.003554 -0.173803 0.863341    
09-Sep    24      264 -0.012475    0.008686             -0.021161 -0.937705 0.357113    
10-Oct    24      264  0.008075    0.006818              0.001257  0.078165 0.938226    
11-Nov    24      264  0.009212    0.006714              0.002498  0.148111 0.883292    
12-Dec    24      264  0.012580    0.006408              0.006171  0.339087 0.737103    

_Interpretation:_ For each row, we test whether the average return in that month differs from the average return in all the other months. Small p-values (e.g., < 0.05) indicate the month’s average return is significantly different from the rest of the year.


## Stationarity

- **ADF**: stat = -17.640, p = 0.0000 ***, lags used = 0, n = 287  
  critical values: 1%=-3.453, 5%=-2.872, 10%=-2.572

- **KPSS (level)**: stat = 0.135, p = 0.1000 , lags used = 5  
  critical values: 10%=0.347, 5%=0.463, 2.5%=0.574, 1%=0.739

- **Joint read**: ADF rejects & KPSS does not reject → returns look stationary.


## Homoskedasticity (constant error variance)

- **Breusch–Pagan**: LM p = 0.5403, F p = 0.5484  
- **White**: LM p = 0.5403, F p = 0.5484
  *Interpretation:* **small p-values (<0.05)** suggest **heteroskedasticity conditional on the regressors** (here: month dummies).

- **ARCH LM (lags=12)**: LM p = 0.0015, F p = 0.0011  
  *Interpretation:* **small p-values (<0.05)** indicate **conditional heteroskedasticity** (volatility clustering) in residuals.

**Conclusion:** Evidence of **heteroskedasticity**. Report **robust standard errors** (HC3; and **HAC/Newey–West** if serial correlation is present).


## Structural Breaks

- **CUSUM (parameter stability test)**: p = 0.2359. CUSUM tracks cumulative sums of recursive residuals; **small p-values (< 0.05)** indicate **time-varying coefficients** (instability) rather than constant effects over the sample.

  **Interpretation:** No evidence against stability — parameters appear stable.

- **Chow (rolling break search)**: F = 1.69, p = 0.0699, break at index 123 (2011-04-01), left n = 123, right n = 165

  *What it does:* For each admissible split point, the test compares a **single-regime fit** to a **two-regime fit** (pre-break vs post-break). **Small p-values (< 0.05)** indicate a **structural break** at (or near) the reported split.

  **Interpretation:** Weak or borderline evidence of a break.


## Outliers

**Regression influence (top 10 by Cook’s D)**
      date  year  month    return  stud_resid  leverage  cooks_d  flag_studentized>|3|  flag_leverage>2k/n  flag_cooks>4/n
2011-04-01  2011      4  0.305332    3.540642  0.041667 0.043598                  True               False            True
2020-07-01  2020      7  0.303337    3.178744  0.041667 0.035441                  True               False            True
2011-09-01  2011      9 -0.276068   -3.103204  0.041667 0.033833                  True               False            True
2009-05-01  2009      5  0.261924    3.020416  0.041667 0.032109                  True               False            True
2004-04-01  2004      4 -0.233480   -2.812040  0.041667 0.027951                 False               False            True
2008-08-01  2008      8 -0.234673   -2.796970  0.041667 0.027661                 False               False            True
2020-05-01  2020      5  0.227131    2.600299  0.041667 0.023997                 False               False            True
2011-05-01  2011      5 -0.203473   -2.439963  0.041667 0.021190                 False               False            True
2008-10-01  2008     10 -0.198187   -2.411945  0.041667 0.020716                 False               False            True
2011-02-01  2011      2  0.204630    2.219313  0.041667 0.017595                 False               False            True


**Univariate return outliers (MAD-z)**
      date  year  month    return     MAD_z  flag_|MADz|>3.5
2011-04-01  2011      4  0.305332  3.796533             True
2020-07-01  2020      7  0.303337  3.771897             True
2011-09-01  2011      9 -0.276068 -3.381348            False
2009-05-01  2009      5  0.261924  3.260619            False
2008-08-01  2008      8 -0.234673 -2.870298            False
2004-04-01  2004      4 -0.233480 -2.855567            False
2020-05-01  2020      5  0.227131  2.831070            False
2011-02-01  2011      2  0.204630  2.553282            False
2011-05-01  2011      5 -0.203473 -2.485098            False
2008-10-01  2008     10 -0.198187 -2.419843            False
