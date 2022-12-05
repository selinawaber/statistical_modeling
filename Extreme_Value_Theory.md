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
