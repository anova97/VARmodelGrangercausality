# VAR model Granger causality
The aim of this project is to build a vector autoregressive model - VAR, causality in Granger's sense will then be examined.

The model will be built for the returns of the given stock indices:
- DAX - German equity index
- FTSE - British stock index
- SHC - Chinese Stock Exchange Index
- SPX - American Stock Exchange Index
- WIG20 - Polish stock exchange index

## Dataset
Data extracted from (https://stooq.pl). Daily historical index data from January 2010 to December 2019 has been downloaded.

As there were missing data, linear interpolation was used. The interpolation uses the last valid value before the missing data and the first valid value after the missing data. 
If missing data occur in the first or last observation in the series, they are not replaced. The function used for this is na.approx.

## Methodology
The variables in the VAR model must be stationary, so the series must satisfy the condition of constant mean, constant variance, and the autocovariance function should depend only on the lag.

The choice of the lag order of the variables in the VAR model is a very important element of modelling. Too many lags over-complicate the model, while too few can cause autocorrelation of the random components.

Using the VARselect function, the appropriate lag order is selected. It calculates four information criteria: Akaike AIC, Hannan and Quinn HQ, Schwartz SC and Final Prediction Error FPE.

In order to verify the estimated VAR model, it is first necessary to check for autocorrelation of random components not only of the first degree, but also of higher degrees up to a degree equal to the number of lags. In order to verify the autocorrelation of the random components, the Ljung-Box test will be used

a variable Y if current values of Y can be predicted with greater accuracy using past values of X than without them (with the remaining information unchanged), that is, if the coefficients on the lagged variables of X are statistically significant. Even if all the parameters for some variable with distributed lags are insignificant, this does not mean that they will not be significant in aggregate.

A linear version of the Granger test will be used to test for causality.

