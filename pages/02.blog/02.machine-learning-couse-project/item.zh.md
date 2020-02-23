---
title: 数据科学机器学习课程Project
published: true
date: '01/16/2019 11:38 am'
metadata:
    Keywords: 'DataScience,R,数据科学,机器学习,ML,统计学习'
    描述: Coursera约翰霍普金斯大学数据科学机器学习课程
taxonomy:
    category:
        - blog
    tag:
        - Rstudio
        - R
        - Coursera
        - Statistics
        - Regression
        - Model
        - 'Machine Learning'
        - Predict
sitemap:
    changefreq: weekly
    priority: 0.7
external_links:
    process: true
    no_follow: true
image:
    summary:
        enabled: '1'
    text:
        enabled: '1'
summary:
    enabled: '1'
googledesc: 'One thing that people regularly do is quantify how much of a particular activity they do,but they rarely quantify how well they do it. In this project, your goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. With Following Steps:'
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
header_image_width: 50
header_image_height: 50
---

### Synopsis

One thing that people regularly do is quantify how much of a particular activity they do,but they rarely quantify how well they do it. In this project, your goal will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants. With Following Steps:

* 1 fit model
* 2 cross validation
* 3 sample errors
* 4 make choices
* 5 predict 20 test case

### Read PML Data



```r
library(tidyverse)
training_raw <- read_csv("pml-training.csv",na = c("NA","#DIV/0!",""))
testing_raw <- read_csv("pml-testing.csv",na = c("NA","#DIV/0!",""))
training_raw <- as.data.frame(training_raw)
testing_raw <- as.data.frame(testing_raw)
```

## Cleaning the Training Data

### Factorized classes

```r
training_raw$classe <- factor(training_raw$classe)
```

### Remove columns with more than 70% of NA or "" values

```r
NArate <- function(x){
    apply(x, 2, function(y) sum(is.na(y)))/nrow(x)
}
training_raw <- training_raw[,NArate(training_raw) < .7]
```


### Remove near zero variances


```r
library(caret)
nzvCol <- nearZeroVar(training_raw, saveMetrics = TRUE)
training_raw <- training_raw[, !nzvCol$nzv]
```


### Remove unused variables

```r
training_raw <- training_raw[,-grep("name|timestamp|window|^X",names(training_raw))]
```

### Impute all NAs

```r
library(mice)
imp <- mice(training_raw,seed = 3)
training_raw <- mice::complete(imp)
```

### Remove 75% highly Correlative Variables


```r
trainingNC <- training_raw[, -length(names(training_raw))]

findcor <- findCorrelation(cor(trainingNC,use="complete.obs"), cutoff = .75)

trainingFiltered <- training_raw[,-findcor]
trainingFilteredNC <- trainingNC[,-findcor]
```

### Split data into Training and Testing sets


```r
inTrain <- createDataPartition(y = trainingFiltered$classe, p = 0.75, 
                               list = FALSE) # 75% training
training <- trainingFiltered[inTrain,]
testing <- trainingFiltered[-inTrain,]
```

## Model Evaluation

### Decision Tree

Time Used


```r
library(rpart)
system.time(mod_rpart <- rpart(formula = classe ~ ., data = training))
```

```
##    user  system elapsed 
##    1.75    0.00    1.78
```

Training Rpart Model

```r
predict_rpart <- predict(mod_rpart,newdata = testing,
                         type = "class")
confusionMatrix(predict_rpart,testing$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 1175  172   47   70   33
##          B   77  528  123   48  111
##          C   61  154  610  192  162
##          D   49   80   36  434   66
##          E   33   15   39   60  529
## 
## Overall Statistics
##                                           
##                Accuracy : 0.668           
##                  95% CI : (0.6546, 0.6812)
##     No Information Rate : 0.2845          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.5791          
##  Mcnemar's Test P-Value : < 2.2e-16       
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.8423   0.5564   0.7135   0.5398   0.5871
## Specificity            0.9082   0.9092   0.8595   0.9437   0.9633
## Pos Pred Value         0.7849   0.5953   0.5174   0.6526   0.7825
## Neg Pred Value         0.9354   0.8952   0.9342   0.9127   0.9120
## Prevalence             0.2845   0.1935   0.1743   0.1639   0.1837
## Detection Rate         0.2396   0.1077   0.1244   0.0885   0.1079
## Detection Prevalence   0.3053   0.1809   0.2404   0.1356   0.1378
## Balanced Accuracy      0.8753   0.7328   0.7865   0.7417   0.7752
```

### C50 Tree

Same processing


```r
library(C50)
system.time(mod_C50 <- C5.0(formula = classe ~ ., data = training))
```

```
##    user  system elapsed 
##    2.25    0.00    2.25
```

```r
predict_C50 <- predict(mod_C50,newdata = testing)
confusionMatrix(predict_C50,testing$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 1370   23    5    8    2
##          B   11  890   22   12   20
##          C    6   23  800   24    5
##          D    6   10   22  751    8
##          E    2    3    6    9  866
## 
## Overall Statistics
##                                           
##                Accuracy : 0.9537          
##                  95% CI : (0.9475, 0.9594)
##     No Information Rate : 0.2845          
##     P-Value [Acc > NIR] : < 2e-16         
##                                           
##                   Kappa : 0.9414          
##  Mcnemar's Test P-Value : 0.06176         
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9821   0.9378   0.9357   0.9341   0.9612
## Specificity            0.9892   0.9836   0.9857   0.9888   0.9950
## Pos Pred Value         0.9730   0.9319   0.9324   0.9423   0.9774
## Neg Pred Value         0.9928   0.9851   0.9864   0.9871   0.9913
## Prevalence             0.2845   0.1935   0.1743   0.1639   0.1837
## Detection Rate         0.2794   0.1815   0.1631   0.1531   0.1766
## Detection Prevalence   0.2871   0.1947   0.1750   0.1625   0.1807
## Balanced Accuracy      0.9856   0.9607   0.9607   0.9614   0.9781
```

