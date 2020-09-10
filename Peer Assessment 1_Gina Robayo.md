---
title: "Reproducible Research: Peer Assessment 1_Gina Robayo.md"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data
activity = read.csv("C:/Users/ginna.robayo/Desktop/GINA PERSONAL/COURSERA/specdata/activity.csv")

## What is mean total number of steps taken per day?
total_of_steps_of_day <- sum(activity$steps, na.rm = TRUE)

##the total number of steps per day is
total_of_steps_of_day
[1] 570608


Next, we make a histogram of the total number of steps in order to see its variance 
and distribution.


```r
hist(total_steps$total, breaks = 10, main = "Histogram of total number of steps taken each day",
     xlab = "Total number of steps per day")
```
(figure/hist-1.png) 

Finally, we compute *mean* and *median* values of the total number of steps.


```r
mean(total_steps$total)
```

```
## [1] 10766.19
```

```r
median(total_steps$total)
```

```
## [1] 10765
```

## What is the average daily activity pattern?

Below, we can see as the number of steps changes throught the day (averaged across all the days).


```r
steps_per_interval <- activity %>% 
  group_by(interval) %>% 
  summarize(mean = mean(steps, na.rm = TRUE))
plot(steps_per_interval, type = 'l', main = "Average number of steps per interval",
     xlab = "Time interval", ylab = "Average number of steps")
```

![plot of chunk timeseries](figure/timeseries-1.png) 

The 5-minute interval with maximum number of steps is as follows:


```r
max_steps <- max(steps_per_interval$mean)
subset(steps_per_interval, mean == max_steps)$interval
```

```
## [1] 835
```

## Imputing missing values

The total number of rows with missing values:


```r
sum(!complete.cases(activity))
```

```
## [1] 2304
```

We fill the missing values of steps with the average number of steps
for the given 5-minute interval.


```r
interval_means <- sapply(activity$interval, 
                         function(x) subset(steps_per_interval, interval == x)$mean)
missing <- is.na(activity$steps)

activity_imputed <- activity
activity_imputed[missing, ]$steps <- interval_means[missing]
```

Next, we calculate total number of steps on the imputed data and visualize the histogram
in order to compare it with the original data.


```r
total_steps_imputed <- activity_imputed %>% 
  group_by(date) %>% 
  summarize(total = sum(steps))

hist(total_steps_imputed$total, breaks = 10, main = 
       "Histogram of total number of steps per day with no missing values",
     xlab = "Total number of steps per day")
```

![plot of chunk histogram_imputed](figure/histogram_imputed-1.png) 

For the sake of comparison, we also compute new *mean* and *median* values 
of the total number of steps.


```r
mean(total_steps_imputed$total)
```

```
## [1] 10766.19
```

```r
median(total_steps_imputed$total)
```

```
## [1] 10766.19
```

We can see that the chosen strategy has no effect on the mean; 
the median is a bit higher and now equal to the mean.

## Are there differences in activity patterns between weekdays and weekends?

We add a column `day_in_week` (a factor variable) which indicates whether the measuring occurred
during a weekday or a weekend.


```r
day_in_week <- function(date) {
  wday <- wday(date)
  is_weekend <- wday == 1 | wday == 6
  
  day_in_week <- character(length = length(date))
  day_in_week[is_weekend] <- "weekend"
  day_in_week[!is_weekend] <- "weekday"
  
  return(as.factor(day_in_week))
}

activity_imputed <- activity_imputed %>% mutate(day_in_week = day_in_week(date))
```

Finally, we plot the time series of number of steps per intervals 
comparing the weekdays and weekends. We use `ggplot` library for this purpose.


```r
steps_per_interval_imputed <- activity_imputed %>% 
  group_by(interval, day_in_week) %>% 
  summarize(mean = mean(steps, na.rm = TRUE))

library(ggplot2)
g <- ggplot(steps_per_interval_imputed, aes(interval, mean))
g <- g + facet_grid(day_in_week ~ .)
g <- g + geom_line()
g + xlab("Interval") + ylab("Number of steps")
```

(figure/timeseries_compare-1.png) 
