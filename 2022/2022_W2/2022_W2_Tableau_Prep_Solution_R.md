2022 W 2 preppin’ data Challenge - R Solution
================
Paula Munoz
2022/01/17

## 2022: Week 2 -The Prep School - Birthday Cakes

Challenge by Carl Allchin.

Challenge Link:
<https://preppindata.blogspot.com/2022/01/2022-week-2-prep-school-birthday-cakes.html>

Data Source:
<https://drive.google.com/file/d/1p8gt3cR3ATCeGK81pnT90x0a6dbCXst1/view>

### DETAILS

#### Requirements

-   Input the csv file (link above)
-   Remove any unnecessary fields
-   Form the pupil’s name in <First Name> <Last Name> format
-   Create the date for the pupil’s birthday in calendar year 2022
-   Work out what day of the week the pupil’s birthday falls on,
    Remember if the birthday falls on a Saturday or Sunday, we need to
    change the weekday to Friday
-   Work out what month the pupil’s birthday falls within
-   Count how many birthdays there are on each weekday in each month
-   Remove any unnecessary columns of data
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
input <- readr::read_csv('additional_files/PD 2022 Wk 1 Input - Input.csv')
```

    ## 
    ## ── Column specification ────────────────────────────────────────────────────────
    ## cols(
    ##   id = col_double(),
    ##   `pupil first name` = col_character(),
    ##   `pupil last name` = col_character(),
    ##   gender = col_character(),
    ##   `Date of Birth` = col_character(),
    ##   `Parental Contact Name_1` = col_character(),
    ##   `Parental Contact Name_2` = col_character(),
    ##   `Preferred Contact Employer` = col_character(),
    ##   `Parental Contact` = col_double()
    ## )

## INSPECTING DATA

-   Inspecting first five rows:

``` r
input %>%
  head() %>%
  kable()
```

|  id | pupil first name | pupil last name | gender | Date of Birth | Parental Contact Name\_1 | Parental Contact Name\_2 | Preferred Contact Employer | Parental Contact |
|----:|:-----------------|:----------------|:-------|:--------------|:-------------------------|:-------------------------|:---------------------------|-----------------:|
|   1 | Ronna            | Nellies         | Female | 12/21/2013    | Purcell                  | Ketti                    | Demizz                     |                1 |
|   2 | Rusty            | Andriulis       | Male   | 7/21/2012     | Vassili                  | Rivi                     | Brainbox                   |                1 |
|   3 | Roberta          | Oakeshott       | Female | 12/4/2011     | Lind                     | Haskell                  | Centidel                   |                2 |
|   4 | Lola             | Rubinfajn       | Male   | 6/29/2012     | Elie                     | Tresa                    | Edgeblab                   |                2 |
|   5 | Kamila           | Benedtti        | Female | 7/10/2012     | Adela                    | Clevey                   | Trudoo                     |                1 |
|   6 | Avery            | Colebourn       | Female | 8/30/2012     | Dalenna                  | Charley                  | Linktype                   |                1 |

-   Detailed summary:

``` r
input %>%
  skim()
```

|                                                  |            |
|:-------------------------------------------------|:-----------|
| Name                                             | Piped data |
| Number of rows                                   | 1000       |
| Number of columns                                | 9          |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |            |
| Column type frequency:                           |            |
| character                                        | 7          |
| numeric                                          | 2          |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |            |
| Group variables                                  | None       |

Data summary

**Variable type: character**

| skim\_variable             | n\_missing | complete\_rate | min | max | empty | n\_unique | whitespace |
|:---------------------------|-----------:|---------------:|----:|----:|------:|----------:|-----------:|
| pupil first name           |          0 |              1 |   2 |  12 |     0 |       935 |          0 |
| pupil last name            |          0 |              1 |   3 |  17 |     0 |       989 |          0 |
| gender                     |          0 |              1 |   4 |  11 |     0 |         8 |          0 |
| Date of Birth              |          0 |              1 |   8 |  10 |     0 |       713 |          0 |
| Parental Contact Name\_1   |          0 |              1 |   2 |  15 |     0 |       945 |          0 |
| Parental Contact Name\_2   |          0 |              1 |   2 |  14 |     0 |       940 |          0 |
| Preferred Contact Employer |          0 |              1 |   3 |  13 |     0 |       353 |          0 |

**Variable type: numeric**

| skim\_variable   | n\_missing | complete\_rate |   mean |     sd |  p0 |    p25 |   p50 |    p75 | p100 | hist  |
|:-----------------|-----------:|---------------:|-------:|-------:|----:|-------:|------:|-------:|-----:|:------|
| id               |          0 |              1 | 500.50 | 288.82 |   1 | 250.75 | 500.5 | 750.25 | 1000 | ▇▇▇▇▇ |
| Parental Contact |          0 |              1 |   1.49 |   0.50 |   1 |   1.00 |   1.0 |   2.00 |    2 | ▇▁▁▁▇ |

-   Glimpse data:

``` r
input %>%
  glimpse()
