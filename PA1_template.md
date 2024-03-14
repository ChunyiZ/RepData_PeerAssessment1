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
### Process the data by interval level

```r
  data_by_interval <- data %>% 
    group_by(interval) %>%
    summarise(steps = mean(steps, na.rm = TRUE))
```

### Plot the line 


```r
  ggplot(data_by_interval, aes(x=interval, y=steps, group = 1))+
  geom_line(color = "darkblue")+
  ggtitle("Interval vs Steps Across Days")+
  labs(x="Time Interval",y="Avearge Steps")
```

![](PA1_template_files/figure-html/unnamed-chunk-10-1.png)<!-- -->

### Find the interval that has the max average steps

```r
maxinterval <- data_by_interval %>%
    slice_max(steps) %>%
    pull(interval)

print(maxinterval)
```

```
## [1] 835
```

## Imputing missing values

### Find the total number of NA in original data set


```r
print("Total missing values in the dataset is")
```

```
## [1] "Total missing values in the dataset is"
```

```r
sum(is.na(data$steps))
```

```
## [1] 2304
```

### Fill the missing data, use 5 min interval avg to fill the missing value



```r
data_impute <- data

data_impute <- data_impute %>% group_by(interval) %>% mutate(steps = ifelse(is.na(steps), mean(steps,na.rm =TRUE),steps))

sum(is.na(data_impute))
```

```
## [1] 0
```

### Draw the histgram


```r
  data_impute_by_day <- data_impute %>% 
    group_by(date) %>%
    summarise(steps = sum (steps))
```


```r
ggplot(data_impute_by_day, aes(x=steps)) + 
  geom_histogram(binwidth = 500, na.rm = TRUE, color = "darkblue", fill = "lightblue") +
  labs(title = "Total Steps Each Day")
```

![](PA1_template_files/figure-html/unnamed-chunk-15-1.png)<!-- -->

## Are there differences in activity patterns between weekdays and weekends?
### Create "Weekend" & "Weekdays" factors

```r
weekdays1 <- c('Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday')

data_impute_by_day <- data_impute_by_day %>% mutate (date = as.Date(date)) %>% mutate(datenew = ifelse(weekdays(date) %in% weekdays1, 'Weekdays','Weekend'))
```

### Plot the linegrame


```r
g <- ggplot(data_impute_by_day,aes(x=date, y=steps))+geom_line(color = "darkblue")+
     facet_wrap(~datenew, ncol = 1)


g
```

![](PA1_template_files/figure-html/unnamed-chunk-17-1.png)<!-- -->



```r
print("Conclusion - people tend to walk more during weekdays.")
```

```
## [1] "Conclusion - people tend to walk more during weekdays."
```
