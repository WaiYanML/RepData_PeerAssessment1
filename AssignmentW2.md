Assignment-Week2
================

Loading and Preprocessing Data
------------------------------

Data is loaded and date is formatted as Date. First few lines of data
can be observed below.

    activity<-read.csv('activity.csv')
    activity$date <- as.Date(activity$date)
    head(activity)

    ##   steps       date interval
    ## 1    NA 2012-10-01        0
    ## 2    NA 2012-10-01        5
    ## 3    NA 2012-10-01       10
    ## 4    NA 2012-10-01       15
    ## 5    NA 2012-10-01       20
    ## 6    NA 2012-10-01       25

Mean total number of steps per day
----------------------------------

The total number of steps per day is calculated. (NA values are
ignored.)

    per_day<-tapply(activity$steps, activity$date, sum, na.rm=TRUE)
    s <- summary(per_day)
    median <- median(per_day)
    mean <- mean(per_day)

### Histogram of Total Steps per Day

    hist(per_day, breaks=10, xlab='Avg-Steps per day')

![](AssignmentW2_files/figure-markdown_strict/unnamed-chunk-3-1.png)

    s

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##       0    6778   10395    9354   12811   21194

    median

    ## [1] 10395

    mean

    ## [1] 9354.23

It can be seen that median and mean of the total steps each day are
10395 and 9354.2295082 respectively.

Daily Pattern of Steps
----------------------

Steps are recorded as total 288 of 5-min intervals each day.  
Average steps during each interval is calculated. (NA values are
ignored.)

    per_interval<-tapply(activity$steps, activity$interval, sum, na.rm=TRUE)
    max_steps <- max(per_interval)
    max_interval <- rownames(per_interval)[per_interval==max_steps]
    summary(per_interval)

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##     0.0   131.8  1808.0  1981.3  2800.2 10927.0

### Average number of steps per interval

    plot(unique(activity$interval), per_interval, type='l', 
         xlab='Intervals',ylab='Avg-Steps per interval')
    abline(v=max_interval)
    text(x=max_interval, y=0, labels=max_interval)

![](AssignmentW2_files/figure-markdown_strict/unnamed-chunk-5-1.png)

    max_steps

    ## [1] 10927

As you can see, the interval with maximum steps(10927) is 835.

Imputing Missing Data
---------------------

    total_na <- sum(is.na(activity$steps))
    total_na

    ## [1] 2304

There are 2304 missing data in total. Missing data is replaced by
avg-steps per interval.

    activity2<-activity
    na_interval<-as.character(activity2$interval[is.na(activity2$steps)])
    activity2$steps[is.na(activity2$steps)]<-per_interval[na_interval]
    head(activity2)

    ##   steps       date interval
    ## 1    91 2012-10-01        0
    ## 2    18 2012-10-01        5
    ## 3     7 2012-10-01       10
    ## 4     8 2012-10-01       15
    ## 5     4 2012-10-01       20
    ## 6   111 2012-10-01       25

### Histogram of total steps per day with replaced missing data

    per_day2<-tapply(activity2$steps, activity2$date, sum, na.rm=TRUE)
    s3 <- summary(per_day2)
    median2 <- s3[[3]]
    mean2 <- s3[[4]]
    hist(per_day2, breaks=10, xlab='Avg-Steps per day')

![](AssignmentW2_files/figure-markdown_strict/unnamed-chunk-8-1.png)

    s3

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##      41    9819   11458   84188   15084  570608

    median2

    ## [1] 11458

    mean2

    ## [1] 84188.07

    median

    ## [1] 10395

    mean

    ## [1] 9354.23