```

    ## Rows: 1,000
    ## Columns: 9
    ## $ id                           <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13…
    ## $ `pupil first name`           <chr> "Ronna", "Rusty", "Roberta", "Lola", "Kam…
    ## $ `pupil last name`            <chr> "Nellies", "Andriulis", "Oakeshott", "Rub…
    ## $ gender                       <chr> "Female", "Male", "Female", "Male", "Fema…
    ## $ `Date of Birth`              <chr> "12/21/2013", "7/21/2012", "12/4/2011", "…
    ## $ `Parental Contact Name_1`    <chr> "Purcell", "Vassili", "Lind", "Elie", "Ad…
    ## $ `Parental Contact Name_2`    <chr> "Ketti", "Rivi", "Haskell", "Tresa", "Cle…
    ## $ `Preferred Contact Employer` <chr> "Demizz", "Brainbox", "Centidel", "Edgebl…
    ## $ `Parental Contact`           <dbl> 1, 1, 2, 2, 1, 1, 2, 2, 1, 2, 2, 2, 1, 1,…

## DATA WRANGLING

### Remove unnecessary columns

``` r
tidy_data  <- input %>% select(-`Parental Contact Name_1`, -`Parental Contact Name_2`, -`Preferred Contact Employer`, -`Parental Contact`, -gender)

tidy_data%>%
  head(10) %>%
  kable() 
```

|  id | pupil first name | pupil last name | Date of Birth |
|----:|:-----------------|:----------------|:--------------|
|   1 | Ronna            | Nellies         | 12/21/2013    |
|   2 | Rusty            | Andriulis       | 7/21/2012     |
|   3 | Roberta          | Oakeshott       | 12/4/2011     |
|   4 | Lola             | Rubinfajn       | 6/29/2012     |
|   5 | Kamila           | Benedtti        | 7/10/2012     |
|   6 | Avery            | Colebourn       | 8/30/2012     |
|   7 | Valentino        | Klimko          | 12/23/2014    |
|   8 | Cal              | Shearwood       | 1/18/2015     |
|   9 | King             | Truswell        | 9/14/2012     |
|  10 | Towney           | Stichall        | 6/4/2015      |

### Form the pupil’s name

``` r
tidy_data <- tidy_data %>%
  mutate(Pupils_Name = str_c(`pupil first name`, ', ',`pupil last name`))

tidy_data%>%
  head(10) %>%
  kable() 
