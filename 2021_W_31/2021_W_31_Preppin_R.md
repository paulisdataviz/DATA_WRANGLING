2021 W 31 preppin’ data Challenge - R Solution
================
Paula Munoz
08/07/2021

## 2021: Week 31 - Excelling in R

Challenge by Carl Allchin

Challenge Link:
<https://preppindata.blogspot.com/2021/08/2021-week-36-excelling-in-prep.html>.

### CHALLENGE DETAILS

When you are working with data in most organisations, you will
frequently come across requests from Excel users using Excel terms. This
week’s challenge looks at term that is ubiquitous with Excel - a pivot
table.

Pivot tables are often contain summarised data values, have totals and
filter out certain parts of the data set. The challenge this week will
be to take an input and create a pivot table. Pivot tables are likely to
be structured differently to most of our analytical outputs.

### Inputs

The weekly sales of Bike Components from Preppin’s bike store Allchains
is what we are analysing. The returns are where the product has been
deemed faulty before it’s sold.

### Requirements

-   Input data
-   Remove the ‘Return to Manufacturer’ records
-   Create a total for each Store of all the items sold (help)
-   Aggregate the data to Store sales by Item
-   Output the data

### Output

6 columns:

-   Items sold per store
-   Wheels
-   Tyres
-   Saddles
-   Brakes
-   Store
-   4 rows of data (5 rows including header)

## LOADING LIBRARIES

``` r
library(tidyverse)
library(skimr)
library(knitr)
library(tidyquant)
```

## IMPORTING DATA

Data resides in a .csv file

``` r
bikes_tbl <- read_csv("PD 2021 Wk 31 Input.csv")
```

    ## 
    ## ── Column specification ────────────────────────────────────────────────────────
    ## cols(
    ##   Date = col_character(),
    ##   Store = col_character(),
    ##   Item = col_character(),
    ##   Status = col_character(),
    ##   `Number of Items` = col_double()
    ## )

## INSPECTING DATA

-   Inspecting first five rows:

``` r
bikes_tbl %>%
  head() %>%
  kable()
```

| Date       | Store     | Item    | Status                 | Number of Items |
|:-----------|:----------|:--------|:-----------------------|----------------:|
| 05/07/2021 | Bristol   | Saddles | Sold                   |               3 |
| 05/07/2021 | York      | Saddles | Sold                   |               3 |
| 05/07/2021 | Wimbledon | Saddles | Sold                   |               1 |
| 05/07/2021 | Wimbledon | Saddles | Return to Manufacturer |               1 |
| 05/07/2021 | Stratford | Saddles | Sold                   |               2 |
| 05/07/2021 | Bristol   | Tyres   | Sold                   |               3 |

-   Detailed summary:

``` r
bikes_tbl %>%
  skim()
```

|                                                  |            |
|:-------------------------------------------------|:-----------|
| Name                                             | Piped data |
| Number of rows                                   | 71         |
| Number of columns                                | 5          |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |            |
| Column type frequency:                           |            |
| character                                        | 4          |
| numeric                                          | 1          |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |            |
| Group variables                                  | None       |

Data summary

**Variable type: character**

| skim\_variable | n\_missing | complete\_rate | min | max | empty | n\_unique | whitespace |
|:---------------|-----------:|---------------:|----:|----:|------:|----------:|-----------:|
| Date           |          0 |              1 |  10 |  10 |     0 |         6 |          0 |
| Store          |          0 |              1 |   4 |   9 |     0 |         4 |          0 |
| Item           |          0 |              1 |   5 |   7 |     0 |         4 |          0 |
| Status         |          0 |              1 |   4 |  22 |     0 |         2 |          0 |

**Variable type: numeric**

| skim\_variable  | n\_missing | complete\_rate | mean |  sd |  p0 | p25 | p50 | p75 | p100 | hist  |
|:----------------|-----------:|---------------:|-----:|----:|----:|----:|----:|----:|-----:|:------|
| Number of Items |          0 |              1 | 1.82 | 0.8 |   1 |   1 |   2 |   2 |    4 | ▇▇▁▃▁ |

-   Glimpse data:

``` r
bikes_tbl %>%
  glimpse()
```

    ## Rows: 71
    ## Columns: 5
    ## $ Date              <chr> "05/07/2021", "05/07/2021", "05/07/2021", "05/07/202…
    ## $ Store             <chr> "Bristol", "York", "Wimbledon", "Wimbledon", "Stratf…
    ## $ Item              <chr> "Saddles", "Saddles", "Saddles", "Saddles", "Saddles…
    ## $ Status            <chr> "Sold", "Sold", "Sold", "Return to Manufacturer", "S…
    ## $ `Number of Items` <dbl> 3, 3, 1, 1, 2, 3, 2, 1, 2, 2, 2, 2, 2, 2, 2, 2, 3, 1…

## DATA WRANGLING

### 1. Remove the ‘Return to Manufacturer’ records

``` r
bikes_tbl_clean_1 <- bikes_tbl %>%
  filter(Status != "Return to Manufacturer") 

bikes_tbl_clean_1 %>%
  head(10) %>%
  kable()
```

| Date       | Store     | Item    | Status | Number of Items |
|:-----------|:----------|:--------|:-------|----------------:|
| 05/07/2021 | Bristol   | Saddles | Sold   |               3 |
| 05/07/2021 | York      | Saddles | Sold   |               3 |
| 05/07/2021 | Wimbledon | Saddles | Sold   |               1 |
| 05/07/2021 | Stratford | Saddles | Sold   |               2 |
| 05/07/2021 | Bristol   | Tyres   | Sold   |               3 |
| 05/07/2021 | York      | Tyres   | Sold   |               2 |
| 05/07/2021 | Wimbledon | Tyres   | Sold   |               1 |
| 05/07/2021 | Stratford | Tyres   | Sold   |               2 |
| 05/07/2021 | Bristol   | Wheels  | Sold   |               2 |
| 05/07/2021 | York      | Wheels  | Sold   |               2 |

### 2. Create a total for each Store of all the items sold

``` r
bikes_summary <- bikes_tbl_clean_1 %>%
  group_by(Store) %>%
  summarise(items_sold_per_store = sum(`Number of Items`))



bikes_summary  %>%
  head(10) %>%
  kable()
```

| Store     | items\_sold\_per\_store |
|:----------|------------------------:|
| Bristol   |                      30 |
| Stratford |                      29 |
| Wimbledon |                      30 |
| York      |                      28 |

### 3. Aggregate the data to Store sales by Item

``` r
bikes_pivot_1 <- bikes_tbl_clean_1 %>%
  pivot_table(
    .rows = Store,
    .columns = Item,
    .values = ~ SUM(`Number of Items`)
  )

# Join summary table with bikes_tbl_clean_1

 bikes_pivot_2 <-  bikes_pivot_1  %>% 
  left_join(bikes_summary) %>%
   select(items_sold_per_store, Wheels, Tyres, Saddles, Brakes, Store)
```

    ## Joining, by = "Store"

``` r
 bikes_pivot_2 %>%
  kable()
```

| items\_sold\_per\_store | Wheels | Tyres | Saddles | Brakes | Store     |
|------------------------:|-------:|------:|--------:|-------:|:----------|
|                      30 |      8 |     9 |       6 |      7 | Bristol   |
|                      29 |      5 |     6 |       8 |     10 | Stratford |
|                      30 |      7 |     8 |       5 |     10 | Wimbledon |
|                      28 |      7 |     6 |       9 |      6 | York      |

### 4. Output data

``` r
bikes_pivot_2 %>%
  write_csv("PD_2021_WK_31_OUTPUT.csv")
```
