# Statistical Modeling

## Clustering

Clean the input data

```R
data<-read.csv("data.txt", sep="\t")
rownames(data)<-data[,1]
data<-data[,-1]
data_scaled <- scale(data)

````



### Hierarchical Clustering
Dissimilarity can be defined with different strategies.

- Single Linkage: (Nearest neighbour linkage) will measure the distance between the two nearest samples from two groups.
- Complete Linkage: (Furhest Neighbout linkage) will measure the distance between the two furthest samples from the two groups.
- Ward Method: In each step find the pair which leads to minimal increase in total within cluster variance.

```R
# hclust requries a dissimilary structure, in this case we will go with dist which computes the eulerian distance between all the rows of the input matrix
hc1 = hclust(dist(matrix), method = "single")
hc2 = hclust(dist(matrix), method = "complete")
hc3 = hclust(dist(matrix), method = "ward.D")

# We can plot the clustering like this:
plot(hc1)

# We can return a datastructure where each item is assigned to a cluster, provided number of clusters
# The output of each cutree() call represents the cluster assignments for each observation in the original dataset.
cut = cutree(hc1, 6)

# We can do the same visually like this:
par(mfrow = c(1, 1))
plot(hc1, xlab = "", sub = "")
rect.hclust(hc1, k = 5, border = rainbow(6))

```

### Ward method - Dendrogram
```R

hc <- hclust( dist( scale(data)), method='ward.D')
dend <- as.dendrogram(hc)
# str( dend)        # a text description of the dendrogram
# plot( dend)       # is virtually similar to plot( hc)
plot( hc, labels=c("-", "--", "---")[data$num_species], hang=-1, main='')
rect.hclust(hc, k=3, border=2:4)             # visualizing 3 clusters
round( rev( hc$height)[1:10], 1)             # height of last ten merges
round( -diff( rev( hc$height))[1:10], 1)     # differences in last ten merges
dd1 <- cutree( hc, 3)             # getting the three clusters
table(data.frame( cluster=dd1, data$species)) # confusion matrix
```


### Conduct cluster analysis

Is it possible to cut the tree such that the resulting classification can be interpreted in
a meaningful way?

```R
hc1_cut<-cutree(hc1,6)
head(hc1_cut)

colo<-rainbow(6)[as.numeric(hc1_cut)]
plot(hc1)
rect.hclust(hc1, k=5, border=rainbow(6))

```


Alternative with the dendextend package

```R
dend <- as.dendrogram(hc1)
dend1 <- color_branches(dend, k = 6)
dend2 <- color_labels(dend, k = 6)
par(mfrow = c(1, 2))
plot(dend1, main = "Colored branches")
plot(dend2, main = "Colored labels")
```


### K-Means Clustering

- 1) Start with k random cluster centers
- 2) Assign observations to the nearest center
- 3) Recompute the centers
- 4) If centers remain the same, stop; otherwise repeat stept 2


```R
my.kmean<-function(x,k){
  #define ranom centroids for the k clusters
  tmp_centroids<-runif(min=min(x), max=max(x), n=k)
  
  #define first cluster assigment of x_i and definde df
  X<-matrix(rep(x,k), ncol=k, byrow=F)
  
  dist_matrix<-(sweep(X, MARGIN=2, temp_centroids))^2
  clusters<-apply(dist_matrix, MARGIN=1, FUN=which.min)
  tmp_df<-cbind.data.frame(x=x, cluster=clusters)
  
  #iteratively update centroids
  tmp_count<-0
  
  repeat{
    #recompute means
    tmp_centroids_new<-aggregate(x~cluster, data=tmp_df, mean)[,2]
    
    #reassign obs
    tmp_df$cluster<-apply((sweep(X, MARGIN=2, temp_centroids_new))^2, MARGIN=1, FUN=which.min)
    
    #+1 count
    tmp_count<-tmp_count +1
    
    #check if the distance between old and new centoids is small
    if(sum(tmp_centroids-tmp_centroids_new)^2< 1e-05)
      break
      
    #check max interations
    if(tmp_count=1000)
      (break)("max iteration reached.")
      
    tmp_centroids<-temp_centroids_new
  }
return(tmp_df)
}


```


```R
set.seed(71)
to_plot <- my.kmean(x = c(rnorm(100), rnorm(100, mean = 1), rnorm(100, mean = 2)),
k = 5)
plot(y = 1:dim(to_plot)[1], x = to_plot$x, col = to_plot$cluster, xlab = "x", ylab = " ")
abline(v = aggregate(data = to_plot, x ~ cluster, mean)[, 2], col = 1:5, lty = 2)
```


```R
kmeans(x, centers, iter.max = 10, nstart = 1)

```

- x: numeric matrix, numeric data frame or a numeric vector
- centers: Possible values are the number of clusters (k) or a set of initial (distinct) cluster centers. If a number, a random set of (distinct) rows     in   x is chosen as the initial centers.
- iter.max: The maximum number of iterations allowed. Default value is 10.
- nstart: The number of random starting partitions when centers is a number. Trying nstart > 1 is often recommended.







```R
km <- kmeans(dat, center = 4, nstart = 20)
plot(dat[, 1], dat[, 2], col = km$cluster, xlab = "Work", ylab = "Price")
plot(dat[, 1], dat[, 3], col = km$cluster, xlab = "Work", ylab = "Salary")
plot(dat[, 2], dat[, 3], col = km$cluster, xlab = "Price", ylab = "Salary")
centroids_clusters <- cbind.data.frame(dat, km$cluster)
names(centroids_clusters) <- c("Work", "Price", "Salary", "Cluster")
aggregate(data = centroids_clusters, . ~ Cluster, mean)
````




### Silhouette plots

Shilouette plots indicate the nearest distance of each sample to the another cluster. Distances towards 1 are favourable, 0 or even negative may indicate wrong assignments.


```R
require(cluster)
set.seed(1)
ks <- kmeans( data, centers=2)
si <- silhouette( ks$cluster, dist(data))
plot( si, col=2:3, main='')
for (k in 3:4)  # compact form for other `k`
  plot( silhouette( kmeans( data, centers=k)$cluster, dist(data)), 
      col=2:(k+1), main='')
# pairs(data, col=ks$cluster)
```

### alternative clustering through package 'cluster'
```R
require( cluster)

cl <- pam( iris[,1:4], 3)  # essentially a k-means
sl <- silhouette( cl)
plot( sl)  
abline( v=mean( sl[,3]))

clusplot( cl)
```


