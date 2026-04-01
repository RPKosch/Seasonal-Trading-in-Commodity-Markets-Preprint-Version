# ZC — Monthly Diagnostics

## Data

- Observations: **288**  
- Date range: **2001-01-01 — 2024-12-01**
- Average monthly volume: **115,990**  
- Minimum monthly volume: **11,093**


## Exploratory Analysis

**Summary of monthly returns**
 count      mean     std     skew  kurtosis_excess       min       q25    median      q75      max
   288 -0.001433 0.07676 0.304117         0.744127 -0.226501 -0.044496 -0.007558 0.044903 0.255253


**Month vs. Rest-of-Year (Welch two-sample t-tests)**

 month  n(m)  n(rest)   mean(m)  mean(rest)  mean(m) − mean(rest)    t_stat  p_value sig
01-Jan    24      264  0.006889   -0.002189              0.009078  0.650742 0.520247    
02-Feb    24      264  0.014433   -0.002875              0.017309  1.362436 0.182792    
03-Mar    24      264 -0.007722   -0.000861             -0.006860 -0.501188 0.619915    
04-Apr    24      264  0.006604   -0.002164              0.008768  0.634359 0.530706    
05-May    24      264 -0.000729   -0.001497              0.000768  0.054325 0.957045    
06-Jun    24      264 -0.030900    0.001246             -0.032146 -1.440578 0.162119    
07-Jul    24      264 -0.026068    0.000807             -0.026874 -1.250166 0.222727    
08-Aug    24      264 -0.001756   -0.001403             -0.000353 -0.022762 0.982002    
09-Sep    24      264 -0.019468    0.000207             -0.019675 -1.066466 0.295932    
10-Oct    24      264  0.028110   -0.004119              0.032229  1.764951 0.089194   *
11-Nov    24      264 -0.010930   -0.000570             -0.010360 -0.776830 0.443279    
12-Dec    24      264  0.024342   -0.003776              0.028118  2.107513 0.043474  **

_Interpretation:_ For each row, we test whether the average return in that month differs from the average return in all the other months. Small p-values (e.g., < 0.05) indicate the month’s average return is significantly different from the rest of the year.


## Stationarity

- **ADF**: stat = -10.638, p = 0.0000 ***, lags used = 1, n = 286  
  critical values: 1%=-3.453, 5%=-2.872, 10%=-2.572

- **KPSS (level)**: stat = 0.049, p = 0.1000 , lags used = 2  
  critical values: 10%=0.347, 5%=0.463, 2.5%=0.574, 1%=0.739

- **Joint read**: ADF rejects & KPSS does not reject → returns look stationary.


## Homoskedasticity (constant error variance)

- **Breusch–Pagan**: LM p = 0.0396, F p = 0.0372  
- **White**: LM p = 0.0396, F p = 0.0372
  *Interpretation:* **small p-values (<0.05)** suggest **heteroskedasticity conditional on the regressors** (here: month dummies).

- **ARCH LM (lags=12)**: LM p = 0.0024, F p = 0.0017  
  *Interpretation:* **small p-values (<0.05)** indicate **conditional heteroskedasticity** (volatility clustering) in residuals.

**Conclusion:** Evidence of **heteroskedasticity**. Report **robust standard errors** (HC3; and **HAC/Newey–West** if serial correlation is present).


## Structural Breaks

- **CUSUM (parameter stability test)**: p = 0.9599. CUSUM tracks cumulative sums of recursive residuals; **small p-values (< 0.05)** indicate **time-varying coefficients** (instability) rather than constant effects over the sample.

  **Interpretation:** No evidence against stability — parameters appear stable.

- **Chow (rolling break search)**: F = 1.24, p = 0.2564, break at index 174 (2015-07-01), left n = 174, right n = 114

  *What it does:* For each admissible split point, the test compares a **single-regime fit** to a **two-regime fit** (pre-break vs post-break). **Small p-values (< 0.05)** indicate a **structural break** at (or near) the reported split.

  **Interpretation:** No strong evidence of a single dominant break.


## Outliers

**Regression influence (top 10 by Cook’s D)**
      date  year  month    return  stud_resid  leverage  cooks_d  flag_studentized>|3|  flag_leverage>2k/n  flag_cooks>4/n
2012-07-01  2012      7  0.255253    3.870496  0.041667 0.051661                  True               False            True
2008-06-01  2008      6  0.207941    3.261345  0.041667 0.037237                  True               False            True
2012-06-01  2012      6  0.195435    3.084500  0.041667 0.033440                  True               False            True
2015-06-01  2015      6  0.184561    2.931562  0.041667 0.030304                 False               False            True
2008-10-01  2008     10 -0.187247   -2.930099  0.041667 0.030275                 False               False            True
2011-09-01  2011      9 -0.226501   -2.813510  0.041667 0.027979                 False               False            True
2021-04-01  2021      4  0.210881    2.775005  0.041667 0.027240                 False               False            True
2006-10-01  2006     10  0.218424    2.580544  0.041667 0.023643                 False               False            True
2019-05-01  2019      5  0.177931    2.419064  0.041667 0.020836                 False               False            True
2006-11-01  2006     11  0.167414    2.414684  0.041667 0.020762                 False               False            True


**Univariate return outliers (MAD-z)**
      date  year  month    return     MAD_z  flag_|MADz|>3.5
2012-07-01  2012      7  0.255253  4.069229             True
2006-10-01  2006     10  0.218424  3.498981            False
2011-09-01  2011      9 -0.226501 -3.390019            False
2021-04-01  2021      4  0.210881  3.382200            False
2008-06-01  2008      6  0.207941  3.336675            False
2012-06-01  2012      6  0.195435  3.143039            False
2009-06-01  2009      6 -0.201577 -3.004097            False
2015-06-01  2015      6  0.184561  2.974676            False
2008-07-01  2008      7 -0.198499 -2.956450            False
2019-05-01  2019      5  0.177931  2.872014            False
