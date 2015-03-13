# ACUPCC Histograms #
```
acupcc.all = read.csv('ACUPCC-clean-finished-trim.csv', header=T)
## take yearly means, only in 21st century
require(plyr)
acupcc.mean = ddply( subset(acupcc.all, year>=2000), 'name', function(x) { colMeans(x[,-1:-2])})
## transform from long form (many columns) to wide form (works better with lattice)
require(reshape)
acupcc.mean.wide = melt(bb, id.vars='name')
## densityplot of all the acupcc variables
## logscale of X -- maybe not the best for heating/cooling days, set log=F to see these in linear
## also, some emissions have negative values, not shown on logscale
require(lattice)
## plot to file
png('acupcc-variables-histograms.png', width=2600, height=1600, res=150)
plot(
    densityplot(~value|variable, acupcc.mean.wide, plot.points='rug', scales=list(x=list(relation='free', log=T)), as.table=T, ylim=c(-0.1, 1))
)
dev.off()
```