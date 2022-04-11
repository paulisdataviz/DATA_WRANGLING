2022 Week 14 preppin’ data Challenge - R Solution
================
Paula Munoz
2022/04/10

## 2022: Week 14 -House of Games Winners

Challenge by Jenny Martin.

Challenge Link:
<https://preppindata.blogspot.com/2022/04/2022-week-14-house-of-games-winners.html>

Data Sources:
<https://drive.google.com/file/d/1cKZbluTPNlbBHm5LWHDEVVdsD9SA8l-A/view>

### DETAILS

#### Requirements

-   Input the data

-   Only keep relevant fields and rename certain fields to remove
    duplication

-&gt; Ser. becomes Series

-&gt; Wk. becomes Week

-&gt; T becomes Tu

-&gt; T 1 becomes Th

-&gt; Total becomes Score

-&gt; Week becomes Points

-&gt; Week 1 becomes Rank

-   Filter the data to remove Series that have a null value, or are
    preceded by an ‘N’

-   Calculate the Points without double points Friday

-&gt; Rank the players based on this new field

-&gt; Create a field to determine if there has been a change in winner
for that particular Series and Week

-   Rank the players based on their Score instead

-&gt; Create a field to determine if there has been a change in winner
for that particular Series and Week

-   Calculate the Score if the score on Friday was doubled (instead of
    the Points)

-&gt; Rank the players based on this new field

-&gt; Create a field to determine if there has been a change in winner
for that particular Series and Week

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
input <- readr::read_csv("additional_files/Richard Osman's House of Games - Episode Guide - Players.csv")
```

    ## Warning: Missing column names filled in: 'X25' [25], 'X26' [26], 'X27' [27]

    ## Warning: Duplicated column names deduplicated: 'T' => 'T_1' [8], 'M' =>
    ## 'M_1' [13], 'T' => 'T_2' [14], 'W' => 'W_1' [15], 'T' => 'T_3' [16], 'F'
    ## => 'F_1' [17], 'Week' => 'Week_1' [23], 'Ser.' => 'Ser._1' [28], 'Wk.' =>
    ## 'Wk._1' [29]

    ## 
    ## ── Column specification ────────────────────────────────────────────────────────
    ## cols(
    ##   .default = col_double(),
    ##   Player = col_character(),
    ##   Ser. = col_character(),
    ##   `Rate*` = col_character(),
    ##   M_1 = col_character(),
    ##   T_2 = col_character(),
    ##   W_1 = col_character(),
    ##   T_3 = col_character(),
    ##   F_1 = col_character(),
    ##   Week_1 = col_character(),
    ##   `*Scoring Rate = % of Total Daily Points Scored Across Week` = col_logical(),
    ##   X25 = col_logical(),
    ##   X26 = col_logical(),
    ##   X27 = col_logical(),
    ##   Ser._1 = col_character()
    ## )
    ## ℹ Use `spec()` for the full column specifications.

## INSPECTING DATA

-   Inspecting first five rows:

``` r
input %>%
  head() %>%
  kable()
```

| Player           | Ser. | Wk. | Seat |   M |   T |   W | T\_1 |   F | Total |  Avg | Rate\* | M\_1 | T\_2 | W\_1 | T\_3 | F\_1 | 1st | 2nd | 3rd | 4th | Week | Week\_1 | \*Scoring Rate = % of Total Daily Points Scored Across Week | X25 | X26 | X27 | Ser.\_1 | Wk.\_1 | 4-Player Total |
|:-----------------|:-----|----:|-----:|----:|----:|----:|-----:|----:|------:|-----:|:-------|:-----|:-----|:-----|:-----|:-----|----:|----:|----:|----:|-----:|:--------|:------------------------------------------------------------|:----|:----|:----|:--------|-------:|---------------:|
| Angela Barnes    | 4    |   6 |    4 |  15 |  14 |  14 |   20 |  19 |    82 | 16.4 | 56%    | 1st  | 1st  | 1st  | 1st  | 1st  |   5 |   0 |   0 |   0 |   24 | 1st     | NA                                                          | NA  | NA  | NA  | 2       |      2 |            167 |
| Ed Gamble        | 3    |   1 |    2 |  16 |  11 |  10 |   14 |  12 |    63 | 12.6 | 43%    | 1st  | 1st  | 2nd  | 1st  | 1st  |   4 |   1 |   0 |   0 |   23 | 1st     | NA                                                          | NA  | NA  | NA  | 3       |     20 |            165 |
| Simon Hickson    | 5    |  16 |    4 |  12 |  15 |   6 |   14 |  15 |    62 | 12.4 | 43%    | 1st  | 1st  | 2nd  | 1st  | 1st  |   4 |   1 |   0 |   0 |   23 | 1st     | NA                                                          | NA  | NA  | NA  | 2       |      5 |            164 |
| Steve Pemberton  | 2    |   2 |    2 |  12 |  11 |  15 |   14 |   8 |    60 | 12.0 | 36%    | 1st  | 1st  | 1st  | 1st  | 2nd  |   4 |   1 |   0 |   0 |   22 | 1st     | NA                                                          | NA  | NA  | NA  | 5       |      6 |            162 |
| Shaun Williamson | 4    |  20 |    4 |  14 |  13 |  10 |   11 |  11 |    59 | 11.8 | 38%    | 1st  | 1st  | 2nd  | 1st  | 1st  |   4 |   1 |   0 |   0 |   23 | 1st     | NA                                                          | NA  | NA  | NA  | 5       |     14 |            160 |
| Rufus Hound      | 4    |   3 |    4 |  12 |  15 |  10 |    8 |  13 |    58 | 11.6 | 43%    | 1st  | 1st  | 1st  | 2nd  | 1st  |   4 |   1 |   0 |   0 |   23 | 1st     | NA                                                          | NA  | NA  | NA  | 1       |      1 |            159 |

-   Glimpse both tables:

``` r
input %>%
  glimpse()
