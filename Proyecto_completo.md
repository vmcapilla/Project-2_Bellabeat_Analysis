# Complete Project 2: Bellabeat: Female Beauty Company: Sleep, Activity and BMI Analysis

# Scenario
You are a junior data analyst working on the marketing analyst team at Bellabeat, a high-tech manufacturer of health-focused products for women. Bellabeat is a successful small company, but they have the potential to become a larger player in the global smart device market. Urška Sršen, cofounder and Chief Creative Officer of Bellabeat, believes that analyzing smart device fitness data could help unlock new growth opportunities for the company. You have been asked to focus on one of Bellabeat’s products and analyze smart device data to gain insight into how consumers are using their smart devices. The insights you discover will then help guide marketing strategy for the company. You will present your analysis to the Bellabeat executive team along with your high-level recommendations for Bellabeat’s marketing strategy.
# About the company
Urška Sršen and Sando Mur founded Bellabeat, a high-tech company that manufactures health-focused smart products. Sršen used her background as an artist to develop beautifully designed technology that informs and inspires women around the world. Collecting data on activity, sleep, stress, and reproductive health has allowed Bellabeat to empower women with knowledge about their own health and habits. Since it was founded in 2013, Bellabeat has grown rapidly and quickly positioned itself as a tech-driven wellness company for women. By 2016, Bellabeat had opened offices around the world and launched multiple products. Bellabeat products became available through a growing number of online retailers in addition to their own e-commerce channel on their website. The company has invested in traditional advertising media, such as radio, out-of-home billboards, print, and television, but focuses on digital marketing extensively. Bellabeat invests year-round in Google Search, maintaining active Facebook and Instagram pages, and consistently engages consumers on Twitter. Additionally, Bellabeat runs video ads on Youtube and display ads on the Google Display Network to support campaigns around key marketing dates. Sršen knows that an analysis of Bellabeat’s available consumer data would reveal more opportunities for growth. She has asked the marketing analytics team to focus on a Bellabeat product and analyze smart device usage data in order to gain insight into how people are already using their smart devices. Then, using this information, she would like high-level recommendations for how these trends can inform Bellabeat marketing strategy.
# Main questions to answer
1. What are some trends in smart device usage?
2. How could these trends apply to Bellabeat customers?
3. How could these trends help influence Bellabeat marketing strategy?
# 1.0 Ask
After looking at the excels and thinking about the questions we are going to ask, I came with this ones:

1. How many users keep track of their weight?
2. How many steps on average per day do users take?
3. When do users sleep and how it the quality of it?
4. What quality of sleep do you get after doing each activity?
5. How do they exercise by BMI quantile?
I think these questions can inform us of the main data that can be obtained and give us information about how a user uses these resources. In all cases, the activity will be as a rank betheen 1 an 3, being 3 the most active activity. So, let´s import the data to the enviroment:
# 2.0 Prepare
For the activity, I've been given a very varied dataset. I've got csv for the days, minutes and seconds.I choose to use R in this activity because I want to get used to it and it's a very useful way.

We are going to load the R environment with the main libraries and we are going to open the files in excel to get an idea of what measures we have to focus on and what files we have to load in the environment
```
library(tidyverse)
library(dplyr)
library(tidyr)
library(ggplot2)
library(lubridate)
library(scales)
```
```
activity <- read.csv('../input/fitbit/Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv')
minute_sleep <- read.csv('../input/fitbit/Fitabase Data 4.12.16-5.12.16/minuteSleep_merged.csv')
day_sleep <- read.csv('../input/fitbit/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv')
weight <- read.csv('../input/fitbit/Fitabase Data 4.12.16-5.12.16/weightLogInfo_merged.csv')
```
# 3.0 Process
```
str(activity)
str(minute_sleep)
str(day_sleep)
str(weight)
```
We'll have to make some changes to the datatypes and the Id column as chr beacuse theres no numbers between them, they're user numbers.
```
activity$Id <- 
    as.character(activity$Id)

activity$ActivityDate <- 
    as.POSIXct(activity$ActivityDate,format="%m/%d/%Y")


minute_sleep$Id <- 
    as.character(minute_sleep$Id)

minute_sleep$date <- 
    as.POSIXct(minute_sleep$date,format="%m/%d/%Y%H:%M:%S")


day_sleep$Id <- 
    as.character(day_sleep$Id)

day_sleep$SleepDay <-
    as.POSIXct(day_sleep$SleepDay,format="%m/%d/%Y%H:%M:%S")


weight$Id <- 
    as.character(weight$Id)

weight$Date <- 
    as.POSIXct(weight$Date,format="%m/%d/%Y%H:%M:%S")

str(activity)
str(minute_sleep)
str(day_sleep)
str(weight)
```
# 4.0 Analysis
## 4.1 How many users keep track of their weight?
```
weight %>% 
    group_by(IsManualReport) %>%
    summarise(count=n()) %>%
    mutate(count_perc = formattable::percent (round(count / sum(count), 3)))
```
Wow! That's much more than I suspect, so... They're actively engaged with the app!
## 4.2 How many steps on average per day do users take?
```
mean(activity$TotalSteps)

ggplot(activity) +
    geom_col(aes(ActivityDate, TotalSteps, fill=Id)) +
    labs(title='Fig. 01: Steps by users', x='Day', y='Total Steps') + facet_wrap(~Id)

ggplot(activity) +
    geom_col(aes(ActivityDate, TotalSteps)) +
    labs(title='Fig. 02: Total steps by day', x='Day', y='Total Steps')
```
It makes me doubt to see the last bar so small, so I try to see what the final time of the project is and I find this: (I use data from another table because they have minutes and seconds to compare)
```
min(weight$Date)
max(weight$Date)

ggplot(activity) +
    geom_bar(aes(ActivityDate)) +
    labs(title='Fig. 03: Total users by day', x='Day', y='Users')
```
The start and end times of the data are not the same! There could be people exercising and they haven't counted their steps. And, in addition, we can see that there are users who have unsubscribed from the initial 30, but this however does not affect the average. (although they are more heavily overweighted at the end of the database)

