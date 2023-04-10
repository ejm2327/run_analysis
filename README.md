# run_analysis
#Download the data from the web and saves it in the working directory as HARdataset.zip

  fileURL <- "https://d396qusza40orc.cloudfront.net  /getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
  download.file(fileURL, destfile = "HARdataset.zip")

#unzip the downloaded data set

  unzip("HARdataset.zip")

#Reading the subject information from subject_train.txt and subject_test.txt files and assigning them to variables subjtrain and subjtest. Both variables are data.frame objects with one column holding the ID nubers of the subjects participating in the study.

  subjtrain <- read.table("UCI HAR Dataset/train/subject_train.txt", col  .names = "subject")
  subjtest <- read.table("UCI HAR Dataset/test/subject_test.txt", col.names = "subject")

#Reading the activity information from y_train.txt and y_test.txt files and assigning them to variables activitytrain and activitytest. Both variables are data.frame objects with one column holding the index number of the activities.

  activitytrain <- read.table("UCI HAR Dataset/train/y_train.txt", col.names = "activity")
  activitytest <- read.table("UCI HAR Dataset/test/y_test.txt", col.names = "activity")

#Reading the activity information from X_train.txt and X_test.txt files and assigning them to variables datatrain and datatest. Both are data.frame objects with 561 columns holding the measurments data.

  datatrain <- read.table("UCI HAR Dataset/train/X_train.txt")
  datatest <- read.table("UCI HAR Dataset/test/X_test.txt")
  
#Binding both datatrain and datatest dataframes in one dataframe called alldata.

  alldata <- rbind(datatrain, datatest)  
  
#Reading the features.txt file which contains information about the measurements taken (names of the measurements) and stores the data in features data.frame with has 2 columns the first is the number of the measurement and the second the name of the measurement.

  features <- read.table("UCI HAR Dataset/features.txt")  
  
#Assigns the measurement names from features as column names of alldata.

  names(alldata) <- features$V2
  
#Extracts only the measurements on the mean and standard deviation for each measurement.

  extrmeansd <- c(grep("mean\\(",names(alldata)),grep("std\\(", names(alldata)))
  extrmeansd <- extrmeansd[order(extrmeansd)]
  extracteddata <- alldata[,extrmeansd]

3. Uses descriptive activity names to name the activities in the data set

allsubjects <- rbind(subjtrain, subjtest)
allactivity <- rbind(activitytrain, activitytest)
extracteddata <- cbind(alldata[,extrmeansd], allsubjects, allactivity)
actlabels <- read.table("UCI HAR Dataset/activity_labels.txt")
extracteddata$activity <- factor(extracteddata$activity, labels = actlabels$V2)

4. Appropriately labels the data set with descriptive variable names.

The variables are already labeled in step 1.

5. From the data set in step 4, creates a second, independent tidy data set with the average of each variable for each activity and each subject.

library(data.table)
dtalldata <- data.table(extracteddata)
tidydata <- dtalldata[,lapply(.SD,mean), by = .(activity, subject)]
write.table(tidydata, file = "tidydata.txt", row.names = FALSE)
