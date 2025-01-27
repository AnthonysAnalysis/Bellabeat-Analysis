# Bellabeat Tech Capstone Project
#### Author: Anthony Tran
#### Date: 2024-12-20

# **About:**

Bellabeat is a small, successful manufacturer of health-focused tech
products for women. They are looking to expand their market share in the
global smart-device market.

I, as a junior data analyst on the marketing analyst team have been
tasked with working on one of Bellabeat’s products, their app which
connects to their many smart devices. We will be analyzing usage data on
one of Bellabeat’s competitor’s devices, the Fitbit, to gain insight
into how customers are using their smart devices, which will help create
data-driven decision making for the company’s marketing strategy for
growth.

Throughout the project, we will follow the six main steps of the data
analysis process:

**Ask, prepare, process, analyze, share, and act.**

# **Ask:**

Business task:

We will identify usage and data trends in the Fitbit, a handheld
wearable from one of Bellabeat’s competitors. We will run some data
analysis and acquire some insight into this smart device’s usage. Then,
figure out how to apply these insights into our own products to drive
Bellabeat’s marketing strategy via data-driven decision making.

# **Prepare:**

Datasource: Our dataset is the Fitbit Fitness Tracker Dataset (CC0:
Public Domain), via Mobius.

Accessibility and privacy: The data is open-sourced and licensed under
CC0: Public Domain in which the original owner relinquished all
copyright and similar rights worldwide, and dedicated those rights to
the public domain. Others can copy, modify, distribute and perform the
work, even for commercial purposes, all without asking permission.

Dataset information: This dataset contains data generated from ~ 33
Fitbit users respondents to an Amazon Mechanical Turk survey, who
consented to the submission of their personal tracker data from the
timeframe 04/12/2016 - 05/12/2016. Various different quantitative data
metrics include physical activity, heart rate, and sleep monitoring at
different levels of scope, including minute-output,hourly, and daily.
Variation between output represents use of different types of Fitbit
trackers and individual tracking behaviors / preferences.

Data organization: The data was made available via 18 .csv files, each
containing various different quantitative data metrics.The majority of
which is in long format, with 3 files in wide format. Some datasets were
excluded from analysis after verification due to being redundant data
already included in a more complete data table, or the sample size for
participant response was too small to generate significant insight.

Data limitations: Unknown demographics. We do not know various
demographics for our participants such as age or sex, which would be
important especially as it relates to our company which focuses on
women-centric products. Also, some of the data given are not clearly
labeled with the proper units, so we had to assume some standard units
to our best guess, given the context.

Small sample size and time frame: In statistics, the golden rule for
analysis is that carries the most power when the sample size is over 30,
the larger the better. For most of our data, we are barely at the bare
minimum so insight might not be able to be extrapolated strongly to a
larger population or timeframe.

# **Process**

Analysis will be carried out in programming language R, in Posit Cloud
due to ease of use for statistical computing, data visualization, and
sharing results with stakeholders.

## Setting up my environment

Notes: We’ll begin by installing and loading the necessary packages to
run our analysis:

tidyverse, dplyr, here, janitor, skimr, lubridate, tibble, and waffle.

``` r
install.packages("tidyverse")
install.packages("dplyr")
install.packages("here")
install.packages("janitor")
install.packages("skimr")
install.packages("lubridate")
install.packages("tibble")
install.packages("waffle")

library(tidyverse)
library(dplyr)
library(here)
library(janitor)
library(skimr)
library(lubridate)
library(tibble)
library(waffle)
```

## Importing our datasets

Notes: We need to import our various datasets, which are in the form of
a .csv, into data frames that we can work with into R.

``` r
daily_activity <- read.csv("dailyActivity_merged.csv", header = TRUE)
daily_calories <- read.csv("dailyCalories_merged.csv", header = TRUE)
daily_intensities <- read.csv("dailyIntensities_merged.csv", header = TRUE)
daily_steps <- read.csv("dailySteps_merged.csv", header = TRUE)
sleep_day <- read.csv("sleepDay_merged.csv", header = TRUE)
weight_log <- read.csv("weightLogInfo_merged.csv", header = TRUE)
hourly_steps <- read.csv("hourlySteps_merged.csv", header = TRUE)
hourly_intensity <- read.csv("hourlyIntensities_merged.csv", header = TRUE)
hourly_calories <- read.csv("hourlyCalories_merged.csv", header = TRUE)
```

## Previewing datasets

Notes: Getting a preview and examining the structure of our recently
imported datasets for preliminary analysis and planning.

