Case Study: Cyclistic Bike-Share
================
Amitesh
2024-06-10

## About the Company

Cyclistic is a bike sharing company that has a network of docking
stations spread all across chicago. A bike can be picked up from any
station and can be returned back to the same station or any other
cyclistic station. They are known in the market for their versatility as
they provide multiple price options and bike choices as per the needs
and the convenience of their users.

## Deliverable: Business Task

The director of marketing has a hypothesis that in order to grow their
revenue they need people to purchase more annual memberships and for
this to happen, they want to convert more casual customers to annual
members. My team needs to figure out how casual and annual members use
Cyclistic bikes differently so that we can create a new strategy for
marketing. These insights need to be backed by data in order to get
approval from the executive team.

#### Stakeholder Expectations

- Executive Team: Insights that are backed by real world data and can
  help to grow the company.
- Director of marketing: Data driven evidence to back up her hypothesis
  and recommendations.

<br>

## Deliverable: Collection and preparation of data

#### Understanding the dataset:

- Dataset Source: Data was provided through a repository held online.
- Data Context: The data was collected to analyze it for potential
  insights regarding growth of the company.
- Data Documentation - No documentation was provided about the data

#### Initial Data Exploration:

- Loading the data: R studio which is a software that uses R programming
  language to manipulate data was used to handle the data.
- Data integration: Data was divided into twelve different datasets each
  representing a month’s worth of data. All of the datasets were
  combined together to form a single dataset representing a whole year.
- Initial preview: After a first look at the data, the variables
  provided are of quantitative nature such as station name, ride id,
  rideable type, date and time, member type and more.

#### Data Quality Assessment:

- Reliable: Data is not 100% reliable since it contains missing values
  and inconsistencies.
- Original: Dataset is original as it is provided by the organization
  handling bike shares.
- Comprehensive: The dataset mostly contains everything required for the
  analysis.
- Current: Time frame of the dataset is relevant to the analysis.

<br>

#### Loading and integrating data

``` r
##loading packages and data sets
library(tidyverse)
library(knitr)
library(scales)

JanuaryData <- read_csv("JanDataRaw.csv")
FebruaryData <- read_csv("FebDataRaw.csv")
MarchData <- read_csv("MarDataRaw.csv")
AprilData <- read_csv("AprilDataRaw.csv")
MayData <- read_csv("MayDataRaw.csv")
JuneData <- read_csv("JuneDataRaw.csv")
JulyData <- read_csv("JulyDataRaw.csv")
AugData <- read_csv("AugDataRaw.csv")
SeptData <- read_csv("SeptDataRaw.csv")
OctData <- read_csv("OctDataRaw.csv")
NovData <- read_csv("NovDataRaw.csv")
DecData <- read_csv("DecDataRaw.csv")

## combining all the data to 1 dataframe
CyclisticDataRaw <- bind_rows(JanuaryData, FebruaryData, MarchData, AprilData, MayData, JuneData, JulyData, AugData, SeptData, OctData, NovData, DecData)
```

<br>

#### Peek at the data variables

``` r
## first look at the data
head(CyclisticDataRaw)
```

    ## # A tibble: 6 × 13
    ##   ride_id          rideable_type started_at          ended_at           
    ##   <chr>            <chr>         <dttm>              <dttm>             
    ## 1 F96D5A74A3E41399 electric_bike 2023-01-21 20:05:42 2023-01-21 20:16:33
    ## 2 13CB7EB698CEDB88 classic_bike  2023-01-10 15:37:36 2023-01-10 15:46:05
    ## 3 BD88A2E670661CE5 electric_bike 2023-01-02 07:51:57 2023-01-02 08:05:11
    ## 4 C90792D034FED968 classic_bike  2023-01-22 10:52:58 2023-01-22 11:01:44
    ## 5 3397017529188E8A classic_bike  2023-01-12 13:58:01 2023-01-12 14:13:20
    ## 6 58E68156DAE3E311 electric_bike 2023-01-31 07:18:03 2023-01-31 07:21:16
    ## # ℹ 9 more variables: start_station_name <chr>, start_station_id <chr>,
    ## #   end_station_name <chr>, end_station_id <chr>, start_lat <dbl>,
    ## #   start_lng <dbl>, end_lat <dbl>, end_lng <dbl>, member_casual <chr>

