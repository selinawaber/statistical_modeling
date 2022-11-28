# Statistical Modeling

## Spatial Statistics

The covariance function c(v) is a function on a vector in the domain D. For example if we want to know the covariance between Z(s_1) and Z(s_2) we need to evalutate c(s_1 - s_2).

- If this function is related to the distance between the two points i.e.: || s_1 - s_2 || then it is called isotropic, otherwise its called anisotropic.
- The difference H = s_1 - s_2 is called the spatial lag.

### Covariance
Functional form of covariance function assuming the argument is a distance between locations.
Exponential: phi* exp( -d/range)

```R
require(fields)

Exponential(d, range = 1, alpha = 1/range, phi = 1)


`````







### Intrinsic Stationary

A process is called intrinsic stationary if E(Z(s_1)) = mean and Var(Z(s_1), Z(s_2)) = 1/2 * gamma(s_1 - s_2).

Gamma is a function of ||H|| only.

### Covariogram

```R
library(gstat)
library(sp)
library(fields)



# Convert the data into long format
sim.long <- cbind(expand.grid(x1 = 1:100, x2 = 1:100), z = c(sim1))
# Fit a linear model
m1 <- lm(z ~ x1 * x2, data = sim1.long)
# create new dataframe
vario_df <- cbind.data.frame(z_res = resid(m1), x1 = sim1.long$x1, x2 = sim1.long$x2)
# Set coodinates
coordinates(vario_df) <- ~x1 + x2
# Create a covariogram
plot(variogram(z_res ~ 1, vario_df, covariogram = T))

```

### Kriging Predictor

A Nugget effect model takes the average over all other points as estime, if the data point lies on top of a prediction we use the prediction instead.








