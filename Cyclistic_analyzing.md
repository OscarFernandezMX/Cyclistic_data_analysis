Cyclistic data analysis - analyzing
================
Oscar Fernandez Solano
2024-02-12

After concluding the cleaning phase ([part
1](https://github.com/OscarFernandezMX) and [part
2](https://github.com/OscarFernandezMX)), we can start analyzing the
data.

We use the following libraries.

``` r
library(tidyverse)
```

    ## ── Attaching core tidyverse packages ──────────────────────── tidyverse 2.0.0 ──
    ## ✔ dplyr     1.1.3     ✔ readr     2.1.4
    ## ✔ forcats   1.0.0     ✔ stringr   1.5.0
    ## ✔ ggplot2   3.4.3     ✔ tibble    3.2.1
    ## ✔ lubridate 1.9.3     ✔ tidyr     1.3.0
    ## ✔ purrr     1.0.2     
    ## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
    ## ✖ dplyr::filter() masks stats::filter()
    ## ✖ dplyr::lag()    masks stats::lag()
    ## ℹ Use the conflicted package (<http://conflicted.r-lib.org/>) to force all conflicts to become errors

``` r
library(skimr)
library(hms)
```

    ## 
    ## Attaching package: 'hms'
    ## 
    ## The following object is masked from 'package:lubridate':
    ## 
    ##     hms

``` r
library(scales)
```

    ## 
    ## Attaching package: 'scales'
    ## 
    ## The following object is masked from 'package:purrr':
    ## 
    ##     discard
    ## 
    ## The following object is masked from 'package:readr':
    ## 
    ##     col_factor

Next we set the working directory, the data path, and read the proper
csv file.

``` r
setwd("~/GitHub/Data_Science_Projects")
data_path = "Data/cyclistic/"
file_name = paste0(data_path, "2023_cyclistic_tripdata_downsampled.csv")
# There is a problem when exporting the data to csv where some values for ride length are missing. Therefore, we create this row again after loading the data.
# When reading the days, the format has changed, therefore we create the day_of_week column again.
df <- read_csv(file_name) %>%
  mutate(
    ride_length = as_hms(ended_at - started_at),
    day_of_week = format(as.Date(started_at), "%A")
    )
```

    ## Warning: One or more parsing issues, call `problems()` on your data frame for details,
    ## e.g.:
    ##   dat <- vroom(...)
    ##   problems(dat)

    ## Rows: 3062290 Columns: 15
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (8): ride_id, rideable_type, start_station_name, start_station_id, end_...
    ## dbl  (4): start_lat, start_lng, end_lat, end_lng
    ## dttm (2): started_at, ended_at
    ## time (1): ride_length
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
skim_without_charts(df)
```

|                                                  |         |
|:-------------------------------------------------|:--------|
| Name                                             | df      |
| Number of rows                                   | 3062290 |
| Number of columns                                | 15      |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |         |
| Column type frequency:                           |         |
| character                                        | 8       |
| difftime                                         | 1       |
| numeric                                          | 4       |
| POSIXct                                          | 2       |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |         |
| Group variables                                  | None    |

Data summary

**Variable type: character**

| skim_variable      | n_missing | complete_rate | min | max | empty | n_unique | whitespace |
|:-------------------|----------:|--------------:|----:|----:|------:|---------:|-----------:|
| ride_id            |         0 |             1 |  16 |  16 |     0 |  3062290 |          0 |
| rideable_type      |         0 |             1 |  11 |  13 |     0 |        3 |          0 |
| start_station_name |         0 |             1 |   3 |  64 |     0 |     1518 |          0 |
| start_station_id   |         0 |             1 |   3 |  35 |     0 |     1455 |          0 |
| end_station_name   |         0 |             1 |   3 |  64 |     0 |     1539 |          0 |
| end_station_id     |         0 |             1 |   3 |  36 |     0 |     1470 |          0 |
| member_casual      |         0 |             1 |   6 |   6 |     0 |        2 |          0 |
| day_of_week        |         0 |             1 |   6 |   9 |     0 |        7 |          0 |

**Variable type: difftime**

| skim_variable | n_missing | complete_rate | min    | max         | median   | n_unique |
|:--------------|----------:|--------------:|:-------|:------------|:---------|---------:|
| ride_length   |         0 |             1 | 1 secs | 728178 secs | 623 secs |    19351 |

**Variable type: numeric**

| skim_variable | n_missing | complete_rate |   mean |   sd |     p0 |    p25 |    p50 |    p75 |   p100 |
|:--------------|----------:|--------------:|-------:|-----:|-------:|-------:|-------:|-------:|-------:|
| start_lat     |         0 |             1 |  41.90 | 0.04 |  41.65 |  41.88 |  41.90 |  41.93 |  42.06 |
| start_lng     |         0 |             1 | -87.64 | 0.03 | -87.84 | -87.66 | -87.64 | -87.63 | -87.53 |
| end_lat       |         0 |             1 |  41.90 | 0.06 |   0.00 |  41.88 |  41.90 |  41.93 |  42.06 |
| end_lng       |         0 |             1 | -87.64 | 0.09 | -87.84 | -87.66 | -87.64 | -87.63 |   0.00 |

**Variable type: POSIXct**

| skim_variable | n_missing | complete_rate | min                 | max                 | median              | n_unique |
|:--------------|----------:|--------------:|:--------------------|:--------------------|:--------------------|---------:|
| started_at    |         0 |             1 | 2023-01-01 06:00:55 | 2023-12-31 23:58:55 | 2023-07-20 16:46:02 |  2772091 |
| ended_at      |         0 |             1 | 2023-01-01 06:08:34 | 2024-01-01 02:52:56 | 2023-07-20 17:02:50 |  2778554 |

### Separating date in day, month, and year

For our analysis, we might want to check for trends based on the month,
as well as the season. Therefore, we need to extract the month from the
starting dates. We’ll also add the year and day columns.

``` r
df <- df %>%
  mutate(
    day = format(as.Date(started_at), "%d"),
    month = format(as.Date(started_at), "%m"),
    year = format(as.Date(started_at), "%Y")
  )

head(df)
```

    ## # A tibble: 6 × 18
    ##   ride_id          rideable_type started_at          ended_at           
    ##   <chr>            <chr>         <dttm>              <dttm>             
    ## 1 4E20CFA54268C856 classic_bike  2023-01-01 06:06:28 2023-01-01 06:13:24
    ## 2 38EF5B06EE1C1A35 classic_bike  2023-01-01 06:13:04 2023-01-01 06:25:35
    ## 3 CC82B7D462C07BAD electric_bike 2023-01-01 06:48:07 2023-01-01 06:56:09
    ## 4 DADBBE8636509755 classic_bike  2023-01-01 06:53:18 2023-01-01 06:57:09
    ## 5 3F4E2BA4BFF06628 electric_bike 2023-01-01 06:55:13 2023-01-01 07:06:56
    ## 6 C034716092997D7F electric_bike 2023-01-01 07:03:05 2023-01-01 07:06:43
    ## # ℹ 14 more variables: start_station_name <chr>, start_station_id <chr>,
    ## #   end_station_name <chr>, end_station_id <chr>, start_lat <dbl>,
    ## #   start_lng <dbl>, end_lat <dbl>, end_lng <dbl>, member_casual <chr>,
    ## #   ride_length <time>, day_of_week <chr>, day <chr>, month <chr>, year <chr>

### Selecting attributes of interest

For this part of the analysis, we focus more on the rides, rather than
their location. Therefore, we select these attributes for this part and
create a new dataframe ignoring the other attributes related to location
(latitude and logitude).

First we see the 14 attributes from our data plus the 4 added from the
above steps.

``` r
colnames(df)
```

    ##  [1] "ride_id"            "rideable_type"      "started_at"        
    ##  [4] "ended_at"           "start_station_name" "start_station_id"  
    ##  [7] "end_station_name"   "end_station_id"     "start_lat"         
    ## [10] "start_lng"          "end_lat"            "end_lng"           
    ## [13] "member_casual"      "ride_length"        "day_of_week"       
    ## [16] "day"                "month"              "year"

Now we select the attributes of interest.

``` r
rides <- df %>%
  select(ride_id, rideable_type, started_at, ended_at, day, month, day_of_week, ride_length, start_station_name, member_casual) %>%
  arrange(started_at)

head(rides)
```

    ## # A tibble: 6 × 10
    ##   ride_id      rideable_type started_at          ended_at            day   month
    ##   <chr>        <chr>         <dttm>              <dttm>              <chr> <chr>
    ## 1 B974A032F77… electric_bike 2023-01-01 06:00:55 2023-01-01 06:08:34 01    01   
    ## 2 4E20CFA5426… classic_bike  2023-01-01 06:06:28 2023-01-01 06:13:24 01    01   
    ## 3 596FD3BED94… electric_bike 2023-01-01 06:08:52 2023-01-01 06:19:14 01    01   
    ## 4 2EE6FC8E3B9… classic_bike  2023-01-01 06:09:11 2023-01-01 06:12:06 01    01   
    ## 5 6239CA7602F… classic_bike  2023-01-01 06:11:48 2023-01-01 06:32:19 01    01   
    ## 6 38EF5B06EE1… classic_bike  2023-01-01 06:13:04 2023-01-01 06:25:35 01    01   
    ## # ℹ 4 more variables: day_of_week <chr>, ride_length <time>,
    ## #   start_station_name <chr>, member_casual <chr>

With this data we can start our analysis now.

## Descriptive analysis

The first part is to summarize the data. We’ll do it by day of the week
and by month. But first let’s take a look at the whole data.

We can see the following statistics for the `ride_length` attribute.
First we can see the minimum, 1st quartile (25th percentile), median
(also the 2nd quartile), 3rd quartile (75th percentile), mean, and
maximum time.

``` r
rides %>%
  summarize(t_min = min(ride_length),
            t_q1 = quantile(ride_length, probs = 0.25),
            t_median = median(ride_length),
            t_q3 = quantile(ride_length, probs = 0.75),
            t_mean = mean(ride_length),
            t_max = max(ride_length))
```

    ## # A tibble: 1 × 6
    ##   t_min  t_q1   t_median t_q3   t_mean        t_max      
    ##   <drtn> <time> <drtn>   <time> <drtn>        <drtn>     
    ## 1 1 secs 05'55" 623 secs 18'49" 1052.543 secs 728178 secs

Next we show the same statistics for each member type.

``` r
rides %>%
  group_by(member_casual) %>%
  summarize(
    t_min = min(ride_length),
    t_q1 = quantile(ride_length, probs = 0.25),
    t_median = median(ride_length),
    t_q3 = quantile(ride_length, probs = 0.75),
    t_mean = as_hms(round(mean(ride_length), 2)),
    t_max = as_hms(round(max(ride_length), 2))
  )
```

    ## # A tibble: 2 × 7
    ##   member_casual t_min  t_q1   t_median t_q3   t_mean    t_max    
    ##   <chr>         <drtn> <time> <time>   <time> <time>    <time>   
    ## 1 casual        1 secs 07'11" 12'45"   24'04" 22'56.34" 202:16:18
    ## 2 member        1 secs 05'03" 08'37"   14'43" 12'08.74"  24:57:30

### By day of the week

We can now summarize the data by day of the week. This code chunck shows
the same statistics from the beginning, but now it is grouped by day of
the week and member type.

``` r
summary_by_day_of_week <- rides %>%
  mutate(day_of_week = ordered(day_of_week, levels=c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"))) %>%
  group_by(day_of_week, member_casual) %>%
  summarize(
    number_of_rides = n(),
    t_min = min(ride_length),
    t_q1 = quantile(ride_length, probs = 0.25),
    t_median = median(ride_length),
    t_q3 = quantile(ride_length, probs = 0.75),
    t_mean = as_hms(round(mean(ride_length), 2)),
    t_max = as_hms(max(ride_length))
  )
```

    ## `summarise()` has grouped output by 'day_of_week'. You can override using the
    ## `.groups` argument.

``` r
summary_by_day_of_week
```

    ## # A tibble: 14 × 9
    ## # Groups:   day_of_week [7]
    ##    day_of_week member_casual number_of_rides t_min  t_q1   t_median t_q3  
    ##    <ord>       <chr>                   <int> <drtn> <time> <time>   <time>
    ##  1 Sunday      casual                 254326 1 secs 08'15" 15'05"   28'50"
    ##  2 Sunday      member                 168579 1 secs 05'16" 09'20"   16'38"
    ##  3 Monday      casual                 175382 1 secs 06'48" 12'06"   23'39"
    ##  4 Monday      member                 211344 1 secs 04'52" 08'13"   13'58"
    ##  5 Tuesday     casual                 181510 1 secs 06'32" 11'19"   21'02"
    ##  6 Tuesday     member                 245535 1 secs 04'59" 08'24"   14'12"
    ##  7 Wednesday   casual                 183065 1 secs 06'25" 10'56"   19'47"
    ##  8 Wednesday   member                 247358 1 secs 04'59" 08'26"   14'05"
    ##  9 Thursday    casual                 198905 1 secs 06'34" 11'12"   20'12"
    ## 10 Thursday    member                 247845 1 secs 05'00" 08'29"   14'15"
    ## 11 Friday      casual                 227828 1 secs 07'06" 12'26"   23'16"
    ## 12 Friday      member                 218805 1 secs 05'00" 08'30"   14'25"
    ## 13 Saturday    casual                 310129 1 secs 08'21" 15'03"   28'06"
    ## 14 Saturday    member                 191679 1 secs 05'26" 09'28"   16'36"
    ## # ℹ 2 more variables: t_mean <time>, t_max <time>

With this data we can make some plots using only the number of rides and
the mean ride duration.

``` r
ggplot(data = summary_by_day_of_week,
       (aes(x = day_of_week,y = number_of_rides, fill = member_casual))) +
  geom_col(position = "dodge") +
  labs(title = "Number of rides", subtitle = "per day and member type") +
  xlab("Day of the week") +
  ylab("Number of rides") +
  scale_y_continuous(labels = unit_format(unit = "K", scale = 1e-3))
```

![](Cyclistic_analyzing_files/figure-gfm/plot%20number%20of%20rides%20per%20day-1.png)<!-- -->
This graph shows that casual users tend to ride more than members on
weekdends. Member users ride more during the weekdays, and the numbers
are very similar each workday. On weekends, specially Saturday and
Sunday, they ride there are less rides than usual.

``` r
ggplot(data = summary_by_day_of_week,
       aes(x = day_of_week, y = t_mean, fill = member_casual)) +
  geom_col(position = "dodge") +
  labs(title = "Average ride duration", subtitle = "per day and member type") +
  xlab("Day of the week") +
  ylab("Average ride duration")
```

![](Cyclistic_analyzing_files/figure-gfm/plot%20average%20ride%20duration%20per%20day-1.png)<!-- -->
The average duration of casual users’ rides is almost twice average from
member rides. In both cases, rides tend to last longer during the
weekend, but this trend is more visible on casual members.

### By month

Another interesting thing, is to observe this data by month. Therefore
we group the data and show the same statistics based on the month
(recall that this data is for the year 2023).

``` r
summary_by_month <- rides %>%
  group_by(month, member_casual) %>%
  summarize(
    number_of_rides = n(),
    t_min = min(ride_length),
    t_q1 = quantile(ride_length, probs = 0.25),
    t_median = median(ride_length),
    t_q3 = quantile(ride_length, probs = 0.75),
    t_mean = as_hms(round(mean(ride_length), 2)),
    t_max = as_hms(max(ride_length))
  )
```

    ## `summarise()` has grouped output by 'month'. You can override using the
    ## `.groups` argument.

``` r
summary_by_month
```

    ## # A tibble: 24 × 9
    ## # Groups:   month [12]
    ##    month member_casual number_of_rides t_min  t_q1   t_median t_q3   t_mean   
    ##    <chr> <chr>                   <int> <drtn> <time> <time>   <time> <time>   
    ##  1 01    casual                  29237 1 secs 05'03" 08'10"   13'52" 14'51.17"
    ##  2 01    member                  64747 1 secs 04'18" 07'03"   11'45" 09'59.19"
    ##  3 02    casual                  32774 2 secs 05'30" 09'15"   16'59" 17'40.40"
    ##  4 02    member                  63487 1 secs 04'20" 07'13"   12'15" 10'29.48"
    ##  5 03    casual                  46786 1 secs 05'29" 09'11"   16'32" 16'43.28"
    ##  6 03    member                  84000 1 secs 04'20" 07'16"   12'14" 10'11.63"
    ##  7 04    casual                 110526 1 secs 06'46" 12'15"   23'59" 22'37.39"
    ##  8 04    member                 116732 1 secs 04'43" 08'10"   14'01" 11'33.10"
    ##  9 05    casual                 177025 1 secs 07'43" 13'57"   26'38" 24'31.58"
    ## 10 05    member                 157187 1 secs 05'15" 09'06"   15'41" 12'41.43"
    ## # ℹ 14 more rows
    ## # ℹ 1 more variable: t_max <time>

With this data we can make similar plots.

``` r
ggplot(data = summary_by_month,
       aes(x = month, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge") +
  labs(title = "Number of rides", subtitle = "per month and member type") +
  xlab("Month") +
  ylab("Number of rides") +
  scale_y_continuous(labels = unit_format(unit = "K", scale = 1e-3))
```

![](Cyclistic_analyzing_files/figure-gfm/plot%20number%20of%20rides%20per%20month-1.png)<!-- -->
We can see that there is a considerable increase in the use of the
bicycle starting from March and peaking at August. In September, the use
starts to decrease and by December it is very low, specially in the
causal riders. In these months, the member riders use the bicycle more
than casual members. Casual users ride more than members from May to
September.

``` r
ggplot(data = summary_by_month,
       aes(x = month, y = t_mean, fill = member_casual)) +
  geom_col(position = "dodge") +
  labs(title = "Average ride duration", subtitle = "per month and member type") +
  xlab("Month") +
  ylab("Average ride duration")
```

![](Cyclistic_analyzing_files/figure-gfm/plot%20average%20ride%20duration%20per%20month-1.png)<!-- -->
This trend also affects the average ride duration. People tend to ride
more time from April, peaking at July, then decreasing smoothly. Until
November, the time average time is still higher than 20 minutes.
However, this trend is more smooth for members, where the average ride
duration is almost the same, no matter the month. Also, we can notably
see that casual members ride more time than members each month.

### Distribution of start time

From the data we can see that the members are more consistent with the
use of the bicycle. This might be because members probably use the bike
to commute to work. In this step we are going to visualize the
distribution of start and end hour of the rides to investigate this
trend further.

``` r
hours_distribution <- rides %>%
  mutate(day_of_week = ordered(day_of_week, levels=c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"))) %>%
  mutate(
    started_at = as_hms(format(started_at, "%H:%M:%S")),
    ended_at = as_hms(format(ended_at, "%H:%M:%S"))
  )
```

#### By day

``` r
hours_distribution %>%
  filter(member_casual == "member") %>%
  ggplot(aes(x = started_at)) +
  geom_histogram(bins = 24) +
  facet_wrap(~day_of_week) +
  labs(title = "Distribution of start times", subtitle = "by day of the week") +
  xlab("The hour at which the ride started") +
  ylab("Number of rides")
```

![](Cyclistic_analyzing_files/figure-gfm/start%20rides%20distribution%20per%20day%20for%20members-1.png)<!-- -->
In this plot we have some evidence that could suggest that members
indeed use the bicycle to commute to work. Since we can see some peaks
before 9:00 and after 15:00 h, which is within the workday.
Interestingly, on weekends we can’t see that trend, because most people
work from Monday to Friday.

``` r
hours_distribution %>%
  filter(member_casual == "casual") %>%
  ggplot(aes(x = started_at)) +
  geom_histogram(bins = 24) +
  facet_wrap(~day_of_week)
```

![](Cyclistic_analyzing_files/figure-gfm/start%20rides%20distribution%20per%20day%20for%20casual%20users-1.png)<!-- -->
In the case of casual users, we can also see two main peaks, but the
highest is after 15:00 h. While this might also suggest that causal
riders commute on the bike to work, the available data is not enough to
support more assumptions. The only thing we might suspect is that, since
casual users ride more time, they might ride after work, not precisely
to commute to their homes, but as a recreation ride.

#### By month

``` r
hours_distribution %>%
  filter(member_casual == "member") %>%
  ggplot(aes(x = started_at)) +
  geom_histogram(bins = 24) +
  facet_wrap(~month)
```

![](Cyclistic_analyzing_files/figure-gfm/start%20rides%20distribution%20per%20month%20for%20members-1.png)<!-- -->

``` r
hours_distribution %>%
  filter(member_casual == "casual") %>%
  ggplot(aes(x = started_at)) +
  geom_histogram(bins = 24) +
  facet_wrap(~month)
```

![](Cyclistic_analyzing_files/figure-gfm/start%20rides%20distribution%20per%20month%20for%20casual%20users-1.png)<!-- -->
We can see that in both cases, the trend is the same for the two type of
users for each month, where we can only see the difference in the amount
of rides.

## Conclusions from the analysis

Based on the available data, we can see two important things:  
- It is probable that casual riders use the bicycle more for
recreational uses. This explains why they ride more time, and the
seasonal use (see graph below - it’s not very enjoyable to ride in low
temperatures). - Probably, people with membership use the bicycle to
commute to work. This might explain why their rides last less and keep
almost the same during the weekdays, and in every month. Though in
winter they tend to ride less, those who ride, still use the bicycle to
commute, therefore, they take almost the same time to arrive, which
might explain why the average time remains almost the same every month.

``` r
ggplot(data = summary_by_month,
       aes(x = month, y = number_of_rides, fill = member_casual)) +
  geom_col(position = "dodge") +
  labs(title = "Number of rides", subtitle = "per month and member type") +
  xlab("Month") +
  ylab("Number of rides") +
  scale_y_continuous(labels = unit_format(unit = "K", scale = 1e-3)) +
  annotate("rect", xmin=c(0, 11.5), xmax=c(3.5, 13),
           ymin=c(0) , ymax=c(250000), alpha=0.1, color="blue", fill="blue") +
  annotate("text", x = 1.5, y = 200000, 
           label = "Winter", color="orange", 
           size = 8, angle = -15, fontface="bold")
```

![](Cyclistic_analyzing_files/figure-gfm/plot%20number%20of%20rides%20per%20month%20with%20annotation-1.png)<!-- -->
