# CC — Monthly Diagnostics

## Data

- Observations: **288**  
- Date range: **2001-01-01 — 2024-12-01**
- Average monthly volume: **13,633**  
- Minimum monthly volume: **2,857**


## Exploratory Analysis

**Summary of monthly returns**
 count    mean      std     skew  kurtosis_excess       min       q25  median      q75      max
   288 0.01379 0.095008 1.167429         5.292989 -0.254618 -0.044421 0.00981 0.061227 0.595752


**Month vs. Rest-of-Year (Welch two-sample t-tests)**

 month  n(m)  n(rest)   mean(m)  mean(rest)  mean(m) − mean(rest)    t_stat  p_value sig
01-Jan    24      264  0.033000    0.012044              0.020956  0.982660 0.334554    
02-Feb    24      264  0.033792    0.011972              0.021820  1.049306 0.303314    
03-Mar    24      264  0.014257    0.013747              0.000510  0.016030 0.987341    
04-Apr    24      264  0.014128    0.013759              0.000369  0.021096 0.983312    
05-May    24      264 -0.015968    0.016495             -0.032463 -1.868954 0.071636   *
06-Jun    24      264  0.015997    0.013589              0.002407  0.151769 0.880348    
07-Jul    24      264  0.012160    0.013938             -0.001779 -0.096338 0.923927    
08-Aug    24      264  0.016679    0.013527              0.003152  0.182108 0.856744    
09-Sep    24      264  0.006313    0.014470             -0.008156 -0.446895 0.658309    
10-Oct    24      264 -0.023127    0.017146             -0.040273 -2.554713 0.015738  **
11-Nov    24      264  0.041190    0.011299              0.029891  1.204398 0.239446    
12-Dec    24      264  0.017058    0.013493              0.003565  0.193333 0.848068    

_Interpretation:_ For each row, we test whether the average return in that month differs from the average return in all the other months. Small p-values (e.g., < 0.05) indicate the month’s average return is significantly different from the rest of the year.


## Stationarity

- **ADF**: stat = -18.360, p = 0.0000 ***, lags used = 0, n = 287  
  critical values: 1%=-3.453, 5%=-2.872, 10%=-2.572

- **KPSS (level)**: stat = 0.322, p = 0.1000 , lags used = 3  
  critical values: 10%=0.347, 5%=0.463, 2.5%=0.574, 1%=0.739

- **Joint read**: ADF rejects & KPSS does not reject → returns look stationary.


## Homoskedasticity (constant error variance)

- **Breusch–Pagan**: LM p = 0.3541, F p = 0.3586  
- **White**: LM p = 0.3541, F p = 0.3586
  *Interpretation:* **small p-values (<0.05)** suggest **heteroskedasticity conditional on the regressors** (here: month dummies).

- **ARCH LM (lags=12)**: LM p = 0.0134, F p = 0.0116  
  *Interpretation:* **small p-values (<0.05)** indicate **conditional heteroskedasticity** (volatility clustering) in residuals.

**Conclusion:** Evidence of **heteroskedasticity**. Report **robust standard errors** (HC3; and **HAC/Newey–West** if serial correlation is present).


## Structural Breaks

- **CUSUM (parameter stability test)**: p = 0.1256. CUSUM tracks cumulative sums of recursive residuals; **small p-values (< 0.05)** indicate **time-varying coefficients** (instability) rather than constant effects over the sample.

  **Interpretation:** No evidence against stability — parameters appear stable.

- **Chow (rolling break search)**: F = 3.90, p = 0.0000, break at index 263 (2022-12-01), left n = 263, right n = 25

  *What it does:* For each admissible split point, the test compares a **single-regime fit** to a **two-regime fit** (pre-break vs post-break). **Small p-values (< 0.05)** indicate a **structural break** at (or near) the reported split.

  **Interpretation:** Break suggested at the reported date; parameter shifts before/after that point.


## Outliers

**Regression influence (top 10 by Cook’s D)**
      date  year  month    return  stud_resid  leverage  cooks_d  flag_studentized>|3|  flag_leverage>2k/n  flag_cooks>4/n
2024-03-01  2024      3  0.595752    6.725156  0.041667 0.141236                  True               False            True
2024-11-01  2024     11  0.367925    3.582563  0.041667 0.044591                  True               False            True
2001-01-01  2001      1  0.328125    3.222160  0.041667 0.036380                  True               False            True
2001-11-01  2001     11  0.307617    2.898729  0.041667 0.029649                 False               False            True
2003-05-01  2003      5 -0.254618   -2.588712  0.041667 0.023789                 False               False            True
2024-02-01  2024      2  0.270531    2.567488  0.041667 0.023410                 False               False            True
2024-12-01  2024     12  0.241229    2.428171  0.041667 0.020990                 False               False            True
2011-03-01  2011      3 -0.201083   -2.330585  0.041667 0.019369                 False               False            True
2003-08-01  2003      8  0.231469    2.324510  0.041667 0.019270                 False               False            True
2004-07-01  2004      7  0.222140    2.271476  0.041667 0.018417                 False               False            True


**Univariate return outliers (MAD-z)**
      date  year  month    return     MAD_z  flag_|MADz|>3.5
2024-03-01  2024      3  0.595752  7.400837             True
2024-11-01  2024     11  0.367925  4.523227             True
2001-01-01  2001      1  0.328125  4.020532             True
2001-11-01  2001     11  0.307617  3.761504             True
2003-05-01  2003      5 -0.254618 -3.339907            False
2024-02-01  2024      2  0.270531  3.293086            False
2024-12-01  2024     12  0.241229  2.922976            False
2003-08-01  2003      8  0.231469  2.799695            False
2008-10-01  2008     10 -0.202718 -2.684380            False
2004-07-01  2004      7  0.222140  2.681865            False
