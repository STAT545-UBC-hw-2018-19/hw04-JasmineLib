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

**Section 1**: Make a tibble with one row per year and columns for life expectancy for two or more countries.

``` r
filter_gapminder = gapminder %>% 
  select(year, lifeExp, country) %>% #select specific variables of interest
  filter(country == "China" | country == "India" | country == "Japan" ) %>% #select countries
  spread(key = "country", value = "lifeExp")
```

**Section 2**: Use knitr::kable() to make this table look pretty.

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

**Section 3**: Take advantage of this new data shape to scatterplot life expectancy for one country against that of another.
- I am curious to compare the life expectancy of China and India over time.
- I also plot another line with a slope of 1 to help with visualization & comparison.
- Points to the left of this line signify that India (y-axis) has a higher life expectancy for the year,
- Points on the right of the line signify that China (x-axis) had a higher life expectancy for that year.

``` r
filter_gapminder %>% 
  ggplot (aes(x = China, y= India)) + #plot lifeexp of China and India
  geom_point(aes(colour = year)) + #additional colour visual to show flow of time.
  theme_classic() + 
  geom_text(aes(label = year, vjust = -0.5), size = 3)+ #add year labels for more clarity.
  ggtitle("Life Expectancy in China vs. India") +
  labs(x = "China Life Expectancy (Years)", 
       y = "India Life Expectancy (Years)") +
  coord_cartesian(xlim = c(35, 75), ylim =c(35,75)) + #set same axis ticks on x & y axis.
  geom_abline(mapping = NULL, data = NULL, slope = 1, #add line (see explanation above)
  na.rm = FALSE) +
  theme(aspect.ratio=1) #square plot is ideal for comparison in this case. 
```

![](STAT545_hw04_JasmineLib_files/figure-markdown_github/China%20vs.%20India%20life%20exp-1.png) Conclude:
- Historically, China has had a higher life expectancy than India.
- We also see an interesting change in years 1952-1962, where China's life expectancy went up, then down, while India's increased (albeit, still remaining below that of China)

We can compare China and Japan

``` r
filter_gapminder %>% 
  ggplot (aes(x = China, y= Japan)) + #plot lifeexp of China and Japan
  geom_point(aes(colour = year)) + #additional colour visual to show flow of time.
  theme_classic() + 
  geom_text(aes(label = year, vjust = -0.5), size = 3)+ #add year labels for more clarity.
  ggtitle("Life Expectancy in China vs. Japan") +
  labs(x = "China Life Expectancy (Years)", 
       y = "Japan Life Expectancy (Years)") +
  coord_cartesian(xlim = c(40, 85), ylim =c(40,85)) + #set same axis ticks on x & y axis.
  geom_abline(mapping = NULL, data = NULL, slope = 1, #add line
  na.rm = FALSE) +
  theme(aspect.ratio=1) # a square plot is better to compare
```

![](STAT545_hw04_JasmineLib_files/figure-markdown_github/China%20vs.%20Japan%20Life%20exp-1.png)

Conclude: - Historically, Japan has had a higher life expectancy than China.
- In the years 1952-1962, the Japanese life expectancy increased, while that of China nearly stayed the same.

### Join Prompts (join, merge, look up) Activity \#1