```

|  id | pupil first name | pupil last name | Date of Birth | Pupils\_Name       |
|----:|:-----------------|:----------------|:--------------|:-------------------|
|   1 | Ronna            | Nellies         | 12/21/2013    | Ronna, Nellies     |
|   2 | Rusty            | Andriulis       | 7/21/2012     | Rusty, Andriulis   |
|   3 | Roberta          | Oakeshott       | 12/4/2011     | Roberta, Oakeshott |
|   4 | Lola             | Rubinfajn       | 6/29/2012     | Lola, Rubinfajn    |
|   5 | Kamila           | Benedtti        | 7/10/2012     | Kamila, Benedtti   |
|   6 | Avery            | Colebourn       | 8/30/2012     | Avery, Colebourn   |
|   7 | Valentino        | Klimko          | 12/23/2014    | Valentino, Klimko  |
|   8 | Cal              | Shearwood       | 1/18/2015     | Cal, Shearwood     |
|   9 | King             | Truswell        | 9/14/2012     | King, Truswell     |
|  10 | Towney           | Stichall        | 6/4/2015      | Towney, Stichall   |

``` r
tidy_data %>% glimpse()
```

    ## Rows: 1,000
    ## Columns: 5
    ## $ id                 <dbl> 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, …
    ## $ `pupil first name` <chr> "Ronna", "Rusty", "Roberta", "Lola", "Kamila", "Ave…
    ## $ `pupil last name`  <chr> "Nellies", "Andriulis", "Oakeshott", "Rubinfajn", "…
    ## $ `Date of Birth`    <chr> "12/21/2013", "7/21/2012", "12/4/2011", "6/29/2012"…
    ## $ Pupils_Name        <chr> "Ronna, Nellies", "Rusty, Andriulis", "Roberta, Oak…

### 2. Create the date for the pupil’s birthday in calendar year 2022 (not academic year)

``` r
tidy_data <- tidy_data %>%
  # Convert Date of Birth to Date Format
  mutate(date = (mdy(tidy_data$`Date of Birth`))) %>%
  # Extract year date
  mutate(year_date = year(date)) %>%
  # Mutate "This Year's Birthday"
  mutate(This_years_birthday = case_when(
                                  year_date == 2011 ~ date + years(11),
                                  year_date == 2012 ~ date + years(10),
                                  year_date == 2013 ~ date + years(9),
                                  year_date == 2014 ~ date + years(8),
                                  year_date == 2015 ~ date + years(7)
  ))
  
tidy_data%>%
  head(10) %>%
  kable() 
```

|  id | pupil first name | pupil last name | Date of Birth | Pupils\_Name       | date       | year\_date | This\_years\_birthday |
|----:|:-----------------|:----------------|:--------------|:-------------------|:-----------|-----------:|:----------------------|
|   1 | Ronna            | Nellies         | 12/21/2013    | Ronna, Nellies     | 2013-12-21 |       2013 | 2022-12-21            |
|   2 | Rusty            | Andriulis       | 7/21/2012     | Rusty, Andriulis   | 2012-07-21 |       2012 | 2022-07-21            |
|   3 | Roberta          | Oakeshott       | 12/4/2011     | Roberta, Oakeshott | 2011-12-04 |       2011 | 2022-12-04            |
|   4 | Lola             | Rubinfajn       | 6/29/2012     | Lola, Rubinfajn    | 2012-06-29 |       2012 | 2022-06-29            |
|   5 | Kamila           | Benedtti        | 7/10/2012     | Kamila, Benedtti   | 2012-07-10 |       2012 | 2022-07-10            |
|   6 | Avery            | Colebourn       | 8/30/2012     | Avery, Colebourn   | 2012-08-30 |       2012 | 2022-08-30            |
|   7 | Valentino        | Klimko          | 12/23/2014    | Valentino, Klimko  | 2014-12-23 |       2014 | 2022-12-23            |
|   8 | Cal              | Shearwood       | 1/18/2015     | Cal, Shearwood     | 2015-01-18 |       2015 | 2022-01-18            |
|   9 | King             | Truswell        | 9/14/2012     | King, Truswell     | 2012-09-14 |       2012 | 2022-09-14            |
|  10 | Towney           | Stichall        | 6/4/2015      | Towney, Stichall   | 2015-06-04 |       2015 | 2022-06-04            |

### 3. Work out what day of the week the pupil’s birthday falls on. Remember if the birthday falls on a Saturday or Sunday, we need to change the weekday to Friday

``` r
tidy_data <- tidy_data %>%
  mutate(cake_needed_on = case_when(
    wday(This_years_birthday, label = TRUE, abbr = FALSE) == "Saturday" ~ "Friday",
    wday(This_years_birthday, label = TRUE, abbr = FALSE) == "Sunday" ~ "Friday",
    TRUE ~ as.character(wday(This_years_birthday, label = TRUE, abbr = FALSE)))
    )
           
tidy_data%>%
  head(10) %>%
  kable()            
