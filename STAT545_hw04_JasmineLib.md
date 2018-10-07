STAT545\_hw04\_JasmineLib
================

### STAT 545 Homework 4

``` r
suppressPackageStartupMessages(library(tidyverse)) 
suppressPackageStartupMessages(library(gapminder))
```

``` r
test = gapminder %>% 
  select (year, lifeExp, country) %>% 
  filter (country == "China" | country == "India" | country == "Japan") %>% 
   spread(key = "country", value = "lifeExp")
test
```

    ## # A tibble: 12 x 4
    ##     year China India Japan
    ##  * <int> <dbl> <dbl> <dbl>
    ##  1  1952  44.0  37.4  63.0
    ##  2  1957  50.5  40.2  65.5
    ##  3  1962  44.5  43.6  68.7
    ##  4  1967  58.4  47.2  71.4
    ##  5  1972  63.1  50.7  73.4
    ##  6  1977  64.0  54.2  75.4
    ##  7  1982  65.5  56.6  77.1
    ##  8  1987  67.3  58.6  78.7
    ##  9  1992  68.7  60.2  79.4
    ## 10  1997  70.4  61.8  80.7
    ## 11  2002  72.0  62.9  82.0
    ## 12  2007  73.0  64.7  82.6

``` r
class(test)
```

    ## [1] "tbl_df"     "tbl"        "data.frame"

``` r
test  %>% 
  knitr::kable()
```

|  year|     China|   India|   Japan|
|-----:|---------:|-------:|-------:|
|  1952|  44.00000|  37.373|  63.030|
|  1957|  50.54896|  40.249|  65.500|
|  1962|  44.50136|  43.605|  68.730|
|  1967|  58.38112|  47.193|  71.430|
|  1972|  63.11888|  50.651|  73.420|
|  1977|  63.96736|  54.208|  75.380|
|  1982|  65.52500|  56.596|  77.110|
|  1987|  67.27400|  58.553|  78.670|
|  1992|  68.69000|  60.223|  79.360|
|  1997|  70.42600|  61.765|  80.690|
|  2002|  72.02800|  62.879|  82.000|
|  2007|  72.96100|  64.698|  82.603|

``` r
test %>%
  ggplot (aes(China, India)) + 
  geom_point(aes(colour = year)) + 
  theme_classic() +
  ggtitle("Life Expectancy in China vs. Canada") +
  scale_y_continuous(breaks = seq(30,70, by=5)) +
  scale_x_continuous(breaks = seq(30,70, by=5)) +
  geom_abline(mapping = NULL, data = NULL, slope = 1, yintercept = 0, xintercept = 0,
  na.rm = FALSE)
```

    ## Warning: Ignoring unknown parameters: yintercept, xintercept

![](STAT545_hw04_JasmineLib_files/figure-markdown_github/unnamed-chunk-2-1.png)
