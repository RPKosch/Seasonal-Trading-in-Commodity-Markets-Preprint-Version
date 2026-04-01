# HO — Monthly Diagnostics

## Data

- Observations: **288**  
- Date range: **2001-01-01 — 2024-12-01**
- Average monthly volume: **35,576**  
- Minimum monthly volume: **9,283**


## Exploratory Analysis

**Summary of monthly returns**
 count     mean     std      skew  kurtosis_excess       min       q25   median      q75      max
   288 0.006581 0.08987 -0.099931         0.670423 -0.314933 -0.045664 0.004328 0.061726 0.275128


**Month vs. Rest-of-Year (Welch two-sample t-tests)**

 month  n(m)  n(rest)   mean(m)  mean(rest)  mean(m) − mean(rest)    t_stat  p_value sig
01-Jan    24      264  0.008504    0.006407              0.002098  0.115670 0.908739    
02-Feb    24      264  0.037579    0.003763              0.033815  1.737617 0.093612   *
03-Mar    24      264  0.009201    0.006343              0.002857  0.126286 0.900481    
04-Apr    24      264  0.022121    0.005169              0.016953  0.830575 0.413573    
05-May    24      264  0.010441    0.006231              0.004210  0.211800 0.833854    
06-Jun    24      264  0.023378    0.005054              0.018323  1.583703 0.121189    
07-Jul    24      264  0.003616    0.006851             -0.003235 -0.177066 0.860735    
08-Aug    24      264  0.015216    0.005796              0.009420  0.600481 0.552661    
09-Sep    24      264 -0.005906    0.007717             -0.013623 -0.682770 0.500581    
10-Oct    24      264 -0.016317    0.008663             -0.024980 -1.230642 0.229165    
11-Nov    24      264 -0.028487    0.009769             -0.038256 -1.835213 0.077728   *
12-Dec    24      264 -0.000369    0.007213             -0.007582 -0.368981 0.715055    

_Interpretation:_ For each row, we test whether the average return in that month differs from the average return in all the other months. Small p-values (e.g., < 0.05) indicate the month’s average return is significantly different from the rest of the year.


## Stationarity

- **ADF**: stat = -13.883, p = 0.0000 ***, lags used = 0, n = 287  
  critical values: 1%=-3.453, 5%=-2.872, 10%=-2.572

- **KPSS (level)**: stat = 0.084, p = 0.1000 , lags used = 3  
  critical values: 10%=0.347, 5%=0.463, 2.5%=0.574, 1%=0.739

- **Joint read**: ADF rejects & KPSS does not reject → returns look stationary.


## Homoskedasticity (constant error variance)

- **Breusch–Pagan**: LM p = 0.6823, F p = 0.6912  
- **White**: LM p = 0.6823, F p = 0.6912
  *Interpretation:* **small p-values (<0.05)** suggest **heteroskedasticity conditional on the regressors** (here: month dummies).

- **ARCH LM (lags=12)**: LM p = 0.0521, F p = 0.0491  
  *Interpretation:* **small p-values (<0.05)** indicate **conditional heteroskedasticity** (volatility clustering) in residuals.

**Conclusion:** Evidence of **heteroskedasticity**. Report **robust standard errors** (HC3; and **HAC/Newey–West** if serial correlation is present).


## Structural Breaks

- **CUSUM (parameter stability test)**: p = 0.4780. CUSUM tracks cumulative sums of recursive residuals; **small p-values (< 0.05)** indicate **time-varying coefficients** (instability) rather than constant effects over the sample.

  **Interpretation:** No evidence against stability — parameters appear stable.

- **Chow (rolling break search)**: F = 0.95, p = 0.4937, break at index 229 (2020-02-01), left n = 229, right n = 59

  *What it does:* For each admissible split point, the test compares a **single-regime fit** to a **two-regime fit** (pre-break vs post-break). **Small p-values (< 0.05)** indicate a **structural break** at (or near) the reported split.

  **Interpretation:** No strong evidence of a single dominant break.


## Outliers

**Regression influence (top 10 by Cook’s D)**
      date  year  month    return  stud_resid  leverage  cooks_d  flag_studentized>|3|  flag_leverage>2k/n  flag_cooks>4/n
2020-03-01  2020      3 -0.314933   -3.768304  0.041667 0.049101                  True               False            True
2020-11-01  2020     11  0.275128    3.518637  0.041667 0.043082                  True               False            True
2008-10-01  2008     10 -0.289421   -3.151524  0.041667 0.034858                  True               False            True
2004-09-01  2004      9  0.218549    2.575097  0.041667 0.023545                 False               False            True
2009-05-01  2009      5  0.233802    2.562237  0.041667 0.023316                 False               False            True
2023-07-01  2023      7  0.226028    2.551089  0.041667 0.023119                 False               False            True
2022-04-01  2022      4  0.241831    2.519381  0.041667 0.022560                 False               False            True
2020-01-01  2020      1 -0.196169   -2.343391  0.041667 0.019578                 False               False            True
2020-04-01  2020      4 -0.172691   -2.228395  0.041667 0.017737                 False               False            True
2005-08-01  2005      8  0.204396    2.162859  0.041667 0.016726                 False               False            True


**Univariate return outliers (MAD-z)**
      date  year  month    return     MAD_z  flag_|MADz|>3.5
2020-03-01  2020      3 -0.314933 -3.827082             True
2008-10-01  2008     10 -0.289421 -3.521262             True
2020-11-01  2020     11  0.275128  3.246163            False
2022-04-01  2022      4  0.241831  2.847026            False
2009-05-01  2009      5  0.233802  2.750772            False
2023-07-01  2023      7  0.226028  2.657583            False
2003-02-01  2003      2  0.221210  2.599829            False
2004-09-01  2004      9  0.218549  2.567939            False
2020-01-01  2020      1 -0.196169 -2.403429            False
2005-08-01  2005      8  0.204396  2.398283            False
