# Statistical Modeling

## Survival Analysis

### Non parametric methods for survival analysis

Kaplan Meier Kurve, essentially a step function which is a 1:1 fit of the data.

```R
survfit(surv ~ t, data=data)

```

### Log Rank Test

````R
survdiff(surv ~ t, data = data)
````

### Parametric Modeling of Survival Data

```R
cox = coxph(surv ~ rx, data = data)
summary(cox)

# Formal statistical test
cox.zph()
# Graphical assesment
plot(cox.zph())

````
