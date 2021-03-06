---
title: 汽车数据R语言机器学习分析
published: true
date: '08/13/2019 01:05 pm'
metadata:
    keywords: '博客,数据分析,R语言,逻辑回归,随机森林,CART树,回归树,线性判别,coursera'
    author: 'Autoz Zhao'
taxonomy:
    category:
        - blog
    tag:
        - Rstudio
        - R
        - Coursera
        - Project
        - Blog
        - Grav
        - Quiz
sitemap:
    changefreq: weekly
    priority: 0.7
external_links:
    process: true
    no_follow: true
image:
    summary:
        enabled: '1'
        file: cardata.jpg
summary:
    enabled: '1'
    format: short
googledesc: '汽车数据R语言机器学习分析 数据科学coursera课程 逻辑回归 随机森林 CART树 回归树 线性判别title: "Car Evaluation Analysis" author: "Suraj Vidyadaran"对汽车数据进行17种方式'
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
---

## 汽车数据R语言机器学习分析

* title: "Car Evaluation Analysis"
* author: "Suraj Vidyadaran"
* date: "Sunday, February 21, 2016"
* output: md_document

对汽车数据进行17种方式的数据分析

<!-- MarkdownTOC autoanchor="true" autolink="true" uri_encoding="false" -->

- [Load the data](#load-the-data)
- [Exploratory Data Analysis](#exploratory-data-analysis)
- [## Classification Analysis](#-classification-analysis)
    - [Linear Classification 线性分类](#linear-classification-线性分类)
        - [1 Logistic Regression 逻辑回归](#1-logistic-regression-逻辑回归)
        - [2 Linear Discriminant Analysis](#2-linear-discriminant-analysis)
    - [Non-Linear Classification](#non-linear-classification)
        - [3 Mixture Discriminant Analysis](#3-mixture-discriminant-analysis)
        - [4 Quadratic Discriminant Analysis](#4-quadratic-discriminant-analysis)
        - [4 Regularized Discriminant Analysis](#4-regularized-discriminant-analysis)
        - [5 Neural Network](#5-neural-network)
        - [6 Flexible Discriminant Analysis](#6-flexible-discriminant-analysis)
        - [7 Support Vector Machine](#7-support-vector-machine)
        - [8 k-Nearest Neighbors](#8-k-nearest-neighbors)
        - [9 Naive Bayes](#9-naive-bayes)
    - [Non-Linear Classification with Decision Trees](#non-linear-classification-with-decision-trees)
        - [10 Classification and Regression Trees\(CART\)](#10-classification-and-regression-treescart)
        - [11 C4.5](#11-c45)
        - [12 PART](#12-part)
        - [13 Bagging CART](#13-bagging-cart)
        - [14 Random Forest](#14-random-forest)
        - [15 Gradient Boosted Machine](#15-gradient-boosted-machine)
        - [16 Boosted C5.0](#16-boosted-c50)

<!-- /MarkdownTOC -->



<a id="load-the-data"></a>
## Load the data

buying 价格 maint 大小 doors 门 persons 载人 lug_boot 轮子 safety 安全 class 等级

```r
car_eval <- read.csv("car.data.txt", header=FALSE)
colnames(car_eval)<-c("buying","maint","doors","persons","lug_boot","safety","class")
head(car_eval)
```
    ##   buying maint doors persons lug_boot safety class
    ## 1  vhigh vhigh     2       2    small    low unacc
    ## 2  vhigh vhigh     2       2    small    med unacc
    ## 3  vhigh vhigh     2       2    small   high unacc
    ## 4  vhigh vhigh     2       2      med    low unacc
    ## 5  vhigh vhigh     2       2      med    med unacc
    ## 6  vhigh vhigh     2       2      med   high unacc

<a id="exploratory-data-analysis"></a>
## Exploratory Data Analysis

    # summary(car_eval)
    str(car_eval)

    ## 'data.frame':    1728 obs. of  7 variables:
    ##  $ buying  : Factor w/ 4 levels "high","low","med",..: 4 4 4 4 4 4 4 4 4 4 ...
    ##  $ maint   : Factor w/ 4 levels "high","low","med",..: 4 4 4 4 4 4 4 4 4 4 ...
    ##  $ doors   : Factor w/ 4 levels "2","3","4","5more": 1 1 1 1 1 1 1 1 1 1 ...
    ##  $ persons : Factor w/ 3 levels "2","4","more": 1 1 1 1 1 1 1 1 1 2 ...
    ##  $ lug_boot: Factor w/ 3 levels "big","med","small": 3 3 3 2 2 2 1 1 1 3 ...
    ##  $ safety  : Factor w/ 3 levels "high","low","med": 2 3 1 2 3 1 2 3 1 2 ...
    ##  $ class   : Factor w/ 4 levels "acc","good","unacc",..: 3 3 3 3 3 3 3 3 3 3 ...

<a id="-classification-analysis"></a>
## Classification Analysis
-------------------------

<a id="linear-classification-线性分类"></a>
### Linear Classification 线性分类

<a id="1-logistic-regression-逻辑回归"></a>
#### 1 Logistic Regression 逻辑回归

    library(VGAM) # 广义线性模型

    #Build the model
    model1<-vglm(class~buying+maint+doors+persons+lug_boot+safety,family = "multinomial",data=car_eval)

    #Summarize the model
    # summary(model1)

    #Predict using the model
    x<-car_eval[,1:6]
    y<-car_eval[,7]
    probability<-predict(model1,x,type="response") # 相应就是二元
    car_eval$pred_log_reg<-apply(probability,1,which.max)
    car_eval$pred_log_reg[which(car_eval$pred_log_reg=="1")]<-levels(car_eval$class)[1]
    car_eval$pred_log_reg[which(car_eval$pred_log_reg=="2")]<-levels(car_eval$class)[2]
    car_eval$pred_log_reg[which(car_eval$pred_log_reg=="3")]<-levels(car_eval$class)[3]
    car_eval$pred_log_reg[which(car_eval$pred_log_reg=="4")]<-levels(car_eval$class)[4]

    #Accuracy of the model
    mtab<-table(car_eval$pred_log_reg,car_eval$class)
    library(caret)
    confusionMatrix(mtab)

    ## Confusion Matrix and Statistics
    ## 
    ##        
    ##          acc good unacc vgood
    ##   acc    340    5    47     2
    ##   good     8   59     1     0
    ##   unacc   31    0  1162     0
    ##   vgood    5    5     0    63
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.9398          
    ##                  95% CI : (0.9275, 0.9506)
    ##     No Information Rate : 0.7002          
    ##     P-Value [Acc > NIR] : < 2.2e-16       
    ##                                           
    ##                   Kappa : 0.8699          
    ##  Mcnemar's Test P-Value : NA              
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: acc Class: good Class: unacc Class: vgood
    ## Sensitivity              0.8854     0.85507       0.9603      0.96923
    ## Specificity              0.9598     0.99458       0.9402      0.99399
    ## Pos Pred Value           0.8629     0.86765       0.9740      0.86301
    ## Neg Pred Value           0.9670     0.99398       0.9103      0.99879
    ## Prevalence               0.2222     0.03993       0.7002      0.03762
    ## Detection Rate           0.1968     0.03414       0.6725      0.03646
    ## Detection Prevalence     0.2280     0.03935       0.6904      0.04225
    ## Balanced Accuracy        0.9226     0.92482       0.9502      0.98161

<a id="2-linear-discriminant-analysis"></a>
#### 2 Linear Discriminant Analysis

    library(caret)
    library(MASS)

    #Build the model
    model2<-lda(class~buying+maint+doors+persons+lug_boot+safety,data=car_eval)

    #Summarize the model
    # summary(model2)

    #Predict using the model
    car_eval$pred_lda<-predict(model2,x)$class

    #Accuracy of the model
    mtab<-table(car_eval$pred_lda,car_eval$class)
    confusionMatrix(mtab)

    ## Confusion Matrix and Statistics
    ## 
    ##        
    ##          acc good unacc vgood
    ##   acc    361   44    70    27
    ##   good    10   23     2     3
    ##   unacc   13    0  1138     0
    ##   vgood    0    2     0    35
    ## 
    ## Overall Statistics
    ##                                          
    ##                Accuracy : 0.901          
    ##                  95% CI : (0.886, 0.9147)
    ##     No Information Rate : 0.7002         
    ##     P-Value [Acc > NIR] : < 2.2e-16      
    ##                                          
    ##                   Kappa : 0.7883         
    ##  Mcnemar's Test P-Value : NA             
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: acc Class: good Class: unacc Class: vgood
    ## Sensitivity              0.9401     0.33333       0.9405      0.53846
    ## Specificity              0.8951     0.99096       0.9749      0.99880
    ## Pos Pred Value           0.7191     0.60526       0.9887      0.94595
    ## Neg Pred Value           0.9812     0.97278       0.8752      0.98226
    ## Prevalence               0.2222     0.03993       0.7002      0.03762
    ## Detection Rate           0.2089     0.01331       0.6586      0.02025
    ## Detection Prevalence     0.2905     0.02199       0.6661      0.02141
    ## Balanced Accuracy        0.9176     0.66215       0.9577      0.76863

<a id="non-linear-classification"></a>
### Non-Linear Classification

<a id="3-mixture-discriminant-analysis"></a>
#### 3 Mixture Discriminant Analysis

    library(mda)

    #Build the model
    model3<-mda(class~buying+maint+doors+persons+lug_boot+safety,data=car_eval)

    #Summarize the model
    # summary(model3)

    #Predict using the model
    car_eval$pred_mda<-predict(model3,x)

    #Accuracy of the model
    mtab<-table(car_eval$pred_mda,car_eval$class)
    confusionMatrix(mtab)

    ## Confusion Matrix and Statistics
    ## 
    ##        
    ##          acc good unacc vgood
    ##   acc    353   13    92    16
    ##   good    20   51     3     0
    ##   unacc    5    0  1115     0
    ##   vgood    6    5     0    49
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.9074          
    ##                  95% CI : (0.8928, 0.9207)
    ##     No Information Rate : 0.7002          
    ##     P-Value [Acc > NIR] : < 2.2e-16       
    ##                                           
    ##                   Kappa : 0.808           
    ##  Mcnemar's Test P-Value : NA              
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: acc Class: good Class: unacc Class: vgood
    ## Sensitivity              0.9193     0.73913       0.9215      0.75385
    ## Specificity              0.9100     0.98614       0.9903      0.99339
    ## Pos Pred Value           0.7447     0.68919       0.9955      0.81667
    ## Neg Pred Value           0.9753     0.98912       0.8438      0.99041
    ## Prevalence               0.2222     0.03993       0.7002      0.03762
    ## Detection Rate           0.2043     0.02951       0.6453      0.02836
    ## Detection Prevalence     0.2743     0.04282       0.6481      0.03472
    ## Balanced Accuracy        0.9146     0.86263       0.9559      0.87362

<a id="4-quadratic-discriminant-analysis"></a>
#### 4 Quadratic Discriminant Analysis

    library(MASS)

    #Build the model
    model4<-qda(class~buying+maint+doors+persons+lug_boot+safety,data=car_eval)

    #Summarize the model
    # summary(model4)

    #Predict using the model
    car_eval$pred_qda<-predict(model4,x)

    #Accuracy of the model
    mtab<-table(car_eval$pred_qda,car_eval$class)
    confusionMatrix(mtab)

<a id="4-regularized-discriminant-analysis"></a>
#### 4 Regularized Discriminant Analysis

    library(klaR)

    #Build the model
    model5<-rda(class~buying+maint+doors+persons+lug_boot+safety,data=car_eval,gamma = 0.05,lambda = 0.01)

    #Summarize the model
    # summary(model5)

    #Predict using the model
    car_eval$pred_rda<-predict(model5,x)$class

    #Accuracy of the model
    mtab<-table(car_eval$pred_rda,car_eval$class)
    confusionMatrix(mtab)

    ## Confusion Matrix and Statistics
    ## 
    ##        
    ##          acc good unacc vgood
    ##   acc    317    0   134     0
    ##   good    45   63     4     0
    ##   unacc    0    0  1072     0
    ##   vgood   22    6     0    65
    ## 
    ## Overall Statistics
    ##                                          
    ##                Accuracy : 0.8779         
    ##                  95% CI : (0.8615, 0.893)
    ##     No Information Rate : 0.7002         
    ##     P-Value [Acc > NIR] : < 2.2e-16      
    ##                                          
    ##                   Kappa : 0.7572         
    ##  Mcnemar's Test P-Value : NA             
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: acc Class: good Class: unacc Class: vgood
    ## Sensitivity              0.8255     0.91304       0.8860      1.00000
    ## Specificity              0.9003     0.97046       1.0000      0.98316
    ## Pos Pred Value           0.7029     0.56250       1.0000      0.69892
    ## Neg Pred Value           0.9475     0.99629       0.7896      1.00000
    ## Prevalence               0.2222     0.03993       0.7002      0.03762
    ## Detection Rate           0.1834     0.03646       0.6204      0.03762
    ## Detection Prevalence     0.2610     0.06481       0.6204      0.05382
    ## Balanced Accuracy        0.8629     0.94175       0.9430      0.99158

<a id="5-neural-network"></a>
#### 5 Neural Network

    library(nnet)

    #Build the model
    model6<-nnet(class~buying+maint+doors+persons+lug_boot+safety,data=car_eval,size = 4,decay = 0.0001,maxit = 500)

    ## # weights:  84
    ## initial  value 4315.884207 
    ## iter  10 value 494.299222
    ## iter  20 value 271.980488
    ## iter  30 value 201.767107
    ## iter  40 value 170.953147
    ## iter  50 value 110.980954
    ## iter  60 value 56.771482
    ## iter  70 value 41.572251
    ## iter  80 value 34.479180
    ## iter  90 value 30.761660
    ## iter 100 value 29.156225
    ## iter 110 value 28.361403
    ## iter 120 value 27.944054
    ## iter 130 value 27.553794
    ## iter 140 value 26.698026
    ## iter 150 value 25.411406
    ## iter 160 value 24.996274
    ## iter 170 value 24.851429
    ## iter 180 value 24.830112
    ## iter 190 value 24.735811
    ## iter 200 value 24.426379
    ## iter 210 value 23.802677
    ## iter 220 value 23.478896
    ## iter 230 value 23.307991
    ## iter 240 value 23.164112
    ## iter 250 value 23.047281
    ## iter 260 value 22.863117
    ## iter 270 value 22.802107
    ## iter 280 value 22.739661
    ## iter 290 value 22.689016
    ## iter 300 value 22.625042
    ## iter 310 value 22.595026
    ## iter 320 value 22.581459
    ## iter 330 value 22.573767
    ## iter 340 value 22.566224
    ## iter 350 value 22.565535
    ## iter 360 value 22.564483
    ## iter 370 value 22.560267
    ## iter 380 value 22.557433
    ## iter 390 value 22.555073
    ## iter 400 value 22.552698
    ## iter 410 value 22.550126
    ## iter 420 value 22.548059
    ## iter 430 value 22.546974
    ## iter 440 value 22.546031
    ## iter 450 value 22.545549
    ## iter 460 value 22.545069
    ## iter 470 value 22.544765
    ## iter 480 value 22.544515
    ## iter 490 value 22.544258
    ## iter 500 value 22.544140
    ## final  value 22.544140 
    ## stopped after 500 iterations

    #Summarize the model
    # summary(model6)

    #Predict using the model
    car_eval$pred_nnet<-predict(model6,x,type="class")

    #Accuracy of the model
    mtab<-table(car_eval$pred_nnet,car_eval$class)
    confusionMatrix(mtab)

    ## Confusion Matrix and Statistics
    ## 
    ##        
    ##          acc good unacc vgood
    ##   acc    384    0     5     0
    ##   good     0   69     0     0
    ##   unacc    0    0  1205     0
    ##   vgood    0    0     0    65
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.9971          
    ##                  95% CI : (0.9933, 0.9991)
    ##     No Information Rate : 0.7002          
    ##     P-Value [Acc > NIR] : < 2.2e-16       
    ##                                           
    ##                   Kappa : 0.9937          
    ##  Mcnemar's Test P-Value : NA              
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: acc Class: good Class: unacc Class: vgood
    ## Sensitivity              1.0000     1.00000       0.9959      1.00000
    ## Specificity              0.9963     1.00000       1.0000      1.00000
    ## Pos Pred Value           0.9871     1.00000       1.0000      1.00000
    ## Neg Pred Value           1.0000     1.00000       0.9904      1.00000
    ## Prevalence               0.2222     0.03993       0.7002      0.03762
    ## Detection Rate           0.2222     0.03993       0.6973      0.03762
    ## Detection Prevalence     0.2251     0.03993       0.6973      0.03762
    ## Balanced Accuracy        0.9981     1.00000       0.9979      1.00000

<a id="6-flexible-discriminant-analysis"></a>
#### 6 Flexible Discriminant Analysis

    library(mda)

    #Build the model

    model7<-fda(class~buying+maint+doors+persons+lug_boot+safety,data=car_eval)

    #Summarize the model
    # summary(model7)

    #Predict using the model
    car_eval$pred_fda<-predict(model7,x,type="class")

    #Accuracy of the model
    mtab<-table(car_eval$pred_fda,car_eval$class)
    confusionMatrix(mtab)

    ## Confusion Matrix and Statistics
    ## 
    ##        
    ##          acc good unacc vgood
    ##   acc    361   44    72    27
    ##   good    10   23     2     3
    ##   unacc   13    0  1136     0
    ##   vgood    0    2     0    35
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.8999          
    ##                  95% CI : (0.8848, 0.9136)
    ##     No Information Rate : 0.7002          
    ##     P-Value [Acc > NIR] : < 2.2e-16       
    ##                                           
    ##                   Kappa : 0.786           
    ##  Mcnemar's Test P-Value : NA              
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: acc Class: good Class: unacc Class: vgood
    ## Sensitivity              0.9401     0.33333       0.9388      0.53846
    ## Specificity              0.8936     0.99096       0.9749      0.99880
    ## Pos Pred Value           0.7163     0.60526       0.9887      0.94595
    ## Neg Pred Value           0.9812     0.97278       0.8722      0.98226
    ## Prevalence               0.2222     0.03993       0.7002      0.03762
    ## Detection Rate           0.2089     0.01331       0.6574      0.02025
    ## Detection Prevalence     0.2917     0.02199       0.6649      0.02141
    ## Balanced Accuracy        0.9169     0.66215       0.9569      0.76863

<a id="7-support-vector-machine"></a>
#### 7 Support Vector Machine

    library(kernlab)

    #Build the model
    model8<-ksvm(class~buying+maint+doors+persons+lug_boot+safety,data=car_eval)

    #Summarize the model
    # summary(model8)

    #Predict using the model
    car_eval$pred_svm<-predict(model8,x,type="response")

    #Accuracy of the model
    mtab<-table(car_eval$pred_svm,car_eval$class)
    confusionMatrix(mtab)

    ## Confusion Matrix and Statistics
    ## 
    ##        
    ##          acc good unacc vgood
    ##   acc    375    0    33     0
    ##   good     8   60     3     0
    ##   unacc    0    0  1174     0
    ##   vgood    1    9     0    65
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.9688          
    ##                  95% CI : (0.9594, 0.9764)
    ##     No Information Rate : 0.7002          
    ##     P-Value [Acc > NIR] : < 2.2e-16       
    ##                                           
    ##                   Kappa : 0.9333          
    ##  Mcnemar's Test P-Value : NA              
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: acc Class: good Class: unacc Class: vgood
    ## Sensitivity              0.9766     0.86957       0.9702      1.00000
    ## Specificity              0.9754     0.99337       1.0000      0.99399
    ## Pos Pred Value           0.9191     0.84507       1.0000      0.86667
    ## Neg Pred Value           0.9932     0.99457       0.9350      1.00000
    ## Prevalence               0.2222     0.03993       0.7002      0.03762
    ## Detection Rate           0.2170     0.03472       0.6794      0.03762
    ## Detection Prevalence     0.2361     0.04109       0.6794      0.04340
    ## Balanced Accuracy        0.9760     0.93147       0.9851      0.99699

<a id="8-k-nearest-neighbors"></a>
#### 8 k-Nearest Neighbors

    library(caret)

    #Build the model
    model9<-knn3(class~buying+maint+doors+persons+lug_boot+safety,data=car_eval,k=5)

    #Summarize the model
    # summary(model9)

    #Predict using the model
    car_eval$pred_knn<-predict(model9,x,type="class")

    #Accuracy of the model
    mtab<-table(car_eval$pred_knn,car_eval$class)
    confusionMatrix(mtab)

    ## Confusion Matrix and Statistics
    ## 
    ##        
    ##          acc good unacc vgood
    ##   acc    332   47    11    19
    ##   good     6   13     0     4
    ##   unacc   44    5  1199     1
    ##   vgood    2    4     0    41
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.9172          
    ##                  95% CI : (0.9032, 0.9298)
    ##     No Information Rate : 0.7002          
    ##     P-Value [Acc > NIR] : < 2.2e-16       
    ##                                           
    ##                   Kappa : 0.8118          
    ##  Mcnemar's Test P-Value : 2.234e-13       
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: acc Class: good Class: unacc Class: vgood
    ## Sensitivity              0.8646    0.188406       0.9909      0.63077
    ## Specificity              0.9427    0.993972       0.9035      0.99639
    ## Pos Pred Value           0.8117    0.565217       0.9600      0.87234
    ## Neg Pred Value           0.9606    0.967155       0.9770      0.98572
    ## Prevalence               0.2222    0.039931       0.7002      0.03762
    ## Detection Rate           0.1921    0.007523       0.6939      0.02373
    ## Detection Prevalence     0.2367    0.013310       0.7228      0.02720
    ## Balanced Accuracy        0.9036    0.591189       0.9472      0.81358

<a id="9-naive-bayes"></a>
#### 9 Naive Bayes

    library(e1071)

    #Build the model
    model10<-naiveBayes(class~buying+maint+doors+persons+lug_boot+safety,data=car_eval,k=5)

    #Summarize the model
    # summary(model10)

    #Predict using the model
    car_eval$pred_naive<-predict(model10,x)

    #Accuracy of the model
    mtab<-table(car_eval$pred_naive,car_eval$class)
    confusionMatrix(mtab)

    ## Confusion Matrix and Statistics
    ## 
    ##        
    ##          acc good unacc vgood
    ##   acc    289   46    47    26
    ##   good    10   21     2     0
    ##   unacc   85    0  1161     0
    ##   vgood    0    2     0    39
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.8738          
    ##                  95% CI : (0.8573, 0.8891)
    ##     No Information Rate : 0.7002          
    ##     P-Value [Acc > NIR] : < 2.2e-16       
    ##                                           
    ##                   Kappa : 0.7139          
    ##  Mcnemar's Test P-Value : NA              
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: acc Class: good Class: unacc Class: vgood
    ## Sensitivity              0.7526     0.30435       0.9595      0.60000
    ## Specificity              0.9115     0.99277       0.8359      0.99880
    ## Pos Pred Value           0.7083     0.63636       0.9318      0.95122
    ## Neg Pred Value           0.9280     0.97168       0.8983      0.98459
    ## Prevalence               0.2222     0.03993       0.7002      0.03762
    ## Detection Rate           0.1672     0.01215       0.6719      0.02257
    ## Detection Prevalence     0.2361     0.01910       0.7211      0.02373
    ## Balanced Accuracy        0.8320     0.64856       0.8977      0.79940

<a id="non-linear-classification-with-decision-trees"></a>
### Non-Linear Classification with Decision Trees

<a id="10-classification-and-regression-treescart"></a>
#### 10 Classification and Regression Trees(CART)

    library(rpart)

    #Build the model
    model11<-rpart(class~buying+maint+doors+persons+lug_boot+safety,data=car_eval)

    #Summarize the model
    # summary(model11)

    #Predict using the model
    car_eval$pred_cart<-predict(model11,x,type="class")

    #Accuracy of the model
    mtab<-table(car_eval$pred_cart,car_eval$class)
    confusionMatrix(mtab)

    ## Confusion Matrix and Statistics
    ## 
    ##        
    ##          acc good unacc vgood
    ##   acc    358    0    45    13
    ##   good    16   60     4     0
    ##   unacc    7    0  1161     0
    ##   vgood    3    9     0    52
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.9439          
    ##                  95% CI : (0.9319, 0.9542)
    ##     No Information Rate : 0.7002          
    ##     P-Value [Acc > NIR] : < 2.2e-16       
    ##                                           
    ##                   Kappa : 0.8806          
    ##  Mcnemar's Test P-Value : NA              
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: acc Class: good Class: unacc Class: vgood
    ## Sensitivity              0.9323     0.86957       0.9595      0.80000
    ## Specificity              0.9568     0.98794       0.9865      0.99278
    ## Pos Pred Value           0.8606     0.75000       0.9940      0.81250
    ## Neg Pred Value           0.9802     0.99454       0.9125      0.99219
    ## Prevalence               0.2222     0.03993       0.7002      0.03762
    ## Detection Rate           0.2072     0.03472       0.6719      0.03009
    ## Detection Prevalence     0.2407     0.04630       0.6759      0.03704
    ## Balanced Accuracy        0.9446     0.92875       0.9730      0.89639

<a id="11-c45"></a>
#### 11 C4.5

    library(caret)
    library(RWeka)

    #Build the model
    model12<-J48(class~buying+maint+doors+persons+lug_boot+safety,data=car_eval)

    #Summarize the model
    # summary(model12)

    #Predict using the model
    car_eval$pred_c45<-predict(model12,x)

    #Accuracy of the model
    mtab<-table(car_eval$pred_c45,car_eval$class)
    confusionMatrix(mtab)

    ## Confusion Matrix and Statistics
    ## 
    ##        
    ##          acc good unacc vgood
    ##   acc    380    9    35     4
    ##   good     2   57     3     6
    ##   unacc    0    0  1172     0
    ##   vgood    2    3     0    55
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.963           
    ##                  95% CI : (0.9529, 0.9714)
    ##     No Information Rate : 0.7002          
    ##     P-Value [Acc > NIR] : < 2.2e-16       
    ##                                           
    ##                   Kappa : 0.9207          
    ##  Mcnemar's Test P-Value : NA              
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: acc Class: good Class: unacc Class: vgood
    ## Sensitivity              0.9896     0.82609       0.9686      0.84615
    ## Specificity              0.9643     0.99337       1.0000      0.99699
    ## Pos Pred Value           0.8879     0.83824       1.0000      0.91667
    ## Neg Pred Value           0.9969     0.99277       0.9317      0.99400
    ## Prevalence               0.2222     0.03993       0.7002      0.03762
    ## Detection Rate           0.2199     0.03299       0.6782      0.03183
    ## Detection Prevalence     0.2477     0.03935       0.6782      0.03472
    ## Balanced Accuracy        0.9769     0.90973       0.9843      0.92157

<a id="12-part"></a>
#### 12 PART

    library(RWeka)

    #Build the model
    model13<-PART(class~buying+maint+doors+persons+lug_boot+safety,data=car_eval)

    #Summarize the model
    # summary(model13)

    #Predict using the model
    car_eval$pred_part<-predict(model13,x)

    #Accuracy of the model
    mtab<-table(car_eval$pred_part,car_eval$class)
    confusionMatrix(mtab)

    ## Confusion Matrix and Statistics
    ## 
    ##        
    ##          acc good unacc vgood
    ##   acc    374    3    13     0
    ##   good     1   63     1     0
    ##   unacc    6    1  1196     0
    ##   vgood    3    2     0    65
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.9826          
    ##                  95% CI : (0.9753, 0.9883)
    ##     No Information Rate : 0.7002          
    ##     P-Value [Acc > NIR] : < 2.2e-16       
    ##                                           
    ##                   Kappa : 0.9622          
    ##  Mcnemar's Test P-Value : NA              
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: acc Class: good Class: unacc Class: vgood
    ## Sensitivity              0.9740     0.91304       0.9884      1.00000
    ## Specificity              0.9881     0.99879       0.9865      0.99699
    ## Pos Pred Value           0.9590     0.96923       0.9942      0.92857
    ## Neg Pred Value           0.9925     0.99639       0.9733      1.00000
    ## Prevalence               0.2222     0.03993       0.7002      0.03762
    ## Detection Rate           0.2164     0.03646       0.6921      0.03762
    ## Detection Prevalence     0.2257     0.03762       0.6962      0.04051
    ## Balanced Accuracy        0.9810     0.95592       0.9875      0.99850

<a id="13-bagging-cart"></a>
#### 13 Bagging CART

    library(ipred)

    #Build the model
    model14<-bagging(class~buying+maint+doors+persons+lug_boot+safety,data=car_eval)

    #Summarize the model
    # summary(model14)

    #Predict using the model
    car_eval$pred_bagging<-predict(model14,x)

    #Accuracy of the model
    mtab<-table(car_eval$pred_bagging,car_eval$class)
    confusionMatrix(mtab)

    ## Confusion Matrix and Statistics
    ## 
    ##        
    ##          acc good unacc vgood
    ##   acc    384    0     0     0
    ##   good     0   69     0     0
    ##   unacc    0    0  1210     0
    ##   vgood    0    0     0    65
    ## 
    ## Overall Statistics
    ##                                      
    ##                Accuracy : 1          
    ##                  95% CI : (0.9979, 1)
    ##     No Information Rate : 0.7002     
    ##     P-Value [Acc > NIR] : < 2.2e-16  
    ##                                      
    ##                   Kappa : 1          
    ##  Mcnemar's Test P-Value : NA         
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: acc Class: good Class: unacc Class: vgood
    ## Sensitivity              1.0000     1.00000       1.0000      1.00000
    ## Specificity              1.0000     1.00000       1.0000      1.00000
    ## Pos Pred Value           1.0000     1.00000       1.0000      1.00000
    ## Neg Pred Value           1.0000     1.00000       1.0000      1.00000
    ## Prevalence               0.2222     0.03993       0.7002      0.03762
    ## Detection Rate           0.2222     0.03993       0.7002      0.03762
    ## Detection Prevalence     0.2222     0.03993       0.7002      0.03762
    ## Balanced Accuracy        1.0000     1.00000       1.0000      1.00000

<a id="14-random-forest"></a>
#### 14 Random Forest

    library(randomForest)

    #Build the model
    model15<-randomForest(class~buying+maint+doors+persons+lug_boot+safety,data=car_eval)

    #Summarize the model
    # summary(model15)

    #Predict using the model
    car_eval$pred_randomforest<-predict(model15,x)

    #Accuracy of the model
    mtab<-table(car_eval$pred_randomforest,car_eval$class)
    confusionMatrix(mtab)

    ## Confusion Matrix and Statistics
    ## 
    ##        
    ##          acc good unacc vgood
    ##   acc    384    0     1     0
    ##   good     0   69     0     0
    ##   unacc    0    0  1209     0
    ##   vgood    0    0     0    65
    ## 
    ## Overall Statistics
    ##                                      
    ##                Accuracy : 0.9994     
    ##                  95% CI : (0.9968, 1)
    ##     No Information Rate : 0.7002     
    ##     P-Value [Acc > NIR] : < 2.2e-16  
    ##                                      
    ##                   Kappa : 0.9987     
    ##  Mcnemar's Test P-Value : NA         
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: acc Class: good Class: unacc Class: vgood
    ## Sensitivity              1.0000     1.00000       0.9992      1.00000
    ## Specificity              0.9993     1.00000       1.0000      1.00000
    ## Pos Pred Value           0.9974     1.00000       1.0000      1.00000
    ## Neg Pred Value           1.0000     1.00000       0.9981      1.00000
    ## Prevalence               0.2222     0.03993       0.7002      0.03762
    ## Detection Rate           0.2222     0.03993       0.6997      0.03762
    ## Detection Prevalence     0.2228     0.03993       0.6997      0.03762
    ## Balanced Accuracy        0.9996     1.00000       0.9996      1.00000

<a id="15-gradient-boosted-machine"></a>
#### 15 Gradient Boosted Machine

    library(gbm)

    #Build the model
    model16<-gbm(class~buying+maint+doors+persons+lug_boot+safety,data=car_eval,distribution="multinomial")

    #Summarize the model
    # summary(model16)

    #Predict using the model
    probability<-predict(model16,x,n.trees=1)
    car_eval$pred_gbm<-colnames(probability)[apply(probability,1,which.max)]

    #Accuracy of the model
    mtab<-table(car_eval$pred_gbm,car_eval$class)
    mtab

    ##        
    ##          acc good unacc vgood
    ##   unacc  384   69  1210    65

<a id="16-boosted-c50"></a>
#### 16 Boosted C5.0

    library(C50)

    #Build the model
    model17<-C5.0(class~buying+maint+doors+persons+lug_boot+safety,data=car_eval,trials=10)

    #Summarize the model
    # summary(model17)

    #Predict using the model
    car_eval$pred_c50<-predict(model17,x)

    #Accuracy of the model
    mtab<-table(car_eval$pred_c50,car_eval$class)
    confusionMatrix(mtab)

    ## Confusion Matrix and Statistics
    ## 
    ##        
    ##          acc good unacc vgood
    ##   acc    383    0     3     2
    ##   good     0   69     0     0
    ##   unacc    1    0  1207     0
    ##   vgood    0    0     0    63
    ## 
    ## Overall Statistics
    ##                                           
    ##                Accuracy : 0.9965          
    ##                  95% CI : (0.9925, 0.9987)
    ##     No Information Rate : 0.7002          
    ##     P-Value [Acc > NIR] : < 2.2e-16       
    ##                                           
    ##                   Kappa : 0.9924          
    ##  Mcnemar's Test P-Value : NA              
    ## 
    ## Statistics by Class:
    ## 
    ##                      Class: acc Class: good Class: unacc Class: vgood
    ## Sensitivity              0.9974     1.00000       0.9975      0.96923
    ## Specificity              0.9963     1.00000       0.9981      1.00000
    ## Pos Pred Value           0.9871     1.00000       0.9992      1.00000
    ## Neg Pred Value           0.9993     1.00000       0.9942      0.99880
    ## Prevalence               0.2222     0.03993       0.7002      0.03762
    ## Detection Rate           0.2216     0.03993       0.6985      0.03646
    ## Detection Prevalence     0.2245     0.03993       0.6991      0.03646
    ## Balanced Accuracy        0.9968     1.00000       0.9978      0.98462

* THE END