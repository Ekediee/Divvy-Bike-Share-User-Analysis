# Bike Share User Analysis

![Title image](img/Bike.png)

This project is the analysis of the **Google Data Analytics Professional Certicate** capstone task. I will be analyzing the user pattern of the membership types of Cyclistic, a bike share company in Chicago. Cyclistic's bike-share program features more than 5,800 bicycles and 600 docking stations. Cyclistic sets itself apart by also offering reclining bikes, hand tricycles, and cargo bikes, making bike-share more inclusive to people with disabilities and riders who can’t use a standard two-wheeled bike. The majority of riders opt for traditional bikes; about 8% of riders use the assistive options. Cyclistic users are more likely to ride for leisure, but about 30% use them to commute to work each day.

## Scenario

> The director of marketing believes the company’s future success depends on maximizing the number of annual memberships. Therefore, I have assigned to help the stakeholders to understand how casual riders and annual members use Cyclistic bikes differently. From these insights, we will design a new marketing strategy to convert casual riders into annual members. But first, Cyclistic executives must approve my recommendations, so they must be backed up with compelling data insights and professional data visualizations.

**Business Task:** To identify how annual members and casual riders use Cyclistic bikes share differently.

**Goal:** Design marketing strategies aimed at converting casual riders into annual members.

## Steps of Analysis

In this project, I intend to demonstrate the step by step process of data analytics problems. Therefore, I will be going through the various stages of data analysis. The following tools and libraries were used to complete the analysis and visualization - `R Programming language`, `tidyverse`, `skimr`, `nania`, `ggplot2` and `PowerBI` for visualization.

### _Stages of analysis_

These includes