```

    ## Rows: 303
    ## Columns: 30
    ## $ Player                                                       <chr> "Angela B…
    ## $ Ser.                                                         <chr> "4", "3",…
    ## $ Wk.                                                          <dbl> 6, 1, 16,…
    ## $ Seat                                                         <dbl> 4, 2, 4, …
    ## $ M                                                            <dbl> 15, 16, 1…
    ## $ T                                                            <dbl> 14, 11, 1…
    ## $ W                                                            <dbl> 14, 10, 6…
    ## $ T_1                                                          <dbl> 20, 14, 1…
    ## $ F                                                            <dbl> 19, 12, 1…
    ## $ Total                                                        <dbl> 82, 63, 6…
    ## $ Avg                                                          <dbl> 16.4, 12.…
    ## $ `Rate*`                                                      <chr> "56%", "4…
    ## $ M_1                                                          <chr> "1st", "1…
    ## $ T_2                                                          <chr> "1st", "1…
    ## $ W_1                                                          <chr> "1st", "2…
    ## $ T_3                                                          <chr> "1st", "1…
    ## $ F_1                                                          <chr> "1st", "1…
    ## $ `1st`                                                        <dbl> 5, 4, 4, …
    ## $ `2nd`                                                        <dbl> 0, 1, 1, …
    ## $ `3rd`                                                        <dbl> 0, 0, 0, …
    ## $ `4th`                                                        <dbl> 0, 0, 0, …
    ## $ Week                                                         <dbl> 24, 23, 2…
    ## $ Week_1                                                       <chr> "1st", "1…
    ## $ `*Scoring Rate = % of Total Daily Points Scored Across Week` <lgl> NA, NA, N…
    ## $ X25                                                          <lgl> NA, NA, N…
    ## $ X26                                                          <lgl> NA, NA, N…
    ## $ X27                                                          <lgl> NA, NA, N…
    ## $ Ser._1                                                       <chr> "2", "3",…
    ## $ Wk._1                                                        <dbl> 2, 20, 5,…
    ## $ `4-Player Total`                                             <dbl> 167, 165,…

``` r
dim(input)
```

    ## [1] 303  30

## DATA WRANGLING

### Keep only relevant fields

``` r
tidy_data  <- input %>%
  select(Player, Ser., Wk., Week, Total, Week_1, F, M_1, T_2, W_1, T_3,F_1)

tidy_data%>%
  head(10) %>%
  kable() 
