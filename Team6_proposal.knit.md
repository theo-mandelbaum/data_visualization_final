---
title: "The Effect of Air Quality on Respiratory Illnesses"
subtitle: 'Math 268: Data Analysis and Visualization'
author: 'Joseph Linneman, Theo Mandelbaum, Rose Villareale'
date: "Due 11/7/2025"
output:
  html_document:
    highlight: espresso
    theme: spacelab
    toc: yes
    toc_depth: 2
    toc_float: yes
    fig_caption: true
    df_print: tibble
---






![An air-polluted highway in India](https://www.usatoday.com/gcdn/-mm-/471aaf7f3239eea52e40fa1593f3f5fe323c72a4/c=0-266-3225-2088/local/-/media/2017/11/08/USATODAY/USATODAY/636457399868151922-AP-India-Air-Pollution.5.jpg)



## Topics: Air quality, Pollution, Human Illness

We wanted to investigate the effects of pollution on chronic illnesses in the United States. We chose to focus on air quality rates to measure air pollution. We also chose to focus on respiratory illnesses and cancers since they would most likely be most affected by air quality compared to other forms of chronic illnesses. 


### Project Research Question

What is the strength of the relationship between state-level ozone pollution levels and chronic respiratory illness rates?

#### Followup Questions

- Does living closer to cities, in more urban areas, have an effect on the risk of chronic respiratory illness?
- Does living in areas with higher populations, have an effect on the risk of chronic respiratory illness?
- What are some of the most dangerous counties to live in based on rates of chronic respiratory illness?
- How has air quality changed over time and how has it affected chronic illness rates?



### Data and Data Source

#### Data on Chronic Disease Infection

[Link to Dataset](https://catalog.data.gov/dataset/u-s-chronic-disease-indicators)

#### Here is a snippet of our data, specifically the way that the values are measured.

![](./images/chronic_disease_data_units.png)

#### Next is a snippet of the metrics the we plan to use to sort our data; i.e. the location where the data is recorded.

![](./images/chronic_disease_data_location.png)


#### Data on National Air Quality Metrics

[Link to Dataset](https://aqs.epa.gov/aqsweb/airdata/download_files.html)

### About the dataset

The link provided directs you to a webpage with a number of downloadable csv files. For our research, we are using the the Annual Summary Data, specifically the annual aqi data by county for the years from 2015-2021. So, we are using 7 different csv files, but they are all the same structure, with each file representing a different year. The air quality is measure by AQI (Air Quality Index), which is a tool to report on air pollution levels, communicating potential health risks with a single number. The higher the AQI, the worse the air quality is.

#### Here are some snippets for the air quality metrics.

![](./images/air_quality1.png)

![](./images/air_quality2.png)

As you can see, each entry has a state, county, and year, allowing use to isolate big cities in the US. In terms of the data collection, we can see how many days deemed were healthy, unhealthy, unhealthy for sensitive groups, hazardous, etc. First, we will sort by state, as that's the data field that both of the data sets share in common. Once we've sorted the air quality data by states, we can compare the AQI measurements and the day totals, to other states.

Now that we've sorted our air quality data we can handle our chronic disease data. Similar to the air quality, we will sort the chronic diseases based on state; this field is called "LocationAbbr" in the chronic diseases data. In order to isolate respiratory illness, we'll sort this data by "Topic," specifying what type of disease the rows represents.

Once we've sorted both data sets as described above, we can answer the question; How strongly does air quality / pollution contribute to risk of chronic respiratory illness?

#### Given these datasets, we need to filter the chronic diseases data to figure out which diseases can be labelled as respiratory

First, we'll look at all of the possible options for the Topic columns:


``` r
unique_topics <- unique(chronic_diseases$Topic)
print(unique_topics)
##  [1] "Health Status"                                  
##  [2] "Cancer"                                         
##  [3] "Diabetes"                                       
##  [4] "Sleep"                                          
##  [5] "Immunization"                                   
##  [6] "Nutrition, Physical Activity, and Weight Status"
##  [7] "Oral Health"                                    
##  [8] "Arthritis"                                      
##  [9] "Asthma"                                         
## [10] "Cardiovascular Disease"                         
## [11] "Disability"                                     
## [12] "Mental Health"                                  
## [13] "Tobacco"                                        
## [14] "Alcohol"                                        
## [15] "Chronic Obstructive Pulmonary Disease"          
## [16] "Social Determinants of Health"                  
## [17] "Cognitive Health and Caregiving"                
## [18] "Chronic Kidney Disease"                         
## [19] "Maternal Health"
```
Based on the given topics, here's a list of which topics can be categorized as respiratory:

Definitely respiratory:

- "Asthma"

- "Chronic Obstructive Pulmonary Disease"

Could be / need more investigation:

- "Cancer"

- "Tobacco"

- "Sleep"

- "Nutrition, Physical Activity, and Weight Status"

Definitely not respiratory:

- "Mental Health"

- "Diabetes"

- "Immunization"

- "Oral Health"

- "Arthritis"

- "Cardiovascular Disease"

- "Disability"

- "Alcohol"

- "Cognitive Health and Caregiving"

- "Maternal Health"

- "Chronic Kidney Disease"

- "Health Status"

- "Social Determinants of Health"

If you look at the list of topics above, some of them are very broad, so to isolate the respiratory diseases, we will need to view all of the possible options for the Question column, within certain topics. After we've investigated our options the Question column, we can sort our disease data to only include respiratory diseases.

Only lung and bronchial cancer would be respiratory

``` r
chronic_diseases %>%
  filter(Topic == "Cancer") %>%
  pull(Question) %>%
  unique()
##  [1] "Invasive cancer (all sites combined), incidence"                                  
##  [2] "Cervical cancer mortality among all females, underlying cause"                    
##  [3] "Prostate cancer mortality among all males, underlying cause"                      
##  [4] "Breast cancer mortality among all females, underlying cause"                      
##  [5] "Invasive cancer (all sites combined) mortality among all people, underlying cause"
##  [6] "Lung and bronchial cancer mortality among all people, underlying cause"           
##  [7] "Colon and rectum (colorectal) cancer mortality among all people, underlying cause"
##  [8] "Mammography use among women aged 50-74 years"                                     
##  [9] "Cervical cancer screening among women aged 21-65 years"                           
## [10] "Colorectal cancer screening among adults aged 45-75 years"
```

Rows 1, 2, 4, and 6 are all respiratory issues

``` r
chronic_diseases %>%
  filter(Topic == "Tobacco") %>%
  pull(Question) %>%
  unique()
## [1] "Quit attempts in the past year among adult current smokers"                                                                                                                         
## [2] "Current cigarette smoking among adults"                                                                                                                                             
## [3] "Current smokeless tobacco use among high school students"                                                                                                                           
## [4] "Cigarette smoking during pregnancy among women with a recent live birth"                                                                                                            
## [5] "Current tobacco use of any tobacco product among high school students"                                                                                                              
## [6] "Current electronic vapor product use among high school students"                                                                                                                    
## [7] "Proportion of the population protected by a comprehensive smoke-free policy prohibiting smoking in all indoor areas of workplaces and public places, including restaurants and bars"
```

None of the sleep questions fit a respiratory disease

``` r
chronic_diseases %>%
  filter(Topic == "Sleep") %>%
  pull(Question) %>%
  unique()
## [1] "Short sleep duration among children aged 4 months to 14 years"
## [2] "Short sleep duration among high school students"              
## [3] "Short sleep duration among adults"
```

Here, row 1, 5 and 10 are all respiratory diseases

``` r
chronic_diseases %>%
  filter(Topic == "Nutrition, Physical Activity, and Weight Status") %>%
  pull(Question) %>%
  unique()
##  [1] "Children and adolescents aged 6-13 years meeting aerobic physical activity guideline"
##  [2] "Obesity among adults"                                                                
##  [3] "No leisure-time physical activity among adults"                                      
##  [4] "Consumed vegetables less than one time daily among adults"                           
##  [5] "Met aerobic physical activity guideline among high school students"                  
##  [6] "Consumed fruit less than one time daily among adults"                                
##  [7] "Consumed vegetables less than one time daily among high school students"             
##  [8] "Obesity among high school students"                                                  
##  [9] "Consumed regular soda at least one time daily among high school students"            
## [10] "Met aerobic physical activity guideline for substantial health benefits, adults"     
## [11] "Consumed fruit less than one time daily among high school students"                  
## [12] "Infants who were breastfed at 12 months"                                             
## [13] "Infants who were exclusively breastfed through 6 months"                             
## [14] "Obesity among WIC children aged 2 to 4 years"
```

So now, we're ready to filter our data. Since "Asthma" and "Chronic Obstructive Pulmonary Disease" are definitely respiratory, we will include all rows that have those topics. For the topics that were up for investigation, we will only include rows that have questions regarding respiratory issues.

``` r
respiratory_diseases <- chronic_diseases %>%
  filter(
    Topic %in% c("Asthma", "Cancer", "Tobacco", "Nutrition, Physical Activity, and Weight Status") &
    (
      (Topic == "Cancer" & Question == "Lung and bronchial cancer mortality among all people, underlying cause") |
      (Topic == "Tobacco" & Question %in% c(
        "Quit attempts in the past year among adult current smokers",
        "Current cigarette smoking among adults",
        "Cigarette smoking during pregnancy among women with a recent live birth",
        "Current electronic vapor product use among high school students"
      )) |
      (Topic == "Nutrition, Physical Activity, and Weight Status" & Question %in% c(
        "Children and adolescents aged 6-13 years meeting aerobic physical activity guideline",
        "Met aerobic physical activity guideline among high school students",
        "Met aerobic physical activity guideline for substantial health benefits, adults"
      )) |
      (Topic == "Asthma") |
      (Topic == "Chronic Obstructive Pulmonary Disease")
    )
  )
```



### Proposal Expectations Evaluation 

As we looked further into our data set we decided to filter the chronic illness data by specific illnesses. We chose to look at Asthma, respiratory illnesses, and lung, throat, and breast cancer. We chose these since they would be more likely to be affected by poor air quality. We also noticed that the data on chronic illnesses was sorted by state so we chose to define how urban a state is by population since we would not be able to compare counties that did and did not have major cities within them. We also felt the population of the state was a better measure than the number of major cities within the state because defining a major city would be based on population. We anticipated a positive correlation between the Air Quality Index (AQI) of a state and rates of illnesses. We also anticipated that higher populations of a state would have a positive correlation with AQI. 

### Anticipated Results

- Urban Citizen risk: Categorized bar chart by how urban an area is or how far from major cities
- Population size: Scatter plot with air quality on the y-axis and population on the x-axis
- Most Dangerous Counties: Detailed map where color represents the level of air quality
- Air Quality over time: Line plot with time on the x-axis and air quality on the y- axis



### Member Contributions
Theo: Found chronic disease data set and examined how it relates to the air quality data set. Helped refine research question.
Rose: Helped define research question as well as come up with follow up questions and how to best graph them.     
Joseph: Developed this file in coordination with team; examined data sources, created and refined research questions.