-  [Data Collection](#data-collection)
-  [Data Preprocessing](#data-preprocessing)

   -  [Data validation and preparation](#data-validation-and-preparation)
   -  [Data Cleaning](#data-cleaning)

-  [Exploratory Data Analysis](#exploratory-data-analysis)
-  [Data Visualization (with PowerBI dashboard)](#data-visualization)
-  [Recommendations](#recommendations)

### Data Collection

The dataset used for this project was made available by Motivate International Inc. under this [license](https://www.divvybikes.com/data-license-agreement). This data was collected from the previous 12 months (October 2021 to September 2022) of the bike share trips records. The data were downloaded from [here](https://divvy-tripdata.s3.amazonaws.com/index.html), which came in 12 different csv files formats, each for one months trip records. I downloaded and saved to a convenient directory in readiness for analysis.

### Data Preprocessing

The data set will be loaded into the R environment, inspected for consistency of datatypes and column names, and finally merged into one big dataframe.

#### _Data validation and preparation_

At this stage, I loaded the csv files into R environment using the following codes.

```r
# Get files from working directory
data_files <- list.files(file.path(getwd(), "data"))

for(i in 1:length(data_files)){
  assign(paste0("month_", i),
         read.csv(paste0(getwd(), "/data/", data_files[i])))
}
```

Next, I will verified that the column naming convention and data types are consistent across all twelve data files to ensure the accuracy of the data set, using the `glimpse` function.

```r
glimpse(month_1)
```

Sample Output

```r
Rows: 631,226
Columns: 13
$ ride_id            <chr> "620BC6107255BF4C", "4471C70731AB2E45", ~
$ rideable_type      <chr> "electric_bike", "electric_bike", "elect~
$ started_at         <chr> "2021-10-22 12:46:42", "2021-10-21 09:12~
$ ended_at           <chr> "2021-10-22 12:49:50", "2021-10-21 09:14~
$ start_station_name <chr> "Kingsbury St & Kinzie St", "", "", "", ~
$ start_station_id   <chr> "KA1503000043", "", "", "", "", "", "", ~
$ end_station_name   <chr> "", "", "", "", "", "", "", "", "", "", ~
$ end_station_id     <chr> "", "", "", "", "", "", "", "", "", "", ~
$ start_lat          <dbl> 41.88919, 41.93000, 41.92000, 41.92000, ~
$ start_lng          <dbl> -87.6385, -87.7000, -87.7000, -87.6900, ~
$ end_lat            <dbl> 41.89, 41.93, 41.94, 41.92, 41.89, 41.93~
$ end_lng            <dbl> -87.63, -87.71, -87.72, -87.69, -87.69, ~
$ member_casual      <chr> "member", "member", "member", "member", ~
```

The results revealed that the column names and data types of the 12 data files were consistent.

In order to further investigate and clean the data, next will be to merge the data files to one dataframe.

```r
combined_trips <- bind_rows(month_1, month_2, month_3, month_4, month_5, month_6, month_7, month_8, month_9, month_10, month_11, month_12)
```

#### _Data Cleaning_

Here, I will check and remove duplicate values if any, handle missing values, and rename some columns with more descriptive names.

#### _Check for duplicate records_

Next we will check to verify that there are no duplicate records

```r
sum(duplicated(combined_trips))
```

```r
[1] 0
```

The output above shows that there are no duplicate values, each row of the trip record represent a unique trip.

Next we will check shape of data to understand the total number of trip records collected over the period of interest.

```r
print(dim(combined_trips))

number_of_records = dim(combined_trips)[1]
```

```r
[1] 5828235      13
```

The result shows that in the past 12 months between October 2021 and September 2022, a total 5.8 million trips were recorded by Cyclistic bikes.

Next, I will rename the columns ride_id and rideable_type with a more descriptive names - trip_id and bike_id respectively.

```r
combined_trips <- combined_trips %>%
  rename(trip_id=ride_id, bike_id=rideable_type)

glimpse(combined_trips)
```

```r
Rows: 5,828,235
Columns: 13
$ trip_id            <chr> "620BC6107255BF4C", "4471C70731AB2E45", ~
$ bike_id            <chr> "electric_bike", "electric_bike", "elect~
$ started_at         <chr> "2021-10-22 12:46:42", "2021-10-21 09:12~
$ ended_at           <chr> "2021-10-22 12:49:50", "2021-10-21 09:14~
$ start_station_name <chr> "Kingsbury St & Kinzie St", "", "", "", ~
$ start_station_id   <chr> "KA1503000043", "", "", "", "", "", "", ~
$ end_station_name   <chr> "", "", "", "", "", "", "", "", "", "", ~
$ end_station_id     <chr> "", "", "", "", "", "", "", "", "", "", ~
$ start_lat          <dbl> 41.88919, 41.93000, 41.92000, 41.92000, ~
$ start_lng          <dbl> -87.6385, -87.7000, -87.7000, -87.6900, ~
$ end_lat            <dbl> 41.89, 41.93, 41.94, 41.92, 41.89, 41.93~
$ end_lng            <dbl> -87.63, -87.71, -87.72, -87.69, -87.69, ~
$ member_casual      <chr> "member", "member", "member", "member", ~
```

#### Get a general overview of the data frame

Here we will get the summary statistics of all features and check for missing values as well as detect attributes with empty values.

```{r}
skim_without_charts(combined_trips)
```

![Overview of data](img/data_overview.png "This image displays the summary statistics of all attributes in the dataset")

The empty column of the above tibble reveals that there are 958227 record with no start station or end station name and id. Hence, it will be difficult to track the routes of these trips. These records will be removed as their route information are inconclusive.

#### Remove records without start or end station details

```r
all_valid_trips <- combined_trips %>%
  filter(end_station_id !="" & start_station_id !="")

# Review the summary detail
skim_without_charts(all_valid_trips)
```

![Overview of data](img/data_overview2.png)

After cleaning the missing and empty values, a few questions comes to mind for further investigation.

### Exploratory Data Analysis

At this point, I will be asking specific questions, and analyzing the data to provide answers.

#### What is the total number of valid trips?

```{r}
# Number of valid trips
num_of_valid_trips = nrow(all_valid_trips)

num_of_valid_trips
```

```r
[1] 4474141
```

There are a total of 4.47 million valid trips record by Cyclistic bikes in the past 12 months.

#### What is the number of trips with missing details?

```r
num_of_invalid_trips = number_of_records - num_of_valid_trips

num_of_invalid_trips
```

```r
[1] 1354094
```

#### What percentage of the trip records were invalid?

```r
percent_of_invalid_trips = (num_of_invalid_trips/number_of_records)*100

percent_of_invalid_trips
```

```r
[1] 23.23335
```

The above results indicates that a total of 1.35 million records, which form 23.2% of the overrall trip records for the period were incorrectly captured.

The ideal next thing to do will be to report this discovery to the management through the team lead for further investigation on what went wrong with the incorrectly captured data, before continuing with the analysis.

### Create new columns

In order to better understand and make sense of the trip data, two new columns (`trip_duration` and `week_day`) will be created.

```r
all_valid_trips <- all_valid_trips %>%
  mutate(week_day = wday(started_at, label = TRUE), wday = wday(started_at))

all_valid_trips$trip_duration <-  round(as.numeric(difftime(all_valid_trips$ended_at, all_valid_trips$started_at))/60,2)

head(all_valid_trips)
```

#### What is the average trip duration?

```r
with(all_valid_trips, round(mean(trip_duration),1))
```

```r
[1] 17.5
```

Here, we can see that the average trip duration is 17.5 minutes per trip.

### Exploratory Data Analysis

At this stage of the analysis, I will be using guided questions to explore the data.

#### What is the proportion of user types?

Let's determine the proportion of the membership types on the trips records for the period of interest.

```r
all_valid_trips %>%
  group_by(member_casual) %>%
  summarise(number_of_trips = n()) %>%
  ggplot(aes(x=member_casual, y=number_of_trips, fill = member_casual)) +
  geom_bar(stat = "identity") +
  theme_minimal()+
  ggtitle("Proportion of User Types by Number of Trips")+
  xlab("")+ylab("")+theme(axis.line = element_line(color='black'))+
  geom_text(aes(label=paste0(round((number_of_trips/1000000),2),"M ",
                             paste0("(",round((number_of_trips/num_of_valid_trips)*100,1),"%",")"))))
```

![Proportion of membership types](img/user_proportion.png)

> -  Casual riders made 40.4% of the total trips recorded.
> -  Annual members made 59.6% of the recorded trips

#### What is the most used bike types by membership types?

```r
all_valid_trips %>%
  group_by(member_casual, bike_id) %>%
  summarise(avg_trip_duration = mean(trip_duration), number_of_trips = n(), .groups="keep") %>%
  ggplot(aes(x=reorder(bike_id, -number_of_trips), y=number_of_trips, fill=member_casual))+
  geom_bar(stat = "identity") +
  theme_minimal()+
  ggtitle("Most used Bikes by User Type")+
  xlab("")+ylab("")+theme(axis.line = element_line(color='black'))+
  geom_text(aes(label=paste0(round((number_of_trips/1000),0),"K")))+
  coord_flip()+
  facet_wrap(~member_casual)
```

![Bike usage by membership type](img/bike_usage.png)

> -  Docked bikes are only used by casual riders, and covered 10.5% of their total trips.
> -  Most used Bike by both user groups is Classic bikes

#### What is the average trip duration by membership type?

```r
all_valid_trips %>%
  group_by(member_casual, bike_id) %>%
  summarise(avg_trip_duration = mean(trip_duration), number_of_trips = n(), .groups = "keep") %>%
  ggplot(aes(x=member_casual, y=avg_trip_duration, fill=bike_id))+
  geom_bar(stat = "identity")+
  theme_minimal()+
  ggtitle("Trip Duration by User type per Bike type")+
  xlab("")+ylab("")+theme(axis.line = element_line(color='black'))+
  geom_text(aes(label=round(avg_trip_duration,1)))+
  facet_wrap(~bike_id)
```

![Trip duration by membership type](img/trip_duration.png)

> -  Casual riders travel longer time per trip on average when compared with annual members
> -  Docked bike users traveled the longest trip duration on average.

#### Which week days do casual riders record highest number of trips?

```r
all_valid_trips %>%
  filter(member_casual=="casual") %>%
  group_by(week_day, wday) %>%
  summarise(avg_trip_duration = mean(trip_duration), number_of_trips = n()) %>%
  ggplot(aes(x=week_day, y=number_of_trips)) +
  geom_bar(stat = "identity", fill = "sky blue") +
  theme_minimal()+
  ggtitle("Total trips per Weekday by Casual riders")+
  xlab("")+ylab("")+theme(axis.line = element_line(color='black'))
```

![Trips by week day by casual riders](img/weekday_trips.png)

> -  Casual riders record highest number of trips on weekends, with a peak on Saturdays

#### Which week days do annual members record highest number of trips?

```r
all_valid_trips %>%
  filter(member_casual=="member") %>%
  group_by(week_day, wday) %>%
  summarise(avg_trip_duration = mean(trip_duration), number_of_trips = n()) %>%
  ggplot(aes(x=week_day, y=number_of_trips)) +
  geom_bar(stat = "identity", fill = "#D5EA67") +
  theme_minimal()+
  ggtitle("Total trips per Weekday by Annual members")+
  xlab("")+ylab("")+theme(axis.line = element_line(color='black'))
```

![Trips by week day by annual members](img/weekday_trips2.png)

> -  Annual members record higher number of trips between Mondays and Thursdays, with a peak on Tuesdays.

#### Which week days do the membership types record highest average trip duration?

```r
all_valid_trips %>%
  group_by(week_day, wday, member_casual) %>%
  summarise(avg_trip_duration = mean(trip_duration), number_of_trips = n()) %>%
  ggplot(aes(x=week_day, y=avg_trip_duration, fill=member_casual)) +
  geom_bar(stat = "identity") +
  theme_minimal()+
  ggtitle("Avg. Trip duration per Weekday")+
  xlab("")+ylab("")+theme(axis.line = element_line(color='black'))
```

![Trip duration per week day by casual riders](img/weekday_trip_duration.png)

> -  Casual riders travel twice as long as annual members in trip duration per day on average
> -  Longer trip durations are recorded during weekends (Saturday & Sunday)

#### What are the most used routes by the membership types?

To evaluate this measure, there is need to create the route column

```r
all_valid_trips$route <- paste(all_valid_trips$start_station_name, "_to_", all_valid_trips$end_station_name)

head(all_valid_trips$route)
```

```r
[1] "Michigan Ave & Oak St _to_ Michigan Ave & Oak St"
[2] "Desplaines St & Kinzie St _to_ Kingsbury St & Kinzie St"
[3] "Michigan Ave & Oak St _to_ Michigan Ave & Oak St"
[4] "Michigan Ave & Oak St _to_ Michigan Ave & Oak St"
[5] "Kingsbury St & Kinzie St _to_ Desplaines St & Kinzie St"
[6] "Sheridan Rd & Noyes St (NU) _to_ Sheridan Rd & Noyes St (NU)"
```

#### Which routes are the most used routes by casual riders

```r
all_valid_trips %>%
  filter(member_casual == "casual") %>%
  group_by(route) %>%
  summarise(avg_trip_duration = mean(trip_duration), number_of_trips = n(), .groups = "keep") %>%
  arrange(-number_of_trips) %>%
    head(5) %>%
  ggplot(aes(x=reorder(route, +number_of_trips), y=number_of_trips))+
  geom_bar(stat = "identity", fill="sky blue")+
  theme_minimal()+
  geom_text(aes(label=paste0(round((number_of_trips/1000),1),"K")))+
  xlab("")+ylab("")+theme(axis.line = element_line(color='black'))+
  coord_flip()
```

![Most used routes by casual riders](img/routes.png)

> -  Most used route by Casual riders is a round trip - from and to Streeter Dr & Ave.
> -  Majority of trips by Casual riders are round trips.

#### Which routes are the most used routes by annual members

```r
all_valid_trips %>%
  filter(member_casual == "member") %>%
  group_by(route) %>%
  summarise(avg_trip_duration = mean(trip_duration), number_of_trips = n(), .groups = "keep") %>%
  arrange(-number_of_trips) %>%
    head(5) %>%
  ggplot(aes(x=reorder(route, +number_of_trips), y=number_of_trips))+
  geom_bar(stat = "identity", fill="sky blue")+
  theme_minimal()+
  geom_text(aes(label=paste0(round((number_of_trips/1000),1),"K")))+
  xlab("")+ylab("")+theme(axis.line = element_line(color='black'))+
  coord_flip()
```

![Most used routes by annual members](img/routes2.png)

> -  Most used route by annual members is Ellis Ave & 60th St to University Ave & 57th St
> -  Trips by Annual Member are one way trip per time.

### Data Visualization

Using the Microsoft PowerBI platform, I came up with the dashboard visualization below to enable top management to easily visualize the insights in the dataset at a glance, to guide decision making.

The dynamic dashboard can be accessed [here](https://www.novypro.com/project/bike-share-user-analysis-by-ekeoma-agu)

![Analysis dashboard](img/dashboard.png "This is the dashboard visualization of the analysis")

### Recommendations

I came up with the following recommendations for the marketing manager after carefully exploring the data.

> -  **Create Membership Discounts on Weekends:**
>    Since majority of casual riders trips happen on weekends, and they spend twice as much time as annual members per trip, a trip discount for trips with duration above a set limit (say 20mins) will attract casual riders to subscribe for annual membership. This bonus will not incur any loss to the organization as current annual members hardly make trips above 14(mins) in duration on weekends.
> -  **Create Email Reminders and Notifications:**
>    Use email or notification reminders regularly on weekends to notify casual riders that they stand a chance of discounted trips on weekends for trips above a set limit when they opt for the annual membership subscription. This strategy will persuade casual riders to switch membership status so as to enjoy the benefits.
> -  **Leverage Billboard Marketing Campaign Strategy:**
>    Since the analysis revealed the top 5 routes traveled by casual riders, advert placements on digital billboards along those routes notifying casual riders of the opportunity to enjoy discounts on their weekend trips will attract some casual riders to convert to annual membership.