``` r
## getting to know data types
glimpse(CyclisticDataRaw)
```

    ## Rows: 5,719,877
    ## Columns: 13
    ## $ ride_id            <chr> "F96D5A74A3E41399", "13CB7EB698CEDB88", "BD88A2E670…
    ## $ rideable_type      <chr> "electric_bike", "classic_bike", "electric_bike", "…
    ## $ started_at         <dttm> 2023-01-21 20:05:42, 2023-01-10 15:37:36, 2023-01-…
    ## $ ended_at           <dttm> 2023-01-21 20:16:33, 2023-01-10 15:46:05, 2023-01-…
    ## $ start_station_name <chr> "Lincoln Ave & Fullerton Ave", "Kimbark Ave & 53rd …
    ## $ start_station_id   <chr> "TA1309000058", "TA1309000037", "RP-005", "TA130900…
    ## $ end_station_name   <chr> "Hampden Ct & Diversey Ave", "Greenwood Ave & 47th …
    ## $ end_station_id     <chr> "202480.0", "TA1308000002", "599", "TA1308000002", …
    ## $ start_lat          <dbl> 41.92407, 41.79957, 42.00857, 41.79957, 41.79957, 4…
    ## $ start_lng          <dbl> -87.64628, -87.59475, -87.69048, -87.59475, -87.594…
    ## $ end_lat            <dbl> 41.93000, 41.80983, 42.03974, 41.80983, 41.80983, 4…
    ## $ end_lng            <dbl> -87.64000, -87.59938, -87.69941, -87.59938, -87.599…
    ## $ member_casual      <chr> "member", "member", "casual", "member", "member", "…

<br>

## Deliverable: Processing the data

#### Handling missing values

- Identifying and addressing missing values:

``` r
## tells the total number of records that have 1 or more null values
sum(is.na(CyclisticDataRaw))
```

    ## [1] 3624089

More than 3.6 million records out of the total 5.7 million have 1 or
more missing values. This figure makes more than 50% of our data set so
omitting all these rows is not advised. Let’s investigate this further
by identifying which columns have the most missing values.

``` r
## tells the number of null observations in each column
colSums(is.na(CyclisticDataRaw))
```

    ##            ride_id      rideable_type         started_at           ended_at 
    ##                  0                  0                  0                  0 
    ## start_station_name   start_station_id   end_station_name     end_station_id 
    ##             875716             875848             929202             929343 
    ##          start_lat          start_lng            end_lat            end_lng 
    ##                  0                  0               6990               6990 
    ##      member_casual 
    ##                  0

The result shows that most of the missing values are in the form of
station names and station ids. There are three ways we can approach this
issue:

- Filling in the values: This is the best approach since it lets us keep
  all the existing data and fill out any holes that it currently has but
  this path is not actionable in this scenario as I have no way of
  finding the values to replace the missing ones or contacting the
  agency that provided this data for any information.
- Omitting records with null values: This is the most common practice as
  it gets rid of incomplete data and makes the analysis more accurate
  whereas in this scenario, the incomplete records account for more than
  50% of our data set and deleting them would negatively impact the
  overall relevance and accuracy of our analysis.
- Keep the data as it is: This approach seems the most profitable in
  this scenario as not deleting the data will help in the overall
  accuracy of this analysis. Moreover as most of the null values are
  station names and ids, it will only partially affect that part of our
  analysis where we take station names and ids into consideration.

<br>

#### Removing Duplicates

``` r
## tells the total number of duplicate records
sum(duplicated(CyclisticDataRaw))
```

    ## [1] 0

The result shows that there are no duplicates in the data set.

<br>

#### Extracting useful data

We’ll extract specific information out of the given data to gain deeper
insights and help us during analysis. We will create 3 data columns: -
Month: Month which the trip data belongs to - Day of the week: Day of
the week when the trip happened - Hour: What time during the day the
trips occur. - Trip duration: Duration of the trip

These month and day values will help us in finding out which months and
days are the busiest ones. Trip duration will help us to find out the
average trip duration.