### SVM


```r
library(kernlab)
```

```r
library(e1071)
system.time(mod_svm <- svm(formula = classe ~ ., data = training,
                           kernel = "linear",cost = 10))
```

```
##    user  system elapsed 
##  158.87    0.38  162.66
```

```r
predict_svm <- predict(mod_svm,newdata = testing)
confusionMatrix(predict_svm,testing$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 1156  194  145   83   98
##          B   64  517   94   62  145
##          C   94  102  522  110  102
##          D   56   63   74  492  113
##          E   25   73   20   57  443
## 
## Overall Statistics
##                                           
##                Accuracy : 0.6383          
##                  95% CI : (0.6246, 0.6517)
##     No Information Rate : 0.2845          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.5392          
##  Mcnemar's Test P-Value : < 2.2e-16       
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.8287   0.5448   0.6105   0.6119  0.49168
## Specificity            0.8518   0.9077   0.8992   0.9254  0.95628
## Pos Pred Value         0.6897   0.5862   0.5613   0.6165  0.71683
## Neg Pred Value         0.9260   0.8926   0.9162   0.9240  0.89314
## Prevalence             0.2845   0.1935   0.1743   0.1639  0.18373
## Detection Rate         0.2357   0.1054   0.1064   0.1003  0.09033
## Detection Prevalence   0.3418   0.1799   0.1896   0.1627  0.12602
## Balanced Accuracy      0.8402   0.7262   0.7549   0.7687  0.72398
```

### Random forest


```r
library(randomForest)
system.time(modrf <- randomForest(formula = classe ~ ., data = training))
```

```
##    user  system elapsed 
##   30.90    0.17   31.80
```

```r
predict_rf <- predict(modrf,newdata = testing)
confusionMatrix(predict_rf,testing$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 1394    6    0    0    0
##          B    1  938    6    0    0
##          C    0    4  844   10    1
##          D    0    0    5  792    0
##          E    0    1    0    2  900
## 
## Overall Statistics
##                                           
##                Accuracy : 0.9927          
##                  95% CI : (0.9899, 0.9949)
##     No Information Rate : 0.2845          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.9907          
##  Mcnemar's Test P-Value : NA              
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9993   0.9884   0.9871   0.9851   0.9989
## Specificity            0.9983   0.9982   0.9963   0.9988   0.9993
## Pos Pred Value         0.9957   0.9926   0.9825   0.9937   0.9967
## Neg Pred Value         0.9997   0.9972   0.9973   0.9971   0.9998
## Prevalence             0.2845   0.1935   0.1743   0.1639   0.1837
## Detection Rate         0.2843   0.1913   0.1721   0.1615   0.1835
## Detection Prevalence   0.2855   0.1927   0.1752   0.1625   0.1841
## Balanced Accuracy      0.9988   0.9933   0.9917   0.9919   0.9991
```

## Model Verification

### Use Parallel CPU

Using parallel acceleration because it's very consuming time in caret Random forest.


```r
library(parallel)
library(doParallel)
cluster <- makeCluster(detectCores() - 1) # convention to leave 1 core for OS
registerDoParallel(cluster)
```

### CV Random forest

Random forest with params PCA method and 2 Cross Validations


```r
set.seed(33)
fitControl <- trainControl(method = "cv",
                           number = 2,
                           classProbs = T,
                           preProcOptions = c("pca"),
                           verboseIter = T,
                           allowParallel = T)

system.time(mod_rf <- train(classe ~ ., data = training,
                            trControl = fitControl,
                            preProcess = c("center", "scale"),
                            method = "rf"))
```

```
## Aggregating results
## Selecting tuning parameters
## Fitting mtry = 16 on full training set
```

```
##    user  system elapsed 
##   33.06    0.27  111.83
```

```r
predict_rf <- predict(mod_rf,newdata = testing)
confusionMatrix(predict_rf,testing$classe)
```

```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 1393    8    0    2    0
##          B    2  932    6    1    0
##          C    0    8  842   10    2
##          D    0    0    7  789    1
##          E    0    1    0    2  898
## 
## Overall Statistics
##                                           
##                Accuracy : 0.9898          
##                  95% CI : (0.9866, 0.9924)
##     No Information Rate : 0.2845          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.9871          
##  Mcnemar's Test P-Value : NA              
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9986   0.9821   0.9848   0.9813   0.9967
## Specificity            0.9972   0.9977   0.9951   0.9980   0.9993
## Pos Pred Value         0.9929   0.9904   0.9768   0.9900   0.9967
## Neg Pred Value         0.9994   0.9957   0.9968   0.9963   0.9993
## Prevalence             0.2845   0.1935   0.1743   0.1639   0.1837
## Detection Rate         0.2841   0.1900   0.1717   0.1609   0.1831
## Detection Prevalence   0.2861   0.1919   0.1758   0.1625   0.1837
## Balanced Accuracy      0.9979   0.9899   0.9899   0.9897   0.9980
```

### Final Stop Parallel


```r
stopCluster(cluster)
registerDoSEQ()
```

### Final Result, Predict 20 Cases

```r
(final_result <- predict(modrf, testing_raw))
```

```
##  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17 18 19 20 
##  B  A  B  A  A  E  D  B  A  A  B  C  B  A  E  E  A  B  B  B 
## Levels: A B C D E
```

## Conclutions

Ramdon Forest Accuracy is 0.9927 and the best fit model of this case. So we choose Ramdon Forest to predict 20 cases.

## Appendix

- Training Data Corrplot

- Regression Tree Plot

* THE END