```

| Player           | Ser. | Wk. | Week | Total | Week\_1 |   F | M\_1 | T\_2 | W\_1 | T\_3 | F\_1 |
|:-----------------|:-----|----:|-----:|------:|:--------|----:|:-----|:-----|:-----|:-----|:-----|
| Angela Barnes    | 4    |   6 |   24 |    82 | 1st     |  19 | 1st  | 1st  | 1st  | 1st  | 1st  |
| Ed Gamble        | 3    |   1 |   23 |    63 | 1st     |  12 | 1st  | 1st  | 2nd  | 1st  | 1st  |
| Simon Hickson    | 5    |  16 |   23 |    62 | 1st     |  15 | 1st  | 1st  | 2nd  | 1st  | 1st  |
| Steve Pemberton  | 2    |   2 |   22 |    60 | 1st     |   8 | 1st  | 1st  | 1st  | 1st  | 2nd  |
| Shaun Williamson | 4    |  20 |   23 |    59 | 1st     |  11 | 1st  | 1st  | 2nd  | 1st  | 1st  |
| Rufus Hound      | 4    |   3 |   23 |    58 | 1st     |  13 | 1st  | 1st  | 1st  | 2nd  | 1st  |
| Ivo Graham (C)   | 5    |  18 |   22 |    57 | 1st     |  14 | 2nd  | 1st  | 2nd  | 1st  | 1st  |
| Matt Forde       | 3    |  13 |   22 |    57 | 1st     |  15 | 1st  | 2nd  | 1st  | 2nd  | 1st  |
| Nish Kumar (C)   | 3    |  20 |   22 |    57 | 1st     |  12 | 1st  | 1st  | 1st  | 1st  | 2nd  |
| Phill Jupitus    | 3    |   3 |   22 |    57 | 1st     |   9 | 1st  | 1st  | 1st  | 1st  | 2nd  |

### Rename fields

``` r
tidy_data  <- tidy_data%>%
  rename(
    Rank    = Week_1,
    Points  = Week,
    Score   = Total,
    Week    = Wk.,
    Series  = Ser.
    )

tidy_data%>%
  head(10) %>%
  kable() 
```

| Player           | Series | Week | Points | Score | Rank |   F | M\_1 | T\_2 | W\_1 | T\_3 | F\_1 |
|:-----------------|:-------|-----:|-------:|------:|:-----|----:|:-----|:-----|:-----|:-----|:-----|
| Angela Barnes    | 4      |    6 |     24 |    82 | 1st  |  19 | 1st  | 1st  | 1st  | 1st  | 1st  |
| Ed Gamble        | 3      |    1 |     23 |    63 | 1st  |  12 | 1st  | 1st  | 2nd  | 1st  | 1st  |
| Simon Hickson    | 5      |   16 |     23 |    62 | 1st  |  15 | 1st  | 1st  | 2nd  | 1st  | 1st  |
| Steve Pemberton  | 2      |    2 |     22 |    60 | 1st  |   8 | 1st  | 1st  | 1st  | 1st  | 2nd  |
| Shaun Williamson | 4      |   20 |     23 |    59 | 1st  |  11 | 1st  | 1st  | 2nd  | 1st  | 1st  |
| Rufus Hound      | 4      |    3 |     23 |    58 | 1st  |  13 | 1st  | 1st  | 1st  | 2nd  | 1st  |
| Ivo Graham (C)   | 5      |   18 |     22 |    57 | 1st  |  14 | 2nd  | 1st  | 2nd  | 1st  | 1st  |
| Matt Forde       | 3      |   13 |     22 |    57 | 1st  |  15 | 1st  | 2nd  | 1st  | 2nd  | 1st  |
| Nish Kumar (C)   | 3      |   20 |     22 |    57 | 1st  |  12 | 1st  | 1st  | 1st  | 1st  | 2nd  |
| Phill Jupitus    | 3      |    3 |     22 |    57 | 1st  |   9 | 1st  | 1st  | 1st  | 1st  | 2nd  |

### Filter the data to remove Series that have a null value, or are preceded by an ‘N’

``` r
# See different values for 'Series', we can see there is 'N1', 'N2' and Nulls/ NA
tidy_data %>%
  group_by(Series) %>%
  summarise( n = n())
```

    ## # A tibble: 8 × 2
    ##   Series     n
    ##   <chr>  <int>
    ## 1 1         12
    ## 2 2         40
    ## 3 3         80
    ## 4 4         80
    ## 5 5         72
    ## 6 N1         8
    ## 7 N2         8
    ## 8 <NA>       3

``` r
tidy_data <- tidy_data %>%
  filter(
    !is.na(Series),
    !str_detect(Series, "^N"))
```

``` r
# Confirming unwanted values have been removed
tidy_data %>%
  group_by(Series) %>%
  summarise( n = n())
```

    ## # A tibble: 5 × 2
    ##   Series     n
    ##   <chr>  <int>
    ## 1 1         12
    ## 2 2         40
    ## 3 3         80
    ## 4 4         80
    ## 5 5         72

``` r
dim(tidy_data)
```

    ## [1] 284  12

``` r
# Reduced number of rows from 303 to 284
```

``` r
tidy_data %>%
  glimpse()
