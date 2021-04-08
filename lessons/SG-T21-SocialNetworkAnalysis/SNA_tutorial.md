---
layout: page
title: "SNA Tutorial"
author: "David N Fisher"
date: "15 March 2021"
visible: true
output: html_document
tags:
  # languages
  - R
  # levels
  - beginner
---
 - **Author**: [David Fisher](https://www.abdn.ac.uk/people/david.fisher/)
 
## An introduction to social network analysis

Croft et al 2008 "Exploring Animal Social Networks" is a good basic introductory book.

Note you will need the following R packages:
sna
vegan
ergm
ergm.count
tnet


```{r, echo = F, error = F, warning = F, message = F} invisible(lapply(c("sna", "tnet", "vegan", "ergm", "ergm.count"), require, character.only = TRUE))
```

## Different types of network data

First we're going to look at some of the different formats of data that can be turned into networks.

### Edge list

Each line is the IDs of two individuals that interacted.

We can pass that to the function "network" as long as we specify it is an edgelist. 

We also need to specify the network is not directed. 
This means all connections are symmetrical i.e., the association *A -> B* is equal to *B -> A*. 
There are situations where this is not true e.g., in networks of dominance interactions.

```{r}
(el = cbind(c(1,1,2),c(2,3,3)))

            [,1] [,2]
      [1,]    1    2
      [2,]    1    3
      [3,]    2    3
```

```{r}
el.net=network(el,matrix.type="edgelist",directed=FALSE)

gplot(el.net, vertex.col="firebrick4")
```
![image](https://user-images.githubusercontent.com/56924820/114059873-b685d680-988c-11eb-94dd-645a4e853a26.png)

### Group by indivdual matrix

Each line is a group sighting, with a 1 if that individual was seen in that group, or a 0 if not.

To convert this n (number of individuals) x m (number of groups) rectangular matrix into a n x n square matrix we multiple the GBI matrix by its transpose using the function "%*%t".

We then need to set the diagonal of this matrix, which represents self-links, to zero, as we typically don't consider these.

```{r}
(gbi=matrix(c(1,1,0,1,0,1,0,1,1),3,3))
   
            [,1] [,2] [,3]
      [1,]    1    1    0
      [2,]    1    0    1
      [3,]    0    1    1
```
```{r}
gbi.net=gbi%*%t(gbi) 
diag(gbi.net)=0

gplot(gbi.net, vertex.col="darkgoldenrod1")
```
![image](https://user-images.githubusercontent.com/56924820/114065019-1fbc1880-9892-11eb-853d-77530fd7089a.png)

### Association matrix

We can of course directly input the n by n matrix of interactions/associations.

Again we can use the "network" function as long as we specify it is an adjacency matrix.

```{r}
(am = matrix(c(0,1,0,0,0,1,1,0,0),3,3))
   
            [,1] [,2] [,3]
      [1,]    0    0    1
      [2,]    1    0    0
      [3,]    0    1    0
```

```{r}
am.net=network(am, matrix.type="adjacency",directed=FALSE)

gplot(am.net, vertex.col="cornflowerblue")
```
![image](https://user-images.githubusercontent.com/56924820/114065204-4ed28a00-9892-11eb-8561-e4186ccf1f33.png)

*Note all these examples are with "binary" networks, where links either exist, or they do not, hence 0 or 1 in the matrices.
However, we can represent interactions of different strengths by using values other than 1. 
These could be counts of interactions, ranging from 1 upwards, or they could be relative measures of interaction strength, ranging from 0-1. 
The latter case is not handled brilliantly by some network packages.*

## Plotting networks

Load a data set from a paper of mine to explore plotting options a bit more.
Data are from "Comparing pre- and post-copulatory mate competition using social network analysis in wild crickets":

> Fisher, Rodríguez-Muñoz, Tregenza 2015: https://academic.oup.com/beheco/article/27/3/912/2366164 

```{r}
load("cricket_network_data.RData")
```

This contains two networks, one based on male crickets fighting each other, the other where they were linked if they mated with the same female.

```{r}
head(mfnet06sym) #the fighting network

   X1 X2 X6 X11 X16 X20 X23 X24 X25 X28 X31 X34 X36 X38 X41 X54 X55 X56 X66 X68 X70 X72 X76 X79 X82 X86 X87 X93
1   0  0  0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   1   0   1   0   0   0   0   0   0   0   0
2   0  0  3   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0
6   0  3  0   0   0   0   0   0   0   0   0   0   0   0   0   1   0   0   1   0   1   0   0   0   0   0   0   0
11  0  0  0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0
16  0  0  0   0   0   0   0   0   0   0   0   1   0   0   0   1   0   0   0   0   0   0   0   0   0   0   0   0
20  0  0  0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0
   X98 X112 X115 X129 X133 X136 X139 X140 X143 X144 X150
1    0    0    0    0    0    0    0    0    0    0    0
2    0    0    0    0    0    0    0    0    0    0    0
6    0    0    5    0    0    0    0    0    0    1    1
11   0    0    0    0    0    0    0    0    0    0    0
16   4    0    1    1    0    0    0    0    0    0    0
20   0    0    0    0    0    0    0    0    0    0    0
```

```{r}
head(mmnet06sym) #the sperm competition network

   X1 X2 X6 X11 X16 X20 X23 X24 X25 X28 X31 X34 X36      X38 X41       X54 X55 X56 X66 X68 X70 X72 X76 X79 X82
1   0  0  0   0   1   0   0   0   0   0   0   0   0 0.000000   0  0.000000   0   0   0   0   0   0   0   0   0
2   0  0  0   0   0   0   0   0   0   0   0   0   0 0.000000   0  0.000000   0   0   0   0   0   0   0   0   0
6   0  0  0   0   0   0   3   0   0   0   0   0   0 4.472136   0 22.583180   0   0   0   0   0   0   0   0   0
11  0  0  0   0   0   0   0   0   0   0   0   0   0 0.000000   0  0.000000   0   0   0   0   0   0   0   0   0
16  1  0  0   0   0   0   1   0   0   0   0   0   0 0.000000   0  1.414214   0   0   0   0   0   0   0   0   0
20  0  0  0   0   0   0   0   0   0   0   0   0   0 0.000000   0  0.000000   0   0   0   0   0   0   0   0   0
   X86 X87 X93      X98 X112     X115 X129 X133 X136     X139     X140 X143    X144     X150
1    0   0   0  0.00000    0 0.000000    0    0    0 0.000000  0.00000    0 0.00000 0.000000
2    0   0   0  0.00000    0 0.000000    0    0    0 0.000000  0.00000    0 0.00000 0.000000
6    0   0   0 20.12461    0 5.744563    0    0    0 3.741657 15.87451    0 2.44949 1.414214
11   0   0   0  0.00000    0 0.000000    0    0    0 0.000000  0.00000    0 0.00000 0.000000
16   0   0   0  0.00000    0 1.732051    0    0    0 0.000000  0.00000    1 0.00000 0.000000
20   0   0   0  0.00000    0 0.000000    0    0    0 0.000000  0.00000    0 0.00000 0.000000
```
The data are symmetrical adjacency matrices and include edge weights.

```{r}
mfnetwork = network(mfnet06sym, 
                      directed=F, 
                      ignore.eval=FALSE, 
                      names.eval="weight")
gplot(mfnetwork, gmode="graph")
```
![image](https://user-images.githubusercontent.com/56924820/114065620-c6a0b480-9892-11eb-9c68-2704ce37ef99.png)

```{r}
gplot(mfnetwork, gmode="graph", mode="circle")
```
![image](https://user-images.githubusercontent.com/56924820/114065660-cef8ef80-9892-11eb-856c-92a5575eaaf6.png)

```{r}
mmnetwork = network(mmnet06sym, #we'll need this later
                    directed=F, 
                    ignore.eval=FALSE, 
                    names.eval="weight")
```

So far so good, but we want to incorporate traits of our individuals.

```{r}
summary(traits06)

       ID       aggression         mass                    flavour  
 1      : 1   Min.   :0.000   Min.   :0.6972   apple           :11  
 10     : 1   1st Qu.:2.000   1st Qu.:0.9163   chocolate       : 8  
 11     : 1   Median :2.000   Median :1.0628   golden retriever: 7  
 12     : 1   Mean   :2.923   Mean   :1.0673   haggis          : 4  
 13     : 1   3rd Qu.:4.000   3rd Qu.:1.2157   nutty           : 9  
 14     : 1   Max.   :8.000   Max.   :1.5305                        
 (Other):33                                                         
 ```
We add these as properties of the nodes (a.k.a "vertices"", hence the v).

*Note I've already ensured the traits are in the same order as the nodes in the network*

```{r}
mfnetwork %v% "aggression" <- traits06$aggression
mfnetwork %v% "mass" <- traits06$mass
mmnetwork %v% "aggression" <- traits06$aggression
mmnetwork %v% "mass" <- traits06$mass
```

Plot fighting network with range of reds for aggression, vertex size based on mass (squared to increase variation), and edge thickness representing number of interactions.
```{r}
palette(colorRampPalette(c("coral", "darkred"))(10))
gplot(mfnetwork, gmode="graph", 
      vertex.cex= (mfnetwork %v% "mass")^2, 
      vertex.col=mfnetwork %v% "aggression", 
      vertex.border = "black",
      edge.col="darkgrey", 
      edge.lwd=mfnetwork %e% "weight")
```
![image](https://user-images.githubusercontent.com/56924820/114065770-ee901800-9892-11eb-98a6-6e54144132e4.png)

Then plot the sperm competition network with a range of blues for aggression.

```{r, echo = F}
palette(colorRampPalette(c("aliceblue", "darkblue"))(10))
gplot(mmnetwork, gmode="graph", 
      vertex.cex= (mfnetwork %v% "mass")^2, 
      vertex.col=mfnetwork %v% "aggression",
      edge.col="darkgrey", 
      edge.lwd=mfnetwork %e% "weight", 
      vertex.border = "black")
```
![image](https://user-images.githubusercontent.com/56924820/114065792-f5b72600-9892-11eb-9de7-599459a77bbb.png)

So these are pretty and all, but it is actually kind of hard to compare them visually as the vertex locations change from plot to plot.
We are going to manually set node coordinates. We sum the two networks to give an overall network, extract the coodinates from it, then use that to plot.

```{r}
allnetwork<-network(mmnet06sym+mfnet06sym, 
                    directed =F, 
                    ignore.eval=F, 
                    names.eval="weight")
pos.all<-network.layout.fruchtermanreingold(allnetwork, layout.par=NULL )
palette(colorRampPalette(c("coral", "darkred"))(10))
gplot(mfnetwork, gmode="graph", 
      vertex.cex= (mfnetwork %v% "mass")^2, 
      vertex.col=mfnetwork %v% "aggression",
      edge.col="darkgrey", 
      edge.lwd=mfnetwork %e% "weight", 
      vertex.border = "black", 
      coord=pos.all)
```
![image](https://user-images.githubusercontent.com/56924820/114065844-0071bb00-9893-11eb-81b1-081cad36114b.png)

```{r, echo = F}
palette(colorRampPalette(c("aliceblue", "darkblue"))(10))
gplot(mmnetwork, gmode="graph", 
      vertex.cex= (mfnetwork %v% "mass")^2, 
      vertex.col=mfnetwork %v% "aggression",
      edge.col="darkgrey", 
      edge.lwd=mfnetwork %e% "weight", 
      vertex.border = "black", 
      coord=pos.all)
```
![image](https://user-images.githubusercontent.com/56924820/114065865-04054200-9893-11eb-95c8-c8fac526d520.png)

## Network characteristics

### Network-level measures

We can calculate measures that summarize something about the entire network e.g., degree correlation, clustering coefficient, or connectedness (a.k.a. density, the proportion of all possible connections that are actually present):

```{r}
connectedness(mmnetwork)

[1] 0.4385965
```
```{r}
connectedness(mfnetwork)

[1] 0.6275304
```

### Individual-level measures

We can also calculate measures for each individual that represent their unique pattern of connections in some way, e.g., "degree" and "betweenness".

I did this for the a question in the BE paper: What is the correlation within-individuals in engagement in pre- and post-copulatory competition. 

We can calculate the **unweighted** degree - the number of unique individuals a cricket interacts with:

```{r}
mmnet06.deg<-sna::degree(mmnet06sym,  cmode="outdegree", ignore.eval=T) 
mfnet06.deg<-sna::degree(mfnet06sym,  cmode="outdegree", ignore.eval=T)
```

```{r}
plot(jitter(mmnet06.deg),jitter(mfnet06.deg), pch=16, 
     xlab = "Degree in fighting network", 
     ylab = "Degree in sperm competition network")
```
![image](https://user-images.githubusercontent.com/56924820/114066926-2481cc00-9894-11eb-93d7-6945af30f6cb.png)

```{r, warning = F}
cor.test(mmnet06.deg,mfnet06.deg,method="spearman")$est

      rho 
0.6781513 
```

I used the **weighted** degree - the total number of interactions an individual has, i.e., summing all their edge weights:

```{r}
mmnet06.wdeg<-sna::degree(mmnet06sym,  cmode="outdegree",ignore.eval=F)
mfnet06.wdeg<-sna::degree(mfnet06sym,  cmode="outdegree",ignore.eval=F)
```

```{r, echo = F, warning = F}
plot(jitter(mmnet06.wdeg),jitter(mfnet06.wdeg), pch=16, 
     xlab = "Weighted degree in fighting network", 
     ylab = "Weighted degree in sperm competition network")
```
![image](https://user-images.githubusercontent.com/56924820/114066999-38c5c900-9894-11eb-8d4f-7f0bc58eadbf.png)

```{r, echo = F, warning = F}
cor.test(mmnet06.wdeg, mfnet06.wdeg,method="spearman")$est

      rho 
0.7016897 
```

## Statistical tests in networks

### Randomisations of node labels

Often we want to answer questions like "Is there an association between aggression and degree?", or "Is the degree correlation I have found bigger than expected?"

A straightforward way to do the first one is:

```{r}
cor.test(mmnet06.wdeg, traits06$aggression)


	Pearson's product-moment correlation

data:  mmnet06.wdeg and traits06$aggression
t = -1.5103, df = 37, p-value = 0.1395
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.5171702  0.0806766
sample estimates:
       cor 
-0.2409744 
```

However, as degree scores are non-independent, they violate the assumptions of the test, and so the p-value is not valid.

Instead, what we do is compare the observed correlation (-0.24 in this case) with a distribution of 1000 correlations from networks where the node identities have been randomized. 
See:

> Croft et al. 2011: https://www.sciencedirect.com/science/article/pii/S0169534711001455

So we will compare the observed correlation from this network:

```{r}
gplot(mfnetwork, gmode="graph", 
      vertex.col = "red",
      edge.col = "black",
      vertex.border = "black", 
      coord=pos.all)
```
![image](https://user-images.githubusercontent.com/56924820/114067166-690d6780-9894-11eb-96ac-3a112f25aab7.png)

vs. from a set of randomized networks:

```{r}
par(mfrow = c(1,3))
for (i in 1:3) {
gplot(rmperm(mfnetwork), gmode="graph", 
      vertex.col = "deeppink",
      edge.col = "black",
      vertex.border = "black", 
      coord=pos.all)
}
par(mfrow = c(1,1))
```
![image](https://user-images.githubusercontent.com/56924820/114067208-732f6600-9894-11eb-90a8-1d0f49ed92d8.png)

```{r}
obs_r = cor.test(mmnet06.wdeg, traits06$aggression)$est
r_r = numeric(1000)
for (i in 1:1000) {
ran_net = rmperm(mmnetwork) #randomise network
ran_wdeg = sna::degree(ran_net,  cmode="outdegree",ignore.eval=F) #calculate degree
r_r[i] = cor.test(ran_wdeg, traits06$aggression)$est #calculate correlation and place in vector
}
```

```{r}
hist(r_r)
abline(v = obs_r, col="red")
```
![image](https://user-images.githubusercontent.com/56924820/114067232-7d516480-9894-11eb-9b3f-bb31f5e79bf7.png)

What is our p value? As we have conducted a two-tailed test (we are looking to see if the observed correlation is larger or smaller than the randomized ones) we take the smaller of the total number of occasions that the observed is larger or small than the distribution of randomized values, divide by the number of randomizations (1000), then multiply by 2 (as two-tailed).
So if the observed value is larger than 999 of the randomizations, it is smaller than 1, so we take 1, divide by 1000, multiply by 2, giving a p value of 0.002.

```{r}
min(c(sum(obs_r<r_r), sum(obs_r>r_r))) /500 #note /1000*2 simplifies to /500

[1] 0.148
```

So the observed correlation is not significantly different from what we would expect by chance.

### Randomisation of data

Sometimes our data collection *method* will tend towards certain types of network structure or relationships, 
even when there is no *biological* process generating that relationship. 
For example, males might be more likely to be observed than females with a particular method we are using. 
This would lead to males having more connections. But does this actually represent higher sociability for males? 
No. To confirm that, we need to conduct our analyses accounting for sighting rate. 

Until recently, this would have been done by randomizing the data stream, such as your group-by-individual matrix, where the sighting rates of individuals are maintained but the exact pattern of connections is randomized. So you repeat the model "network_metric ~ sex" 1000 times, each time using a new randomized data set. 
See:

> Farine & Whitehead 2015: https://besjournals.onlinelibrary.wiley.com/doi/10.1111/1365-2656.12418

However, this approach has recently been shown to be anti-conservative, with a false positive rate of much higher 
than 5%:

> Weiss et al. 2020: https://besjournals.onlinelibrary.wiley.com/doi/10.1111/2041-210X.13508

A better approach is to include sighting rate as a fixed effect in our model of interactions (so the model becomes network_metric ~ sex + sighting rate):

> Franks et al. 2020: https://besjournals.onlinelibrary.wiley.com/doi/full/10.1111/2041-210X.13429

While in *some* situations exponential random graph models (see below) have lower error rates:

> Evans, Fisher, Silk 2020: https://academic.oup.com/beheco/article/31/5/1266/5904748

 I can't really recommend the use of datastream permutations for regression-based analysis of networks and other traits.

What may still be fine is randomizing the data in order to calculate network-level measures, and then comparing those with the observed value. These need to be **biologically valid** null models however. 
Comparing a real network to completely randomized data has been likened to:

> *"Comparing a skyscraper to a pile of rubble"*

So you *need* to design your randomizations such that observations are only swapped within the same location, 
or in the same year, or however your data is structured. This can be challenging and often needs custom code. One solution I found was using "group_by" (from the tidyverse package), grouping my edgelist by both "timepoint" and "colony ID" (*this was for an analysis on social interactions in colonies of social spiders over time*). I then randomised the identity of one ID in each edge (using "new_ID2 = sample(ID2, replace = F)"), and recalculated the network and appropriate statistic (1000 times...). I am not showing that here as it is ~~ugly~~ ~~long~~ bespoke to 
the dataset and the question. But be aware that you need appropriate null models!

## Correlating networks

We might want to know if two different networks, of the same dimensions, are correlated. So, do more genetically related individuals interact more with each other, or do individuals closer in space interact more with each other?
This requires the networks to be the same size and in the same order, I've already sorted this out for the data we are using.

*Note we can't correlate networks that are different dimensions. In general, comparing networks with different dimensions is quite difficult.*

### Mantel test

One option is the mantel test:
```{r}
mantel(mfnet06sym, mmnet06sym)

Mantel statistic based on Pearson's product-moment correlation 

Call:
mantel(xdis = mfnet06sym, ydis = mmnet06sym) 

Mantel statistic r: 0.4605 
      Significance: 0.001 

Upper quantiles of permutations (null model):
   90%    95%  97.5%    99% 
0.0615 0.0905 0.1241 0.1673 
Permutation: free
Number of permutations: 999
```

### Network regression

This is fine, but we might want to control for some other things. We did this for a question in the BE paper, looking at the relationship between the intensity of pre- and post-copulatory competition within pairs of competing males. We wanted to look at the correlation between their interaction strength in the fighting network and the interaction strength in the sperm comp network, while controlling for how much the males overlapped in space and time.

We can do that with a network regression:
```{r}
sperm.pred06<- list(mmnet06sym, mtime06, mspace06)

sperm.model06<-netlm(mfnet06sym, sperm.pred06, intercept=T, mode="graph", nullhyp="qapspp")
summary(sperm.model06)

OLS Network Model

Residuals:
          0%          25%          50%          75%         100% 
-2.150542592 -0.054652251 -0.009498181  0.013078853  3.877989719 

Coefficients:
            Estimate     Pr(<=b) Pr(>=b) Pr(>=|b|)
(intercept) -0.031140481 0.202   0.798   0.203    
x1           0.034187454 0.999   0.001   0.001    
x2           0.004515407 0.998   0.002   0.002    
x3           0.070134348 1.000   0.000   0.000    

Residual standard error: 0.4264 on 737 degrees of freedom
Multiple R-squared: 0.3528 	Adjusted R-squared: 0.3502 
F-statistic: 133.9 on 3 and 737 degrees of freedom, p-value:     0 


Test Diagnostics:

	Null Hypothesis: qapspp 
	Replications: 1000 
	Coefficient Distribution Summary:

       (intercept)       x1       x2       x3
Min       -3.41898 -3.12566 -2.68395 -2.69518
1stQ      -1.30476 -0.52709 -0.61962 -0.71797
Median    -0.82381 -0.02479 -0.09201 -0.12128
Mean      -0.83189  0.05571 -0.03394 -0.02901
3rdQ      -0.35738  0.56485  0.51279  0.56754
Max        1.56262  5.05669  3.75805  5.41451
```

*Note we might want to rescale each variable to equivalent units by doing something like this:*

```{r}
s.mmnet06sym<- (mmnet06sym-mean(mmnet06sym))/sd(mmnet06sym)
s.mtime06 <- (mtime06 -mean(mtime06))/sd(mtime06)
s.mspace06 <- (mspace06 -mean(mspace06))/sd(mspace06)
```

*Can do that for each variable and then repeat*

### Exponential Random Graph Models

If we want to get **really** fancy, we can fit a ERGM. This models the probability of links forming in a network, 
given what predictor variables you specify. The best thing about ERGMs is, you can use node-level and dyad-level covariates (including another network), and network structure-based terms. check out this paper on ERGMs for animal behaviour research here: 

> Silk & Fisher 2017: https://www.sciencedirect.com/science/article/pii/S0003347217302543 

So we're going to fit an ERGM to the fighting network. We want to see if the sperm competition network influences it's structure, while controlling for the overlap in space and time. We are also including a cricket's mass and aggression as node-level covariates, to see if these variables influence the number of fights a cricket has.

Note we use the "CMP" term as Using it fits a "Conway-Maxwell-Poisson" distribution that is Poisson when its coefficient is 0 and geometric when its coefficient is 1, as this distribution suits counts of interactions. 
We use the "nonzero" term to model the probability that any two crickets may not interact. We expect this term to be *negative* as most pairs of crickets do **not** interact (i.e., the network is "sparse"; as is typically true for animal social networks).

```{r, warning = F, message= F}
ergm_fit<- ergm(mfnetwork ~   CMP + nonzero +
                     nodecov("mass")+ nodecov("aggression") + 
                     edgecov(s.mtime06) + edgecov(s.mspace06) + edgecov(s.mmnet06sym),
                    reference=~Poisson, response="weight")
summary(ergm_fit)

==========================
Summary of model fit
==========================

Call:
ergm(formula = mfnetwork ~ CMP + nonzero + nodecov("mass") + 
    nodecov("aggression") + edgecov(s.mtime06) + edgecov(s.mspace06) + 
    edgecov(s.mmnet06sym), response = "weight", reference = ~Poisson)

Iterations:  5 out of 20 

Monte Carlo MLE Results:
                        Estimate Std. Error MCMC % z value Pr(>|z|)    
CMP                    -0.563458   0.438200      0  -1.286 0.198496    
nonzero                -1.876193   0.553424      0  -3.390 0.000699 ***
nodecov.sum.mass       -0.486202   0.237647      0  -2.046 0.040766 *  
nodecov.sum.aggression -0.008635   0.033400      0  -0.259 0.796008    
edgecov.sum.NULL        0.393370   0.084381      0   4.662  < 1e-04 ***
edgecov.sum.NULL.1      0.296110   0.063733      0   4.646  < 1e-04 ***
edgecov.sum.NULL.2      0.033036   0.041046      0   0.805 0.420913    
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

     Null Deviance:     0  on 741  degrees of freedom
 Residual Deviance: -1137  on 734  degrees of freedom
 
Note that the null model likelihood and deviance are defined to be 0. This means that all
likelihood-based inference (LRT, Analysis of Deviance, AIC, BIC, etc.) is only valid between models
with the same reference distribution and constraints.

AIC: -1123    BIC: -1091    (Smaller is better.) 
```

* Lighter crickets have fewer fighting interactions
* Aggression has no impact on fighting interactions
* Greater overlap in both time and space increases interactions
* Being connected in sperm competition network does not increase chances of fighting

Is this model any good? Can look at diagnostics this way (*it is long so not showing here*):
```{r, eval = F}
mcmc.diagnostics(ergm_fit)

Show in New Window
     [,1] [,2]
[1,]    1    2
[2,]    1    3
[3,]    2    3
Show in New Window

Show in New Window
     [,1] [,2] [,3]
[1,]    1    1    0
[2,]    1    0    1
[3,]    0    1    1
Show in New Window

Show in New Window
     [,1] [,2] [,3]
[1,]    0    0    1
[2,]    1    0    0
[3,]    0    1    0
Show in New Window

Show in New Window
   X1 X2 X6 X11 X16 X20 X23 X24 X25 X28 X31 X34 X36 X38 X41 X54 X55 X56 X66 X68 X70 X72 X76 X79 X82 X86 X87 X93
1   0  0  0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   1   0   1   0   0   0   0   0   0   0   0
2   0  0  3   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0
6   0  3  0   0   0   0   0   0   0   0   0   0   0   0   0   1   0   0   1   0   1   0   0   0   0   0   0   0
11  0  0  0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0
16  0  0  0   0   0   0   0   0   0   0   0   1   0   0   0   1   0   0   0   0   0   0   0   0   0   0   0   0
20  0  0  0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0   0
   X98 X112 X115 X129 X133 X136 X139 X140 X143 X144 X150
1    0    0    0    0    0    0    0    0    0    0    0
2    0    0    0    0    0    0    0    0    0    0    0
6    0    0    5    0    0    0    0    0    0    1    1
11   0    0    0    0    0    0    0    0    0    0    0
16   4    0    1    1    0    0    0    0    0    0    0
20   0    0    0    0    0    0    0    0    0    0    0
Show in New Window
   X1 X2 X6 X11 X16 X20 X23 X24 X25 X28 X31 X34 X36      X38 X41       X54 X55 X56 X66 X68 X70 X72 X76 X79 X82
1   0  0  0   0   1   0   0   0   0   0   0   0   0 0.000000   0  0.000000   0   0   0   0   0   0   0   0   0
2   0  0  0   0   0   0   0   0   0   0   0   0   0 0.000000   0  0.000000   0   0   0   0   0   0   0   0   0
6   0  0  0   0   0   0   3   0   0   0   0   0   0 4.472136   0 22.583180   0   0   0   0   0   0   0   0   0
11  0  0  0   0   0   0   0   0   0   0   0   0   0 0.000000   0  0.000000   0   0   0   0   0   0   0   0   0
16  1  0  0   0   0   0   1   0   0   0   0   0   0 0.000000   0  1.414214   0   0   0   0   0   0   0   0   0
20  0  0  0   0   0   0   0   0   0   0   0   0   0 0.000000   0  0.000000   0   0   0   0   0   0   0   0   0
   X86 X87 X93      X98 X112     X115 X129 X133 X136     X139     X140 X143    X144     X150
1    0   0   0  0.00000    0 0.000000    0    0    0 0.000000  0.00000    0 0.00000 0.000000
2    0   0   0  0.00000    0 0.000000    0    0    0 0.000000  0.00000    0 0.00000 0.000000
6    0   0   0 20.12461    0 5.744563    0    0    0 3.741657 15.87451    0 2.44949 1.414214
11   0   0   0  0.00000    0 0.000000    0    0    0 0.000000  0.00000    0 0.00000 0.000000
16   0   0   0  0.00000    0 1.732051    0    0    0 0.000000  0.00000    1 0.00000 0.000000
20   0   0   0  0.00000    0 0.000000    0    0    0 0.000000  0.00000    0 0.00000 0.000000
Show in New Window

Show in New Window

Show in New Window
       ID       aggression         mass                    flavour  
 1      : 1   Min.   :0.000   Min.   :0.6972   apple           :11  
 10     : 1   1st Qu.:2.000   1st Qu.:0.9163   chocolate       : 8  
 11     : 1   Median :2.000   Median :1.0628   golden retriever: 7  
 12     : 1   Mean   :2.923   Mean   :1.0673   haggis          : 4  
 13     : 1   3rd Qu.:4.000   3rd Qu.:1.2157   nutty           : 9  
 14     : 1   Max.   :8.000   Max.   :1.5305                        
 (Other):33                                                         
Show in New Window

Show in New Window

Show in New Window

Show in New Window

Show in New Window
[1] 0.4385965
Show in New Window
[1] 0.6275304
Show in New Window

Show in New Window
      rho 
0.6781513 
Show in New Window

Show in New Window
      rho 
0.7016897 
Show in New Window

	Pearson's product-moment correlation

data:  mmnet06.wdeg and traits06$aggression
t = -1.5103, df = 37, p-value = 0.1395
alternative hypothesis: true correlation is not equal to 0
95 percent confidence interval:
 -0.5171702  0.0806766
sample estimates:
       cor 
-0.2409744 

Show in New Window

Show in New Window

Show in New Window

Show in New Window
[1] 0.148
Show in New Window

Mantel statistic based on Pearson's product-moment correlation 

Call:
mantel(xdis = mfnet06sym, ydis = mmnet06sym) 

Mantel statistic r: 0.4605 
      Significance: 0.001 

Upper quantiles of permutations (null model):
   90%    95%  97.5%    99% 
0.0615 0.0905 0.1241 0.1673 
Permutation: free
Number of permutations: 999

Show in New Window

OLS Network Model

Residuals:
          0%          25%          50%          75%         100% 
-2.150542592 -0.054652251 -0.009498181  0.013078853  3.877989719 

Coefficients:
            Estimate     Pr(<=b) Pr(>=b) Pr(>=|b|)
(intercept) -0.031140481 0.202   0.798   0.203    
x1           0.034187454 0.999   0.001   0.001    
x2           0.004515407 0.998   0.002   0.002    
x3           0.070134348 1.000   0.000   0.000    

Residual standard error: 0.4264 on 737 degrees of freedom
Multiple R-squared: 0.3528 	Adjusted R-squared: 0.3502 
F-statistic: 133.9 on 3 and 737 degrees of freedom, p-value:     0 


Test Diagnostics:

	Null Hypothesis: qapspp 
	Replications: 1000 
	Coefficient Distribution Summary:

       (intercept)       x1       x2       x3
Min       -3.41898 -3.12566 -2.68395 -2.69518
1stQ      -1.30476 -0.52709 -0.61962 -0.71797
Median    -0.82381 -0.02479 -0.09201 -0.12128
Mean      -0.83189  0.05571 -0.03394 -0.02901
3rdQ      -0.35738  0.56485  0.51279  0.56754
Max        1.56262  5.05669  3.75805  5.41451

Show in New Window
Call:
ergm(formula = mfnetwork ~ CMP + nonzero + nodecov("mass") + 
    nodecov("aggression") + edgecov(s.mtime06) + edgecov(s.mspace06) + 
    edgecov(s.mmnet06sym), response = "weight", reference = ~Poisson)

Iterations:  5 out of 20 

Monte Carlo MLE Results:
                        Estimate Std. Error MCMC % z value Pr(>|z|)    
CMP                    -0.563458   0.438200      0  -1.286 0.198496    
nonzero                -1.876193   0.553424      0  -3.390 0.000699 ***
nodecov.sum.mass       -0.486202   0.237647      0  -2.046 0.040766 *  
nodecov.sum.aggression -0.008635   0.033400      0  -0.259 0.796008    
edgecov.sum.NULL        0.393370   0.084381      0   4.662  < 1e-04 ***
edgecov.sum.NULL.1      0.296110   0.063733      0   4.646  < 1e-04 ***
edgecov.sum.NULL.2      0.033036   0.041046      0   0.805 0.420913    
---
Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1

     Null Deviance:     0  on 741  degrees of freedom
 Residual Deviance: -1137  on 734  degrees of freedom
 
Note that the null model likelihood and deviance are defined to be 0. This means that all
likelihood-based inference (LRT, Analysis of Deviance, AIC, BIC, etc.) is only valid between models
with the same reference distribution and constraints.

AIC: -1123    BIC: -1091    (Smaller is better.) 
```
* Lighter crickets have fewer fighting interactions
* Aggression has no impact on fighting interactions
* Greater overlap in both time and space increases interactions
* Being connected in sperm competition network does not increase chances of fighting

Is this model any good? Can look at diagnostics this way (*it is long so not showing here*):

```{r, eval = F}
mcmc.diagnostics(ergm_fit)

Sample statistics summary:

Iterations = 16384:4209664
Thinning interval = 1024 
Number of chains = 1 
Sample size per chain = 4096 

1. Empirical mean and standard deviation for each variable,
   plus standard error of the mean:

                          Mean     SD Naive SE Time-series SE
CMP                     1.7685  8.011   0.1252         0.2838
nonzero                 0.8247  6.601   0.1031         0.1156
nodecov.sum.mass        3.5719 21.535   0.3365         0.5751
nodecov.sum.aggression 12.3569 66.286   1.0357         1.6458
edgecov.sum.NULL        1.4923 21.275   0.3324         0.6731
edgecov.sum.NULL        4.8466 36.323   0.5675         1.2570
edgecov.sum.NULL        3.0426 38.209   0.5970         1.4049

2. Quantiles for each variable:

                          2.5%     25%    50%    75%  97.5%
CMP                     -12.62  -3.677  1.273  6.874  19.03
nonzero                 -12.00  -4.000  1.000  5.000  14.00
nodecov.sum.mass        -37.70 -10.919  3.332 17.069  47.17
nodecov.sum.aggression -113.00 -32.000 11.000 55.000 147.00
edgecov.sum.NULL        -38.45 -13.419  1.148 15.818  42.95
edgecov.sum.NULL        -65.85 -19.780  4.636 28.777  77.56
edgecov.sum.NULL        -71.88 -23.130  3.270 29.464  77.85


Sample statistics cross-correlations:
                             CMP    nonzero nodecov.sum.mass nodecov.sum.aggression edgecov.sum.NULL
CMP                    1.0000000 0.18052002        0.6914507              0.5669601        0.7949111
nonzero                0.1805200 1.00000000        0.8053790              0.7325866        0.3086455
nodecov.sum.mass       0.6914507 0.80537898        1.0000000              0.8539615        0.6958029
nodecov.sum.aggression 0.5669601 0.73258660        0.8539615              1.0000000        0.5797049
edgecov.sum.NULL       0.7949111 0.30864546        0.6958029              0.5797049        1.0000000
edgecov.sum.NULL       0.8801748 0.17569965        0.6186990              0.5218232        0.7439867
edgecov.sum.NULL       0.6472690 0.09248389        0.4394661              0.1925828        0.5216537
                       edgecov.sum.NULL edgecov.sum.NULL
CMP                           0.8801748       0.64726896
nonzero                       0.1756996       0.09248389
nodecov.sum.mass              0.6186990       0.43946608
nodecov.sum.aggression        0.5218232       0.19258283
edgecov.sum.NULL              0.7439867       0.52165365
edgecov.sum.NULL              1.0000000       0.68592100
edgecov.sum.NULL              0.6859210       1.00000000

Sample statistics auto-correlation:
Chain 1 
               CMP      nonzero nodecov.sum.mass nodecov.sum.aggression edgecov.sum.NULL edgecov.sum.NULL
Lag 0    1.0000000  1.000000000       1.00000000             1.00000000        1.0000000        1.0000000
Lag 1024 0.6355356  0.027932884       0.26564403             0.25044637        0.4600947        0.6275570
Lag 2048 0.4441853  0.016078299       0.17515106             0.18207354        0.3025933        0.4286924
Lag 3072 0.3075570  0.032160207       0.12121835             0.11712943        0.1954417        0.2984556
Lag 4096 0.2296115 -0.001314992       0.08005740             0.08544095        0.1452503        0.2127052
Lag 5120 0.1589990  0.016506521       0.07687535             0.06000333        0.0800274        0.1470940
         edgecov.sum.NULL
Lag 0           1.0000000
Lag 1024        0.6284170
Lag 2048        0.4372426
Lag 3072        0.3086709
Lag 4096        0.2350929
Lag 5120        0.1666200

Sample statistics burn-in diagnostic (Geweke):
Chain 1 

Fraction in 1st window = 0.1
Fraction in 2nd window = 0.5 

                   CMP                nonzero       nodecov.sum.mass nodecov.sum.aggression 
                1.9169                -0.5273                 1.3309                 1.9795 
      edgecov.sum.NULL       edgecov.sum.NULL       edgecov.sum.NULL 
                1.0022                 2.4016                 1.8212 

Individual P-values (lower = worse):
                   CMP                nonzero       nodecov.sum.mass nodecov.sum.aggression 
            0.05525425             0.59801798             0.18322055             0.04776171 
      edgecov.sum.NULL       edgecov.sum.NULL       edgecov.sum.NULL 
            0.31623130             0.01632179             0.06858060 
Joint P-value (lower = worse):  0.074687 .

MCMC diagnostics shown here are from the last round of simulation, prior to computation of final parameter estimates. Because the final estimates are refinements of those used for this simulation run, these diagnostics may understate model performance. To directly assess the performance of the final model on in-model statistics, please use the GOF command: gof(ergmFitObject, GOF=~model).
```
![image](https://user-images.githubusercontent.com/56924820/114068279-a8888380-9895-11eb-927e-1a5b6362892f.png)
![image](https://user-images.githubusercontent.com/56924820/114068291-ab837400-9895-11eb-925a-217ab7117865.png)
![image](https://user-images.githubusercontent.com/56924820/114068298-ade5ce00-9895-11eb-92b7-8377da9b98c6.png)

None of the individual p-values for the Geweke convergence diagnostic test are significant (good). Honestly, not sure how worried to be about the fact the joint p-value is significant or the auto-correlation scores given the model says it converged.

## Multilayer networks

Really cool. Check out:

> Silk et al. 2018: https://www.cell.com/trends/ecology-evolution/fulltext/S0169-5347(18)30062-4
> Finn et al. 2019: https://www.sciencedirect.com/science/article/pii/S0003347218304020
> Finn 2021: https://academic.oup.com/cz/article/67/1/81/6081014
> Hasenjager, Silk, Fisher 2021: https://academic.oup.com/cz/article/67/1/45/6124358
END

***
