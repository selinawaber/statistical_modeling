# Statistical Modeling

## Linear Models

```R
# Classical linear model
lm(predictor ~ responseA + responseB, data = data)
# Linear model with explicitly defined intercept term
lm(predictor ~ 1 + responseA + responseB, data = data)
# Linear model with formulate applied to response variable
# Note that the I() is necessary to bypass the meaning of operators in the context of the LM formula
lm(predictor ~ responseA + I(responseB * 2))

````

### Assumpitons

- Homoscedastic: Var(error_i)=variance. Look at scale location plot, watch out for a horizontal line and equally spread points.
- Independence: Corr(error_i, error_j) =0 for i not equal j
- Gaussian: The errors are gaussian with a mean of 0. We can look at the QQ plot, if we see a more or less linear distribution, we can assume that the error are gaussian distributed.
- Linearity: The regression model is linear in parameters. Look at residuals vs. fitted. Equally spread residuals without distinct patterns are a good indicator for a linear relationship.

Residuals versus leverage helps us identify cases which have high leverage.

### Check Linear assumptions using plots:

- Residuals vs Fitted. Used to check the linear relationship assumptions. A horizontal line, without distinct patterns is an indication for a linear relationship, what is good.

- Normal Q-Q. Used to examine whether the residuals are normally distributed. It’s good if residuals points follow the straight dashed line.

- Scale-Location (or Spread-Location). Used to check the homogeneity of variance of the residuals (homoscedasticity). Horizontal line with equally spread points is a good indication of homoscedasticity. 

- Residuals vs Leverage. Used to identify influential cases, that is extreme values that might influence the regression results when included or excluded from the analysis.  Outliers can be identified by examining the standardized residual (or studentized residual), which is the residual divided by its estimated standard error. Standardized residuals can be interpreted as the number of standard errors away from the regression line.
Observations whose standardized residuals are greater than 3 in absolute value are possible outliers

### Inflection points with Cooks distance

```R
cooksD<-cooks.distance(fit1)

#Plot Cook’s Distance with a horizontal line
#at 4/n to see which observations exceed this thresdhold
n<-nrow(data)
plot(cooksD, main="Cooks Diastance for Influential Obs")
ablie(h=4/n, lty=2, col="blue") #add cut of line

influcential_obs<-as.numeric(names(cooksD)[(cooksD>(4/n))])

outliners_removed<-data[-influcential_obs,]

fit2<-lm(outliners_removed$y ~., data=outliners_removed)

````




### Leave on out for linear models

What is the effect of the ith observation on
 - the stimate
 - the predicition
 - the estimated standard errors

```R
lm = lm(predictor ~ response1 + response2, data=data)
influence.measures(lm)

```

### ANOVA

```R
# One way ANOVA
# Test weather any of the group means are different from the overall mean of the data
anova(lmA)
```

Variance that is not explained by the model is called residual variance.

- Sum Sq: Sum of squares, the total variation between the group means and the overall means
- Mean Sq: Mean sum of squares, Sum Sq divided by degrees of freedom
- F-value: The higher this value, the more likely the variation is caused by real effects and not by chance
- P-value: Likelyhood of null hypothesis

```R
# Two way ANOVA
anova(lmA, lmB)

```

To compare the fits of two models, you can use the anova() function with the regression objects as two separate arguments. The anova() function will take the model objects as arguments, and return an ANOVA testing whether the more complex model is significantly better at capturing the data than the simpler model. If the resulting p-value is sufficiently low (usually less than 0.05), we conclude that the more complex model is significantly better than the simpler model, and thus favor the more complex model. If the p-value is not sufficiently low (usually greater than 0.05), we should favor the simpler model.



### AIC and BIC


The Akaike information criterion (AIC) is an estimator of the relative quality of statistical models for a given set of data. Given a collection of models for the data, AIC estimates the quality of each model, relative to each of the other models. Thus, AIC provides a means for model selection.

BIC is additionally penalizing for the number of observations. With many observations available, AIC might be choosing to complex models



```R
lcs.aic <- step( fit.all)       # AIC is default choice
summary( lcs.aic)$coefficients
# AIC the smaller the better!
extractAIC( lcs.aic)[2]

plot( lcs.aic)                  # 4 plots to assess the model


# BIC as an alternative: 
summary( step( fit.all, k=log(50), trace=0))$coefficients

```      
![AIC](Images/image-36.png?raw=true "AIC")






      
### Hypothesis testing

Given $y=\beta_0 + \beta_1 x_1 + \beta_2 x_2$ and $(X^TX)^{-1}$ and hat(beta0), hat(beta1), hat(beta2) and $s^2$

Standard Error SE(hat(beta2))
```R
# estimated covariance matrix of LS estimates
V <- s2 * XX.inv
# se for beta2
(se.beta2 <- sqrt(V[3, 3]))
````

Test the hypothesis beta2=0

````R
(t0 <- beta2/se.beta2)
# reject H0: beta2=0?
t0.975 <- qt(0.975, df = n - 3)
abs(t0) > t0.975

# corresponding p-value is
2 * (1 - pt(abs(t0), df = n - 3))

`````

Estimate covariance between hat(beta1) and hat(beta2). What is the standard error of hat(beta1)-hat(beta2)?
```R
cov.beta1beta2 <- V[2, 3]
# standard error for beta1-beta2
se.diff <- sqrt(V[2, 2] + V[3, 3] - 2 * cov.beta1beta2)
# same as
A <- matrix(c(1, -1), 1, 2)
sqrt(A %*% V[2:3, 2:3] %*% t(A))
````
Test hypothesis that beta1 = beta2
```R
t0 <- (beta1 - beta2)/se.diff
# reject H0: beta1=beta2?
abs(t0) > t0.975

# corresponding CI
(beta1 - beta2) + c(-1, 1) * t0.975 * se.diff
```

Total sum of squares is 120. Consturct the ANOVA table and test the hypothesis that beta1=beta2=0.
```R
SST <- 120
df.resid <- (n - 3)
df.reg <- 2
SSE <- s2 * df.resid
SSR <- SST - SSE
# reject H0: beta1=beta2=0?
F0 <- (SSR/df.reg)/s2
F0 > qf(0.95, df.reg, df.resid)

# coef of determination
(R2 <- SSR/SST)

```
