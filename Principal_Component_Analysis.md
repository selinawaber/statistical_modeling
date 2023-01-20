# Statistical Modeling

## Principal Component Analysis


```R
# scaling will scale the variables to have a unit variance before performing the PCA
pca = prcomp(data, scale = TRUE)
# Gives a summary of the pca
# sdev are the standard deviations of the prinipal components
# rotation, contains the matrix with the eigenvectors
# center The varibale means
# scale the variables standard deviations => the scaling applied to each variable
# Coordinates of the individual observarions
str(pca)
# Plot of the amount of variance explained by each PC
screeplot(pca)
plot(pca)
# Explaines how the two PC are being constructed based on the original variables
biplot(pca)

# Calculate the cumulative variance explained
cumsum(pca$sdev^2)/n
##With the method of proportion of variance explained i choose each component until a total of around 0.8 or 80% is reached, this helps to avoid overfitting. 
##The principal component analysis starting with 9 variables suggests to keep the first 4 to explain the most variability of the data. 
##Also, with the scree plot we can clearly see that adding the fifth principal component has a significant drop in variance explained in relative to the fourth feature.
```

### When is PCA suitable

- Multiple continous variables
- Linear Relationship between all values
- Large samples are required
- Adequate correlations to be able to reduce variables to a smaller number of dmensions
- No significant outliners






### PCA

```R
# Loading Data
data(mtcars)
 
# Apply PCA using prcomp function
# Need to scale / Normalize as
# PCA depends on distance measure
my_pca <- prcomp(mtcars, scale = TRUE,
                center = TRUE, retx = T)
names(my_pca)
 
# Summary
summary(my_pca)
my_pca
 
# View the principal component loading
# my_pca$rotation[1:5, 1:4]
my_pca$rotation
 
# See the principal components
dim(my_pca$x)
my_pca$x
 
# Plotting the resultant principal components
# The parameter scale = 0 ensures that arrows
# are scaled to represent the loadings
biplot(my_pca, main = "Biplot", scale = 0)
 
# Compute standard deviation
my_pca$sdev
 
# Compute variance
my_pca.var <- my_pca$sdev ^ 2
my_pca.var
 
# Proportion of variance for a scree plot
propve <- my_pca.var / sum(my_pca.var)
propve
 
# Plot variance explained for each principal component
plot(propve, xlab = "principal component",
            ylab = "Proportion of Variance Explained",
            ylim = c(0, 1), type = "b",
            main = "Scree Plot")
 
# Plot the cumulative proportion of variance explained
plot(cumsum(propve),
    xlab = "Principal Component",
    ylab = "Cumulative Proportion of Variance Explained",
    ylim = c(0, 1), type = "b")
 
# Find Top n principal component
# which will atleast cover 90 % variance of dimension
which(cumsum(propve) >= 0.9)[1]
 
# Predict mpg using first 4 new Principal Components
# Add a training set with principal components
train.data <- data.frame(disp = mtcars$disp, my_pca$x[, 1:4])
 

```

### PCA Exercise 1


```R

apply(data, 2, sd)
apply(data, 2, mean)

# apply(X, MARGIN, FUN)
# Here:
# -x: an array or matrix
# -MARGIN:  take a value or range between 1 and 2 to define where to apply the function:
# -MARGIN=1`: the manipulation is performed on rows
# -MARGIN=2`: the manipulation is performed on columns
# -MARGIN=c(1,2)` the manipulation is performed on rows and columns
# -FUN: tells which function to apply. Built functions like mean, median, sum, min, max and even user-defined functions can be applied>


## --> scale variable to normalize impact
pca<-prcomp(data,scale=T, center=T)

screeplot(pca)
biplot(pca, col=c("red", "blue"))

pca$sdev^2
# Calculate the cummulative variance explained
cumsum(pca$sdev^2)/ncol(data)