Before we extract the data, it is important to know that the month and
day data can be extracted in either character form where it will say the
name of the day and the month or it can be extracted in the form of
numeric values where it will give us numbers from 1-12 for each month
from January to December and 1-7 for each day from Monday to Sunday.
Since we need to use this data to perform mathematical operations on it,
we will extract it in the numeric form for now and later convert it to
characters so that it can be used to create graphs with correct labels.

``` r
## column for month
CyclisticDataRaw <- CyclisticDataRaw %>% mutate(month = month(CyclisticDataRaw$started_at, label = TRUE))

## column for day of the week
CyclisticDataRaw <- CyclisticDataRaw %>% mutate(day_of_week = wday(CyclisticDataRaw$started_at, label = TRUE))

## column for hour
CyclisticDataRaw <- CyclisticDataRaw %>% mutate(hour = hour(CyclisticDataRaw$started_at))

## column for trip duration
CyclisticDataRaw <- CyclisticDataRaw %>% mutate(trip_duration_mins = as.numeric(difftime(ended_at, started_at, unit = 'mins')))

glimpse((CyclisticDataRaw))
```

    ## Rows: 5,719,877
    ## Columns: 17
    ## $ ride_id            <chr> "F96D5A74A3E41399", "13CB7EB698CEDB88", "BD88A2E670…
    ## $ rideable_type      <chr> "electric_bike", "classic_bike", "electric_bike", "…
    ## $ started_at         <dttm> 2023-01-21 20:05:42, 2023-01-10 15:37:36, 2023-01-…
    ## $ ended_at           <dttm> 2023-01-21 20:16:33, 2023-01-10 15:46:05, 2023-01-…
    ## $ start_station_name <chr> "Lincoln Ave & Fullerton Ave", "Kimbark Ave & 53rd …
    ## $ start_station_id   <chr> "TA1309000058", "TA1309000037", "RP-005", "TA130900…
    ## $ end_station_name   <chr> "Hampden Ct & Diversey Ave", "Greenwood Ave & 47th …
    ## $ end_station_id     <chr> "202480.0", "TA1308000002", "599", "TA1308000002", …
    ## $ start_lat          <dbl> 41.92407, 41.79957, 42.00857, 41.79957, 41.79957, 4…
    ## $ start_lng          <dbl> -87.64628, -87.59475, -87.69048, -87.59475, -87.594…
    ## $ end_lat            <dbl> 41.93000, 41.80983, 42.03974, 41.80983, 41.80983, 4…
    ## $ end_lng            <dbl> -87.64000, -87.59938, -87.69941, -87.59938, -87.599…
    ## $ member_casual      <chr> "member", "member", "casual", "member", "member", "…
    ## $ month              <ord> Jan, Jan, Jan, Jan, Jan, Jan, Jan, Jan, Jan, Jan, J…
    ## $ day_of_week        <ord> Sat, Tue, Mon, Sun, Thu, Tue, Sun, Wed, Wed, Fri, T…
    ## $ hour               <int> 20, 15, 7, 10, 13, 7, 21, 10, 20, 16, 17, 17, 19, 2…
    ## $ trip_duration_mins <dbl> 10.850000, 8.483333, 13.233333, 8.766667, 15.316667…

Here we can see that three new columns have been added to the table.

Now let us check to see if we have extracted the months and days
correctly.

``` r
unique(CyclisticDataRaw$month)
```

    ##  [1] Jan Feb Mar Apr May Jun Jul Aug Sep Oct Nov Dec
    ## 12 Levels: Jan < Feb < Mar < Apr < May < Jun < Jul < Aug < Sep < ... < Dec

It shows values from 1-12 which represent a month hence we can conclude
that we have extracted them correctly. Let’s do the same for days.

``` r
unique(CyclisticDataRaw$day_of_week)
```

    ## [1] Sat Tue Mon Sun Thu Wed Fri
    ## Levels: Sun < Mon < Tue < Wed < Thu < Fri < Sat

Here we can see values from 1-7 for each day of the week so we can
conclude that these values are also correct.

In the next step, we will perform some operations on our newly created
trip duration column to check for any anomalies.

