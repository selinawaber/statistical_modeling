# Statistical Modeling

## Survival Analysis

### Creating Survival Object

```R
data$treat<-factor(data$treat)
data.surv<-Surv(time=data$time, event=data$d)
````



### Non parametric methods for survival analysis

Kaplan Meier Kurve, essentially a step function which is a 1:1 fit of the data.

```R
data.fit<- survfit(surv ~ t, data=data)

plot(data.fit, col=c("blue", "red"), xlab="Time since start of treatment", ylab="Cumulative survival probability", main="Kaplan-Meier Plot")
legend("topright", c("Placebo", "Treatment"), lwd=3, col=c("blue", "red"))

```

### Log Rank Test

````R
survdiff(surv ~ t, data = data)
````

### Parametric Modeling of Survival Data

```R
cox <- coxph(surv ~ t, data = data)
summary(cox)

# Formal statistical test
cox.zph(cox)


# Graphical assesment
plot(cox.zph(cox))
abline(h = 0, col = 2, lty = 3)

````
