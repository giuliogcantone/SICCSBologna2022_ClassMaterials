APIs and Twitter
================

![](API0.png)

What is an API and why Computational Social Scientists use API?

![](API1.png)

API is first and foremost a source of observational data.

------------------------------------------------------------------------

Before digging into API and Twitter, let’s explore the concept of

### R Packages that allow to connect and download observational data

First of all, let be sure that “pacman” is installed in your library.
With pacman we will install and activate some useful packages

``` r
pacman::p_load("owidR",
               "gutenbergr",
               "academictwitteR")
```

[Our World in Data](https://ourworldindata.org/) is a repository for
demographic and socio-economical data.

It has only few functions. The fastest way to explore all of them is to
digit `owidR::` in a script or within a chunk.

The fast way to use the package `owidR` is in two steps.

-   STEP 1: run the function `owid_search("")` with the “empty string”
    argument:

``` r
owid_search("") %>% View()
```

You can than filter results with the Filter of R Studio. Once some
datasets interest you, copy-paste the `chart id` value.

-   STEP 2: retrive the dataset with the function
    `owid_search("[chart id]")`, with the specific value that you
    selected

``` r
owid("pupil-teacher-ratio-for-primary-education-by-country") -> Education
```

------------------------------------------------------------------------

## Project Gutenberg is cool to download old books. Let’s look at the tible structure of these.

With this code we are going to count how many book per language are
stored in [Project Gutenberg](https://www.gutenberg.org/)

``` r
pacman::p_load(tidyverse)
gutenberg_metadata %>%
  group_by(language) %>%
  count()
```

    ## # A tibble: 103 x 2
    ## # Groups:   language [103]
    ##    language     n
    ##    <chr>    <int>
    ##  1 af           4
    ##  2 ale/en       1
    ##  3 ang/de       1
    ##  4 ang/en       3
    ##  5 ar           1
    ##  6 arp          2
    ##  7 bg           6
    ##  8 bgi/es       1
    ##  9 br           1
    ## 10 ca          28
    ## # ... with 93 more rows

The catalogue can be filtered with `tidyverse` functions.

``` r
gutenberg_metadata %>%
  filter(language == "it",
         title %>% str_detect("Sicil")
         )
```

    ## # A tibble: 7 x 8
    ##   gutenberg_id title    author gutenberg_autho~ language gutenberg_books~ rights
    ##          <int> <chr>    <chr>             <int> <chr>    <chr>            <chr> 
    ## 1        22506 "Le tre~ Sangi~            25368 it       IT Romanzi       Publi~
    ## 2        29409 "La gue~ Amari~            33895 it       IT Storia        Publi~
    ## 3        30984 "Gli av~ Colaj~            34935 it       IT Scienze poli~ Publi~
    ## 4        42649 "Avveni~ Pitrè~            39005 it       IT Folklore      Publi~
    ## 5        46887 "Storia~ Amari~            33895 it       IT Storia        Publi~
    ## 6        46888 "Storia~ Amari~            33895 it       IT Storia        Publi~
    ## 7        47114 "La gue~ Amari~            33895 it       IT Storia        Publi~
    ## # ... with 1 more variable: has_text <lgl>

`gutemberg_id` is the field of values to set as argument for downloading
the whole book.

``` r
gutenberg_download(30984) -> Sicilia
```

    ## Determining mirror for Project Gutenberg from http://www.gutenberg.org/robot/harvest

    ## Using mirror http://aleph.gutenberg.org

This book needs to be cleansed!

``` r
Sicilia %>% select(text)
```

    ## # A tibble: 16,343 x 1
    ##    text                      
    ##    <chr>                     
    ##  1 "D.r NAPOLEONE COLAJANNI" 
    ##  2 ""                        
    ##  3 "_Deputato al Parlamento_"
    ##  4 ""                        
    ##  5 ""                        
    ##  6 "GLI"                     
    ##  7 ""                        
    ##  8 "AVVENIMENTI DI SICILIA"  
    ##  9 ""                        
    ## 10 "E"                       
    ## # ... with 16,333 more rows

Usually books in **English** come already cleansed, in **other
languages** not.

------------------------------------------------------------------------

Napoleone Colajanni (1847 - 1921): Sicilian sociologist.

## He wrote about crime and racism and was a rival to Cesare Lombroso.
