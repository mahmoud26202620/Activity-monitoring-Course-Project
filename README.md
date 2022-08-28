# Activity-monitoring-Course-Project
This project is part of pre-graduate Assignment of "Data Science: Foundations using R Specialization" course

# Dataset

**-Dataset file** [Activity monitoring data](https://d396qusza40orc.cloudfront.net/repdata%2Fdata%2Factivity.zip)

**-Description :**

This data from a personal activity monitoring device. This device collects data at 5 minute intervals through out the day. The data consists of two months of data from an anonymous individual collected during the months of October and November, 2012 and include the number of steps taken in 5 minute intervals each day.

# Descriptions of the variables

**steps:** Number of steps taking in a 5-minute interval (missing values are coded as NA)

**date:** The date on which the measurement was taken in YYYY-MM-DD format

**interval:** Identifier for the 5-minute interval in which measurement was taken

**First:** loading the packages we will used through the project

```
library(tidyverse)
```

**Reading the data**

```
activity<-read.csv("activity.csv")
```

The first question we need to answer is : **What is mean total number of steps taken per day?**

we will make a new table that group the total of steps taken per day

```
activity_day<-activity %>% group_by(date)%>% summarize(total=sum(steps))
```

report of the total steps taken per day

```
summary(activity_day$total)
```

**outputs**

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
     41    8841   10765   10766   13294   21194       8 
```

plotting histogram of total steps taken per day

```
ggplot(data = activity_day,aes(total)) + geom_histogram()
```

![plot1](https://user-images.githubusercontent.com/41892582/187039681-15dd4884-9e95-426a-81a5-3d97ff345ea9.png)

The second question we need to answer is : **What is the average daily activity pattern?**

we will make a new table that group the average of steps taken interval 

```
min<-activity %>% group_by(interval) %>% summarize(avg_steps=mean(steps,na.rm=TRUE))
```

Which 5-minute interval, on average across all the days in the dataset, contains the maximum number of steps?

```
max_avg_steps_location<-which.max(min$avg_steps)
max_avg_steps<-min[max_avg_steps_location,2]
max_avg_interval<-min[max_avg_steps_location,1]

ggplot(data = min,aes(interval,avg_steps))+geom_line()+geom_hline(yintercept =as.numeric(max_avg_steps),col="red")+geom_vline(xintercept =as.numeric(max_avg_interval),col="blue")
```

![plot2](https://user-images.githubusercontent.com/41892582/187040108-56e21ebf-1c09-42ee-bf51-64a3917fdc65.png)

# Dealing with missing values

first we want to know how many missing values are there

```
colSums(is.na(activity))
```

**outputs**

```
   steps     date interval 
    2304        0        0 
```

there is a 2304 missing values in a steps field the strategy that we will dealing with those missing values is with filling them with the mean value of its 5-minute intervals

```
new_activity<-data.frame(activity$steps,activity$date,activity$interval,min$avg_steps)

new_activity$activity.steps[is.na(new_activity$activity.steps)]<-new_activity$min.avg_steps[]
```

we will make a new table that group the total of steps taken per day

```
new_activity_day<-new_activity %>% group_by(activity.date) %>% summarize(avg_steps_new=mean(activity.steps))
```
plotting histogram of the new data

```
ggplot(data = new_activity_day,aes(total_steps_new)) + geom_histogram()
```

![plot3](https://user-images.githubusercontent.com/41892582/187045688-5f55423c-fc3c-43ce-a2d3-f869a9dea459.png)

report of the total steps taken per day

```
summary(new_activity_day$total_steps_new)
```

**outputs**

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
     41    9819   10766   10766   12811   21194 
```

comparing with the original data we find that the mean and median nearly didn't change, so our dealing with NAs is sophisticated

The fourth question we need to answer is : Are there differences in activity patterns between weekdays and weekends?

create a new data called "activity2" with new vqriable "weekday" describe the day of the week

```
activity2<-activity
activity2$weekdays<-weekdays(as.Date(activity2$date))
```

adding new variable "weekend" discribe the day weekend or week day

```
activity2$weekend<-ifelse(activity2$weekdays=="Saturday"|activity2$weekdays=="Sunday" ,"weekend","weekday")
```


```
activity2_week_total<-activity2 %>% group_by(date,weekend) %>% summarise(total_steps=sum(steps))
```

we will make a new table that group the total of steps taken per day

```
activity2_week_total<-activity2 %>% group_by(date,weekend) %>% summarise(total_steps=sum(steps))
```

split the data to two data weekend or weekday

```
activity2_weekend_total<-activity2_week_total%>% filter(weekend=="weekend")
activity2_weekday_total<-activity2_week_total%>% filter(weekend=="weekday")
```

comparing the two data

```
summary(activity2_weekend_total$total_steps)
summary(activity2_weekday_total$total_steps)
```

**outputs**

```
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
   8821   10682   12130   12407   14443   15420       2 

   Min. 1st Qu.  Median    Mean 3rd Qu.    Max.    NA's 
     41    7835   10304   10177   12847   21194       6 
```

```
ggplot(data =activity2_week_total,aes(as.Date(date),total_steps))+geom_line()+facet_grid(weekend ~ .)+xlab("The day")
```

![plot4](https://user-images.githubusercontent.com/41892582/187053848-f322a41d-9a6a-4645-ad53-015b58e54889.png)