To calculate the final average I will eliminate the last day and obtain the new average
```
activity_clean_date <-
activity %>% 
    filter (ActivityDate < max(activity$ActivityDate))

mean(activity_clean_date$TotalSteps)
```
## 4.3 When do users sleep and how it the quality of it?
### 4.3.1 When do they sleep?
```
minute_sleep$sleep_hours <- format(minute_sleep$date, "%H")

ggplot(minute_sleep) +
   geom_bar(aes(sleep_hours)) +
    labs(title='Fig. 04: Sleep by hour', x='Hour', y='Sum of min')
```
Well... that was predictable. Now let's see how they sleep by comparing the minutes asleep vs time in bed:
### 4.3.2 How do they sleep?
```
day_sleep_v02 <- 
    day_sleep %>% select(Id, SleepDay, TotalMinutesAsleep, TotalTimeInBed)

day_sleep_v02 %>% 
    pivot_longer(TotalMinutesAsleep:TotalTimeInBed)  %>%
    ggplot() + 
    geom_col(aes(SleepDay, value, fill=name), position ='dodge')  +
    labs(title='Fig. 05: Minutes Asleep vs Minutes in Bed', x='Day', y='Mins')

day_sleep_v02$NotSleeping <- day_sleep_v02$TotalTimeInBed - day_sleep_v02$TotalMinutesAsleep

summary(day_sleep_v02$NotSleeping)
```
Oh well, the median it's not much either. I think I can relate the max value :')
## 4.4 What quality of sleep do you get after doing each activity?
```
activity_n_sleep <- merge(activity, day_sleep_v02, by = "Id", all.y = TRUE) %>%
    select(-c(SleepDay ))

activity_n_sleep %>% 
    pivot_longer(VeryActiveDistance:LightActiveDistance)  %>%
    ggplot() + 
    geom_density(aes(NotSleeping, fill=name),position = "stack") +
    labs(title='Fig. 06: Density of minutes of no sleeping by activity record', x='Min of no sleeping', fill='Type of activity')
```
As we can see (and as expected), you are more likely to sleep well if you exercise and, if you do, the more active the better
## 4.5 How many user do we have by BMI and how do they exercise?
### 4.5.1 How many users do we have per quantile?
```
activity_n_weight <- merge(activity, weight, by = "Id", all.y = TRUE) %>%
    select(-c(Date ))
```
In order to know how to group the values, we've to check the upper and lower values:
```
min(activity_n_weight$BMI)
max(activity_n_weight$BMI)
```
Since it's got 26 intermediate values and the BMI quantiles go 5 though 5, we will divide it into 5 parts:

1. q1: Normal
2. q2: Over weight
3. q3: Obesity Class I
4. q4: Obesity Class II
5. q5: Extreme Obesity
```
activity_n_weight$BMI_qt <- as.numeric(cut(activity_n_weight$BMI, 5))
```
```
activity_n_weight %>% 
    group_by(BMI_qt) %>%
    summarise(perc_qt=n()) %>%
    mutate(perc_qt = formattable::percent (round(perc_qt / sum(perc_qt), 3))) 
```
It is a pity that we have a database with only 30 values and that they are grouped into so few quantiles, although it is normal that we have many more people in the 'normal' weight.
### 4.5.2 How do they exercise by BMI quantile?
```
activity_n_weight %>% 
    pivot_longer(VeryActiveDistance:LightActiveDistance)  %>%
    ggplot(aes(BMI_qt, value, col=BMI_qt, group=BMI_qt)) +
    geom_boxplot() +
    geom_jitter(alpha=0.05) +
    facet_wrap(~name) +
    labs(title='Fig. 07: Activity days by intensity and BMI quantiles', x='BMI quantiles', y='Activity days', col='BMI quantiles')
```
We can see that obviously people who are fitter tend to exercise more intensely. It is somewhat obvious from the condition of each group.

We could also calculate the calories burned by quantile, since, in that aspect, they are all more even:
```
activity_n_weight %>% 
    ggplot(aes(BMI_qt, Calories, col=BMI_qt, group=BMI_qt)) +
    geom_boxplot() +
    geom_jitter(alpha=0.05) +
    labs(title='Fig. 08: Calories burnt by BMI quantile', x='BMI quantiles', y='Calories', col='BMI quantiles')
```
# 5.0 Conclusions
To begin with, we are going to answer the questions in the questionnaire.

1. What are some trends in smart device usage?

  They are beginning to be used much more than before and, despite the fact that during the experiment there have been users who have stopped using it (fig.3), most of the weight imputations are manual, which denotes an interest on the part of the user to improve the statistics and be able to know the results (61% of the weight data is added by the users directly)

2. How could these trends apply to Bellabeat customers?

  We could offer you sleep data and statistics to improve it (like fig.6). It seems to me that consistency is something that we can all fail at some point and it would be nice if a device indicates the long-term benefits of meeting our goals.

  We can also offer goals by quantile so that they can see how they are progressing within their category and be able to compare themselves in a better way (differentiation as in fig.7 and comparison with all users as in fig.8)

3. How could these trends help influence Bellabeat marketing strategy?

  Bellabeat could make personalized plans for each user, with much more detailed attention and being able to offer you data to motivate you to meet your goals. (if you exercise today, you will sleep x% better on average this week) or focus on certain percentiles the marketing if you see that there are many more users of a certain type (it would be convenient to expand the dataset to be able to carry out these conclusions and study them one by one)







