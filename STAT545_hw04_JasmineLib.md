STAT545\_hw04\_JasmineLib
================

STAT 545 Homework 4
-------------------

#### In this assignment I chose the following two activities:

**Data Reshaping Prompt - Activity \#2: ** - Make a tibble with one row per year and columns for life expectancy for two or more countries.
- Use knitr::kable() to make this table look pretty in your rendered homework.
- Take advantage of this new data shape to scatterplot life expectancy for one country against that of another.

**Join Prompt - Activity \#1 ** - Create a second data frame, complementary to Gapminder. - Join this with (part of) Gapminder using a dplyr join function and make some observations about the process and result.
- Explore the different types of joins.

First, Start by loading packages:

``` r
suppressPackageStartupMessages(library(tidyverse)) 
suppressPackageStartupMessages(library(gapminder))
```

### Data Reshaping Prompts (and relationship to aggregation) - Activity \#2

Section 1: Make a tibble with one row per year and columns for life expectancy for two or more countries.

``` r
filter_gapminder = gapminder %>% 
  select (year, lifeExp, country) %>% #select specific variables of interest
  filter (country == "China" | country == "India" | country == "Japan" ) %>% #select countries of interest
   spread(key = "country", value = "lifeExp")

filter_gapminder
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

Section 2: Use knitr::kable() to make this table look pretty in your rendered homework.

``` r
filter_gapminder  %>% 
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

Section 3: Take advantage of this new data shape to scatterplot life expectancy for one country against that of another.
- I am curious to compare the life expectancy of China and India over time.
- I plot another line with a slope of 1, intercept (0,0) to help with visualization.
- Points to the left of this line signify that India (y-axis) has a higher life expectancy for the year,
- points on the right of the line signify that China (x-axis) had a higher life expectancy for that year.

``` r
filter_gapminder %>% 
  ggplot (aes(China, India)) + #plot life expectancy of China and India
  geom_point(aes(colour = year)) + #legend helps show the direction/flow of time
  theme_classic() + 
  ggtitle("Life Expectancy in China vs. India") +
  labs(x = "China Life Expectancy (Years)", y = "India Life Expectancy (Years)") +
  scale_y_continuous(breaks=seq(from = 25, to =75, by=5)) + #fix the axis tick marks
  scale_x_continuous(breaks=seq(25,75, by=5)) +
  geom_abline(mapping = NULL, data = NULL, slope = 1, #add line (see explanation above)
  na.rm = FALSE) +
  theme(aspect.ratio=1) #a square plot is ideal for comparison in this plot. 
```

![](STAT545_hw04_JasmineLib_files/figure-markdown_github/unnamed-chunk-4-1.png)

### Join Prompts (join, merge, look up) Activity \#1

Section 1: Create a second data frame, complementary to Gapminder.

