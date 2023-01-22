# Statistical Modeling

## Miscellaneous

### Test Exam 1
Predict the math scores for a subset of ten students from the dataset. Compare the predictions including
and excluding random effects and discuss the differences to the realised scores
Hint: Use re.form=NA or re.form=NULL to specify if you want to include or exclude random effects in the
prediction

```R

predind <- sample(c(1:dim(data)[1]),10)
predset <- data[predind,c("gender", "social", "raven","school")]

a <- predict(fit2,predset)
b <- predict(fit2,predset,re.form=NA)

comp <- cbind(round(a,0),round(b,0),jspr[predind,"math"])

colnames(comp) <- c("With RE","witout RE","obs")

plot(c(1:10),comp[,2],pch=20, ylim=c(min(comp[,3])-3,max(comp[,3])+3),xlab="Student", ylab="Final mpoints(c(1:10),comp[,c(1)], col="red",pch=20)
points(c(1:10),comp[,c(3)], col="blue",pch=2)
legend("bottomright",legend=c("without RE", "with RE","Observed"), col=c("black","red","blue"),pch 


`````


### Test Exam2


Using the Boston data set (from package MASS), fit classification models in order to predict whether a given suburb has a crime rate above or below the median. Include an EDA. Explore classification tree and LDA. Describe your findings.


````R
# Create indicator variable: crime rate above median
median_cr <- median(boston_data$crim)
boston_data$crim_median <- as.factor(boston_data$crim > median_cr)
levels(boston_data$crim_median) <- c("below","above")

# histogramm
hist(boston_data$crim, breaks = 50, freq = FALSE, main = "Histogram of Crime Rate")
lines(density(boston_data$crim, from = 0), col = 3)
abline(v = median_cr, col = 2)

# Create a new dataframe - without variable crim
boston_data2 <- boston_data[,-1]


# lda with all predictors
lda_ana <- lda(crim_median ~ ., data = boston_data2)
lda_pred <- predict(lda_ana, newdata=boston_data2)
table(lda_pred$class, boston_data2$crim_median, dnn=c("Predicted Class", "Observed Class"))

# accuracy of lda
sum(lda_pred$class == boston_data2$crim_median)/length(boston_data2$crim_median)

# classification tree
require(partykit)
c_tree <- ctree(crim_median~ ., data=boston_data2)
plot(c_tree)
c_tree_pred <- predict(c_tree, newdata=boston_data2)
table(c_tree_pred, boston_data2$crim_median, dnn=c("Predicted Class", "Observed Class"))

# accuracy of classification trees
sum(c_tree_pred == boston_data2$crim_median)/length(boston_data2$crim_median)


#For both model we just computed the training accuracy. We could use cross-validation to verify that the models don’t overfit.

# random forest
require(randomForest)
rf <- randomForest(crim_median~ ., data=boston_data2)
rf_pred <- predict(rf, newdata=boston_data2)
table(rf_pred, boston_data2$crim_median, dnn=c("Predicted Class", "Observed Class"))



```````