``` r
summarize(CyclisticDataRaw, min(trip_duration_mins), mean(trip_duration_mins), max(trip_duration_mins))
```

    ## # A tibble: 1 × 3
    ##   `min(trip_duration_mins)` `mean(trip_duration_mins)` `max(trip_duration_mins)`
    ##                       <dbl>                      <dbl>                     <dbl>
    ## 1                   -16657.                       18.2                    98489.

The result shows a minimum value which is a negative number which is
incorrect. Trip duration values cannot be negative. This tells us that
there is definitely some incorrect data in the start and end date
columns. Let us isolate all the values where trip duration is negative
and explore it further.

``` r
## creating a new data frame to store and observe negative trip duration values
durations <- CyclisticDataRaw %>% filter(CyclisticDataRaw$trip_duration_mins < 0)

durations %>% select(started_at, ended_at, trip_duration_mins)
```

    ## # A tibble: 272 × 3
    ##    started_at          ended_at            trip_duration_mins
    ##    <dttm>              <dttm>                           <dbl>
    ##  1 2023-02-04 13:08:08 2023-02-04 13:04:52            -3.27  
    ##  2 2023-04-04 17:15:08 2023-04-04 17:15:05            -0.05  
    ##  3 2023-04-19 14:47:18 2023-04-19 14:47:14            -0.0667
    ##  4 2023-04-27 07:51:14 2023-04-27 07:51:09            -0.0833
    ##  5 2023-04-06 23:09:31 2023-04-06 23:00:35            -8.93  
    ##  6 2023-05-29 17:34:21 2023-05-29 17:34:09            -0.2   
    ##  7 2023-05-29 16:57:34 2023-05-29 16:57:27            -0.117 
    ##  8 2023-05-26 15:39:47 2023-05-26 15:38:17            -1.5   
    ##  9 2023-05-26 15:38:53 2023-05-26 15:38:17            -0.6   
    ## 10 2023-05-07 15:54:58 2023-05-07 15:54:47            -0.183 
    ## # ℹ 262 more rows

Taking a glance at the negative trip duration values, we can see that
the date and times tamp given in the “ended_at” column occurs before the
date and times tamp in the “started_at” column. We would ideally need to
replace these values with the correct ones but since we have no way of
doing that, the next best way is to delete these records altogether. The
number of bad records in this case are only around 200 which is very
less as compared to the size of our data set so it can easily be omitted
without any issues.

``` r
## removing negative trip duration values
CyclisticData <- CyclisticDataRaw %>% filter(CyclisticDataRaw$trip_duration_mins > 0)

summarize(CyclisticData, min(trip_duration_mins))
```

    ## # A tibble: 1 × 1
    ##   `min(trip_duration_mins)`
    ##                       <dbl>
    ## 1                    0.0167

As we can see, the data set is now clean from any negative trip duration
values.

<br>

## Deliverable: Performing analysis on the data

Now we will aggregate the data to find out how casual users and members
use cyclistic differently.

<br>

#### Average trip duration per user type

``` r
## aggregating trip duration
CyclisticData %>% 
  group_by(User_Type = member_casual) %>% 
  summarize(Avg_Trip_Duration = mean(trip_duration_mins))
```

    ## # A tibble: 2 × 2
    ##   User_Type Avg_Trip_Duration
    ##   <chr>                 <dbl>
    ## 1 casual                 28.3
    ## 2 member                 12.5

This shows that casual users rent bikes for an average of 2.3 times more
duration than members. Although this indicates that casuals use the
bikes more than members, it doesn’t tell us the full story. We don’t
know what is happening during this rental time period. We can not say
for sure whether they are riding the bike the entire time or they take
multiple stops which is why the rental times are longer. Let’s dig
deeper into this by analyzing ride duration for each day of the week.

<br>

#### **Daily average trip duration per user type**

Daily average trip duration will help us to find out the average usage
of cyclistic bikes for each day of the week. We will first look at the
data for only casual users and then for members to keep the data and the
findings clear and concise.

##### Daily average trip data for casuals

