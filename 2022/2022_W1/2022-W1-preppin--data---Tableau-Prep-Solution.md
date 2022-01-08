2022 W1 preppin’ data Challenge - Tableau Prep Solution
================
Paula Munoz
2022/01/07

## 2022: Week 1 - The Prep School - Parental Contact Details

Challenge by Carl Allchin.

[Challenge Link](https://preppindata.blogspot.com/2022/01/2022-week-1-prep-school-parental.html)<br>

### CHALLENGE DETAILS

### Inputs

[One .csv file](https://drive.google.com/file/d/1p8gt3cR3ATCeGK81pnT90x0a6dbCXst1/view)<br>


### Requirements

-   Input the csv file (link above)
-   Form the pupil's name correctly for the records in the format _Last Name, First Name_
-   Form the parental contact's name in the same format as the pupil's:
-   The Parental Contact Name 1 and 2 are the first names of each parent.
-   Use parental contact column to select which parent first name to use along with the pupil's last name
-   Create the email address to contact the parent using the format _Parent First Name.Parent Last Name@Employer.com_
-   Create the academic year the pupils are in:
-   Each academic year starts on 1st September.
-   Year 1 is anyone born after 1st Sept 2014 
-   Year 2 is anyone born between 1st Sept 2013 and 31st Aug 2014 etc
-   Remove any unnecessary columns of data
-   Output the data


## TABLEAU PREP WORKFLOW

Data resides in a .csv file, and it has one sheet.

I created below Tableau Prep workflow separating it into the main steps:

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


