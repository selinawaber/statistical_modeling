# Statistical Modeling

## Generalized Linear Models

```R
glm = glm()
```
Instead of talking by certain factor of increase in y when we change position in x, instead we talk about a change in log odds.

Fromt the summary of the glm in r we get the temp estime. Its value x will tell us about the log odd increase when we increase x. Which corresponds to a factor inrease of e^value.

### Poisson Regression

```R
glm = glm(y ~ x, family="poisson", data = data)
```
