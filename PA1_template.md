Summary
-------

This document analyzes 2 months long data about personal movement
collected from the activity monitoring device of one person. The goal is
to draw meaningful conclusions about the variability of the human
movement across time, in weekdays Vs weekends as well as over different
hours of the day.

Reading and summarizing the Data
--------------------------------

We start by reading and summarizing the data.

    activity <- read.csv("activity.csv")
    summary(activity)

    ##      steps                date          interval     
    ##  Min.   :  0.00   2012-10-01:  288   Min.   :   0.0  
    ##  1st Qu.:  0.00   2012-10-02:  288   1st Qu.: 588.8  
    ##  Median :  0.00   2012-10-03:  288   Median :1177.5  
    ##  Mean   : 37.38   2012-10-04:  288   Mean   :1177.5  
    ##  3rd Qu.: 12.00   2012-10-05:  288   3rd Qu.:1766.2  
    ##  Max.   :806.00   2012-10-06:  288   Max.   :2355.0  
    ##  NA's   :2304     (Other)   :15840

We notice that we have three variables: "steps" corresponding to the
number of walked steps, "date" corresponding to given day and "interval"
referring to a 5 minutes time Interval during a given day.

Analyzing the total number of steps per day
-------------------------------------------

Next, we calculate the total number of steps walked per person each day.
We then dress a histogram showing the frequency distribution of the
total number of steps per day over 2 months. We observe that, during 27
days out of the 2 months observation window, a person walks in total
between 10000 and 15000 steps per day. During 2 months, there are 13
days where the monitored person walks less than 5000 steps per day and
only 2 days where he/she walks between 20000 and 25000 steps.

    SumSteps <- tapply(activity$steps,activity$date,sum,na.rm=TRUE)
    hist(SumSteps,col="red",xlab="total nb of Steps per Day", main="Distribution of Total Steps per Day over 2 months")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-2-1.png)

    means <- mean(SumSteps)
    median <- median(SumSteps)

We also calculate the mean and the median of the total number of steps
taken each day. The mean of the total number of steps per day is:
9354.23 The median of the total number of steps per day is: 10395 We
notice that the mean and the median have two different values and that
the mean has a lesser value than the median. This is particularly due to
the high number of NA values that were removed from the dataset.

Analyzing Steps hourly distribution
-----------------------------------

Next, we consider the 5 minutes intervals that we have per day and we
use the 2 months long data in order to calculate the average number of
steps taken at each interval. We plot the daily distribution of the
average number of steps per interval and we track the interval where the
maximum number of steps are walked.

    AvgSteps <- tapply(activity$steps,activity$interval,mean,na.rm=TRUE)
    n <- dim(AvgSteps)
    plot(AvgSteps,xlab="Time Interval",ylab="Average nb of Steps Taken",type="l",col="red")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-3-1.png)

    index <- 0
    for (i in 1:length(AvgSteps)) {if (AvgSteps[i] == max(AvgSteps)) index<-i} 

The interval is 104. The corresponding time is around 8h40 am.

Imputing Missing Data and analyzing consequences
------------------------------------------------

Next is the R code that we used for imputing NA values in the dataset.
The strategy that we use consists of replacing NA values in the "steps"
variable with the previously calculated average steps value for the
interval in question.

    naactivity <- subset(activity,is.na(activity$steps)==TRUE)
    nrow(naactivity)

    ## [1] 2304

    for (i in 1:nrow(activity)) {if (is.na(activity$steps[i])==TRUE) {
      if (i%%n !=0) 
      {activity$steps[i] <- AvgSteps[i %% n]}
      else {activity$steps[i] <- AvgSteps[n]}
     }
    }

Once this is done, we recompute the total number of steps walked each
day and the frequency distribution of these in the 2 months dataset that
we consider. We equally recompute the total median and mean values of
the total number of steps walked each day.

    SumSteps2 <- tapply(activity$steps,activity$date,sum)
    hist(SumSteps2,col="red",xlab="total nb of Steps per Day", main="Distribution of Total Steps per Day over 2 months")

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-5-1.png)

    means2 <- mean(SumSteps2)
    median2 <- median(SumSteps2)

We notice that data imputation led to a different Frequency distribution
of the total steps per day compared to previous. In particular, we
observe more days where the total number of steps is between 10000 and
15000 (35 days) and less days where the total number of steps is between
0 and 5000 (5 days). The mean of the total number of steps per day is:
1.07661910^{4} The median of the total number of steps per day is:
1.07661910^{4} After data imputation, the mean and medium number of
steps walked per day are equal and are superior to the median and mean
values without imputation.

Comparing Weekdays and Weekends walking activity
------------------------------------------------

In this section, we create a new label variable "weekday" which
indicates whether a given date is a weekday or a weekend. We then plot
the daily distribution of the average number of steps per 5 minutes
interval for a weeday and a weekend day, respectively.

    activity$weekday <- weekdays(as.Date(activity$date))
    for (i in 1:nrow(activity)) {
     if (activity$weekday[i] == "Saturday" || activity$weekday[i] == "Sunday") {activity$weekday[i] <- "weekend"}
      else {activity$weekday[i] <- "weekday"}
    }
     weekdayAct <- subset(activity, activity$weekday =="weekday")
     weekendAct <- subset(activity, activity$weekday =="weekend")


    AvgStepsWeekday <- tapply(weekdayAct$steps,weekdayAct$interval,mean)
    AvgStepsWeekend <- tapply(weekendAct$steps,weekendAct$interval,mean) 
    par(mfrow=c(2,1))
    plot(AvgStepsWeekday,xlab="Time Interval",ylab="",type="l",col="blue", main= "weekday")
    plot(AvgStepsWeekend,xlab="Time Interval",ylab="",type="l",col="blue", main = "Weekend") 

![](PA1_template_files/figure-markdown_strict/unnamed-chunk-6-1.png)

Based on plots, we witness more time intervals with a high number of
steps walked in average during a weekend day than during a weekday. In
particular, we observe peaks in walking activities in the morning
(around 9 am), at noon time (around 12 am), in the afternoon (around 4
pm) and in the evening (around 8 pm) in a weekend day whereas these
peaks are concentrated in the morning and the evening during a weekday.