I obtained data from the gapminder website, where you can find plenty of interesting data! [Check it out!](https://www.gapminder.org/data/) The file that I used is a CSV file named "people\_living\_with\_HIV\_number\_all\_ages.csv" You might need to add it to your environment in order to access it.

The data after selection contains the total number of people living with HIV in 2007, for each country.

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
#I only want data from the year 2007. 
numHIV_sel = numHIV %>% 
  select(geo, `2007`) %>% 
  mutate(country = geo) #add a variable called country, to facilitate joining (same variable name)

head(numHIV_sel)
```

    ## # A tibble: 6 x 3
    ##   geo         `2007` country    
    ##   <chr>        <int> <chr>      
    ## 1 Afghanistan   3600 Afghanistan
    ## 2 Angola      190000 Angola     
    ## 3 Argentina    83000 Argentina  
    ## 4 Armenia       3700 Armenia    
    ## 5 Australia    18000 Australia  
    ## 6 Austria      12000 Austria

Section 2: Join this with (part of) Gapminder using a dplyr join function and make some observations about the process and result.

First, I need to filter gapminder to select data only from 2007. - I create a table with the first few rows here to ensure I filtered the variables I want.

``` r
gapminder_sel = gapminder %>% 
  filter (year == "2007") %>% 
  select(country = "country", pop, gdpPercap, continent) #select the population and gdpPercap data for the year 2007. 
  
gapminder_sel %>% 
  head() %>% 
  knitr::kable()
```

| country     |       pop|   gdpPercap| continent |
|:------------|---------:|-----------:|:----------|
| Afghanistan |  31889923|    974.5803| Asia      |
| Albania     |   3600523|   5937.0295| Europe    |
| Algeria     |  33333216|   6223.3675| Africa    |
| Angola      |  12420476|   4797.2313| Africa    |
| Argentina   |  40301927|  12779.3796| Americas  |
| Australia   |  20434176|  34435.3674| Oceania   |

Create a new data frame gapminder HIV which joins the gapminder\_sel and numHIV\_sel: - I use head(gapminderHIV) to ensure that the join worked. - I notice that some data is missing, but that's okay, we can remove the NAs later on.

``` r
gapminderHIV = left_join(gapminder_sel, numHIV_sel, by= "country") # join columns, join by comparing country and geo columns. 
```

    ## Warning: Column `country` joining factor and character vector, coercing
    ## into character vector

``` r
gapminderHIV
```

    ## # A tibble: 142 x 6
    ##    country           pop gdpPercap continent geo         `2007`
    ##    <chr>           <int>     <dbl> <fctr>    <chr>        <int>
    ##  1 Afghanistan  31889923       975 Asia      Afghanistan   3600
    ##  2 Albania       3600523      5937 Europe    <NA>            NA
    ##  3 Algeria      33333216      6223 Africa    <NA>            NA
    ##  4 Angola       12420476      4797 Africa    Angola      190000
    ##  5 Argentina    40301927     12779 Americas  Argentina    83000
    ##  6 Australia    20434176     34435 Oceania   Australia    18000
    ##  7 Austria       8199783     36126 Europe    Austria      12000
    ##  8 Bahrain        708573     29796 Asia      <NA>            NA
    ##  9 Bangladesh  150448339      1391 Asia      Bangladesh    4800
    ## 10 Belgium      10392226     33693 Europe    Belgium      16000
    ## # ... with 132 more rows

``` r
head(gapminderHIV) %>% 
  knitr::kable()
```

| country     |       pop|   gdpPercap| continent | geo         |    2007|
|:------------|---------:|-----------:|:----------|:------------|-------:|
| Afghanistan |  31889923|    974.5803| Asia      | Afghanistan |    3600|
| Albania     |   3600523|   5937.0295| Europe    | NA          |      NA|
| Algeria     |  33333216|   6223.3675| Africa    | NA          |      NA|
| Angola      |  12420476|   4797.2313| Africa    | Angola      |  190000|
| Argentina   |  40301927|  12779.3796| Americas  | Argentina   |   83000|
| Australia   |  20434176|  34435.3674| Oceania   | Australia   |   18000|

Create a graph: - I am interested in HIV rates in the Americas. - I want to normalize HIV cases using by population using mutate() in order to better represent the data.

``` r
gapminderHIV_america = gapminderHIV %>% 
  filter(continent =="Americas") %>% 
  mutate(HIVper100000 = `2007`/pop*100000) #calculate HIV cases per 100 000 people

knitr::kable(gapminderHIV_america)
```

| country             |        pop|  gdpPercap| continent | geo                 |     2007|  HIVper100000|
|:--------------------|----------:|----------:|:----------|:--------------------|--------:|-------------:|
| Argentina           |   40301927|  12779.380| Americas  | Argentina           |    83000|     205.94549|
| Bolivia             |    9119152|   3822.137| Americas  | Bolivia             |    20000|     219.31864|
| Brazil              |  190010647|   9065.801| Americas  | Brazil              |   490000|     257.88029|
| Canada              |   33390141|  36319.235| Americas  | Canada              |    62000|     185.68355|
| Chile               |   16284741|  13171.639| Americas  | Chile               |    49000|     300.89518|
| Colombia            |   44227550|   7006.580| Americas  | Colombia            |   150000|     339.15512|
| Costa Rica          |    4133884|   9645.061| Americas  | Costa Rica          |     7700|     186.26551|
| Cuba                |   11416987|   8948.103| Americas  | Cuba                |     8100|      70.94691|
| Dominican Republic  |    9319622|   6025.375| Americas  | Dominican Republic  |    47000|     504.31230|
| Ecuador             |   13755680|   6873.262| Americas  | Ecuador             |    35000|     254.44035|
| El Salvador         |    6939688|   5728.354| Americas  | El Salvador         |    18000|     259.37766|
| Guatemala           |   12572928|   5186.050| Americas  | Guatemala           |    43000|     342.00466|
| Haiti               |    8502814|   1201.637| Americas  | Haiti               |   120000|    1411.29748|
| Honduras            |    7483763|   3548.331| Americas  | Honduras            |    41000|     547.85273|
| Jamaica             |    2780132|   7320.880| Americas  | Jamaica             |    31000|    1115.05497|
| Mexico              |  108700891|  11977.575| Americas  | Mexico              |   160000|     147.19291|
| Nicaragua           |    5675356|   2749.321| Americas  | Nicaragua           |     4700|      82.81419|
| Panama              |    3242173|   9809.186| Americas  | Panama              |    20000|     616.87023|
| Paraguay            |    6667147|   4172.838| Americas  | Paraguay            |     7700|     115.49168|
| Peru                |   28674757|   7408.906| Americas  | Peru                |    69000|     240.62976|
| Puerto Rico         |    3942491|  19328.709| Americas  | NA                  |       NA|            NA|
| Trinidad and Tobago |    1056608|  18008.509| Americas  | Trinidad and Tobago |    13000|    1230.35222|
| United States       |  301139947|  42951.653| Americas  | United States       |  1200000|     398.48582|
| Uruguay             |    3447496|  10611.463| Americas  | Uruguay             |    12000|     348.07872|
| Venezuela           |   26084662|  11415.806| Americas  | Venezuela           |    82000|     314.36098|

``` r
#make a plot
gapminderHIV_america %>% 
  ggplot(aes(country, HIVper100000)) + 
  geom_point(na.rm = TRUE) + #remove NAs
  theme(axis.text.x = element_text(angle = 90, hjust = 1)) + #use this to write axis text sideways + 
  labs(x = "Country", y = "HIV frequency per 100 000 people") +  
  scale_y_continuous(breaks=seq(from = 0, to =1500, by=200)) 
```

![](STAT545_hw04_JasmineLib_files/figure-markdown_github/unnamed-chunk-8-1.png)

Section 3: Explore the different types of joins. Right\_join:

``` r
gapminderHIV_right = right_join(gapminder_sel, numHIV_sel, by= "country")
```

    ## Warning: Column `country` joining factor and character vector, coercing
    ## into character vector

``` r
head(gapminderHIV_right) %>% 
  knitr::kable()
```

| country     |       pop|   gdpPercap| continent | geo         |    2007|
|:------------|---------:|-----------:|:----------|:------------|-------:|
| Afghanistan |  31889923|    974.5803| Asia      | Afghanistan |    3600|
| Angola      |  12420476|   4797.2313| Africa    | Angola      |  190000|
| Argentina   |  40301927|  12779.3796| Americas  | Argentina   |   83000|
| Armenia     |        NA|          NA| NA        | Armenia     |    3700|
| Australia   |  20434176|  34435.3674| Oceania   | Australia   |   18000|
| Austria     |   8199783|  36126.4927| Europe    | Austria     |   12000|

anti\_join: tells us what rows in gapminder don't have a match in the HIV dataset:

``` r
gapminderHIV_anti = anti_join(gapminder_sel, numHIV_sel, by= "country")
```

    ## Warning: Column `country` joining factor and character vector, coercing
    ## into character vector

``` r
head(gapminderHIV_anti) %>% 
  knitr::kable()
```

| country                |       pop|   gdpPercap| continent |
|:-----------------------|---------:|-----------:|:----------|
| Albania                |   3600523|   5937.0295| Europe    |
| Algeria                |  33333216|   6223.3675| Africa    |
| Bahrain                |    708573|  29796.0483| Asia      |
| Bosnia and Herzegovina |   4552198|   7446.2988| Europe    |
| Congo, Dem. Rep.       |  64606759|    277.5519| Africa    |
| Hong Kong, China       |   6980412|  39724.9787| Asia      |

``` r
nrow(gapminderHIV_anti) 
```

    ## [1] 21

From these results, we can see that 21 countries were found in gapminder that did not have a match in the HIV dataset. Some of the countries are shown in the table above using the head() function.

using anti\_join once again, we can reverse the order of the dataframes in the command to see what rows in the HIV dataset don't have a match in gapminder:

``` r
gapminderHIV_revanti = anti_join(numHIV_sel, gapminder_sel, by = "country" )
```

    ## Warning: Column `country` joining character vector and factor, coercing
    ## into character vector

``` r
  head(gapminderHIV_revanti) %>% 
  knitr::kable()
```

| geo        |   2007| country    |
|:-----------|------:|:-----------|
| Armenia    |   3700| Armenia    |
| Azerbaijan |   5400| Azerbaijan |
| Bahamas    |   6400| Bahamas    |
| Barbados   |   1300| Barbados   |
| Belarus    |  17000| Belarus    |
| Belize     |   4200| Belize     |

``` r
  nrow(gapminderHIV_revanti)
```

    ## [1] 28

We can see that 28 countries are found in the HIV dataset, but not in gapminder. Some of those countries are shown above using the head() function.
