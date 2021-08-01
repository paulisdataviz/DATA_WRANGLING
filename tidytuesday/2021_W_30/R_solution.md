2021 W30 tidytuesday - The Olympics
================
Paula M
7/28/2021

## About the Data

Data comes from Kaggle and it contains **120 years of Olympic history**
with basic bio data on athletes and medal results from Athens 1896 to
Rio 2016
<https://www.kaggle.com/heesoo37/120-years-of-olympic-history-athletes-and-results?select=noc_regions.csv>

## LOADING LIBRARIES

``` r
library(tidyverse)
library(skimr)
library(lubridate)
library(knitr)
library(plotly)
#install.packages("hrbrthemes")
library(hrbrthemes)
```

## IMPORTING DATA

``` r
olympics <- readr::read_csv('https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2021/2021-07-27/olympics.csv')
```

    ## 
    ## ── Column specification ────────────────────────────────────────────────────────
    ## cols(
    ##   id = col_double(),
    ##   name = col_character(),
    ##   sex = col_character(),
    ##   age = col_double(),
    ##   height = col_double(),
    ##   weight = col_double(),
    ##   team = col_character(),
    ##   noc = col_character(),
    ##   games = col_character(),
    ##   year = col_double(),
    ##   season = col_character(),
    ##   city = col_character(),
    ##   sport = col_character(),
    ##   event = col_character(),
    ##   medal = col_character()
    ## )

## INSPECTING DATA

-   Inspecting first five rows

``` r
olympics %>%
  head() %>%
  kable()
```

|  id | name                     | sex | age | height | weight | team           | noc | games       | year | season | city      | sport         | event                              | medal |
|----:|:-------------------------|:----|----:|-------:|-------:|:---------------|:----|:------------|-----:|:-------|:----------|:--------------|:-----------------------------------|:------|
|   1 | A Dijiang                | M   |  24 |    180 |     80 | China          | CHN | 1992 Summer | 1992 | Summer | Barcelona | Basketball    | Basketball Men’s Basketball        | NA    |
|   2 | A Lamusi                 | M   |  23 |    170 |     60 | China          | CHN | 2012 Summer | 2012 | Summer | London    | Judo          | Judo Men’s Extra-Lightweight       | NA    |
|   3 | Gunnar Nielsen Aaby      | M   |  24 |     NA |     NA | Denmark        | DEN | 1920 Summer | 1920 | Summer | Antwerpen | Football      | Football Men’s Football            | NA    |
|   4 | Edgar Lindenau Aabye     | M   |  34 |     NA |     NA | Denmark/Sweden | DEN | 1900 Summer | 1900 | Summer | Paris     | Tug-Of-War    | Tug-Of-War Men’s Tug-Of-War        | Gold  |
|   5 | Christine Jacoba Aaftink | F   |  21 |    185 |     82 | Netherlands    | NED | 1988 Winter | 1988 | Winter | Calgary   | Speed Skating | Speed Skating Women’s 500 metres   | NA    |
|   5 | Christine Jacoba Aaftink | F   |  21 |    185 |     82 | Netherlands    | NED | 1988 Winter | 1988 | Winter | Calgary   | Speed Skating | Speed Skating Women’s 1,000 metres | NA    |

-   Using Glimpse

``` r
olympics %>%
  glimpse() 
```

    ## Rows: 271,116
    ## Columns: 15
    ## $ id     <dbl> 1, 2, 3, 4, 5, 5, 5, 5, 5, 5, 6, 6, 6, 6, 6, 6, 6, 6, 7, 7, 7, …
    ## $ name   <chr> "A Dijiang", "A Lamusi", "Gunnar Nielsen Aaby", "Edgar Lindenau…
    ## $ sex    <chr> "M", "M", "M", "M", "F", "F", "F", "F", "F", "F", "M", "M", "M"…
    ## $ age    <dbl> 24, 23, 24, 34, 21, 21, 25, 25, 27, 27, 31, 31, 31, 31, 33, 33,…
    ## $ height <dbl> 180, 170, NA, NA, 185, 185, 185, 185, 185, 185, 188, 188, 188, …
    ## $ weight <dbl> 80, 60, NA, NA, 82, 82, 82, 82, 82, 82, 75, 75, 75, 75, 75, 75,…
    ## $ team   <chr> "China", "China", "Denmark", "Denmark/Sweden", "Netherlands", "…
    ## $ noc    <chr> "CHN", "CHN", "DEN", "DEN", "NED", "NED", "NED", "NED", "NED", …
    ## $ games  <chr> "1992 Summer", "2012 Summer", "1920 Summer", "1900 Summer", "19…
    ## $ year   <dbl> 1992, 2012, 1920, 1900, 1988, 1988, 1992, 1992, 1994, 1994, 199…
    ## $ season <chr> "Summer", "Summer", "Summer", "Summer", "Winter", "Winter", "Wi…
    ## $ city   <chr> "Barcelona", "London", "Antwerpen", "Paris", "Calgary", "Calgar…
    ## $ sport  <chr> "Basketball", "Judo", "Football", "Tug-Of-War", "Speed Skating"…
    ## $ event  <chr> "Basketball Men's Basketball", "Judo Men's Extra-Lightweight", …
    ## $ medal  <chr> NA, NA, NA, "Gold", NA, NA, NA, NA, NA, NA, NA, NA, NA, NA, NA,…

