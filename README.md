# Getting-and-Cleaning-Data

Getting and Cleaning Data Final Project

You should create one R script called run_analysis.R that does the following. 
1. Merges the training and the test sets to create one data set.
2. Extracts only the measurements on the mean and standard deviation for each measurement. 
3. Uses descriptive activity names to name the activities in the data set
4. Appropriately labels the data set with descriptive variable names. 
5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

Download and unzip the file and put the file in the data folder.

> if(!file.exists("./data")){dir.create("./data")}
> fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
> data<-file.path(getwd(), "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip")
> unzip(zipfile="./data/Dataset.zip",exdir="./data")
> path_rf <- file.path("./data/getdata-projectfiles-UCI HAR Dataset" , "UCI HAR Dataset")
> files<-list.files(path_rf, recursive=TRUE)
> files

Read the activity files
> dataActivityTest  <- read.table(file.path(path_rf, "test" , "Y_test.txt" ),header = FALSE)
> dataActivityTrain <- read.table(file.path(path_rf, "train", "Y_train.txt"),header = FALSE)

Read the subject files
> dataSubjectTrain <- read.table(file.path(path_rf, "train", "subject_train.txt"),header = FALSE)
> dataSubjectTest  <- read.table(file.path(path_rf, "test" , "subject_test.txt"),header = FALSE)

Read the features files
> dataFeaturesTest  <- read.table(file.path(path_rf, "test" , "X_test.txt" ),header = FALSE)
> dataFeaturesTrain <- read.table(file.path(path_rf, "train", "X_train.txt"),header = FALSE)

Look at the properties of the variables
> str(dataActivityTest)
> str(dataActivityTrain)
> str(dataSubjectTrain)
> str(dataSubjectTest)
> str(dataFeaturesTest)
> str(dataFeaturesTrain)

Concatenates the data tables by rows
> dataSubject <- rbind(dataSubjectTrain, dataSubjectTest)
> dataActivity<- rbind(dataActivityTrain, dataActivityTest)
> dataFeatures<- rbind(dataFeaturesTrain, dataFeaturesTest)

Set names to variables
> names(dataSubject)<-c("subject")
> names(dataActivity)<- c("activity")
> dataFeaturesNames <- read.table(file.path(path_rf, "features.txt"),head=FALSE)
> names(dataFeatures)<- dataFeaturesNames$V2

Merge columns to get the data frame Data for all data
> dataCombine <- cbind(dataSubject, dataActivity)
> Data <- cbind(dataFeatures, dataCombine)

Subset name and data frame of features by measurements on the mean and stardard deviation
> subdataFeaturesNames<-dataFeaturesNames$V2[grep("mean\\(\\)|std\\(\\)", dataFeaturesNames$V2)]
> selectedNames<-c(as.character(subdataFeaturesNames), "subject", "activity" )
> Data<-subset(Data,select=selectedNames)
> str(Data)

Read descriptive names from activity_lables.txt
> activityLabels <- read.table(file.path(path_rf, "activity_labels.txt"),header = FALSE)
> head(Data$activity,30)

Label using descriptive variable names 
> names(Data)<-gsub("^t", "time", names(Data))
> names(Data)<-gsub("^f", "frequency", names(Data))
> names(Data)<-gsub("Acc", "Accelerometer", names(Data))
> names(Data)<-gsub("Gyro", "Gyroscope", names(Data))
> names(Data)<-gsub("Mag", "Magnitude", names(Data))
> names(Data)<-gsub("BodyBody", "Body", names(Data))
> names(Data)

5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
> library(plyr);
> Data2<-aggregate(. ~subject + activity, Data, mean)
> Data2<-Data2[order(Data2$subject,Data2$activity),]
> write.table(Data2, file = "tidydata.txt",row.name=FALSE)




