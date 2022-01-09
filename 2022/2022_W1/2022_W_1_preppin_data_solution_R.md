2022 W1 Preppin’ Data Challenge - R Solution
================
Paula Munoz
2022/01/08

## 2022: Week 1 - The Prep School - Parental Contact Details

Challenge by Carl Allchin.

Challenge Link:
<https://preppindata.blogspot.com/2022/01/2022-week-1-prep-school-parental.html>

Data Source:
<https://drive.google.com/file/d/1p8gt3cR3ATCeGK81pnT90x0a6dbCXst1/view>

### DETAILS

#### Requirements

-   Input the csv file (link above)
-   Form the pupil’s name correctly for the records in the format Last
    Name, First Name
-   Form the parental contact’s name in the same format as the pupil’s:
-   The Parental Contact Name 1 and 2 are the first names of each
    parent.
-   Use parental contact column to select which parent first name to use
    along with the pupil’s last name
-   Create the email address to contact the parent using the format
    Parent First Name.Parent Last <Name@Employer.com>
-   Create the academic year the pupils are in:
-   Each academic year starts on 1st September.
-   Year 1 is anyone born after 1st Sept 2014
-   Year 2 is anyone born between 1st Sept 2013 and 31st Aug 2014 etc
-   Remove any unnecessary columns of data
-   Output the data

## LOADING LIBRARIES

``` r
library(tidyverse)
library(skimr)
library(knitr)
library(tidyquant)
library(rvest)
library(ggthemes)
```

## IMPORTING DATA

Importing data

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

### 1. Form the pupil’s name (Last Name, First Name)

``` r
tidy_data <- input %>%
  mutate(Pupils_Name = str_c(`pupil last name`, ', ',`pupil first name`))

tidy_data%>%
  head(10) %>%
  kable()      
```

|  id | pupil first name | pupil last name | gender | Date of Birth | Parental Contact Name\_1 | Parental Contact Name\_2 | Preferred Contact Employer | Parental Contact | Pupils\_Name       |
|----:|:-----------------|:----------------|:-------|:--------------|:-------------------------|:-------------------------|:---------------------------|-----------------:|:-------------------|
|   1 | Ronna            | Nellies         | Female | 12/21/2013    | Purcell                  | Ketti                    | Demizz                     |                1 | Nellies, Ronna     |
|   2 | Rusty            | Andriulis       | Male   | 7/21/2012     | Vassili                  | Rivi                     | Brainbox                   |                1 | Andriulis, Rusty   |
|   3 | Roberta          | Oakeshott       | Female | 12/4/2011     | Lind                     | Haskell                  | Centidel                   |                2 | Oakeshott, Roberta |
|   4 | Lola             | Rubinfajn       | Male   | 6/29/2012     | Elie                     | Tresa                    | Edgeblab                   |                2 | Rubinfajn, Lola    |
|   5 | Kamila           | Benedtti        | Female | 7/10/2012     | Adela                    | Clevey                   | Trudoo                     |                1 | Benedtti, Kamila   |
|   6 | Avery            | Colebourn       | Female | 8/30/2012     | Dalenna                  | Charley                  | Linktype                   |                1 | Colebourn, Avery   |
|   7 | Valentino        | Klimko          | Female | 12/23/2014    | Arlette                  | Onofredo                 | Thoughtblab                |                2 | Klimko, Valentino  |
|   8 | Cal              | Shearwood       | Male   | 1/18/2015     | Leontine                 | Berne                    | Browseblab                 |                2 | Shearwood, Cal     |
|   9 | King             | Truswell        | Female | 9/14/2012     | Evvy                     | Othelia                  | Photospace                 |                1 | Truswell, King     |
|  10 | Towney           | Stichall        | Male   | 6/4/2015      | Wendie                   | Joyann                   | Kwimbee                    |                2 | Stichall, Towney   |

### 2. Form the parental contact’s name in the same format as the pupil’s

``` r
tidy_data <- tidy_data %>%
  mutate(Parental_Contact_Full_Name = case_when(
    `Parental Contact` == 1 ~ str_c(`pupil last name`, ', ',`Parental Contact Name_1`),
    `Parental Contact` == 2 ~ str_c(`pupil last name`, ', ',`Parental Contact Name_2`)
    )
  )

tidy_data%>%
  head(10) %>%
  kable()   
```

