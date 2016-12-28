#Getting-and-Cleaning-Data-Course-Project code book
#author: Yerzhan Kadyrsizov

#Introduction
The purpose of this project is to demonstrate your ability to collect, work with, and clean a data set. The goal is to prepare tidy data that can be used for later analysis. 

#Source data:
A full description of the data used in this project can be found at https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip

The script run_analysis.R performs the 5 steps described in the course project's definition.

#1.Merges the training and the test sets to create one data set

features <- read.table("UCI HAR Dataset/features.txt")
features[,2] <- as.character(features[,2])
filter <- grep(".*mean.*|.*std.*", features[,2])
filter.names <- features[filter,2]
filter.names = gsub('-mean', 'Mean', filter.names)
filter.names = gsub('-std', 'Std', filter.names)
filter.names <- gsub('[-()]', '', filter.names)
x_train <- read.table("UCI HAR Dataset/train/X_train.txt")[filter]
x_test <- read.table("UCI HAR Dataset/test/X_test.txt")[filter]
x_data <- rbind(x_train, x_test)

#2. Extracts only the measurements on the mean and standard deviation for each measurement


y_train <- read.table("UCI HAR Dataset/train/y_train.txt")
y_test <- read.table("UCI HAR Dataset/test/y_test.txt")
y_data <- rbind(y_train, y_test)
activities <- read.table("UCI HAR Dataset/activity_labels.txt")
y_data[, 1] <- activities[y_data[, 1], 2]
names(y_data) <- "activity"


#3.Uses descriptive activity names to name the activities in the data set


subject_train <- read.table("UCI HAR Dataset/train/subject_train.txt")
subject_test <- read.table("UCI HAR Dataset/test/subject_test.txt")
subject_data <- rbind(subject_train, subject_test)
names(subject_data) <- "subject"


#4. Appropriately labels the data set with descriptive variable names


combined_data <- cbind(subject_data, y_data, x_data)
colnames(combined_data) <- c("subject", "activity", filter.names)


#5. Creates a second,independent tidy data set and ouput it


tidy_data <- ddply(combined_data, .(subject, activity), function(x) colMeans(x[, 3:68]))
write.table(tidy_data, "tidy_data.txt", row.name=FALSE)


#Variables
x_train, y_train, x_test, y_test, subject_train and subject_test contain the data from the downloaded files.
x_data, y_data and subject_data merge the previous datasets to further analysis.
features contains the correct names for the x_data dataset, which are applied to the column names stored in mean_and_std_features, a numeric vector used to extract the desired data.
A similar approach is taken with activity names through the activities variable.
all_data merges x_data, y_data and subject_data in a big dataset.
Finally, averages_data contains the relevant averages which will be later stored in a .txt file. ddply() from the plyr package is used to apply colMeans() and ease the development.

