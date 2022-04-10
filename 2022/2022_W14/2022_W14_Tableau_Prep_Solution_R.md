2022 Week 14 preppin’ data Challenge - R Solution
================
Paula Munoz
2022/04/10

## 2022: Week 14 -House of Games Winners

Challenge by Jenny Martin.

Challenge Link:
<https://preppindata.blogspot.com/2022/04/2022-week-14-house-of-games-winners.html>

Data Sources:
<https://drive.google.com/file/d/1p8gt3cR3ATCeGK81pnT90x0a6dbCXst1/view>

### DETAILS

#### Requirements

-   Input the data

-   Only keep relevant fields and rename certain fields to remove
    duplication -&gt; Ser. becomes Series -&gt; Wk. becomes Week -&gt; T
    becomes Tu -&gt; T 1 becomes Th -&gt; Total becomes Score -&gt; Week
    becomes Points -&gt; Week 1 becomes Rank

-   Filter the data to remove Series that have a null value, or are
    preceded by an ‘N’

-   Calculate the Points without double points Friday -&gt; Rank the
    players based on this new field -&gt; Create a field to determine if
    there has been a change in winner for that particular Series and
    Week

-   Rank the players based on their Score instead -&gt; Create a field
    to determine if there has been a change in winner for that
    particular Series and Week

-   Calculate the Score if the score on Friday was doubled (instead of
    the Points) -&gt; Rank the players based on this new field -&gt;
    Create a field to determine if there has been a change in winner for
    that particular Series and Week

-   Remove unnecessary fields

-   Output the data

## LOADING LIBRARIES

``` r
library(tidyverse)
library(skimr)
library(knitr)
library(tidyquant)
library(lubridate)
```

## IMPORTING DATA

Data resides in a .csv file

``` r
input <- readr::read_csv('additional_files/PD 2022 Wk 1 Input - Input.csv', 
                           col_types = 
                             cols(
                                id = col_character()
                              ))
input_2 <- readr::read_csv('additional_files/PD 2022 WK 3 Grades.csv', 
                           col_types = 
                             cols(
                               `Student ID` = col_character()
                             ))
```

## INSPECTING DATA

-   Inspecting first five rows:

``` r
input %>%
  head() %>%
  kable()
```

| id  | pupil first name | pupil last name | gender | Date of Birth | Parental Contact Name\_1 | Parental Contact Name\_2 | Preferred Contact Employer | Parental Contact |
|:----|:-----------------|:----------------|:-------|:--------------|:-------------------------|:-------------------------|:---------------------------|-----------------:|
| 1   | Ronna            | Nellies         | Female | 12/21/2013    | Purcell                  | Ketti                    | Demizz                     |                1 |
| 2   | Rusty            | Andriulis       | Male   | 7/21/2012     | Vassili                  | Rivi                     | Brainbox                   |                1 |
| 3   | Roberta          | Oakeshott       | Female | 12/4/2011     | Lind                     | Haskell                  | Centidel                   |                2 |
| 4   | Lola             | Rubinfajn       | Male   | 6/29/2012     | Elie                     | Tresa                    | Edgeblab                   |                2 |
| 5   | Kamila           | Benedtti        | Female | 7/10/2012     | Adela                    | Clevey                   | Trudoo                     |                1 |
| 6   | Avery            | Colebourn       | Female | 8/30/2012     | Dalenna                  | Charley                  | Linktype                   |                1 |

``` r
input_2 %>%
  head() %>%
  kable()
```

| Student ID | Maths | English | Spanish | Science | Art | History | Geography |
|:-----------|------:|--------:|--------:|--------:|----:|--------:|----------:|
| 1          |    66 |      97 |      85 |      75 |  76 |      94 |        76 |
| 2          |    84 |      85 |      62 |      87 |  68 |      75 |        74 |
| 3          |    88 |      68 |      69 |      81 |  92 |      89 |        75 |
| 4          |    65 |      97 |      96 |      89 |  98 |      77 |        62 |
| 5          |    86 |      97 |      94 |      98 |  67 |      77 |        97 |
| 6          |    80 |      78 |      70 |      89 |  71 |      67 |        72 |