-   Detailed Summary

``` r
olympics %>%
  skim()
```

|                                                  |            |
|:-------------------------------------------------|:-----------|
| Name                                             | Piped data |
| Number of rows                                   | 271116     |
| Number of columns                                | 15         |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_   |            |
| Column type frequency:                           |            |
| character                                        | 10         |
| numeric                                          | 5          |
| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ |            |
| Group variables                                  | None       |

Data summary

**Variable type: character**

| skim\_variable | n\_missing | complete\_rate | min | max | empty | n\_unique | whitespace |
|:---------------|-----------:|---------------:|----:|----:|------:|----------:|-----------:|
| name           |          0 |           1.00 |   2 | 108 |     0 |    134731 |          0 |
| sex            |          0 |           1.00 |   1 |   1 |     0 |         2 |          0 |
| team           |          0 |           1.00 |   2 |  47 |     0 |      1184 |          0 |
| noc            |          0 |           1.00 |   3 |   3 |     0 |       230 |          0 |
| games          |          0 |           1.00 |  11 |  11 |     0 |        51 |          0 |
| season         |          0 |           1.00 |   6 |   6 |     0 |         2 |          0 |
| city           |          0 |           1.00 |   4 |  22 |     0 |        42 |          0 |
| sport          |          0 |           1.00 |   4 |  25 |     0 |        66 |          0 |
| event          |          0 |           1.00 |  15 |  85 |     0 |       765 |          0 |
| medal          |     231333 |           0.15 |   4 |   6 |     0 |         3 |          0 |

**Variable type: numeric**

| skim\_variable | n\_missing | complete\_rate |     mean |       sd |   p0 |   p25 |   p50 |      p75 |   p100 | hist  |
|:---------------|-----------:|---------------:|---------:|---------:|-----:|------:|------:|---------:|-------:|:------|
| id             |          0 |           1.00 | 68248.95 | 39022.29 |    1 | 34643 | 68205 | 102097.2 | 135571 | ▇▇▇▇▇ |
| age            |       9474 |           0.97 |    25.56 |     6.39 |   10 |    21 |    24 |     28.0 |     97 | ▇▃▁▁▁ |
| height         |      60171 |           0.78 |   175.34 |    10.52 |  127 |   168 |   175 |    183.0 |    226 | ▁▂▇▂▁ |
| weight         |      62875 |           0.77 |    70.70 |    14.35 |   25 |    60 |    70 |     79.0 |    214 | ▃▇▁▁▁ |
| year           |          0 |           1.00 |  1978.38 |    29.88 | 1896 |  1960 |  1988 |   2002.0 |   2016 | ▁▂▃▆▇ |

## DATA WRANGLING

Goal: Visualize a heatmap of proportion of Women participation by year
and Sport

### 1. Convert **year** from numeric to Date format

``` r
olympics$year <- as.Date(paste(olympics$year , 1, 1), '%Y %m %d')
```

### 2. Calculating Total counts by Sport \| Year

``` r
count_sport_year <- olympics %>%
  select(sex, season, sport, year) %>%
  
  group_by(season,sport, year) %>%
  summarise(total_count_sport_year = n()) %>%
  ungroup()
```

    ## `summarise()` has grouped output by 'season', 'sport'. You can override using the `.groups` argument.

``` r
count_sport_year %>%
  head() %>%
  kable()
```

| season | sport       | year       | total\_count\_sport\_year |
|:-------|:------------|:-----------|--------------------------:|
| Summer | Aeronautics | 1936-01-01 |                         1 |
| Summer | Alpinism    | 1932-01-01 |                         2 |
| Summer | Alpinism    | 1936-01-01 |                         2 |
| Summer | Archery     | 1900-01-01 |                        32 |
| Summer | Archery     | 1904-01-01 |                        70 |
| Summer | Archery     | 1908-01-01 |                        77 |

### 3. Calculating counts by Sex \| Sport \| Year

