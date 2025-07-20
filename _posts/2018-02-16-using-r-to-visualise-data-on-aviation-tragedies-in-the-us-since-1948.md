---
layout: post
title: "Using R to Visualise Data on Aviation Tragedies in the US since 1948"
date: 2018-02-16 10:00:00 +0400
categories: [data-science, data-analysis]
tags: [r, data-visualization, exploratory-data-analysis, ggplot2, aviation, data-analysis, ntsb]
original_url: "https://medium.com/data-science/data-visualisations-of-aviation-tragedies-in-the-us-since-1948-4f1d7371b799"
canonical_url: "https://medium.com/data-science/data-visualisations-of-aviation-tragedies-in-the-us-since-1948-4f1d7371b799"
description: "An in-depth exploratory data analysis of aviation accidents in the US from 1948-2017 using R and ggplot2, examining relationships between weather conditions, phases of flight, engine types, and fatalities using NTSB data."
image: "/assets/images/posts/aviation-analysis.jpg"
republished: true
---

> *Originally published on [Medium]({{ page.original_url }}) on {{ page.date | date: "%B %d, %Y" }}*

![Aviation accident investigation](https://unsplash.com/photos/6ArTTluciuA/download?force=true&w=800)
*NTSB investigators looking at aviation accident scenes help us understand safety patterns*

In this post, I look at a dataset sourced from the [NTSB Aviation Accident Database](https://www.ntsb.gov/_layouts/ntsb.aviation/index.aspx) which contains information about civil aviation accidents. A dataset is available on [Kaggle](https://www.kaggle.com/khsamaha/aviation-accident-database-synopses) also.

This [Exploratory Data Analysis](https://towardsdatascience.com/tagged/exploratory-data-analysis) ([EDA](https://en.wikipedia.org/wiki/Exploratory_data_analysis)) aims to perform an initial exploration of the data and get an initial look at relationships between the various variables present in the dataset.

My aim is to also show how a simple understanding of [Data Analysis](https://towardsdatascience.com/tagged/data-analysis) and [Wrangling](https://towardsdatascience.com/tagged/data-wrangling) in [R](https://towardsdatascience.com/tagged/R) coupled with domain knowledge can provide a better understanding of relationships between variables in a dataset. The R Markdown file can found in this [GitHub repo](https://github.com/hamzaben86/Exploratory-Data-Analysis-Projects/tree/master/NSTB-Aviation-Accidents-EDA-R).

# Introduction

First, a quick intro of the dataset I'll be exploring. The dataset features 81,013 observations of 31 variables which are related to aviation accidents recorded.

```r
# Dataset dimensions
dim(aviation_data)
## [1] 81013    31
```

Variables provide information on a variety of topics including date and location of observations, model and type of aircraft, information on the sustained injuries to passengers and to the aircraft, and the reported weather conditions at the time.

```r
# Variable names in the dataset
names(aviation_data)
## [1] "Event.Id"               "Investigation.Type"    
## [3] "Accident.Number"        "Event.Date"            
## [5] "Location"               "Country"               
## [7] "Latitude"               "Longitude"             
## [9] "Airport.Code"           "Airport.Name"          
## [11] "Injury.Severity"        "Aircraft.Damage"       
## [13] "Aircraft.Category"      "Registration.Number"   
## [15] "Make"                   "Model"                 
## [17] "Amateur.Built"          "Number.of.Engines"     
## [19] "Engine.Type"            "FAR.Description"       
## [21] "Schedule"               "Purpose.of.Flight"     
## [23] "Air.Carrier"            "Total.Fatal.Injuries"  
## [25] "Total.Serious.Injuries" "Total.Minor.Injuries"  
## [27] "Total.Uninjured"        "Weather.Condition"     
## [29] "Broad.Phase.of.Flight"  "Report.Status"         
## [31] "Publication.Date"
```

# Data Wrangling

Since this an NTSB database from the United States, the majority of accidents (over 94%) in this database are observations in the US. Hence, I will be focusing on the accidents that took place in the US in this analysis. After removing international observations the new dataframe now features 76,188 observations.

```r
# Filter for US accidents only
us_aviation_data <- aviation_data[aviation_data$Country == "United States", ]
dim(us_aviation_data)
## [1] 76188    31
```

Some data wrangling was necessary of course (details of which can be found in the R Markdown file in the [GitHub repo](https://github.com/hamzaben86/NSTB-Aviation-Accidents-EDA-R)). For instance, the listed location names (city, state) was separated into two variables: one for the city and one for the state, for each observation.

The variable related to the observation's event date was broken down into the observation's event date by day, month, and year to investigate if there are any correlations between number of accidents and particular periods within a year.

Also, a better way of displaying data related to total fatalities for a given observation was to group number of fatalities in buckets (or bands). This will give us a better representation of the distribution of fatalities across the observations in the dataset.

# Univariate Plots Section

In this section, I will create univariate plots for variables of interest.

## Accidents by Year, Month, and Weekday

Let's plots frequency histograms for the year, month, and weekday of accidents in the dataset. The majority of the observations in the dataframe are from after the early 1980s onwards. So let's generate a plot from 1980 to 2017.

![Accidents by year](https://unsplash.com/photos/JKUTrJ4vK00/download?force=true&w=800)
*Aviation accidents by year show an overall declining trend*

The number of accidents has overall decreased by approx. 47% between 1982 and 2017 from approx. 3400 observations to approx. 1600 observations.

Next, let's look at observations distribution by months of the year.

The highest number of accidents in the dataset for a given year take place during northern hemisphere summer time (Jun-Jul-Aug). This is also likely to be correlated with the increased numbers of flights during the summer holiday period.

And finally, let's look at the observations distribution by day of the week.

The highest frequency of accidents in a given week take place during the weekend (Sat-Sun). Again, this is also likely to be correlated with the increased numbers of flights during the summer holiday period.

## Total Fatal Injuries

The next variable of interest relates to the Total Fatal Injuries for each observation in the dataset. This is quantified by the number of people fatally injured for each recorded observation. Let's group the number of fatalities in buckets as shown in the plot below. Note the use of the Log10 scale for the y-axis in the plot below.

The bulk of the recorded accidents have a number of fatalities <10 while some observations are displaying numbers of fatalities >100.

## Engine Types

Next, I look at the [aircraft engine types](https://en.wikipedia.org/wiki/Aircraft_engine) recorded in the dataset. I've abbreviated engine type names to improve labelling of the x-axis. Note the use of the Log10 scale for the y-axis in the plot below.

According to the plots above, the bulk of engine types in the reported accidents are [Reciprocating engine](https://en.wikipedia.org/wiki/Reciprocating_engine) types which were prevalent in commercial aircraft, particularly in aircraft built during the 20th century. Recent aircraft, like the [Airbus A380](https://en.wikipedia.org/wiki/Airbus_A380#Engines) or the Boeing [787 Dreamliner](https://en.wikipedia.org/wiki/Boeing_787_Dreamliner#Engines) rely on [Jet engines](https://en.wikipedia.org/wiki/Jet_engine#Turbine_powered) (e.g. TurboFan, TurboProp).

## Weather Conditions

Next, I look at the weather conditions recorded in the dataset. Two key aviation weather conditions to be familiar with here: [VMC](http://vmc%20which%20stands%20for%20visual%20meteorological%20conditions%20and%20imc%20stands%20for%20instrument%20meteorological%20conditions./) which means that conditions are such that pilots have sufficient visibility to fly the aircraft maintaining visual separation from terrain and other aircraft. [IMC](https://en.wikipedia.org/wiki/Instrument_meteorological_conditions) which means weather conditions require pilots to fly primarily by reference to instruments.

The bulk of accidents in the dataset take place during VMC weather conditions, which are great conditions for flying as VMC requires greater visibility and cloud clearance than IMC.

I imagine this would go against most people's intuition when it comes to the relationship between weather conditions and aviation accidents. Pilots are indeed well trained to fly in all sorts of weather conditions, relying solely on the [avionics instruments](https://en.wikipedia.org/wiki/Avionics) at their disposal.

## Broad Phases of Flight

Next, let's look at the phases of flight for the recorded accidents in the dataset. According to the plot, the bulk of accidents took place during landing or take-off. It is well known in the industry that these are high-risk — often referred to as "[critical phases of flight](http://aviationknowledge.wikidot.com/sop:critical-phases-of-flights)".

![Flight phases analysis](https://unsplash.com/photos/WKOKyXrKnJc/download?force=true&w=800)
*Take-off and landing are the most critical phases of flight*

# Bivariate Plots Section

Let's look at the relationship between pairs of variables that could show interesting relationships.

## Engine Types and Total Fatal Injuries

The bulk of the distribution has a total fatal injuries under 10, let's zoom in on that portion of the data. The R function [geom_jitter](http://ggplot2.tidyverse.org/reference/geom_jitter.html) was used to amplify the visualisation of the data points.

According to the plot, the bulk of the data for fatalities under 10 is with the engine type [reciprocating engine](https://en.wikipedia.org/wiki/Reciprocating_engine) type. The first plot shows that the Turbo-Fan engine has more outliers with higher number of fatalities than other engines. This is likely correlated to the use of Turbo-Fan engines use on large commercial aircraft.

## Weather Conditions and Total Fatal Injuries

As previously noted, weather conditions do not show a particularly strong relationship with total fatal injuries. The bulk of the distribution is associated with VMC weather conditions. However, that is likely due to the fact that the vast majority of flights are flown in VMC conditions.

## Phase of Flight and Total Fatal Injuries

Let's look at the relationship of Phase of Flight and Total Fatal Injuries.

The plots show that Take-Off and Approach are associated with outliers with high number of fatalities. As previously noted, these two phases of flight are often referred to as "[critical phases of flight](http://aviationknowledge.wikidot.com/sop:critical-phases-of-flights)" for that particular reason.

## Event Month and Weekday and Total Fatal Injuries

Let's look at the relationship of Event Date and Total Fatal Injuries. We'll focus on the bulk of the distribution with fatalities of less than 10. There doesn't seem to be any specific month or weekday showing a particularly high frequency of accidents.

## Broad Phase of Flight and Weather Conditions

The plots indicate that there is higher frequency of recorded observations for certain combinations of weather and phases of flight — for example, IMC flying conditions while during "cruise" or "approach" phases of flight.

## Broad Phase of Flight and Event Month

The plots indicate that there is a higher frequency of recorded observations for Northern summer months during Landing and Take-off. Across all months, the heat map also shows that the Take-off and Landing register the highest number of observations.

## Longitude and Latitude of Recorded Accidents

Plotting the Latitude vs Longitude of the accidents essentially gives us the map of the US. The plots also indicate that the coastal states are more heavily impacted compared to mid-western states and most of Alaska. This can be explained by the volume of flights to/from destinations in those areas of the US. A sad chart however as it shows that the vast majority of US States suffered an aviation tragedy between 1948 and 2017.

# Multivariate Plots Section

Now let's look at multivariate plots.

## Latitude vs Longitude of observations by Month of the Year

The distribution of accident months across latitude and longitude is fairly spread across the US, with a slightly higher prevalence of observations during the winter in southern states like Florida.

## Longitude and Latitude by Weather Conditions

Let's now look at latitude vs longitude add layer for the weather condition.

Weather condition VMC seems to be quite consistent except for patches of primarily IMC conditions for certain discrete areas of the mid-west.

## Broad Phase of Flight and Event Month by Weather Conditions

Let's now look at the relationship of Broad Phase of Flight vs Month by Weather Conditions. I decided to leave the observations with "unknown" weather conditions as there is what I perceive to be non-negligible number of observations, particularly for the "Cruise" phase of flight.

When looking at the relationship between Broad Phase of Flight vs Month and Weather Condition, we can see that accidents primarily take place during VMC weather conditions. However, for certain months of the year such as December and January, IMC conditions are a non-negligible portion of the observations, particularly during Approach and Cruise phases of flight.

## Total Fatal Injuries and Broad Phases of Flight by Weather Condition

Next, let's look at Total Fatal Injuries vs Broad Phases of Flight by Weather Condition. I decided to focus on total fatal injuries of less than 40 to highlight the bulk of the distribution of recorded fatal injuries.

IMC weather conditions are associated with accidents during "Cruise" and "Approach" phases of flight with higher frequencies than in VMC weather conditions.

## Total Fatal Injuries and Engine Type by Year

This plot is interesting as it shows how certain engines have become prevalent in different time periods. For instance, Turbo-Jet and Turbo-Fan powered aircraft show a higher number of fatalities in later years whereas reciprocating engines show a distribution of fatalities in earlier years, this corresponds to the increasing use of jet engines in modern aircraft.

![Aviation technology evolution](https://unsplash.com/photos/6ArTTluciuA/download?force=true&w=800)
*Evolution of aviation technology reflected in accident data*

# Concluding Remarks

I hope you enjoyed this EDA and learned a few things about aviation along the way. I hope that this post also shows the power of simple data visualisations with [ggplot2 in R](https://www.statmethods.net/advgraphs/ggplot2.html). This skill is particularly useful when exploring a dataset in the initial phase of the [data science pipeline](https://towardsdatascience.com/a-beginners-guide-to-the-data-science-pipeline-a4904b2d8ad3). I certainly enjoyed this fun little study done over the weekend.

**Key Findings:**
- Aviation accidents have decreased by ~47% from 1982 to 2017
- Most accidents occur during summer months and weekends (higher flight volume)
- Landing and take-off remain the most critical phases of flight
- Counterintuitively, most accidents occur in good weather conditions (VMC)
- Engine technology evolution is clearly visible in the accident patterns over time

---

*If you found this article helpful, feel free to connect with me on [LinkedIn](https://linkedin.com/in/hamzabendemra){:target="_blank" rel="noopener"} or check out my other articles on [Medium](https://medium.com/@hamzabendemra){:target="_blank" rel="noopener"}.*