```

    ## Rows: 284
    ## Columns: 12
    ## $ Player <chr> "Angela Barnes", "Ed Gamble", "Simon Hickson", "Steve Pemberton…
    ## $ Series <chr> "4", "3", "5", "2", "4", "4", "5", "3", "3", "3", "2", "5", "5"…
    ## $ Week   <dbl> 6, 1, 16, 2, 20, 3, 18, 13, 20, 3, 9, 6, 12, 11, 3, 5, 1, 5, 17…
    ## $ Points <dbl> 24, 23, 23, 22, 23, 23, 22, 22, 22, 22, 21, 23, 23, 21, 19, 21,…
    ## $ Score  <dbl> 82, 63, 62, 60, 59, 58, 57, 57, 57, 57, 56, 56, 56, 55, 55, 54,…
    ## $ Rank   <chr> "1st", "1st", "1st", "1st", "1st", "1st", "1st", "1st", "1st", …
    ## $ F      <dbl> 19, 12, 15, 8, 11, 13, 14, 15, 12, 9, 16, 12, 14, 14, 7, 9, 12,…
    ## $ M_1    <chr> "1st", "1st", "1st", "1st", "1st", "1st", "2nd", "1st", "1st", …
    ## $ T_2    <chr> "1st", "1st", "1st", "1st", "1st", "1st", "1st", "2nd", "1st", …
    ## $ W_1    <chr> "1st", "2nd", "2nd", "1st", "2nd", "1st", "2nd", "1st", "1st", …
    ## $ T_3    <chr> "1st", "1st", "1st", "1st", "1st", "2nd", "1st", "2nd", "1st", …
    ## $ F_1    <chr> "1st", "1st", "1st", "2nd", "1st", "1st", "1st", "1st", "2nd", …

### Convert Ranks to numeric

``` r
 tidy_data$Rank <- tidy_data$Rank %>%
  substr(1,1) %>%
  as.integer()
  
 tidy_data$M_1 <- tidy_data$M_1 %>%
  substr(1,1) %>%
  as.integer()
 
  tidy_data$T_2 <- tidy_data$T_2 %>%
  substr(1,1) %>%
  as.integer()
  
  tidy_data$W_1 <- tidy_data$W_1 %>%
  substr(1,1) %>%
  as.integer()

 tidy_data$T_3 <- tidy_data$T_3 %>%
  substr(1,1) %>%
  as.integer()
  
 tidy_data$F_1 <- tidy_data$F_1 %>%
  substr(1,1) %>%
  as.integer()

tidy_data%>%
  glimpse()  
```

    ## Rows: 284
    ## Columns: 12
    ## $ Player <chr> "Angela Barnes", "Ed Gamble", "Simon Hickson", "Steve Pemberton…
    ## $ Series <chr> "4", "3", "5", "2", "4", "4", "5", "3", "3", "3", "2", "5", "5"…
    ## $ Week   <dbl> 6, 1, 16, 2, 20, 3, 18, 13, 20, 3, 9, 6, 12, 11, 3, 5, 1, 5, 17…
    ## $ Points <dbl> 24, 23, 23, 22, 23, 23, 22, 22, 22, 22, 21, 23, 23, 21, 19, 21,…
    ## $ Score  <dbl> 82, 63, 62, 60, 59, 58, 57, 57, 57, 57, 56, 56, 56, 55, 55, 54,…
    ## $ Rank   <int> 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 2, 1, …
    ## $ F      <dbl> 19, 12, 15, 8, 11, 13, 14, 15, 12, 9, 16, 12, 14, 14, 7, 9, 12,…
    ## $ M_1    <int> 1, 1, 1, 1, 1, 1, 2, 1, 1, 1, 1, 1, 2, 3, 3, 1, 2, 1, 1, 2, 1, …
    ## $ T_2    <int> 1, 1, 1, 1, 1, 1, 1, 2, 1, 1, 1, 1, 1, 1, 1, 1, 1, 1, 2, 1, 1, …
    ## $ W_1    <int> 1, 2, 2, 1, 2, 1, 2, 1, 1, 1, 3, 1, 1, 2, 2, 1, 1, 2, 1, 1, 1, …
    ## $ T_3    <int> 1, 1, 1, 1, 1, 2, 1, 2, 1, 1, 2, 2, 1, 1, 1, 2, 1, 1, 1, 3, 2, …
    ## $ F_1    <int> 1, 1, 1, 2, 1, 1, 1, 1, 2, 2, 1, 1, 1, 1, 2, 2, 1, 1, 1, 2, 1, …

``` r
tidy_data %>%
  head()
