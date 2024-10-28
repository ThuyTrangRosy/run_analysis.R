# run_analysis.R. This is the code to do with project
baseDir <- "~/Documents/coursera/getting-and-cleaning-data/assessment-tidy-data"
setwd(baseDir)
# TODO: exit if we cannot set this working directory

# download the source and unzip into a data directory
dataSrc <- "https://d396qusza40orc.cloudfront.net/getdata%2Fprojectfiles%2FUCI%20HAR%20Dataset.zip"
dataDir <- "data"
zipFile <- "Dataset.zip"

if (!file.exists(dataDir)) {
	dir.create(dataDir)
}
setwd(dataDir)

# TODO: skip download step if already done and identical
download.file(dataSrc, destfile = zipFile, method = "curl")
date()

list.files(pattern = zipFile)
unzip(zipFile)

# TODO: check if the subdir really exists
# something like if (file.access("data/UCI HAR Dataset"))  ...
dataBaseDir <- "UCI HAR Dataset"

setwd(baseDir)
testDataDir <- paste(dataDir, "/", dataBaseDir, "/test", sep = "")
trainingDataDir <- paste(dataDir, "/", dataBaseDir, "/train", sep = "")
# 1.1 - get column names
colNamesRaw <- read.table(paste(dataDir, "/", dataBaseDir, "/features.txt", sep = ""))
# should be: 561 2
dim(colNamesRaw)
# remove first column and convert to a vector by transposing with t()
colNames <- t(colNamesRaw[-1])
# rm(colNamesRaw)
# 1.2 - merge *within* training
train1 <- read.table(paste(trainingDataDir, "/", "X_train.txt", sep = ""))
# check to see if it is what we wanted, should return "[1] 7352  561"
dim(train1)

# train1 <- read.table(paste(trainingDataDir, "/", "X_train.txt", sep=""), col.names=colNames)
# ane have meaningful column names
# head(names(train1))
# "-", "(" or ")" gets replaced in column names as "."

train1subject <- read.table(paste(trainingDataDir, "/", "subject_train.txt", sep = ""))
# should have: "[1] 7352    1"
dim(train1subject)

train1activity <- read.table(paste(trainingDataDir, "/", "y_train.txt", sep = ""))
# should have: "[1] 7352    1"
dim(train1activity)
train <- cbind(train1, train1subject, train1activity)
# should extend train1 with two columns and return "[1] 7352  563"
dim(train)
# 1.3 - merge *within* test
test1 <- read.table(paste(testDataDir, "/", "X_test.txt", sep = ""))
# check to see if it is what we wanted, should return "[1] 2947  561"
dim(test1)

# test1 <- read.table(paste(testDataDir, "/", "X_test.txt", sep=""), col.names=colNames)
# ane have meaningful column names
# head(names(test1))
# "-", "(" or ")" gets replaced in column names as "."

test1subject <- read.table(paste(testDataDir, "/", "subject_test.txt", sep = ""))
# should have: "[1] 2947    1"
dim(test1subject)

test1activity <- read.table(paste(testDataDir, "/", "y_test.txt", sep = ""))
# should have: "[1] 2947    1"
dim(test1activity)

test <- cbind(test1, test1subject, test1activity)
# should extend test1 with two columns and return "[1] 2947  563"
dim(test)

####################################################################################
# 1.4 - merge training with test

data <- rbind(train, test)
# should return "[1] 10299   563"
dim(data)
usedCols <- colNamesRaw[which(grepl(pattern = "std\\(\\)|mean\\(\\)", x = colNamesRaw[, 2])), ]
# should return 66 rows in 2 cols: "[1] 66  2"
dim(usedCols)

# now using column 1 of usedCols as index into the data
# but add the subject and activity columns (562 and 563) since we need them later
data2 <- data[, c(usedCols[, 1], 562, 563)]
# we should now have only 68 cols (66 usedCols + subject label + activity label)
# with the same number of rows: [1] 10299    68
dim(data2)