``` r
str(daily_activity)
```

    ## 'data.frame':    940 obs. of  15 variables:
    ##  $ Id                      : num  1.5e+09 1.5e+09 1.5e+09 1.5e+09 1.5e+09 ...
    ##  $ ActivityDate            : chr  "4/12/2016" "4/13/2016" "4/14/2016" "4/15/2016" ...
    ##  $ TotalSteps              : int  13162 10735 10460 9762 12669 9705 13019 15506 10544 9819 ...
    ##  $ TotalDistance           : num  8.5 6.97 6.74 6.28 8.16 ...
    ##  $ TrackerDistance         : num  8.5 6.97 6.74 6.28 8.16 ...
    ##  $ LoggedActivitiesDistance: num  0 0 0 0 0 0 0 0 0 0 ...
    ##  $ VeryActiveDistance      : num  1.88 1.57 2.44 2.14 2.71 ...
    ##  $ ModeratelyActiveDistance: num  0.55 0.69 0.4 1.26 0.41 ...
    ##  $ LightActiveDistance     : num  6.06 4.71 3.91 2.83 5.04 ...
    ##  $ SedentaryActiveDistance : num  0 0 0 0 0 0 0 0 0 0 ...
    ##  $ VeryActiveMinutes       : int  25 21 30 29 36 38 42 50 28 19 ...
    ##  $ FairlyActiveMinutes     : int  13 19 11 34 10 20 16 31 12 8 ...
    ##  $ LightlyActiveMinutes    : int  328 217 181 209 221 164 233 264 205 211 ...
    ##  $ SedentaryMinutes        : int  728 776 1218 726 773 539 1149 775 818 838 ...
    ##  $ Calories                : int  1985 1797 1776 1745 1863 1728 1921 2035 1786 1775 ...

``` r
str(daily_calories)
```

    ## 'data.frame':    940 obs. of  3 variables:
    ##  $ Id         : num  1.5e+09 1.5e+09 1.5e+09 1.5e+09 1.5e+09 ...
    ##  $ ActivityDay: chr  "4/12/2016" "4/13/2016" "4/14/2016" "4/15/2016" ...
    ##  $ Calories   : int  1985 1797 1776 1745 1863 1728 1921 2035 1786 1775 ...

``` r
str(daily_intensities)
```

    ## 'data.frame':    940 obs. of  10 variables:
    ##  $ Id                      : num  1.5e+09 1.5e+09 1.5e+09 1.5e+09 1.5e+09 ...
    ##  $ ActivityDay             : chr  "4/12/2016" "4/13/2016" "4/14/2016" "4/15/2016" ...
    ##  $ SedentaryMinutes        : int  728 776 1218 726 773 539 1149 775 818 838 ...
    ##  $ LightlyActiveMinutes    : int  328 217 181 209 221 164 233 264 205 211 ...
    ##  $ FairlyActiveMinutes     : int  13 19 11 34 10 20 16 31 12 8 ...
    ##  $ VeryActiveMinutes       : int  25 21 30 29 36 38 42 50 28 19 ...
    ##  $ SedentaryActiveDistance : num  0 0 0 0 0 0 0 0 0 0 ...
    ##  $ LightActiveDistance     : num  6.06 4.71 3.91 2.83 5.04 ...
    ##  $ ModeratelyActiveDistance: num  0.55 0.69 0.4 1.26 0.41 ...
    ##  $ VeryActiveDistance      : num  1.88 1.57 2.44 2.14 2.71 ...

``` r
str(daily_steps)
```

    ## 'data.frame':    940 obs. of  3 variables:
    ##  $ Id         : num  1.5e+09 1.5e+09 1.5e+09 1.5e+09 1.5e+09 ...
    ##  $ ActivityDay: chr  "4/12/2016" "4/13/2016" "4/14/2016" "4/15/2016" ...
    ##  $ StepTotal  : int  13162 10735 10460 9762 12669 9705 13019 15506 10544 9819 ...

``` r
str(sleep_day)
```

    ## 'data.frame':    413 obs. of  5 variables:
    ##  $ Id                : num  1.5e+09 1.5e+09 1.5e+09 1.5e+09 1.5e+09 ...
    ##  $ SleepDay          : chr  "4/12/2016 12:00:00 AM" "4/13/2016 12:00:00 AM" "4/15/2016 12:00:00 AM" "4/16/2016 12:00:00 AM" ...
    ##  $ TotalSleepRecords : int  1 2 1 2 1 1 1 1 1 1 ...
    ##  $ TotalMinutesAsleep: int  327 384 412 340 700 304 360 325 361 430 ...
    ##  $ TotalTimeInBed    : int  346 407 442 367 712 320 377 364 384 449 ...