**Section 1:** Create a second data frame, complementary to Gapminder.
- Obtained data from the gapminder website, where you can find plenty of interesting data! [Check it out!](https://www.gapminder.org/data/)
- The file I used is a CSV file named "people\_living\_with\_HIV\_number\_all\_ages.csv"
- You might need to add it to your R environment in order to run the code.

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

knitr::kable(head(numHIV_sel))
```

| geo         |    2007| country     |
|:------------|-------:|:------------|
| Afghanistan |    3600| Afghanistan |
| Angola      |  190000| Angola      |
| Argentina   |   83000| Argentina   |
| Armenia     |    3700| Armenia     |
| Australia   |   18000| Australia   |
| Austria     |   12000| Austria     |

-   The head() function shows me the first few rows of data in the selective HIV dataset numHIV\_sel.

**Section 2**: Join this with (part of) Gapminder using a dplyr join function and make some observations about the process and result.

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

-   The head() function shows me the first few rows in the selective gapminder dataset (gapminder\_sel)
-   I also notice that some of the countries in gapminder\_sel do not correspond to the numHIV\_sel and vice versa (I explore the consequences of this finding later on).

Create a new data frame called gapminder\_HIV which joins the gapminder\_sel and numHIV\_sel:
- I use left join because I want to join the values in matched numHIV\_sel to those of gapminder\_sel.

``` r
gapminderHIV = left_join(gapminder_sel, numHIV_sel, by= "country") #join columns, join by comparing country and geo columns. 
```

    ## Warning: Column `country` joining factor and character vector, coercing
    ## into character vector

``` r
head(gapminderHIV) %>% 
  select("country", `2007`, "geo") %>% 
  knitr::kable()
```

| country     |    2007| geo         |
|:------------|-------:|:------------|
| Afghanistan |    3600| Afghanistan |
| Albania     |      NA| NA          |
| Algeria     |      NA| NA          |
| Angola      |  190000| Angola      |
| Argentina   |   83000| Argentina   |
| Australia   |   18000| Australia   |

-   I use head(gapminderHIV) to ensure that the join worked.
-   I notice that some data is missing, but that's okay for now, we can remove the NAs later on.
-   The missing data is because these countries in gapminder were not found in the HIV dataset.
-   If the HIV dataset were missing a value for the number of people with HIV in that country, then only the `2007` column would contain NA, and not the "geo" column.

Create a graph: - I am interested in seeing the HIV rates in the Americas.
- First, I need to normalize HIV cases using by population using mutate() in order to better represent and be able to compare the data between countries.

``` r
gapminderHIV_america = gapminderHIV %>% 
  filter(continent =="Americas") %>% 
  mutate(HIVper100000 = `2007`/pop*100000) #calculate HIV cases per 100 000 people

gapminderHIV_america %>% 
  select(country, HIVper100000) %>% 
  knitr::kable()
```

| country             |  HIVper100000|
|:--------------------|-------------:|
| Argentina           |     205.94549|
| Bolivia             |     219.31864|
| Brazil              |     257.88029|
| Canada              |     185.68355|
| Chile               |     300.89518|
| Colombia            |     339.15512|
| Costa Rica          |     186.26551|
| Cuba                |      70.94691|
| Dominican Republic  |     504.31230|
| Ecuador             |     254.44035|
| El Salvador         |     259.37766|
| Guatemala           |     342.00466|
| Haiti               |    1411.29748|
| Honduras            |     547.85273|
| Jamaica             |    1115.05497|
| Mexico              |     147.19291|
| Nicaragua           |      82.81419|
| Panama              |     616.87023|
| Paraguay            |     115.49168|
| Peru                |     240.62976|
| Puerto Rico         |            NA|
| Trinidad and Tobago |    1230.35222|
| United States       |     398.48582|
| Uruguay             |     348.07872|
| Venezuela           |     314.36098|

I can plot this data:

``` r
#make a plot
gapminderHIV_america %>% 
  ggplot(aes(country, HIVper100000)) + 
  geom_point(na.rm = TRUE, colour = "red") + #remove NAs
  theme(axis.text.x = element_text(angle = 90, hjust = 1)) + #use this to write axis text sideways + 
  labs(x = "Country", y = "HIV frequency per 100 000 people") +  
  scale_y_continuous(breaks=seq(from = 0, to =1500, by=200)) 
```

![](STAT545_hw04_JasmineLib_files/figure-markdown_github/gapminder%20HIV%20america%20graph-1.png)

Conclude:
- I can see from the table and graph that Haiti had the highest incidence of HIV per 100 000 people. - Cuba has the lowest incidence of HIV per 100 000 people, followed closely by Nicaragua.

Combining these two datasets also allows us to do other interesting analyses, like, whether there is a link between GDP and HIV incidence per 100 000 people.
Make a quick plot of this for the year 2007:

``` r
gapminderHIV_america %>% 
  ggplot(aes(gdpPercap, HIVper100000)) + 
  geom_point(na.rm = TRUE, colour = "red", alpha = 0.5) + #remove NAs
  labs(x = "GDP per Capita", y = "HIV frequency per 100 000 people") +  
  scale_y_continuous(breaks=seq(from = 0, to =1500, by=200)) +
  theme_linedraw()+
  ggtitle("HIV Frequency vs. GDP per Capita in America") +
  geom_smooth(method = "lm") #add a linear regression line
```

    ## Warning: Removed 1 rows containing non-finite values (stat_smooth).

![](STAT545_hw04_JasmineLib_files/figure-markdown_github/HIV%20Freq%20vs.%20GDP%20per%20Capita-1.png)

-   Interestingly, there doesn't seem to be a real significant trend between the GDP per Capita and HIV incidence.
-   However, there do seem to be a few outlier countries with a lower GDP and high HIV incidence, though this might be due to other variables.

**Section 3**: Explore the different types of joins.

Trying out right\_join:

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

-   I see that in right join, we look for matches of values in the numHIV\_sel (right) rather than in gapminder\_sel (left).
-   This means that rows for countries found in numHIV\_sel, but not in gapminder\_sel, are filled with NAs. In this case pop, gdpPercap, and continent are left blank
-   This was the opposite case when we used left join, where instead the 'geo' and `2007` variables were left blank.

Trying out anti\_join:
- anti\_join tells us what rows in gapminder don't have a match in the HIV dataset:

``` r
gapminderHIV_anti = anti_join(gapminder_sel, numHIV_sel, by= "country")
```

    ## Warning: Column `country` joining factor and character vector, coercing
    ## into character vector

``` r
head(gapminderHIV_anti) %>% 
  select("country") %>% 
  knitr::kable()
```

| country                |
|:-----------------------|
| Albania                |
| Algeria                |
| Bahrain                |
| Bosnia and Herzegovina |
| Congo, Dem. Rep.       |
| Hong Kong, China       |

``` r
nrow(gapminderHIV_anti) 
```

    ## [1] 21

-   From these results, we can see that 21 countries were found in gapminder that did not have a match in the HIV dataset.
-   Some of these countries are shown in the table above using the head() function.

Trying out anti\_join in reverse order: - using anti\_join once again, we can reverse the order of the dataframes in the command to see what rows in the HIV dataset don't have a match in gapminder:

``` r
gapminderHIV_revanti = anti_join(numHIV_sel, gapminder_sel, by = "country" )
```

    ## Warning: Column `country` joining character vector and factor, coercing
    ## into character vector

``` r
  head(gapminderHIV_revanti) %>% 
  select("country", `2007`) %>% 
  knitr::kable()
```

| country    |   2007|
|:-----------|------:|
| Armenia    |   3700|
| Azerbaijan |   5400|
| Bahamas    |   6400|
| Barbados   |   1300|
| Belarus    |  17000|
| Belize     |   4200|

``` r
  nrow(gapminderHIV_revanti)
```

    ## [1] 28

-   We can see that 28 countries are found in the HIV dataset, but not in gapminder. Some of those countries are shown above using the head() function.
