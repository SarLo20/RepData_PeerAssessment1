---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---



## Loading and preprocessing the data

```r
unzip("activity.zip")
myData <- read.csv("activity.csv")
```

## What is mean total number of steps taken per day?

```r
stepsPerDay <- myData %>% 
  group_by(date) %>% 
  mutate(total = sum(steps)) %>% 
  select(date, total) %>% 
  unique()
meanStepsPerDay <- round(mean(stepsPerDay$total, na.rm = T))
medianStepsPerDay <- round(median(stepsPerDay$total, na.rm = T))

ggplot(data = stepsPerDay, aes(stepsPerDay$total)) + 
  geom_histogram(binwidth = 100) +
  labs(title = "Histogram: Total number of steps taken each day",
       x = "Total number of steps") 
```

![](PA1_template_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

The mean total number of steps taken per day is 10766 and the median total number of steps taken per day is 10765.

## What is the average daily activity pattern?

```r
myDataIntervalMean <- myData %>% 
  filter(steps != "NA") %>% 
  group_by(interval) %>% 
  mutate(mean = mean(steps)) %>% 
  select(interval, mean) %>% 
  unique()

intervalMax <- myDataIntervalMean$interval[which(myDataIntervalMean$mean == max(myDataIntervalMean$mean))]

ggplot(data = myDataIntervalMean, aes(x = interval, y = mean)) + 
  geom_line()
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

The 5-minute interval 835, on average across all the days in the dataset, contains the maximum number of steps.

## Imputing missing values

There are 2304 missing values in the dataset.

**Fill in missing data**

```r
myDataComplete <- myData
for(idx in 1:nrow(myData)){
  if(is.na(myData$steps[idx])){
    # if NA then substitute with mean value of corresponding interval
    thisInterval <- myData$interval[idx]
    myDataComplete$steps[idx] <- myDataIntervalMean$mean[myDataIntervalMean$interval == myData$interval[idx]]
  }
}
```

**histogram**

```r
stepsPerDayComplete <- myDataComplete %>% 
  group_by(date) %>% 
  mutate(total = sum(steps)) %>% 
  select(date, total) %>% 
  unique()
meanStepsPerDayComplete <- round(mean(stepsPerDayComplete$total, na.rm = T))
medianStepsPerDayComplete <- round(median(stepsPerDayComplete$total, na.rm = T))

ggplot(data = stepsPerDayComplete, aes(stepsPerDayComplete$total)) + 
  geom_histogram(binwidth = 100) +
  labs(title = "Histogram: Total number of steps taken each day",
       x = "Total number of steps") 
```

![](PA1_template_files/figure-html/unnamed-chunk-6-1.png)<!-- -->

**Do these values differ from the estimates from the first part of the assignment?**

The mean total number of steps taken per day is 10766 and the median total number of steps taken per day is 10766.
These values differ only slightly from the estimates from the first part of the assignment.

**What is the impact of imputing missing data on the estimates of the total daily number of steps?**

Imputing missing data leads to an increased daily number of steps.

## Are there differences in activity patterns between weekdays and weekends?

```r
myDataComplete <- myDataComplete %>% 
  mutate(weekdayFactor = weekdays(as.POSIXct(date), abbreviate = FALSE)) 
myDataComplete$weekdayFactor[myDataComplete$weekdayFactor == "Montag"] <- "weekday"
myDataComplete$weekdayFactor[myDataComplete$weekdayFactor == "Dienstag"] <- "weekday"
myDataComplete$weekdayFactor[myDataComplete$weekdayFactor == "Mittwoch"] <- "weekday"
myDataComplete$weekdayFactor[myDataComplete$weekdayFactor == "Donnerstag"] <- "weekday"
myDataComplete$weekdayFactor[myDataComplete$weekdayFactor == "Freitag"] <- "weekday"
myDataComplete$weekdayFactor[myDataComplete$weekdayFactor == "Samstag"] <- "weekend"
myDataComplete$weekdayFactor[myDataComplete$weekdayFactor == "Sonntag"] <- "weekend"
myDataComplete$weekdayFactor <- as.factor(myDataComplete$weekdayFactor)

# average number of steps for each interval
aveStepsInterval <- myDataComplete %>% 
  group_by(weekdayFactor, interval) %>% 
  summarise(mean = mean(steps))
```
The following plot shows the differences in steps per interval between weekdays and weekend.

```r
ggplot(data = aveStepsInterval, aes(x = interval, y = mean)) + 
  geom_line() +
  facet_grid(rows = vars(weekdayFactor)) +
  labs(y = "average number of steps",
       title = "average number of steps taken for each interval")
```

![](PA1_template_files/figure-html/unnamed-chunk-8-1.png)<!-- -->

