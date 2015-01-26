---
title: "Getting & Cleaning data Project"
output: html_document
---

Assigning working directory and loading required libraries


```r
wd <- "/Users/krenganthan/Desktop/Coursera/Getting & Cleaning Data"
setwd(wd)

library(plyr)
library(dplyr)
```
# Merging Test & Tain data

* Reading training/test data sets
* Column binding three sets - Subject,Activity,561 feature vector
* Using features.txt to name variables
* Merging Test & Train data sets
* Cleaning up column names


```r
# Reading Test data components and collating in to data frame

X_test <- read.table("./data/UCI HAR Dataset/test/X_test.txt")
y_test <- read.table("./data/UCI HAR Dataset/test/y_test.txt")
subject_test <- read.table("./data/UCI HAR Dataset/test/subject_test.txt")
test <- cbind(subject_test,y_test,X_test)

# Assigning labels

features <- read.table("./data/UCI HAR Dataset/features.txt")
colnames(test) <- c("Subject","Activity")
for(i in 3:563){
  temp <- as.character(features[i-2,2])
  colnames(test)[i] <- c(temp)
}

# Reading train data components and collating in to data frame

X_train <- read.table("./data/UCI HAR Dataset/train/X_train.txt")
y_train <- read.table("./data/UCI HAR Dataset/train/y_train.txt")
subject_train <- read.table("./data/UCI HAR Dataset/train/subject_train.txt")
train <- cbind(subject_train,y_train,X_train)

# Assigning labels

colnames(train) <- c("Subject","Activity")
for(i in 3:563){
  temp <- as.character(features[i-2,2])
  colnames(train)[i] <- c(temp)
}

# Merging Test & Train

all_subjects <- rbind(test,train)

#Make syntactically valid names

names_changed <- make.names(names(all_subjects))
colnames(all_subjects) <- make.names(names_changed, unique=TRUE)
```

# Tidy data output

* Extracting only columns with mean or std measure
  + function contains with condition (mean or Mean or std) is applied to subset columns
* Assigining Descriptive Activity names
* Computing mean for all extracted variabled for each Subject and Activities
* Write the output to a text file


```r
#Extract
all_subj_mean_std <- select(all_subjects,Subject,Activity,contains("mean"),contains("std"),contains("Mean"))

# Assign activity names
all_subj_mean_std$Activity <- as.factor(all_subj_mean_std$Activity)
all_subj_mean_std$Activity <- factor(all_subj_mean_std$Activity
                    ,levels = c(1,2,3,4,5,6)
                    ,labels = c("WALKING", "WALKING_UPSTAIRS", "WALKING_DOWNSTAIRS",
                                "SITTING","STANDING","LAYING"))

# Compute means
subj_activity_mean <- all_subj_mean_std %>% group_by(Subject,Activity) %>% summarise_each(funs(mean))

# Write output
write.table(subj_activity_mean,"subj_activity_mean.txt",row.names = FALSE)
```
