# ZS — Monthly Diagnostics

## Data

- Observations: **288**  
- Date range: **2001-01-01 — 2024-12-01**
- Average monthly volume: **70,199**  
- Minimum monthly volume: **4,537**


## Exploratory Analysis

**Summary of monthly returns**
 count     mean      std      skew  kurtosis_excess       min       q25   median      q75      max
   288 0.006405 0.070542 -0.016726         0.677064 -0.219524 -0.037535 0.002086 0.047612 0.211885


**Month vs. Rest-of-Year (Welch two-sample t-tests)**

 month  n(m)  n(rest)   mean(m)  mean(rest)  mean(m) − mean(rest)    t_stat  p_value sig
01-Jan    24      264 -0.005315    0.007471             -0.012786 -1.086856 0.285445    
02-Feb    24      264  0.032744    0.004011              0.028734  1.716989 0.097736   *
03-Mar    24      264 -0.003662    0.007320             -0.010982 -0.777192 0.443541    
04-Apr    24      264  0.021572    0.005026              0.016545  1.389929 0.174495    
05-May    24      264 -0.001246    0.007101             -0.008347 -0.540946 0.592964    
06-Jun    24      264  0.021616    0.005022              0.016593  0.971638 0.340125    
07-Jul    24      264 -0.015191    0.008369             -0.023560 -1.410404 0.170124    
08-Aug    24      264 -0.002879    0.007249             -0.010128 -0.664887 0.511716    
09-Sep    24      264 -0.022493    0.009032             -0.031526 -1.633006 0.114850    
10-Oct    24      264  0.021529    0.005030              0.016498  1.124455 0.270497    
11-Nov    24      264  0.006786    0.006371              0.000415  0.037379 0.970412    
12-Dec    24      264  0.023403    0.004860              0.018543  1.487691 0.147280    

_Interpretation:_ For each row, we test whether the average return in that month differs from the average return in all the other months. Small p-values (e.g., < 0.05) indicate the month’s average return is significantly different from the rest of the year.


## Stationarity

- **ADF**: stat = -7.603, p = 0.0000 ***, lags used = 8, n = 279  
  critical values: 1%=-3.454, 5%=-2.872, 10%=-2.572

- **KPSS (level)**: stat = 0.116, p = 0.1000 , lags used = 3  
  critical values: 10%=0.347, 5%=0.463, 2.5%=0.574, 1%=0.739

- **Joint read**: ADF rejects & KPSS does not reject → returns look stationary.


## Homoskedasticity (constant error variance)

- **Breusch–Pagan**: LM p = 0.1587, F p = 0.1580  
- **White**: LM p = 0.1587, F p = 0.1580
  *Interpretation:* **small p-values (<0.05)** suggest **heteroskedasticity conditional on the regressors** (here: month dummies).

- **ARCH LM (lags=12)**: LM p = 0.0534, F p = 0.0505  
  *Interpretation:* **small p-values (<0.05)** indicate **conditional heteroskedasticity** (volatility clustering) in residuals.

**Conclusion:** **Mixed/ambiguous** evidence (some tests borderline). Prefer **robust/HAC standard errors** to be safe.


## Structural Breaks

- **CUSUM (parameter stability test)**: p = 0.6505. CUSUM tracks cumulative sums of recursive residuals; **small p-values (< 0.05)** indicate **time-varying coefficients** (instability) rather than constant effects over the sample.

  **Interpretation:** No evidence against stability — parameters appear stable.

- **Chow (rolling break search)**: F = 0.99, p = 0.4610, break at index 174 (2015-07-01), left n = 174, right n = 114

  *What it does:* For each admissible split point, the test compares a **single-regime fit** to a **two-regime fit** (pre-break vs post-break). **Small p-values (< 0.05)** indicate a **structural break** at (or near) the reported split.

  **Interpretation:** No strong evidence of a single dominant break.


## Outliers

**Regression influence (top 10 by Cook’s D)**
      date  year  month    return  stud_resid  leverage  cooks_d  flag_studentized>|3|  flag_leverage>2k/n  flag_cooks>4/n
2008-03-01  2008      3 -0.219524   -3.208378  0.041667 0.036081                  True               False            True
2004-05-01  2004      5 -0.199016   -2.930674  0.041667 0.030286                 False               False            True
2003-09-01  2003      9  0.172727    2.891743  0.041667 0.029511                 False               False            True
2005-02-01  2005      2  0.211885    2.647206  0.041667 0.024849                 False               False            True
2018-06-01  2018      6 -0.156944   -2.638404  0.041667 0.024688                 False               False            True
2008-09-01  2008      9 -0.197389   -2.582927  0.041667 0.023685                 False               False            True
2011-09-01  2011      9 -0.189691   -2.466667  0.041667 0.021646                 False               False            True
2023-06-01  2023      6  0.185368    2.414751  0.041667 0.020763                 False               False            True
2004-07-01  2004      7 -0.176934   -2.384501  0.041667 0.020257                 False               False            True
2003-08-01  2003      8  0.157171    2.359039  0.041667 0.019835                 False               False            True


**Univariate return outliers (MAD-z)**
      date  year  month    return     MAD_z  flag_|MADz|>3.5
2008-03-01  2008      3 -0.219524 -3.621082             True
2005-02-01  2005      2  0.211885  3.428097            False
2004-05-01  2004      5 -0.199016 -3.285981            False
2008-09-01  2008      9 -0.197389 -3.259390            False
2011-09-01  2011      9 -0.189691 -3.133607            False
2008-02-01  2008      2  0.188781  3.050586            False
2023-06-01  2023      6  0.185368  2.994809            False
2004-07-01  2004      7 -0.176934 -2.925165            False
2003-10-01  2003     10  0.174890  2.823594            False
2008-06-01  2008      6  0.173059  2.793688            False