```

    ## # A tibble: 6 × 12
    ##   Player     Series  Week Points Score  Rank     F   M_1   T_2   W_1   T_3   F_1
    ##   <chr>      <chr>  <dbl>  <dbl> <dbl> <int> <dbl> <int> <int> <int> <int> <int>
    ## 1 Angela Ba… 4          6     24    82     1    19     1     1     1     1     1
    ## 2 Ed Gamble  3          1     23    63     1    12     1     1     2     1     1
    ## 3 Simon Hic… 5         16     23    62     1    15     1     1     2     1     1
    ## 4 Steve Pem… 2          2     22    60     1     8     1     1     1     1     2
    ## 5 Shaun Wil… 4         20     23    59     1    11     1     1     2     1     1
    ## 6 Rufus Hou… 4          3     23    58     1    13     1     1     1     2     1

### Calculate the Points without double points Friday

``` r
tidy_data <- tidy_data %>%
  mutate(points_without_double_points_friday = (5 - M_1) + (5 - T_2) + (5 - W_1) + (5 - T_3) + (5 - F_1))

tidy_data%>%
  head(10) %>%
  kable() 
```

| Player           | Series | Week | Points | Score | Rank |   F | M\_1 | T\_2 | W\_1 | T\_3 | F\_1 | points\_without\_double\_points\_friday |
|:-----------------|:-------|-----:|-------:|------:|-----:|----:|-----:|-----:|-----:|-----:|-----:|----------------------------------------:|
| Angela Barnes    | 4      |    6 |     24 |    82 |    1 |  19 |    1 |    1 |    1 |    1 |    1 |                                      20 |
| Ed Gamble        | 3      |    1 |     23 |    63 |    1 |  12 |    1 |    1 |    2 |    1 |    1 |                                      19 |
| Simon Hickson    | 5      |   16 |     23 |    62 |    1 |  15 |    1 |    1 |    2 |    1 |    1 |                                      19 |
| Steve Pemberton  | 2      |    2 |     22 |    60 |    1 |   8 |    1 |    1 |    1 |    1 |    2 |                                      19 |
| Shaun Williamson | 4      |   20 |     23 |    59 |    1 |  11 |    1 |    1 |    2 |    1 |    1 |                                      19 |
| Rufus Hound      | 4      |    3 |     23 |    58 |    1 |  13 |    1 |    1 |    1 |    2 |    1 |                                      19 |
| Ivo Graham (C)   | 5      |   18 |     22 |    57 |    1 |  14 |    2 |    1 |    2 |    1 |    1 |                                      18 |
| Matt Forde       | 3      |   13 |     22 |    57 |    1 |  15 |    1 |    2 |    1 |    2 |    1 |                                      18 |
| Nish Kumar (C)   | 3      |   20 |     22 |    57 |    1 |  12 |    1 |    1 |    1 |    1 |    2 |                                      19 |
| Phill Jupitus    | 3      |    3 |     22 |    57 |    1 |   9 |    1 |    1 |    1 |    1 |    2 |                                      19 |

### Rank without double points Friday

``` r
tidy_data <- tidy_data %>%
  group_by(Series, Week) %>%
  mutate(rank_without_double_points_friday = dense_rank(desc(points_without_double_points_friday))) %>%
  ungroup()
```

## Create a field to determine if there has been a change in winner for that particular Series and Week

``` r
tidy_data_change_1 <- tidy_data %>%
  group_by(Series, Week) %>%
  arrange(Rank) %>%
  mutate(change_in_winner_no_double_points_friday =  case_when(Rank == rank_without_double_points_friday ~ 'FALSE',
                                                               TRUE ~ 'TRUE')) %>%
  filter(row_number() == 1) %>%
  select(Series, Week, change_in_winner_no_double_points_friday) 
```

## Joining to original table and including change\_in\_winner\_no\_double\_points\_friday

``` r
tidy_data <- tidy_data %>%
  left_join(tidy_data_change_1)
```

    ## Joining, by = c("Series", "Week")

``` r
tidy_data%>%
  head(10) %>%
  kable()
