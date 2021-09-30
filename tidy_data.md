Tidy\_data
================

## `pivot_longer`

Load the PULSE data

``` r
pulse_df = 
  read_sas("data/public_pulse_data.sas7bdat") %>% 
  janitor::clean_names()
```

Let’s try to pivot

``` r
pulse_tidy = 
  pulse_df %>% 
  pivot_longer(
    bdi_score_bl:bdi_score_12m,
    names_to = "visit",
    names_prefix = "bdi_score_",
    values_to = "bdi"
  ) %>% 
  mutate(
    visit = replace(visit, visit == "bl", "00m"),
    visit = factor(visit)
  )
```

## `pivor_wider`

make up a results data table

``` r
analysis_df = 
  tibble(
    group = c("treatment", "treatment", "control", "control"),
    time = c("a", "b", "a", "b"),
    group_mean = c(4, 8, 3, 6)
  )
analysis_df
```

    ## # A tibble: 4 x 3
    ##   group     time  group_mean
    ##   <chr>     <chr>      <dbl>
    ## 1 treatment a              4
    ## 2 treatment b              8
    ## 3 control   a              3
    ## 4 control   b              6

``` r
analysis_df %>% 
  pivot_wider(
    names_from = "time",
    values_from = "group_mean"
  ) %>% 
  knitr::kable()
```

| group     |   a |   b |
|:----------|----:|----:|
| treatment |   4 |   8 |
| control   |   3 |   6 |

## `bind_rows`

import the LotR movies words stuff

``` r
fellowship_df = 
  readxl::read_excel("data/LotR_Words.xlsx", range = "B3:D6") %>% 
  mutate(movie = "fellolwship_rings")

two_towers_df = 
  readxl::read_excel("data/LotR_Words.xlsx", range = "F3:H6") %>% 
  mutate(movie = "two_towers")

return_df = 
  readxl::read_excel("data/LotR_Words.xlsx", range = "J3:L6") %>% 
  mutate(movie = "return_king")

lotr_df = 
  bind_rows(fellowship_df, two_towers_df, return_df) %>% 
  janitor::clean_names() %>% 
  pivot_longer(
    female:male,
    names_to = "sex",
    values_to = "words"
  ) %>% 
  relocate(movie)
lotr_df
```

    ## # A tibble: 18 x 4
    ##    movie             race   sex    words
    ##    <chr>             <chr>  <chr>  <dbl>
    ##  1 fellolwship_rings Elf    female  1229
    ##  2 fellolwship_rings Elf    male     971
    ##  3 fellolwship_rings Hobbit female    14
    ##  4 fellolwship_rings Hobbit male    3644
    ##  5 fellolwship_rings Man    female     0
    ##  6 fellolwship_rings Man    male    1995
    ##  7 two_towers        Elf    female   331
    ##  8 two_towers        Elf    male     513
    ##  9 two_towers        Hobbit female     0
    ## 10 two_towers        Hobbit male    2463
    ## 11 two_towers        Man    female   401
    ## 12 two_towers        Man    male    3589
    ## 13 return_king       Elf    female   183
    ## 14 return_king       Elf    male     510
    ## 15 return_king       Hobbit female     2
    ## 16 return_king       Hobbit male    2673
    ## 17 return_king       Man    female   268
    ## 18 return_king       Man    male    2459
