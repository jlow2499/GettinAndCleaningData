###load the plyr package
library(plyr)

###Merges the training and the test sets to create one data set.


##reads the train datasets
xtrain<-read.table("UCI HAR Dataset/train/X_train.txt")
ytrain<-read.table("UCI HAR Dataset/train/Y_train.txt")
subtrain<-read.table("UCI HAR Dataset/train/Subject_train.txt")

###reads the test datasets
xtest<-read.table("UCI HAR Dataset/test/X_test.txt")
ytest<-read.table("UCI HAR Dataset/test/Y_test.txt")
subtest<-read.table("UCI HAR Dataset/test/subject_test.txt")

###reads the activity labels and features
labels<-read.table("UCI HAR Dataset/activity_labels.txt",col.names=c("ActivityID","Activity"))
features<-read.table("UCI HAR Dataset/features.txt",colClasses=c("character"))

###binds the data sets
df<-rbind(cbind(cbind(xtrain,subtrain),ytrain),cbind(cbind(xtest,subtest),ytest))

###label columns
dflabels<-rbind(rbind(features, c(562, "Subject")), c(563, "ActivityID"))[,2]


###Appropriately labels the data set with descriptive variable names
###This is done here so that ActivityIDs can be matched with join
df<-setNames(df,t(dflabels))


###Extracts only the measurements on the mean and standard deviation for each measurement. 


df2<-df[,grep("mean|std|Subject|ActivityID",dflabels)]


###Uses descriptive activity names to name the activities in the data set


df2<-join(df2,labels,by="ActivityID",match="first")


###From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.


df3<-ddply(df2,c("Subject","Activity"),numcolwise(mean))


###Write the data set from step 5 as a text file


write.table(df3,file="tidy_data_set.txt")
