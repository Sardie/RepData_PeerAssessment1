# Reproducible Research: Peer Assessment 1



## Loading and preprocessing the data

```r
data <- read.csv("activity.csv")
```


## What is the mean total number of steps taken per day?

### Total number of steps taken per day

```r
dataSum <- with(data, tapply(steps, date, sum))
hist(dataSum, main = "Histogram on the total number of steps taken per day", xlab = "", col = "turquoise", border = "blue", labels = T)
```

![](./PA1_template_files/figure-html/unnamed-chunk-2-1.png) 


### Mean number of steps taken per day

```r
mean(dataSum, na.rm = T)
```

```
## [1] 10766.19
```

### Median number of steps taken per day

```r
median(dataSum, na.rm = T)
```

```
## [1] 10765
```


## What is the average daily activity pattern?

```r
dataAvgIntv <- with(data, tapply(steps, interval, mean, na.rm = T))
plot(dataAvgIntv, type = "l", xlab = "Index of 5-minute intervals", ylab = "Avg no. of steps taken, avg-ed across all days", main = "Time series plot of 5-minute interval", col = "blue")
```

![](./PA1_template_files/figure-html/unnamed-chunk-5-1.png) 

### The 5-minute interval with the most number of steps averaged across all days is:

```r
names(dataAvgIntv[dataAvgIntv == max(dataAvgIntv)])
```

```
## [1] "835"
```


## Imputing missing values

### The number of NA entries in the steps variable is:

```r
nrow(data[is.na(data$steps),])
```

```
## [1] 2304
```

### Missing data is imputed by filling them up with the mean number of steps of the corresponding 5-minute interval

```r
data2 <- data
for (i in 1:nrow(data2)) {
        if (is.na(data2$steps[i])) {
                data2$steps[i] <- dataAvgIntv[match(data2$interval[i], names(dataAvgIntv))]
                      
        }  
}
```

### Re-generating histogram, mean, and median again using new dataset:

### Total number of steps taken per day:

```r
dataSum <- with(data2, tapply(steps, date, sum))
hist(dataSum, main = "Histogram on the total number of steps taken per day", xlab = "", col = "magenta", border = "purple", labels = T)
```

![](./PA1_template_files/figure-html/unnamed-chunk-9-1.png) 


### Mean number of steps taken per day:

```r
mean(dataSum, na.rm = T)
```

```
## [1] 10766.19
```

### Median number of steps taken per day:

```r
median(dataSum, na.rm = T)
```

```
## [1] 10766.19
```

### Impact of imputation:
The peak of the histogram became larger, from 28 to 36.
The mean and median of the total number of steps taken per day became equal compared to that before the imputation where the median is smaller than the mean.

## Are there differences in activity patterns between weekdays and weekends?

### First, formating and including additional column in imputed dataset

```r
data2$date <- as.Date(data2$date)
data2$week <- weekdays(data2$date)
```

### Second, split data into weekend and weekday datasets

```r
dataWeekends <- data2[(data2$week == "Saturday" | data2$week == "Sunday"),]
dataWeekdays <- data2[!(data2$week == "Saturday" | data2$week == "Sunday"),]
```

### Third, examine the differences between weekend and weekday datasets

For weekend first:

Total number of steps taken per day:

```r
dataSum <- with(dataWeekends, tapply(steps, date, sum))
hist(dataSum, main = "Histogram on the total number of steps taken per day", xlab = "", col = "green", border = "blue", labels = T)
```

![](./PA1_template_files/figure-html/unnamed-chunk-14-1.png) 


Mean number of steps taken per day:

```r
mean(dataSum, na.rm = T)
```

```
## [1] 12201.52
```

Median number of steps taken per day:

```r
median(dataSum, na.rm = T)
```

```
## [1] 11646
```

Time series plot:

```r
dataAvgIntv <- with(dataWeekends, tapply(steps, interval, mean))
plot(dataAvgIntv, type = "l", xlab = "Index of 5-minute intervals", ylab = "Avg no. of steps taken, avg-ed across all days", main = "Time series plot of 5-minute interval", col = "blue")
```

![](./PA1_template_files/figure-html/unnamed-chunk-17-1.png) 

The 5-minute interval with the most number of steps averaged across all days is:

```r
names(dataAvgIntv[dataAvgIntv == max(dataAvgIntv)])
```

```
## [1] "915"
```


Next, for weekday:

Total number of steps taken per day:

```r
dataSum <- with(dataWeekdays, tapply(steps, date, sum))
hist(dataSum, main = "Histogram on the total number of steps taken per day", xlab = "", col = "red", border = "pink", labels = T)
```

![](./PA1_template_files/figure-html/unnamed-chunk-19-1.png) 


Mean number of steps taken per day:

```r
mean(dataSum, na.rm = T)
```

```
## [1] 10255.85
```

Median number of steps taken per day:

```r
median(dataSum, na.rm = T)
```

```
## [1] 10765
```

Time series plot:

```r
dataAvgIntv <- with(dataWeekdays, tapply(steps, interval, mean, na.rm = T))
plot(dataAvgIntv, type = "l", xlab = "Index of 5-minute intervals", ylab = "Avg no. of steps taken, avg-ed across all days", main = "Time series plot of 5-minute interval", col = "red")
```

![](./PA1_template_files/figure-html/unnamed-chunk-22-1.png) 

The 5-minute interval with the most number of steps averaged across all days is:

```r
names(dataAvgIntv[dataAvgIntv == max(dataAvgIntv)])
```

```
## [1] "835"
```


### Observation:
1. The distribution of the total number of steps per weekday was more normally distributed compared to that of weekends. 
2. More steps were recorded during weekends than weekdays when comparing the mean and median of weekends and weekdays.
3. Most steps were recorded at 9.15 am during weekends while that of weekdays is 8.35 am.
4. The histogram and times series plot of weekdays resemble the overall plots more than weekends. This might not be surprising since the data has not yet been normalized.






