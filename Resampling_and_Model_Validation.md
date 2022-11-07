# Statistical Modeling

## Resampling and Model Validation


### Bootstrap

- Bootstrap is a resampling method where large numbers of samples of the same size are repeatedly drawn, with replacement, from a single original sample.
- By repeatedly sampling with replacement, bootstrap creates the resulting samples distribution a Gaussian distribution, which makes statistical inference (e.g., constructing a Confidence Interval) possible.

Bootstrap:
- 1) decide how many bootstrap samples to perform
- 2) what is the sample size?
- 3) for each bootstrap sample: draw a sample with replacement with the chosen size and calculate the statistic of interest for that sample
- 4) calculate the mean of the calculated sample statistics


### Cross Validation

-The basic idea, behind cross-validation techniques, consists of dividing the data into two sets: the training set and the test/validation set


```R
# package to compute
# cross - validation methods
library(caret)

set.seed(1)
 
# creating training data as 80% of the dataset
random_sample <- createDataPartition(data$x, p = 0.8, list = FALSE)
 
# generating training dataset from the random_sample
training_dataset  <- data[random_sample, ]
 
# generating testing dataset from rows which are not included in random_sample
testing_dataset <- data[-random_sample, ]
 
# Building the model
 
# training the model by assigning sales column
# as target variable and rest other columns
# as independent variables
model <- lm(x ~., data = training_dataset)
 
# predicting the target variable
predictions <- predict(model, testing_dataset)
 
# computing model performance metrics
data.frame( R2 = R2(predictions, testing_dataset$x),
            RMSE = RMSE(predictions, testing_dataset$x),
            MAE = MAE(predictions, testing_dataset $x))


```

### Resampling

Linear regression $Y_i = \beta_0 + \beta_1 x_i + \epsilon_i$ with $\epsilon_i \sim Normal(0,\sigma^2)$ and $X_i \sim Normal(\mu, \sigma_x^2). $X_i$ and $\epsilon_i$ are independent.

- Simulate for n=15 data points with $\beta_0=1, \beta_1=2, \mu=4, \sigma_x=4, \sigma=2$
- Estimate regression coefficients $\beta_0$ and $\beta_1$
- Plot and add true and estimated regression lines

```R
set.seed(1)
n<-15
beta0<-1
beta1<-2
mu<-4
sigma.x<-4
sigma<-2

x<-rnorm(n,mu,sigma.x)
y<-beta0 + x* beta1 + rnorm(n,0,sigma)

plot(y~x)
fit1<-lm(y~x)

abline(beta0, beta1)
abline( fit1)
legend("topleft", legend=c("True", "Estimated"), lty=1:2)


```

- Repeat R=1000 times
- Investigate $\hat{\beta_0}, \hat{\beta_1}$

```R
R<-1000
coeff<-matrix(0,R,2)

for (i in 1:R){
    x <- rnorm(n, 4, sigma.x)
    y<- beta0 + x*beta1 + rnorm(n,0,sigma)
    coeff[i, ] <-coef(lm(y~x))
}

par(mfrow = c(1,2))
hist( l1[,1], main=’’, xlab=quote(widehat(beta[0])), prob=TRUE)
hist( l1[,2], main=’’, xlab=quote(widehat(beta[1])), prob=TRUE)

```
Unsurprisingly, the variability in the estimates increases as we introduce more randomness, by generating our data points every simulation. However, the mean of the estimates is roughly the same.


### Training, Validation and Test Sets

- fit a polynomial model ( lin. reg. up to degree 8)
- subset in trianing and validation (80\% and 20\%) and fit polynomial model. 
- repeat subsetting 100 times
- visualize the fits
- analyse residual sum of squares $RSS=\sum_{i=1}^n(y_i-f(x_i))^2$ with yi=ith value of the variable to be predicted, f(xi)=predicted value of yi


```R
require(fma)
bicoal

str( bicoal, strict.width='cut') # bicoal is a ’ts’ object.
year <- c(time(bicoal))
coal <- as.numeric( bicoal)
coal_df <- data.frame(coal=coal, year = year)

#year <- 1920:1968
#coal <- as.numeric(bicoal)[1:49]
#data <- data.frame(coal = coal, year = year)


rss <- matrix(NA,ncol = 8, nrow = 100)

par(mfrow=c(2,4))

set.seed(1)
for(degree in 1:8){
    plot(year,coal, main=paste0('degree of poly: ',degree))
    for(i in 1:100){
    
        dt<-sort(sample(nrow(data), nrow(data)*.8)) #test and training set split
        train<-data[dt,]
        test<-data[-dt,]
        
        out <- lm(coal ~ poly(year, degree), data=coal_df, subset = dt)
        
        res_valid <- predict(out, coal_df[-dt,] )
        rss[i , degree] <- sum((res_valid - coal_df$coal[-dt])^2) #RSS= SUM(yi-f(xi))^2
        
        lines( year[dt], out$fitted.values, col='blue')
    
    }

}

```
![Exercise 1 Aufgabe 2](Images/00002b.png?raw=true "Exercise 1 Aufgabe 2")