``` r
CyclisticData %>% 
  filter(member_casual == "casual") %>% 
  group_by(User_Type = member_casual, Weekday = day_of_week) %>% 
  summarize(Avg_Trip_Duration = mean(trip_duration_mins)) %>% 
  arrange(desc(Avg_Trip_Duration))
```

    ## `summarise()` has grouped output by 'User_Type'. You can override using the
    ## `.groups` argument.

    ## # A tibble: 7 × 3
    ## # Groups:   User_Type [1]
    ##   User_Type Weekday Avg_Trip_Duration
    ##   <chr>     <ord>               <dbl>
    ## 1 casual    Sun                  32.9
    ## 2 casual    Sat                  32.1
    ## 3 casual    Mon                  27.7
    ## 4 casual    Fri                  27.3
    ## 5 casual    Tue                  25.1
    ## 6 casual    Thu                  24.7
    ## 7 casual    Wed                  24.3

The data is arranged in descending order based on average trip duration.
It shows that on day number 7 and 6 which is Sunday and Saturday
respectively, the average trip duration for casual users is
significantly higher than any other day.

##### Daily average trip data for members

``` r
CyclisticData %>% 
  filter(member_casual == "member") %>% 
  group_by(User_Type = member_casual, Weekday = day_of_week) %>% 
  summarize(Avg_Trip_Duration = mean(trip_duration_mins)) %>% 
  arrange(desc(Avg_Trip_Duration))
```

    ## `summarise()` has grouped output by 'User_Type'. You can override using the
    ## `.groups` argument.

    ## # A tibble: 7 × 3
    ## # Groups:   User_Type [1]
    ##   User_Type Weekday Avg_Trip_Duration
    ##   <chr>     <ord>               <dbl>
    ## 1 member    Sun                  14.0
    ## 2 member    Sat                  13.9
    ## 3 member    Fri                  12.5
    ## 4 member    Thu                  12.0
    ## 5 member    Tue                  12.0
    ## 6 member    Wed                  11.9
    ## 7 member    Mon                  11.9

The data for members show a very consistent usage irrespective of the
day of the week.

Comparing the two data ranges, we can assume that casual users use
cyclistic data for leisure more than regular work whereas it is opposite
for members. An important thing to note here is that even though casuals
use bikes less often, their average usage is way more than members which
can be an important metric to keep in mind while trying to create
marketing strategies.

<br>

#### **Monthly average trip duration per user type**

##### Monthly average trip duration for casuals

``` r
CyclisticData %>% 
  filter(member_casual == "casual") %>% 
  group_by(User_Type = member_casual, Month = month) %>% 
  summarize(Avg_Trip_Duration = mean(trip_duration_mins)) %>% 
  arrange(desc(Avg_Trip_Duration)) %>% 
  print(n = 12)
```

    ## `summarise()` has grouped output by 'User_Type'. You can override using the
    ## `.groups` argument.

    ## # A tibble: 12 × 3
    ## # Groups:   User_Type [1]
    ##    User_Type Month Avg_Trip_Duration
    ##    <chr>     <ord>             <dbl>
    ##  1 casual    Aug                35.3
    ##  2 casual    Jul                32.3
    ##  3 casual    Jun                29.4
    ##  4 casual    May                28.5
    ##  5 casual    Apr                27.7
    ##  6 casual    Sep                25.2
    ##  7 casual    Feb                23.2
    ##  8 casual    Jan                22.9
    ##  9 casual    Oct                22.9
    ## 10 casual    Mar                21.4
    ## 11 casual    Dec                19.9
    ## 12 casual    Nov                19.9

This data indicates peak usage of cyclistic services between the months
of June and August which also happens to be the peak tourist season.
This metric doesn’t provide us sufficient information for our current
task as the high usage might be regular casual users or it could also be
because of the tourists and it is generally much harder to sell any
sorts of region locked memberships to the tourists as they are only
residing there for a couple of days. However, it can be used to create
special offers for toursists during peak tourism season to attract more
customers and generate more profit during that period of time. We would
need additional information regarding the customers to find out if the
people using bikes at this time of the year are regular casual customers
or if they are new customers.

##### Monthly average trip duration for members

