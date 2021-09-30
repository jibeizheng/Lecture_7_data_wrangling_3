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
  mutate(race = str_to_lower(race)) %>% 
  relocate(movie)
lotr_df
```

    ## # A tibble: 18 x 4
    ##    movie             race   sex    words
    ##    <chr>             <chr>  <chr>  <dbl>
    ##  1 fellolwship_rings elf    female  1229
    ##  2 fellolwship_rings elf    male     971
    ##  3 fellolwship_rings hobbit female    14
    ##  4 fellolwship_rings hobbit male    3644
    ##  5 fellolwship_rings man    female     0
    ##  6 fellolwship_rings man    male    1995
    ##  7 two_towers        elf    female   331
    ##  8 two_towers        elf    male     513
    ##  9 two_towers        hobbit female     0
    ## 10 two_towers        hobbit male    2463
    ## 11 two_towers        man    female   401
    ## 12 two_towers        man    male    3589
    ## 13 return_king       elf    female   183
    ## 14 return_king       elf    male     510
    ## 15 return_king       hobbit female     2
    ## 16 return_king       hobbit male    2673
    ## 17 return_king       man    female   268
    ## 18 return_king       man    male    2459

## `joins`

Look at FAS data.

``` r
litters_df = 
  read_csv("data/FAS_litters.csv") %>% 
  janitor::clean_names() %>% 
  separate(group, into = c("dose", "day_of_tx"), 3) %>% 
  relocate(litter_number) %>% 
  mutate(dose = str_to_lower(dose))
```

    ## Rows: 49 Columns: 8

    ## -- Column specification --------------------------------------------------------
    ## Delimiter: ","
    ## chr (2): Group, Litter Number
    ## dbl (6): GD0 weight, GD18 weight, GD of Birth, Pups born alive, Pups dead @ ...

    ## 
    ## i Use `spec()` to retrieve the full column specification for this data.
    ## i Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
litters_df
```

    ## # A tibble: 49 x 9
    ##    litter_number   dose  day_of_tx gd0_weight gd18_weight gd_of_birth
    ##    <chr>           <chr> <chr>          <dbl>       <dbl>       <dbl>
    ##  1 #85             con   7               19.7        34.7          20
    ##  2 #1/2/95/2       con   7               27          42            19
    ##  3 #5/5/3/83/3-3   con   7               26          41.4          19
    ##  4 #5/4/2/95/2     con   7               28.5        44.1          19
    ##  5 #4/2/95/3-3     con   7               NA          NA            20
    ##  6 #2/2/95/3-2     con   7               NA          NA            20
    ##  7 #1/5/3/83/3-3/2 con   7               NA          NA            20
    ##  8 #3/83/3-3       con   8               NA          NA            20
    ##  9 #2/95/3         con   8               NA          NA            20
    ## 10 #3/5/2/2/95     con   8               28.5        NA            20
    ## # ... with 39 more rows, and 3 more variables: pups_born_alive <dbl>,
    ## #   pups_dead_birth <dbl>, pups_survive <dbl>

``` r
pups_df = 
  read_csv("data/FAS_pups.csv") %>% 
  janitor::clean_names() %>% 
  mutate(sex = recode(sex, `1` = "male", `2` = "female"))
```

    ## Rows: 313 Columns: 6

    ## -- Column specification --------------------------------------------------------
    ## Delimiter: ","
    ## chr (1): Litter Number
    ## dbl (5): Sex, PD ears, PD eyes, PD pivot, PD walk

    ## 
    ## i Use `spec()` to retrieve the full column specification for this data.
    ## i Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
pups_df
```

    ## # A tibble: 313 x 6
    ##    litter_number sex   pd_ears pd_eyes pd_pivot pd_walk
    ##    <chr>         <chr>   <dbl>   <dbl>    <dbl>   <dbl>
    ##  1 #85           male        4      13        7      11
    ##  2 #85           male        4      13        7      12
    ##  3 #1/2/95/2     male        5      13        7       9
    ##  4 #1/2/95/2     male        5      13        8      10
    ##  5 #5/5/3/83/3-3 male        5      13        8      10
    ##  6 #5/5/3/83/3-3 male        5      14        6       9
    ##  7 #5/4/2/95/2   male       NA      14        5       9
    ##  8 #4/2/95/3-3   male        4      13        6       8
    ##  9 #4/2/95/3-3   male        4      13        7       9
    ## 10 #2/2/95/3-2   male        4      NA        8      10
    ## # ... with 303 more rows

Let’s join these up!

``` r
fas_df = 
  left_join(pups_df, litters_df, by = "litter_number") %>% 
  relocate(litter_number, dose, day_of_tx)
fas_df
```

    ## # A tibble: 313 x 14
    ##    litter_number dose  day_of_tx sex   pd_ears pd_eyes pd_pivot pd_walk
    ##    <chr>         <chr> <chr>     <chr>   <dbl>   <dbl>    <dbl>   <dbl>
    ##  1 #85           con   7         male        4      13        7      11
    ##  2 #85           con   7         male        4      13        7      12
    ##  3 #1/2/95/2     con   7         male        5      13        7       9
    ##  4 #1/2/95/2     con   7         male        5      13        8      10
    ##  5 #5/5/3/83/3-3 con   7         male        5      13        8      10
    ##  6 #5/5/3/83/3-3 con   7         male        5      14        6       9
    ##  7 #5/4/2/95/2   con   7         male       NA      14        5       9
    ##  8 #4/2/95/3-3   con   7         male        4      13        6       8
    ##  9 #4/2/95/3-3   con   7         male        4      13        7       9
    ## 10 #2/2/95/3-2   con   7         male        4      NA        8      10
    ## # ... with 303 more rows, and 6 more variables: gd0_weight <dbl>,
    ## #   gd18_weight <dbl>, gd_of_birth <dbl>, pups_born_alive <dbl>,
    ## #   pups_dead_birth <dbl>, pups_survive <dbl>

don’t use gather(), spread(), rbind()