```R
par(mfrow=c(1,1)

which.min(apply(rss,MARGIN = 2,median)) # 8 lowest median rss

#apply() takes Data frame or matrix as an input and gives output in vector, list or array. Apply function in R is primarily used to avoid explicit uses #of loop constructs. It is the most basic of all collections can be used over a matrice.
#MARGIN=1`: the manipulation is performed on rows
#MARGIN=2`: the manipulation is performed on columns


boxplot(log(rss), main = 'log(rss) by polynomial degree')
abline(h = c(mean(log(rss)),median(log(rss))), col=c('blue','red'))

```
Based on the rss criteria, a model with a polynomial degree of 7 would be the ’best’ model.



![Exercise 1 Aufgabe 2b](Images/00002f.png?raw=true "Exercise 1 Aufgabe 2b")


### Empirical density of Variance with underlying Gaussian distirbution

```R
set.seed(12)
N <- 10000
vars <- numeric(N)
for (i in c(1:N)){
  samples <- rnorm(n = 5, mean = 0, sd = 1)
  vars[i] <- var(samples)
}
hist(vars, prob = TRUE,  breaks = 50)
```
### Empirical density of sigma^2 with underlying Gaussian and Poisson distributions. 
### True mean and sample mean indicated.

```R
par(mfrow=c(1,2))
n <- 10         # sample size
R <- 1000       # result weakly dependents on R, histograms get smoother
set.seed( 16)   # for reproducibility
variance <- 2
sigma2s <- numeric( R)
for (i in 1:R) {
    x <- rnorm(n, mean=0, sd=sqrt(variance)) # sampling from X ~ N(0,2)
    sigma2s[i] <- sum( ( x-mean(x))^2)/n     # manual calculation of variance
}
hist( sigma2s, prob=TRUE, main='', ylim=c(0,.6), xlim=c(0,8))
lines( density(sigma2s))
abline( v=c(variance, mean(sigma2s)), col=2:3)
variance - mean( sigma2s)  # theoretical value is variance/n, here 0.2

# similar for Poisson: `x <- rpois(n, variance)`, e.g., X ~ Pois( 2)
for (i in 1:R) {
    x <- rpois(n, variance) # sampling from Pois(2)
    sigma2s[i] <- sum( ( x-mean(x))^2)/n
}
hist( sigma2s, prob=TRUE, main='', ylim=c(0,.6), xlim=c(0,8))
abline( v=c(variance, mean(sigma2s)), col=2:3)
lines( density(sigma2s))
```

### Cross-Validation

```R
#???????????????????
```

### Bootstrap

```{r }
set.seed(14)    # here bootstrap results do not depend much on this seed
R <- 9999       # nr of replicates
sam <- data$x/data$y  # ratio

ratio <- numeric( R)
for (i in 1:R){  # try to vectorize this loop
    ratio[i] <- mean( sample( sam, replace=TRUE)) 
    }
hist( ratio, prob=TRUE, main='')
lines( density(ratio))
curve( dnorm(x, mean=mean(ratio), sd=sd(ratio)), add=TRUE, col='red')
abline( v=c(mean(sam), mean(ratio)), col=c(4,3), lty=c(1,2))
```
### Use a parametric bootstrap to simulate a 95% confidence interval for the parameter in the model

```R
n <- length(levels(term$dish))
iterations <- 300
out_vector <- numeric(iterations)
for (iter in 1:iterations) {
     index <- matrix(sample(n, n, replace = TRUE), ncol = 1) # sample n dishes with replacement
     colnames(index) <- "dish"
     
     tmp_term <- merge(index, term, all.x = T) # build the long format dataset
     # with repeated dishes
     
     if (prod(summary(tmp_term$dose)) == 0)
      next # have we sampled all dishes from the same dose? if so, go to next iteration
      
     m.tmp <- lmer(n.termites ~ dose + day + (1 | dish), data = tmp_term)
     
    ifelse(isSingular(m.tmp), next, out_vector[iter] <- fixef(m.tmp)[2]) # check whether
    # the fitted mixed model is (almost/near) singular (?isSingular). If so, go to
    # next iteration without storing the results.
}
quantile(out_vector, c(0.025, 0.975))
```
