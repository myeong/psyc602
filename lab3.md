Lab 3
========================================================



```r
library(reshape2)
my.dat <- ChickWeight[, c("weight", "Diet")]
summary(my.dat)
```

```
##      weight    Diet   
##  Min.   : 35   1:220  
##  1st Qu.: 63   2:120  
##  Median :103   3:120  
##  Mean   :122   4:118  
##  3rd Qu.:164          
##  Max.   :373
```


#Fixed Effect Model (<-> Random Effect Model)

Whether several groups in a population are different or not.

j: each group, 
i: individual output, 
$\alpha_j$: difference of the group from the grand mean,
a: the number of groups,
$\mu_j$: mean of each group

- Greek letters: all about POPULATION
- Null Hypothesis: $\alpha_j = 0$, where $j=1,2,...$
- Error: N(0, $\sigma^2$)
- Regression Model

#ANOVA

- F = $\frac{MS_{between}}{MS_{within}}$
- $SS_B$: $\sum (n_j(Y_{.j} - Y_{..})^2$


```r
gm <- mean(my.dat$weight)
a <- aggregate(weight ~ Diet, dat = my.dat, mean)
names(a) <- c("Diet", "mean.j")
a$hat <- a$mean.j - gm
a$n <- table(my.dat$Diet)
SSB <- sum(a$hat^2 * a$n)
```


Their weights and their groups mean

- $SS_W = \sum\sum(Y_ij - Y_{.j})^2$ 


```r
SSW <- sum(apply(my.dat, 1, function(x) (a[a$Diet == x["Diet"], ]$mean.j - as.numeric(x["weight"]))^2))
```


- $df_B = a - 1$
- $df_W = N - a$


```r
df.B <- length(levels(my.dat$Diet)) - 1
df.W <- nrow(my.dat) - df.B - 1
```


- levels: how many different variables in the parameter 


- Mean Squares

```r
MSB <- SSB/df.B
MSW <- SSW/df.W
F.stat <- MSB/MSW
```


- F value assumes F distribution: has 2 parameteres ($df_B$, $df_W$)
- p value? Probability $P(T>t | H_0)$


```r
p.level = 1 - pf(F.stat, df.B, df.W)
p.level
```

```
## [1] 6.433e-07
```


- c function: combine
##### Compare the result!

```r
c(F = F.stat, p = p.level)
```

```
##         F         p 
## 1.081e+01 6.433e-07
```

```r

fit1 <- lm(weight ~ Diet, my.dat)
anova(fit1)
```

```
## Analysis of Variance Table
## 
## Response: weight
##            Df  Sum Sq Mean Sq F value  Pr(>F)    
## Diet        3  155863   51954    10.8 6.4e-07 ***
## Residuals 574 2758693    4806                    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```



####Cumulative Probability Function $\Phi$

- $\Phi(0) = 0.5$
- if we do pf(F, df, df), it shows cdf, so we should substract it from 1.

#Bonferroni Correction

- $P(T\geq t_i|H_0) \leq \frac{\alpha}{n}, i=1 ... n$
- $P(T\geq t_i|H_0)n \leq \alpha, i=1 ... n$
- Multiply p value by n and compare it with $\alpha$. 

#### Uncorrected pairwise comparisons

```r
mc.none <- with(my.dat, pairwise.t.test(weight, Diet, p.adj = "none"))
mc.none
```

```
## 
## 	Pairwise comparisons using t tests with pooled SD 
## 
## data:  weight and Diet 
## 
##   1       2     3    
## 2 0.011   -     -    
## 3 4.1e-07 0.023 -    
## 4 4.3e-05 0.160 0.393
## 
## P value adjustment method: none
```


- Banferroni adjustment

```r
mc.bonf <- with(my.dat, pairwise.t.test(weight, Diet, p.adj = "bonf"))
mc.bonf
```

```
## 
## 	Pairwise comparisons using t tests with pooled SD 
## 
## data:  weight and Diet 
## 
##   1       2       3      
## 2 0.06838 -       -      
## 3 2.5e-06 0.14077 -      
## 4 0.00026 0.95977 1.00000
## 
## P value adjustment method: bonferroni
```


- 1,3 and 1,4 are significant
- Number of comparisons = k(k-1)/2

##### Compare the result with the actual producted value

```r
k <- length(levels(my.dat$Diet))
n.c <- k * (k - 1)/2
n.c
```

```
## [1] 6
```

```r
mc.none$p.value <- mc.none$p.value * n.c
mc.none
```

```
## 
## 	Pairwise comparisons using t tests with pooled SD 
## 
## data:  weight and Diet 
## 
##   1       2       3      
## 2 0.06838 -       -      
## 3 2.5e-06 0.14077 -      
## 4 0.00026 0.95977 2.35644
## 
## P value adjustment method: none
```


