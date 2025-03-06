# CyclisticBikeShare_CaseStudy
A case study which involves a bike sharing company based in chicago called Cyclistic. The purpose of this study is to demonstrate my data analytics skills by gaining insights and using the insights to promote growth of the company.

## About the Company
In 2016, Cyclistic launched a successful bike-share offering. Since then, the program has grown
to a fleet of 5,824 bicycles that are geotracked and locked into a network of 692 stations
across Chicago. The bikes can be unlocked from one station and returned to any other station
in the system anytime.

Until now, Cyclistic’s marketing strategy relied on building general awareness and appealing to
broad consumer segments. Oneapproach that helped make these things possible was the
exibility of its pricing plans: single-ride passes, full-day passes, and annual memberships.
Customers who purchase single-ride or full-day passes are referred to as casual riders.
Customers who purchase annual memberships are Cyclistic members.

Cyclistic’s finance analysts have concluded that annual members are much more profitable
than casual riders. Although the pricing flexibility helps Cyclistic attract more customers,
Moreno believes that maximizing the number of annual members will be key to future growth.
Rather than creating a marketing campaign that targets all-new customers, Moreno believes
there is a solid opportunity to convert casual riders into members. She notes that casual riders
are already aware of the Cyclistic program and have chosen Cyclistic for their mobility needs.
 
### Business Task
The director of marketing has a hypothesis that in order to grow their revenue they need people to purchase more annual memberships and for this to happen, they want to convert more casual customers to annual members. My team needs to figure out how casual and annual members use Cyclistic bikes differently so that we can create a new strategy for marketing. These insights need to be backed by data in order to get approval from the executive team.

<b>Stakeholder Expectations</b>
- Executive Team: Insights that are backed by real world data and can help to grow the company.
- Director of marketing: Data driven evidence to back up her hypothesis and recommendations.

### Data Preperation
The data provided for this case study is Cyclistic's historical trip data and is made available by Motivate International Inc. under this [license](https://divvybikes.com/data-license-agreement).

<b>Visual Studio Code</b> IDE was used along with <b>Python</b> programming language to work on the data.

The provided data covers the time period from Jaunary 2023 to December 2023.

The data was provided in the form of 12 csv files where each csv file represented each month from January to December. All the files had to be combined into a singular dataframe to perform analysis efficiently. The size of the final dataframe came out to be over <b>5 Million rows and 13 Columns</b>. More columns were created later on to help with analysis.

### Processing the Data
The processing phase includes cleaning the data of any null and duplicate values. Upon investigating, almost <b>1.4 Million</b> records came out to have one or more null values. This accounts for almost 25% of the size of our dataset. Most of the null data was in the form of station names and station ids. Since I was not using station names and ids in my analysis and all the other columns which were going to be used in the analysis had no null values I left the data as it is. Upon looking for duplicate values, no duplicated values were found in the dataset.

After cleaning the data, I used the starting date and time of each trip from the started_at column to extract the <b>month, day and hour</b> values into seperate columns. Then I used the started_at and ended_at columns to find the <b>duration</b> for each trip and stored it in a new duration column. This data will help us analyse how the rental service is used throughout different months, days of the week and at different hours of the day.

### Analysing the Data
In the analysis process I first looked at how long do people ride bikes for between casuals and members and it was a surprising discovery to find out that Casual customers ride for more than <b>2 times</b> longer duration than members with Casual customers having an average trip duration of <b>28.25 mins</b> while members having an average trip duration of <b>12.53 mins</b>.

Next I grouped the data by days of the week and found out that Casual customers tend to ride bikes for the longest duration on weekends which is <b>Saturday and Sunday</b> whereas Members have almost <ins>similar ride duration</ins> throughout the week.

Now after analysing the months, I found out that <b>August and July</b> are the busiest months for the company and this is when the company sees the most rides. These months happen to be the <b>peak tourist season</b> in chicago which seems to be the reason for extra traffic at this time.

Upon analysing data on an hourly basis, I found that the total number of trips consistently increase for both casual customers and members as the day proceeds but we see a <ins>sudden spike</ins> in number of trips for members during the <ins>morning</ins> rush hour from <b>7-8 am</b> which is also the time most people leave for work. After this the total number of trips start to decline a little until we reach lunch time and soon enough we see the <ins>peak</ins> number of rides for a day around <ins>evening</ins> from <b>4pm to 6pm</b> which is when I assume people start to leave their workplaces.

### Visualising the Data
![trip_count_hour.png](https://github.com/AmiteshB/Portfolio_DataAnalyst/blob/main/CyclisticBikeShare_CaseStudy/trip_count_hour.png, "Total Trips by Hour and Membership Type")

![trip_duration_day.png](https://github.com/AmiteshB/Portfolio_DataAnalyst/blob/main/CyclisticBikeShare_CaseStudy/trip_duration_day.png, "Average Trip Duration by Weekdays and Membership Type")

![trip_duration_month.png](https://github.com/AmiteshB/Portfolio_DataAnalyst/blob/main/CyclisticBikeShare_CaseStudy/trip_duration_month.png, "Average Trip Duration by Month and Membership Type")
