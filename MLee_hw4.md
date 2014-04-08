Homework 4 (3/27)
========================================================

Myeong Lee
--------------------------------------------------------


```r
options(contrasts = c("contr.sum", "contr.poly"))
data1 <- read.csv("C:/Users/myeong/Desktop/hw4.csv")

my.dat <- data1[, c("y", "Group")]
my.dat$Group <- factor(my.dat$Group, levels = c(1, 2, 3, 4))
my.dat
```

```
##    y Group
## 1  1     1
## 2  2     1
## 3  3     1
## 4  2     1
## 5  3     2
## 6  2     2
## 7  3     2
## 8  4     2
## 9  4     3
## 10 3     3
## 11 5     3
## 12 4     3
## 13 4     4
## 14 3     4
## 15 2     4
## 16 3     4
```


# Part I

### 1. Model
- Y = $\beta_0 + \beta_1X_1 + \beta_2X_2 + \beta_3X_3 + \epsilon$

### 2. H0

- $H_0: \beta_1 = \beta_2 = \beta_3 = 0$ or $\mu_1 = \mu_2 = \mu_3 = \mu_4$

- There is no systematic difference among the group means. All the groups have same means. 

### 3. H1

- $H_1: \beta_j \neq 0$, $Not H_0$
  
- There are systematic differences among group means. 

### 4. Error Rate $\alpha$

- at $\alpha = 0.05$, critical t(2)=4.30
- actual $\alpha$ = 1 - $0.95^3$ = .142625
- $\frac{.142625}{4}$ = .0356 < .0.05
- So, alpha can be set as $\alpha = P(P|H_0) = 0.05$ 
  
### 5. Regression form

```r
fit1 <- lm(y ~ Group, my.dat)
coef(summary(fit1))
```

```
##               Estimate Std. Error    t value  Pr(>|t|)
## (Intercept)  3.000e+00     0.2041  1.470e+01 4.907e-09
## Group1      -1.000e+00     0.3536 -2.828e+00 1.522e-02
## Group2      -4.532e-17     0.3536 -1.282e-16 1.000e+00
## Group3       1.000e+00     0.3536  2.828e+00 1.522e-02
```


### 6. One Predictor

```r
fit0 <- lm(y ~ 1, my.dat)
coef(summary(fit0))
```

```
##             Estimate Std. Error t value Pr(>|t|)
## (Intercept)        3     0.2582   11.62 6.71e-09
```

```r

anova(fit0, fit1)
```

```
## Analysis of Variance Table
## 
## Model 1: y ~ 1
## Model 2: y ~ Group
##   Res.Df RSS Df Sum of Sq  F Pr(>F)  
## 1     15  16                         
## 2     12   8  3         8  4  0.035 *
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

- The model that includes the categorical is better, since P-value shows that it rejects the null hypothesis in the significance level 0.05.

### 7. Plot

```r
my.dat$e <- residuals(fit1)
my.dat$y.hat <- fitted(fit1)
frame1 <- data.frame(y = my.dat$y, y_hat = my.dat$y.hat, e = my.dat$e, X = my.dat$Group)

library(GGally)
```

```
## Loading required package: ggplot2
## Loading required package: reshape
## Loading required package: plyr
## 
## Attaching package: 'reshape'
## 
## The following objects are masked from 'package:plyr':
## 
##     rename, round_any
```

```r

