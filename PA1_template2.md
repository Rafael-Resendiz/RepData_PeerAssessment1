---
title: "Reproducible Research: Peer Assessment 1"
author: "Rafael Resendiz Ramirez"
date: "September 15, 2014"
output: html_document
---

This is an R Markdown document. Markdown is a simple formatting syntax for authoring HTML, PDF, and MS Word documents. For more details on using R Markdown see <http://rmarkdown.rstudio.com>.

When you click the **Knit** button a document will be generated that includes both content as well as the output of any embedded R code chunks within the document. You can embed an R code chunk like this:

## Introduction

It is now possible to collect a large amount of data about personal movement using activity monitoring devices such as a [Fitbit](http://www.fitbit.com), [Nike Fuelband](http://www.nike.com/us/en_us/c/nikeplus-fuelband), or [Jawbone Up](https://jawbone.com/up). These type of devices are part of the "quantified self" movement -- a group of enthusiasts who take measurements about themselves regularly to improve their health, to find patterns in their behavior, or because they are tech geeks. But these data remain under-utilized both because the raw data are hard to obtain and there is a lack of statistical methods and software for processing and interpreting the data.

This assignment makes use of data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

## Data

The data for this assignment can be downloaded from the course web site:

* **Dataset**: [Activity monitoring data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip) [52K]  

The variables included in this dataset are:

* **steps**: Number of steps taking in a 5-minute interval (missing values are coded as NA)

* **date**: The date on which the measurement was taken in YYYY-MM-DD format

* **interval**: Identifier for the 5-minute interval in which measurement was taken

The dataset is stored in a comma-separated-value (CSV) file and there are a total of 17,568 observations in this dataset.

## Assignment

This assignment will be described in multiple parts. You will need to write a report that answers the questions detailed below. Ultimately, you will need to complete the entire assignment in a **single R markdown** document that can be processed by knitr and be transformed into an HTML file.

Throughout your report make sure you always include the code that you used to generate the output you present. When writing code chunks in the R markdown document, always use echo = TRUE so that someone else will be able to read the code. **This assignment will be evaluated via peer assessment so it is essential that your peer evaluators be able to review the code for your analysis**.

For the plotting aspects of this assignment, feel free to use any plotting system in R (i.e., base, lattice, ggplot2)

Fork/clone the GitHub repository created for this assignment. You will submit this assignment by pushing your completed files into your forked repository on GitHub. The assignment submission will consist of the URL to your GitHub repository and the SHA-1 commit ID for your repository state.

NOTE: The GitHub repository also contains the dataset for the assignment so you do not have to download the data separately.

## Loading and preprocessing the data

Show any code that is needed to

1. Load the data (i.e. read.csv())

2. Process/transform the data (if necessary) into a format suitable for your analysis

### Download a file from the web
```{r}
fileUrl <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip?accessType=DOWNLOAD"
download.file(fileUrl, destfile = "/Users/administrador/Specialization/Activity_monitoring_data.zip", method = "curl")
```

### unzip the file
```{r}
unzip("/Users/administrador/Specialization/Activity_monitoring_data.zip")
```


### Read the file
```{r}
activity <- read.table("/Users/administrador/Specialization/activity.csv", sep = ",", header = TRUE)
```

### Show the head file
```{r}
head(activity)
```

           steps           date         interval    
       1     NA         2012-10-01        0
       2     NA         2012-10-01        5
       3     NA         2012-10-01       10
       4     NA         2012-10-01       15
       5     NA         2012-10-01       20
       6     NA         2012-10-01       25

### Stream the databases
```{r}
str(activity)
```
                 'data.frame':    17568 obs. of  3 variables:
             $ steps   : int  NA NA NA NA NA NA NA NA NA NA ...
              $ date    : Factor w/ 61 levels "2012-10-01","2012-10-02",..: 1 1 1 1 1 1 1 1 1 1 ...
             $ interval: int  0 5 10 15 20 25 30 35 40 45 ...


## What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

1. Make a histogram of the total number of steps taken each day

```{r}
totalNumberSteps <- xtabs(steps ~ date,activity)
    mean(totalNumberSteps)
    sum(activity$steps, na.rm=TRUE)/length(levels(activity$date)) 
```
             [1] 9354

```{r, echo=FALSE}
plot(totalNumberSteps, main="Total number of steps taken each day")
```

![Sample panel plot](figures2/Steps_by_day.png) 


2. Calculate and report the **mean** and **median** total number of steps taken per day
```{r}
average <- xtabs(activity$steps ~ activity$interval)/xtabs(~ activity$interval)
    mean(as.numeric(average))  
```
             [1] 32.48

    
```{r}    
    median(as.numeric(average))
```
             [1] 29.64

## What is the average daily activity pattern?

1. Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```{r, echo=FALSE}
library(lattice)
barchart(average ~ names(average),xlab="24 hours in the day",
         ylab="Average number of steps in intervals of every 5 minutes",
         ylim=c(0,205.),scales=list(x=list(at=seq(0,288,by=12),labels=c(0:24))),
         main = "Daily Activity Patern")
```
![Sample panel plot](figures2/Daily_Activity_Pattern.png) 


2. Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?
```{r} 
# average2 <- arrange(average ~interval, by= at=seq(0,288,by=12))
maxstep <- max(xtabs(activity$steps ~ activity$interval)/xtabs(~ activity$interval))
maxstep
```

             [1] 179.1311


```{r} 
maxinterval <- xtabs(steps ~ interval,activity)
    mean(maxinterval)
 #   sum(activity$steps, na.rm=TRUE)/length(levels(activity$interval)) 

```
             [1] 1981.278

### Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

1. Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)


