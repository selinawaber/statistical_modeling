
# Statistical Modeling

## Classification

### Linear Discirmination


We assume that both categories have a gaussian distribution.

In the linear case we assume for the distributions to have the same covariance matrices, only the means can vary. We have several distributions and we want to associate each sample to a distribution. The distributions are known, but the samples are intertwined, thus we have to use LDA to decide which sample can be associated with which distribution.

The discrimination line is at the point (in 2D) with the same densities.

```R
lda = lda(response ~ predictor1 + predictor2, data = data)

```

### Quadratic Discrimination

In the quadratic case the distributions can have varying variances. In fact for all samples both variants can be applied but QDA performs with different variances.

```R
qda = qda(response ~ predictor1 + predictor2, data = data)

```

### Fishers Discriminant Rule

It is identital to LDA when covariance matrices are equal.

### Classification Tree

Actually pretty much the same as a K-D tree, but in this case we have more than 3 Dimensions.

- non robust method


```R
ct=ctree(response ~., data=data)
plot(ct)
```

### Bagging

Use bootstrapping to create many training sets and for each of them create a new tree. Take the average for the final classification.

### Boosting 

Similar to bagging but samples are associated with a weight which corresponds to the amount of missclassifications, with this the training will focus on hard cases.

### Random Forest

Similar to bagging but for each tree we choose a random subsample of the featutres, where k = sqrt(n).

```R
rf = randomForest(response ~ ., data = data)
plot(rf)

# Optionally repeat rd with only most important features extracted with
varImpPlot(rf)
rf2 = randomForest(response ~ a + b + c)

```
