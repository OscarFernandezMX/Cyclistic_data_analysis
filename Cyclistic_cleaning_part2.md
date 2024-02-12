Cyclistic data analysis - cleaning (part 2)
================
Oscar Fernandez Solano
2024-02-11

The first part of the cleaning phase can be found
[here](https://github.com/OscarFernandezMX). In the first part we used
Apache Spark and SQL to clean the data. Here is a summary of the steps
made:  
- Inspected each row for inconsistencies. - Removed duplicates.  
- Removed rows containing NA in stations’ information.

As mentioned in the pervious phase, we need to make a downsampling to
have balanced classes. This is because we want to find differences
between casual cyclists and members.

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
library(janitor)
```

    ## 
    ## Attaching package: 'janitor'
    ## 
    ## The following objects are masked from 'package:stats':
    ## 
    ##     chisq.test, fisher.test

``` r
library(hms)
```

    ## 
    ## Attaching package: 'hms'
    ## 
    ## The following object is masked from 'package:lubridate':
    ## 
    ##     hms

Next we set the working directory, the data path, and read the csv file
created from the preparing phase.

``` r
setwd("/Users/oscarfs/GitHub/Data_Science_Projects")
data_path = "Data/cyclistic/"
file_name = paste0(data_path, "2023_cyclistic_tripdata_clean.csv")
df <- read_csv(file_name)
```

    ## Warning: One or more parsing issues, call `problems()` on your data frame for details,
    ## e.g.:
    ##   dat <- vroom(...)
    ##   problems(dat)

    ## Rows: 4330400 Columns: 15
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (7): ride_id, rideable_type, start_station_name, start_station_id, end_...
    ## dbl  (5): start_lat, start_lng, end_lat, end_lng, day_of_week
    ## dttm (2): started_at, ended_at
    ## time (1): ride_duration
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

### Data check

We get the summary of the data to check that the file is clean. After
this, we can continue with the cleaning phase.

``` r
skim_without_charts(df)
```

|                                                  |         |
|:-------------------------------------------------|:--------|
| Name                                             | df      |
| Number of rows                                   | 4330400 |
| Number of columns                                | 15      |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |         |
| Column type frequency:                           |         |
| character                                        | 7       |
| difftime                                         | 1       |
| numeric                                          | 5       |
| POSIXct                                          | 2       |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |         |
| Group variables                                  | None    |

Data summary

**Variable type: character**

| skim_variable      | n_missing | complete_rate | min | max | empty | n_unique | whitespace |
|:-------------------|----------:|--------------:|----:|----:|------:|---------:|-----------:|
| ride_id            |         0 |             1 |  16 |  16 |     0 |  4330400 |          0 |
| rideable_type      |         0 |             1 |  11 |  13 |     0 |        3 |          0 |
| start_station_name |         0 |             1 |   3 |  64 |     0 |     1534 |          0 |
| start_station_id   |         0 |             1 |   3 |  35 |     0 |     1468 |          0 |
| end_station_name   |         0 |             1 |   3 |  64 |     0 |     1557 |          0 |
| end_station_id     |         0 |             1 |   3 |  36 |     0 |     1483 |          0 |
| member_casual      |         0 |             1 |   6 |   6 |     0 |        2 |          0 |

**Variable type: difftime**

| skim_variable | n_missing | complete_rate | min    | max         | median   | n_unique |
|:--------------|----------:|--------------:|:-------|:------------|:---------|---------:|
| ride_duration |         3 |             1 | 1 secs | 322740 secs | 00:09:48 |    20211 |

**Variable type: numeric**

| skim_variable | n_missing | complete_rate |   mean |   sd |     p0 |    p25 |    p50 |    p75 |   p100 |
|:--------------|----------:|--------------:|-------:|-----:|-------:|-------:|-------:|-------:|-------:|
| start_lat     |         0 |             1 |  41.90 | 0.04 |  41.65 |  41.88 |  41.90 |  41.93 |  42.06 |
| start_lng     |         0 |             1 | -87.64 | 0.02 | -87.84 | -87.66 | -87.64 | -87.63 | -87.53 |
| end_lat       |         0 |             1 |  41.90 | 0.06 |   0.00 |  41.88 |  41.90 |  41.93 |  42.06 |
| end_lng       |         0 |             1 | -87.64 | 0.08 | -87.84 | -87.66 | -87.64 | -87.63 |   0.00 |
| day_of_week   |         0 |             1 |   4.10 | 1.98 |   1.00 |   2.00 |   4.00 |   6.00 |   7.00 |

**Variable type: POSIXct**

| skim_variable | n_missing | complete_rate | min                 | max                 | median              | n_unique |
|:--------------|----------:|--------------:|:--------------------|:--------------------|:--------------------|---------:|
| started_at    |         0 |             1 | 2023-01-01 06:00:55 | 2023-12-31 23:58:55 | 2023-07-20 15:24:54 |  3789260 |
| ended_at      |         0 |             1 | 2023-01-01 06:08:34 | 2024-01-01 02:52:56 | 2023-07-20 15:42:17 |  3800050 |

### Set \``ride_length` and `day_of_week`

The next step is to create the ride length based on the information from
the starting and ending dates. We also need to create a column that
indicates the week of the day where the ride started (starting with
Sunday = 1 to Saturday = 7).

``` r
df_modified <- df %>%
  mutate(ride_duration = as_hms(ended_at - started_at)) %>%
  mutate(day_of_week = wday(started_at)) %>%
  arrange(started_at)

head(df_modified)
```

    ## # A tibble: 6 × 15
    ##   ride_id          rideable_type started_at          ended_at           
    ##   <chr>            <chr>         <dttm>              <dttm>             
    ## 1 B974A032F770676B electric_bike 2023-01-01 06:00:55 2023-01-01 06:08:34
    ## 2 F7A4E0D13AE390AF classic_bike  2023-01-01 06:04:57 2023-01-01 06:17:59
    ## 3 4E20CFA54268C856 classic_bike  2023-01-01 06:06:28 2023-01-01 06:13:24
    ## 4 60840382B4B7BE4C classic_bike  2023-01-01 06:08:17 2023-01-01 06:12:12
    ## 5 596FD3BED9467C28 electric_bike 2023-01-01 06:08:52 2023-01-01 06:19:14
    ## 6 2EE6FC8E3B960D00 classic_bike  2023-01-01 06:09:11 2023-01-01 06:12:06
    ## # ℹ 11 more variables: start_station_name <chr>, start_station_id <chr>,
    ## #   end_station_name <chr>, end_station_id <chr>, start_lat <dbl>,
    ## #   start_lng <dbl>, end_lat <dbl>, end_lng <dbl>, member_casual <chr>,
    ## #   ride_duration <time>, day_of_week <dbl>

Now that we have included this attributes, we create a new csv file. We
replace the clean file to avoid having multiple files.

``` r
file_name = paste0(data_path, "2023_cyclistic_tripdata_clean.csv")
write_csv(df_modified, file_name)
print("File created.")
```

    ## [1] "File created."

### Downsampling

As we mentioned in the [preparing
phase](https://github.com/OscarFernandezMX), our groups of interest
don’t have the same number of instances. This was observed before
cleaning. Now, we plot each class to verify how the cleaning process
changed (or not) this observation.

``` r
ggplot(df_modified) +
  geom_bar(aes(x = member_casual)) +
  ylab("Rows") +
  labs(title = "Unbalanced classes for member_casual")
```

![](Cyclistic_cleaning_part2_files/figure-gfm/bar%20plot%20for%20member%20casual%201-1.png)<!-- -->
The plot shows the same observation we made in the beginning. Therefore,
we still need to downsample our data set for the member class.

First, we save the data from the “casual” class.

``` r
casual_df <- df_modified %>%
  filter(member_casual == "casual")

cat("Number of rows:", nrow(casual_df))
```

    ## Number of rows: 1531145

Now we randomly sample cases with “member” class.

``` r
downsampled_member <- df_modified %>%
  filter(member_casual == "member") %>%
  sample_n(size = nrow(casual_df), replace = FALSE, seed = 123)

cat("Number of rows:", nrow(downsampled_member))
```

    ## Number of rows: 1531145

Finally, we can bind both dataframes and save the new data set.

``` r
file_name = paste0(data_path, "2023_cyclistic_tripdata_downsampled.csv")

if (!file.exists(file_name)) {
  downsampled_df <- bind_rows(casual_df, downsampled_member)
  write_csv(downsampled_df, file_name)
  print("File created.")
} else {
  print("File already created.")
  downsampled_df <- read_csv(file_name)
}
```

    ## [1] "File already created."

    ## Warning: One or more parsing issues, call `problems()` on your data frame for details,
    ## e.g.:
    ##   dat <- vroom(...)
    ##   problems(dat)

    ## Rows: 3062290 Columns: 15
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (7): ride_id, rideable_type, start_station_name, start_station_id, end_...
    ## dbl  (5): start_lat, start_lng, end_lat, end_lng, day_of_week
    ## dttm (2): started_at, ended_at
    ## time (1): ride_duration
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
ggplot(downsampled_df) +
  geom_bar(aes(x = member_casual)) +
  ylab("Rows") +
  labs(title = "Balanced classes", subtitle = "After downsampling")
```

![](Cyclistic_cleaning_part2_files/figure-gfm/bar%20plot%20for%20member%20casual%202-1.png)<!-- -->

Now that the data is ready, we can continue with the analysis phase.
