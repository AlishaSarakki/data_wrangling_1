Data manipulatiom
================

``` r
library(tidyverse)
```

    ## ── Attaching packages ───────────────────────────────────────── tidyverse 1.3.0 ──

    ## ✓ ggplot2 3.3.2     ✓ purrr   0.3.4
    ## ✓ tibble  3.0.3     ✓ dplyr   1.0.2
    ## ✓ tidyr   1.1.2     ✓ stringr 1.4.0
    ## ✓ readr   1.3.1     ✓ forcats 0.5.0

    ## ── Conflicts ──────────────────────────────────────────── tidyverse_conflicts() ──
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

## Load in the FAS litters data

``` r
litters_df = read.csv("./data/FAS_litters.csv")
litters_df = janitor::clean_names(litters_df)
```

## ‘select’

choose some columns and not others.

Here, select which columns you want displayed.

``` r
select(litters_df, group, litter_number)
```

    

Below, select columns from a starting point to an ending point (range)
using a colon.

``` r
select(litters_df, group, gd0_weight:gd_of_birth)
```

   

To get rid of a column, use a minus sign:

``` r
select(litters_df, -litter_number)
```


Renaming columns…..

``` r
select(litters_df, GROUP = group, LITTer_NUmBer = litter_number)
```

   

If you do not want to simultaneously select and rename,

``` r
rename(litters_df, Group = group, Litter_NUMBER = litter_number)
```

   

Select helpers

``` r
select(litters_df, starts_with("gd"))
```

   

these helper functions give us a quick way to select things - this
selects everything that starts with ‘gd’

``` r
select(litters_df, litter_number, everything())
```

   

moves litter number to the beginning, but keeps everything else another
way to do this:

``` r
relocate(litters_df, litter_number)
```

  
## ‘filter’

keep only certain ROWS. (‘select’ operates on columns)

``` r
filter(litters_df, gd0_weight < 22)
```

    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ## 1  Con7           #85       19.7        34.7          20               3
    ## 2  Mod7           #59       17.0        33.4          19               8
    ## 3  Mod7          #103       21.4        42.1          19               9
    ## 4  Mod7          #106       21.7        37.8          20               5
    ## 5  Mod7           #62       19.5        35.9          19               7
    ## 6  Low8           #53       21.8        37.2          20               8
    ## 7  Low8          #100       20.0        39.2          20               8
    ## 8  Low8         #4/84       21.8        35.2          20               4
    ##   pups_dead_birth pups_survive
    ## 1               4            3
    ## 2               0            5
    ## 3               1            9
    ## 4               0            2
    ## 5               2            4
    ## 6               1            7
    ## 7               0            7
    ## 8               0            4

``` r
filter(litters_df, gd0_weight >= 22)
```

  

\! indicates that it is not equal to.

``` r
filter(litters_df, !(gd_of_birth == 20))
```

    
``` r
filter(litters_df, gd0_weight >= 22, gd_of_birth == 20)
```


``` r
filter(litters_df, group == "Mod8")
```

    ##   group litter_number gd0_weight gd18_weight gd_of_birth pups_born_alive
    ## 1  Mod8           #97       24.5        42.8          20               8
    ## 2  Mod8         #5/93         NA        41.1          20              11
    ## 3  Mod8       #5/93/2         NA          NA          19               8
    ## 4  Mod8     #7/82-3-2       26.9        43.2          20               7
    ## 5  Mod8    #7/110/3-2       27.5        46.0          19               8
    ## 6  Mod8       #2/95/2       28.5        44.5          20               9
    ## 7  Mod8         #82/4       33.4        52.7          20               8
    ##   pups_dead_birth pups_survive
    ## 1               1            8
    ## 2               0            9
    ## 3               0            8
    ## 4               0            7
    ## 5               1            8
    ## 6               0            9
    ## 7               0            6

``` r
filter(litters_df, group %in% c("Con7", "Mod8"))
```

   

Tells us where it has con7 or mod8. Keep track of everything you want to
include in your dataset\!\!

## ‘mutate’

``` r
mutate(litters_df, wt_gain = gd18_weight - gd0_weight)
```

   

``` r
mutate(
  litters_df, 
  wt_gain = gd18_weight - gd0_weight,
  group = str_to_lower(group))
```

   

Create a new variable, modify an existing variable. Everything in group
is lower case, new variables get tacked on to the end.

## ‘arrange’

Function: put things in order

``` r
arrange(litters_df, pups_born_alive, gd0_weight)
```

  
first in order for pups born alive, and within that, gestational day 0
weight.

## ‘%\>%’

PIPING\!\!\!\!\!\!\!\!\!

``` r
litters_data_raw = read.csv("./data/FAS_litters.csv")
litters_clean_name = janitor::clean_names(litters_data_raw)
litters_data_selected = select(litters_clean_name, -pups_survive)
litters_mutated = mutate(litters_data_selected, wt_gain = gd18_weight - gd0_weight )
litters_without_missing = drop_na(litters_mutated, gd0_weight)
```

USE THE PIPE OPERATOR INSTEAD OF ALL OF THIS. Below yields the same
results, but it’s much cleaner.

``` r
litters_df = 
  read.csv("./data/FAS_litters.csv") %>% 
  janitor::clean_names() %>% 
  select(-pups_survive) %>% 
  mutate(wt_gain = gd18_weight - gd0_weight) %>% 
  drop_na(gd0_weight)
```
