# CO — Monthly Diagnostics

## Data

- Observations: **288**  
- Date range: **2001-01-01 — 2024-12-01**
- Average monthly volume: **224,915**  
- Minimum monthly volume: **48,893**


## Exploratory Analysis

**Summary of monthly returns**
 count     mean      std     skew  kurtosis_excess       min       q25   median      q75      max
   288 0.004669 0.101849 0.079756         6.363407 -0.533804 -0.053646 0.009981 0.063008 0.610254


**Month vs. Rest-of-Year (Welch two-sample t-tests)**

 month  n(m)  n(rest)   mean(m)  mean(rest)  mean(m) − mean(rest)    t_stat  p_value sig
01-Jan    24      264  0.006735    0.004481              0.002254  0.113426 0.910489    
02-Feb    24      264  0.033225    0.002073              0.031152  1.808643 0.080283   *
03-Mar    24      264 -0.000016    0.005095             -0.005111 -0.185897 0.854000    
04-Apr    24      264  0.018070    0.003450              0.014620  0.741186 0.464635    
05-May    24      264  0.022577    0.003041              0.019537  0.574445 0.570924    
06-Jun    24      264  0.025792    0.002748              0.023043  1.602578 0.117825    
07-Jul    24      264  0.004947    0.004643              0.000304  0.015945 0.987387    
08-Aug    24      264 -0.002219    0.005295             -0.007514 -0.516411 0.608779    
09-Sep    24      264 -0.008866    0.005899             -0.014765 -0.756239 0.455671    
10-Oct    24      264 -0.020466    0.006954             -0.027420 -1.157833 0.257261    
11-Nov    24      264 -0.030148    0.007834             -0.037982 -1.661275 0.108316    
12-Dec    24      264  0.006393    0.004512              0.001882  0.088064 0.930463    

_Interpretation:_ For each row, we test whether the average return in that month differs from the average return in all the other months. Small p-values (e.g., < 0.05) indicate the month’s average return is significantly different from the rest of the year.


## Stationarity

- **ADF**: stat = -14.194, p = 0.0000 ***, lags used = 0, n = 287  
  critical values: 1%=-3.453, 5%=-2.872, 10%=-2.572

- **KPSS (level)**: stat = 0.127, p = 0.1000 , lags used = 0  
  critical values: 10%=0.347, 5%=0.463, 2.5%=0.574, 1%=0.739

- **Joint read**: ADF rejects & KPSS does not reject → returns look stationary.


## Homoskedasticity (constant error variance)

- **Breusch–Pagan**: LM p = 0.3588, F p = 0.3635  
- **White**: LM p = 0.3588, F p = 0.3635
  *Interpretation:* **small p-values (<0.05)** suggest **heteroskedasticity conditional on the regressors** (here: month dummies).

- **ARCH LM (lags=12)**: LM p = 0.0000, F p = 0.0000  
  *Interpretation:* **small p-values (<0.05)** indicate **conditional heteroskedasticity** (volatility clustering) in residuals.

**Conclusion:** Evidence of **heteroskedasticity**. Report **robust standard errors** (HC3; and **HAC/Newey–West** if serial correlation is present).


## Structural Breaks

- **CUSUM (parameter stability test)**: p = 0.4422. CUSUM tracks cumulative sums of recursive residuals; **small p-values (< 0.05)** indicate **time-varying coefficients** (instability) rather than constant effects over the sample.

  **Interpretation:** No evidence against stability — parameters appear stable.

- **Chow (rolling break search)**: F = 1.45, p = 0.1440, break at index 230 (2020-03-01), left n = 230, right n = 58

  *What it does:* For each admissible split point, the test compares a **single-regime fit** to a **two-regime fit** (pre-break vs post-break). **Small p-values (< 0.05)** indicate a **structural break** at (or near) the reported split.

  **Interpretation:** No strong evidence of a single dominant break.


## Outliers

**Regression influence (top 10 by Cook’s D)**
      date  year  month    return  stud_resid  leverage  cooks_d  flag_studentized>|3|  flag_leverage>2k/n  flag_cooks>4/n
2020-05-01  2020      5  0.610254    6.266188  0.041667 0.124943                  True               False            True
2020-03-01  2020      3 -0.533804   -5.621792  0.041667 0.103079                  True               False            True
2008-10-01  2008     10 -0.331921   -3.162536  0.041667 0.035093                  True               False            True
2020-11-01  2020     11  0.277183    3.119176  0.041667 0.034170                  True               False            True
2009-05-01  2009      5  0.277157    2.569562  0.041667 0.023447                 False               False            True
2020-04-01  2020      4 -0.220844   -2.407995  0.041667 0.020650                 False               False            True
2015-07-01  2015      7 -0.205932   -2.120504  0.041667 0.016088                 False               False            True
2008-12-01  2008     12 -0.198850   -2.062947  0.041667 0.015240                 False               False            True
2011-10-01  2011     10  0.183816    2.053136  0.041667 0.015097                 False               False            True
2014-12-01  2014     12 -0.195682   -2.030617  0.041667 0.014773                 False               False            True


**Univariate return outliers (MAD-z)**
      date  year  month    return     MAD_z  flag_|MADz|>3.5
2020-05-01  2020      5  0.610254  6.846610             True
2020-03-01  2020      3 -0.533804 -6.202321             True
2008-10-01  2008     10 -0.331921 -3.899682             True
2020-11-01  2020     11  0.277183  3.047658            False
2009-05-01  2009      5  0.277157  3.047363            False
2020-04-01  2020      4 -0.220844 -2.632751            False
2018-11-01  2018     11 -0.216823 -2.586892            False
2015-07-01  2015      7 -0.205932 -2.462670            False
2008-11-01  2008     11 -0.199559 -2.389979            False
2008-12-01  2008     12 -0.198850 -2.381899            False