``` r
str(weight_log)
```

    ## 'data.frame':    67 obs. of  8 variables:
    ##  $ Id            : num  1.50e+09 1.50e+09 1.93e+09 2.87e+09 2.87e+09 ...
    ##  $ Date          : chr  "5/2/2016 11:59:59 PM" "5/3/2016 11:59:59 PM" "4/13/2016 1:08:52 AM" "4/21/2016 11:59:59 PM" ...
    ##  $ WeightKg      : num  52.6 52.6 133.5 56.7 57.3 ...
    ##  $ WeightPounds  : num  116 116 294 125 126 ...
    ##  $ Fat           : int  22 NA NA NA NA 25 NA NA NA NA ...
    ##  $ BMI           : num  22.6 22.6 47.5 21.5 21.7 ...
    ##  $ IsManualReport: chr  "True" "True" "False" "True" ...
    ##  $ LogId         : num  1.46e+12 1.46e+12 1.46e+12 1.46e+12 1.46e+12 ...

``` r
str(hourly_steps)
```

    ## 'data.frame':    22099 obs. of  3 variables:
    ##  $ Id          : num  1.5e+09 1.5e+09 1.5e+09 1.5e+09 1.5e+09 ...
    ##  $ ActivityHour: chr  "4/12/2016 12:00:00 AM" "4/12/2016 1:00:00 AM" "4/12/2016 2:00:00 AM" "4/12/2016 3:00:00 AM" ...
    ##  $ StepTotal   : int  373 160 151 0 0 0 0 0 250 1864 ...

``` r
str(hourly_intensity)
```

    ## 'data.frame':    22099 obs. of  4 variables:
    ##  $ Id              : num  1.5e+09 1.5e+09 1.5e+09 1.5e+09 1.5e+09 ...
    ##  $ ActivityHour    : chr  "4/12/2016 12:00:00 AM" "4/12/2016 1:00:00 AM" "4/12/2016 2:00:00 AM" "4/12/2016 3:00:00 AM" ...
    ##  $ TotalIntensity  : int  20 8 7 0 0 0 0 0 13 30 ...
    ##  $ AverageIntensity: num  0.333 0.133 0.117 0 0 ...

``` r
str(hourly_calories)
```

    ## 'data.frame':    22099 obs. of  3 variables:
    ##  $ Id          : num  1.5e+09 1.5e+09 1.5e+09 1.5e+09 1.5e+09 ...
    ##  $ ActivityHour: chr  "4/12/2016 12:00:00 AM" "4/12/2016 1:00:00 AM" "4/12/2016 2:00:00 AM" "4/12/2016 3:00:00 AM" ...
    ##  $ Calories    : int  81 61 59 47 48 48 48 47 68 141 ...

Upon examining the data frames and column names, we see that
daily_activities also contains the complete data set for daily_calories,
daily_intensities, and daily_steps as well.

So from now on when using the data for daily_calories,
daily_intensities, and daily_steps, I will reference their respective
columns from the daily_activity data frame for simplicity.

## Verifying sample size

Notes: Counting how many distinct participants we have for each dataset,
and whether it is appropriate to use for our analysis.

``` r
n_distinct(daily_activity$Id)
```

    ## [1] 33

``` r
n_distinct(daily_calories$Id)
```

    ## [1] 33

``` r
n_distinct(daily_intensities$Id)
```

    ## [1] 33

``` r
n_distinct(daily_steps$Id)
```

    ## [1] 33

``` r
n_distinct(sleep_day$Id)
```

    ## [1] 24

``` r
n_distinct(weight_log$Id)
```

    ## [1] 8

``` r
n_distinct(hourly_steps$Id)
```

    ## [1] 33

``` r
n_distinct(hourly_intensity$Id)
```

    ## [1] 33

``` r
n_distinct(hourly_calories$Id)
```

    ## [1] 33

In statistics, there is a “rule of 30,” where 30 is typically the
minimum sample size needed to make a statistically strong conclusion
about the data. So because of this I will discard the weight data, which
only has 8 people. Sleep has 24 participants, but I will still run a
preliminary analysis on it regardless, with that in consideration.

## Converting into proper date format

We also see from the previous section that the dates are in character
format, so we will need to convert to proper date or date/time format,
and hide the old columns.

This is so our packages will be able to properly run on the data in the
column.

