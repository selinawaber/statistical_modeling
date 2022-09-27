# Statistical Modeling

## Principal Component Analysis

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

# or the same plot, but with a different aesthetics:
library(factoextra)
fviz_pca_ind(pca,
             col.ind = "darkblue", # Color by the quality of representation,
             repel = TRUE     # Avoid text overlapping
             )

```

### Classical biplot:
```R
biplot( pca, scale = 0)

## to plot other principal components, use, for example:
biplot( pca, scale = 0, choices=c(3, 2))

# or equivalent 
fviz_pca_biplot(pca, repel = TRUE,
                col.var = "#2E9FDF", # Variables color
                col.ind = "#696969"  # Individuals color
                )
```

###  Reconstruction of the data in PCA
```R
mean_jobs = colMeans(jobs)
pca <- prcomp(jobs, center = FALSE, scale = FALSE) # no scaling for simplicity of the example
nComp = 9
jobshat = pca$x[,1:nComp] %*% t(pca$rotation[,1:nComp])


jobs[5,]
jobshat[5,]

```

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
 
# Running a Decision tree algporithm
## Installing and loading packages
install.packages("rpart")
install.packages("rpart.plot")
library(rpart)
library(rpart.plot)
 
rpart.model <- rpart(disp ~ .,
                    data = train.data, method = "anova")
 
rpart.plot(rpart.model)
```

```R
