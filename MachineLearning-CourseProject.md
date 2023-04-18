---
title: 'Practical Machine Learning: Course project'
author: "Hervé Schilling"
date: "2023-04-18"
output:
  html_document: 
    toc: yes
    toc_float: yes
    keep_md: yes
  pdf_document:
    toc: yes
    toc_depth: '3'
---



# introduction

Our study is based on the work of the computer science department of the Pontifical Catholic University of Rio de Janeiro. They compiled the data collected by various tools for capturing and measuring the movements and activity of individuals (fitbit, Jawbone Up etc.). In this project, we will use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participants who were asked to perform barbell lifts correctly and incorrectly in 5 different ways.

Using these data, we will try to identify a model to predict whether the exercises were carried out correctly or not.

Nowadays, the **link to the description of the dataset** provided in the subject results in a **404 error**. Also, we will dispense with this description and guess the essentials through data mining. As the document is limited to 2000 terms, this exploration will be provided as an appendix.

The given data sets are

-   a training data set with 19622 measures and 160 variables, including the "*classe*" attribute returning if the exercise was properly done

-   a testing data set with 20 measures and 160 variables, including the "problem_id".

# Getting, exploring and cleaning the data


```
## 
## Attachement du package : 'kernlab'
```

```
## L'objet suivant est masqué depuis 'package:ggplot2':
## 
##     alpha
```

```
## Le chargement a nécessité le package : tibble
```

```
## Le chargement a nécessité le package : bitops
```

```
## Rattle: A free graphical interface for data science with R.
## Version 5.5.1 Copyright (c) 2006-2021 Togaware Pty Ltd.
## Entrez 'rattle()' pour secouer, faire vibrer, et faire défiler vos données.
```

```
## corrplot 0.92 loaded
```




```
## [1] 19622   160
```

```
## [1]  20 160
```

We exclude columns that are mostly composed of NAs.


```
## [1] 19622    60
```

The first seven columns ("X", "user_name", "raw_timestamp_part_1" "raw_timestamp_part_2" "cvtd_timestamp", "new_window" and "num_window") contain the names of people, schedules and information about how the application will be displayed. It is obvious that they will not be good predictors, unless they assume that at certain times, some individuals would have reasons to be more successful or not (difficult digestion, laborious awakening...), which would introduce a bias in our results. So we will exclude them:


```
## [1] 19622    53
```

# Model selection

Let's build training and testing sets :


```
## [1] 11776    53
```

```
## [1] 7846   53
```

## Cross validation

The easiest way to perform k-fold cross-validation is to use the `trainControl()` function of caret package. We chose to use the cross-validation method (*method=cv*) with 5 (k-)folds to evaludate the model performance for *regression tree*, *random forest* and *Generalized Boosted regression* models.


```
## Le chargement a nécessité le package : foreach
```

```
## Le chargement a nécessité le package : iterators
```



### Regression tree



#### Selection of the best model with cross validation method


```
## CART 
## 
## 11776 samples
##    52 predictor
##     5 classes: 'A', 'B', 'C', 'D', 'E' 
## 
## No pre-processing
## Resampling: Cross-Validated (5 fold) 
## Summary of sample sizes: 9420, 9423, 9421, 9420, 9420 
## Resampling results across tuning parameters:
## 
##   cp          Accuracy   Kappa     
##   0.02177266  0.5991964  0.48752818
##   0.02883246  0.5317568  0.38989239
##   0.03559563  0.5111244  0.36218992
##   0.06055213  0.4174415  0.21037211
##   0.11568581  0.3320351  0.07295722
## 
## Accuracy was used to select the optimal model using the largest value.
## The final value used for the model was cp = 0.02177266.
```

With the **classification tree method**, the best model selected (0.02177266) reaches a **0.6 accuracy**, which is unsatisfying.

#### Prediction with classification tree:


