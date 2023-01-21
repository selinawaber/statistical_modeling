# Statistical Modeling

## Exterme Value Theory

We model the distribution of P(max(X_i) > m).
- In contrary to classical statistcs we do not model averages.
- In classical statistics central values are normal distrbited (CLT)
- In extreme values the distribution of the maximas converges to the generalized extreme value distribution.

````R
require(extRemes) 
gevfit = fevd(data)
summary(gevfit)
````
```R
require(extRemes)

gevfit <- fevd(maximum$Speed, period.basis = "month")
summary(gevfit, silent = TRUE)
ci(gevfit, type = "parameter")
````



Where the shape will define the following distributions:
- bigger than 0: Frechet
- smaller than 0: Weibull
- Equal 0 : Gambel

````R
plot(gevfit, type="density")
plot(gevfit, type="qq")
plot(gevfit, type="prob")


````

### Modeling Peaks over thresholds

Instead of modeling the maxima, which severely limits the datapoints, we can also model the peaks over threshold. In this case we have P(X - u > y | X > u)

But in this case the limit theorem does not hold anymore. In this case the theoretical distribution is the generalized Pareto distribution.

```R
gevfit = fevd(data, threeshold = 10)
````


### Threshold Selection for Modeling Peaks over thresholds

Mean residual life plot:

```R
require(evd)

mrlplot(dataWind_noNA$Speed)
```
```R
threshrange.plot(dataWind_noNA$Speed, r = c(10, 22), nint = 15)
gpdfit <- fevd(dataWind_noNA$Speed, threshold = 19, type = "GP", time.units = "days")

ci(gpdfit, type = "parameter")

plot(gpdfit, type = "qq", main = "")
plot(gpdfit, type = "prob", main = "")

```


```R
# return level for return period of 50 years of the gev model (3)
ci(gevfit, type = "return.level", return.period = 50 * 12)
# return level for return period of 50 years of the gpd model (5)
ci(gpdfit, type = "return.level", return.period = 50)
```

### Plot maximum monthly wind speed

````R
monthly_max <- aggregate(Speed ~ Month + Year, data = dataWind_noNA, max)
plot(Speed ~ Month, data = monthly_max)
# time series
ts_mm <- ts(monthly_max$Speed, start = c(1976, 1), frequency = 12)
plot(ts_mm, ylab = "Monthly maxima")


````
