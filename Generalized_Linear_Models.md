# Statistical Modeling

## Generalized Linear Models

```R
glm(cbind(Y,N-Y) ~ X,  family = binomial(link="logit"),data=DataFrame) 
glm(cbind(Y,N-Y) ~ X,  family = binomial(link="probit"),data=DataFrame) 
```
Instead of talking by certain factor of increase in y when we change position in x, instead we talk about a change in log odds.

Fromt the summary of the glm in r we get the temp estime. Its value x will tell us about the log odd increase when we increase x. Which corresponds to a factor inrease of e^value.

Computes the inverse logit transformation
```R
require(faraway)
ilogit(coef(glm1)[1] + coef(glm1)[2]*w)

fitted(glm1)
````

### Poisson Regression

```R
glm = glm(y ~ x, family="poisson", data = data)
```

### Comparing logit and probit

```R
x <- seq(-1, 5, length.out = 100)
pp <- predict(mprobit, newdata = data.frame(conc = x), type = "response", se.fit = TRUE)
pl <- predict(mlogit, newdata = data.frame(conc = x), type = "response", se.fit = TRUE)
plot(x, ((pl$fit)/(pp$fit)), type = "l", xlab = "dose", ylab = "Ratio")
abline(h = 1, lty = 2)
grid()
````

### Build 0/1 response

````R
df_final$label_glm <- factor(ifelse(df_final$label == 1,1,0))

model_glm <- glm(label_glm ~ x1 + x2 ,family = binomial(),data = df_final)
model_glm

`````
