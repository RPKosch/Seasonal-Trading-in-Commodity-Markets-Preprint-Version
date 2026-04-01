# GD — Monthly Diagnostics

## Data

- Observations: **288**  
- Date range: **2001-01-01 — 2024-12-01**
- Average monthly volume: **86,320**  
- Minimum monthly volume: **1,084**


## Exploratory Analysis

**Summary of monthly returns**
 count     mean      std      skew  kurtosis_excess       min       q25   median      q75      max
   288 0.006626 0.046312 -0.081465         0.579027 -0.180978 -0.023208 0.004604 0.035311 0.128779


**Month vs. Rest-of-Year (Welch two-sample t-tests)**

 month  n(m)  n(rest)   mean(m)  mean(rest)  mean(m) − mean(rest)    t_stat  p_value sig
01-Jan    24      264  0.022982    0.005139              0.017843  1.777968 0.086617   *
02-Feb    24      264  0.005431    0.006734             -0.001303 -0.133556 0.894726    
03-Mar    24      264  0.000317    0.007199             -0.006882 -0.860367 0.396316    
04-Apr    24      264  0.009447    0.006369              0.003078  0.306426 0.761615    
05-May    24      264  0.002051    0.007042             -0.004991 -0.527473 0.602042    
06-Jun    24      264 -0.006727    0.007840             -0.014567 -1.409667 0.170109    
07-Jul    24      264  0.007651    0.006533              0.001118  0.132589 0.895419    
08-Aug    24      264  0.016575    0.005721              0.010854  1.130811 0.267843    
09-Sep    24      264  0.002855    0.006968             -0.004114 -0.365929 0.717360    
10-Oct    24      264  0.000376    0.007194             -0.006817 -0.646804 0.523279    
11-Nov    24      264  0.007269    0.006567              0.000702  0.062744 0.950446    
12-Dec    24      264  0.011281    0.006203              0.005078  0.508923 0.614902    

_Interpretation:_ For each row, we test whether the average return in that month differs from the average return in all the other months. Small p-values (e.g., < 0.05) indicate the month’s average return is significantly different from the rest of the year.


## Stationarity

- **ADF**: stat = -18.722, p = 0.0000 ***, lags used = 0, n = 287  
  critical values: 1%=-3.453, 5%=-2.872, 10%=-2.572

- **KPSS (level)**: stat = 0.206, p = 0.1000 , lags used = 5  
  critical values: 10%=0.347, 5%=0.463, 2.5%=0.574, 1%=0.739

- **Joint read**: ADF rejects & KPSS does not reject → returns look stationary.


## Homoskedasticity (constant error variance)

- **Breusch–Pagan**: LM p = 0.9515, F p = 0.9547  
- **White**: LM p = 0.9515, F p = 0.9547
  *Interpretation:* **small p-values (<0.05)** suggest **heteroskedasticity conditional on the regressors** (here: month dummies).

- **ARCH LM (lags=12)**: LM p = 0.0612, F p = 0.0583  
  *Interpretation:* **small p-values (<0.05)** indicate **conditional heteroskedasticity** (volatility clustering) in residuals.

**Conclusion:** **Mixed/ambiguous** evidence (some tests borderline). Prefer **robust/HAC standard errors** to be safe.


## Structural Breaks

- **CUSUM (parameter stability test)**: p = 0.1646. CUSUM tracks cumulative sums of recursive residuals; **small p-values (< 0.05)** indicate **time-varying coefficients** (instability) rather than constant effects over the sample.

  **Interpretation:** No evidence against stability — parameters appear stable.

- **Chow (rolling break search)**: F = 2.69, p = 0.0019, break at index 122 (2011-03-01), left n = 122, right n = 166

  *What it does:* For each admissible split point, the test compares a **single-regime fit** to a **two-regime fit** (pre-break vs post-break). **Small p-values (< 0.05)** indicate a **structural break** at (or near) the reported split.

  **Interpretation:** Break suggested at the reported date; parameter shifts before/after that point.


## Outliers

**Regression influence (top 10 by Cook’s D)**
      date  year  month    return  stud_resid  leverage  cooks_d  flag_studentized>|3|  flag_leverage>2k/n  flag_cooks>4/n
2008-10-01  2008     10 -0.180978   -4.088320  0.041667 0.057297                  True               False            True
2011-12-01  2011     12 -0.105963   -2.597501  0.041667 0.023947                 False               False            True
2008-11-01  2008     11  0.121918    2.538646  0.041667 0.022899                 False               False            True
2011-09-01  2011      9 -0.111653   -2.535450  0.041667 0.022842                 False               False            True
2009-11-01  2009     11  0.120133    2.498213  0.041667 0.022191                 False               False            True
2011-08-01  2011      8  0.128779    2.483294  0.041667 0.021933                 False               False            True
2013-06-01  2013      6 -0.118880   -2.482122  0.041667 0.021912                 False               False            True
2008-08-01  2008      8 -0.094257   -2.452269  0.041667 0.021400                 False               False            True
2006-04-01  2006      4  0.113474    2.298687  0.041667 0.018852                 False               False            True
2004-04-01  2004      4 -0.093567   -2.275908  0.041667 0.018487                 False               False            True


**Univariate return outliers (MAD-z)**
      date  year  month    return     MAD_z  flag_|MADz|>3.5
2008-10-01  2008     10 -0.180978 -4.224464             True
2011-08-01  2011      8  0.128779  2.826628            False
2013-06-01  2013      6 -0.118880 -2.810894            False
2008-11-01  2008     11  0.121918  2.670435            False
2011-09-01  2011      9 -0.111653 -2.646384            False
2009-11-01  2009     11  0.120133  2.629799            False
2011-12-01  2011     12 -0.105963 -2.516868            False
2006-04-01  2006      4  0.113474  2.478226            False
2012-01-01  2012      1  0.106351  2.316073            False
2016-02-01  2016      2  0.104114  2.265174            False
