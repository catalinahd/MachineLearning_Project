---
title: "Practical Machine Learning Project"
author: "Catalina Herrera"
date: "June 5, 2016"
output: html_document
---




# Background and Introduction

Using devices such as Jawbone Up, Nike FuelBand, and Fitbit is now possible to collect a large amount of data about personal activity relatively inexpensively. These type of devices are part of the quantified self movement – a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. One thing that people regularly do is quantify how much of a particular activity they do, but they rarely quantify how well they do it.

In this project, we will be to use data from accelerometers on the belt, forearm, arm, and dumbell of 6 participant They were asked to perform barbell lifts correctly and incorrectly in 5 different ways. The five ways are exactly according to the specification (Class A), throwing the elbows to the front (Class B), lifting the dumbbell only halfway (Class C), lowering the dumbbell only halfway (Class D) and throwing the hips to the front (Class E). Only Class A corresponds to correct performance. The goal of this project is to predict the manner in which they did the exercise, i.e., Class A to E. More information is available from the website here: http://groupware.les.inf.puc-rio.br/har (see the section on the Weight Lifting Exercise Dataset).


## Import the data from the URLs

```
trainUrl <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-training.csv"
training <- source_data(trainurl, na.strings = c("NA", "#DIV/0!", ""), header = TRUE)
testUrl <- "https://d396qusza40orc.cloudfront.net/predmachlearn/pml-testing.csv"
testing <- source_data(testurl, na.strings = c("NA", "#DIV/0!", ""), header = TRUE)
```
##Load data to memory

```
 training <- read.csv(url(trainUrl), na.strings=c("NA","#DIV/0!",""))
 testing <- read.csv(url(testUrl), na.strings=c("NA","#DIV/0!",""))
```
##Clean Data
 
```
 training <- training[, colSums(is.na(training)) == 0]
 testing <- testing[, colSums(is.na(testing)) == 0]
 
```

##Calculate correlations

```
 cor <- abs(sapply(colnames(training[, -ncol(training)]), function(x) cor(as.numeric(training[, x]), as.numeric(training$classe), method = "spearman")))
 
 trainData<-training
 testData<-testing
```

##Partioning Training data set into two data sets, 70% for myTraining, 30% for myTesting

```
set.seed(3344) 
 inTrain <- createDataPartition(trainData$classe, p = 0.7, list = FALSE)
 train <- trainData[inTrain, ]
 valid <- trainData[-inTrain, ]
 dim(train); dim(valid)
```
#Fisrt Model
##Classification trees
 
```
control <- trainControl(method = "cv", number = 5)
 fit_rpart <- train(classe ~ ., data = train, method = "rpart", 
                    trControl = control)
 print(fit_rpart, digits = 4)
```



####Predict outcomes using validation set
```
predict_rpart <- predict(fit_rpart, valid)
```
 
####Show prediction result
```
(conf_rpart <- confusionMatrix(valid$classe, predict_rpart))
 fancyRpartPlot(fit_rpart$finalModel)
```
 

####Accuracy Classification trees
```
(accuracy_rpart <- co
nf_rpart$overall[1])
```
 Accuracy 
0.6618522


#Second Model
##Random Forest 
 ```
fit_rf <- train(classe ~ ., data = train, method = "rf", 
                 trControl = control)
 print(fit_rf, digits = 4)
```

####Predict outcomes using validation set
 ```
predict_rf <- predict(fit_rf, valid)
```
 

####Show prediction result
 ```
 (conf_rf <- confusionMatrix(valid$classe, predict_rf))
```

 
####Accuracy Random forests
 ```
 (accuracy_rf <- conf_rf$overall[1])
```

Accuracy 
0.9998301 
 
#Conclusion
 Random Forests yielded better Results.
 Accuracy ~99% may be due to the fact that many 
 predictors are highly correlated. Random forests 
 chooses a subset of predictors at each split and 
 decorrelate the trees. 
 
#Final Model and Prediction
 ```
 (predict(fit_rf, testData)) 
```
 
###Results:
[1] A A A A A A A A A A A A A A A A A A A A
Levels: A B C D E

