# Getting-Cleaning-Data-Course-Project
# 1.Merges the training and the test sets to create one data set.
# 1.1 Read all data
X_test<-read.table("D:/Coursera courses/Getting and Cleaning Data/Course Project/UCI HAR Dataset/test/X_test.txt")
dim(X_test) 

X_train<-read.table("D:/Coursera courses/Getting and Cleaning Data/Course Project/UCI HAR Dataset/train/X_train.txt")
dim(X_train) 

Y_test<-read.table("D:/Coursera courses/Getting and Cleaning Data/Course Project/UCI HAR Dataset/test/y_test.txt")
dim(Y_test) 

Y_train<-read.table("D:/Coursera courses/Getting and Cleaning Data/Course Project/UCI HAR Dataset/train/y_train.txt")
dim(Y_train) 

subject_test<-read.table("D:/Coursera courses/Getting and Cleaning Data/Course Project/UCI HAR Dataset/test/subject_test.txt")
dim(subject_test) 

subject_train<-read.table("D:/Coursera courses/Getting and Cleaning Data/Course Project/UCI HAR Dataset/train/subject_train.txt")
dim(subject_train) 

activity_labels<-read.table("D:/Coursera courses/Getting and Cleaning Data/Course Project/UCI HAR Dataset/activity_labels.txt")
dim(activity_labels) 

features<-read.table("D:/Coursera courses/Getting and Cleaning Data/Course Project/UCI HAR Dataset/features.txt")
dim(features) 

#1.2 Combine all the data and attach variable name
X_data<-rbind(X_test, X_train)
colnames(X_data) <- t(features[2])

Y_data<-rbind(Y_test, Y_train)
names(Y_data)[1]<-"Activity"

Sub_data<-rbind(subject_test, subject_train)
names(Sub_data)[1]<-"Subject"


#1.3 Create one data set and named XY_data
All_data<-cbind(Sub_data, X_data, Y_data)
dim(All_data)
names(All_data)

# 2.Extracts only the measurements on the mean and standard deviation for each measurement. 
# 2.1 Subset the variables which name containing mean()
MeanVar_AllData<-grep("mean()", names(All_data), value=TRUE, fixed=TRUE)
MeanData<-subset(All_data,select=MeanVar_AllData)
dim(MeanData) #10299 33

# 2.2 Subset the variables which name containing std()
StdVar_AllData<-grep("std()", names(All_data), value=TRUE, fixed=TRUE)
StdData<-subset(All_data,select=StdVar_AllData)
dim(StdData) #10299 33

# 2.3 Combine mean and Std datasets
MeanStd_AllData<- cbind(MeanData, StdData)
dim(MeanStd_AllData) #10299 66


# 3.Uses descriptive activity names to name the activities in the data set
All_data$Activity<-activity_labels[match(All_data$Activity, activity_labels$V1), 'V2']

# 4.Appropriately labels the data set with descriptive variable names. 
names(All_data)<-gsub("^t", "time", names(All_data))
names(All_data)<-gsub("^f", "frequency", names(All_data))
names(All_data)<-gsub("Acc", "Accelerator", fixed = TRUE, names(All_data))
names(All_data)<-gsub("Gyro", "Gyroscope", fixed = TRUE, names(All_data))
names(All_data)<-gsub("Mag", "Magnitude", fixed=TRUE, names(All_data))

# 5.From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.
library(plyr)
dim(All_data) #10299 563
All_data2<-aggregate(. ~Subject + Activity, All_data, mean)
All_data2<-All_data[order(All_data2$Subject,All_data2$Activity),]

View(All_data2)
dim(All_data2) #180 563

write.table(All_data2, file = "tidydata.txt",row.name=FALSE)