``` r
CyclisticData %>% 
  filter(member_casual == "member") %>% 
  group_by(User_Type = member_casual, Month = month) %>% 
  summarize(Avg_Trip_Duration = mean(trip_duration_mins)) %>% 
  arrange(desc(Avg_Trip_Duration)) %>% 
  print(n = 12)
```

    ## `summarise()` has grouped output by 'User_Type'. You can override using the
    ## `.groups` argument.

    ## # A tibble: 12 × 3
    ## # Groups:   User_Type [1]
    ##    User_Type Month Avg_Trip_Duration
    ##    <chr>     <ord>             <dbl>
    ##  1 member    Aug                13.8
    ##  2 member    Jul                13.7
    ##  3 member    Jun                13.2
    ##  4 member    Sep                13.1
    ##  5 member    May                13.0
    ##  6 member    Oct                12.2
    ##  7 member    Apr                11.7
    ##  8 member    Nov                11.6
    ##  9 member    Dec                11.4
    ## 10 member    Feb                10.7
    ## 11 member    Mar                10.4
    ## 12 member    Jan                10.4

The data for members again shows a consistent usage of cyclistic
services regardless of the month.

<br>

#### **Total number of trips per hour**

The data about number of trips per hour will let us further solidify the
assumed relationship between casual users and members.

``` r
CyclisticData %>% 
  group_by(hour) %>% 
  summarize(trip_count = n()) %>% 
  arrange(desc(trip_count)) %>% 
  print(n = 24)
```

    ## # A tibble: 24 × 2
    ##     hour trip_count
    ##    <int>      <int>
    ##  1    17     587116
    ##  2    16     514010
    ##  3    18     479880
    ##  4    15     405931
    ##  5    19     345165
    ##  6    14     344575
    ##  7    13     335320
    ##  8    12     330661
    ##  9     8     314857
    ## 10    11     286848
    ## 11     7     247561
    ## 12    20     243534
    ## 13    10     235423
    ## 14     9     234874
    ## 15    21     195029
    ## 16    22     156333
    ## 17     6     135439
    ## 18    23     105697
    ## 19     0      72399
    ## 20     5      45567
    ## 21     1      45056
    ## 22     2      26716
    ## 23     3      15871
    ## 24     4      14746

The result tells us that evening time from 4-6pm is the busiest out of
the entire day. Let us expand on this further by categorizing this data
between members and casual users.

#### **Total number of trips per hour per user type**

``` r
CyclisticData %>% 
  group_by(hour, member_casual) %>% 
  summarize(trip_count = n()) %>% 
  ungroup() %>% 
  pivot_wider(names_from = member_casual, values_from = trip_count) %>% 
  print(n = 24)
```

    ## `summarise()` has grouped output by 'hour'. You can override using the
    ## `.groups` argument.

    ## # A tibble: 24 × 3
    ##     hour casual member
    ##    <int>  <int>  <int>
    ##  1     0  36878  35521
    ##  2     1  23896  21160
    ##  3     2  14445  12271
    ##  4     3   7937   7934
    ##  5     4   5968   8778
    ##  6     5  11428  34139
    ##  7     6  30141 105298
    ##  8     7  52988 194573
    ##  9     8  70682 244175
    ## 10     9  69984 164890
    ## 11    10  86594 148829
    ## 12    11 110501 176347
    ## 13    12 130747 199914
    ## 14    13 136620 198700
    ## 15    14 142708 201867
    ## 16    15 159228 246703
    ## 17    16 182453 331557
    ## 18    17 199236 387880
    ## 19    18 172144 307736
    ## 20    19 127237 217928
    ## 21    20  91895 151639
    ## 22    21  77289 117740
    ## 23    22  68343  87990
    ## 24    23  49279  56418

This data paints a more clearer picture between casuals and members. As
shown by the data, the total number of trips consistently increase for
both casual users and members as the day proceeds but we see a sudden
spike in trips for members during the morning rush hour from 7-8 which
is also the time most people leave for work. After this the total number
of trips start to decline a little until we reach lunch time and soon
enough we see the peek number of rides for the day around evening
starting from 4pm which is when I assume people start to leave their
workplaces. Let us look further into this hypothesis by diving the data
between weekdays and weekends.

##### Total number of trips per hour on weekdays

``` r
CyclisticData %>% 
  filter(day_of_week >= 1 & day_of_week <= 5) %>% 
  group_by(hour, member_casual) %>% 
  summarize(trip_count = n()) %>% 
  ungroup() %>% 
  pivot_wider(names_from = member_casual, values_from = trip_count) %>% 
  print(n = 24)
```

    ## `summarise()` has grouped output by 'hour'. You can override using the
    ## `.groups` argument.

    ## # A tibble: 0 × 1
    ## # ℹ 1 variable: hour <int>