````{r} 
length(which(is.na(activity)==TRUE))
# or 
count2 <- complete.cases(activity)
length(which(count2==FALSE))
````
                      [1] 2304

2. Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.


````{r} 
naForRep <- mean(activity$steps, na.rm=TRUE)
naForRep

```

                      [1] 37.3826


3. Create a new dataset that is equal to the original dataset but with the missing data filled in.
```{r} 
activity2 <- activity
activity2[is.na(activity2)] <- naForRep
head(activity2)
```

               steps             date             interval
            1 37.3826            2012-10-01        0
            2 37.3826            2012-10-01        5
            3 37.3826            2012-10-01       10
            4 37.3826            2012-10-01       15
            5 37.3826            2012-10-01       20
            6 37.3826            2012-10-01       25

4. Make a histogram of the total number of steps taken each day and Calculate and report the **mean** and **median** total number of steps taken per day. 

The dayli activity pattern is defined by the interval

```{r pattern2}
     activity2 <- as.data.frame(activity2)
     average2 <- xtabs(activity2$steps ~ activity2$interval)/xtabs(~ activity2$interval)
```

Comparison
Do these values differ from the estimates from the first part of the assignment?
First assignment
```{r summarysumm1}
     summary(as.numeric(average))   
```
            Min.   1st Qu.  Median    Mean    3rd Qu.    Max. 
            0.00    2.16    29.64     32.48   45.91      179.10 

Second assignment
```{r summarysumm2}
     summary(as.numeric(average2))   
```
            Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
            4.903   7.062  34.540  37.380  50.810 184.000 

Comparison
What is the impact of imputing missing data on the estimates of the total daily number of steps?

First Plot
![Sample panel plot](figures/averageInterval.png) 

Second Plot 

```{r echo=FALSE, barplot9,fig.align='center',fig.height=4,fig.width=8}
library(lattice)
barchart(average2 ~ names(average2),
xlab="24 hours in the day",ylab="Average number of steps in intervals of every 5 minutes",
ylim=c(0,205.),scales=list(x=list(at=seq(0,288,by=12),labels=c(0:24))), 
main = "Daily Activity Patern with imputing missing data")
```
![Sample panel plot](figures2/Daily_Activity_Pattern_missingData.png) 

### Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

1. Create a new factor variable in the dataset with two levels -- "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

```{r}
activity2$wday <- factor(weekdays(as.Date(activity2$date)))
activity2$compday <- factor(activity2$wday)
levels(activity2$compday) <- c( "Weekdays","Weekdays", "Weekends", "Weekends", "Weekdays","Weekdays","Weekdays")
head(activity2)
```
                steps       date     interval  wday  compday
            1   37.3826   2012-10-01    0     lunes Weekends
            2   37.3826   2012-10-01    5     lunes Weekends
            3   37.3826   2012-10-01    10    lunes Weekends
            4   37.3826   2012-10-01    15    lunes Weekends
            5   37.3826   2012-10-01    20    lunes Weekends
            6   37.3826   2012-10-01    25    lunes Weekends



2. Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was created using **simulated data**:

First Plot
Plot by weekdays and weekends

```{r echo=FALSE, fig.align='center'}
averageIntervalDay <- xtabs(steps ~ interval+compday,activity2)/xtabs(~interval+compday,activity2)
dfrm2 <- as.data.frame(averageIntervalDay)
barchart(Freq ~ interval|compday,dfrm2,layout=c(1,2),strip=T,strip.left=F,
ylab="Average number of steps",xlab="Intervals of every 5 minutes by hours",
scales=list(x=list(at=seq(0,288,by=12),labels=c(0:24))),
main= "Differences in activity patterns between weekdays and weekends")
```
![Sample panel plot](figures2/Diff_weekdays_weekends.png) 


Second Plot
Plot by day
```{r echo=FALSE}
averageIntervalDay <- xtabs(steps ~ interval+wday,activity2)/xtabs(~interval+wday,activity2)
dfrm2 <- as.data.frame(averageIntervalDay)
barchart(Freq ~ interval|wday,dfrm2,layout=c(1,7),strip=F,strip.left=T,
ylab="Average number of steps",xlab="Intervals of every 5 minutes by hours",
scales=list(x=list(at=seq(0,288,by=12),labels=c(0:24))),
main= "Differences in activity patterns between weekdays and weekends by days")
```
![Sample panel plot](figures2/ActivityPatterns_by_Days.png) 


Your plot will look different from the one above because you will be using the activity monitor data. Note that the above plot was made using the lattice system but you can make the same version of the plot using any plotting system you choose.




