Lab 4
========================================================

# ANOVA, t-test, and Regression (Overview)

- An example of MOM_Highschool vs. Child's IQ
- R-squared: % of variance
- t-test? two groups are not overlapping -- it's significant!
- Regression? drawing a line.
- Interactions between categorical (mom high) vs. continuous (mom IQ)
  - One more term is added: mom_hs X mom_iq  
  - All you can say with this interaction term is that two slopes are different.
  

- Assumptions and diagnostics (in order of importance)
  - Validity
  - Additivity and Linearity
  - Independence of errors
  - Equal variance or errors
  - Normality of errors

# ANOVA Stuff II

## Fixed versus Random
- $Y_{ij}$: Weight for every chicken.
- $\mu$: population mean
- $Y_{ij} \sim N(\mu, \sigma^2)$: the model is an interpretation of this distribution.
- Models are same.
- Difference?
  - Research question: Does this matter? vs. What affects the result?
  - What we are doing about error term.
  - Variable's are random variable vs. fixed variable


```r
mydat <- ChickWeight
head(mydat)
```

```
##   weight Time Chick Diet
## 1     42    0     1    1
## 2     51    2     1    1
## 3     59    4     1    1
## 4     64    6     1    1
## 5     76    8     1    1
## 6     93   10     1    1
```

```r
fit1 <- weight ~ Diet
summary(aov(fit1, mydat))
```

```
##              Df  Sum Sq Mean Sq F value  Pr(>F)    
## Diet          3  155863   51954    10.8 6.4e-07 ***
## Residuals   574 2758693    4806                    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```


- aov fundtion is another way of doing ANOVA
- 1 - (F value, df_between, df_within) 

## Random?

```r
fit2 <- weight ~ Error(Diet)
summary(aov(fit2, mydat))
```

```
## 
## Error: Diet
##           Df Sum Sq Mean Sq F value Pr(>F)
## Residuals  3 155863   51954               
## 
## Error: Within
##            Df  Sum Sq Mean Sq F value Pr(>F)
## Residuals 574 2758693    4806
```



- 1 is the grand mean (just put there)

```r
fit3 <- weight ~ Diet + Error(1/Diet)
summary(aov(fit3, mydat))
```

```
## 
## Error: Within
##            Df  Sum Sq Mean Sq F value  Pr(>F)    
## Diet        3  155863   51954    10.8 6.4e-07 ***
## Residuals 574 2758693    4806                    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```


- Not yet learned: To show Why Random model matters


```r
fit4 <- weight ~ Time + Error(Chick/Time)
summary(aov(fit4, mydat))
```

```
## 
## Error: Chick
##           Df Sum Sq Mean Sq F value Pr(>F)   
## Time       1  89700   89700    9.78  0.003 **
## Residuals 48 440405    9175                  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Error: Chick:Time
##           Df  Sum Sq Mean Sq F value Pr(>F)    
## Time       1 1962914 1962914     280 <2e-16 ***
## Residuals 49  343364    7007                   
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Error: Within
##            Df Sum Sq Mean Sq F value Pr(>F)
## Residuals 478  78173     164
```



# Scheffe's Test

```r
library(agricolae)
mc.scheffe <- scheffe.test(aov(fit1, mydat), "Diet", group = FALSE, console = TRUE)
```

```
## 
## Study: aov(fit1, mydat) ~ "Diet"
## 
## Scheffe Test for weight 
## 
## Mean Square Error  : 4806 
## 
## Diet,  means
## 
##   weight   std   r Min Max
## 1  102.6 56.66 220  35 305
## 2  122.6 71.61 120  39 331
## 3  142.9 86.54 120  39 373
## 4  135.3 68.83 118  39 322
## 
## alpha: 0.05 ; Df Error: 574 
## Critical Value of F: 2.62 
## 
## Harmonic Mean of Cell Sizes  134.7
## Comparison between treatments means
## 
##       Difference   pvalue sig    LCL     UCL
## 1 - 2    -19.971 0.093138   . -42.79   2.846
## 1 - 3    -40.305 0.000011 *** -63.12 -17.487
## 1 - 4    -32.617 0.000788 *** -55.56  -9.675
## 2 - 3    -20.333 0.161627     -46.29   5.624
## 2 - 4    -12.646 0.576978     -38.71  13.421
## 3 - 4      7.687 0.865720     -18.38  33.754
```

```r
mc.scheffe$comparison
```

```
##       Difference   pvalue sig    LCL     UCL
## 1 - 2    -19.971 0.093138   . -42.79   2.846
## 1 - 3    -40.305 0.000011 *** -63.12 -17.487
## 1 - 4    -32.617 0.000788 *** -55.56  -9.675
## 2 - 3    -20.333 0.161627     -46.29   5.624
## 2 - 4    -12.646 0.576978     -38.71  13.421
## 3 - 4      7.687 0.865720     -18.38  33.754
```


- So far, all the things are pairwise (Bonferoni?)
- Scheffe does multiple comparisons (between groups)
- More conservative than Bonferoni


```r
mc.bonf <- with(mydat, pairwise.t.test(weight, Diet, p.adj = "bonf"))
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