```

| Player           | Series | Week | Points | Score | Rank |   F | M\_1 | T\_2 | W\_1 | T\_3 | F\_1 | points\_without\_double\_points\_friday | rank\_without\_double\_points\_friday | change\_in\_winner\_no\_double\_points\_friday |
|:-----------------|:-------|-----:|-------:|------:|-----:|----:|-----:|-----:|-----:|-----:|-----:|----------------------------------------:|--------------------------------------:|:-----------------------------------------------|
| Angela Barnes    | 4      |    6 |     24 |    82 |    1 |  19 |    1 |    1 |    1 |    1 |    1 |                                      20 |                                     1 | FALSE                                          |
| Ed Gamble        | 3      |    1 |     23 |    63 |    1 |  12 |    1 |    1 |    2 |    1 |    1 |                                      19 |                                     1 | FALSE                                          |
| Simon Hickson    | 5      |   16 |     23 |    62 |    1 |  15 |    1 |    1 |    2 |    1 |    1 |                                      19 |                                     1 | FALSE                                          |
| Steve Pemberton  | 2      |    2 |     22 |    60 |    1 |   8 |    1 |    1 |    1 |    1 |    2 |                                      19 |                                     1 | FALSE                                          |
| Shaun Williamson | 4      |   20 |     23 |    59 |    1 |  11 |    1 |    1 |    2 |    1 |    1 |                                      19 |                                     1 | FALSE                                          |
| Rufus Hound      | 4      |    3 |     23 |    58 |    1 |  13 |    1 |    1 |    1 |    2 |    1 |                                      19 |                                     1 | FALSE                                          |
| Ivo Graham (C)   | 5      |   18 |     22 |    57 |    1 |  14 |    2 |    1 |    2 |    1 |    1 |                                      18 |                                     1 | FALSE                                          |
| Matt Forde       | 3      |   13 |     22 |    57 |    1 |  15 |    1 |    2 |    1 |    2 |    1 |                                      18 |                                     1 | FALSE                                          |
| Nish Kumar (C)   | 3      |   20 |     22 |    57 |    1 |  12 |    1 |    1 |    1 |    1 |    2 |                                      19 |                                     1 | FALSE                                          |
| Phill Jupitus    | 3      |    3 |     22 |    57 |    1 |   9 |    1 |    1 |    1 |    1 |    2 |                                      19 |                                     1 | FALSE                                          |

### Rank the players based on their Score instead

``` r
tidy_data <- tidy_data %>%
  group_by(Series, Week) %>%
  mutate(rank_based_on_score = dense_rank(desc(Score))) %>%
  ungroup()
```

### Create a field to determine if there has been a change in winner for that particular Series and Week

``` r
tidy_data_change_2 <- tidy_data %>%
  group_by(Series, Week) %>%
  arrange(Rank) %>%
  mutate(change_in_winner_based_on_score =  case_when(Rank == rank_based_on_score ~ 'FALSE',
                                                               TRUE ~ 'TRUE')) %>%
  filter(row_number() == 1) %>%
  select(Series, Week, change_in_winner_based_on_score) 
```

## Joining to original table and including change\_in\_winner\_based\_on\_score

``` r
tidy_data <- tidy_data %>%
  left_join(tidy_data_change_2)
```

    ## Joining, by = c("Series", "Week")

``` r
tidy_data%>%
  head(10) %>%
  kable()
```

| Player           | Series | Week | Points | Score | Rank |   F | M\_1 | T\_2 | W\_1 | T\_3 | F\_1 | points\_without\_double\_points\_friday | rank\_without\_double\_points\_friday | change\_in\_winner\_no\_double\_points\_friday | rank\_based\_on\_score | change\_in\_winner\_based\_on\_score |
|:-----------------|:-------|-----:|-------:|------:|-----:|----:|-----:|-----:|-----:|-----:|-----:|----------------------------------------:|--------------------------------------:|:-----------------------------------------------|-----------------------:|:-------------------------------------|
| Angela Barnes    | 4      |    6 |     24 |    82 |    1 |  19 |    1 |    1 |    1 |    1 |    1 |                                      20 |                                     1 | FALSE                                          |                      1 | FALSE                                |
| Ed Gamble        | 3      |    1 |     23 |    63 |    1 |  12 |    1 |    1 |    2 |    1 |    1 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |
| Simon Hickson    | 5      |   16 |     23 |    62 |    1 |  15 |    1 |    1 |    2 |    1 |    1 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |
| Steve Pemberton  | 2      |    2 |     22 |    60 |    1 |   8 |    1 |    1 |    1 |    1 |    2 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |
| Shaun Williamson | 4      |   20 |     23 |    59 |    1 |  11 |    1 |    1 |    2 |    1 |    1 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |
| Rufus Hound      | 4      |    3 |     23 |    58 |    1 |  13 |    1 |    1 |    1 |    2 |    1 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |
| Ivo Graham (C)   | 5      |   18 |     22 |    57 |    1 |  14 |    2 |    1 |    2 |    1 |    1 |                                      18 |                                     1 | FALSE                                          |                      1 | FALSE                                |
| Matt Forde       | 3      |   13 |     22 |    57 |    1 |  15 |    1 |    2 |    1 |    2 |    1 |                                      18 |                                     1 | FALSE                                          |                      1 | FALSE                                |
| Nish Kumar (C)   | 3      |   20 |     22 |    57 |    1 |  12 |    1 |    1 |    1 |    1 |    2 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |
| Phill Jupitus    | 3      |    3 |     22 |    57 |    1 |   9 |    1 |    1 |    1 |    1 |    2 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |

### Calculate the Score if the score on Friday was doubled (instead of the Points)

``` r
tidy_data <- tidy_data %>%
  mutate(score_if_double_friday = Score + F)

tidy_data%>%
  head(10) %>%
  kable()