-   Glimpse both tables:

``` r
input %>%
  glimpse()
```

    ## Rows: 1,000
    ## Columns: 9
    ## $ id                           <chr> "1", "2", "3", "4", "5", "6", "7", "8", "…
    ## $ `pupil first name`           <chr> "Ronna", "Rusty", "Roberta", "Lola", "Kam…
    ## $ `pupil last name`            <chr> "Nellies", "Andriulis", "Oakeshott", "Rub…
    ## $ gender                       <chr> "Female", "Male", "Female", "Male", "Fema…
    ## $ `Date of Birth`              <chr> "12/21/2013", "7/21/2012", "12/4/2011", "…
    ## $ `Parental Contact Name_1`    <chr> "Purcell", "Vassili", "Lind", "Elie", "Ad…
    ## $ `Parental Contact Name_2`    <chr> "Ketti", "Rivi", "Haskell", "Tresa", "Cle…
    ## $ `Preferred Contact Employer` <chr> "Demizz", "Brainbox", "Centidel", "Edgebl…
    ## $ `Parental Contact`           <dbl> 1, 1, 2, 2, 1, 1, 2, 2, 1, 2, 2, 2, 1, 1,…

``` r
input_2 %>%
  glimpse()
```

    ## Rows: 1,000
    ## Columns: 8
    ## $ `Student ID` <chr> "1", "2", "3", "4", "5", "6", "7", "8", "9", "10", "11", …
    ## $ Maths        <dbl> 66, 84, 88, 65, 86, 80, 68, 82, 100, 81, 61, 94, 78, 63, …
    ## $ English      <dbl> 97, 85, 68, 97, 97, 78, 69, 76, 84, 71, 98, 80, 79, 92, 6…
    ## $ Spanish      <dbl> 85, 62, 69, 96, 94, 70, 100, 81, 79, 94, 83, 99, 65, 99, …
    ## $ Science      <dbl> 75, 87, 81, 89, 98, 89, 84, 96, 82, 73, 100, 85, 69, 68, …
    ## $ Art          <dbl> 76, 68, 92, 98, 67, 71, 94, 84, 60, 66, 93, 77, 84, 79, 9…
    ## $ History      <dbl> 94, 75, 89, 77, 77, 67, 90, 87, 62, 63, 87, 98, 87, 100, …
    ## $ Geography    <dbl> 76, 74, 75, 62, 97, 72, 68, 71, 97, 90, 70, 94, 93, 95, 6…

## DATA WRANGLING

### Remove unnecessary columns

``` r
# Keeping only Student ID and Gender from first table
tidy_data  <- input %>% select(id,gender)

tidy_data%>%
  head(10) %>%
  kable() 
```

| id  | gender |
|:----|:-------|
| 1   | Female |
| 2   | Male   |
| 3   | Female |
| 4   | Male   |
| 5   | Female |
| 6   | Female |
| 7   | Female |
| 8   | Male   |
| 9   | Female |
| 10  | Male   |

### Join the datasets

``` r
input_2 <- input_2 %>%
  #Renaming Student ID column under second dataset to id
  rename(id = `Student ID`)

tidy_data <- tidy_data %>%
  left_join(input_2)
```

    ## Joining, by = "id"

``` r
tidy_data%>%
  head(10) %>%
  kable() 
```

