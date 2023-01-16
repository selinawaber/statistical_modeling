
# Statistical Modeling

## Classification

### Linear Discirmination


We assume that both categories have a gaussian distribution.

In the linear case we assume for the distributions to have the same covariance matrices, only the means can vary. We have several distributions and we want to associate each sample to a distribution. The distributions are known, but the samples are intertwined, thus we have to use LDA to decide which sample can be associated with which distribution.

The discrimination line is at the point (in 2D) with the same densities.

```R
library(MASS)

lda = lda(response ~ predictor1 + predictor2, data = data)

```

### R-Code/Figure:
### Linear discrimination in the case of bivariate densities 
### with known parameters and in the case of samples. Simplified. (lecture I)
```{r}
require( mvtnorm)

mu1 <- c( 2, 1)
mu2 <- c( 4,-1)
Sigma <- matrix( c(6,2,2,2), 2) 
x1 <- seq( -3, to=7, length=99)
x2 <- seq( -4, to=4, length=100)
grid <- expand.grid( x=x1, y=x2)  

dens1 <- matrix( dmvnorm( grid, mean=mu1, sigma=Sigma), 99, 100)
dens2 <- matrix( dmvnorm( grid, mean=mu2, sigma=Sigma), 99, 100)


##  Construct right panel based on empirical quantities
require( MASS)
set.seed(1)
N <- 25
s1 <- rmvnorm( N, mean=mu1, sigma=Sigma)
s2 <- rmvnorm( N, mean=mu2, sigma=Sigma)

obs <- rbind( s1, s2)
colnames( obs) <- c( 'x', 'y')
group <- factor( rep( c("a","b"), each=N))

tmp <- lda( obs, grouping=group)              # from package MASS
cll <- as.numeric( predict( tmp, grid)$class) # predict the fine grid

image( x1, x2, matrix( cll, 99, 100), col=c("seashell1","palegreen1"))
points( obs, col=group, pch=20)

dens1 <- matrix( dmvnorm( grid, mean=mu1, sigma=Sigma), 99, 100)
dens2 <- matrix( dmvnorm( grid, mean=mu2, sigma=Sigma), 99, 100)
contour( x1, x2, dens1, nlevels=7, drawlabels=F, xlab='x1', ylab='x2', add = T)
contour( x1, x2, dens2, nlevels=7, drawlabels=F, col=2, add=T)

```

### Quadratic Discrimination

In the quadratic case the distributions can have varying variances. In fact for all samples both variants can be applied but QDA performs with different variances.

```R
qda = qda(response ~ predictor1 + predictor2, data = data)

```

```R
tmp <- qda( obs, grouping=group)
clq <- predict(tmp, grid)$class
image( x1,x2, matrix(as.numeric(clq), 99, 100), 
      col=c(rgb(0,0,0,0.2), rgb(1,0,0,.3)))
points( obs, col=group, pch=20)
 contour( x1, x2, dens1, col=1, add=T, drawlabels=F)
 contour( x1, x2, dens2, col=2, add=T, drawlabels=F)
 
```

### Fishers Discriminant Rule

It is identital to LDA when covariance matrices are equal.

### Classification Tree

Actually pretty much the same as a K-D tree, but in this case we have more than 3 Dimensions.

- non robust method


```R
require( partykit) 
ct=ctree(response ~., data=data)
plot(ct)
```

### Random Forest

```R
require(randomForest)
set.seed(1000)
rf_analysis <- randomForest(Type ~ ., data = wines_df)
rf_analysis

plot(rt_analysis)
```

### Bagging

Use bootstrapping to create many training sets and for each of them create a new tree. Take the average for the final classification.

```R
require("adabag")
peng.bagging <- bagging( species~., data=train, mfinal=11)
bagging.pred <- predict( peng.bagging, newdata=test)
print(conf.mat <- bagging.pred$confusion) # to discuss: confusion matrix
sum( diag(conf.mat)) / sum(conf.mat)      # accuracy  (overall measure)
diag(conf.mat) / rowSums(conf.mat)        # precision for each class
diag(conf.mat) / colSums(conf.mat)        # sensitivity for each class
```



### Boosting 

Similar to bagging but samples are associated with a weight which corresponds to the amount of missclassifications, with this the training will focus on hard cases.

### Figure/R-Code:
### Manual implementation of a boosting algorithm.

```R
set.seed(14)
n1 <- 12      # group sizes
n2 <- 12
M <- 7       # number of iterations
tmp <- rbind( WC=0, err=numeric(M), a=0)
dat <- data.frame( g=rep(0:1, c(n1,n2)),y=c(rnorm(n1, 0), rnorm(n2, 1.5)))
n <- n1 + n2  # total number of observations
plot( dat$y, rep(0, n), col=dat$g+1, ylim=c(0, M+1), pch=19,
      ylab='m (1,...,M)', xlab='x')
col <- c(rgb(0,0,0,.2), rgb(1,0,0,.3))
w <- rep(1/n,n)  # weights, will be adapted
WC <- function( dat, w)  # weak classifier
  (weighted.mean( dat$y[dat$g==0], w[dat$g==0]) + 
              weighted.mean(dat$y[dat$g==1], w[dat$g==1]))/2
for (i in 1:M) {
  sel <- sample(n, .8*n)           # selector for training dataset
  WCout <- WC(dat[sel,], w[sel])   # weak classifier. 0 if x < WCout
  tmp["WC",i] <- WCout
  wrong <- as.numeric( dat$g !=  (WCout < dat$y ))   # wrongly classified
  tmp["err",i] <- sum(w * wrong)/sum(w)              # error measure 2.
  tmp["a",i] <- log( (1-tmp["err",i])/tmp["err",i] ) # update weight 3.
  w <- w * exp(tmp["a",i]* wrong)        # update weights       
  w <- w/sum(w)                          # normalize weights
  segments( c(-4,WCout), c(i,i), c(WCout, 4), c(i,i), col=col, lwd=10)
  points( dat$y[sel], rep(i, .8*n), cex=exp(w[sel]*n)/3, col=dat$g[sel]+1)
}
tmp                           # show thresholds, errors and update-weights
x <- seq(0, to=2, l=50)
finaltmp <-  colSums( outer(tmp["WC",], x, "<") * tmp["a",]) >
           colSums( outer(tmp["WC",], x, ">") * tmp["a",]) 
print( final<- x[which.max(finaltmp)])    # final threshold
segments( c(-4,final), c(M,M)+1, c(final, 4), c(M,M)+1, col=col, lwd=15)
```