```

### PCA Exercise 2

 The variables are:
- pre: is an array with dimensions c(lon, lat, time) = c(31, 19, 240)
- lon: is a vector with the corresponding longitudes in degree
- lat: is a vector with the corresponding latitudes in degree


```R
require(maps)
require(fields)


for (i in 1:4){
  image.plot(lon, lat, pre[, , i])
  map("world", add = T , main = paste("January 1st", i , sep=" "))
  title(main = paste("January 1st", i , sep=" "))
}

# The apply function calculates the mean of each latitude and longitude
image.plot(lon, lat, apply(pre, c(1, 2), mean), main = "Mean precipitable content")
map("world", add = T)
image.plot(lon, lat, apply(pre, c(1, 2), var), main = "Variance of precipitable content")
map("world", add = T)



```
Use PCA and display maps of the pattern that explain most of the variance of the data.

```R
# Convert the array to a matrix with n=240 rows(observations) and p= 31*19 columns (variables)
dim.pre <- dim(pre)
pre2 <- t(array(pre, c(dim.pre[1] * dim.pre[2], dim.pre[3])))
pca <- prcomp(pre2, center = TRUE, scale = FALSE)


for ( i in 1:4){
    image.plot(lon,lat, array(pca$rotation[,i], dim=dim.pre[1:2]))
    map("world", add=T)
}

```

Display the variance (eigenvalues) of the principal components. How many principal components are relevant?

```R
var <- pca$sdev^2
plot(var[-length(var)], type = "b", log = "y")
plot(var[1:20], type = "b", log = "y", ylab = "eigenvalues")


```

Truncate number of components at i, if $\frac{\lambda_i - \lambda_{i+1}}{\lambda_i} < \sqrt{2/N}$ where N is the number of eigenvalues $\lambda_i$ (here N=240)


```R
L <- var
error.factor <- sqrt(2/dim(pca$rotation)[2])
cut.ok <- (L[-length(L)] - L[-1])/L[-length(L)] < error.factor
plot(var[1:20], type = "b", log = "y", ylab = "eigenvalues")
abline(v = which(cut.ok) + 0.5, lty = 2, col = 2)


```
### PCA Example 3

Given X that follows multivariate normal distribtuion with given mu and Sigma.

- What are the Eigenvalues and Eigenvectors of Sigma
- Draw 100 realizaitons of Normal(mu, Sigma)
- Add one arrow for each EV of Sigma to the plot. The lengths schould be proportional to the correspodning EV
- Calculate Principal Components of the data. 
- Display roated points

```R
mu <- c(2, 5)
Sigma <- matrix(c(1, 1/2, 1/2, 1), 2, 2)
eigen(Sigma)


require(mvtnorm)
set.seed(5)
n <- 100
X <- rmvnorm(n, mean = mu, sigma = Sigma)
plot(X, xlab = "X", ylab = "Y", col = "grey", xlim = range(X), ylim = range(X))

eig <- eigen(Sigma)
sqrt_eigvalues <- sqrt(eig$values) #take the square root of the eigenvalues to # calculate the length of the vectors
arrows(2, 5, 2 + eig$vectors[1, 1] * sqrt_eigvalues[1], 5 + eig$vectors[2,
    1] * sqrt_eigvalues[1], col = 2, length = 0.1)
arrows(2, 5, 2 + eig$vectors[1, 2] * sqrt_eigvalues[2], 5 + eig$vectors[2,
    2] * sqrt_eigvalues[2], col = 2, length = 0.1)
    

pca <- prcomp(X)
# Calculate the rotated points
X.center <- X - matrix(mu, 100, 2, byrow = TRUE)
X.rot <- cbind(X.center %*% pca$rotation[, 1], X.center %*% pca$rotation[,
    2]) + matrix(mu, 100, 2, byrow = TRUE)
par(pty = "s", lwd = 2)
# set up a square plotting region.
plot(X.rot, xlab = "PC 1", ylab = "PC 2", xlim = range(X.rot), ylim = range(X.rot),
    col = "grey")

