# Statistical Modeling

## Clustering





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
# Draws rectangles around the branches of a dendrogram highlighting the corresponding clusters. First the dendrogram is cut at a certain level, then a rectangle is drawn around selected branches.

# dendextend package
dend <- as.dendrogram(hc1)
dend1 <- color_branches(dend, k = 6)
dend2 <- color_labels(dend, k = 6)
par(mfrow = c(1, 2))
plot(dend1, main = "Colored branches")
plot(dend2, main = "Colored labels")
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


One Dimensional K-Means Clustering

```R

my_function <- function(x, k){
centx <- array(seq(max(x)/k, max(x), by = max(x)/k), k)
data <- data.frame(x = x, clus = NA)
clus <- data.frame(name = 1:k, centx = centx)
finish <- FALSE
while(finish == FALSE){ for(i in 1:length(x)){
  dist <- sqrt((x[i] - clus$centx)^2)
  data$clus[i] <- which.min(dist)
}
centx_old <- clus$centx
for(i in 1:k){
clus[i, 2] <- mean(subset(data$x, data$clus == i)) }
if(identical(centx_old, clus$centx)) finish <- TRUE
}
data
}
x <- c(1,2,1,3,2,6,5,7,6,12) k=3
my_function(x, k)


```




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
## plotting iris (data frame) in a 2-dimensional plot and partitioning
## into 3 clusters.
install.packages("cluster")
library("cluster")

data.x <- data[, 1:4]
cl3 <- pam(data.x, 3)$clustering
op <- par(mfrow= c(2,2))
clusplot(data.x, cl3, color = TRUE)
```
The R function pam() can be used to compute PAM algorithm. The simplified format is pam(x, k), where “x” is the data and k is the number of clusters to be generated.



