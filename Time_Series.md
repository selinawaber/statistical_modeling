# Statistical Modeling

## Time Series

For time series there might be a dependence between variables, as a measurement x_i is dependant on x_i-1. Thus we want to plot the residual of x_i against x_i-1 for all observations. In R this looks something like this:

```R
plot( resids[-c(n,n-1)], resids[-c(1:2)])
```

### Atuoregressive Model

The current observation depends on the previous one. Y_t = a * Y_t-1 + e

We can also extend this model by considering more previous datapoints, where we call the number of datapoints the order of the model.

```R
ar(resid(lm), order= 2)

````

### Moving Average Model

Present observation depends on a weighted average of white noise components: Y_t = e_t + a_0 * e_t-1 … a_q * e_t-q

### ARMA Model

Combined MA and AR model into ARMA model.

### Auto Correlative Function (ACF)
