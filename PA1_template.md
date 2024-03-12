---
title: "Reproducible Research: Peer Assessment 1"
output: 
  html_document:
    keep_md: true
---


## Loading and preprocessing the data


```r
  filename <- "activity.zip"
```
### Check if zip existed

```r
  if (!file.exists("activity.zip")){
  fileURL <- "https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip"
  download.file(fileURL, filename, method = "curl") 
  }
```
### Check if csv existed

```r
  if(!file.exists("activity.csv"))
    unzip(filename)
```
### Load CSV data


```r
  data <- read.csv("activity.csv")
  #data$date <- as.Date(data$date)
```

## Total number of steps taken each day histagram

### Process the data by day level

```r
  library(ggplot2)
  library(dplyr)
```


```r
  data_by_day <- data %>% 
    group_by(date) %>%
    summarise(steps = sum (steps))
```

### Draw the histgram


```r
ggplot(data_by_day, aes(x=steps)) + 
  geom_histogram(binwidth = 800, na.rm = TRUE, color = "darkblue", fill = "lightblue") +
  labs(title = "Total Steps Each Day")
```

![](PA1_template_files/figure-html/unnamed-chunk-7-1.png)<!-- -->

### Mean and Median total number of steps taken per day


```r
  summary(data_by_day$steps)
```

```
##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
##      41    8841   10765   10766   13294   21194       8
```

## What is the average daily activity pattern?



## Imputing missing values



## Are there differences in activity patterns between weekdays and weekends?
