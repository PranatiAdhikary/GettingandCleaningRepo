# Created a new working directory named as Sayanti

if(!file.exists("Sayanti")){
   dir.create("Sayanti)
}

# Downloaded the file from the given url and unzipped the file

fileUrl <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
download.file(fileUrl,destfile="./Sayanti/Dataset.zip")
unzip(zipfile="./Sayanti/Dataset.zip",exdir="./Sayanti")

# Merges the training and test data set to create one data set
# Reading train file
x_train <- read.table("./Sayanti/UCI HAR Dataset/train/X_train.txt")
y_train <- read.table("./Sayanti/UCI HAR Dataset/train/y_train.txt")
subject_train <- read.table("./Sayanti/UCI HAR Dataset/train/subject_train.txt")

# Reading test file
x_test <- read.table("./Sayanti/UCI HAR Dataset/test/X_test.txt")
y_test <- read.table("./Sayanti/UCI HAR Dataset/test/y_test.txt")
subject_test <- read.table("./Sayanti/UCI HAR Dataset/test/subject_test.txt")

# Reading features vectors
features <- read.table("./Sayanti/UCI HAR Dataset/features.txt")

# Reading activity labels
activityLabels <- read.table("./Sayanti/UCI HAR Dataset/activity_labels.txt")

# Assigning Column names
colnames(x_train) <- features[,2]
colnames(y_train) <- "activityId"
colnames(subject_train) <- "subjectId"
colnames(x_test) <- features[,2] 
colnames(y_test) <- "activityId"
colnames(subject_test) <- "subjectId"
colnames(activityLabels) <- c('activityId','activityType')

# 1.Merge the training and test data and created a new data set finaldataset
mrg_trainingData <- cbind(y_train,subject_train,x_train)
mrg_testingData <- cbind(y_test,subject_test,x_test)
finaldataset <- rbind(mrg_trainingData,mrg_testingData)

# Reading column names of the new data set finaldataset
colnames <- colnames(finaldataset)

# Create vector for defining ID, mean and standard deviation
mean_and_std <- (grepl("activityId",colnames)|grepl("subjectId",colnames)|grepl("mean",colnames)|grepl("std",colnames))

# 2.Created nessesary subset from finaldataset extracting only the measurements on the mean and standard deviation for each measurement:
setForMeanAndStd <- finaldataset[ , mean_and_std == TRUE]

# 3.Used descriptive activity names to name the activities in the data set
setWithActivityNames <- merge(setForMeanAndStd, activityLabels,
                              by='activityId',
                              all.x=TRUE)
# 4.Labels the data set with descriptive variable names and 5. Created a second independent tidy data set
secTidySet <- aggregate(. ~subjectId + activityId, setWithActivityNames, mean)
secTidySet <- secTidySet[order(secTidySet$subjectId, secTidySet$activityId),]

# Write the text file using write.table() command
write.table(secTidySet, "secTidySet.txt", row.name=FALSE)

