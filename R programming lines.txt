#import reshape. This function reshapes a data frame between ‘wide’ format with repeated measurements in separate columns of the same record and ‘long’ format with the repeated measurements in separate records.

library(reshape2)
#import datasets
actlabel <- read.table("getdata_projectfiles_UCI HAR Dataset/UCI HAR Dataset/activity_labels.txt")
actlabel [,2] <- as.character(actlabel[,2])
features <- read.table("getdata_projectfiles_UCI HAR Dataset/UCI HAR Dataset/features.txt")
features[,2] <- as.character(features[,2])

wantedf <- grep(".*mean.*|.*std.*", features[,2])
wantedf.names <- features[wantedf,2]
wantedf.names = gsub('-mean', 'Mean', wantedf.names)
wantedf.names = gsub('-std', 'Std', wantedf.names)
wantedf.names <- gsub('[-()]', '', wantedf.names)


# Import more datasets
train <- read.table("getdata_projectfiles_UCI HAR Dataset/UCI HAR Dataset/train/X_train.txt")[featuresWanted]
trainact <- read.table("getdata_projectfiles_UCI HAR Dataset/UCI HAR Dataset/train/Y_train.txt")
trainsub<- read.table("getdata_projectfiles_UCI HAR Dataset/UCI HAR Dataset/train/subject_train.txt")
train <- cbind(trainsub, trainact, train)

test <- read.table("getdata_projectfiles_UCI HAR Dataset/UCI HAR Dataset/test/X_test.txt")[featuresWanted]
testact <- read.table("getdata_projectfiles_UCI HAR Dataset/UCI HAR Dataset/test/Y_test.txt")
testsub <- read.table("getdata_projectfiles_UCI HAR Dataset/UCI HAR Dataset/test/subject_test.txt")
test <- cbind(testsub, testact, test)

# merge datasets and add labels
allData <- rbind(train, test)
colnames(allData) <- c("subject", "activity", featuresWanted.names)

# convert activities and  subjects into factors
allData$activity <- factor(allData$activity, levels = actlabel[,1], labels = actlabel[,2])
allData$subject <- as.factor(allData$subject)

allData.melted <- melt(allData, id = c("subject", "activity"))
allData.mean <- dcast(allData.melted, subject + activity ~ variable, mean)#export the result of merged datasets into a text file
write.table(allData.mean, "tidy.txt", row.names = FALSE, quote = FALSE)
