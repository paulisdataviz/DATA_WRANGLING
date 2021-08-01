2021 W29 preppin’ data Challenge - Tableau Prep Solution
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


## IMPORTING DATA

Data resides in a .xlsx file, and it has two sheets:

\* Event Schedule \* Venue Details

![Tableau Prep workflow](https://github.com/paulisdataviz/DATA_WRANGLING/blob/main/2021_W29/1_Workflow.png)

## INSPECTING DATA

### event scheduled table

### venue details table

## DATA WRANGLING

### 1. Create a correctly formatted DateTime field (on event\_schedule\_tbl)

``` r
code
```

### 2. Parse the event list so each event is on a separate row

``` r
code
```

### 3. Group similar sports into a Sport Type field

``` r
code
```

### 4. Combine the Venue table

``` r
code
```

### 5. Calculate whether the event is a ‘Victory Ceremony’ or ‘Gold Medal’ event. (Note, this might not pick up all of the medal events.)

``` r
code
```

### Extra: Split Location into Latitude, Longitude

``` r
code
```

### Final Step - Output the Data - Clean Data

``` r
code
```
