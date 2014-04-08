Lab 5 (3/13)
========================================================

# Contrasts?

- This is an ANOVA thing. 
- Polynomial equation whose coefficents sum to 0.
- in ANOVA: a linear combination of factor level means whose coefficients sum to 0. "A weighted sum of the treatment means."

## Simple Example

- 2 means: $\mu_1, \mu_2$
- k levels: k-1 comparisons possible. 

## Orthogonal Contrasts

- if there are more than 2 comparisons, the pairwise product sum should be all 0. 

### Example.

- Treatments: T1, T2, T3 (control)
- Means: $\mu_1, \mu_2, \mu_3$
- ANOVA H0: all mus are same
- # of independent comparisons? df = 2
- Contrast hypotheses
  - Treatments different from control?
  - Treatments are different from each other?
- $L_a = 1\mu_1 + 0\mu_2 - 1\mu_3 = 0$
- $L_b = 0\mu_1 + 1\mu_2 - 1\mu_3 = 0$
- This is not orthogonal

## Regression Contrasts

- "Sum to Zero": ANOVA model in Regression form
- Criterion Y = $\beta_0 + \beta_1X_1 + \beta_2X_2 + \epsilon$
- $X_j$ = 1 or 0
- $\beta_0 = \mu_1 + \mu_2 + \mu_3/3$
- $\beta_1 = \mu_1 - (\mu_1 + \mu_2 + \mu_3)/3$
- $\beta_2 = \mu_2 - (\mu_1 + \mu_2 + \mu_3)/3$
- 1 fewer number of beta than listed


```r
y <- c(4, 5, 4, 4, 5, 4, 3, 4, 4, 3, 3, 4, 4, 4, 3)
group <- rep(letters[1:3], each = 5)
dta <- data.frame(y = y, X = factor(group))
head(dta)
```

```
##   y X
## 1 4 a
## 2 5 a
## 3 4 a
## 4 4 a
## 5 5 a
## 6 4 b
```

```r

options(contrasts = c("contr.sum", "contr.poly"))
contrasts(dta$X)
```

```
##   [,1] [,2]
## a    1    0
## b    0    1
## c   -1   -1
```

```r
model.matrix(y ~ X, dta)
```

```
##    (Intercept) X1 X2
## 1            1  1  0
## 2            1  1  0
## 3            1  1  0
## 4            1  1  0
## 5            1  1  0
## 6            1  0  1
## 7            1  0  1
## 8            1  0  1
## 9            1  0  1
## 10           1  0  1
## 11           1 -1 -1
## 12           1 -1 -1
## 13           1 -1 -1
## 14           1 -1 -1
## 15           1 -1 -1
## attr(,"assign")
## [1] 0 1 1
## attr(,"contrasts")
## attr(,"contrasts")$X
## [1] "contr.sum"
```

```r

contrast.joe <- matrix(NA, 3, 3)
contrast.joe[1, ] <- 1/3  #intercept
contrast.joe[2, ] <- c(-1, 0, 1)
contrast.joe[3, ] <- c(0, -1, 1)
contrast.joe
```

```
##         [,1]    [,2]   [,3]
## [1,]  0.3333  0.3333 0.3333
## [2,] -1.0000  0.0000 1.0000
## [3,]  0.0000 -1.0000 1.0000
```

```r

contrasts(dta$X) <- solve(contrast.joe)[, -1]
solve(contrast.joe)
```

```
##      [,1]    [,2]    [,3]
## [1,]    1 -0.6667  0.3333
## [2,]    1  0.3333 -0.6667
## [3,]    1  0.3333  0.3333
```

```r

fit2 <- lm(y ~ X, dta)
mus <- aggregate(y ~ X, dta, mean)[, 2]
L.a <- contrast.joe

SSL <- sum(L.a * mus)^2/(sum(L.a^2)/5)
SSL/anova(fit2)["Residuals", "Mean Sq"]
```

```
## [1] 74.46
```

```r
coef(summary(fit2))[, "t value"]^2
```

```
## (Intercept)          X1          X2 
##     747.556       5.333       0.000
```


### Belows are the only thing that we need for HW4


```r
contrast.lee <- matrix(NA, 3, 3)
rownames(contrast.lee) <- c("GM", ":lv2", ":lv3")
contrast.lee[1, ] <- 1/3
contrast.lee[2, ] <- c(-1, 1, 0)
contrast.lee[3, ] <- c(-1, 0, 1)
contrasts(dta$X) <- solve(contrast.lee)[, -1]
contrast.lee
```

```
##         [,1]   [,2]   [,3]
## GM    0.3333 0.3333 0.3333
## :lv2 -1.0000 1.0000 0.0000
## :lv3 -1.0000 0.0000 1.0000
```

```r

solve(contrast.lee)[, -1]
```

```
##         :lv2    :lv3
## [1,] -0.3333 -0.3333
## [2,]  0.6667 -0.3333
## [3,] -0.3333  0.6667
```


### fit the model

```r
coef(summary(lm(y ~ X, dta)))
```

```
##             Estimate Std. Error t value  Pr(>|t|)
## (Intercept)    3.867     0.1414  27.341 3.532e-12
## X:lv2         -0.800     0.3464  -2.309 3.952e-02
## X:lv3         -0.800     0.3464  -2.309 3.952e-02
```


