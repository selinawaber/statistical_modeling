# Statistical Modeling

## Linear Models
















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
      
