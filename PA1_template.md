---
title: "PA1_template"
author: "Bryan Gottshall"
output: html_document
---

Load The Data:

```r
setwd("/Users/bryangottshall/Downloads")
data.act<-read.csv("activity.csv")
library(Hmisc)
```

Examining Mean and Median Steps Per Day:

  Find the Mean and Median Number of Steps Taken Per Day:

```r
step.day<-tapply(data.act$steps, data.act$date, sum, na.rm=TRUE)
mean.step.day<-mean(step.day)
med.step.day<-median(step.day)
med.step.day
```

```
## [1] 10395
```
  Mean Steps Per Day:

```r
mean.step.day
```

```
## [1] 9354.23
```
  Median Steps Per Day:

```r
med.step.day
```

```
## [1] 10395
```

  Create Historgram of the total number of steps taken per day:


```r
hist(step.day, xlab="Steps Per Day", ylab="Frequency", main="Frequency of Steps Per Day")
```

![plot of chunk unnamed-chunk-5](figure/unnamed-chunk-5-1.png) 

Average Daily Activity Pattern:

```r
average.steps.interval<-aggregate(x=list(meanSteps=data.act$steps), by=list(interval=data.act$interval), FUN="mean", na.rm=TRUE)
```

  Create a Time Series Plot of AVG Daily Activity Pattern:

```r
plot(average.steps.interval$interval, average.steps.interval$meanSteps, type="l",xlab="Interval", ylab="Steps",main="Time Series of Steps by Interval")
```

![plot of chunk unnamed-chunk-7](figure/unnamed-chunk-7-1.png) 

  Find 5 Minute Interval with the Most Steps:
  

```r
max.step<-which.max(average.steps.interval$meanSteps)
max.step.data<-average.steps.interval[104,]
interval.max.step<-max.step.data$interval
```

Create Data With Imputed Missing Values for Steps Per Day:

 Calculate Total Number of Missing Values:

```r
missing.val.amt<-length(which(is.na(data.act$steps)))
```

  Create New Data Set with Imputed Values:
  

```r
data.act.impute<-data.act
data.act.impute$steps<-impute(data.act$steps, FUN="mean")
```

  Calculate Daily Mean and Median Steps:

```r
step.day.impute<-tapply(data.act.impute$steps, data.act.impute$date, sum)
mean.step.day.impute<-mean(step.day.impute)
med.step.day.impute<-median(step.day.impute)
```
  
  Make a Histogram of Total Number of Daily Steps:
  

```r
hist(step.day.impute, xlab="Total Steps Per Day", ylab="Frequency", main="Histogram of Daily Steps")
```

![plot of chunk unnamed-chunk-12](figure/unnamed-chunk-12-1.png) 

Look at Differences Between Weekday and Weekend Activities:

  Create a Factor Variables with Two Levels, "Weekday" and "Weekend":
  
  

```r
data.act.impute$date<-as.POSIXlt(data.act.impute$date)
data.act.impute$weekend<-ifelse(weekdays(data.act.impute$date)== "Saturday","Weekend",
                                ifelse(weekdays(data.act.impute$date)=="Sunday","Weekend","Weekday"))
```

  Make a Panel Plot with Weekday and Weekend Time Series:
  

```r
average.steps.interval.impute<-aggregate(steps~interval+ weekend,data=data.act.impute, mean)
par(mfrow=c(2,1))
average.steps.interval.impute.weekend<-average.steps.interval.impute[average.steps.interval.impute$weekend=="Weekend",]
average.steps.interval.impute.weekday<-average.steps.interval.impute[average.steps.interval.impute$weekend=="Weekday",]
plot(average.steps.interval.impute.weekend$interval, average.steps.interval.impute.weekend$steps, type="l", xlab="Average Steps", ylab="Interval", main="Weekend Average Steps")
plot(average.steps.interval.impute.weekday$interval, average.steps.interval.impute.weekday$steps, type="l", xlab="Average Steps", ylab="Interval", main="Weekday Average Steps")
```

![plot of chunk unnamed-chunk-14](figure/unnamed-chunk-14-1.png) 
