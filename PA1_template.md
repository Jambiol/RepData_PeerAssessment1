# Reproducible Research: Peer Assessment 1
###Loading and preprocessing the data

Show any code that is needed to

- Load the data (i.e. read.csv())
- Process/transform the data (if necessary) into a format suitable for your analysisains the dataset for the assignment so you do not have to download the data separately.


```r
data <- read.csv("/home/jordi/activity.csv", header = TRUE)
data$date <- as.Date(data$date)
```

###What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

- Calculate the total number of steps taken per day

```r
stepsxday <- aggregate(steps ~ date, data, sum, na.rm = TRUE)
```

- If you do not understand the difference between a histogram and a barplot, research the difference between them. Make a histogram of the total number of steps taken each day

```r
hist(stepsxday$steps, main="Frequency of steps per day", xlab="Steps per day", breaks = 10)
```

![](figure/unnamed-chunk-3-1.png)

- Calculate and report the mean and median of the total number of steps taken per day

```r
mean(stepsxday$steps)
```

```
## [1] 10766.19
```

```r
median(stepsxday$steps)
```

```
## [1] 10765
```

###What is the average daily activity pattern?

- Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

```r
stepsxinterval <- aggregate(steps ~ interval, data, mean, na.rm=TRUE)
with(stepsxinterval, plot(interval, steps, type="l", main="Average steps across all days",
                          xlab="Minute", ylab="Steps"))
```

![](figure/unnamed-chunk-5-1.png)

- Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
stepsxinterval$interval[which.max(stepsxinterval$steps)]
```

```
## [1] 835
```

###Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

- Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

```r
sum(is.na(data$steps))
```

```
## [1] 2304
```

- Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.  
*I am going to fill the missing values with the rounded mean for that 5-minute interval*

- Create a new dataset that is equal to the original dataset but with the missing data filled in.

```r
tidydata <- data
nafill <- round(stepsxinterval$steps[match(data$interval,stepsxinterval$interval)])
tidydata$steps <- ifelse(is.na(tidydata$steps), nafill, data$steps)
head(tidydata)
```

```
##   steps       date interval
## 1     2 2012-10-01        0
## 2     0 2012-10-01        5
## 3     0 2012-10-01       10
## 4     0 2012-10-01       15
## 5     0 2012-10-01       20
## 6     2 2012-10-01       25
```

- Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
stepsxdaytidy <- aggregate(steps ~ date, tidydata, sum)
hist(stepsxdaytidy$steps, main="Frequency of steps per day", xlab="Steps per day", breaks = 10)
```

![](figure/unnamed-chunk-9-1.png)

*We can see no differences in the shape of the graphic, but differences in the frequencies.*


```r
mean(stepsxdaytidy$steps)
```

```
## [1] 10765.64
```

```r
median(stepsxdaytidy$steps)
```

```
## [1] 10762
```

*We notice that both mean a median are slightly different. I changed the NA values for the rounded mean for that interval, so this negligible effect was expected.*

###Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

- Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

```r
tidydata$week <- as.factor(ifelse(weekdays(tidydata$date) %in% c("Saturday", "Sunday"),
                                "weekend", "weekday"))
```

- Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.

```r
stepsweek <- aggregate(steps ~ interval + week, tidydata, mean)
require(lattice)
```

```
## Loading required package: lattice
```

```r
xyplot(steps ~ interval | week, data=stepsweek, type='l')
```

![](figure/unnamed-chunk-12-1.png)