```

|  id | pupil first name | pupil last name | Date of Birth | Pupils\_Name       | date       | year\_date | This\_years\_birthday | cake\_needed\_on |
|----:|:-----------------|:----------------|:--------------|:-------------------|:-----------|-----------:|:----------------------|:-----------------|
|   1 | Ronna            | Nellies         | 12/21/2013    | Ronna, Nellies     | 2013-12-21 |       2013 | 2022-12-21            | Wednesday        |
|   2 | Rusty            | Andriulis       | 7/21/2012     | Rusty, Andriulis   | 2012-07-21 |       2012 | 2022-07-21            | Thursday         |
|   3 | Roberta          | Oakeshott       | 12/4/2011     | Roberta, Oakeshott | 2011-12-04 |       2011 | 2022-12-04            | Friday           |
|   4 | Lola             | Rubinfajn       | 6/29/2012     | Lola, Rubinfajn    | 2012-06-29 |       2012 | 2022-06-29            | Wednesday        |
|   5 | Kamila           | Benedtti        | 7/10/2012     | Kamila, Benedtti   | 2012-07-10 |       2012 | 2022-07-10            | Friday           |
|   6 | Avery            | Colebourn       | 8/30/2012     | Avery, Colebourn   | 2012-08-30 |       2012 | 2022-08-30            | Tuesday          |
|   7 | Valentino        | Klimko          | 12/23/2014    | Valentino, Klimko  | 2014-12-23 |       2014 | 2022-12-23            | Friday           |
|   8 | Cal              | Shearwood       | 1/18/2015     | Cal, Shearwood     | 2015-01-18 |       2015 | 2022-01-18            | Tuesday          |
|   9 | King             | Truswell        | 9/14/2012     | King, Truswell     | 2012-09-14 |       2012 | 2022-09-14            | Wednesday        |
|  10 | Towney           | Stichall        | 6/4/2015      | Towney, Stichall   | 2015-06-04 |       2015 | 2022-06-04            | Friday           |

### 4. Work out what month the pupil’s birthday falls within

``` r
tidy_data <- tidy_data %>%
  mutate(Month = month(date, label = TRUE , abbr = FALSE))

tidy_data%>%
  head(10) %>%
  kable() 
```

|  id | pupil first name | pupil last name | Date of Birth | Pupils\_Name       | date       | year\_date | This\_years\_birthday | cake\_needed\_on | Month     |
|----:|:-----------------|:----------------|:--------------|:-------------------|:-----------|-----------:|:----------------------|:-----------------|:----------|
|   1 | Ronna            | Nellies         | 12/21/2013    | Ronna, Nellies     | 2013-12-21 |       2013 | 2022-12-21            | Wednesday        | December  |
|   2 | Rusty            | Andriulis       | 7/21/2012     | Rusty, Andriulis   | 2012-07-21 |       2012 | 2022-07-21            | Thursday         | July      |
|   3 | Roberta          | Oakeshott       | 12/4/2011     | Roberta, Oakeshott | 2011-12-04 |       2011 | 2022-12-04            | Friday           | December  |
|   4 | Lola             | Rubinfajn       | 6/29/2012     | Lola, Rubinfajn    | 2012-06-29 |       2012 | 2022-06-29            | Wednesday        | June      |
|   5 | Kamila           | Benedtti        | 7/10/2012     | Kamila, Benedtti   | 2012-07-10 |       2012 | 2022-07-10            | Friday           | July      |
|   6 | Avery            | Colebourn       | 8/30/2012     | Avery, Colebourn   | 2012-08-30 |       2012 | 2022-08-30            | Tuesday          | August    |
|   7 | Valentino        | Klimko          | 12/23/2014    | Valentino, Klimko  | 2014-12-23 |       2014 | 2022-12-23            | Friday           | December  |
|   8 | Cal              | Shearwood       | 1/18/2015     | Cal, Shearwood     | 2015-01-18 |       2015 | 2022-01-18            | Tuesday          | January   |
|   9 | King             | Truswell        | 9/14/2012     | King, Truswell     | 2012-09-14 |       2012 | 2022-09-14            | Wednesday        | September |
|  10 | Towney           | Stichall        | 6/4/2015      | Towney, Stichall   | 2015-06-04 |       2015 | 2022-06-04            | Friday           | June      |

### 5. Count how many birthdays there are on each weekday in each month

``` r
  tidy_data <- tidy_data %>%
  group_by(Month, cake_needed_on) %>%
  summarise(BDs_per_weekday_and_month = n()) %>%
  ungroup() %>%
  right_join(tidy_data) 
