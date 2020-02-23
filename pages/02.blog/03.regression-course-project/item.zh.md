---
title: 数据科学回归分析课程Project
published: true
date: '08/13/2019 05:08 am'
metadata:
    Keywords: 'R,Rstudio,Coursera,Statistics,Regression,MotorTrend,MPG,miles per gallon,MTCARS,H0 Model,JHU,DataScience'
    关键字: 'R语言,回归分析,汽车数据数据'
    描述: Coursera约翰霍普金斯大学数据科学回归分析课程
taxonomy:
    category:
        - blog
    tag:
        - R
        - Rstudio
        - Coursera
        - Statistics
        - Regression
        - MotorTrend
        - MPG
        - 'miles per gallon'
        - MTCARS
        - 'H0 Model'
        - JHU
        - DataScience
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
googledesc: "This Study is about Regression Models Course Project.We use data of Motor Trend which is they are interested in exploring the relationship between a set of variables and miles per gallon (MPG) (outcome). Here's the missions:\r\n\"Is an automatic or manual transmission better for MPG\"\r\n\"Quantify the MPG difference between automatic and manual transmissions\""
twittercardoptions: summary
articleenabled: false
musiceventenabled: false
orgaenabled: false
orga:
    ratingValue: 2.5
orgaratingenabled: false
eventenabled: false
personenabled: false
restaurantenabled: false
restaurant:
    priceRange: $
header_image: '0'
header_image_width: 50
header_image_height: 50
---

### Course7project : Is Manual Transmission more Fuel-efficient than Automatic Transmission?

Autoz  
2017.12.20  

### Synopsis

This Study is about Regression Models Course Project.We use data of Motor Trend which is they are interested in exploring the relationship between a set of variables and miles per gallon (MPG) (outcome). Here's the missions:

#### * "Is an automatic or manual transmission better for MPG"
#### * "Quantify the MPG difference between automatic and manual transmissions"

About Data: mtcars which shows the relationships between automatic and manual by different transmission.

#### * mpg    Miles/(US) gallon
#### * cyl    Number of cylinders
#### * disp   Displacement (cu.in.)
#### * hp     Gross horsepower
#### * drat   Rear axle ratio
#### * wt     Weight (1000 lbs)
#### * qsec   1/4 mile time
#### * vs     V/S
#### * am     Transmission (0 = automatic, 1 = manual)
#### * gear   Number of forward gears
#### * carb   Number of carburetors

# Reading MTCARS Data 

Prepare Environment.
Look at the Data Structure.

```
library(ggplot2)
library(dplyr)
data(mtcars)
head(mtcars,3)
```

```
##                mpg cyl disp  hp drat    wt  qsec vs am gear carb
## Mazda RX4     21.0   6  160 110 3.90 2.620 16.46  0  1    4    4
## Mazda RX4 Wag 21.0   6  160 110 3.90 2.875 17.02  0  1    4    4
## Datsun 710    22.8   4  108  93 3.85 2.320 18.61  1  1    4    1
```

### Cleaning The Training Data

At first, we need to change numerics into factors in order to fit the model correctly.


```
mtcars$am <- factor(mtcars$am,labels = c("Automatic", "Manual"))
mtcars$gear = factor(mtcars$gear)
mtcars$cyl  = factor(mtcars$cyl)
mtcars$carb = factor(mtcars$carb)
mtcars$vs   = factor(mtcars$vs)
```

### Exploratory Data Analysis

As quiz 1 said "Is an automatic or manual transmission better for MPG"
.So first look at the assuming relationship between miles per gallon and trasmissons types : mpg ~ am. 

![](course7project_files/figure-html/unnamed-chunk-4-1.png?lightbox=600,400&resize=200,200)<!-- -->

As we can see maual cars will contribute more miles per gallon than automatic cars.

### Learning H0 Model

Assuming relationship "manal is better" is null hypothesis


```
fit_H0 <- lm(mpg ~ am, data = mtcars)
summary(fit_H0)
```

```
## 
## Call:
## lm(formula = mpg ~ am, data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -9.3923 -3.0923 -0.2974  3.2439  9.5077 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   17.147      1.125  15.247 1.13e-15 ***
## amManual       7.245      1.764   4.106 0.000285 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 4.902 on 30 degrees of freedom
## Multiple R-squared:  0.3598, Adjusted R-squared:  0.3385 
## F-statistic: 16.86 on 1 and 30 DF,  p-value: 0.000285
```

As we can see above , H0 model's Pr(>|t|) is very significant. But the R-squared is just 0.3598, not enough to interpret all the variances informations and only interpret 36% information.

### Reselected Model

we use stepwise to choose our new fit model.


```
summary(step(lm(data = mtcars, mpg ~ .),trace = 0,direction="both"))
```

```
## 
## Call:
## lm(formula = mpg ~ cyl + hp + wt + am, data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3.9387 -1.2560 -0.4013  1.1253  5.0513 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept) 33.70832    2.60489  12.940 7.73e-13 ***
## cyl6        -3.03134    1.40728  -2.154  0.04068 *  
## cyl8        -2.16368    2.28425  -0.947  0.35225    
## hp          -0.03211    0.01369  -2.345  0.02693 *  
## wt          -2.49683    0.88559  -2.819  0.00908 ** 
## amManual     1.80921    1.39630   1.296  0.20646    
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.41 on 26 degrees of freedom
## Multiple R-squared:  0.8659, Adjusted R-squared:  0.8401 
## F-statistic: 33.57 on 5 and 26 DF,  p-value: 1.506e-10
```

