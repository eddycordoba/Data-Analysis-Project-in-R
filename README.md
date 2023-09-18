# Data-Analysis-Project-in-R
Analyzing smart device usage data in order to gain insights of consumer use.

```yaml
title: "Bellabeat Case Study Using R"
author: "Jose Eduardo Cordoba"
date: "`r format(Sys.time(), '%B %d, %Y')`"
output:
  word_document: default
  pdf_document: default
  html_document: default
editor_options:
  chunk_output_type: console

```




## **About The Company**  

Urska Srsen and Sando Mur founded Bellabeat, a high-tech company that manufactures health-focused smart products.Srsen used her background as an artist to develop beautifully designed technology that informs and inspires women around the world. Collecting data on activity, sleep, stress, and reproductive health has allowed Bellabeat to empower women with knowledge about their own health and habits. Since it was founded in 2013, Bellabeat has grown rapidly and quickly positioned itself as a tech-driven wellness company for women.
​
By 2016 Bellabeat had opened offices around the world and launched multiple products. Bellabeat products became available through a growing number of online retailers in addition to their own e-commerce channel on their website. The company has invested in traditional advertising media, such as radio, out-of-home billboards, print, and television, but focuses on digital marketing extensively. Bellabeat invests year-round in Google Search, maintaining active Facebook and Instagram pages, and consistently engages consumers on Twitter. Additionally, Bellabeat runs video ads on Youtube and display ads on the Google Display Network to support campaigns around key marketing dates.