```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 2021  631  618  574  209
##          B   35  436   39   13  160
##          C  135  352  564  180  331
##          D   36   99  147  519   95
##          E    5    0    0    0  647
## 
## Overall Statistics
##                                           
##                Accuracy : 0.5336          
##                  95% CI : (0.5225, 0.5447)
##     No Information Rate : 0.2845          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.3924          
##                                           
##  Mcnemar's Test P-Value : < 2.2e-16       
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9055  0.28722  0.41228  0.40358  0.44868
## Specificity            0.6380  0.96097  0.84594  0.94253  0.99922
## Pos Pred Value         0.4986  0.63836  0.36108  0.57924  0.99233
## Neg Pred Value         0.9444  0.84895  0.87206  0.88964  0.88949
## Prevalence             0.2845  0.19347  0.17436  0.16391  0.18379
## Detection Rate         0.2576  0.05557  0.07188  0.06615  0.08246
## Detection Prevalence   0.5166  0.08705  0.19908  0.11420  0.08310
## Balanced Accuracy      0.7718  0.62409  0.62911  0.67305  0.72395
```

The accuracy with regression tree method is 0.54, which is still quite weak.

### Random forest





#### Selection of the best model for the random forest method with cross validation:


```
## Random Forest 
## 
## 11776 samples
##    52 predictor
##     5 classes: 'A', 'B', 'C', 'D', 'E' 
## 
## No pre-processing
## Resampling: Cross-Validated (5 fold) 
## Summary of sample sizes: 9420, 9422, 9421, 9421, 9420 
## Resampling results across tuning parameters:
## 
##   mtry  Accuracy   Kappa    
##    2    0.9882807  0.9851730
##   27    0.9900643  0.9874316
##   52    0.9790255  0.9734660
## 
## Accuracy was used to select the optimal model using the largest value.
## The final value used for the model was mtry = 27.
```

The best model selected (mtry = 27) reaches a very good accuracy rate of 0.99. It could be a good candidate for our purpose.

#### Prediction with random forest:


```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 2231   11    0    0    0
##          B    1 1498    4    4    0
##          C    0    8 1362   23    5
##          D    0    1    2 1259    5
##          E    0    0    0    0 1432
## 
## Overall Statistics
##                                           
##                Accuracy : 0.9918          
##                  95% CI : (0.9896, 0.9937)
##     No Information Rate : 0.2845          
##     P-Value [Acc > NIR] : < 2.2e-16       
##                                           
##                   Kappa : 0.9897          
##                                           
##  Mcnemar's Test P-Value : NA              
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9996   0.9868   0.9956   0.9790   0.9931
## Specificity            0.9980   0.9986   0.9944   0.9988   1.0000
## Pos Pred Value         0.9951   0.9940   0.9742   0.9937   1.0000
## Neg Pred Value         0.9998   0.9968   0.9991   0.9959   0.9984
## Prevalence             0.2845   0.1935   0.1744   0.1639   0.1838
## Detection Rate         0.2843   0.1909   0.1736   0.1605   0.1825
## Detection Prevalence   0.2858   0.1921   0.1782   0.1615   0.1825
## Balanced Accuracy      0.9988   0.9927   0.9950   0.9889   0.9965
```

The overall statistic attains a 0.9922 level, confirming the cross validation choice.

### Generalized Boosted regression model




```
## Iter   TrainDeviance   ValidDeviance   StepSize   Improve
##      1        1.6094             nan     0.1000    0.2433
##      2        1.4579             nan     0.1000    0.1592
##      3        1.3567             nan     0.1000    0.1267
##      4        1.2755             nan     0.1000    0.1074
##      5        1.2091             nan     0.1000    0.0878
##      6        1.1544             nan     0.1000    0.0819
##      7        1.1031             nan     0.1000    0.0683
##      8        1.0589             nan     0.1000    0.0575
##      9        1.0217             nan     0.1000    0.0530
##     10        0.9866             nan     0.1000    0.0571
##     20        0.7502             nan     0.1000    0.0206
##     40        0.5267             nan     0.1000    0.0109
##     60        0.4026             nan     0.1000    0.0065
##     80        0.3201             nan     0.1000    0.0039
##    100        0.2647             nan     0.1000    0.0024
##    120        0.2207             nan     0.1000    0.0030
##    140        0.1861             nan     0.1000    0.0013
##    150        0.1723             nan     0.1000    0.0016
```