Now we can see the R-squared is 0.8659. It interpreted almost 87% variances. So we temporarily choose this model for our analysis.

We can get new fit model : **mpg ~ cyl + hp + wt + am**. Let's re-order those variables in formula. 

### Model Evaluation

Let's valid the chosen model. This model is nested model. We can use function anova to analyse all of them


```
fit0 <- lm(data = mtcars, mpg ~ am)
fit1 <- lm(data = mtcars, mpg ~ am + hp)
fit2 <- lm(data = mtcars, mpg ~ am + hp + wt)
fit3 <- lm(data = mtcars, mpg ~ am + hp + wt + cyl)
anova(fit0,fit1,fit2,fit3)
```

```
## Analysis of Variance Table
## 
## Model 1: mpg ~ am
## Model 2: mpg ~ am + hp
## Model 3: mpg ~ am + hp + wt
## Model 4: mpg ~ am + hp + wt + cyl
##   Res.Df    RSS Df Sum of Sq       F    Pr(>F)    
## 1     30 720.90                                   
## 2     29 245.44  1    475.46 81.8529 1.634e-09 ***
## 3     28 180.29  1     65.15 11.2157  0.002484 ** 
## 4     26 151.03  2     29.27  2.5191  0.099998 .  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

We can see those models `RSS` is from 721 to 151. But we just only interesting in Model 1 and Model 4. Now we focus the last Model 4

### Another Model

As we can see, we disregard variable cylinder `cyl` and see what happened.

```
summary(step(lm(data = mtcars, mpg ~ . - cyl),trace = 0,direction="both"))
```

```
## 
## Call:
## lm(formula = mpg ~ wt + qsec + am, data = mtcars)
## 
## Residuals:
##     Min      1Q  Median      3Q     Max 
## -3.4811 -1.5555 -0.7257  1.4110  4.6610 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)   9.6178     6.9596   1.382 0.177915    
## wt           -3.9165     0.7112  -5.507 6.95e-06 ***
## qsec          1.2259     0.2887   4.247 0.000216 ***
## amManual      2.9358     1.4109   2.081 0.046716 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 2.459 on 28 degrees of freedom
## Multiple R-squared:  0.8497, Adjusted R-squared:  0.8336 
## F-statistic: 52.75 on 3 and 28 DF,  p-value: 1.21e-11
```

As you can see R-squared is 0.8497. Maybe **mpg ~ wt + qsec + am** included `qsec` is a good model, but wait.

The `am` variables seems merely significant, that means we are not sure whether the model is good enough. 

Now We can compare fit0 and fit4.


```
fit4 <- lm(data = mtcars, mpg ~ wt + qsec + am)
anova(fit0,fit4)
```

```
## Analysis of Variance Table
## 
## Model 1: mpg ~ am
## Model 2: mpg ~ wt + qsec + am
##   Res.Df    RSS Df Sum of Sq      F   Pr(>F)    
## 1     30 720.90                                 
## 2     28 169.29  2    551.61 45.618 1.55e-09 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
```

As comparing RSS and Pr(>F) of fit3 and fit4, finally we choose fit3.

### Final Selected Model 

```
par(mfrow = c(2,2))
plot(fit3)
```

![](course7project_files/figure-html/unnamed-chunk-10-1.png?lightbox=600,400&resize=200,200)<!-- -->

As we can see , residuals center , normal QQ  fit normal
So the final model's coeficient is:

```
fitfinal <- lm(data = mtcars, mpg ~ am + hp + wt + cyl - 1)
summary(fitfinal)$coef
```

```
##                Estimate Std. Error   t value     Pr(>|t|)
## amAutomatic 33.70832390 2.60488618 12.940421 7.733392e-13
## amManual    35.51753528 2.03171034 17.481594 6.811749e-16
## hp          -0.03210943 0.01369257 -2.345025 2.693461e-02
## wt          -2.49682942 0.88558779 -2.819404 9.081408e-03
## cyl6        -3.03134449 1.40728351 -2.154040 4.068272e-02
## cyl8        -2.16367532 2.28425172 -0.947214 3.522509e-01
```

### Conclusion

As other factors are constants. Every automatic car will increase `33.8` miles per gallon while manual car increase `35.5` miles per gallon. No matter what, `manual can drive more mile than automatic when amount of fuel is the same`.

### Appendix

Plots about the relationships of variables in `Final Model`. To answer the Quiz 2 "Quantify the MPG difference between automatic and manual transmissions"

* MPG vs. Weight by Transmissions Types

![](course7project_files/figure-html/unnamed-chunk-12-1.png?lightbox=600,400&resize=200,200)<!-- -->

* MPG vs. Horsepower by Transmissions Types

![](course7project_files/figure-html/unnamed-chunk-13-1.png?lightbox=600,400&resize=200,200)<!-- -->

* MPG vs. 1/4 Miles Time by Transmissions Types

![](course7project_files/figure-html/unnamed-chunk-14-1.png?lightbox=600,400&resize=200,200)<!-- -->

* MPG vs. All Selected Factors by Transmissions Types

![](course7project_files/figure-html/unnamed-chunk-15-1.png?lightbox=600,400&resize=200,200)<!-- -->

* So you can see it is very clear for answer the questions.

### THE END