```

| Player           | Series | Week | Points | Score | Rank |   F | M\_1 | T\_2 | W\_1 | T\_3 | F\_1 | points\_without\_double\_points\_friday | rank\_without\_double\_points\_friday | change\_in\_winner\_no\_double\_points\_friday | rank\_based\_on\_score | change\_in\_winner\_based\_on\_score | score\_if\_double\_friday |
|:-----------------|:-------|-----:|-------:|------:|-----:|----:|-----:|-----:|-----:|-----:|-----:|----------------------------------------:|--------------------------------------:|:-----------------------------------------------|-----------------------:|:-------------------------------------|--------------------------:|
| Angela Barnes    | 4      |    6 |     24 |    82 |    1 |  19 |    1 |    1 |    1 |    1 |    1 |                                      20 |                                     1 | FALSE                                          |                      1 | FALSE                                |                       101 |
| Ed Gamble        | 3      |    1 |     23 |    63 |    1 |  12 |    1 |    1 |    2 |    1 |    1 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        75 |
| Simon Hickson    | 5      |   16 |     23 |    62 |    1 |  15 |    1 |    1 |    2 |    1 |    1 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        77 |
| Steve Pemberton  | 2      |    2 |     22 |    60 |    1 |   8 |    1 |    1 |    1 |    1 |    2 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        68 |
| Shaun Williamson | 4      |   20 |     23 |    59 |    1 |  11 |    1 |    1 |    2 |    1 |    1 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        70 |
| Rufus Hound      | 4      |    3 |     23 |    58 |    1 |  13 |    1 |    1 |    1 |    2 |    1 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        71 |
| Ivo Graham (C)   | 5      |   18 |     22 |    57 |    1 |  14 |    2 |    1 |    2 |    1 |    1 |                                      18 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        71 |
| Matt Forde       | 3      |   13 |     22 |    57 |    1 |  15 |    1 |    2 |    1 |    2 |    1 |                                      18 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        72 |
| Nish Kumar (C)   | 3      |   20 |     22 |    57 |    1 |  12 |    1 |    1 |    1 |    1 |    2 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        69 |
| Phill Jupitus    | 3      |    3 |     22 |    57 |    1 |   9 |    1 |    1 |    1 |    1 |    2 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        66 |

### Rank the players based on this new field

``` r
tidy_data <- tidy_data %>%
  group_by(Series, Week) %>%
  mutate(rank_if_double_score_friday = dense_rank(desc(score_if_double_friday))) %>%
  ungroup()
```

### Create a field to determine if there has been a change in winner for that particular Series and Week

``` r
tidy_data_change_3 <- tidy_data %>%
  group_by(Series, Week) %>%
  arrange(Rank) %>%
  mutate(change_in_winner_double_score_friday=  case_when(Rank == rank_if_double_score_friday ~ 'FALSE',
                                                               TRUE ~ 'TRUE')) %>%
  filter(row_number() == 1) %>%
  select(Series, Week, change_in_winner_double_score_friday) 
```

## Joining to original table and including change\_in\_winner\_double\_score\_friday

``` r
tidy_data <- tidy_data %>%
  left_join(tidy_data_change_3)
```

    ## Joining, by = c("Series", "Week")

``` r
tidy_data%>%
  head(10) %>%
  kable()
