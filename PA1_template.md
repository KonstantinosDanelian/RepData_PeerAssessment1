Reproducible Research: Peer Assessment 1
========================================

1.  Load the data (i.e. read.csv()) and process/transform the data into
    a format suitable for your analysis

<!-- -->

    library(ggplot2)

    ## Warning: package 'ggplot2' was built under R version 3.3.2

     library(plyr)

    ## Warning: package 'plyr' was built under R version 3.3.2

     activity <- read.csv("activity.csv",colClasses=c("numeric","Date","numeric"))

What is mean total number of steps taken per day?
=================================================

1.  Make a histogram of the total number of steps taken each day

Calculate and report the mean and median total number of steps taken per
day

    sum_by_date <- tapply(activity$steps,activity$date,sum,na.rm=TRUE)
     sum_steps_per_day <- tapply(activity$steps,activity$date,sum,na.rm=TRUE)

    hist(sum_steps_per_day,col=heat.colors(5),xlab="Total Steps per day",main="Histogram of Total Steps per day")

![](PA1_template_files/figure-markdown_strict/hist-1.png)

1.  Calculate and report the mean and median total number of steps taken
    per day

<!-- -->

    mean(sum_steps_per_day)

    ## [1] 9354.23

    median(sum_steps_per_day)

    ## [1] 10395

What is the average daily activity pattern?
===========================================

1.  We will make a time series plot (i.e. type = "l") of the 5-minute
    interval (x-axis) and the average number of steps taken, averaged
    across all days (y-axis)

<!-- -->

    mean_by_interval <- tapply(activity$steps,activity$interval,mean,na.rm=TRUE)

    plot(row.names(mean_by_interval),mean_by_interval,type="l",xlab="5-minute interval",ylab="Average of total steps",main="Time Series Plot of the Average of Total Steps per day")

![](PA1_template_files/figure-markdown_strict/serier_plot-1.png)

2.Which 5-minute interval, on average across all the days in the
dataset, contains the maximum number of steps?

    x <- max(mean_by_interval)
    match(x,mean_by_interval)

    ## [1] 104

    mean_by_interval[104]

    ##      835 
    ## 206.1698

Imputing missing values
=======================

1.Calculate and report the total number of missing values in the dataset
(i.e. the total number of rows with NAs) 2.Devise a strategy for filling
in all of the missing values in the dataset. The strategy does not need
to be sophisticated. For example, you could use the mean/median for that
day, or the mean for that 5-minute interval, etc. 3.Create a new dataset
that is equal to the original dataset but with the missing data filled
in.

    sum(is.na(activity))

    ## [1] 2304

    activity_na <- activity[is.na(activity),]
    activity_no_na <- activity[complete.cases(activity),]
    activity_na$steps <- as.numeric(mean_by_interval)
    new_activity <- rbind(activity_na,activity_no_na)
    new_activity <-  new_activity[order(new_activity[,2],new_activity[,3]),]

4.Make a histogram of the total number of steps taken each day and
Calculate and report the mean and median total number of steps taken per
day.

    new_daily_sum <- tapply(new_activity$steps,new_activity$date,sum)

    hist(new_daily_sum,col=heat.colors(5),xlab="Daily Steps",main="Histogram of Total Steps with NA's filled")

![](PA1_template_files/figure-markdown_strict/hist_new-1.png)

    mean(new_daily_sum)

    ## [1] 10766.19

    median(new_daily_sum)

    ## [1] 10766.19

Are there differences in activity patterns between weekdays and weekends?
=========================================================================

1.Create a new factor variable in the dataset with two levels --
"weekday" and "weekend" indicating whether a given date is a weekday or
weekend day.

    days <- weekdays(new_activity[,2])
    new_activity <- cbind(new_activity,days)

    library(plyr)

    new_activity$days <- revalue(new_activity$days,c("Δευτέρα"="weekday","Τρίτη"="weekday","Τετάρτη"="weekday","Πέμπτη"="weekday","Παρασκευή"="weekday"))

    new_activity$days <- revalue(new_activity$days,c("Σάββατο"="weekend","Κυριακή"="weekend"))


    new_mean_interval <- tapply(new_activity$steps,list(new_activity$interval,new_activity$days),mean)

    library(reshape2)

    ## Warning: package 'reshape2' was built under R version 3.3.3

    new_mean_interval <- melt(new_mean_interval)
    colnames(new_mean_interval) <- c("interval","day","steps")
    library(lattice)

2.Make a panel plot containing a time series plot (i.e. type = "l") of
the 5-minute interval (x-axis) and the average number of steps taken,
averaged across all weekday days or weekend days (y-axis)

    xyplot(new_mean_interval$steps ~ new_mean_interval$interval | new_mean_interval$day, layout=c(1,2),type="l",main="Time Series Plot of the Average of Total Steps (weekday/weekend)",xlab="Time intervals",ylab="Average Total Steps")

![](PA1_template_files/figure-markdown_strict/time_serier_new-1.png)
