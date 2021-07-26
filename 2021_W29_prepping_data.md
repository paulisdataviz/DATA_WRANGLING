2021\_W29\_prepping\_data
================
Paula Munoz
7/25/2021

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
-   Create a correctly formatted DateTime field (hint)
-   Parse the event list so each event is on a separate row (hint)
-   Group similar sports into a Sport Type field (hint)
-   Combine the Venue table (hint)
-   Calculate whether the event is a ‘Victory Ceremony’ or ‘Gold Medal’
    event. (Note, this might not pick up all of the medal events.)
-   Output the Data

## LOADING LIBRARIES

``` r
library(tidyverse)
library(skimr)
library(lubridate)
```

## IMPORTING DATA

Data resides in a .xlsx file, and it has two sheets: \* Event Schedule
\* Venue Details

``` r
event_schedule_tbl <- readxl::read_excel(path = '2021_W29_prepping_data_files/Olympic Events.xlsx', 
                                         sheet = 1)

venue_details_tbl <- readxl::read_excel(path = '2021_W29_prepping_data_files/Olympic Events.xlsx', 
                                        sheet = 2)
```

## INSPECTING DATA

**event scheduled table**

-   Inspecting first five rows:

``` r
event_schedule_tbl %>%
  head()
```

    ## # A tibble: 6 x 5
    ##   Date      Time  Sport      Venue            Events                            
    ##   <chr>     <chr> <chr>      <chr>            <chr>                             
    ## 1 21st_Jul… 1:00  Baseball/… Fukushima Azuma… Australia vs Japan, Italy vs Unit…
    ## 2 21st_Jul… 8:30  Football   Sapporo Dome     Women's Group E: Great Britain vs…
    ## 3 21st_Jul… 9:00  Football   Miyagi Stadium   Women's Group F: China vs Brazil,…
    ## 4 21st_Jul… 9:30  Football   Tokyo Stadium    Women's Group G: Sweden vs United…
    ## 5 22nd_Jul… 1:00  Baseball/… Fukushima Azuma… United States vs Canada, Mexico v…
    ## 6 22nd_Jul… 8:30  Football   Sapporo Dome     Men's Group C: Egypt vs Spain, Me…

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

event_schedule_clean_1_tbl
```

    ## # A tibble: 709 x 4
    ##    Sport      Venue          Events                          Date               
    ##    <chr>      <chr>          <chr>                           <dttm>             
    ##  1 Baseball/… Fukushima Azu… Australia vs Japan, Italy vs U… 2021-07-21 01:00:00
    ##  2 Football   Sapporo Dome   Women's Group E: Great Britain… 2021-07-21 08:30:00
    ##  3 Football   Miyagi Stadium Women's Group F: China vs Braz… 2021-07-21 09:00:00
    ##  4 Football   Tokyo Stadium  Women's Group G: Sweden vs Uni… 2021-07-21 09:30:00
    ##  5 Baseball/… Fukushima Azu… United States vs Canada, Mexic… 2021-07-22 01:00:00
    ##  6 Football   Sapporo Dome   Men's Group C: Egypt vs Spain,… 2021-07-22 08:30:00
    ##  7 Football   Ibaraki Kashi… Men's Group B: New Zealand vs … 2021-07-22 09:00:00
    ##  8 Football   Tokyo Stadium  Men's Group A: Mexico vs Franc… 2021-07-22 09:00:00
    ##  9 Football   Ibaraki Kashi… Men's Group D: Côte d'Ivoire v… 2021-07-22 09:30:00
    ## 10 Rowing     Sea Forest Wa… Men's Single Sculls Heats, Wom… 2021-07-23 00:30:00
    ## # … with 699 more rows

### 2. Parse the event list so each event is on a separate row

``` r
event_schedule_clean_2_tbl <- event_schedule_clean_1_tbl %>%
  # Separate events into different rows
  separate_rows(Events, 
                sep = ',',
                convert = TRUE) %>%
  # Trim spaces on Events
  mutate(Events = str_trim(Events))

