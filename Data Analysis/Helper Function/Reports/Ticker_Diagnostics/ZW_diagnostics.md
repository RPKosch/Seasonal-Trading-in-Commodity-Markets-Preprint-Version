# ZW — Monthly Diagnostics

## Data

- Observations: **288**  
- Date range: **2001-01-01 — 2024-12-01**
- Average monthly volume: **46,260**  
- Minimum monthly volume: **6,957**


## Exploratory Analysis

**Summary of monthly returns**
 count      mean      std     skew  kurtosis_excess       min       q25    median      q75      max
   288 -0.005072 0.085042 0.422424         1.501921 -0.262826 -0.063264 -0.008137 0.044768 0.380282


**Month vs. Rest-of-Year (Welch two-sample t-tests)**

 month  n(m)  n(rest)   mean(m)  mean(rest)  mean(m) − mean(rest)    t_stat  p_value sig
01-Jan    24      264 -0.016958   -0.003992             -0.012967 -1.067639 0.292913    
02-Feb    24      264  0.004430   -0.005936              0.010366  0.569787 0.573477    
03-Mar    24      264 -0.023497   -0.003397             -0.020100 -1.290939 0.206794    
04-Apr    24      264 -0.000672   -0.005472              0.004800  0.292314 0.772157    
05-May    24      264  0.007140   -0.006182              0.013323  0.715362 0.480508    
06-Jun    24      264 -0.017768   -0.003918             -0.013850 -0.516600 0.610042    
07-Jul    24      264  0.012200   -0.006642              0.018842  0.754235 0.457766    
08-Aug    24      264 -0.010849   -0.004547             -0.006302 -0.390067 0.699354    
09-Sep    24      264 -0.004238   -0.005148              0.000910  0.046222 0.963479    
10-Oct    24      264  0.003802   -0.005879              0.009681  0.580024 0.566477    
11-Nov    24      264 -0.012954   -0.004356             -0.008598 -0.609736 0.546442    
12-Dec    24      264 -0.001501   -0.005397              0.003896  0.261095 0.795795    

_Interpretation:_ For each row, we test whether the average return in that month differs from the average return in all the other months. Small p-values (e.g., < 0.05) indicate the month’s average return is significantly different from the rest of the year.


## Stationarity

- **ADF**: stat = -13.981, p = 0.0000 ***, lags used = 1, n = 286  
  critical values: 1%=-3.453, 5%=-2.872, 10%=-2.572

- **KPSS (level)**: stat = 0.058, p = 0.1000 , lags used = 5  
  critical values: 10%=0.347, 5%=0.463, 2.5%=0.574, 1%=0.739

- **Joint read**: ADF rejects & KPSS does not reject → returns look stationary.


## Homoskedasticity (constant error variance)

- **Breusch–Pagan**: LM p = 0.0099, F p = 0.0085  
- **White**: LM p = 0.0099, F p = 0.0085
  *Interpretation:* **small p-values (<0.05)** suggest **heteroskedasticity conditional on the regressors** (here: month dummies).

- **ARCH LM (lags=12)**: LM p = 0.7683, F p = 0.7777  
  *Interpretation:* **small p-values (<0.05)** indicate **conditional heteroskedasticity** (volatility clustering) in residuals.

**Conclusion:** Evidence of **heteroskedasticity**. Report **robust standard errors** (HC3; and **HAC/Newey–West** if serial correlation is present).


## Structural Breaks

- **CUSUM (parameter stability test)**: p = 0.7954. CUSUM tracks cumulative sums of recursive residuals; **small p-values (< 0.05)** indicate **time-varying coefficients** (instability) rather than constant effects over the sample.

  **Interpretation:** No evidence against stability — parameters appear stable.

- **Chow (rolling break search)**: F = 1.38, p = 0.1734, break at index 139 (2012-08-01), left n = 139, right n = 149

  *What it does:* For each admissible split point, the test compares a **single-regime fit** to a **two-regime fit** (pre-break vs post-break). **Small p-values (< 0.05)** indicate a **structural break** at (or near) the reported split.

  **Interpretation:** No strong evidence of a single dominant break.


## Outliers

**Regression influence (top 10 by Cook’s D)**
      date  year  month    return  stud_resid  leverage  cooks_d  flag_studentized>|3|  flag_leverage>2k/n  flag_cooks>4/n
2010-07-01  2010      7  0.380282    4.522364  0.041667 0.069222                  True               False            True
2015-06-01  2015      6  0.272211    3.513549  0.041667 0.042962                  True               False            True
2011-06-01  2011      6 -0.262826   -2.950395  0.041667 0.030683                 False               False            True
2011-09-01  2011      9 -0.227330   -2.678681  0.041667 0.025429                 False               False            True
2008-10-01  2008     10 -0.215148   -2.627687  0.041667 0.024493                 False               False            True
2022-02-01  2022      2  0.216938    2.548529  0.041667 0.023073                 False               False            True
2007-08-01  2007      8  0.194915    2.465834  0.041667 0.021632                 False               False            True
2017-06-01  2017      6  0.184685    2.425289  0.041667 0.020941                 False               False            True
2015-07-01  2015      7 -0.187551   -2.392240  0.041667 0.020386                 False               False            True
2021-04-01  2021      4  0.185081    2.221477  0.041667 0.017629                 False               False            True


**Univariate return outliers (MAD-z)**
      date  year  month    return     MAD_z  flag_|MADz|>3.5
2010-07-01  2010      7  0.380282  4.764805             True
2015-06-01  2015      6  0.272211  3.439077            False
2011-06-01  2011      6 -0.262826 -3.124326            False
2022-02-01  2022      2  0.216938  2.761037            False
2011-09-01  2011      9 -0.227330 -2.688891            False
2008-10-01  2008     10 -0.215148 -2.539450            False
2007-08-01  2007      8  0.194915  2.490879            False
2009-05-01  2009      5  0.186685  2.389920            False
2021-04-01  2021      4  0.185081  2.370235            False
2017-06-01  2017      6  0.184685  2.365378            False
