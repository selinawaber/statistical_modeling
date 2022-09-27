# Statistical Modeling

## Resampling and Model Validation


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
Unsurprisingly, the variability in the estimates increases as we introduce more randomness, by generating
our data points every simulation. However, the mean of the estimates is roughly the same.


### Training, Validation and Test Sets

- fit a polynomial model ( lin. reg. up to degree 8)
- subset in trianing and validation (80\% and 20\%) and fit polynomial model. 
- repeat subsetting 100 times
- visualize the fits
- analyse residual sum of squares $RSS=\sum_{i=1}^n(y_i-f(x_i))^2 with yi=ith value of the variable to be predicted, f(xi)=predicted value of yi


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


-Test set containing the first and last five years
- Split in training and validation (26 and 10 data points)
- Use predict function for test years

```R
require(fma)
bicoal

str( bicoal, strict.width='cut') # bicoal is a ’ts’ object.

year <- 1920:1968
coal <- as.numeric( bicoal)
coal_df <- data.frame(coal=coal, year = year)

test_df <- coal_df[c(1:5, 44:49),]
new_coal_df <- coal_df[6:43,]

rss <- matrix(NA, ncol=8, nrow=101)

par(mfrow=c(2,4))

for(degree in 1:8){
    plot(year, coal, pch=20, ylim = c(100,800), main = paste0('degree of poly: ',degree))
    for (i in 1:101) {
        set.seed(i)
        train <- sort( sample( 34, 26))
        out <- lm(coal ~ poly(year, degree), data=new_coal_df, subset = train)
        
        res_valid <- predict(out, test_df )
        rss[i,degree] <- sum((res_valid - test_df$coal)^2)
        
        lines(test_df$year[1:5], res_valid[1:5], col='gray')
        lines(test_df$year[6:10], res_valid[6:10], col ='gray')
    }
}

par(mfrow=c(1,1))

which.min(apply(rss,MARGIN = 2,median)) # now, 1 lowest median rss

boxplot(log(rss), main = 'log(rss) by polynomial degree')
abline(h = c(mean(log(rss)),median(log(rss))), col=c('blue','red'),lty=c(2,2))

```

Here the results changes completely. Now the simplest model achieves the lowest mean rss. This is mainly
because we are assessing the capabilities of our model outside the range of the predictor values used for
training.

![Exercise 1 Aufgabe 2b](Images/00002f.png?raw=true "Exercise 1 Aufgabe 2b")

![Exercise 1 Aufgabe 2b](Images/00002f.png?raw=true "Exercise 1 Aufgabe 2b")