event_schedule_clean_2_tbl
```

    ## # A tibble: 1,895 x 4
    ##    Sport        Venue               Events                   Date               
    ##    <chr>        <chr>               <chr>                    <dttm>             
    ##  1 Baseball/So… Fukushima Azuma Ba… Australia vs Japan       2021-07-21 01:00:00
    ##  2 Baseball/So… Fukushima Azuma Ba… Italy vs United States   2021-07-21 01:00:00
    ##  3 Baseball/So… Fukushima Azuma Ba… Mexico vs Canada         2021-07-21 01:00:00
    ##  4 Football     Sapporo Dome        Women's Group E: Great … 2021-07-21 08:30:00
    ##  5 Football     Sapporo Dome        Women's Group E: Japan … 2021-07-21 08:30:00
    ##  6 Football     Miyagi Stadium      Women's Group F: China … 2021-07-21 09:00:00
    ##  7 Football     Miyagi Stadium      Women's Group F: Zambia… 2021-07-21 09:00:00
    ##  8 Football     Tokyo Stadium       Women's Group G: Sweden… 2021-07-21 09:30:00
    ##  9 Football     Tokyo Stadium       Women's Group G: Austra… 2021-07-21 09:30:00
    ## 10 Baseball/So… Fukushima Azuma Ba… United States vs Canada  2021-07-22 01:00:00
    ## # … with 1,885 more rows

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

event_schedule_clean_3_tbl
```

    ## # A tibble: 1,895 x 5
    ##    Sport      Venue          Events            Date                Sport_Group  
    ##    <chr>      <chr>          <chr>             <dttm>              <chr>        
    ##  1 Baseball/… Fukushima Azu… Australia vs Jap… 2021-07-21 01:00:00 Baseball/Sof…
    ##  2 Baseball/… Fukushima Azu… Italy vs United … 2021-07-21 01:00:00 Baseball/Sof…
    ##  3 Baseball/… Fukushima Azu… Mexico vs Canada  2021-07-21 01:00:00 Baseball/Sof…
    ##  4 Football   Sapporo Dome   Women's Group E:… 2021-07-21 08:30:00 Football     
    ##  5 Football   Sapporo Dome   Women's Group E:… 2021-07-21 08:30:00 Football     
    ##  6 Football   Miyagi Stadium Women's Group F:… 2021-07-21 09:00:00 Football     
    ##  7 Football   Miyagi Stadium Women's Group F:… 2021-07-21 09:00:00 Football     
    ##  8 Football   Tokyo Stadium  Women's Group G:… 2021-07-21 09:30:00 Football     
    ##  9 Football   Tokyo Stadium  Women's Group G:… 2021-07-21 09:30:00 Football     
    ## 10 Baseball/… Fukushima Azu… United States vs… 2021-07-22 01:00:00 Baseball/Sof…
    ## # … with 1,885 more rows

### 4. Combine the Venue table

``` r
event_schedule_clean_4_tbl <- event_schedule_clean_3_tbl %>%
  #Joining by Sport and Venue (Both tables have the same field names)
  left_join(venue_details_tbl)
```

    ## Joining, by = c("Sport", "Venue")

``` r
event_schedule_clean_4_tbl
```

    ## # A tibble: 1,895 x 6
    ##    Sport   Venue      Events       Date                Sport_Group  Location    
    ##    <chr>   <chr>      <chr>        <dttm>              <chr>        <chr>       
    ##  1 Baseba… Fukushima… Australia v… 2021-07-21 01:00:00 Baseball/So… 37.72216480…
    ##  2 Baseba… Fukushima… Italy vs Un… 2021-07-21 01:00:00 Baseball/So… 37.72216480…
    ##  3 Baseba… Fukushima… Mexico vs C… 2021-07-21 01:00:00 Baseball/So… 37.72216480…
    ##  4 Footba… Sapporo D… Women's Gro… 2021-07-21 08:30:00 Football     43.01517544…
    ##  5 Footba… Sapporo D… Women's Gro… 2021-07-21 08:30:00 Football     43.01517544…
    ##  6 Footba… Miyagi St… Women's Gro… 2021-07-21 09:00:00 Football     38.33557331…
    ##  7 Footba… Miyagi St… Women's Gro… 2021-07-21 09:00:00 Football     38.33557331…
    ##  8 Footba… Tokyo Sta… Women's Gro… 2021-07-21 09:30:00 Football     35.66446761…
    ##  9 Footba… Tokyo Sta… Women's Gro… 2021-07-21 09:30:00 Football     35.66446761…
    ## 10 Baseba… Fukushima… United Stat… 2021-07-22 01:00:00 Baseball/So… 37.72216480…
    ## # … with 1,885 more rows

