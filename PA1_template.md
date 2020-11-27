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
myDataPlot <- myData %>% 
    filter(steps != "NA") %>% 
    group_by(interval) %>% 
    mutate(mean = mean(steps)) %>% 
    select(interval, mean) %>% 
    unique()

intervalMax <- myDataPlot$interval[which(myDataPlot$mean == max(myDataPlot$mean))]

ggplot(data = myDataPlot, aes(x = interval, y = mean)) + 
  geom_line()
```

![](PA1_template_files/figure-html/unnamed-chunk-4-1.png)<!-- -->

The 5-minute interval 835, on average across all the days in the dataset, contains the maximum number of steps.

## Imputing missing values



## Are there differences in activity patterns between weekdays and weekends?
