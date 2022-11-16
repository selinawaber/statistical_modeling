# Statistical Modeling

## Basics

### How to clean the dataset

Times Series "ts" Object

```R
str(data, strict.width="cut")
year<-c(time(data))
coal<-as.numeric(data)
coal_df<-data.frame(coal=coal, year=year)

```

Clean data

```R
data<-read.csv("location", sep="\t") #or sep=","
rownames(data)<-data[,1]
data<-data[,-1]

data_scaled<-scale(data)

data$x<-as.factor(data$x)

data.noNA<-na.omit(data)

```

Create a factor variable for the size of the district and add it as additional
variable (with columnname size) to the data set.

```R
data$size <- factor(data$districtSize, levels = 1:3, labels = c("small", "medium",
"large"))

```

Choose data from a dataset, e.g. for a teacher with 12 years experience
in the district category large.

```R
new_data <- data.frame(districtSize = 3, experience = 12, size = "large")
```

Partition data into levels

cut divides the range of x into intervals and codes the values in x according to which interval they fall. The leftmost interval corresponds to level one, the next leftmost to level two and so on.

```R
data_col <- cut(data$Salary, 3, labels = FALSE)
pairs(data[, 1:3], col = data_col)
```
replace Infty values with NA

```R
data$x[is.infinite(data$x)]<-NA
```

 Subset 
 
 ```R
 fit2 <- update(fit1, subset= 20:50)
 
 ```



### Handling Logitudial Data

````R
library(reshape)

data <- read.table("data.txt", header = TRUE)

data_long<-reshape(data, varying=paste("day", 1:15, sep=""), direction="long", v.names="n.termites", timevar="day", idvar="dish")

dimnames(data_long)[[1]] <- seq(dimnames(data_long)[[1]])
data_long$dose <- factor(data_long$dose)
data_long$dish <- factor(data_long$dish)

`````


### EDA


```R
str(data)
head(data)
summary(data)

data$x<-as.numeric(data$x)

hist(data$y, main="Histogram of x")
abline(v = mean(data$y), lty = 2, col = "blue")

plot(data$y, col=data$x)
legend("topright", legend = c("x1", "x2", "x3", "x4"), col = 1:4, pch = "o")

boxplot(data$y~ data$x, main ="boxplot")

pairs(data)
# choosing pairs
pairs(data[, c(1,3)]) #  1 and 3
pairs(data[, c(1:3)]) # 1 to 3
```

### Add the line of the predicted probabilities to the plot

````R
conc<-dat$conc

plot(dead/30 ~ conc, data=dat, ylim = c(0, 1), xlim = c(-2, 6))

x <- seq(-2, 6, length.out = 2000)

newdat<-expand.grid(conc=x)
newdat<-data.frame(conc=x)

pl <- predict(ml, newdata = newdat, type = "response")
lines(x, pl, col = "blue")
abline(h = 1, col = "red", lty = 2)
abline(h = 0, col = "red", lty = 2)
grid()

`````

### 95\% Confidence Interval of the predicted values

```R
plot(dead/30 ~ conc, data = dat, xlim = c(-1, 5), ylim = c(0, 1))
pl <- predict(ml, newdata = data.frame(conc = x), type = "response", se.fit = TRUE)
lines(x, pl$fit, col = "blue")
lines(x, pl$fit + 1.96 * pl$se.fit, col = 4, lty = 2)
lines(x, pl$fit - 1.96 * pl$se.fit, col = 4, lty = 2)
abline(h = 1, col = "red", lty = 2)
abline(h = 0, col = "red", lty = 2)
grid()

````