#### Selection of the best model for generalized boosted regression model


```
## Stochastic Gradient Boosting 
## 
## 11776 samples
##    52 predictor
##     5 classes: 'A', 'B', 'C', 'D', 'E' 
## 
## No pre-processing
## Resampling: Cross-Validated (5 fold) 
## Summary of sample sizes: 9421, 9421, 9421, 9420, 9421 
## Resampling results across tuning parameters:
## 
##   interaction.depth  n.trees  Accuracy   Kappa    
##   1                   50      0.7476228  0.6798132
##   1                  100      0.8192084  0.7711347
##   1                  150      0.8531759  0.8141985
##   2                   50      0.8559782  0.8175171
##   2                  100      0.9062501  0.8813530
##   2                  150      0.9301118  0.9115677
##   3                   50      0.8963140  0.8687250
##   3                  100      0.9398776  0.9239204
##   3                  150      0.9580502  0.9469232
## 
## Tuning parameter 'shrinkage' was held constant at a value of 0.1
## 
## Tuning parameter 'n.minobsinnode' was held constant at a value of 10
## Accuracy was used to select the optimal model using the largest value.
## The final values used for the model were n.trees = 150, interaction.depth =
##  3, shrinkage = 0.1 and n.minobsinnode = 10.
```

The best model for this method is the n.trees 150 with a 0.93 accuracy.

#### Prediction with GBM


```
## Confusion Matrix and Statistics
## 
##           Reference
## Prediction    A    B    C    D    E
##          A 2209   59    0    2    5
##          B   16 1407   36    8   17
##          C    3   49 1317   50   15
##          D    2    2   12 1212   21
##          E    2    1    3   14 1384
## 
## Overall Statistics
##                                          
##                Accuracy : 0.9596         
##                  95% CI : (0.955, 0.9638)
##     No Information Rate : 0.2845         
##     P-Value [Acc > NIR] : < 2.2e-16      
##                                          
##                   Kappa : 0.9489         
##                                          
##  Mcnemar's Test P-Value : 2.62e-13       
## 
## Statistics by Class:
## 
##                      Class: A Class: B Class: C Class: D Class: E
## Sensitivity            0.9897   0.9269   0.9627   0.9425   0.9598
## Specificity            0.9882   0.9878   0.9819   0.9944   0.9969
## Pos Pred Value         0.9710   0.9481   0.9184   0.9704   0.9858
## Neg Pred Value         0.9959   0.9826   0.9920   0.9888   0.9910
## Prevalence             0.2845   0.1935   0.1744   0.1639   0.1838
## Detection Rate         0.2815   0.1793   0.1679   0.1545   0.1764
## Detection Prevalence   0.2900   0.1891   0.1828   0.1592   0.1789
## Balanced Accuracy      0.9890   0.9574   0.9723   0.9684   0.9783
```

With an **accuracy of 0.96**, this model is a good candidate but remains **below the Random Forest** one.

## In sample error rate:

The in-sample error rate measures the error rate when we predict the output from the sample and compare them with the actual values. Let's calculate this rate for our 3 models:

-   Regression trees:


```
## [1] 0.4663523
```

-   GBM:


```
## [1] 0.04040275
```

-   Random Forest:


```
## [1] 0.008157023
```

The **Random Forest model** has the **lower in-sample expected error rate**. So it seems to be the most appropriate model to predict the *classe* output among the 3 that we tested here.

# Conclusion

Now that we identified the Random Forest Model as the more accurate, we will use it to predict the output on the test cases, but this will not be displayed in this document.