Now, median and mean become 1.145810^{4} and 8.418806610^{4}.  
It was 10395 and 9354.2295082 before.  
Median only changed a bit while mean increased enormously.  
Let’s see how many data were missed in each interval.

    table(na_interval)

    ## na_interval
    ##    0   10  100 1000 1005 1010 1015 1020 1025 1030 1035 1040 1045  105 1050 1055 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8 
    ##  110 1100 1105 1110 1115 1120 1125 1130 1135 1140 1145  115 1150 1155  120 1200 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8 
    ## 1205 1210 1215 1220 1225 1230 1235 1240 1245  125 1250 1255  130 1300 1305 1310 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8 
    ## 1315 1320 1325 1330 1335 1340 1345  135 1350 1355  140 1400 1405 1410 1415 1420 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8 
    ## 1425 1430 1435 1440 1445  145 1450 1455   15  150 1500 1505 1510 1515 1520 1525 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8 
    ## 1530 1535 1540 1545  155 1550 1555 1600 1605 1610 1615 1620 1625 1630 1635 1640 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8 
    ## 1645 1650 1655 1700 1705 1710 1715 1720 1725 1730 1735 1740 1745 1750 1755 1800 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8 
    ## 1805 1810 1815 1820 1825 1830 1835 1840 1845 1850 1855 1900 1905 1910 1915 1920 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8 
    ## 1925 1930 1935 1940 1945 1950 1955   20  200 2000 2005 2010 2015 2020 2025 2030 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8 
    ## 2035 2040 2045  205 2050 2055  210 2100 2105 2110 2115 2120 2125 2130 2135 2140 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8 
    ## 2145  215 2150 2155  220 2200 2205 2210 2215 2220 2225 2230 2235 2240 2245  225 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8 
    ## 2250 2255  230 2300 2305 2310 2315 2320 2325 2330 2335 2340 2345  235 2350 2355 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8 
    ##  240  245   25  250  255   30  300  305  310  315  320  325  330  335  340  345 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8 
    ##   35  350  355   40  400  405  410  415  420  425  430  435  440  445   45  450 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8 
    ##  455    5   50  500  505  510  515  520  525  530  535  540  545   55  550  555 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8 
    ##  600  605  610  615  620  625  630  635  640  645  650  655  700  705  710  715 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8 
    ##  720  725  730  735  740  745  750  755  800  805  810  815  820  825  830  835 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8 
    ##  840  845  850  855  900  905  910  915  920  925  930  935  940  945  950  955 
    ##    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8    8

It shows 8 data points per interval are missing.  
Below is the summary of values replaced.

    summary(per_interval[na_interval])

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##     0.0   131.8  1808.0  1981.3  2800.2 10927.0

So, most of the data are below 2800 steps while maximum is over 10000.
This may be why data averages and histogram changed.

Weekdays vs Weekends
--------------------

Now, let’s see weekdays and weekends impact on activity.

    weekends <- c('Saturday','Sunday')
    activity2$weekday<-weekdays(activity2$date) %in% weekends
    is_weekend<-activity2$weekday
    activity2$weekday[!is_weekend]<-'weekday'
    activity2$weekday[is_weekend]<-'weekend'
    head(activity2)

    ##   steps       date interval weekday
    ## 1    91 2012-10-01        0 weekday
    ## 2    18 2012-10-01        5 weekday
    ## 3     7 2012-10-01       10 weekday
    ## 4     8 2012-10-01       15 weekday
    ## 5     4 2012-10-01       20 weekday
    ## 6   111 2012-10-01       25 weekday

Calculate avg steps per interval for weekdays and weekends.

    by_weekday <- split(activity2,activity2$weekday)
    per_interval2 <- lapply(by_weekday,function(x) tapply(x$steps,x$interval,sum))
    par(mfrow=c(1,2),mar=c(5,4,4,1))
    plot(unique(activity2$interval), per_interval2$weekday/5, type='l',  xlab='Intervals',ylab='Avg-Steps per interval',main='Weekdays')
    plot(unique(activity2$interval), per_interval2$weekend/2, type='l', xlab='Intervals',ylab='Avg-Steps per interval',main='Weekends')

![](AssignmentW2_files/figure-markdown_strict/unnamed-chunk-12-1.png)