| id  | gender | Maths | English | Spanish | Science | Art | History | Geography |
|:----|:-------|------:|--------:|--------:|--------:|----:|--------:|----------:|
| 1   | Female |    66 |      97 |      85 |      75 |  76 |      94 |        76 |
| 2   | Male   |    84 |      85 |      62 |      87 |  68 |      75 |        74 |
| 3   | Female |    88 |      68 |      69 |      81 |  92 |      89 |        75 |
| 4   | Male   |    65 |      97 |      96 |      89 |  98 |      77 |        62 |
| 5   | Female |    86 |      97 |      94 |      98 |  67 |      77 |        97 |
| 6   | Female |    80 |      78 |      70 |      89 |  71 |      67 |        72 |
| 7   | Female |    68 |      69 |     100 |      84 |  94 |      90 |        68 |
| 8   | Male   |    82 |      76 |      81 |      96 |  84 |      87 |        71 |
| 9   | Female |   100 |      84 |      79 |      82 |  60 |      62 |        97 |
| 10  | Male   |    81 |      71 |      94 |      73 |  66 |      63 |        90 |

### Pivot the data to create one row of data per student and subject

### Rename the pivoted fields to Subject and Score

``` r
tidy_data <- tidy_data %>%
  pivot_longer(Maths:Geography,
               names_to = "Subject",
               values_to = "Score")
```

### Create a field that records whether the student passed each subject:

### Pass mark is 75 and above in all subjects

``` r
tidy_data <- tidy_data %>%
  mutate(Subject_Passed = case_when(Score >= 75 ~ 1,
                                    TRUE ~ 0))
  
tidy_data%>%
  head(10) %>%
  kable() 
```

| id  | gender | Subject   | Score | Subject\_Passed |
|:----|:-------|:----------|------:|----------------:|
| 1   | Female | Maths     |    66 |               0 |
| 1   | Female | English   |    97 |               1 |
| 1   | Female | Spanish   |    85 |               1 |
| 1   | Female | Science   |    75 |               1 |
| 1   | Female | Art       |    76 |               1 |
| 1   | Female | History   |    94 |               1 |
| 1   | Female | Geography |    76 |               1 |
| 2   | Male   | Maths     |    84 |               1 |
| 2   | Male   | English   |    85 |               1 |
| 2   | Male   | Spanish   |    62 |               0 |

### Create an average score per student based on all of their grades

### Aggregate the data per student to count how many subjects each student passed

### Round the average score per student to one decimal place

``` r
tidy_data <- tidy_data %>%
  group_by(id,gender) %>%
  summarise(Suject_Passed= sum(Subject_Passed),
            Students_Avg_Score = mean(Score)) %>%
  ungroup()
```

    ## `summarise()` has grouped output by 'id'. You can override using the `.groups` argument.

``` r
tidy_data%>%
  head(10) %>%
  kable()            
```

| id   | gender | Suject\_Passed | Students\_Avg\_Score |
|:-----|:-------|---------------:|---------------------:|
| 1    | Female |              6 |             81.28571 |
| 10   | Male   |              3 |             76.85714 |
| 100  | Male   |              7 |             83.14286 |
| 1000 | Female |              5 |             79.42857 |
| 101  | Female |              6 |             83.85714 |
| 102  | Male   |              7 |             86.42857 |
| 103  | Female |              3 |             72.57143 |
| 104  | Female |              5 |             77.71429 |
| 105  | Male   |              5 |             82.71429 |
| 106  | Male   |              4 |             77.00000 |

### Remove any unnecessary fields and output the data

``` r
tidy_data <- tidy_data %>%
  mutate(Students_Avg_Score = round(Students_Avg_Score,1))

tidy_data%>%
  head(10) %>%
  kable() 
```

| id   | gender | Suject\_Passed | Students\_Avg\_Score |
|:-----|:-------|---------------:|---------------------:|
| 1    | Female |              6 |                 81.3 |
| 10   | Male   |              3 |                 76.9 |
| 100  | Male   |              7 |                 83.1 |
| 1000 | Female |              5 |                 79.4 |
| 101  | Female |              6 |                 83.9 |
| 102  | Male   |              7 |                 86.4 |
| 103  | Female |              3 |                 72.6 |
| 104  | Female |              5 |                 77.7 |
| 105  | Male   |              5 |                 82.7 |
| 106  | Male   |              4 |                 77.0 |

### Outut data

``` r
tidy_data %>%
    write_csv("additional_files/2020_W3_Output_From_R")
```