### 5. Calculate whether the event is a ‘Victory Ceremony’ or ‘Gold Medal’ event. (Note, this might not pick up all of the medal events.)

``` r
event_type <- 'Victory Ceremony|Gold Medal' 

event_schedule_clean_5_tbl <- event_schedule_clean_4_tbl %>%
  mutate(Medal_Ceremony = str_detect(event_schedule_clean_4_tbl$Events, event_type)) 

event_schedule_clean_5_tbl
```

    ## # A tibble: 1,895 x 7
    ##    Sport  Venue  Events  Date                Sport_Group Location Medal_Ceremony
    ##    <chr>  <chr>  <chr>   <dttm>              <chr>       <chr>    <lgl>         
    ##  1 Baseb… Fukus… Austra… 2021-07-21 01:00:00 Baseball/S… 37.7221… FALSE         
    ##  2 Baseb… Fukus… Italy … 2021-07-21 01:00:00 Baseball/S… 37.7221… FALSE         
    ##  3 Baseb… Fukus… Mexico… 2021-07-21 01:00:00 Baseball/S… 37.7221… FALSE         
    ##  4 Footb… Sappo… Women'… 2021-07-21 08:30:00 Football    43.0151… FALSE         
    ##  5 Footb… Sappo… Women'… 2021-07-21 08:30:00 Football    43.0151… FALSE         
    ##  6 Footb… Miyag… Women'… 2021-07-21 09:00:00 Football    38.3355… FALSE         
    ##  7 Footb… Miyag… Women'… 2021-07-21 09:00:00 Football    38.3355… FALSE         
    ##  8 Footb… Tokyo… Women'… 2021-07-21 09:30:00 Football    35.6644… FALSE         
    ##  9 Footb… Tokyo… Women'… 2021-07-21 09:30:00 Football    35.6644… FALSE         
    ## 10 Baseb… Fukus… United… 2021-07-22 01:00:00 Baseball/S… 37.7221… FALSE         
    ## # … with 1,885 more rows

### Extra: Split Location into Latitude, Longitude

``` r
event_schedule_clean_6_tbl <- event_schedule_clean_5_tbl %>%
  separate(Location, into = c('Latitude', 'Longitude'), sep = ',') %>%
  mutate(Longitude = str_trim(Longitude)) %>%
  select(Date, Sport_Group, Sport, Events, Medal_Ceremony,Venue, Latitude, Longitude)

event_schedule_clean_6_tbl
```

    ## # A tibble: 1,895 x 8
    ##    Date                Sport_Group  Sport  Events  Medal_Ceremony Venue Latitude
    ##    <dttm>              <chr>        <chr>  <chr>   <lgl>          <chr> <chr>   
    ##  1 2021-07-21 01:00:00 Baseball/So… Baseb… Austra… FALSE          Fuku… 37.7221…
    ##  2 2021-07-21 01:00:00 Baseball/So… Baseb… Italy … FALSE          Fuku… 37.7221…
    ##  3 2021-07-21 01:00:00 Baseball/So… Baseb… Mexico… FALSE          Fuku… 37.7221…
    ##  4 2021-07-21 08:30:00 Football     Footb… Women'… FALSE          Sapp… 43.0151…
    ##  5 2021-07-21 08:30:00 Football     Footb… Women'… FALSE          Sapp… 43.0151…
    ##  6 2021-07-21 09:00:00 Football     Footb… Women'… FALSE          Miya… 38.3355…
    ##  7 2021-07-21 09:00:00 Football     Footb… Women'… FALSE          Miya… 38.3355…
    ##  8 2021-07-21 09:30:00 Football     Footb… Women'… FALSE          Toky… 35.6644…
    ##  9 2021-07-21 09:30:00 Football     Footb… Women'… FALSE          Toky… 35.6644…
    ## 10 2021-07-22 01:00:00 Baseball/So… Baseb… United… FALSE          Fuku… 37.7221…
    ## # … with 1,885 more rows, and 1 more variable: Longitude <chr>

### Final Step - Output the Data - Clean Data

``` r
event_schedule_clean_6_tbl %>%
  write_csv('Output_Data_Olympics_2020.csv')
```