```

    ## `summarise()` has grouped output by 'Month'. You can override using the `.groups` argument.

    ## Joining, by = c("Month", "cake_needed_on")

``` r
tidy_data%>%
  head(10) %>%
  kable() 
```

| Month   | cake\_needed\_on | BDs\_per\_weekday\_and\_month |  id | pupil first name | pupil last name | Date of Birth | Pupils\_Name         | date       | year\_date | This\_years\_birthday |
|:--------|:-----------------|------------------------------:|----:|:-----------------|:----------------|:--------------|:---------------------|:-----------|-----------:|:----------------------|
| January | Friday           |                            33 |  71 | Cinnamon         | Stoyle          | 1/16/2012     | Cinnamon, Stoyle     | 2012-01-16 |       2012 | 2022-01-16            |
| January | Friday           |                            33 | 121 | Jared            | Koba            | 1/15/2012     | Jared, Koba          | 2012-01-15 |       2012 | 2022-01-15            |
| January | Friday           |                            33 | 141 | Hurley           | D’Alessandro    | 1/16/2014     | Hurley, D’Alessandro | 2014-01-16 |       2014 | 2022-01-16            |
| January | Friday           |                            33 | 153 | Aleece           | Bernardet       | 1/9/2015      | Aleece, Bernardet    | 2015-01-09 |       2015 | 2022-01-09            |
| January | Friday           |                            33 | 247 | Joly             | Wybrow          | 1/8/2015      | Joly, Wybrow         | 2015-01-08 |       2015 | 2022-01-08            |
| January | Friday           |                            33 | 294 | Miran            | Gowans          | 1/8/2015      | Miran, Gowans        | 2015-01-08 |       2015 | 2022-01-08            |
| January | Friday           |                            33 | 320 | Bobbe            | Rangall         | 1/22/2015     | Bobbe, Rangall       | 2015-01-22 |       2015 | 2022-01-22            |
| January | Friday           |                            33 | 373 | Zarah            | Symington       | 1/1/2012      | Zarah, Symington     | 2012-01-01 |       2012 | 2022-01-01            |
| January | Friday           |                            33 | 428 | Bernetta         | Barkaway        | 1/23/2013     | Bernetta, Barkaway   | 2013-01-23 |       2013 | 2022-01-23            |
| January | Friday           |                            33 | 455 | Stanley          | Monks           | 1/15/2012     | Stanley, Monks       | 2012-01-15 |       2012 | 2022-01-15            |

### 6. Remove unnecessary columns

``` r
tidy_data <- tidy_data %>%
  select(Pupils_Name, `Date of Birth`, This_years_birthday, Month, cake_needed_on, BDs_per_weekday_and_month)

tidy_data%>%
  head(10) %>%
  kable() 
```

| Pupils\_Name         | Date of Birth | This\_years\_birthday | Month   | cake\_needed\_on | BDs\_per\_weekday\_and\_month |
|:---------------------|:--------------|:----------------------|:--------|:-----------------|------------------------------:|
| Cinnamon, Stoyle     | 1/16/2012     | 2022-01-16            | January | Friday           |                            33 |
| Jared, Koba          | 1/15/2012     | 2022-01-15            | January | Friday           |                            33 |
| Hurley, D’Alessandro | 1/16/2014     | 2022-01-16            | January | Friday           |                            33 |
| Aleece, Bernardet    | 1/9/2015      | 2022-01-09            | January | Friday           |                            33 |
| Joly, Wybrow         | 1/8/2015      | 2022-01-08            | January | Friday           |                            33 |
| Miran, Gowans        | 1/8/2015      | 2022-01-08            | January | Friday           |                            33 |
| Bobbe, Rangall       | 1/22/2015     | 2022-01-22            | January | Friday           |                            33 |
| Zarah, Symington     | 1/1/2012      | 2022-01-01            | January | Friday           |                            33 |
| Bernetta, Barkaway   | 1/23/2013     | 2022-01-23            | January | Friday           |                            33 |
| Stanley, Monks       | 1/15/2012     | 2022-01-15            | January | Friday           |                            33 |

### 6. Output data

``` r
tidy_data %>%
    write_csv("additional_files/2020_W2_Output_From_R")
```
