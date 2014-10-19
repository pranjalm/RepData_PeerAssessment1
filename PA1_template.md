# Peer Assessment 1
Pranjal Mandhaniya  
Tuesday, October 14, 2014  

Loading and preprocessing the data

Show any code that is needed to

Load the data (i.e. read.csv())

Process/transform the data (if necessary) into a format suitable for your analysisis:


```r
j <- read.csv("activity.csv") -> p
j[is.na(j)] <- 0
aggregate(steps~date,data=j,FUN=sum) -> t
x <- barplot(t$steps, names.arg = t$date,xlab = "date", ylab = "steps",xaxt="n")
labs <- paste(names(table(t$date)))
text(cex=0.6, x=x, y=0,pos=2,labs, xpd=TRUE, srt=90)
```

![plot of chunk unnamed-chunk-1](./PA1_template_files/figure-html/unnamed-chunk-1.png) 

```r
mean(t$steps)
```

```
## [1] 9354
```

```r
median(t$steps)
```

```
## [1] 10395
```

What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in the dataset.

Make a histogram of the total number of steps taken each day

Calculate and report the mean and median total number of steps taken per day


```r
m <- aggregate(steps~interval,data=j,FUN="mean")
plot(m,type="l")
```

![plot of chunk unnamed-chunk-2](./PA1_template_files/figure-html/unnamed-chunk-2.png) 

```r
m$interval[which.max(m$steps)]
```

```
## [1] 835
```
What is the average daily activity pattern?

Make a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all days (y-axis)

Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```r
sum(is.na(p))
```

```
## [1] 2304
```

```r
p <- merge(p, m, by = "interval", suffixes = c("",".y"))
nas <- is.na(p$steps)
p$steps[nas] <- p$steps.y[nas]
p <- p[, c(1:3)]
p <- p[with(p,order(date)),]
```
Imputing missing values

Note that there are a number of days/intervals where there are missing values (coded as NA). The presence of missing days may introduce bias into some calculations or summaries of the data.

Calculate and report the total number of missing values in the dataset (i.e. the total number of rows with NAs)

Devise a strategy for filling in all of the missing values in the dataset. The strategy does not need to be sophisticated. For example, you could use the mean/median for that day, or the mean for that 5-minute interval, etc.

Create a new dataset that is equal to the original dataset but with the missing data filled in.

Make a histogram of the total number of steps taken each day and Calculate and report the mean and median total number of steps taken per day. Do these values differ from the estimates from the first part of the assignment? What is the impact of imputing missing data on the estimates of the total daily number of steps?

```r
t <- aggregate(steps ~ date, data = p, FUN = sum)
barplot(t$steps, names.arg = t$date, xlab = "date", ylab = "steps")
```

![plot of chunk unnamed-chunk-4](./PA1_template_files/figure-html/unnamed-chunk-4.png) 

```r
mean(t$steps)
```

```
## [1] 10581
```

```r
median(t$steps)
```

```
## [1] 10395
```
Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the dataset with the filled-in missing values for this part.

Create a new factor variable in the dataset with two levels - "weekday" and "weekend" indicating whether a given date is a weekday or weekend day.

Make a panel plot containing a time series plot (i.e. type = "l") of the 5-minute interval (x-axis) and the average number of steps taken, averaged across all weekday days or weekend days (y-axis). The plot should look something like the following, which was creating using simulated data:

```r
daytype <- function(date) {
    if (weekdays(as.Date(date)) %in% c("Saturday", "Sunday")) {
        "weekend"
    } else {
        "weekday"
    }
}
j$daytype <- as.factor(sapply(j$date, daytype))

layout(matrix(c(1,2), 1, 1, byrow = TRUE))
for (dt in c("weekend", "weekday")) {
  h <- aggregate(steps~interval, data = j, 
                  subset=j$daytype==dt, FUN = "mean")
    plot(h, type = "l", main = dt)
}
```

![plot of chunk unnamed-chunk-5](./PA1_template_files/figure-html/unnamed-chunk-51.png) ![plot of chunk unnamed-chunk-5](./PA1_template_files/figure-html/unnamed-chunk-52.png) 


Your plot will look different from the one above because you will be using the activity monitor data. Note that the above plot was made using the lattice system but you can make the same version of the plot using any plotting system you choose.