ggpairs(frame1, lower = list(continuous = "smooth"))
```

![plot of chunk unnamed-chunk-4](figure/unnamed-chunk-4.png) 


### 8. Data frame

```r
frame1
```

```
##    y y_hat          e X
## 1  1     2 -1.000e+00 1
## 2  2     2 -2.498e-16 1
## 3  3     2  1.000e+00 1
## 4  2     2  1.943e-16 1
## 5  3     3  1.110e-16 2
## 6  2     3 -1.000e+00 2
## 7  3     3  1.110e-16 2
## 8  4     3  1.000e+00 2
## 9  4     4 -2.776e-17 3
## 10 3     4 -1.000e+00 3
## 11 5     4  1.000e+00 3
## 12 4     4 -2.776e-17 3
## 13 4     3  1.000e+00 4
## 14 3     3 -1.110e-16 4
## 15 2     3 -1.000e+00 4
## 16 3     3 -1.110e-16 4
```


### 9. Correlation

```r
library(xtable)
frame2 <- data.frame(y = my.dat$y, y_hat = my.dat$y.hat, e = my.dat$e)
my.cor <- cor(frame2)
print(xtable(my.cor), type = "html")
```

<!-- html table generated in R 3.0.2 by xtable 1.7-1 package -->
<!-- Sat Apr 05 15:56:56 2014 -->
<TABLE border=1>
<TR> <TH>  </TH> <TH> y </TH> <TH> y_hat </TH> <TH> e </TH>  </TR>
  <TR> <TD align="right"> y </TD> <TD align="right"> 1.00 </TD> <TD align="right"> 0.71 </TD> <TD align="right"> 0.71 </TD> </TR>
  <TR> <TD align="right"> y_hat </TD> <TD align="right"> 0.71 </TD> <TD align="right"> 1.00 </TD> <TD align="right"> -0.00 </TD> </TR>
  <TR> <TD align="right"> e </TD> <TD align="right"> 0.71 </TD> <TD align="right"> -0.00 </TD> <TD align="right"> 1.00 </TD> </TR>
   </TABLE>


### 10, 11. Explanation
- The data are not that systematically differentiated between groups. y and y_hat are correlated, while y and e are also highly correlated: cannot be said that samples are systematically differentiated. 


# Part II

```r
contrasts(my.dat$Group)
```

```
##   [,1] [,2] [,3]
## 1    1    0    0
## 2    0    1    0
## 3    0    0    1
## 4   -1   -1   -1
```

```r
model.matrix(y ~ Group, my.dat)
```

```
##    (Intercept) Group1 Group2 Group3
## 1            1      1      0      0
## 2            1      1      0      0
## 3            1      1      0      0
## 4            1      1      0      0
## 5            1      0      1      0
## 6            1      0      1      0
## 7            1      0      1      0
## 8            1      0      1      0
## 9            1      0      0      1
## 10           1      0      0      1
## 11           1      0      0      1
## 12           1      0      0      1
## 13           1     -1     -1     -1
## 14           1     -1     -1     -1
## 15           1     -1     -1     -1
## 16           1     -1     -1     -1
## attr(,"assign")
## [1] 0 1 1 1
## attr(,"contrasts")
## attr(,"contrasts")$Group
## [1] "contr.sum"
```

```r

contrast.lee <- matrix(NA, 4, 4)
rownames(contrast.lee) <- c("GM", ":12v34", ":2v3", ":1v4")
contrast.lee[1, ] <- 1/4
contrast.lee[2, ] <- c(-1, -1, 1, 1)
contrast.lee[3, ] <- c(0, -1, 1, 1)
contrast.lee[4, ] <- c(-1, 0, 0, 1)
contrasts(my.dat$Group) <- solve(contrast.lee)[, -1]
contrast.lee
```

```
##         [,1]  [,2] [,3] [,4]
## GM      0.25  0.25 0.25 0.25
## :12v34 -1.00 -1.00 1.00 1.00
## :2v3    0.00 -1.00 1.00 1.00
## :1v4   -1.00  0.00 0.00 1.00
```

```r

solve(contrast.lee)
```

```
##      GM :12v34 :2v3 :1v4
## [1,]  0   -1.0    1    0
## [2,]  2    0.5   -1    0
## [3,]  2    1.5   -1   -1
## [4,]  0   -1.0    1    1
```

```r

fit2 <- lm(y ~ Group, my.dat)
mus <- aggregate(y ~ Group, my.dat, mean)[, 2]
L.a <- contrast.lee

SSL <- sum(L.a * mus)^2/(sum(L.a^2)/5)
SSL/anova(fit2)["Residuals", "Mean Sq"]
```

```
## [1] 29.19
```

```r

coef(summary(fit2))[, "t value"]^2
```

```
## (Intercept) Group:12v34   Group:2v3   Group:1v4 
##     216.000       6.000       2.182       3.000
```


- I am not sure how to set the contrast matrix...

# Part III

```r
contrasts(my.dat$Group) <- cbind(`:12v34` = c(-1, -1, 1, 1), `:1v4` = c(-1, 
    0, 0, 1), `:2v3` = c(0, -1, 1, 0))
cbind(`:12v34` = c(-1, -1, 1, 1), `:1v4` = c(-1, 0, 0, 1), `:2v3` = c(0, -1, 
    1, 0))
```

```
##      :12v34 :1v4 :2v3
## [1,]     -1   -1    0
## [2,]     -1    0   -1
## [3,]      1    0    1
## [4,]      1    1    0
```

```r
fit3 <- lm(y ~ Group, my.dat)
mus <- aggregate(y ~ Group, my.dat, mean)[, 2]
L.a <- contrast.lee

SSL <- sum(L.a * mus)^2/(sum(L.a^2)/5)
SSL/anova(fit3)["Residuals", "Mean Sq"]
```

```
## [1] 21.08
```

```r

coef(summary(fit3))[, "t value"]^2
```

```
## (Intercept) Group:12v34   Group:1v4 
##   1.560e+02   2.167e+00   1.335e-32
```


- The result shows that the treatment 1 and 2 are systematically different from the controls (3 and 4).