``` r
count_sex_sport_year <- olympics %>%
  select(season, sex, sport, year) %>%
  
  group_by(season, sex, sport, year) %>%
  summarise(count_sex_sport_year = n()) %>%
  ungroup()
```

    ## `summarise()` has grouped output by 'season', 'sex', 'sport'. You can override using the `.groups` argument.

``` r
count_sex_sport_year %>%
  head() %>%
  kable()
```

| season | sex | sport    | year       | count\_sex\_sport\_year |
|:-------|:----|:---------|:-----------|------------------------:|
| Summer | F   | Alpinism | 1936-01-01 |                       1 |
| Summer | F   | Archery  | 1904-01-01 |                      16 |
| Summer | F   | Archery  | 1908-01-01 |                      25 |
| Summer | F   | Archery  | 1972-01-01 |                      40 |
| Summer | F   | Archery  | 1976-01-01 |                      27 |
| Summer | F   | Archery  | 1980-01-01 |                      29 |

### 4. Getting Proportions

``` r
olympics_tidy1 <- count_sex_sport_year %>%
  # Combining two previous tables
  left_join(count_sport_year) %>%
  # Getting Proportions
  mutate(pct = round((count_sex_sport_year / total_count_sport_year),1)) %>%
  # Keep Female rows only
  filter(sex == 'F') %>%
  # Converting sport as factor to preserve alphabetical order
  mutate(sport = as.factor(sport) %>% fct_rev())
```

    ## Joining, by = c("season", "sport", "year")

``` r
olympics_tidy1 %>%
  head() %>%
  kable()
```

| season | sex | sport    | year       | count\_sex\_sport\_year | total\_count\_sport\_year | pct |
|:-------|:----|:---------|:-----------|------------------------:|--------------------------:|----:|
| Summer | F   | Alpinism | 1936-01-01 |                       1 |                         2 | 0.5 |
| Summer | F   | Archery  | 1904-01-01 |                      16 |                        70 | 0.2 |
| Summer | F   | Archery  | 1908-01-01 |                      25 |                        77 | 0.3 |
| Summer | F   | Archery  | 1972-01-01 |                      40 |                        95 | 0.4 |
| Summer | F   | Archery  | 1976-01-01 |                      27 |                        64 | 0.4 |
| Summer | F   | Archery  | 1980-01-01 |                      29 |                        67 | 0.4 |

## DATA VISUALIZATION

``` r
p_summer <- olympics_tidy1 %>%
  filter(season == "Summer") %>%
  ggplot(aes(year, sport, fill = pct)) +
  geom_tile() +
  geom_text(aes(label=scales::percent(pct)), size = 3) +
  
  scale_fill_distiller(palette = "RdPu", direction = 1) +
  theme_ipsum() +
  labs(
        title = "Female Participation rate in the Olympics",
        subtitle = "Summer Season | 1900 - 2016",
        x = "Year",
        y = "Sport"
    ) +
   theme(
    legend.position = "none",
    axis.text.x = element_text(angle = 45, hjust = 1),
    panel.background = element_rect(fill = "grey95", colour = "grey50")
  ) + scale_x_date(date_breaks = "2 year", date_labels = "%y")

p_summer
```

![](2021_W_30_tidy_tuesday_file_files/figure-gfm/unnamed-chunk-10-1.png)<!-- -->

``` r
#ggplotly(p_summer, tooltip = "pct")
```

``` r
p_winter <- olympics_tidy1 %>%
  filter(season == "Winter") %>%
  ggplot(aes(year, sport, fill = pct)) +
  geom_tile() +
  geom_text(aes(label=scales::percent(pct)), size = 3) +
#  facet_wrap(~season, scales = "free_x") +
  
  scale_fill_distiller(palette = "RdPu", direction = 1) +
  theme_ipsum() +
  labs(
        title = "Female Participation rate in the Olympics",
        subtitle = "Winter Season | 1900 - 2016",
        x = "Year",
        y = "Sport") +
  theme(
    legend.position = "none",
    axis.text.x = element_text(angle = 45, hjust = 1),
    panel.background = element_rect(fill = "grey95", colour = "grey50")
  ) 
#+ scale_x_date(date_breaks = "2 year", date_labels = "%Y")
    

p_winter
```

![](2021_W_30_tidy_tuesday_file_files/figure-gfm/unnamed-chunk-11-1.pnghttps://github.com/paulisdataviz/DATA_WRANGLING/blob/main/2021_W_30_tidy_tuesday_file_files/figure-gfm/unnamed-chunk-11-1.png
``` r
#ggplotly(p_winter, tooltip = "pct")
```
