# Statistical Modeling

## Mixed Models

```R
library(lme4)
# Random intercept (pred2)
# Different starting points determined by pred2, but not different slopes determined by pred2
lmer(response ~ pred1 + (1 | pred2) , data=data)
# For longtidual data, where x is for example time and pred2 is the id of subjects for which we have multiple datapoints over time
lmer(response ~ pred1 + (x | pred2) , data=data)

```

# Longitudinal Data and Ploting
```R
library(reshape)
data_long <- reshape(data, direction = "long", varying = paste("x", 1:15, sep = ""),
v.names = "w", timevar = "z", idvar = "k")
dimnames(data_long)[[1]] <- seq(dimnames(data_long)[[1]])

require(lattice)

plot1 <- xyplot(y ~ y | z, group = w, data = data_long, type = "l")
print(plot1)
print(bwplot(~y | z, data = data_long))

````