```

| Player           | Series | Week | Points | Score | Rank |   F | M\_1 | T\_2 | W\_1 | T\_3 | F\_1 | points\_without\_double\_points\_friday | rank\_without\_double\_points\_friday | change\_in\_winner\_no\_double\_points\_friday | rank\_based\_on\_score | change\_in\_winner\_based\_on\_score | score\_if\_double\_friday | rank\_if\_double\_score\_friday | change\_in\_winner\_double\_score\_friday |
|:-----------------|:-------|-----:|-------:|------:|-----:|----:|-----:|-----:|-----:|-----:|-----:|----------------------------------------:|--------------------------------------:|:-----------------------------------------------|-----------------------:|:-------------------------------------|--------------------------:|--------------------------------:|:------------------------------------------|
| Angela Barnes    | 4      |    6 |     24 |    82 |    1 |  19 |    1 |    1 |    1 |    1 |    1 |                                      20 |                                     1 | FALSE                                          |                      1 | FALSE                                |                       101 |                               1 | FALSE                                     |
| Ed Gamble        | 3      |    1 |     23 |    63 |    1 |  12 |    1 |    1 |    2 |    1 |    1 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        75 |                               1 | FALSE                                     |
| Simon Hickson    | 5      |   16 |     23 |    62 |    1 |  15 |    1 |    1 |    2 |    1 |    1 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        77 |                               1 | FALSE                                     |
| Steve Pemberton  | 2      |    2 |     22 |    60 |    1 |   8 |    1 |    1 |    1 |    1 |    2 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        68 |                               1 | FALSE                                     |
| Shaun Williamson | 4      |   20 |     23 |    59 |    1 |  11 |    1 |    1 |    2 |    1 |    1 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        70 |                               1 | FALSE                                     |
| Rufus Hound      | 4      |    3 |     23 |    58 |    1 |  13 |    1 |    1 |    1 |    2 |    1 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        71 |                               1 | FALSE                                     |
| Ivo Graham (C)   | 5      |   18 |     22 |    57 |    1 |  14 |    2 |    1 |    2 |    1 |    1 |                                      18 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        71 |                               1 | FALSE                                     |
| Matt Forde       | 3      |   13 |     22 |    57 |    1 |  15 |    1 |    2 |    1 |    2 |    1 |                                      18 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        72 |                               1 | FALSE                                     |
| Nish Kumar (C)   | 3      |   20 |     22 |    57 |    1 |  12 |    1 |    1 |    1 |    1 |    2 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        69 |                               1 | FALSE                                     |
| Phill Jupitus    | 3      |    3 |     22 |    57 |    1 |   9 |    1 |    1 |    1 |    1 |    2 |                                      19 |                                     1 | FALSE                                          |                      1 | FALSE                                |                        66 |                               1 | FALSE                                     |

### Remove unnecessary fields

``` r
tidy_data <- tidy_data %>%
  select(Series, Week, Player, Rank, rank_without_double_points_friday, change_in_winner_no_double_points_friday, rank_based_on_score, change_in_winner_based_on_score, rank_if_double_score_friday, change_in_winner_double_score_friday, Score, Points, score_if_double_friday, points_without_double_points_friday) %>%
  arrange(Series, Week)
  
 tidy_data%>%
  head(10) %>%
  kable()  
```

| Series | Week | Player          | Rank | rank\_without\_double\_points\_friday | change\_in\_winner\_no\_double\_points\_friday | rank\_based\_on\_score | change\_in\_winner\_based\_on\_score | rank\_if\_double\_score\_friday | change\_in\_winner\_double\_score\_friday | Score | Points | score\_if\_double\_friday | points\_without\_double\_points\_friday |
|:-------|-----:|:----------------|-----:|--------------------------------------:|:-----------------------------------------------|-----------------------:|:-------------------------------------|--------------------------------:|:------------------------------------------|------:|-------:|--------------------------:|----------------------------------------:|
| 1      |    1 | Nish Kumar      |    1 |                                     1 | FALSE                                          |                      1 | FALSE                                |                               1 | FALSE                                     |    51 |     23 |                        59 |                                      19 |
| 1      |    1 | Al Murray       |    2 |                                     2 | FALSE                                          |                      2 | FALSE                                |                               2 | FALSE                                     |    47 |     17 |                        53 |                                      15 |
| 1      |    1 | Clara Amfo      |    3 |                                     3 | FALSE                                          |                      3 | FALSE                                |                               3 | FALSE                                     |    36 |     13 |                        44 |                                      10 |
| 1      |    1 | Anneka Rice     |    4 |                                     4 | FALSE                                          |                      4 | FALSE                                |                               4 | FALSE                                     |    25 |      7 |                        29 |                                       6 |
| 1      |    2 | Rick Edwards    |    1 |                                     1 | FALSE                                          |                      1 | FALSE                                |                               1 | FALSE                                     |    47 |     21 |                        58 |                                      17 |
| 1      |    2 | Sara Pascoe     |    2 |                                     1 | FALSE                                          |                      2 | FALSE                                |                               2 | FALSE                                     |    40 |     20 |                        47 |                                      17 |
| 1      |    2 | Clive Myrie     |    3 |                                     2 | FALSE                                          |                      3 | FALSE                                |                               3 | FALSE                                     |    20 |     12 |                        24 |                                      10 |
| 1      |    2 | Angela Scanlon  |    4 |                                     3 | FALSE                                          |                      4 | FALSE                                |                               4 | FALSE                                     |    18 |     10 |                        19 |                                       9 |
| 1      |    3 | Jamie Theakston |    1 |                                     1 | FALSE                                          |                      1 | FALSE                                |                               1 | FALSE                                     |    55 |     19 |                        62 |                                      16 |
| 1      |    3 | Chris Ramsey    |    3 |                                     2 | FALSE                                          |                      2 | FALSE                                |                               3 | FALSE                                     |    40 |     14 |                        44 |                                      13 |

### Outut data

``` r
tidy_data %>%
    write_csv("additional_files/2022_W14_Output_From_R.csv")
```
