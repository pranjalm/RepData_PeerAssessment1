# Reproducible Research:Peer Assessment 1
Pranjal Mandhaniya  
Sunday, January 18, 2015  

<b>Loading and preprocessing the data</b>

- Show any code that is needed to

- Load the data (i.e. read.csv())

- Process/transform the data (if necessary) into a format suitable for your analysis


```r
library(ggplot2)
dt <- read.csv("activity.csv")
```

<b>What is mean total number of steps taken per day?</b>

- For this part of the assignment, you can ignore the missing values in the dataset.

- Make a histogram of the total number of steps taken each day

- Calculate and report the mean and median total number of steps taken per day


```r
agdt <- aggregate(steps~date,data=dt,sum)
ggplot(agdt, aes(date, steps)) + 
  geom_bar(stat = "identity", colour = "cyan",fill = "red", width = 0.5) + 
  labs(title = "Histogram of Steps Taken Each Day", x = "Date",y = "Total number of steps") +
  theme(axis.text.x = element_text(angle = 90,hjust = 0))
```

![](PA1_template_files/figure-html/unnamed-chunk-2-1.png) 

<b>What is mean total number of steps taken per day?</b>

- For this part of the assignment, you can ignore the missing values in the dataset.

- Make a histogram of the total number of steps taken each day

- Calculate and report the mean and median total number of steps taken per day


```r
mean(agdt$steps)
```

```
## [1] 10766.19
```

```r
median(agdt$steps)
```

```
## [1] 10765
```

<b>What is the average daily activity pattern?</b>

- Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

- Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?


```r
agint <- aggregate(steps~interval,data=dt,mean)
ggplot(agint, aes(x = interval, y = steps)) + 
  geom_line(color="red") + 
  labs(title = "Steps v/s Interval plot", x = "Time Interval(5 minutes)",y = "Steps") 
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png) 

```r
maxint <- agint[agint$steps==max(agint$steps),]$interval
```

<b>Imputing missing values</b>

- Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

- Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

- Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

- Create a new dataset that is equal to the original dataset but with the missing data filled in.

- Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?


```r
sum(is.na(dt))
```

```
## [1] 2304
```

```r
ndt <- dt
for (i in 1:nrow(ndt)) {
  if(is.na(ndt$steps[i])) {
    ndt$steps[i] <- agint[ndt$interval[i] == agint$interval, ]$steps
  }
}
```


```r
agndt <- aggregate(steps~date,data=ndt,sum)
ggplot(agndt, aes(date, steps)) + 
  geom_bar(stat = "identity", colour = "red",fill = "cyan", width = 0.5) + 
  labs(title = "Histogram of Steps Taken Each Day(new data)", x = "Date",y = "Total number of steps") + theme(axis.text.x = element_text(angle = 90,hjust = 0))
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png) 

```r
mean(agndt$steps)
```

```
## [1] 10766.19
```

```r
median(agndt$steps)
```

```
## [1] 10766.19
```

<b>Are there differences in activity patterns between weekdays and weekends?</b>

- For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

- Create a new factor variable in the dataset with two levels – “weekday” and “weekend” indicating whether a given date is a weekday or weekend day.

- Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). See the README file in the GitHub repository to see an example of what this plot should look like using simulated data.


```r
dt$date <- as.Date(dt$date,"%Y-%m-%d") 
dt$day <- weekdays(dt$date)

wkdt <- dt[dt$day =="Sunday" | dt$day =="Saturday",]
wkdt <- aggregate(steps~interval,data=wkdt,mean)
ggplot(wkdt, aes(x = interval, y = steps)) + geom_line() + 
  labs(title = "Steps v/s Interval plot(weekend)", x = "Time Interval(5 minutes)",y = "Steps")  
```

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png) 

```r
wddt <- dt[dt$day !="Sunday" & dt$day !="Saturday",]
wddt <- aggregate(steps~interval,data=wddt,mean)
ggplot(wddt, aes(x = interval, y = steps)) + geom_line() + 
  labs(title = "Steps v/s Interval plot(weekday)", x = "Time Interval(5 minutes)",y = "Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-7-2.png) 

