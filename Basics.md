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
