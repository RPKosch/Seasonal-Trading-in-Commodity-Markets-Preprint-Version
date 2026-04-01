# CT — Monthly Diagnostics

## Data

- Observations: **288**  
- Date range: **2001-01-01 — 2024-12-01**
- Average monthly volume: **13,669**  
- Minimum monthly volume: **1,061**


## Exploratory Analysis

**Summary of monthly returns**
 count      mean      std     skew  kurtosis_excess       min       q25  median      q75      max
   288 -0.000752 0.082275 0.063001           0.6421 -0.244779 -0.050277 0.00403 0.046799 0.252632


**Month vs. Rest-of-Year (Welch two-sample t-tests)**

 month  n(m)  n(rest)   mean(m)  mean(rest)  mean(m) − mean(rest)    t_stat  p_value sig
01-Jan    24      264  0.015594   -0.002238              0.017832  1.427447 0.162727    
02-Feb    24      264  0.018439   -0.002496              0.020935  1.083766 0.288260    
03-Mar    24      264 -0.015103    0.000553             -0.015656 -0.872879 0.390392    
04-Apr    24      264 -0.004539   -0.000407             -0.004132 -0.224868 0.823781    
05-May    24      264 -0.031666    0.002059             -0.033724 -1.995596 0.055890   *
06-Jun    24      264 -0.008103   -0.000083             -0.008020 -0.447691 0.657929    
07-Jul    24      264 -0.011093    0.000189             -0.011281 -0.739486 0.465517    
08-Aug    24      264  0.008854   -0.001625              0.010479  0.616512 0.542587    
09-Sep    24      264 -0.014417    0.000491             -0.014908 -0.680630 0.502236    
10-Oct    24      264 -0.002512   -0.000591             -0.001921 -0.093269 0.926407    
11-Nov    24      264  0.008884   -0.001628              0.010512  0.629780 0.533955    
12-Dec    24      264  0.026643   -0.003242              0.029885  2.148758 0.039637  **

_Interpretation:_ For each row, we test whether the average return in that month differs from the average return in all the other months. Small p-values (e.g., < 0.05) indicate the month’s average return is significantly different from the rest of the year.


## Stationarity

- **ADF**: stat = -6.945, p = 0.0000 ***, lags used = 8, n = 279  
  critical values: 1%=-3.454, 5%=-2.872, 10%=-2.572

- **KPSS (level)**: stat = 0.116, p = 0.1000 , lags used = 4  
  critical values: 10%=0.347, 5%=0.463, 2.5%=0.574, 1%=0.739

- **Joint read**: ADF rejects & KPSS does not reject → returns look stationary.


## Homoskedasticity (constant error variance)

- **Breusch–Pagan**: LM p = 0.4044, F p = 0.4101  
- **White**: LM p = 0.4044, F p = 0.4101
  *Interpretation:* **small p-values (<0.05)** suggest **heteroskedasticity conditional on the regressors** (here: month dummies).

- **ARCH LM (lags=12)**: LM p = 0.0007, F p = 0.0005  
  *Interpretation:* **small p-values (<0.05)** indicate **conditional heteroskedasticity** (volatility clustering) in residuals.

**Conclusion:** Evidence of **heteroskedasticity**. Report **robust standard errors** (HC3; and **HAC/Newey–West** if serial correlation is present).


## Structural Breaks

- **CUSUM (parameter stability test)**: p = 0.3054. CUSUM tracks cumulative sums of recursive residuals; **small p-values (< 0.05)** indicate **time-varying coefficients** (instability) rather than constant effects over the sample.

  **Interpretation:** No evidence against stability — parameters appear stable.

- **Chow (rolling break search)**: F = 1.36, p = 0.1864, break at index 25 (2003-02-01), left n = 25, right n = 263

  *What it does:* For each admissible split point, the test compares a **single-regime fit** to a **two-regime fit** (pre-break vs post-break). **Small p-values (< 0.05)** indicate a **structural break** at (or near) the reported split.

  **Interpretation:** No strong evidence of a single dominant break.


## Outliers

**Regression influence (top 10 by Cook’s D)**
      date  year  month    return  stud_resid  leverage  cooks_d  flag_studentized>|3|  flag_leverage>2k/n  flag_cooks>4/n
2001-11-01  2001     11  0.252632    3.072116  0.041667 0.033181                  True               False            True
2008-10-01  2008     10 -0.236511   -2.945289  0.041667 0.030580                 False               False            True
2010-10-01  2010     10  0.229244    2.916189  0.041667 0.029997                 False               False            True
2022-09-01  2022      9 -0.244779   -2.898104  0.041667 0.029637                 False               False            True
2010-12-01  2010     12  0.234106    2.602524  0.041667 0.024038                 False               False            True
2004-08-01  2004      8  0.213393    2.564956  0.041667 0.023365                 False               False            True
2010-09-01  2010      9  0.182367    2.465507  0.041667 0.021626                 False               False            True
2022-06-01  2022      6 -0.192813   -2.311202  0.041667 0.019054                 False               False            True
2011-04-01  2011      4 -0.182852   -2.229691  0.041667 0.017757                 False               False            True
2009-04-01  2009      4  0.168566    2.163439  0.041667 0.016735                 False               False            True


**Univariate return outliers (MAD-z)**
      date  year  month    return     MAD_z  flag_|MADz|>3.5
2022-09-01  2022      9 -0.244779 -3.457726            False
2001-11-01  2001     11  0.252632  3.454854            False
2008-10-01  2008     10 -0.236511 -3.342827            False
2010-12-01  2010     12  0.234106  3.197402            False
2010-10-01  2010     10  0.229244  3.129839            False
2004-08-01  2004      8  0.213393  2.909548            False
2012-05-01  2012      5 -0.199664 -2.830766            False
2022-06-01  2022      6 -0.192813 -2.735556            False
2011-04-01  2011      4 -0.182852 -2.597127            False
2020-03-01  2020      3 -0.181265 -2.575066            False
