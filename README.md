# Statistical Modeling

## ![Resampling and Model Validation](resampling_and_model_validation.md?raw=true)


-Test set containing the first and last five years
- Split in training and validation (26 and 10 data points)
- Use predict function for test years

```R
require(fma)
bicoal

str( bicoal, strict.width='cut') # bicoal is a ’ts’ object.

year <- 1920:1968
coal <- as.numeric( bicoal)
coal_df <- data.frame(coal=coal, year = year)

test_df <- coal_df[c(1:5, 44:49),]
new_coal_df <- coal_df[6:43,]

rss <- matrix(NA, ncol=8, nrow=101)

par(mfrow=c(2,4))

for(degree in 1:8){
    plot(year, coal, pch=20, ylim = c(100,800), main = paste0('degree of poly: ',degree))
    for (i in 1:101) {
        set.seed(i)
        train <- sort( sample( 34, 26))
        out <- lm(coal ~ poly(year, degree), data=new_coal_df, subset = train)
        
        res_valid <- predict(out, test_df )
        rss[i,degree] <- sum((res_valid - test_df$coal)^2)
        
        lines(test_df$year[1:5], res_valid[1:5], col='gray')
        lines(test_df$year[6:10], res_valid[6:10], col ='gray')
    }
}

par(mfrow=c(1,1))

which.min(apply(rss,MARGIN = 2,median)) # now, 1 lowest median rss

boxplot(log(rss), main = 'log(rss) by polynomial degree')
abline(h = c(mean(log(rss)),median(log(rss))), col=c('blue','red'),lty=c(2,2))

```

Here the results changes completely. Now the simplest model achieves the lowest mean rss. This is mainly
because we are assessing the capabilities of our model outside the range of the predictor values used for
training.

![Exercise 1 Aufgabe 2b](Images/000031.png?raw=true "Exercise 1 Aufgabe 2b")

![Exercise 1 Aufgabe 2b](Images/000039.png?raw=true "Exercise 1 Aufgabe 2b")

