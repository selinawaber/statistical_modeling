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
- analyse residual sum of squares


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
        rss[i , degree] <- sum((res_valid - coal_df$coal[-dt])^2)
        
        lines( year[dt], out$fitted.values, col='blue')
    
    }

}

```
![Exercise 1 Aufgabe 2](Images/00002b.png?raw=true "Exercise 1 Aufgabe 2")

```R
par(mfrow=c(1,1)

which.min(apply(rss,MARGIN = 2,median)) # 8 lowest median rss

boxplot(log(rss), main = 'log(rss) by polynomial degree')
abline(h = c(mean(log(rss)),median(log(rss))), col=c('blue','red'))

```
Based on the rss criteria, a model with a polynomial degree of 7 would be the ’best’ model.



![Exercise 1 Aufgabe 2b](Images/00002f.png?raw=true "Exercise 1 Aufgabe 2b")


