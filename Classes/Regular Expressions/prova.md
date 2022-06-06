prova
================

![](API0.png)

What is an API and why Computational Social Scientists use API?

![](API1.png)

API is first and foremost a source of observational data.

-   Advanced use: to “upload” and automate.

------------------------------------------------------------------------

Before digging into API and Twitter, let’s explore the concept of

### R Packages that allow to connect and download observational data (even without API)

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
