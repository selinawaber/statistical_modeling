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


### Leave on out ofr linear models

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



### AIC and BIC


The Akaike information criterion (AIC) is an estimator of the relative quality of statistical models for a given set of data. Given a collection of models for the data, AIC estimates the quality of each model, relative to each of the other models. Thus, AIC provides a means for model selection.

BIC is additionally penalizing for the number of observations. With many observations available, AIC might be choosing to complex models



```R
lcs.aic <- step( lcs.all)       # AIC is default choice
summary( lcs.aic)$coefficients
# AIC the smaller the better!
extractAIC( lcs.aic)[2]

plot( lcs.aic)                  # 4 plots to assess the model


# BIC as an alternative: 
summary( step( lcs.all, k=log(50), trace=0))$coefficients
# => the variable  'pop75'    drops from the model
```      
      
