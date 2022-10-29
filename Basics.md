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

### Handling Logitudial Data

````R
library(reshape)

data <- read.table("data.txt", header = TRUE)

data_long<-reshape(data, varying=paste("day", 1:15, sep=""), direction="long", v.names="n.termites", timevar="day", idvar="dish")

dimnames(data_long)[[1]] <- seq(dimnames(data_long)[[1]])
data_long$dose <- factor(data_long$dose)
data_long$dish <- factor(data_long$dish)

`````