As shown in the results, there is a significant difference in the number
of rides between casual users and members during the morning and evening
rush hour.

##### Total number of trips per hour on weekends

``` r
CyclisticData %>% 
  filter(day_of_week == 6 | day_of_week == 7) %>% 
  group_by(hour, member_casual) %>% 
  summarize(trip_count = n()) %>% 
  ungroup() %>% 
  pivot_wider(names_from = member_casual, values_from = trip_count) %>% 
  print(n = 24)
```

    ## `summarise()` has grouped output by 'hour'. You can override using the
    ## `.groups` argument.

    ## # A tibble: 0 × 1
    ## # ℹ 1 variable: hour <int>

Here the number of trips between casual users and members are quite
similar throughout the day.

From the data, we can now almost clearly signify that members use
cyclistic bikes to commute to and fro from work as the number of trips
during weekdays surpass the number of trips on weekends by a large
margin. During the weekends, the number of trips are similar between
casuals and members which can signify that the usage is for leisure and
fun during the weekend.

<br>

### Analysis Summary

**Data-driven insights**

**Casuals**

- Take rides for longer duration on an average than members.
- Rent more bikes during afternoon and evening.
- Rent bikes for even longer duration during weekends.
- Most active during the summer season.
- Least active during winter season.

**Members**

- Take more total number of rides than casuals but have lower average
  ride duration.
- Rent bikes more during morning and evening rush hours.
- Rent more bikes during weekdays and less on weekends.
- Have no favorite time period. They are consistently active through out
  the year.

**Speculated insights**

Using the above mentioned data driven insights, we can speculate
qualitative insights about the behaviour of each of the user type as
mentioned below:

**Casuals**

- Use bikes mostly for leisure activities.
- Don’t use bikes regularly but keep it as an alternative transport.
- Don’t have the requirements of the perks that the membership offers.

**Members**

- Use bikes to commute to work regularly.
- Use bikes as a main source of transport for daily commuting.
- Have enough regular usage to justify paying for an annual membership.

<br>

## Deliverable: Sharing insights with stakeholders

Presentation currently in progress. When finished, link will be added
[here](www.google.com)

<br>

## Deliverable: Acting on insights

##### 1. Collect additional data:

Building an effective marketing campaign requires time, effort and money
and after putting in all of the work, we don’t want it to perform
poorly. Keeping this in mind, additional quantitative and qualitative
data should be collected to give us an even better understanding about
the needs of our users and also provide us more confidence regarding our
marking strategies by confirming our current speculations.

To understand different users’ behaviour, we need qualitative data which
can be gathered by surveying our customers and finding out about things
such as the features that they like the most and the features that want
to be improved or added, their main purpose of using bikes and provide
them a list of external factors to choose from that might affect their
decision about using a bike.

To gain further quantitative insights, we need more data variables such
as age, gender, financial data, availability of bikes.

##### 2. Act on current insights:

These are the recommended steps to move forward with based on the
insights gained from currently available data. Moving forward with these
strategies have some risk involved and might not guarantee 100% success.

- Dynamic pricing: Instead of a single static price throughout the day,
  introduce dynamic pricing where the price depends on the demand. This
  will make the prices higher during peak hours. Relieve users with
  membership from this dynamic pricing model so that there is a clear
  monetary benefit of buying a membership.

- New membership models: There are only two pricing models as of now
  which consists of single day passes and annual memberships. Provide
  users with more options by adding new subscriptions such as monthly
  subscriptions or a pricing model where users pay depending on the
  duration of rental or distance traveled. This will help the users who
  use bikes frequently but not regularly enough to justify paying for an
  annual membership.

- Additional membership benefits: Provide more benefits of buying a
  membership such as a certain number of free rides per month and
  reserved rides at docking stations. Casual users tend to ride for
  longer hence new benefits could be added which would incentivise
  longer rides.

- More user engagement: New features which would display metrics such as
  calories burnt and carbon emissions saved can be added to the
  cyclistic app. This will help users have a sense of achievement after
  finishing a ride and will encourage and motivate them to ride more.
