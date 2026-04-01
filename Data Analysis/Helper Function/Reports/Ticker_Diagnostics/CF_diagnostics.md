# CF — Monthly Diagnostics

## Data

- Observations: **288**  
- Date range: **2001-01-01 — 2024-12-01**
- Average monthly volume: **15,205**  
- Minimum monthly volume: **4,063**


## Exploratory Analysis

**Summary of monthly returns**
 count    mean      std     skew  kurtosis_excess       min       q25    median     q75      max
   288 0.00121 0.088785 0.793819         1.771229 -0.229047 -0.056002 -0.011786 0.04282 0.419685


**Month vs. Rest-of-Year (Welch two-sample t-tests)**

 month  n(m)  n(rest)   mean(m)  mean(rest)  mean(m) − mean(rest)    t_stat  p_value sig
01-Jan    24      264  0.014492    0.000003              0.014489  0.858981 0.397440    
02-Feb    24      264  0.008731    0.000527              0.008204  0.332313 0.742395    
03-Mar    24      264 -0.019897    0.003129             -0.023026 -1.262715 0.217194    
04-Apr    24      264  0.008915    0.000510              0.008405  0.488698 0.628788    
05-May    24      264 -0.011233    0.002342             -0.013575 -0.615051 0.543871    
06-Jun    24      264 -0.010151    0.002243             -0.012394 -0.641965 0.526279    
07-Jul    24      264 -0.000832    0.001396             -0.002228 -0.130782 0.896861    
08-Aug    24      264  0.002337    0.001108              0.001229  0.081644 0.935458    
09-Sep    24      264 -0.010673    0.002291             -0.012963 -0.750422 0.459156    
10-Oct    24      264 -0.009596    0.002193             -0.011789 -0.572207 0.571998    
11-Nov    24      264  0.038763   -0.002204              0.040967  1.926730 0.064920   *
12-Dec    24      264  0.003668    0.000987              0.002681  0.164633 0.870361    

_Interpretation:_ For each row, we test whether the average return in that month differs from the average return in all the other months. Small p-values (e.g., < 0.05) indicate the month’s average return is significantly different from the rest of the year.


## Stationarity

- **ADF**: stat = -18.306, p = 0.0000 ***, lags used = 0, n = 287  
  critical values: 1%=-3.453, 5%=-2.872, 10%=-2.572

- **KPSS (level)**: stat = 0.172, p = 0.1000 , lags used = 4  
  critical values: 10%=0.347, 5%=0.463, 2.5%=0.574, 1%=0.739

- **Joint read**: ADF rejects & KPSS does not reject → returns look stationary.


## Homoskedasticity (constant error variance)

- **Breusch–Pagan**: LM p = 0.6752, F p = 0.6842  
- **White**: LM p = 0.6752, F p = 0.6842
  *Interpretation:* **small p-values (<0.05)** suggest **heteroskedasticity conditional on the regressors** (here: month dummies).

- **ARCH LM (lags=12)**: LM p = 0.9964, F p = 0.9968  
  *Interpretation:* **small p-values (<0.05)** indicate **conditional heteroskedasticity** (volatility clustering) in residuals.

**Conclusion:** Tests **do not suggest heteroskedasticity** at conventional levels. A homoskedastic assumption appears reasonable; **robust SEs** are still a conservative default in finance.


## Structural Breaks

- **CUSUM (parameter stability test)**: p = 0.4128. CUSUM tracks cumulative sums of recursive residuals; **small p-values (< 0.05)** indicate **time-varying coefficients** (instability) rather than constant effects over the sample.

  **Interpretation:** No evidence against stability — parameters appear stable.

- **Chow (rolling break search)**: F = 1.64, p = 0.0818, break at index 234 (2020-07-01), left n = 234, right n = 54

  *What it does:* For each admissible split point, the test compares a **single-regime fit** to a **two-regime fit** (pre-break vs post-break). **Small p-values (< 0.05)** indicate a **structural break** at (or near) the reported split.

  **Interpretation:** Weak or borderline evidence of a break.


## Outliers

**Regression influence (top 10 by Cook’s D)**
      date  year  month    return  stud_resid  leverage  cooks_d  flag_studentized>|3|  flag_leverage>2k/n  flag_cooks>4/n
2014-02-01  2014      2  0.419685    4.896517  0.041667 0.080193                  True               False            True
2024-11-01  2024     11  0.295256    2.977886  0.041667 0.031239                 False               False            True
2002-03-01  2002      3  0.230108    2.900229  0.041667 0.029679                 False               False            True
2004-05-01  2004      5  0.235379    2.859698  0.041667 0.028879                 False               False            True
2010-06-01  2010      6  0.223534    2.705691  0.041667 0.025931                 False               False            True
2002-10-01  2002     10  0.215668    2.605738  0.041667 0.024096                 False               False            True
2020-01-01  2020      1 -0.207642   -2.568652  0.041667 0.023430                 False               False            True
2004-11-01  2004     11  0.257419    2.527495  0.041667 0.022703                 False               False            True
2008-03-01  2008      3 -0.229047   -2.415227  0.041667 0.020771                 False               False            True
2009-05-01  2009      5  0.187554    2.293210  0.041667 0.018764                 False               False            True


**Univariate return outliers (MAD-z)**
      date  year  month    return     MAD_z  flag_|MADz|>3.5
2014-02-01  2014      2  0.419685  5.840440             True
2024-11-01  2024     11  0.295256  4.156150             True
2004-11-01  2004     11  0.257419  3.643996             True
2004-05-01  2004      5  0.235379  3.345656            False
2002-03-01  2002      3  0.230108  3.274300            False
2010-06-01  2010      6  0.223534  3.185317            False
2002-10-01  2002     10  0.215668  3.078848            False
2008-03-01  2008      3 -0.229047 -2.940859            False
2010-12-01  2010     12  0.194438  2.791468            False
2009-05-01  2009      5  0.187554  2.698292            False
