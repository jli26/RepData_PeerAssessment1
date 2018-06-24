### Loading and preprocessing the data

Show any code that is needed to:

1.  Load the data (i.e. read.csv()).

<!-- -->

    data <- read.csv("activity.csv", na.strings = "NA")
    head(data)

    ##   steps       date interval
    ## 1    NA 2012-10-01        0
    ## 2    NA 2012-10-01        5
    ## 3    NA 2012-10-01       10
    ## 4    NA 2012-10-01       15
    ## 5    NA 2012-10-01       20
    ## 6    NA 2012-10-01       25

1.  Process/transform the data (if necessary) into a format suitable for
    your analysis.  

-   Convert the "date" data field into an appropriate date format.

<!-- -->

    data$date <- as.POSIXct(data$date)

### What is mean total number of steps taken per day?

For this part of the assignment, you can ignore the missing values in
the dataset.

1.  Calculate the total number of steps taken per day.

<!-- -->

    dailySteps <- aggregate(steps ~ date, data, sum, na.rm = TRUE)

1.  If you do not understand the difference between a histogram and a
    barplot, research the difference between them. Make a histogram of
    the total number of steps taken each day.

<!-- -->

    hist(dailySteps$steps, main = "Histogram of Daily Step Count", breaks = 10, col = "gray", xlab = "Daily Steps")

![](Course_Project_1_files/figure-markdown_strict/unnamed-chunk-4-1.png)

1.  Calculate and report the mean and median of the total number of
    steps taken per day.

-   The mean of the daily total steps is 10766 and the median is 10765.

<!-- -->

    summary(dailySteps$steps)

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##      41    8841   10765   10766   13294   21194

### What is the average daily activity pattern?

1.  Make a time series plot (i.e. type = "l") of the 5-minute interval
    (x-axis) and the average number of steps taken, averaged across all
    days (y-axis).

<!-- -->

    avgStepsInterval <- aggregate(steps ~ interval, data, mean, na.rm = TRUE)
    with(avgStepsInterval, plot(interval, steps, type = "l", main = "Average Steps by Interval")) 

![](Course_Project_1_files/figure-markdown_strict/unnamed-chunk-6-1.png)

1.  Which 5-minute interval, on average across all the days in the
    dataset, contains the maximum number of steps?

-   Interval 835 contains the highest number of average steps.

<!-- -->

    avgStepsInterval[which.max(avgStepsInterval$steps),]

    ##     interval    steps
    ## 104      835 206.1698

### Imputing missing values

Note that there are a number of days/intervals where there are missing
values (coded as NA). The presence of missing days may introduce bias
into some calculations or summaries of the data.

1.  Calculate and report the total number of missing values in the
    dataset (i.e. the total number of rows with NAs).  

-   There are 2304 missing values in the dataset.

<!-- -->

    sum(is.na(data))

    ## [1] 2304

1.  Devise a strategy for filling in all of the missing values in the
    dataset. The strategy does not need to be sophisticated. For
    example, you could use the mean/median for that day, or the mean for
    that 5-minute interval, etc.  

-   The strategy used to fill in the missing values is to use the mean
    for the 5-minute interval.

1.  Create a new dataset that is equal to the original dataset but with
    the missing data filled in.

<!-- -->

    mergedData <- merge(data, avgStepsInterval, by = "interval")
    mergedData$steps.complete <- ifelse(is.na(mergedData$steps.x), mergedData$steps.y, mergedData$steps.x)

1.  Make a histogram of the total number of steps taken each day and
    calculate and report the mean and median total number of steps taken
    per day. Do these values differ from the estimates from the first
    part of the assignment? What is the impact of imputing missing data
    on the estimates of the total daily number of steps?

<!-- -->

    dailySteps.complete <- aggregate(steps.complete ~ date, mergedData, sum, na.rm = TRUE)
    hist(dailySteps.complete$steps.complete, main = "Histogram of Daily Step Count (Imputed Data)", breaks = 10, col = "gray", xlab = "Daily Steps")

![](Course_Project_1_files/figure-markdown_strict/unnamed-chunk-10-1.png)

    summary(dailySteps.complete$steps.complete)

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##      41    9819   10766   10766   12811   21194

-   Raw Data: The mean of the daily total steps is 10766 and the median
    is 10765.
-   Imputed Data: The new mean of the daily total steps is 10766 and the
    new median is 10766.
-   Impact on Mean: The mean stayed the same. This is in line with
    expectations because the value used for the missing values is the
    average steps across all days by interval. Since each day contains
    the same number of total intervals, this imputing strategy is
    equivalent to replacing the daily step count (for the days with
    missing values) with the average daily step count across all days.
-   Impact on Median: The median increased slightly to be the same value
    as the mean. This is in line with expectations because the imputing
    strategy increased the frequency of the daily step count values that
    are equal to the mean.

### Are there differences in activity patterns between weekdays and weekends?

For this part the weekdays() function may be of some help here. Use the
dataset with the filled-in missing values for this part.

1.  Create a new factor variable in the dataset with two levels –
    “weekday” and “weekend” indicating whether a given date is a weekday
    or weekend day.

<!-- -->

    mergedData$weekday <- weekdays(mergedData$date)
    mergedData$daytype <- ifelse(mergedData$weekday %in% c("Saturday","Sunday"), "weekend", "weekday")

1.  Make a panel plot containing a time series plot (i.e. type = "l") of
    the 5-minute interval (x-axis) and the average number of steps
    taken, averaged across all weekday days or weekend days (y-axis).
    See the README file in the GitHub repository to see an example of
    what this plot should look like using simulated data.

<!-- -->

    avgStepsInterval_dayType <- aggregate(steps.complete ~ interval + daytype, mergedData, mean, na.rm = TRUE)
    xyplot(steps.complete ~ interval | daytype, data = avgStepsInterval_dayType, layout = c(1,2), type = "l", xlab = "Interval", ylab = "Number of Steps")

![](Course_Project_1_files/figure-markdown_strict/unnamed-chunk-12-1.png)
