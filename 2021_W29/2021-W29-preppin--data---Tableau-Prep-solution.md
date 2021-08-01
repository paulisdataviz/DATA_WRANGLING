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


## TABLEAU PREP WORKFLOW

Data resides in a .xlsx file, and it has two sheets:

\* Event Schedule 

\* Venue Details

I created Tableau Prep workflow separating it into the main steps:

![Tableau Prep workflow](https://github.com/paulisdataviz/DATA_WRANGLING/blob/main/2021_W29/1_Workflow.png)



## RELEVANT CALCULATIONS

### 1. Create a correctly formatted DateTime field (on event\_schedule\_tbl)

1.1 Group hour xx to 0:00

1.2 Split Date field by '_' and perform cleaning steps

1.3 Convert Time to Datetime and extract time

1.4 Use Makedatetime fuction to create Datetime field

``` r
#DATE Field
DATEPARSE('yyyy.MMMM.dd', ([Year]+'.'+[Month]+'.'+[Day]))
```

``` r
#DATETIME field
MAKEDATETIME([Date],[Time-1])
```

### 2. Parse the event list so each event is on a separate row

Used built in function to split strings using ',' as delimiter

### 3. Group similar sports into a Sport Type field

Used built in function to group strings by Spelling (Went from 58 to 48 Sports)

### 4. Combine the Venue table

Created a **left join** between 'event schedule' and 'venue details' table 

Left join was done on **venue** and **sport** field

![Tableau Prep left join](https://github.com/paulisdataviz/DATA_WRANGLING/blob/main/2021_W29/3_join.png)


### 5. Calculate whether the event is a ‘Victory Ceremony’ or ‘Gold Medal’ event. (Note, this might not pick up all of the medal events.)

``` r
#Medal ceremony Field
CONTAINS([Events], 'Victory Ceremony'  ) or 
CONTAINS([Events], 'Gold Medal'  )
```

### Extra: Split Location into Latitude, Longitude

Used built in function to split strings using ',' as delimiter

### Final Step - Output the Data - Clean Data


![Tableau Prep output](https://github.com/paulisdataviz/DATA_WRANGLING/blob/main/2021_W29/2_output.png)

[Download Tableau Prep Workflow](https://github.com/paulisdataviz/DATA_WRANGLING/blob/main/2021_W29/2021W29.tflx)


