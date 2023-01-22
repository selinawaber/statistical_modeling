# Statistical Modeling

## Non Parametric Regression

Datasets where linear models do not fit. Thus we extend the model to Y = g(x) + e, where e follows a normal distribution. In a parametric regression we are looking for the parameters of the function but the shape of the function is known. In a non parametric regression the general shape of the function is unknown.

### Kernel Smoothing (local estimation approch)


```R
# The kernel, the higher the bandwith, the smoother the result
m2 <- with(data, ksmooth(x, y, kernel = "normal", bandwidth = 15))
m3 <- with(data, ksmooth(x, y, kernel = "box", bandwidth = 15))
```
### Local Polynomials (local estimation approach)
Often better at boundaries. Kernel approach cannot correctly fit a line.

```R
m1<-lowess(x, y, f = 0.3, iter = 3, delta = 0)
m2<-loess(x~y, span = 0.3, degree = 1, family = "symmetric", iterations = 4, surface = "direct")

with(data, plot(y,x))
lines(m1)
lines(m2)

````

### Splines
Similar to local polynomials but tries to fit polynomial for each point seperatly. Smoothing is reached by reducing the number of knots.

```R
m5 <- with(data.noNA, smooth.spline(data$x, data$z, spar = 0.5))

````

### Cross-validatory choice of smoothing parameter

Use cross-validation to find an optimal bandwidth for the kernel approach

```R
cv <- with(data , hcv(x, y, display = "line", ngrid = 50))
abline(v = cv, col = "darkgreen")
plot(data[, c(1, 3)])
tt <- with(data, sm.regression(x,y, h = cv, add = T, col = "darkgreen"))


`````
