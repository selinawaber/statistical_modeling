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

Exponential(d, range = 1, alpha = 1/range, phi=1.0,theta = NULL)
Matern(d , range = 1,alpha=1/range, smoothness = 0.5, 
       nu= smoothness, phi=1.0) 


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




````R
## function that impelements the expoential covariance function
exp.cov <- function(h, theta2, theta3) {
theta2 * exp(-h/theta3)
}
h <- seq(0, 4, 0.01)
theta2 <- 3
theta3 <- 1
y <- exp.cov(h = h, theta2 = theta2, theta3 = theta3)
plot(h, y, type = "l", ylab = "exp.cov(h)")

```````
```R
#function that returns pairwiese (Euclidean) distances between each elemts of vectors x and y
dist.matrix <- function(x, y) abs(outer(x, y, FUN = "-"))
DIST.MAT <- dist.matrix(data$s, data$s) ## matrix containing all possible
distances of the locations

````

```R
## Covariance Matrix
(SIGMA <- exp.cov(h = DIST.MAT, theta2 = theta2, theta3 = theta3))
````

````R
## new locations
s.new <- seq(0, 10, 0.001) 
dist.mat <- dist.matrix(data$s, s.new)
dim(dist.mat) # for each of the old locations, we have all the distances for the new locations
sigma <- exp.cov(h = dist.mat, theta2 = theta2, theta3 = theta3)
````

```R
## kriging prediction for the locations snew. 
simple.kriging <- function(SIGMA, sigma, Z) {
lambda <- t(sigma) %*% solve(SIGMA)
c(lambda %*% Z)
}

z.new <- simple.kriging(SIGMA, sigma, data$Z.s)
plot(data, xlim = c(0, 10), ylab = "Z(s)")
lines(s.new, z.new, lty = 2)
legend("topright", legend = "Simple kriging predicition", lty = 2)

````