### Random Forest

Similar to bagging but for each tree we choose a random subsample of the featutres, where k = sqrt(n).

```R
require(fields)
require("randomForest")

rf = randomForest(response ~ ., data = train , ntree=50, prox=TRUE)
plot(rf)
rf.pred <- predict( rf, newdata=test)
table(rf.pred, test$species, dnn=c("Predicted Class", "Observed Class"))


rt <- ctree( species~., data=train)    # Compare with simple tree
rt.pred <- predict(rt, newdata=test)
table(rt.pred, test$species, dnn=c("Predicted Class", "Observed Class"))


ct <- cforest( species ~ ., data=train, ntree=50)  # with package partykit
ct.pred <- predict( ct, newdata=test)
table(ct.pred, test$species, dnn=c("Predicted Class", "Observed Class"))

#  length( ct$nodes)         # nr of trees in random forest (== ntree=50)
#  ct$nodes[[11]]         # accessin the 11th tree
#  ct$nodes[[11]]$split[1:2]   # first split of 11th tree
#  ct$nodes[[11]]$kids[1:2]    # branches after first split
#  ct$nodes[[11]]$kids[[2]]$split[1:2] # second split of second branch, etc



# Optionally repeat rd with only most important features extracted with
varImpPlot(rf)
rf2 = randomForest(response ~ a + b + c)

```

### linear and quadratic discriminant analysis 

```R
require(MASS)

lda1 <- lda(SalaryCat ~ Work + Price, data = ec.noNA)

Work <- with(ec.noNA, seq(min(Work), max(Work), length = 100))
Price <- with(ec.noNA, seq(min(Price), max(Price), length = 100))
grid <- expand.grid(Work = Work, Price = Price)

pred.lda1 <- predict(lda1, grid)$class

qda1 <- qda(SalaryCat ~ Work + Price, data = ec.noNA)

pred.qda1 <- predict(qda1, grid)$class


cols <- c("white", rgb(0.9, 0.9, 0.8), rgb(0.8, 0.9, 0.9))
image(Work, Price, array(as.numeric(pred.lda1), c(100, 100)), col = cols)
with(ec, points(Work, Price, col = ec_col))
image(Work, Price, array(as.numeric(pred.qda1), c(100, 100)), col = cols)
with(ec, points(Work, Price, col = ec_col))

````

### Apply cross validation to evaluate the percentage of correctly predicted types of wine for each model.


```R
K_folds_cv <- function(k,dataset,model_to_fit,formula_to_use, ...){
      # shuffle our data
      dataset <- dataset[sample(1:nrow(dataset)),]
      # create k equally size folds
      folds <- cut(seq(1,nrow(dataset)), breaks=k, labels=FALSE)
      err <- numeric(nrow(dataset))
      j <- 1
      for(i in 1:k){
            testIndexes <- which(folds==i, arr.ind=TRUE)
            testData <- dataset[testIndexes, ]
            len_test <- nrow(testData)
            trainData <- dataset[-testIndexes, ]
            switch(model_to_fit, 
                  ’ctrees’ = {
                  rt_analysis <- ctree( formula_to_use, data=trainData)
                  pred_ch_r <- predict(rt_analysis,testData)
                  },
                  ’randomForest’ = {
                  rf_analysis <- randomForest(trainData[,2:ncol(wines_df)],y=trainData[,1], ...)
                  pred_ch_r <- predict(rf_analysis,testData)
            }
            )
         err[j : (j+len_test-1)] <- pred_ch_r == testData[,1]
         j <- j+len_test
       }
return(err)
}


```

```R
k_cv_randomForest_1 <- K_folds_cv(k = 10, dataset = wines_df, model_to_fit = "randomForest",
formula_to_use = formula(Type ~ .))

sum(k_cv_randomForest_1)/length(k_cv_randomForest_1)

```
### Use Cross Validation to find optimal bandwidth for the kernel approach. Use hcv() and sm.regression() from package sm.

````R
require(sm)

cv <- with(data, hcv(x, y, display = "line", ngrid = 50))
abline(v = cv, col = "darkgreen")
plot(data)
tt <- with(data, sm.regression(x, y, h = cv, add = T, col = "darkgreen"))
``````


### Cross Validation based on smoothing splines

```R
ln <- 50
output <- array(NA, c(ln, 4))
sparseq <- seq(0.1, to = 1, l = ln)

for (i in 1:ln) {
      si <- with(data.noNA, smooth.spline(x, y, spar = sparseq[i], cv = TRUE))
      output[i, ] <- with(si, c(lambda, cv.crit, df, spar = spar))
}

colnames(output) <- c("lambda", "CV", "d.f.", "spar")
min.value <- output[which(min(output[, 2]) == output[, 2]), ]
par(mfrow = c(1, 2))
plot(output[, 1], output[, 2], type = "l")
abline(v = min.value[1])
plot(data)
with(data.noNA, lines(smooth.spline(x, y, spar = min.value["spar"],
cv = TRUE), col = 2))

````