|  id | pupil first name | pupil last name | gender | Date of Birth | Parental Contact Name\_1 | Parental Contact Name\_2 | Preferred Contact Employer | Parental Contact | Pupils\_Name       | Parental\_Contact\_Full\_Name |
|----:|:-----------------|:----------------|:-------|:--------------|:-------------------------|:-------------------------|:---------------------------|-----------------:|:-------------------|:------------------------------|
|   1 | Ronna            | Nellies         | Female | 12/21/2013    | Purcell                  | Ketti                    | Demizz                     |                1 | Nellies, Ronna     | Nellies, Purcell              |
|   2 | Rusty            | Andriulis       | Male   | 7/21/2012     | Vassili                  | Rivi                     | Brainbox                   |                1 | Andriulis, Rusty   | Andriulis, Vassili            |
|   3 | Roberta          | Oakeshott       | Female | 12/4/2011     | Lind                     | Haskell                  | Centidel                   |                2 | Oakeshott, Roberta | Oakeshott, Haskell            |
|   4 | Lola             | Rubinfajn       | Male   | 6/29/2012     | Elie                     | Tresa                    | Edgeblab                   |                2 | Rubinfajn, Lola    | Rubinfajn, Tresa              |
|   5 | Kamila           | Benedtti        | Female | 7/10/2012     | Adela                    | Clevey                   | Trudoo                     |                1 | Benedtti, Kamila   | Benedtti, Adela               |
|   6 | Avery            | Colebourn       | Female | 8/30/2012     | Dalenna                  | Charley                  | Linktype                   |                1 | Colebourn, Avery   | Colebourn, Dalenna            |
|   7 | Valentino        | Klimko          | Female | 12/23/2014    | Arlette                  | Onofredo                 | Thoughtblab                |                2 | Klimko, Valentino  | Klimko, Onofredo              |
|   8 | Cal              | Shearwood       | Male   | 1/18/2015     | Leontine                 | Berne                    | Browseblab                 |                2 | Shearwood, Cal     | Shearwood, Berne              |
|   9 | King             | Truswell        | Female | 9/14/2012     | Evvy                     | Othelia                  | Photospace                 |                1 | Truswell, King     | Truswell, Evvy                |
|  10 | Towney           | Stichall        | Male   | 6/4/2015      | Wendie                   | Joyann                   | Kwimbee                    |                2 | Stichall, Towney   | Stichall, Joyann              |

### 3. Create the email address to contact the parent using the format Parent First Name.Parent Last <Name@Employer.com>

``` r
tidy_data  <- tidy_data %>%
  mutate(Parental_Contact_Email_Address = 
           str_c(
            (str_split(Parental_Contact_Full_Name,',', simplify = T)[,2]),
            '@',
           `Preferred Contact Employer`,
           '.com'
           )
         )

tidy_data%>%
  head(10) %>%
  kable() 
```