``` r
daily_activity <- daily_activity %>%
  mutate(ActivityDate = mdy(ActivityDate))
hourly_steps <- hourly_steps %>%
  mutate(ActivityDateHour = mdy_hms(ActivityHour)) %>%
  select(-ActivityHour)
hourly_intensity <- hourly_intensity %>%
  mutate(ActivityDateHour = mdy_hms(ActivityHour)) %>%
  select(-ActivityHour)
hourly_calories <- hourly_calories %>%
  mutate(ActivityDateHour = mdy_hms(ActivityHour)) %>%
  select(-ActivityHour)
sleep_day <- sleep_day %>%
  mutate(SleepDate = as.Date(mdy_hms(SleepDay))) %>% 
  select(-SleepDay)
```

## Verifying the values were properly changed from character to date & date/time.

``` r
class(daily_activity$ActivityDate)
```

    ## [1] "Date"

``` r
class(hourly_steps$ActivityDateHour)
```

    ## [1] "POSIXct" "POSIXt"

``` r
class(hourly_intensity$ActivityDateHour)
```

    ## [1] "POSIXct" "POSIXt"

``` r
class(hourly_calories$ActivityDateHour)
```

    ## [1] "POSIXct" "POSIXt"

``` r
class(sleep_day$SleepDate)
```

    ## [1] "Date"

## Cleaning the data

Notes: Each data frame was manually imported and assigned the
appropriate naming convention by me in snakecase.

Next we will check for errors and inconsistencies in the data by
checking for duplicated records and missing values.

``` r
sum(duplicated(daily_activity))
```

    ## [1] 0

``` r
sum(duplicated(sleep_day))
```

    ## [1] 3

``` r
sum(duplicated(hourly_steps))
```

    ## [1] 0

``` r
sum(duplicated(hourly_calories))
```

    ## [1] 0

``` r
sum(duplicated(hourly_intensity))
```

    ## [1] 0

``` r
sum(is.na(daily_activity))
```

    ## [1] 0

``` r
sum(is.na(sleep_day))
```

    ## [1] 0

``` r
sum(is.na(hourly_steps))
```

    ## [1] 0

``` r
sum(is.na(hourly_calories))
```

    ## [1] 0

``` r
sum(is.na(hourly_intensity))
```

    ## [1] 0

sleep_day has 3 duplicated records. All other datasets passed the
initial check.

## Get rid of duplicated observations, and drop missing values from data frames as needed.

``` r
daily_activity <- daily_activity %>%
  distinct %>%
  drop_na
sleep_day <-sleep_day %>%
  distinct %>%
  drop_na
hourly_steps <- hourly_steps %>%
  distinct %>%
  drop_na
hourly_calories <- hourly_calories %>%
  distinct %>%
  drop_na
hourly_intensity <- hourly_intensity %>%
  distinct %>%
  drop_na
```

## Merge to create a new combined data frame, and verify distinct users.

``` r
daily_activity_sleep <- merge(daily_activity, sleep_day, by.x = c("Id", "ActivityDate"), by.y = c("Id", "SleepDate"), all = TRUE)

n_distinct(daily_activity_sleep$Id)
```

    ## [1] 33

Here we are creating a newly merged data frame for one of our analyses
that we are going to run.

# **Analyze & Share:**

## Basic overview summary:

Notes: Some basic preliminary summary analysis

``` r
daily_activity %>%
  select(TotalSteps, Calories, SedentaryMinutes) %>%
  summary()
```

    ##    TotalSteps       Calories    SedentaryMinutes
    ##  Min.   :    0   Min.   :   0   Min.   :   0.0  
    ##  1st Qu.: 3790   1st Qu.:1828   1st Qu.: 729.8  
    ##  Median : 7406   Median :2134   Median :1057.5  
    ##  Mean   : 7638   Mean   :2304   Mean   : 991.2  
    ##  3rd Qu.:10727   3rd Qu.:2793   3rd Qu.:1229.5  
    ##  Max.   :36019   Max.   :4900   Max.   :1440.0

``` r
sleep_day %>%
  select(TotalSleepRecords, TotalMinutesAsleep, TotalTimeInBed) %>%
  summary()
```

    ##  TotalSleepRecords TotalMinutesAsleep TotalTimeInBed 
    ##  Min.   :1.00      Min.   : 58.0      Min.   : 61.0  
    ##  1st Qu.:1.00      1st Qu.:361.0      1st Qu.:403.8  
    ##  Median :1.00      Median :432.5      Median :463.0  
    ##  Mean   :1.12      Mean   :419.2      Mean   :458.5  
    ##  3rd Qu.:1.00      3rd Qu.:490.0      3rd Qu.:526.0  
    ##  Max.   :3.00      Max.   :796.0      Max.   :961.0

``` r
hourly_steps %>%
  select(StepTotal) %>%
  filter(StepTotal != 0) %>%
  summary()
```

    ##    StepTotal      
    ##  Min.   :    1.0  
    ##  1st Qu.:  103.0  
    ##  Median :  287.0  
    ##  Mean   :  552.7  
    ##  3rd Qu.:  643.0  
    ##  Max.   :10554.0

