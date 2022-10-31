# Statistical Modeling

## Non Parametric Regression

Datasets where linear models do not fit. Thus we extend the model to Y = g(x) + e, where e follows a normal distribution. In a parametric regression we are looking for the parameters of the function but the shape of the function is known. In a non parametric regression the general shape of the function is unknown.

### Kernel Smoothing (local estimation approch)


```R
# The kernel, the higher the bandwith, the smoother the result
m2 <- with(data, ksmooth(x, y, kernel = "normal / normal / box", bandwidth = 15))

```
### Local Polynomials (local estimation approach)
Often better at boundaries. Kernel approach cannot correctly fit a line.

```R
lowess(x, y, f = 0.3, iter = 3, delta = 0)
loess(x~y, span = 0.3, degree = 1, family = "symmetric", iterations = 4, surface = "direct")

````

### Splines
Similar to local polynomials but tries to fit polynomial for each point seperatly. Smoothing is reached by reducing the number of knots.

```R
m5 <- with(data.noNA, smooth.spline(data$x, data$z, spar = 0.5))

````
