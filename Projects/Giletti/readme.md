Project Giletti
================

``` r
pacman::p_load(tidyverse,
               academictwitteR
               )
```

``` r
get_user_id("GilettiMassimo") %>% get_user_followers() -> MGfollowers
```