## Compare the Eigenvalues or Sigma with the standard deviations of the
## principal components:
pca$sdev
sqrt(eigen(Sigma)$values)


## The subtle difference between the Eigenvectors can also be shown
## graphically, when the arrows from the PCA are added to the plot from
## b
par(pty = "s", lwd = 2)
plot(X, xlab = "X", ylab = "Y", xlim = range(X), ylim = range(X), col = "grey")
eig <- eigen(Sigma)
sqrt_eigvalues <- sqrt(eig$values) #take the square root of the eigenvalues to calculate # the length of the vectors
arrows(2, 5, 2 + eig$vectors[1, 1] * sqrt_eigvalues[1], 5 + eig$vectors[2,
    1] * sqrt_eigvalues[1], col = 2, length = 0.1)
arrows(2, 5, 2 + eig$vectors[1, 2] * sqrt_eigvalues[2], 5 + eig$vectors[2,
    2] * sqrt_eigvalues[2], col = 2, length = 0.1)
    
    
# Add the arrows of the PCA to the original plot
arrows(2, 5, 2 + pca$rotation[1, 1] * (pca$sdev)[1], 5 + pca$rotation[2,
    1] * (pca$sdev)[1], col = 4, length = 0.1)
arrows(2, 5, 2 + pca$rotation[1, 2] * (pca$sdev)[2], 5 + pca$rotation[2,
    2] * (pca$sdev)[2], col = 4, length = 0.1)

```


### Sampling from a bivariate normal distrbution:

```R
require(mvtnorm)
set.seed(12) 
par(pty="s") # to have a square plot
mu <- c(0,0)
Sigma <- array( c(1, -0.6, -0.6, 1), c(2,2)) 
# an array with values 1,-0.6, -0.6, 1 , which has 2 rows and 2 colums
sample <- rmvnorm( 5000, mean = mu, sigma=Sigma) # sample from bivariate 
# normal distriubtion with given parameters
plot( sample, pch='.', xlab='', ylab='', xlim=c(-4, 5),ylim=c(-4, 5))

#####eigendecomposition and the shape of the data
eigenSigma <- eigen(Sigma)

plot( sample, pch='.', xlab='', ylab='', xlim=c(-4, 5),ylim=c(-4, 5))
arrows(0, 0, 0+eigenSigma$vectors[1,], 0+eigenSigma$vectors[2,], col = 2, lwd = 2)
# plots an arrow from (0,0) to (0+eigenSigma$vectors[1,], 0+eigenSigma$vectors[2,])

#  with eigenvectors * sqrt(eigenvalues):

plot( sample, pch='.', xlab='', ylab='', xlim=c(-4, 5),ylim=c(-4, 5))
arrows(0, 0, 0+sqrt(eigenSigma$values)*eigenSigma$vectors[1,],
       0+sqrt(eigenSigma$values)*eigenSigma$vectors[2,], col = 2, lwd = 2, 
       length = 0.15)

```
### Variances explained and data projected on 2 PCs:

```R
# PCA in R: prcomp. See ?prcomp for details. 
pca <- prcomp( jobs, scale=TRUE)

# Having a look at the output of PCA 
str( pca, give.attr=FALSE)

#compare center with:
colMeans(jobs)

#compare scale with:
apply(jobs, 2, sd)

#A look at the rotation matrix 
print( pca, digits=2)

# How much variance is explained by the 1st, the 1st and 2nd, the 1st, 2nd and 3rd PCs. 
round( cumsum( pca$sdev^2)/sum( pca$sdev^2), 3)

plot( pca)  # equivalent to `screeplot( pca)`, how much variance is explained by each PC

plot( pca$x[,1:2], type='n', ylim=c(-2.7, 3.7), xlim=c(-3, 7))
text( pca$x[,1:2], rownames( jobs))

```

         