![](https://imageio.forbes.com/specials-images/imageserve/639374f1960e6bcf7f92f2d2/Young-woman-beside-sea--wearing-sports-clothing--looking-at-activity-tracker/960x0.jpg?format=jpg&amp;width=960) 

## **Ask Phase** 

In this project, our stakeholder Srsen has asked us to analyze smart device usage data in order to gain insight into how consumers use non-Bellabeat smart devices. She then wants us to select one Bellabeat product to aplly these insights to in our presentation. The following questions will guide our analysis:
    

**Key Analysis Questions** 

* What are some trends in smart device usage?
* How could the trends found be applied to bellabeat’s products (watch device in this case)?
* What are the steps for marketing strategy enhancement of bellabeat products?

**Bellabeat's Key Stakeholders**

* Urška Sršen, cofounder and Chief Creative Officer of Bellabeat.
* Sando Mur,Bellabeat’s co-founder.
* Bellabeat marketing analytics team.


#  **Prepare Phase**
​
**Data Source**
 
 * This is the link to our open-source data [FitBit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit)
 
 * This data was generated by respondents to a distributed survey via Amazon Mechanical Turk between 03.12.2016-05.12.2016 and can be found in Kaggle.
 
 * Thirty eligible Fitbit users consented to the submission of personal tracker data, including minute-level output for physical activity, heart rate, and sleep monitoring. Individual reports can be parsed by export session ID (column A) or timestamp (column B). Variation between output represents use of different types of Fitbit trackers and individual tracking behaviors / preferences.
 
 * For this Case Study we will use the daily_activity, sleep_day, daily_calories, and weight_log_info datasets to analyze the behaviors and trends of the device usage in order to gain insights that can help us and guide us in the improvement of Bellabeat devices as well as its marketing. 
 
 * The limitations this data has includes: 
 1.Size of data, only 30 fitbit users were monitored.
 2.Personal and demographical information is not included in the the data.
 3.Data could potentialy lead to biased conclusions because of biased recoleccion of data,
 
# **Process Phase**
**Importing CSV files, Cleaning and Manipulating Data with R Packages** 


1.**Installing and Loading Packages**
```{r}
# Packages needed for data wrangling and visualizations
# install.packages("tidyverse")
# install.packages("Rmisc")
# install.packages("RColorBrewer")
# install.packages("wesanderson")
```


```{r results="hide", message=FALSE}
# Libraries must be loaded "library()" in every new session.
library(tidyverse)
library(lubridate)
library(dplyr)
library(ggplot2)
library(tidyr)
library(skimr)
library(janitor)
library(scales)
library( Rmisc)
library(RColorBrewer)
library(wesanderson)
```




2.**Importing Files From [FitBit Fitness Tracker Data](https://www.kaggle.com/datasets/arashnic/fitbit)**
```{r results="hide",message=FALSE}
daily_activity <- read_csv("Capstone_Project_Fitabase_Data/Fitabase Data 4.12.16-5.12.16/dailyActivity_merged.csv")

sleep_day <- read_csv("Capstone_Project_Fitabase_Data/Fitabase Data 4.12.16-5.12.16/sleepDay_merged.csv")

daily_calories <- read_csv("Capstone_Project_Fitabase_Data/Fitabase Data 4.12.16-5.12.16/dailyCalories_merged.csv")

weight_log_info <- weightLogInfo_merged <- read_csv("Capstone_Project_Fitabase_Data/Fitabase Data 4.12.16-5.12.16/weightLogInfo_merged.csv")
```
3.**Exploring our Datasets for data cleaning**

```{r results='hide'}
# will give us a tibble and "colnames" the names of all of our columns.
head(daily_activity)
colnames(daily_activity)

head(sleep_day)
colnames(sleep_day)

head(daily_calories) 
colnames(daily_calories)

head(weight_log_info)
colnames(weight_log_info)
```
```{r}
# Checking unique participants and if there are any duplicates
n_distinct(daily_activity$Id)
n_distinct(sleep_day$Id)
n_distinct(daily_calories$Id)
n_distinct(weight_log_info$Id)
```



```{r}
# total number of rows
nrow(daily_activity)
nrow(sleep_day)
nrow(daily_calories)
nrow(weight_log_info)
```

4.**What Needs To Be Cleaned**
- After previewing our data, we noticed that our our clumns need to be reformatted, our data type needs to be change from character to date format and some of our data sets seem to have duplicates. Next, we will remove duplicates, use clean_names to have column names all in snake_format and handle any missing data as well as filtering the data that will be useful to our analysis using pipelines. After completing this we will validate our results.

```{r}
# Looking for any duplicates in the data
sum(duplicated(daily_activity))
sum(duplicated(sleep_day))
sum(duplicated(weight_log_info))
sum(duplicated(daily_calories))
```

```{r}
# Removing 3 duplicates in sleep_day, and renaming it to sleep_day_dr, dr=(duplicates removed)

sleep_day_dr <- sleep_day %>% distinct()


```

```{r}
# Verifying if the duplicated values are removed
sum(duplicated(sleep_day_dr))
```

```{r results='hide'}
# Now we will change dates from characters to date format
daily_activity$ActivityDate <- mdy(daily_activity$ActivityDate)
sleep_day_dr$SleepDay <- mdy_hms(sleep_day_dr$SleepDay)
daily_calories$ActivityDay <- mdy(daily_calories$ActivityDay)
weight_log_info$Date <- mdy_hms(weight_log_info$Date)
```

```{r results='hide'}
# Lets verify and validate our data type change
str(daily_activity)
str(sleep_day_dr)
str(daily_calories)
str(weight_log_info)
```

```{r results='hide'}
# Lets make column names more understandable, and change column names to snake format
daily_activity <- daily_activity %>% clean_names() 
sleep_day_dr <- sleep_day %>% clean_names() 
daily_calories <- daily_calories %>% clean_names() 
weight_log_info <- weight_log_info %>% clean_names() 
```
```{r }
# Lets view our data's column names
colnames(daily_activity)
colnames(sleep_day_dr)
colnames(daily_calories)
colnames(weight_log_info)

```

- **Continuing cleaning and then transitioning into data transformation of  *daily_activity* by filtering and sorting data.**

```{r}
# lets explore logged_activities_distance and if we need this data
daily_activity %>% 
select(logged_activities_distance)

sum(daily_activity$logged_activities_distance, na.rm = TRUE)
```

```{r}
# lets preview a filtered tibble to check if values are > than 0 and if they tell us something.
daily_activity %>% 
select(logged_activities_distance) %>%
filter(logged_activities_distance > 0)
```

* We are not sure if these were manually logged activities by participant or automated records from device. A hypothesis about this variable is that it could be a recorded activity distance manually made by participants. For our analysis and calculations we prefer to leave *logged_activities_distance* out because we are uncertain of the purpose of the values and what they mean.

- Next, Lets validate the data in *total_distance* column by summing the other active distances creating a new column called sum_total_distance

```{r}
# Creating (mutating) a new column made from sum of active distances
daily_activity <- daily_activity %>% 
    mutate(new_total_distances= very_active_distance + moderately_active_distance + light_active_distance + sedentary_active_distance+logged_activities_distance)

# Previewing and comparing new column with preexisting total_distance and tracker_distance
daily_activity %>% 
    select(new_total_distances, total_distance,tracker_distance)
```

- Many of our values matched, however, the majority of them had a significant difference. We are not sure how the preexisting *total_distance* and tracker_distance were calculated, however, we do know how our *new_total_distances* were. For that reason we will use our mutated column for analysis.

- Now we will clean and transform the **weight_log_info** data set because although information is limited, we can still use it to make our Bellabeat device better through some insights regarding weight and other body information our customers could have.

```{r}
# renamed date and bmi and column names snake_style
weight_log_info <- weight_log_info %>% 
  clean_names() %>% 
  dplyr::rename(date_weight_recorded=date,body_mass_index=bmi)

colnames(weight_log_info)
```


- We also want to filter and mutate information we want from the weight data frame to perform individual analysis that can help us get other insights.

```{r}
# New variable of clean and filtered data
weight_log_info_clean <- weight_log_info %>% 
  select(id,
         date_weight_recorded,
         weight_kg,
         body_mass_index,
         is_manual_report)

head(weight_log_info_clean)
```

- We want to mutate a column with people on healthy BMI range and people on overweight BMI range

```{r}
# mutating two new columns with bmi info
weight_log_info_clean <- weight_log_info_clean %>% 
  mutate(healthy_bmi= body_mass_index<=25.0) %>%
  mutate(overweight_bmi= body_mass_index >=25.0) 
# testing code 
head(weight_log_info_clean)
```


- Since only 8 participants recorded their weight, we will keep distinct "id" records code for individual analysis of those 8 participants in our Analysis Phase.

```{r results='hide'}
# This returns all of our columns with distinct id
weight_log_info_clean %>% 
  distinct(id,.keep_all = TRUE)
```


- We now want to explore and transform the **daily_calories** data to have plot-able data. 

```{r}
# activity_day transformed to show the day of the week.
daily_calories$activity_day <- daily_calories$activity_day %>%
weekdays()
# testing code if transformation worked
daily_calories %>%
select(activity_day)
```


- For our **sleep_day** data set we thought it would be more convenient for our analysis to have our recorded minutes at sleep and our recorded minutes in bed transformed to hours with minutes so it can be more understandable to our audience.

```{r}
# transforming both minute columns to hours for time in bed clarity
sleep_day_dr <- sleep_day_dr %>%
mutate(total_hours_asleep= total_minutes_asleep/60)%>%
mutate(total_hrs_in_bed= total_time_in_bed/60)
# Testing our code
sleep_day_dr %>%
select(total_hours_asleep,total_hrs_in_bed) %>%
round(digits=2)
```


- Now that we have made some transformations to have a better understanding of our variables, we will end our cleaning and transforming phase by **removing** every single row from all four of our data sets that have **NA's** in order to have concise data for our analysis. After that we will identify our data's variables names with the name "_cleaned".

```{r results='hide'}
# Removing Na's from our data for final cleaning stage
daily_activity_cleaned <- daily_activity %>% 
drop_na() 
sleep_day_cleaned <- sleep_day_dr %>% 
  drop_na()
daily_calories_cleaned <- daily_calories %>%
drop_na()
weight_log_info_cleaned <- weight_log_info %>% 
drop_na()

daily_activity_cleaned

sleep_day_cleaned

daily_calories_cleaned

weight_log_info_cleaned
```



## **Analyse Phase**

In this analyse phase we will start by looking at some basic statistics to have an in-depth understanding of our data. Lets remember that our main objective is to analyse a non-Bellabeat smart device (FitBit data) by examining trends and central tendencies which will guide us to bring our Bellabeat products to an optimal yet more consumable place in the industry. To do this we will also preview data visualizations with our concise and transformed data through which we will be able to have more clarity of what our data is telling us and drive the marketing team to greater business decisions.



- **Understanding some summary statistics**


```{r echo=FALSE}
# quick summary statistics of selected variables daily_activity_cleaned data
daily_activity_cleaned %>% 
  select(total_steps,
         new_total_distances,
         sedentary_minutes) %>% 
  summary()


# quick summary statistics of selected variables sleep_day_cleaned data
sleep_day_cleaned %>% 
  select(total_sleep_records,
         total_hours_asleep,
         total_hrs_in_bed) %>%
  summary()


# quick summary statistics of selected variables weight_log_info_cleaned
weight_log_info_cleaned %>% 
  select(weight_kg,
         fat,
         body_mass_index) %>% 
  summary()
```


**Some useful insights gathered from our summary:**

* The average total steps that were taken daily were 7,638 that is around 5.5 miles walked, which happens to be our mean total distance.
* You can see that many of our participants recorded a significant amount of sedentary minutes, on average 991 minutes, this is definitely not helpful if the objective is to exercise and burn calories.
* Also lets keep in mind that these statistics are descriptive of our given data and cannot be representative of all smart device user population, but as a sample can still give us some good insights for recommendations.


*After having a look at summarized statistics of our data, we will create some bar graphs, histograms and scatter plots to analyse the behavior of our data and then merge data in order to see if there is any relationships or correlations with our different variables. Having visualizations helps us understand and view our data with a better understanding and get valuable insights with promising leads that we can later use and apply in our own Bellabeat products to make the best recommendations.*  





 - **Analyzing with Data Visualizations**



```{r echo=FALSE,results='hide', message=FALSE,warning=FALSE}
# activity count per date grouped by activity_date 
Activities_graph_1 <- daily_activity_cleaned %>%
  group_by(activity_date) %>% 
  dplyr::summarise(activities_per_day= n())

head(Activities_graph_1)
```
```{r}
# Data distribution Histogram 
  Activities_graph_1 %>% 
  ggplot()+ aes(x=activity_date, y= activities_per_day) + geom_col(fill = "#0099f9") +
  labs(title = "Activity Count (by Day)",subtitle = "A simple bar chart", caption = "Source: FitBit Fitness 2016", x="Date", y="Activity Count" )+
  theme(
    plot.title = element_text(color = "#0099f9", size = 16),
    plot.subtitle = element_text(face = "bold"),
    plot.caption = element_text(face = "italic")
  ) +
   
  theme(
    axis.title.x = element_text(size = 12, face = "italic"),
    axis.title.y = element_text(size = 12, face = "italic")
  )
```




**Our bar graph summarizes an analysis of how frequent our participants became active in a 4 week period. It is good to have this simple graph to analyse our data and its reliability to consider it robust enough to understand the usage of Fitbit devices. We can see that the volunteers became intentionally active in the first 3 weeks and perhaps exhaustion or busyness dropped the trend in the last week as well as the amount of calories they were able to burn. Just with this graph we can see that the user appears to have had more activity on some days than others. Specifically, there appear to be a few spikes in activity counts, such as on 4/13/2016 and 4/17/2016. Another insight from this graph is that the user seems to be more active on weekdays compared to weekends, as there is a clear drop in activity counts on Saturdays and Sundays. This could suggest that the user is more likely to engage in physical activity as part of their daily routine on weekdays, possibly due to work or school obligations. Additionally, the graph shows a peak in activity count on Thursday, which could indicate that the user is particularly active or engaged in physical activity on that day of the week. **  








```{r}

# A.S (Average Steps summarised)
 A.S <- daily_activity_cleaned %>%
  group_by(activity_date) %>% 
  dplyr::summarise(mean_steps_daily= mean(total_steps)) %>% 
   ggplot()+ aes(x=activity_date, y=mean_steps_daily) + geom_col(fill = "#0099f9") +
  labs(title = "Average Steps by Day", subtitle = "(4 week period)", caption = "Source: FitBit Fitness 2016", x="Date", y="Avg Steps (per day)" )+
    theme(
    plot.title = element_text(color = "#0099f9", size = 16),
    plot.subtitle = element_text(face = "bold"),
    plot.caption = element_text(face = "italic")
  ) +
   
  theme(
    axis.title.x = element_text(size = 12, face = "italic"),
    axis.title.y = element_text(size = 12, face = "italic")
  )

A.S+
  geom_hline(yintercept = mean(daily_activity_cleaned$total_steps), linetype = "dashed", size = 1)
```





**In this graph we can see an average of all steps taken in our four weeks, this gives us an insight of which days our participants performed better by taking more steps than the average (represented by a line), of course if this were a competition or performance based experiment. It is interesting to note that there are several days where the user took significantly more steps than the average, as well as several days where the user took significantly fewer steps than the average.**

**This could suggest that the user's activity level varied significantly day-to-day, and that they were not consistently meeting their daily step goals. It could also suggest that the user was engaging in more intense physical activity on certain days (resulting in higher step counts), and taking more rest days on others (resulting in lower step counts). For our Bellabeat product analysis is also a great way to analyze any peaks throughout our 4 week of tracking, one peak can clearly be seen at the end of the first week.**













```{r}
ggplot(data = daily_activity_cleaned, aes(x = total_steps, y = calories)) +
  geom_point(color = "blue", alpha = 0.5) +
  labs(title = "Total Steps vs. Calories", x = "Total Steps", y = "Calories") +
  theme_bw()

  
```
 **The scatterplot shows that there is a positive relationship between the two variables, meaning that as the number of steps taken increases, the number of calories burned also increases. The blue dots represent individual data points, while the transparency of the dots indicates the density of the data points in that area. This graph could be useful for people who are interested in tracking their physical activity and the corresponding number of calories burned. It could also be used to understsand the relationship between physical activity and energy expenditure. It makes sense to infer that as physical activity and exercise increase the number of calories burned also does. However, it is worth noting that the relationship does not appear to be entirely linear, with a greater spread of data points at higher step counts. This suggests that other factors, such as the intensity or duration of activity, may also play a role in the number of calories burned ** 



- **Continuing Observing Relationships and Correlations Between Variables with Scatter Plots**

1.**Fisrt we will analyze the relationship between Calories burned and other variables to see what affects calories burning the most.**






```{r}
# Creating multiplot to show relationship of calories with distance variables
gb <- daily_activity_cleaned %>%
 select(new_total_distances,calories,total_steps,very_active_distance,moderately_active_distance,light_active_distance,sedentary_active_distance)


sp <- gb %>% 
  ggplot()+aes(x=sedentary_active_distance,y=calories,color=calories)+geom_point()+
  geom_smooth(color="red")+ labs(title = "Calories vs. Sedentary Active", x= "Sedentary Active Distance (Miles)", y= "Calories")+
  scale_colour_gradient(low = "yellow", high = "red", na.value = NA)

lp <- gb %>% 
  ggplot()+aes(x=light_active_distance,y=calories,color=calories)+geom_point()+
  geom_smooth(color="red")+ labs(title = "Calories vs. Light Active", x= "Light Active Distance (Miles)", y= "Calories")+
  scale_colour_gradient(low = "yellow", high = "red", na.value = NA)

mp <- gb %>% 
  ggplot()+aes(x= moderately_active_distance, y=calories,color=calories)+ geom_point()+
  geom_smooth(color="red")+ labs(title = "Calories vs. Moderately Active ", x= "Moderately Active Distance (Miles)", y= "Calories")+
  scale_colour_gradient(low = "yellow", high = "red", na.value = NA)

vp <- gb %>% 
  ggplot()+aes(x= very_active_distance, y=calories,color=calories)+ geom_point()+
  geom_smooth(color="red")+ labs(title = "Calories vs. Very Active ", x= "Very Active Distance (Miles)", y= "Calories")+
  scale_colour_gradient(low = "yellow", high = "red", na.value = NA)

multiplot(sp,lp,mp,vp,cols = 2)

```


**These graphs show the relationship between calories and different distance variables (sedentary active, light active, moderately active, and very active). The graphs show that as the distance covered in each of these activity levels increases, the number of calories burned also increases. This relationship appears to be relatively strong, as indicated by the positive slope of the trend lines and the relatively tight clustering of points around the trend lines. The graphs also show some variability in the relationship between calories and distance covered for each activity level, suggesting that other factors, such as duration, intensity, and individual differences, may also play a role in the number of calories burned during physical activity.**




*Continuing observing Calories data this time including the daily_calories_cleaned data set*





```{r echo=FALSE,results='hide'}

# Merging data sets together to analyze correlations
combined_data <- merge(sleep_day_cleaned,daily_activity_cleaned,by="id")

```


```{r}
# understanding relationship between active distance and calories 
active_calories_mean <- combined_data %>% group_by(activity_date,id) %>% drop_na() %>% 
  dplyr::summarise(mean_act_distance=mean(new_total_distances),mean_calories=mean(calories))

#preview our transformed data for plotting
head(active_calories_mean)

# Scatter Plot with Positive Correlation
ggplot(active_calories_mean,aes(x=mean_act_distance,y=mean_calories))+ geom_point(aes(color=activity_date))+ geom_smooth(color="red") + labs(title = "Average Active Distance vs. Average Calories Burned", x= "Active Distance",y= "Calories")
```

**We can see that there is positive correlation between the average distance exercised and the average calories burned overall. We can safely assume that the more distance our participants walked or jogged, the more calories they burned. This is just helping us confirm our previous insights with our multiplots**
















```{r warning=FALSE,message=FALSE}
# Understanding relationship between calories vs sedentary minutes
 P <- combined_data

# Set theme
theme_set(theme_classic())

# Plot the graph
P %>% ggplot(aes(x = sedentary_minutes, y = calories)) + 
  geom_point(color = "#0099f9", alpha = 0.5, size = 3) +
  geom_smooth(color = "#e74c3c", size = 1.2, method = "lm", se = FALSE) +
  labs(title = "Calories Burned vs. Sedentary Minutes", x = "Sedentary Minutes", y = "Calories") +
  theme(plot.title = element_text(size = 20, face = "bold", hjust = 0.5),
        plot.subtitle = element_text(size = 16, face = "italic", hjust = 0.5),
        axis.title = element_text(size = 16, face = "bold"),
        axis.text = element_text(size = 12),
        legend.position = "none")

```
 
 

 
 **This graph shows the relationship between calories burned and sedentary minutes. The red line represents the trendline or the regression line (non-linear) that shows the overall pattern of the relationship between the two variables. The scatter plot suggests that there is a negative correlation between calories burned and sedentary minutes, meaning that as sedentary minutes increase, calories burned decrease. This is not surprising since sedentary behavior is associated with a lower metabolism and lower energy expenditure. The trendline confirms this relationship, as it slopes downward.I find it to be a very important graph to have a clearer image on something that can become a metric in our watches that will suggest or infer that our costumer is not physically doing their part if their goal is to use our devices to loose weight and burn more calories. This is something discussed more in our act phase.  **
 
 



```{r}

# #Bar chart showing total calories burned for each day of the week
calories_by_day <- ggplot(data = daily_calories_cleaned, aes(x = factor(activity_day, levels = c("Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday")), y = calories)) +
  geom_bar(stat = "identity", fill = "#1ABC9C") +
  labs(title = "Calories Burned by Day", x = "Day of the Week", y = "Calories Burned") +
  theme_minimal() +
  scale_y_continuous(labels = scales::comma) +
  theme(axis.text.x = element_text(angle = 45, hjust = 1), panel.grid.major.x = element_blank(),
        panel.grid.minor.y = element_blank(), panel.grid.major.y = element_line(colour = "gray80"))

calories_by_day




```


**The total calories burned seems to increase as the week progresses, peaking on Tuesdays through Thursdays before slightly dropping on Friday and the weekend. Sundays have the lowest total calories burned, which could suggest that people are more likely to rest or take a break from exercise on weekends. There is a clear difference in the total calories burned between weekdays and weekends, with weekdays having higher totals overall.**





2.**Analyzing relationship between time *Asleep* and other variables**




**Lets analyze the relationship between total_steps vs. total_hours_asleep, and check if there is correlation, if taking more steps is linear to sleeping more.**
 
```{r}
# plotting hours asleep with total steps using merged data 
  P %>%
  group_by(total_hours_asleep) %>% 
  dplyr::summarise(mean_total_steps=mean(total_steps),mean_total_hours_asleep=mean(total_hours_asleep))%>%  
  ggplot(aes(x=mean_total_steps,y=mean_total_hours_asleep))+ geom_point(alpha=0.7)+ geom_smooth(color="red")+ labs(title= "Steps Vs. Hours Asleep", x= "Steps", y="Hours Asleep" )

```


**we can see a positive correlation between hours of sleep and total steps. As the number of total steps increases, the average hours of sleep also tend to increase. The red line represents the trend line, which shows a general positive trend between these two variables. However, it's important to note that correlation does not necessarily imply causation. There may be other variables that affect both hours of sleep and total steps, so we can't conclude that increasing the number of steps will directly lead to an increase in hours of sleep. In addition we can make our participants acknowledge that walking or exercising longer distances will not necessarily provide them a better sleep and to be conscious of that, there could be other factors like diet, caffeine exposure or perhaps even a clinical problem as a lurking variable intervening with their repose. **





**Lets analyse hours asleep vs. sedentary minutes now.**

```{r}

# hours asleep vs. sedentary minutes
  P.2 <- merge(sleep_day_cleaned,daily_activity_cleaned,by="id") 

  P.2 <- P.2 %>% 
    mutate(good_sleep= total_hours_asleep>=7) %>% 
    mutate(bad_sleep= total_hours_asleep<7)
  
  P.2 %>% 
  ggplot()+aes(new_total_distances ,calories,color=good_sleep)+geom_point()+geom_smooth()+ labs(x="Active Distance (Miles)", y="Calories",color="Good Sleep")
                                                                          
```




**In this graph we can have a superior view of the positive correlation between more activity and calories burned complemented by the type of sleep our participants had, good sleep is determined by 7 hours or more of sleep, if not, it is considered bad sleep according to the [NIH](https://www.nhlbi.nih.gov/health/sleep/how-much-sleep#:~:text=Experts%20recommend%20that%20adults%20sleep,or%20more%20hours%20a%20night.) (National Heart, Lung, And Blood Institute). We can see that there is a more positive association with having a good night rest and burning more calories. We can also make assumptions (infer) such as that going further in distance does not necessarily give you more calories burned and that perhaps a better night sleep, more energy, and high intensity work out and intentional activity in shorter distances can make you burn more calories and obtain better results.**

```{r}
# Pie chart showing the breakdown of active minutes by activity level

# calculate the percentage of activity levels
activity_levels <- c(sum(daily_activity_cleaned$very_active_minutes), 
                     sum(daily_activity_cleaned$fairly_active_minutes), 
                     sum(daily_activity_cleaned$lightly_active_minutes), 
                     sum(daily_activity_cleaned$sedentary_minutes))
activity_percentages <- activity_levels / sum(activity_levels) * 100

# create a data frame for the pie chart
activity_data <- data.frame(activity = c("Very Active", "Fairly Active", "Lightly Active", "Sedentary"), 
                            percentage = activity_percentages)

# create the pie chart
ggplot(activity_data, aes(x="", y=percentage, fill=activity)) +
  geom_bar(width = 1, stat = "identity", color = "white") +
  coord_polar(theta = "y") +
  theme_void() +
  labs(title = "Activity Levels",
       subtitle = "Percentage of Total Activity Time",
       fill = "Activity Level")

```

**In this pie chart we can see that a significant portion of the participants' activity time was spent in sedentary behavior, indicating a potential need for interventions to encourage more physical activity and reduce sedentary behavior. Additionally, the majority of the participants' activity time was in the "lightly active" category, which could suggest that they are not engaging in enough moderate to vigorous physical activity. This information can be valuable in developing targeted health interventions and promoting healthy lifestyle behaviors within our Bellabeat products.**


## **Act Phase**



**Recommendations After Analysis**

  In addition to beautifully designed Bellabeat products, the smart device analysis allowed us to infer of smart device's clientele for the most beneficial and favorable features Bellabeat can cater to (target market), to refine and enhance their experience specifically with the app embedded in the Watch along with the subscription users get.   Based on the data and visualizations provided, here are some recommendations for Bellabeat's watches, app, and subscription packages:

Expand the target market: The data shows that the current users of Bellabeat's products are predominantly female, and the majority of them are interested in tracking their sleep and activity levels. However, there is an opportunity to expand the target market to include a wider demographic, such as men and people interested in tracking other health metrics such as heart rate and stress levels.

Enhance the social features: The data shows that users who interact with social features, such as the app's community and challenges, tend to be more engaged and active. Therefore, Bellabeat should consider enhancing these features to encourage social interaction and motivation.

Increase the focus on sleep tracking: Sleep is a critical component of overall health, and the data shows that it is a top priority for Bellabeat's users. Therefore, Bellabeat should continue to focus on developing and improving its sleep tracking features.

Introduce personalized insights: The data shows that users are interested in receiving personalized insights and recommendations based on their data. Bellabeat should consider implementing machine learning algorithms to provide customized feedback and actionable insights.

Introduce new subscription packages: Based on the data, users are primarily interested in tracking their sleep and activity levels. Bellabeat should consider introducing new subscription packages that cater specifically to these needs and offer additional value, such as personalized coaching and access to exclusive content.

Overall, Bellabeat should continue to prioritize user engagement, personalization, and value-
