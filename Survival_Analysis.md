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

### Density, Distribution, Survival, Hazard Function

````R
density<- function(x, a=1, b=1) {(b/a)*(x/a)^(b-1)/(1+(x/a)^b)^2} 
distribution<- function(x, a=1, b=1) {x^b / (a^b + x^b)}

b <- c(0.5, 1, 2, 4, 8)
time <- seq(0, 10, 0.01)

# Density Function
plot(time, density(time, b=b[1]), type="l", xlab="x", ylab=expression(paste("f(x;",alpha,",",beta,")",sep="")), ylim=c(0, 2), col=1, lty=1, main="Density function of Log-Logistic distribution")

for( i in 2:length(b))
    lines(times, density(time, b=b[i]), col=i)

legend("topright", legned=paste("beta=", b), col=1:length(b), lty=1)

# Distribution Function
plot(time, distribution(time, b=b[1], type="l", xlab="x", ylab=expression(paste("F(x;",alpha,",",beta,")",sep="")), xlim=c(0,10), ylim=c(0,1),
     lty=1, main="Distribution function of Log-Logistic distribution")

# Survival function
plot(time, 1- distribution(time, b=b[1], type="l", xlab="x", ylab=expression(paste("S(x;",alpha,",",beta,")",sep="")), xlim=c(0,10), ylim=c(0,1), col=1, lty=1, main = "Survival function of Log-Logistic distribution")

for(i in 2:length(b))
    lines(time, 1 - distribution(time, b=b[i]), col=i)
    
legend("topright", legend=c(paste("beta =",b)), col=1:length(b), lty=1)

#Hazard Function
plot(time, density(time, b=b[1])/(1-distribution(time, b=b[1])), type="l", xlab="x", ylab=expression(paste("h(x;",alpha,",",beta,")",sep="")),
     xlim=c(0,10), ylim=c(0,6),  main="Hazard function of Log-Logistic distribution")

for(i in 2:length(b))
    lines(time,  density(time, b=b[i])/(1-distirbution(time, b=b[i])), col=i)
    
legend("topright", legend=c(paste("beta =",b)),
       col=1:length(b), lty=1)

````