``` r
hourly_calories %>%
  select(Calories) %>%
  summary()
```

    ##     Calories     
    ##  Min.   : 42.00  
    ##  1st Qu.: 63.00  
    ##  Median : 83.00  
    ##  Mean   : 97.39  
    ##  3rd Qu.:108.00  
    ##  Max.   :948.00

``` r
hourly_intensity %>%
  select(TotalIntensity, AverageIntensity) %>%
  summary()
```

    ##  TotalIntensity   AverageIntensity
    ##  Min.   :  0.00   Min.   :0.0000  
    ##  1st Qu.:  0.00   1st Qu.:0.0000  
    ##  Median :  3.00   Median :0.0500  
    ##  Mean   : 12.04   Mean   :0.2006  
    ##  3rd Qu.: 16.00   3rd Qu.:0.2667  
    ##  Max.   :180.00   Max.   :3.0000

-   The Center for Disease Control (CDC) recommends that the average
    adult take around 8,000-10,000+ steps per day. Compared to our data
    showing the mean and median daily steps at 7638 and 7406,
    respectively. Adults are not being as active as they should be. One
    premature recommendation is a device feature reminding people to get
    up and be active/walk around once in a while, after a certain amount
    of sedentary minutes has elapsed.
-   The National Institute of Health (NIH) recommends that adults get
    between 7 and 9 hours of sleep per night. The average sleep time for
    our group here is just around 7 hours, barely at the lower end of
    the minimum. The data shows that this sample does not meet the
    recommended goal. We can use this as an opportunity to add a feature
    to our device that reminds users to go to bed at a reasonable time,
    given what time they wake up. As well as add in these
    recommendations from government institutions as a credible source to
    help persuade people to comply.
-   The average number of hourly steps during waking hours for the
    participants in our study is 552.7 steps, with a median of 287.0.
    This indicates the data is skewed to the right.
-   The average number of hourly calories burned for our study
    participants is ~98 calories.
-   The median hourly total intensity for the FitBit users is 3.00, with
    a mean of 12.04, also indicating the data is skewed right.

## Let days where users used at least one functionality of the device count as an “active day.”

Out of a possible 31 days.

Let us classify usage days as:

-   0-10 Low usage

-   11-20 Medium usage

-   21-31 High usage

``` r
usage_counts <- daily_activity_sleep %>%
  group_by(Id) %>%
  summarise(days_active = n_distinct(ActivityDate)) %>%
  mutate(device_usage = case_when(
    days_active >= 0 & days_active <= 10 ~ "Low Usage",
    days_active >= 11 & days_active <= 20 ~ "Medium Usage",
    days_active >= 21 & days_active <= 31 ~ "High Usage"
  )) %>%
  group_by(device_usage) %>%
  count(device_usage)
  
print(usage_counts)
```

    ## # A tibble: 3 × 2
    ## # Groups:   device_usage [3]
    ##   device_usage     n
    ##   <chr>        <int>
    ## 1 High Usage      29
    ## 2 Low Usage        1
    ## 3 Medium Usage     3

``` r
  waffle_data <- setNames(usage_counts$n, usage_counts$device_usage)
waffle(waffle_data,
       rows = 3,
       xlab = "1 Square = 1 User",
       title = "Device Usage Frequency by Total Days",
       colors = c("green", "red", "yellow"),
       size = 1,
       legend_pos = "right")
```

