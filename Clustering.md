# Statistical Modeling

## Clustering

### Ward method - Dendrogram for Penguin dataset.
```R
#load('download/sta121/Penguins.RData')  # Essentially from pkg palmerpenguins
load(url("http://user.math.uzh.ch/furrer/download/sta121/Penguins.RData"))
hc <- hclust( dist( scale(Penguins[,3:6])), method='ward.D')
dend <- as.dendrogram(hc)
# str( dend)        # a text description of the dendrogram
# plot( dend)       # is virtually similar to plot( hc)
plot( hc, labels=c("-", "--", "---")[Penguins$num_species], hang=-1, main='')
rect.hclust(hc, k=3, border=2:4)             # visualizing 3 clusters
round( rev( hc$height)[1:10], 1)             # height of last ten merges
round( -diff( rev( hc$height))[1:10], 1)     # differences in last ten merges
dd1 <- cutree( hc, 3)             # getting the three clusters
table(data.frame( cluster=dd1, Penguins$species)) # confusion matrix
```

### Complete linkage clustering dendrogram for the Penguin dataset
```R
hc2 <- hclust( dist( scale(Penguins[,3:6])))   # method='complete' is default.
plot( hc2, labels=c("-", "--", "---")[Penguins$num_species], hang=-1, main='')
rect.hclust(hc2, k=3, border=2:4)
round( -diff( rev( hc2$height))[1:10], 1)     # differences in last ten merges
dd2 <- cutree( hc2, 3)
table(data.frame( cluster=dd2, Penguins$species))  # confusion matrix
```


### Single linkage clustering dendrogram for the Penguin dataset
```R
hc3 <- hclust( dist( scale( Penguins[,3:6])), method='single')
plot( hc3, labels=c("-", "--", "---")[Penguins$num_species], hang=-1, main='')
rect.hclust(hc3, k=3, border=2:4)             # visualizing 3 clusters
dd3 <- cutree( hc3, 3)
```

### Clustering jobs dataset with three different hierarchical algorithms:
```R
#par( mfrow=c(3,1))
jobs <- read.csv(header=TRUE, 'http://user.math.uzh.ch/furrer/download/sta121/europejobs.csv') 
# apply(jobs,1,sum) # is 100 up to rounding no scaling necessary

hc1 <- hclust( dist( jobs), method="single") 
plot( hc1, hang=-5, main="Single Method") 
hc2 <- hclust( dist( jobs))      # complete method is the default
plot( hc2, hang=-1, main="Complete Method")
hc3 <- hclust( dist( jobs), method="ward.D")
plot( hc3, hang=-1, main="Ward Method")

```      





### Basic illustration of the k-means algorithm:
```R
par( mfcol=c(1,3))
require( deldir)    # for function 'deldir' and 'tile.list'
require( splancs)   # for function 'inout'
x <- scale(Penguins$bill_depth)
y <- scale(Penguins$bill_length)
centx <- c( -0.5, -.5, 1.5)        # starting centroid centers
centy <- c( -1, 1, 1)
for (i in 1:3){           # Normally looping until convergence, here 7
  # Assignment step:  
  # Here, it is done based on the triangulation of the area. 
  # It could be also done manually, looking at the distance between each point and the
  # the current centers.
    z <- deldir( x=centx, y=centy, rw=c(-2.5,2.5,-2.5,2.5), suppressMsge=TRUE)
    w <- tile.list(z)
    g1 <- inout( cbind(x,y), w[[1]][3:4])
    g2 <- inout( cbind(x,y), w[[2]][3:4])
    g3 <- inout( cbind(x,y), w[[3]][3:4])
    # Intermediate plotting
    plot( y ~ x, pch=Penguins$num, 
         col=g1+2*g2+3*g3, main=paste( "Iteration", i))
    plot( w, add=TRUE)
    points( centx, centy, cex=2, pch=20, col=6)
  
  # Update step:
    centx <- c( mean( x[g1]), mean( x[g2]), mean( x[g3]))
    centy <- c( mean( y[g1]), mean( y[g2]), mean( y[g3]))  
}

## For different starting values you may need more iterations, for example
# centx <- c( -1,0,1)        # starting centroid centers
# centy <- c( -1,0,1)        # close together... bad choice
##  requires four more iterations to converge to the same solution
par( mfrow=c(1,1))
```

### The kmeans algorithm applied to the Penguin data and resulting scatter plot.
```R
cl <- kmeans( scale( Penguins[,3:4]), 3)
# print( cl)  # 'Clustering vector' and 'Available components' not of interest
table( cluster=cl$cluster, Species=Penguins$species)
pairs( Penguins[,3:6], col=cl$cluster, pch=Penguins$num_species, 
      gap=0, lower.panel=NULL, xaxt="n", yaxt="n")
unlist( cl[grepl('ss', names(cl))]) # extract all variables with 'ss' in name
cbind( cl$centers,cbind(centy, centx)[c(1,3,2),] )
      
      

## require(cluster)
## set.seed(14)
## ks <- kmeans( swiss, centers=2)
## si <- silhouette( ks$cluster, dist(swiss))
## plot( si, col=2:3, main='')
## for (k in 3:4)  # compact form for other `k`
##   plot( silhouette( kmeans( swiss, centers=k)$cluster, dist(swiss)),
##       col=2:(k+1), main='')
## # pairs(swiss, col=ks$cluster, pch=19, lower.panel=NULL, gap=0)

```


### Silhouette plots of swiss dataset
```R
par(mfrow=c(1,3), cex=.75)
require(cluster)
set.seed(14)
ks <- kmeans( swiss, centers=2)
si <- silhouette( ks$cluster, dist(swiss))
plot( si, col=2:3, main='')
for (k in 3:4)  # compact form for other `k`
  plot( silhouette( kmeans( swiss, centers=k)$cluster, dist(swiss)), 
      col=2:(k+1), main='')
# pairs(swiss, col=ks$cluster, pch=19, lower.panel=NULL, gap=0)
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

### Clusters and resulting bivariate density. (lecture III)
```R
require( mclust)
data("geyser", package="MASS")
mcl <- Mclust(geyser)
summary(mcl)
plot( mcl, what="classification")
legend("bottomleft", legend=paste("Cluster", 1:4), bty='n', 
       pch=mclust.options("classPlotSymbols")[1:4], # is 16  0 17  3
       col=mclust.options("classPlotColors")[1:4])  # or some approx color
uncert <- which.max( mcl$uncertainty)  # most difficult observation to cluster
points( geyser[uncert,], cex=2, lwd=2)
round( mcl$z[which.max(mcl$uncertainty),], 3)  # prob of belonging to cluster
plot( mcl, what="density", type="persp")
mcl$parameters$pro                             # mixture weights pi_k
```      