|  id | pupil first name | pupil last name | gender | Date of Birth | Parental Contact Name\_1 | Parental Contact Name\_2 | Preferred Contact Employer | Parental Contact | Pupils\_Name       | Parental\_Contact\_Full\_Name | Parental\_Contact\_Email\_Address |
|----:|:-----------------|:----------------|:-------|:--------------|:-------------------------|:-------------------------|:---------------------------|-----------------:|:-------------------|:------------------------------|:----------------------------------|
|   1 | Ronna            | Nellies         | Female | 12/21/2013    | Purcell                  | Ketti                    | Demizz                     |                1 | Nellies, Ronna     | Nellies, Purcell              | <Purcell@Demizz.com>              |
|   2 | Rusty            | Andriulis       | Male   | 7/21/2012     | Vassili                  | Rivi                     | Brainbox                   |                1 | Andriulis, Rusty   | Andriulis, Vassili            | <Vassili@Brainbox.com>            |
|   3 | Roberta          | Oakeshott       | Female | 12/4/2011     | Lind                     | Haskell                  | Centidel                   |                2 | Oakeshott, Roberta | Oakeshott, Haskell            | <Haskell@Centidel.com>            |
|   4 | Lola             | Rubinfajn       | Male   | 6/29/2012     | Elie                     | Tresa                    | Edgeblab                   |                2 | Rubinfajn, Lola    | Rubinfajn, Tresa              | <Tresa@Edgeblab.com>              |
|   5 | Kamila           | Benedtti        | Female | 7/10/2012     | Adela                    | Clevey                   | Trudoo                     |                1 | Benedtti, Kamila   | Benedtti, Adela               | <Adela@Trudoo.com>                |
|   6 | Avery            | Colebourn       | Female | 8/30/2012     | Dalenna                  | Charley                  | Linktype                   |                1 | Colebourn, Avery   | Colebourn, Dalenna            | <Dalenna@Linktype.com>            |
|   7 | Valentino        | Klimko          | Female | 12/23/2014    | Arlette                  | Onofredo                 | Thoughtblab                |                2 | Klimko, Valentino  | Klimko, Onofredo              | <Onofredo@Thoughtblab.com>        |
|   8 | Cal              | Shearwood       | Male   | 1/18/2015     | Leontine                 | Berne                    | Browseblab                 |                2 | Shearwood, Cal     | Shearwood, Berne              | <Berne@Browseblab.com>            |
|   9 | King             | Truswell        | Female | 9/14/2012     | Evvy                     | Othelia                  | Photospace                 |                1 | Truswell, King     | Truswell, Evvy                | <Evvy@Photospace.com>             |
|  10 | Towney           | Stichall        | Male   | 6/4/2015      | Wendie                   | Joyann                   | Kwimbee                    |                2 | Stichall, Towney   | Stichall, Joyann              | <Joyann@Kwimbee.com>              |

### 4. Create the academic year the pupils are in

``` r
tidy_data <- tidy_data %>%
  # Convert Date of Birth to Date Format
  mutate(date = (mdy(tidy_data$`Date of Birth`))) %>%
  # Extract year date
  mutate(year_date = year(date)) %>%
  # Extract month date
  mutate(month_date = month(date)) %>%
  # Adjustment: If month is <= 8 (August) then substract 1 to year (in next step), else substract 0
  mutate(adjustment = ifelse(month_date <= 8, -1, 0 )) %>%
  mutate(year_adjusted = year_date + adjustment) %>%
  mutate(academic_year = case_when( year_adjusted >= 2014 ~ 1,
                     year_adjusted >= 2013 ~ 2,
                     year_adjusted >= 2012 ~ 3,
                     TRUE ~ 4)) %>%
  select(academic_year, Pupils_Name, Parental_Contact_Full_Name, Parental_Contact_Email_Address)

tidy_data%>%
  head(10) %>%
  kable() 
```

| academic\_year | Pupils\_Name       | Parental\_Contact\_Full\_Name | Parental\_Contact\_Email\_Address |
|---------------:|:-------------------|:------------------------------|:----------------------------------|
|              2 | Nellies, Ronna     | Nellies, Purcell              | <Purcell@Demizz.com>              |
|              4 | Andriulis, Rusty   | Andriulis, Vassili            | <Vassili@Brainbox.com>            |
|              4 | Oakeshott, Roberta | Oakeshott, Haskell            | <Haskell@Centidel.com>            |
|              4 | Rubinfajn, Lola    | Rubinfajn, Tresa              | <Tresa@Edgeblab.com>              |
|              4 | Benedtti, Kamila   | Benedtti, Adela               | <Adela@Trudoo.com>                |
|              4 | Colebourn, Avery   | Colebourn, Dalenna            | <Dalenna@Linktype.com>            |
|              1 | Klimko, Valentino  | Klimko, Onofredo              | <Onofredo@Thoughtblab.com>        |
|              1 | Shearwood, Cal     | Shearwood, Berne              | <Berne@Browseblab.com>            |
|              3 | Truswell, King     | Truswell, Evvy                | <Evvy@Photospace.com>             |
|              1 | Stichall, Towney   | Stichall, Joyann              | <Joyann@Kwimbee.com>              |
