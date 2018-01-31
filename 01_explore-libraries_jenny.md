01\_explore-libraries\_jenny.R
================
ryamcc1
Wed Jan 31 17:05:49 2018

``` r
## how jenny might do this in a first exploration
## purposely leaving a few things to change later!
```

Which libraries does R search for packages?

``` r
.libPaths()
```

    ## [1] "C:/Program Files/R/R-3.3.3/library"

``` r
## let's confirm the second element is, in fact, the default library
.Library
```

    ## [1] "C:/PROGRA~1/R/R-33~1.3/library"

``` r
library(fs)
path_real(.Library)
```

    ## C:/Program Files/R/R-3.3.3/library

Installed packages

``` r
library(tidyverse)
```

    ## -- Attaching packages --------------------------------------------------------------------------------------------------------------------------------------- tidyverse 1.2.1 --

    ## v ggplot2 2.2.1     v purrr   0.2.4
    ## v tibble  1.4.2     v dplyr   0.7.4
    ## v tidyr   0.7.2     v stringr 1.2.0
    ## v readr   1.1.1     v forcats 0.2.0

    ## -- Conflicts ------------------------------------------------------------------------------------------------------------------------------------------ tidyverse_conflicts() --
    ## x dplyr::filter() masks stats::filter()
    ## x dplyr::lag()    masks stats::lag()

``` r
ipt <- installed.packages() %>%
  as_tibble()

## how many packages?
nrow(ipt)
```

    ## [1] 163

Exploring the packages

``` r
## count some things! inspiration
##   * tabulate by LibPath, Priority, or both
ipt %>%
  count(LibPath, Priority)
```

    ## # A tibble: 3 x 3
    ##   LibPath                            Priority        n
    ##   <chr>                              <chr>       <int>
    ## 1 C:/Program Files/R/R-3.3.3/library base           14
    ## 2 C:/Program Files/R/R-3.3.3/library recommended    15
    ## 3 C:/Program Files/R/R-3.3.3/library <NA>          134

``` r
##   * what proportion need compilation?
ipt %>%
  count(NeedsCompilation) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 3 x 3
    ##   NeedsCompilation     n   prop
    ##   <chr>            <int>  <dbl>
    ## 1 no                  72 0.442 
    ## 2 yes                 83 0.509 
    ## 3 <NA>                 8 0.0491

``` r
##   * how break down re: version of R they were built on
ipt %>%
  count(Built) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 2 x 3
    ##   Built     n   prop
    ##   <chr> <int>  <dbl>
    ## 1 3.3.2    14 0.0859
    ## 2 3.3.3   149 0.914

Reflections

``` r
## reflect on ^^ and make a few notes to yourself; inspiration
##   * does the number of base + recommended packages make sense to you?
##   * how does the result of .libPaths() relate to the result of .Library?
```

Going further

``` r
## if you have time to do more ...

## is every package in .Library either base or recommended?
all_default_pkgs <- list.files(.Library)
all_br_pkgs <- ipt %>%
  filter(Priority %in% c("base", "recommended")) %>%
  pull(Package)
setdiff(all_default_pkgs, all_br_pkgs)
```

    ##   [1] "assertthat"    "backports"     "base64enc"     "BH"           
    ##   [5] "bindr"         "bindrcpp"      "bit"           "bit64"        
    ##   [9] "bitops"        "blob"          "broom"         "car"          
    ##  [13] "caTools"       "cellranger"    "chron"         "cli"          
    ##  [17] "clipr"         "clisymbols"    "colorspace"    "crayon"       
    ##  [21] "curl"          "DBI"           "dbplyr"        "debugme"      
    ##  [25] "desc"          "devtools"      "dichromat"     "digest"       
    ##  [29] "dplyr"         "DT"            "enc"           "evaluate"     
    ##  [33] "extrafont"     "extrafontdb"   "flexdashboard" "forcats"      
    ##  [37] "forecast"      "formatR"       "fracdiff"      "fs"           
    ##  [41] "ggplot2"       "ggthemes"      "gh"            "git2r"        
    ##  [45] "glue"          "gridExtra"     "gsubfn"        "gtable"       
    ##  [49] "haven"         "highr"         "hms"           "htmltools"    
    ##  [53] "htmlwidgets"   "httpuv"        "httr"          "ini"          
    ##  [57] "jsonlite"      "knitr"         "labeling"      "lazyeval"     
    ##  [61] "lme4"          "lmtest"        "lubridate"     "magrittr"     
    ##  [65] "markdown"      "MatrixModels"  "memoise"       "mime"         
    ##  [69] "minqa"         "mnormt"        "modelr"        "moments"      
    ##  [73] "munsell"       "nloptr"        "openssl"       "pbkrtest"     
    ##  [77] "pillar"        "pkgconfig"     "plogr"         "plyr"         
    ##  [81] "praise"        "proto"         "psych"         "purrr"        
    ##  [85] "quadprog"      "quantmod"      "quantreg"      "R6"           
    ##  [89] "RColorBrewer"  "Rcpp"          "RcppArmadillo" "RcppEigen"    
    ##  [93] "readr"         "readxl"        "rematch"       "rematch2"     
    ##  [97] "reprex"        "reshape2"      "rlang"         "rmarkdown"    
    ## [101] "rpivotTable"   "rprojroot"     "RSQLite"       "rstudioapi"   
    ## [105] "Rttf2pt1"      "rvest"         "scales"        "selectr"      
    ## [109] "shiny"         "sourcetools"   "SparseM"       "sqldf"        
    ## [113] "stringi"       "stringr"       "styler"        "testthat"     
    ## [117] "tibble"        "tidyr"         "tidyselect"    "tidyverse"    
    ## [121] "timeDate"      "translations"  "tseries"       "TTR"          
    ## [125] "usethis"       "utf8"          "viridisLite"   "whisker"      
    ## [129] "withr"         "xml2"          "xtable"        "xts"          
    ## [133] "yaml"          "zoo"

``` r
## study package naming style (all lower case, contains '.', etc

## use `fields` argument to installed.packages() to get more info and use it!
ipt2 <- installed.packages(fields = "URL") %>%
  as_tibble()
ipt2 %>%
  mutate(github = grepl("github", URL)) %>%
  count(github) %>%
  mutate(prop = n / sum(n))
```

    ## # A tibble: 2 x 3
    ##   github     n  prop
    ##   <lgl>  <int> <dbl>
    ## 1 F         78 0.479
    ## 2 T         85 0.521