![](https://github.com/AnthonysAnalysis/Data-Analytics-Portfolio/blob/main/Images/1.png)

-   The majority of our sample participants have engaged with their
    device at least once per day throughout our study period, most
    fitting into our criteria of “high usage,” which is at least over
    2/3rds of the total time frame of 31 days.
-   We can market our more premium subscription tiers for the high usage
    class who are clearly into their health metrics. We can also do this
    with different price plans and types to capture the different ways
    people prefer to pay, as well as for those hesitant. Possibly one
    that offers more metrics, recommendations, and reminders based on
    different tiers.
-   Can we determine how we can get the low/medium usage groups to
    engage more often?
-   What are the reasons behind the low usage group and is it worth
    looking into?

## Visualizations

What else can we interpret from creating some visuals?

``` r
calories_distance_model <- lm(TotalDistance ~ Calories, data = na.omit(daily_activity))
calories_distance_r_squared <- summary(calories_distance_model)$r.squared
ggplot(data = na.omit(daily_activity), aes(x = Calories, y = TotalDistance)) + 
  geom_point(color = "blue") + 
  geom_smooth(method = "lm", color = "yellow") +
  labs(title = "Longer Distances Walked Leads to More Calories Burned",
       x = "Daily Calories Burned",
       y = "Total Daily Distance Walked") +
  theme_minimal() +
  annotate("text", x = max(na.omit(daily_activity)$Calories) * 0.7, 
           y = max(na.omit(daily_activity)$TotalDistance) * 0.9, 
           label = paste("R² = ", round(calories_distance_r_squared, 3)), size = 5, color = "black")
```

    ## `geom_smooth()` using formula = 'y ~ x'

![](https://github.com/AnthonysAnalysis/Data-Analytics-Portfolio/raw/main/Images/2.png)

-   The general trend here is that there is a positive correlation
    between total daily distance walked and daily calories burned. We
    can include this information in an pop-up reminder for the user to
    be aware of, if that’s their goal.

``` r
sleep_day_of_week <- sleep_day %>% 
  mutate(day_of_week = wday(SleepDate, label = TRUE, abbr = FALSE))
average_sleep_per_day <- sleep_day_of_week %>%
  group_by(day_of_week) %>%
  summarise(average_sleep_time = mean(TotalMinutesAsleep, na.rm = TRUE)) %>%
  arrange(match(day_of_week, c("Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday")))
overall_mean_sleep <- mean(average_sleep_per_day$average_sleep_time)
ggplot(average_sleep_per_day, aes(x = day_of_week, y = average_sleep_time, fill = day_of_week)) +
  geom_col() + 
  geom_hline(yintercept = overall_mean_sleep, linetype = "dashed", color = "red") +
  labs( title = "Why Do People Sleep More on Wednesdays?", x = "Day of the Week", y = "Average Time Asleep (minutes)") + 
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

![](https://github.com/AnthonysAnalysis/Data-Analytics-Portfolio/raw/main/Images/3.png)

-   It looks like people sleep slightly more on the weekend on average.
    This is expected as people are recovering from the workweek and tend
    to sleep in.

-   Possible point of future analysis is why it appears people on
    average also sleep more on Wednesdays as well.

``` r
hourly_calories_by_hour <- hourly_calories %>% 
  mutate(hour_of_day = hour(ActivityDateHour))
average_hourly_calories <- hourly_calories_by_hour %>% 
  group_by(hour_of_day) %>% 
  summarise(average_calories = mean(Calories, na.rm = TRUE)) %>% 
  arrange(hour_of_day)
ggplot(average_hourly_calories, aes(x = hour_of_day, y = average_calories, fill = factor(hour_of_day))) + 
  geom_bar(stat = "identity", show.legend = FALSE) +
  labs(title = "Most Hourly Calories Are Burned During Active Hours",
       x = "Hour of Day",
       y = "Average Calories Burned" ) +
  theme_minimal() +
  scale_x_continuous(breaks = 0:23) +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

![](https://github.com/AnthonysAnalysis/Data-Analytics-Portfolio/raw/main/Images/4.png)

-   The most active hours of the day are during the waking hours.
-   Most people wake up around 6-7am for the day, with the calories
    burned gradually ramping up hourly until they get off of work at
    around 4-5pm. Then presumably they would go take care of other
    miscellaneous things for a little while, before hourly calories
    burned comes back down towards the end of the day.

``` r
hourly_intensity_by_hour <- hourly_intensity %>%
  mutate( hour_of_day = hour(ActivityDateHour),
          day_type = if_else(wday(ActivityDateHour, label = TRUE) %in% c("Sat", "Sun"), "Weekend", "Weekday"))
average_hourly_intensity_by_weekend_weekday <- hourly_intensity_by_hour %>%
  group_by(day_type, hour_of_day) %>% summarise(mean_intensity_hour = mean(TotalIntensity, na.rm = TRUE)) %>% 
  arrange(factor(day_type, levels = c("Weekday", "Weekend")), hour_of_day)
```

    ## `summarise()` has grouped output by 'day_type'. You can override using the
    ## `.groups` argument.

``` r
ggplot(average_hourly_intensity_by_weekend_weekday, aes(x = hour_of_day, y = mean_intensity_hour, fill = day_type)) +
  geom_bar(stat = "identity", position = "dodge") +
  labs( title = "People's Routines Differ on Weekdays vs Weekends", 
        x = "Hour of Day",
        y = "Mean Intensity Value" ) +
  scale_x_continuous(breaks = 0:23) +
  scale_fill_manual(values = c("Weekday" = "blue", "Weekend" = "pink")) +
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

![](https://github.com/AnthonysAnalysis/Data-Analytics-Portfolio/raw/main/Images/5.png)

-   People have different schedules/routines on the weekend vs weekday.
-   We can take this into consideration when designing our app features.

``` r
distance_calories_model <- lm(Calories ~ TotalDistance, data = daily_activity)
distance_calories_r_squared <- summary(distance_calories_model)$r.squared
ggplot(daily_activity, aes(x = TotalDistance, y = Calories)) +
  geom_point(alpha = 0.6, color = "blue") +  
  geom_smooth(method = "lm", color = "red", linewidth = 1) +  
  labs(title = "A Positive Correlation Between Daily Distance and Calories",
       x = "Total Distance",
       y = "Calories") +
  annotate("text", x = max(daily_activity$TotalDistance) * 0.7, 
           y = max(daily_activity$Calories) * 0.9, 
           label = paste("R-squared = ", round(distance_calories_r_squared, 3)), 
           color = "black", size = 5, fontface = "bold") +  
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

    ## `geom_smooth()` using formula = 'y ~ x'

![](https://github.com/AnthonysAnalysis/Data-Analytics-Portfolio/raw/main/Images/6.png)

``` r
steps_calories_model <- lm(Calories ~ TotalSteps, data = daily_activity)
steps_calories_r_squared <- summary(steps_calories_model)$r.squared
ggplot(daily_activity, aes(x = TotalSteps, y = Calories)) +
  geom_point(alpha = 0.6, color = "blue") +  
  geom_smooth(method = "lm", color = "red", linewidth = 1) +  
  labs(title = "Positive Correlation of Steps vs Calories",
       x = "Total Steps",
       y = "Calories") +
  annotate("text", x = max(daily_activity$TotalSteps) * 0.7, 
           y = max(daily_activity$Calories) * 0.9, 
           label = paste("R-squared = ", round(steps_calories_r_squared, 3)), 
           color = "black", size = 5, fontface = "bold") +  
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))
```

    ## `geom_smooth()` using formula = 'y ~ x'

![](https://github.com/AnthonysAnalysis/Data-Analytics-Portfolio/raw/main/Images/7.png)

-   Both these metrics have similar predictive correlations for calories
    burned.

``` r
steps_distance_model <- lm(TotalDistance ~ TotalSteps, data = daily_activity)
steps_distance_r_squared <- summary(steps_distance_model)$r.squared
ggplot(daily_activity, aes(x = TotalSteps, y = TotalDistance)) +
  geom_point(alpha = 0.6, color = "blue") +  
  geom_smooth(method = "lm", color = "red", linewidth = 1) +  
  labs(title = "Closely Correlated As Expected",
       x = "Total Daily Steps",
       y = "Total Daily Distance") +
  annotate("text", x = max(daily_activity$TotalSteps) * 0.7, 
           y = max(daily_activity$TotalDistance) * 0.9, 
           label = paste("R-squared = ", round(steps_distance_r_squared, 3)), 
           color = "black", size = 5, fontface = "bold") +  
  theme_minimal() +
  theme(axis.text.x = element_text(angle = 45, hjust = 1))  
```

    ## `geom_smooth()` using formula = 'y ~ x'

![](https://github.com/AnthonysAnalysis/Data-Analytics-Portfolio/raw/main/Images/8.png)

-   Both daily steps and total distance are a good predictor for daily
    calories burned.

``` r
average_hourly_steps_intensity_merged <- hourly_steps %>%
  merge(hourly_intensity, by = "ActivityDateHour") %>%
  group_by(hour = hour(ActivityDateHour)) %>%
  summarise(
    average_hourly_steps = mean(StepTotal, na.rm = TRUE), 
    average_hourly_intensity = mean(TotalIntensity, na.rm = TRUE)
  ) %>%
  arrange(hour)


ggplot(average_hourly_steps_intensity_merged, aes(x = hour)) +
  geom_line(aes(y = average_hourly_steps, color = "Average Hourly Steps"), linewidth = 1) +
  geom_line(aes(y = average_hourly_intensity * 100, color = "Average Hourly Intensity"), linewidth = 1) +
  scale_y_continuous(
    name = "Average Hourly Steps",
    sec.axis = sec_axis(~ . / 100, name = "Average Hourly Intensity (Scaled x100)")
  ) +
  labs(
    title = "People Are Most Active Mid-day", 
    x = "Hour of Day"
  ) +
  theme_minimal() +
  theme(
    axis.text.x = element_text(angle = 45, hjust = 1), 
    axis.title.y.right = element_text(color = "blue"),
    axis.text.y.right = element_text(color = "blue"), 
    axis.title.y.left = element_text(color = "green"),
    axis.text.y.left = element_text(color = "green")
  ) +
  scale_color_manual(values = c("Average Hourly Steps" = "green", "Average Hourly Intensity" = "blue")) +
  theme(legend.title = element_blank())
```

![](https://github.com/AnthonysAnalysis/Data-Analytics-Portfolio/raw/main/Images/9.png)

-   Both hourly steps and intensity follow a similar trend of ramping up
    mid-day.

``` r
percent_time_in_bed_sleeping <- sleep_day %>%
  summarise( sum_total_minutes_asleep = sum(TotalMinutesAsleep, na.rm = TRUE),
             sum_total_time_in_bed = sum(TotalTimeInBed, na.rm = TRUE),
             percentage_asleep = (sum_total_minutes_asleep / sum_total_time_in_bed) * 100,
             percentage_awake = 100 - percentage_asleep)

percent_sleep_pie <- data.frame(
  category = c("Time In Bed Asleep", "Time In Bed Awake"), 
  percentage = c(percent_time_in_bed_sleeping$percentage_asleep,
                 percent_time_in_bed_sleeping$percentage_awake) )

ggplot(percent_sleep_pie, aes(x = "", y = percentage, fill = category)) +
  geom_bar(stat = "identity", width = 1) +
  coord_polar(theta = "y") +
  labs(title = "Are Users Wasting Time Lounging Around In Bed?") + 
  theme_void() +  # Remove background grid
  scale_fill_manual(values = c("skyblue", "salmon")) +  
  geom_text(aes(label = paste0(round(percentage, 1), "%")),  
            position = position_stack(vjust = 0.5),  
            color = "black", size = 6)
```

![](https://github.com/AnthonysAnalysis/Data-Analytics-Portfolio/raw/main/Images/10.png)

-   Here we have percent time spent sleeping as a function of total time
    spent in bed, on average between all individual users.

-   As a whole, it looks like users are not getting distracted and
    having a hard time falling asleep.

-   If they did spend more time awake in bed, we could possibly
    implement a feature to try and remind users to stop all activity and
    focus on trying to sleep if they want to hit their scheduled hours
    of sleep goal, for those who need or want it.

# **Act:**

## Recommendations & final thoughts:

Marketing and branding:

-   Work on a marketing campaign that reinvents our brand as more than
    just a product, but as a lifestyle. That if you use our product,
    you’re a part of an exclusive group with a company that knows and
    understands you. A brand that empowers a group whose concerns have
    been historically overlooked. We offer women focused recommendations
    that no other company such as Fitbit can.

Expand products offerings:

-   From analyzing our Fitbit smart device usage frequency, we saw that
    29/33 users were categorized as “high frequency” users in which they
    used their device over ~2/3 the individual days of the total study
    period. They are clearly interested in the smart device tech field,
    so we can market to and offer them other similar products as well.
    We would conduct market research to figure out what this demographic
    would also be interested in and go from there.

-   By offering more products, a strong selling point to purchase more
    of our products would be seamless integration across products from
    our brand ecosystem, as well as possible cost savings under one
    subscription membership. This would incentivize customers to stay
    loyal to our brand, rather than exploring our competitors.

Expand current product features:

-   Research other features that customers in this segment would be
    into, and determine whether to offer it as a new product or add into
    an existing product.

-   Offer a connectedness feature. Give users the opportunity to share
    to social media, or other “Bellabeat friends” when they achieve a
    certain goal or milestone.

-   Add an “educational feature” that gives recommendations from
    credible sources such as healthy lifestyle habits, such as the
    Center for Disease Control (CDC) or National Institute of Health
    (NIH). We saw from our analysis that a good portion of adults from
    our study barely met the daily recommended amount of steps and sleep
    hours on the lower end.

-   For recommendations such as the above, implement a reminder feature
    that allows users to set a goal, with appropriate reminders to help
    users reach that goal if it appears they are not on track.

Diversity distribution channels:

-   Bellabeat currently only sells their products through online
    retailers and their own website. An easy way to increase our reach
    and market share is to extend our distribution channels. We could
    begin offering our products in physical retail stores as well.
    Bellabeat being a women-centric company, could further capitalize on
    this by partnering with other women focused retail stores to reach
    their target demographic easier.

Future analysis:

-   If given the opportunity, I’d like to run a future analysis on
    Bellabeat’s data itself, instead of relying on Fitbit’s data and
    extrapolating to our company, as the differing demographics may be a
    point of weak validity of this analysis. By analyzing Bellabeat’s
    data, we would also get insight into some metrics that only our
    women-centric products captured, and not the Fitbit, such as the
    reproductive cycle.

-   Trends for various metrics may also be different for women vs a
    mixed, unknown demographic such as that of the Fitbit data.
