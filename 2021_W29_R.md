2021\_W29\_prepping\_data
================
Paula Munoz
7/26/2021

## 2021: Week 29 - PD x WOW - Tokyo 2020 Calendar

Challenge by Tom Prowse with collaboration with the Workout Wednesday
team!

Challenge Link:
<https://preppindata.blogspot.com/2021/07/2021-week-29-pd-x-wow-tokyo-2020.html>.

### CHALLENGE DETAILS

### Inputs

The data comes from the Olympics website. (Note; this was taken on
Wednesday 14th July.

#### 1. Event Schedule

A list of all the event dates, times and locations throughout the games

#### 2. Venue Details

A list of all of the different venue locations

### Requirements

-   Input the Data
-   Create a correctly formatted DateTime field
-   Parse the event list so each event is on a separate row
-   Group similar sports into a Sport Type field
-   Combine the Venue table
-   Calculate whether the event is a ‘Victory Ceremony’ or ‘Gold Medal’
    event. (Note, this might not pick up all of the medal events.)
-   Output the Data

## LOADING LIBRARIES

``` r
library(tidyverse)
library(skimr)
library(lubridate)
library(knitr)
```

## IMPORTING DATA

Data resides in a .xlsx file, and it has two sheets: \* Event Schedule
\* Venue Details

``` r
event_schedule_tbl <- readxl::read_excel(path = '../DATA_WRANGLING/2021_W29_prepping_data_files/Olympic Events.xlsx', 
                                         sheet = 1)

venue_details_tbl <- readxl::read_excel(path = '../DATA_WRANGLING/2021_W29_prepping_data_files/Olympic Events.xlsx', 
                                        sheet = 2)
```

## INSPECTING DATA

**event scheduled table**

-   Inspecting first five rows:

``` r
event_schedule_tbl %>%
  head() %>%
  kable()
```

| Date             | Time | Sport             | Venue                            | Events                                                                              |
|:-----------------|:-----|:------------------|:---------------------------------|:------------------------------------------------------------------------------------|
| 21st\_July\_2021 | 1:00 | Baseball/Softball | Fukushima Azuma Baseball Stadium | Australia vs Japan, Italy vs United States, Mexico vs Canada                        |
| 21st\_July\_2021 | 8:30 | Football          | Sapporo Dome                     | Women’s Group E: Great Britain vs Chile, Women’s Group E: Japan vs Canada           |
| 21st\_July\_2021 | 9:00 | Football          | Miyagi Stadium                   | Women’s Group F: China vs Brazil, Women’s Group F: Zambia vs Netherlands            |
| 21st\_July\_2021 | 9:30 | Football          | Tokyo Stadium                    | Women’s Group G: Sweden vs United States, Women’s Group G: Australia vs New Zealand |
| 22nd\_July\_2021 | 1:00 | Baseball/Softball | Fukushima Azuma Baseball Stadium | United States vs Canada, Mexico vs Japan, Italy vs Australia                        |
| 22nd\_July\_2021 | 8:30 | Football          | Sapporo Dome                     | Men’s Group C: Egypt vs Spain, Men’s Group C: Argentina vs Australia                |

-   Detailed summary:

``` r
event_schedule_tbl %>%
  skim()
```

|                                                  |            |
|:-------------------------------------------------|:-----------|
| Name                                             | Piped data |
| Number of rows                                   | 709        |
| Number of columns                                | 5          |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |            |
| Column type frequency:                           |            |
| character                                        | 5          |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |            |
| Group variables                                  | None       |

Data summary

**Variable type: character**

| skim\_variable | n\_missing | complete\_rate | min | max | empty | n\_unique | whitespace |
|:---------------|-----------:|---------------:|----:|----:|------:|----------:|-----------:|
| Date           |          0 |              1 |  12 |  15 |     0 |        20 |          0 |
| Time           |          0 |              1 |   2 |   5 |     0 |        62 |          0 |
| Sport          |          0 |              1 |   4 |  21 |     0 |        64 |          0 |
| Venue          |          0 |              1 |  12 |  32 |     0 |        40 |          0 |
| Events         |          0 |              1 |   1 | 595 |     0 |       565 |          0 |

-   Glimpse data:

``` r
event_schedule_tbl %>%
  glimpse()
```

    ## Rows: 709
    ## Columns: 5
    ## $ Date   <chr> "21st_July_2021", "21st_July_2021", "21st_July_2021", "21st_Jul…
    ## $ Time   <chr> "1:00", "8:30", "9:00", "9:30", "1:00", "8:30", "9:00", "9:00",…
    ## $ Sport  <chr> "Baseball/Softball", "Football", "Football", "Football", "Baseb…
    ## $ Venue  <chr> "Fukushima Azuma Baseball Stadium", "Sapporo Dome", "Miyagi Sta…
    ## $ Events <chr> "Australia vs Japan, Italy vs United States, Mexico vs Canada",…

**venue details table**

-   Detailed summary of:

``` r
venue_details_tbl %>%
  skim()
```

|                                                  |            |
|:-------------------------------------------------|:-----------|
| Name                                             | Piped data |
| Number of rows                                   | 59         |
| Number of columns                                | 3          |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |            |
| Column type frequency:                           |            |
| character                                        | 3          |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |            |
| Group variables                                  | None       |

Data summary

**Variable type: character**

| skim\_variable | n\_missing | complete\_rate | min | max | empty | n\_unique | whitespace |
|:---------------|-----------:|---------------:|----:|----:|------:|----------:|-----------:|
| Venue          |          0 |              1 |  12 |  32 |     0 |        41 |          0 |
| Sport          |          0 |              1 |   4 |  21 |     0 |        48 |          0 |
| Location       |          0 |              1 |  36 |  38 |     0 |        41 |          0 |

-   Glimpse data:

``` r
venue_details_tbl %>%
  glimpse()
```

    ## Rows: 59
    ## Columns: 3
    ## $ Venue    <chr> "Olympic Stadium", "Olympic Stadium", "Olympic Stadium", "Oly…
    ## $ Sport    <chr> "Opening Ceremony", "Closing Ceremony", "Athletics", "Footbal…
    ## $ Location <chr> "35.67786383266573, 139.71366292613558", "35.67786383266573, …

## DATA WRANGLING

### 1. Create a correctly formatted DateTime field (on event\_schedule\_tbl)

``` r
event_schedule_clean_1_tbl <- event_schedule_tbl %>%
  # First cleaning the Time column and replacing xx -> 0:00
  mutate(Time = recode(Time, xx = '0:00')) %>%
  # Separate Date Field into different columns to do some cleanup on dateparts late on
  separate(Date, into = c("Day", "Month", "Year"), sep = "_") %>%
  # Remove letters from Day Field
  mutate(Day = str_extract(Day,"[[:digit:]]+")) %>%
  # Paste Date Time related columns into a single field
  mutate(Date = paste(Year, Month, Day, Time, sep ="-")) %>%
  # Convert to datetimeformat
  mutate(Date = ymd_hm(Date)) %>%
  # Remove individual Date columns
  select(-Day:-Time)

event_schedule_clean_1_tbl %>%
  head(10) %>%
  kable()
```

| Sport             | Venue                            | Events                                                                                                                                                                       | Date                |
|:------------------|:---------------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------------------|
| Baseball/Softball | Fukushima Azuma Baseball Stadium | Australia vs Japan, Italy vs United States, Mexico vs Canada                                                                                                                 | 2021-07-21 01:00:00 |
| Football          | Sapporo Dome                     | Women’s Group E: Great Britain vs Chile, Women’s Group E: Japan vs Canada                                                                                                    | 2021-07-21 08:30:00 |
| Football          | Miyagi Stadium                   | Women’s Group F: China vs Brazil, Women’s Group F: Zambia vs Netherlands                                                                                                     | 2021-07-21 09:00:00 |
| Football          | Tokyo Stadium                    | Women’s Group G: Sweden vs United States, Women’s Group G: Australia vs New Zealand                                                                                          | 2021-07-21 09:30:00 |
| Baseball/Softball | Fukushima Azuma Baseball Stadium | United States vs Canada, Mexico vs Japan, Italy vs Australia                                                                                                                 | 2021-07-22 01:00:00 |
| Football          | Sapporo Dome                     | Men’s Group C: Egypt vs Spain, Men’s Group C: Argentina vs Australia                                                                                                         | 2021-07-22 08:30:00 |
| Football          | Ibaraki Kashima Stadium          | Men’s Group B: New Zealand vs Republic of Korea, Men’s Group C: Honduras vs Romania                                                                                          | 2021-07-22 09:00:00 |
| Football          | Tokyo Stadium                    | Men’s Group A: Mexico vs France, Men’s Group A: Japan vs South Africa                                                                                                        | 2021-07-22 09:00:00 |
| Football          | Ibaraki Kashima Stadium          | Men’s Group D: Côte d’Ivoire vs Saudi Arabia, Men’s Group D: Brazil vs Germany                                                                                               | 2021-07-22 09:30:00 |
| Rowing            | Sea Forest Waterway              | Men’s Single Sculls Heats, Women’s Single Sculls Heats, Men’s Double Sculls Heats, Women’s Double Sculls Heats, Men’s Quadruple Sculls Heats, Women’s Quadruple Sculls Heats | 2021-07-23 00:30:00 |

### 2. Parse the event list so each event is on a separate row

``` r
event_schedule_clean_2_tbl <- event_schedule_clean_1_tbl %>%
  # Separate events into different rows
  separate_rows(Events, 
                sep = ',',
                convert = TRUE) %>%
  # Trim spaces on Events
  mutate(Events = str_trim(Events))

event_schedule_clean_2_tbl %>%
  head(10) %>%
  kable()
```

| Sport             | Venue                            | Events                                    | Date                |
|:------------------|:---------------------------------|:------------------------------------------|:--------------------|
| Baseball/Softball | Fukushima Azuma Baseball Stadium | Australia vs Japan                        | 2021-07-21 01:00:00 |
| Baseball/Softball | Fukushima Azuma Baseball Stadium | Italy vs United States                    | 2021-07-21 01:00:00 |
| Baseball/Softball | Fukushima Azuma Baseball Stadium | Mexico vs Canada                          | 2021-07-21 01:00:00 |
| Football          | Sapporo Dome                     | Women’s Group E: Great Britain vs Chile   | 2021-07-21 08:30:00 |
| Football          | Sapporo Dome                     | Women’s Group E: Japan vs Canada          | 2021-07-21 08:30:00 |
| Football          | Miyagi Stadium                   | Women’s Group F: China vs Brazil          | 2021-07-21 09:00:00 |
| Football          | Miyagi Stadium                   | Women’s Group F: Zambia vs Netherlands    | 2021-07-21 09:00:00 |
| Football          | Tokyo Stadium                    | Women’s Group G: Sweden vs United States  | 2021-07-21 09:30:00 |
| Football          | Tokyo Stadium                    | Women’s Group G: Australia vs New Zealand | 2021-07-21 09:30:00 |
| Baseball/Softball | Fukushima Azuma Baseball Stadium | United States vs Canada                   | 2021-07-22 01:00:00 |

### 3. Group similar sports into a Sport Type field

``` r
#Inspecting Sport column - 64 sports
event_schedule_clean_2_tbl %>%
  count(Sport) 
```

    ## # A tibble: 64 x 2
    ##    Sport                   n
    ##    <chr>               <int>
    ##  1 3x3 Basketball         36
    ##  2 Archery                39
    ##  3 Artistic Gymnastic      1
    ##  4 Artistic Gymnastics    26
    ##  5 Artistic Swimming       5
    ##  6 Athletics             146
    ##  7 Badminton              64
    ##  8 Baseball               16
    ##  9 Baseball/Softball       8
    ## 10 Basketball             54
    ## # … with 54 more rows

``` r
event_schedule_clean_3_tbl  <- event_schedule_clean_2_tbl %>%
  # Remove Punctuation (Excluding "/") >> Reduces Sports list from 64 to 60
  mutate(Sport = str_replace(Sport, "[\\p{P}\\p{S}&&[^/]]" , "")) %>% 
  # Standardize Sports to Title case >> Reduces Sports list from 60 to 54
  mutate(Sport = str_to_title(Sport)) %>%
  # Fixing misspellings >> Reduces Sports list from 54 to 48
  mutate(Sport_Group = recode(Sport,
                              'Artistic Gymnastic' = 'Artistic Gymnastics',
                              'Baseball' = 'Baseball/Softball',
                              'Softball' = 'Baseball/Softball',
                              'Softball/Baseball' = 'Baseball/Softball',
                              'Beach Volley' = 'Beach Volleyball',
                              'Beach Volleybal' = 'Beach Volleyball'
                              )) 

event_schedule_clean_3_tbl %>%
  head(10) %>%
  kable()
```

| Sport             | Venue                            | Events                                    | Date                | Sport\_Group      |
|:------------------|:---------------------------------|:------------------------------------------|:--------------------|:------------------|
| Baseball/Softball | Fukushima Azuma Baseball Stadium | Australia vs Japan                        | 2021-07-21 01:00:00 | Baseball/Softball |
| Baseball/Softball | Fukushima Azuma Baseball Stadium | Italy vs United States                    | 2021-07-21 01:00:00 | Baseball/Softball |
| Baseball/Softball | Fukushima Azuma Baseball Stadium | Mexico vs Canada                          | 2021-07-21 01:00:00 | Baseball/Softball |
| Football          | Sapporo Dome                     | Women’s Group E: Great Britain vs Chile   | 2021-07-21 08:30:00 | Football          |
| Football          | Sapporo Dome                     | Women’s Group E: Japan vs Canada          | 2021-07-21 08:30:00 | Football          |
| Football          | Miyagi Stadium                   | Women’s Group F: China vs Brazil          | 2021-07-21 09:00:00 | Football          |
| Football          | Miyagi Stadium                   | Women’s Group F: Zambia vs Netherlands    | 2021-07-21 09:00:00 | Football          |
| Football          | Tokyo Stadium                    | Women’s Group G: Sweden vs United States  | 2021-07-21 09:30:00 | Football          |
| Football          | Tokyo Stadium                    | Women’s Group G: Australia vs New Zealand | 2021-07-21 09:30:00 | Football          |
| Baseball/Softball | Fukushima Azuma Baseball Stadium | United States vs Canada                   | 2021-07-22 01:00:00 | Baseball/Softball |

### 4. Combine the Venue table

``` r
event_schedule_clean_4_tbl <- event_schedule_clean_3_tbl %>%
  #Joining by Sport and Venue (Both tables have the same field names)
  left_join(venue_details_tbl)
```

    ## Joining, by = c("Sport", "Venue")

``` r
event_schedule_clean_4_tbl %>%
  head(10) %>%
  kable()
```

| Sport             | Venue                            | Events                                    | Date                | Sport\_Group      | Location                              |
|:------------------|:---------------------------------|:------------------------------------------|:--------------------|:------------------|:--------------------------------------|
| Baseball/Softball | Fukushima Azuma Baseball Stadium | Australia vs Japan                        | 2021-07-21 01:00:00 | Baseball/Softball | 37.72216480340486, 140.3640114979229  |
| Baseball/Softball | Fukushima Azuma Baseball Stadium | Italy vs United States                    | 2021-07-21 01:00:00 | Baseball/Softball | 37.72216480340486, 140.3640114979229  |
| Baseball/Softball | Fukushima Azuma Baseball Stadium | Mexico vs Canada                          | 2021-07-21 01:00:00 | Baseball/Softball | 37.72216480340486, 140.3640114979229  |
| Football          | Sapporo Dome                     | Women’s Group E: Great Britain vs Chile   | 2021-07-21 08:30:00 | Football          | 43.01517544330762, 141.41041300340524 |
| Football          | Sapporo Dome                     | Women’s Group E: Japan vs Canada          | 2021-07-21 08:30:00 | Football          | 43.01517544330762, 141.41041300340524 |
| Football          | Miyagi Stadium                   | Women’s Group F: China vs Brazil          | 2021-07-21 09:00:00 | Football          | 38.33557331725407, 140.95096377309127 |
| Football          | Miyagi Stadium                   | Women’s Group F: Zambia vs Netherlands    | 2021-07-21 09:00:00 | Football          | 38.33557331725407, 140.95096377309127 |
| Football          | Tokyo Stadium                    | Women’s Group G: Sweden vs United States  | 2021-07-21 09:30:00 | Football          | 35.66446761779039, 139.52756286092847 |
| Football          | Tokyo Stadium                    | Women’s Group G: Australia vs New Zealand | 2021-07-21 09:30:00 | Football          | 35.66446761779039, 139.52756286092847 |
| Baseball/Softball | Fukushima Azuma Baseball Stadium | United States vs Canada                   | 2021-07-22 01:00:00 | Baseball/Softball | 37.72216480340486, 140.3640114979229  |

### 5. Calculate whether the event is a ‘Victory Ceremony’ or ‘Gold Medal’ event. (Note, this might not pick up all of the medal events.)

``` r
event_type <- 'Victory Ceremony|Gold Medal' 

event_schedule_clean_5_tbl <- event_schedule_clean_4_tbl %>%
  mutate(Medal_Ceremony = str_detect(event_schedule_clean_4_tbl$Events, event_type)) 

event_schedule_clean_5_tbl %>%
  head(10) %>%
  kable()
```

| Sport             | Venue                            | Events                                    | Date                | Sport\_Group      | Location                              | Medal\_Ceremony |
|:------------------|:---------------------------------|:------------------------------------------|:--------------------|:------------------|:--------------------------------------|:----------------|
| Baseball/Softball | Fukushima Azuma Baseball Stadium | Australia vs Japan                        | 2021-07-21 01:00:00 | Baseball/Softball | 37.72216480340486, 140.3640114979229  | FALSE           |
| Baseball/Softball | Fukushima Azuma Baseball Stadium | Italy vs United States                    | 2021-07-21 01:00:00 | Baseball/Softball | 37.72216480340486, 140.3640114979229  | FALSE           |
| Baseball/Softball | Fukushima Azuma Baseball Stadium | Mexico vs Canada                          | 2021-07-21 01:00:00 | Baseball/Softball | 37.72216480340486, 140.3640114979229  | FALSE           |
| Football          | Sapporo Dome                     | Women’s Group E: Great Britain vs Chile   | 2021-07-21 08:30:00 | Football          | 43.01517544330762, 141.41041300340524 | FALSE           |
| Football          | Sapporo Dome                     | Women’s Group E: Japan vs Canada          | 2021-07-21 08:30:00 | Football          | 43.01517544330762, 141.41041300340524 | FALSE           |
| Football          | Miyagi Stadium                   | Women’s Group F: China vs Brazil          | 2021-07-21 09:00:00 | Football          | 38.33557331725407, 140.95096377309127 | FALSE           |
| Football          | Miyagi Stadium                   | Women’s Group F: Zambia vs Netherlands    | 2021-07-21 09:00:00 | Football          | 38.33557331725407, 140.95096377309127 | FALSE           |
| Football          | Tokyo Stadium                    | Women’s Group G: Sweden vs United States  | 2021-07-21 09:30:00 | Football          | 35.66446761779039, 139.52756286092847 | FALSE           |
| Football          | Tokyo Stadium                    | Women’s Group G: Australia vs New Zealand | 2021-07-21 09:30:00 | Football          | 35.66446761779039, 139.52756286092847 | FALSE           |
| Baseball/Softball | Fukushima Azuma Baseball Stadium | United States vs Canada                   | 2021-07-22 01:00:00 | Baseball/Softball | 37.72216480340486, 140.3640114979229  | FALSE           |

### Extra: Split Location into Latitude, Longitude

``` r
event_schedule_clean_6_tbl <- event_schedule_clean_5_tbl %>%
  separate(Location, into = c('Latitude', 'Longitude'), sep = ',') %>%
  mutate(Longitude = str_trim(Longitude)) %>%
  select(Date, Sport_Group, Sport, Events, Medal_Ceremony,Venue, Latitude, Longitude)

event_schedule_clean_6_tbl %>%
  head(10) %>%
  kable()
```

| Date                | Sport\_Group      | Sport             | Events                                    | Medal\_Ceremony | Venue                            | Latitude          | Longitude          |
|:--------------------|:------------------|:------------------|:------------------------------------------|:----------------|:---------------------------------|:------------------|:-------------------|
| 2021-07-21 01:00:00 | Baseball/Softball | Baseball/Softball | Australia vs Japan                        | FALSE           | Fukushima Azuma Baseball Stadium | 37.72216480340486 | 140.3640114979229  |
| 2021-07-21 01:00:00 | Baseball/Softball | Baseball/Softball | Italy vs United States                    | FALSE           | Fukushima Azuma Baseball Stadium | 37.72216480340486 | 140.3640114979229  |
| 2021-07-21 01:00:00 | Baseball/Softball | Baseball/Softball | Mexico vs Canada                          | FALSE           | Fukushima Azuma Baseball Stadium | 37.72216480340486 | 140.3640114979229  |
| 2021-07-21 08:30:00 | Football          | Football          | Women’s Group E: Great Britain vs Chile   | FALSE           | Sapporo Dome                     | 43.01517544330762 | 141.41041300340524 |
| 2021-07-21 08:30:00 | Football          | Football          | Women’s Group E: Japan vs Canada          | FALSE           | Sapporo Dome                     | 43.01517544330762 | 141.41041300340524 |
| 2021-07-21 09:00:00 | Football          | Football          | Women’s Group F: China vs Brazil          | FALSE           | Miyagi Stadium                   | 38.33557331725407 | 140.95096377309127 |
| 2021-07-21 09:00:00 | Football          | Football          | Women’s Group F: Zambia vs Netherlands    | FALSE           | Miyagi Stadium                   | 38.33557331725407 | 140.95096377309127 |
| 2021-07-21 09:30:00 | Football          | Football          | Women’s Group G: Sweden vs United States  | FALSE           | Tokyo Stadium                    | 35.66446761779039 | 139.52756286092847 |
| 2021-07-21 09:30:00 | Football          | Football          | Women’s Group G: Australia vs New Zealand | FALSE           | Tokyo Stadium                    | 35.66446761779039 | 139.52756286092847 |
| 2021-07-22 01:00:00 | Baseball/Softball | Baseball/Softball | United States vs Canada                   | FALSE           | Fukushima Azuma Baseball Stadium | 37.72216480340486 | 140.3640114979229  |

### Final Step - Output the Data - Clean Data

``` r
event_schedule_clean_6_tbl %>%
  write_csv('Output_Data_Olympics_2020.csv')
```
