# Statistical Modeling

## Resampling and Model Validation


### Bootstrap

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
