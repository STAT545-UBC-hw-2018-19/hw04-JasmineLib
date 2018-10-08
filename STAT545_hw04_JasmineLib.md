STAT545\_hw04\_JasmineLib
================

### STAT 545 Homework 4

Data Reshaping Prompts (and relationship to aggregation)

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
  ggtitle("Life Expectancy in China vs. India") +
  labs(x = "China Life Expectancy (Years)", y = "India Life Expectancy (Years)") +
  scale_y_continuous(breaks=seq(from = 25, to =75, by=5)) +
  scale_x_continuous(breaks=seq(30,70, by=5)) +
  geom_abline(mapping = NULL, data = NULL, slope = 1,
  na.rm = FALSE) +
  theme(aspect.ratio=1)
```

![](STAT545_hw04_JasmineLib_files/figure-markdown_github/unnamed-chunk-2-1.png)

Join Prompts (join, merge, look up)

Activity \#1

Create a second data frame, complementary to Gapminder. Join this with (part of) Gapminder using a dplyr join function and make some observations about the process and result. Explore the different types of joins. Examples of a second data frame you could build:

One row per country, a country variable and one or more variables with extra info, such as language spoken, NATO membership, national animal, or capitol city. One row per continent, a continent variable and one or more variables with extra info, such as northern versus southern hemisphere.

``` r
#Obtained data from: https://www.gapminder.org/data/
numHIV = read_csv(file = "people_living_with_hiv_number_all_ages.csv")
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_integer(),
    ##   geo = col_character(),
    ##   `1988` = col_character(),
    ##   `1989` = col_character()
    ## )

    ## See spec(...) for full column specifications.

``` r
gapminder_sel = gapminder %>% 
  filter (year == "2007") %>% 
  select(country = "country", pop, gdpPercap, continent) #select the population and gdpPercap data for the year 2007. 
  #filter(country =="Canada" | country == "Japan" | country =="India")


gapminder_sel 
```

    ## # A tibble: 142 x 4
    ##    country           pop gdpPercap continent
    ##    <fctr>          <int>     <dbl> <fctr>   
    ##  1 Afghanistan  31889923       975 Asia     
    ##  2 Albania       3600523      5937 Europe   
    ##  3 Algeria      33333216      6223 Africa   
    ##  4 Angola       12420476      4797 Africa   
    ##  5 Argentina    40301927     12779 Americas 
    ##  6 Australia    20434176     34435 Oceania  
    ##  7 Austria       8199783     36126 Europe   
    ##  8 Bahrain        708573     29796 Asia     
    ##  9 Bangladesh  150448339      1391 Asia     
    ## 10 Belgium      10392226     33693 Europe   
    ## # ... with 132 more rows

``` r
numHIV_sel =numHIV %>% 
  select(geo, "2007") #rename the "geo" column to country to make joining easier, and select data for the year 2007. 
  #filter(geo =="Canada" | geo == "Japan" | geo =="India")
 
class(gapminder$country)
```

    ## [1] "factor"

``` r
class(numHIV$geo)
```

    ## [1] "character"

``` r
gapminderHIV = left_join(gapminder_sel, numHIV_sel, by= c("country" = "geo")) #left join columns, join by comparing country and geo columns. 
```

    ## Warning: Column `country`/`geo` joining factor and character vector,
    ## coercing into character vector

``` r
gapminderHIV %>% 
  filter(continent =="Americas") %>% 
  mutate(HIVper10000 = `2007`/pop*10000) %>% 
  ggplot(aes(country, HIVper10000)) + 
  geom_point(na.rm = TRUE) + 
  theme(axis.text.x = element_text(angle = 90, hjust = 1)) + 
  labs(x = "Country", y = "HIV frequency per 10 000 people")
```

![](STAT545_hw04_JasmineLib_files/figure-markdown_github/unnamed-chunk-3-1.png)
