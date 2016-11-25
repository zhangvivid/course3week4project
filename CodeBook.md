
## original train dataset
train_x <- read.table("UCI HAR Dataset/train/X_train.txt")
train_y <- read.table("UCI HAR Dataset/train/Y_train.txt")
train_sub <- read.table("UCI HAR Dataset/train/subject_train.txt")

## original test dataset
test_x <- read.table("UCI HAR Dataset/test/X_test.txt")
test_y <- read.table("UCI HAR Dataset/test/Y_test.txt")
test_sub <- read.table("UCI HAR Dataset/test/subject_test.txt")

## activity data
activity <- read.table("UCI HAR Dataset/activity_labels.txt")
## assign descriptive name to activity data
names(activity) <- c("activity_code","activity_name")

## load features labels
feature <- read.table("UCI HAR Dataset/features.txt")
## assign descriptive name to feature data
names(feature) <- c("feature_code","feature_name")

## rename col names of train and test data set
names(train_x) <- feature$feature_name
names(test_x) <- feature$feature_name

names(train_y)<- "activity_code"
names(test_y)<- "activity_code"

names(train_sub) <- "subject"
names(test_sub) <- "subject"

## select only needed feature
feature_sel <- grep("-(mean|std)\\(\\)", feature[, 2])

## filter out train and test data of x, for the features needed. 
train_x2 <- train_x[,feature_sel]
test_x2 <- test_x[,feature_sel]

## create the train and test dataset, with subject in the first col, activity type in second col, and features selected in the remaining col
dt_train <- cbind(train_sub, train_y, train_x2)
dt_test <- cbind(test_sub, test_y, test_x2)

## combine the train and test dataset
dt_comb <- rbind(dt_train,dt_test)

## create a new dataset with the average of each variable for each acvitity type and each subject. 
library(dplyr)
## group the data by subject and activity code
group_by_sub <- group_by(dt_comb,subject,activity_code)
## take the mean of each feature
dt_avg <- summarize_each(group_by_sub,funs(mean))
## save the data into a txt file
write.table(dt_avg, "dt_avg.txt", row.name=FALSE)
