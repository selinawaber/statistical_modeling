# Statistical Modeling


### Decompose time series into a seasonal trend with stl()

```R
decomp <- stl(data_ts, s.window = "periodic")
head(decomp$time.series, 2)
plot(decomp)

````
Adjust the time series for seasonality.

````R

seas_adj_ts<-data_ts - decomp$time.series[, 1]

`````

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
Are the residuals correlated?

````R
tt <- acf(decomp$time.series[, 3])


`````

### PCAF

```R

pacf(decomp$time.series[, 3])

```
### Auto Regressive Process

$Y_t = \phi Y_{t-1}+ \epsilon_t$ with $\epsilon \sim^{iid} Normal(0, \sigma^2)$ and $\phi \in (0,1)$ and $Y_1 \sim Normal(0,\sigma^2)$

Simulate AR(1) process for t=1....50 in a sequential simulation and a direct approach.


````R
set.seed(1)
T <- 50
Y <- numeric(Ti) ## Y<-c()
sigma <- sqrt(2)
phi <- 0.5


## sequential
Y[1] <- rnorm(1, 0, sigma) 
for (i in 2:T) {
    Y[i] <- phi * Y[i - 1] + rnorm(1, 0, sigma)
}
par(mfrow = c(1, 2))
plot(Y, type = "l", main = "sequential")


## direct
require(mvtnorm)
sig_eq <- function(x, y, phi = 0.5, sigma_s = 1) { (sigma_s^2)/(1 - phi^2) * phi^(abs(x - y)) }

SIGMA <- matrix(ncol = T, nrow = T)
for (i in 1:T) {
  for (j in 1:T) {
    SIGMA[i, j] <- sig_eq(i, j, sigma_s = sigma)
  }
}

Y2 <- c(rmvnorm(1, rep(0, T), SIGMA))
plot(Y2, type = "l", main = "direct")


``````

### Variation of sigma

```R
sigma_vector <- c(2, 4, 8, 16, 32, 64)
Y_matrix <- matrix(nrow = Ti, ncol = length(sigma_vector))
phi <- 0.5

## sequential
for (j in 1:length(sigma_vector)) { 
    set.seed(115)
    Y_matrix[1, j] <- rnorm(1, 0, sigma_vector[j]) 
    for (i in 2:Ti) {
        Y_matrix[i, j] <- phi * Y_matrix[i - 1, j] + rnorm(1, 0, sigma_vector[j])
    }
}

Y_matrix <- as.data.frame(Y_matrix)
colnames(Y_matrix) <- as.character(sigma_vector)

library(reshape2)
Y_melted <- melt(Y_matrix)
colnames(Y_melted) <- c("sigma", "value")
Y_melted$time <- rep(1:50, length(sigma_vector))

library(ggplot2)
ggplot(data = Y_melted) + geom_line(aes(x = time, y = value, color = sigma)) + theme_bw()

````
