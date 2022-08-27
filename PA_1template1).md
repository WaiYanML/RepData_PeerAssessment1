---
output: html_notebook
---
# Assignment-Week2

## Loading and Preprocessing Data
Data is loaded and date is formatted as Date. First few lines of data can be observed below.
```{r}
activity<-read.csv('activity.csv')
activity$date <- as.Date(activity$date)
head(activity)
```

## Mean total number of steps per day
The total number of steps per day is calculated. (NA values are ignored.)
```{r}
per_day<-tapply(activity$steps, activity$date, sum, na.rm=TRUE)
s <- summary(per_day)
median <- median(per_day)
mean <- mean(per_day)
```
### Histogram of Total Steps per Day
```{r}
hist(per_day, breaks=10, xlab='Avg-Steps per day')
s
```
It can be seen that median and mean of the total steps each day are `r median` and `r mean` respectively.

## Daily Pattern of Steps
Steps are recorded as total 288 of 5-min intervals each day.  
Average steps during each interval is calculated. (NA values are ignored.)
```{r}
per_interval<-tapply(activity$steps, activity$interval, sum, na.rm=TRUE)
max_steps <- max(per_interval)
max_interval <- rownames(per_interval)[per_interval==max_steps]
summary(per_interval)
```
### Average number of steps per interval
```{r}
plot(unique(activity$interval), per_interval, type='l', 
     xlab='Intervals',ylab='Avg-Steps per interval')
abline(v=max_interval)
text(x=max_interval, y=0, labels=max_interval)
```
As you can see, the interval with maximum steps(`r max_steps`) is `r max_interval`.

## Imputing Missing Data
```{r}
total_na <- sum(is.na(activity$steps))
```
There are `r total_na` missing data in total.
Missing data is replaced by avg-steps per interval.
```{r}
activity2<-activity
na_interval<-as.character(activity2$interval[is.na(activity2$steps)])
activity2$steps[is.na(activity2$steps)]<-per_interval[na_interval]
head(activity2)
```
### Histogram of total steps per day with replaced missing data
```{r}
per_day2<-tapply(activity2$steps, activity2$date, sum, na.rm=TRUE)
s3 <- summary(per_day2)
median2 <- s3[[3]]
mean2 <- s3[[4]]
hist(per_day2, breaks=10, xlab='Avg-Steps per day')
s3
```
Now, median and mean become `r median2` and `r mean2`.  
It was `r median` and `r mean` before.  
Median only changed a bit while mean increased enormously.  
Let's see how many data were missed in each interval.
```{r}
table(na_interval)
```
It shows 8 data points per interval are missing.  
Below is the summary of values replaced.
```{r}
summary(per_interval[na_interval])
```
So, most of the data are below 2800 steps while maximum is over 10000. This may be why data averages and histogram changed.

## Weekdays vs Weekends
Now, let's see weekdays and weekends impact on activity.
```{r}
weekends <- c('Saturday','Sunday')
activity2$weekday<-weekdays(activity2$date) %in% weekends
is_weekend<-activity2$weekday
activity2$weekday[!is_weekend]<-'weekday'
activity2$weekday[is_weekend]<-'weekend'
head(activity2)
```
Calculate avg steps per interval for weekdays and weekends.
```{r}
by_weekday <- split(activity2,activity2$weekday)
per_interval2 <- lapply(by_weekday,function(x) tapply(x$steps,x$interval,sum))
par(mfrow=c(1,2),mar=c(5,4,4,1))
plot(unique(activity2$interval), per_interval2$weekday/5, type='l',  xlab='Intervals',ylab='Avg-Steps per interval',main='Weekdays')
plot(unique(activity2$interval), per_interval2$weekend/2, type='l', xlab='Intervals',ylab='Avg-Steps per interval',main='Weekends')
```


