# Statistical Modeling

## Mixed Models

```R
library(lme4)
# Random intercept (pred2)
# Different starting points determined by pred2, but not different slopes determined by pred2
lmer(response ~ pred1 + (1 | pred2) , data=data)
# For longtidual data, where x is for example time and pred2 is the id of subjects for which we have multiple datapoints over time
lmer(response ~ pred1 + (x | pred2) , data=data)

## then ANOVA to find the best model
```

# Longitudinal Data and Ploting
```R
library(reshape)
data_long <- reshape(data, direction = "long", varying = paste("x", 1:15, sep = ""),
v.names = "w", timevar = "z", idvar = "k")
dimnames(data_long)[[1]] <- seq(dimnames(data_long)[[1]])

require(lattice)

plot1 <- xyplot(y ~ y | z, group = w, data = data_long, type = "l")
print(plot1)
print(bwplot(~y | z, data = data_long))

````

### Mixed Models and Prediciton

Predict the math scores for a subset of ten students from the dataset. Compare the predictions including
and excluding random effects and discuss the differences to the realised scores.
Hint: Use re.form=NA or re.form=NULL to specify if you want to include or exclude random effects in the prediction.

`````R
fit2<-lmer(math~socaial + raven + (1|school) , data=jspr)

predind <- sample(c(1:dim(jspr)[1]), 10)
predset <- jspr[predind, c("gender", "social", "raven", "school")]

a <- predict(fit2, predset)
b <- predict(fit2, predset, re.form = NA)

comp <- cbind(round(a, 0), round(b, 0), jspr[predind, "math"])

colnames(comp) <- c("With RE", "witout RE", "obs")

plot(c(1:10), comp[, 2], pch = 20, ylim = c(min(comp[, 3]) -
    3, max(comp[, 3]) + 3), xlab = "Student", ylab = "Final math grade")
    
points(c(1:10), comp[, c(1)], col = "red", pch = 20)
points(c(1:10), comp[, c(3)], col = "blue", pch = 2)

legend("topleft", legend = c("without RE", "with RE", "Observed"),
    col = c("black", "red", "blue"), pch = c(20, 20, 2))


```````
