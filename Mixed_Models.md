# Statistical Modeling

## Mixed Models

```R
library(lme4)
# Random intercept (pred2)
# Different starting points determined by pred2, but not different slopes determined by pred2
lmer(response ~ pred1 + (1 | pred2) , data=data)
# For longtidual data, where x is for example time and pred2 is the id of subjects for which we have multiple datapoints over time
lmer(response ~ pred1 + (x | pred2) , data=data)

```
