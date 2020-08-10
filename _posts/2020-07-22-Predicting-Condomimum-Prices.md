---
title: "Predicting House Prices Project"
date: 2020-07-22
tags: [machine learning, missing data, NYC Condomimum]
excerpt: "Predicting house prices in NYC"
mathjax: "true"
---
How well does the size of a Condominium in New York City Explain Sale Price?
============================================================================

This project will explore the following questions:

1.  How well does the size of a property (measured in gross square feet)
    explain or predict sale price across New York City as a whole?
2.  How well does the size of a property explain or predict sale price
    for each individual borough?

To answer question 1 we will explore property data for all five boroughs

To answer question 2 we will build, analyze and compare linear models
for each borough

Understanding the data
======================

The data that we are using from this project come from five separate
excel files (one for each borough) and can be obtained from
[here](https://www1.nyc.gov/site/finance/taxes/property-rolling-sales-data.page)

The data is for June 2019-May 2020

``` r
library(readxl)
```

    ## Warning: package 'readxl' was built under R version 4.0.2

``` r
library(readr)
library(stringr)
```

    ## Warning: package 'stringr' was built under R version 4.0.2

``` r
library(dplyr)
library(tidyr)
library(magrittr)
```

    ## Warning: package 'magrittr' was built under R version 4.0.2

``` r
library(ggplot2)
```

    ## Warning: package 'ggplot2' was built under R version 4.0.2

``` r
# loading in the data from each excel file setting skip = 4 to avoid header rows
queens <- read_excel("rollingsales_queens.xls", skip = 4)
staten_island <- read_excel("rollingsales_statenisland.xls", skip = 4)
brooklyn <- read_excel("rollingsales_brooklyn.xls", skip = 4)
bronx <- read_excel("rollingsales_bronx.xls", skip = 4)
manhattan <- read_excel("rollingsales_manhattan.xls", skip = 4)
```

Each Borough in New York has a specific number which we can get from the
`BOROUGH` variable in each dataframe

``` r
head(queens$BOROUGH, 1)
```

    ## [1] "4"

``` r
head(staten_island$BOROUGH, 1)
```

    ## [1] "5"

``` r
head(brooklyn$BOROUGH, 1)
```

    ## [1] "3"

``` r
head(bronx$BOROUGH, 1)
```

    ## [1] "2"

``` r
head(manhattan$BOROUGH, 1)
```

    ## [1] "1"

``` r
# The Borough numbers are as follows:
# Manhattan = 1
# Bronx = 2
# Brooklyn = 3
# Queens = 4
# Staten Island = 5
```

We will now bind the five dataframes into a single dataframe

``` r
NYC_property_sales <- bind_rows(manhattan, bronx, brooklyn, queens, staten_island)
```

Since we have a single dataframe with all NYC property sales, we don’t
need the 5 individual dataframes and can remove then using the `rm`
function

``` r
rm(brooklyn, bronx, manhattan, queens, staten_island)
```

Since we have the numbers for each borough we can replace them with the
borough names so that it is more clear

``` r
NYC_property_sales <- NYC_property_sales %>% mutate(BOROUGH = 
                                                      case_when(BOROUGH == 1 ~ "Manhattan",
                                                                BOROUGH == 2 ~ "Bronx",
                                                                BOROUGH == 3 ~ "Brooklyn",
                                                                BOROUGH == 4 ~ "Queens",
                                                                BOROUGH == 5 ~ "Staten Island"))
```

Now that we have our data in a single dataframe with the `BOROUGH`
column taken care of there are a few more steps to get this into the
format that we need

``` r
# Convert all column names to lowercase and replace spaces with underscores
colnames(NYC_property_sales) %<>% str_replace_all("\\s", "_") %>% tolower()

# Convert CAPTIALIZED case to Title case (the NEIGHBORHOOD,BUILDING CLASS CATEGORY and ADDRESS columns)
NYC_property_sales <- NYC_property_sales %>% mutate(neighborhood = str_to_title(neighborhood)) %>% mutate(building_class_category = str_to_title(building_class_category)) %>% mutate(address = str_to_title(address))
```

``` r
# Drop the ease-ment column as it has no data and only select distinct values
NYC_property_sales <- NYC_property_sales %>% select(-`ease-ment`) %>% distinct()

# Filter out property exchanges between family members with a threshold of 10,000
NYC_property_sales <- NYC_property_sales %>% filter(sale_price > 10000) %>%
# Remove properties that have gross_square_feet of 0 
filter(gross_square_feet > 0) %>% 
# Remove NA values in both sale_price and gross_square_feet
drop_na(c(gross_square_feet, sale_price))

# Arrange by borough and neighborhood
NYC_property_sales <- NYC_property_sales %>% arrange(borough, neighborhood)
```

We will save it to a csv file

``` r
write_csv(NYC_property_sales, "NYC_property_sales.csv")
```

As I found out for condominums `gross_square_feet` isn’t being collected
for condominiums (this uses the latest data obtained on July 20th 2020)
so we need to find out what building types we can use

``` r
sort(table(NYC_property_sales$building_class_at_present))
```

    ## 
    ##   GW   H7   H8   H9   I3   J2   M4   P7   Q2   Q8   W1   W6   Z3   Z5   C6   G3 
    ##    1    1    1    1    1    1    1    1    1    1    1    1    1    1    2    2 
    ##   G4   H4   HB   I1   I7   I9   J9   K3   K8   P5   P6   Q1   Q9   D8   G5   G8 
    ##    2    2    2    2    2    2    2    2    2    2    2    2    2    3    3    3 
    ##   H2   N2   S0   W3   W7   W8   D5   F2   P2   O3   P9   C9   GU   I6   M3   W9 
    ##    3    3    3    3    3    3    4    4    4    5    5    6    6    6    6    6 
    ##   K6   N9   A7   W2   H1   H3   K5   O9   E7   RR   E2   M9   D9   F1   I5   K7 
    ##    7    7    8    8    9    9   10   10   11   11   12   13   14   15   16   16 
    ##   O1   D3   F4   G9   D6   F9   D7   O4   O8   O6   O5   O2   A6   O7   E9   F5 
    ##   16   17   20   20   21   21   23   28   30   31   32   34   35   35   43   44 
    ##   M1   S4   S5   G2   G1   S3   Z9   S9   C5   D1   C4   K2   E1   A4   C7   K4 
    ##   45   48   50   52   53   56   56   61   66   70   71   85  100  115  122  138 
    ##   S1   K1   A3   C1   C2   A0   S2   C3   A9   B9   A2   C0   B3   B1   B2   A5 
    ##  139  173  207  222  230  272  323  363  707  766 1501 1714 1986 2321 2521 3406 
    ##   A1 
    ## 4070

The most listed is type A1 which according to the building codes is
this: TWO STORIES - DETACHED SM OR MID

According to further research found
[here](https://www.propertyshark.com/info/class-buildings-one-family-dwellings/#a1)
it is a single familiy home and thus we will need to include
land\_square\_feet

``` r
NYC_property_sales <- NYC_property_sales %>% filter(land_square_feet > 0) %>% drop_na(land_square_feet)
```

We will now filter according to A1

``` r
NYC_sm_md_two_story <- NYC_property_sales %>% filter(building_class_at_time_of_sale == "A1")
head(NYC_sm_md_two_story)
```

    ## # A tibble: 6 x 20
    ##   borough neighborhood building_class_~ tax_class_at_pr~ block   lot
    ##   <chr>   <chr>        <chr>            <chr>            <dbl> <dbl>
    ## 1 Bronx   Bathgate     01 One Family D~ 1                 3030    65
    ## 2 Bronx   Bathgate     01 One Family D~ 1                 3030    67
    ## 3 Bronx   Bathgate     01 One Family D~ 1                 3030    70
    ## 4 Bronx   Baychester   01 One Family D~ 1                 4723    59
    ## 5 Bronx   Baychester   01 One Family D~ 1                 4724    21
    ## 6 Bronx   Baychester   01 One Family D~ 1                 4745    23
    ## # ... with 14 more variables: building_class_at_present <chr>, address <chr>,
    ## #   apartment_number <chr>, zip_code <dbl>, residential_units <dbl>,
    ## #   commercial_units <dbl>, total_units <dbl>, land_square_feet <dbl>,
    ## #   gross_square_feet <dbl>, year_built <dbl>, tax_class_at_time_of_sale <chr>,
    ## #   building_class_at_time_of_sale <chr>, sale_price <dbl>, sale_date <dttm>

Explore Bivarate Relationships with Scatterplots
================================================

``` r
ggplot(data = NYC_sm_md_two_story,
       aes(x = gross_square_feet, y = sale_price)) +
  geom_point(aes(color = borough), alpha = 0.3) +
  scale_y_continuous(labels = scales::comma, limits = c(0,10000000)) +
  xlim(0, 10000) +
  geom_smooth(method = "lm", se = FALSE) +
  theme_minimal() +
  labs(title = "Single Family Home Sale Price increases with Size",
       x = "Size (Gross Square Feet)",
       y = "Sale Price (USD)")
```

    ## `geom_smooth()` using formula 'y ~ x'

![](/images/Project_Notebook_files/figure-markdown_github/unnamed-chunk-14-1.png)

Let’s adjust our y limits by looking at the maximum sale price

``` r
max(NYC_sm_md_two_story$sale_price)
```

    ## [1] 9e+06

We see in general as gross square feet increases sale price increases

Let’s look at land square feet to see if that holds true

``` r
ggplot(data = NYC_sm_md_two_story,
       aes(x = land_square_feet, y = sale_price)) +
  geom_point(aes(color = borough), alpha = 0.3) +
  scale_y_continuous(labels = scales::comma, limits = c(0,10000000)) +
  xlim(0, 10000) +
  geom_smooth(method = "lm", se = FALSE) +
  theme_minimal() +
  labs(title = "Single Family Home Sale Price increases with Size",
       x = "Land Size (Square Feet)",
       y = "Sale Price (USD)")
```

    ## `geom_smooth()` using formula 'y ~ x'

    ## Warning: Removed 66 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 66 rows containing missing values (geom_point).

![](/images/Project_Notebook_files/figure-markdown_github/unnamed-chunk-16-1.png)

We see this also holds up for land square feet (which is included in
gross square feet as homes include the square footage of the house as
well as land)

We will also zoom in on a smaller subset of data with a max sale price
of $2,500,000 and a max gross square feet of 5000

``` r
ggplot(data = NYC_sm_md_two_story,
       aes(x = gross_square_feet, y = sale_price)) +
  geom_point(aes(color = borough), alpha = 0.3) +
  scale_y_continuous(labels = scales::comma, limits = c(0,2500000)) +
  xlim(0, 5000) +
  geom_smooth(method = "lm", se = FALSE) +
  theme_minimal() +
  labs(title = "Single Family Home Sale Price increases with Size",
       x = "Size (Gross Square Feet)",
       y = "Sale Price (USD)")
```

    ## `geom_smooth()` using formula 'y ~ x'

    ## Warning: Removed 21 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 21 rows containing missing values (geom_point).

![](/images/Project_Notebook_files/figure-markdown_github/unnamed-chunk-17-1.png)

Zooming in also shows for single family homes as the size increases so
does the price

Looking at land values

``` r
ggplot(data = NYC_sm_md_two_story,
       aes(x = land_square_feet, y = sale_price)) +
  geom_point(aes(color = borough), alpha = 0.3) +
  scale_y_continuous(labels = scales::comma, limits = c(0,2500000)) +
  xlim(0, 5000) +
  geom_smooth(method = "lm", se = FALSE) +
  theme_minimal() +
  labs(title = "Single Family Home Sale Price increases with Size",
       x = "Land Size (Square Feet)",
       y = "Sale Price (USD)")
```

    ## `geom_smooth()` using formula 'y ~ x'

    ## Warning: Removed 516 rows containing non-finite values (stat_smooth).

    ## Warning: Removed 516 rows containing missing values (geom_point).

![](Project_Notebook_files/figure-markdown_github/unnamed-chunk-18-1.png)

This trend also shows that as land size increases the sale price also
increaes

However, these graphs are cluttered in that all five boroughs are shown.

``` r
ggplot(data = NYC_sm_md_two_story,
       aes(x = gross_square_feet, y = sale_price)) +
  geom_point(alpha = 0.3) +
  facet_wrap(~ borough, scales = "free", ncol = 2) +
  scale_y_continuous(labels = scales::comma) +
  geom_smooth(method = "lm", se = FALSE) +
  theme_minimal() +
  labs(title = "Single Family Home Sale Price increases with Size",
       x = "Size (Gross Square Feet)",
       y = "Sale Price (USD)")
```

    ## `geom_smooth()` using formula 'y ~ x'

![](/images/Project_Notebook_files/figure-markdown_github/unnamed-chunk-19-1.png)

For each borough we see that homes with larger gross square feet have
higher sale prices

Outliers and Data Integrity Issues
==================================

To see any outliers we first need to sort sale prices by highest to
lowest

``` r
NYC_sm_md_two_story %>% arrange(desc(sale_price)) %>% head()
```

    ## # A tibble: 6 x 20
    ##   borough neighborhood building_class_~ tax_class_at_pr~ block   lot
    ##   <chr>   <chr>        <chr>            <chr>            <dbl> <dbl>
    ## 1 Brookl~ Ocean Parkw~ 01 One Family D~ 1                 6520     1
    ## 2 Brookl~ Ocean Parkw~ 01 One Family D~ 1                 6519    27
    ## 3 Brookl~ Borough Park 01 One Family D~ 1                 5466    40
    ## 4 Brookl~ Bay Ridge    01 One Family D~ 1                 6133    56
    ## 5 Queens  Whitestone   01 One Family D~ 1                 4416   215
    ## 6 Brookl~ Boerum Hill  01 One Family D~ 1                  196     4
    ## # ... with 14 more variables: building_class_at_present <chr>, address <chr>,
    ## #   apartment_number <chr>, zip_code <dbl>, residential_units <dbl>,
    ## #   commercial_units <dbl>, total_units <dbl>, land_square_feet <dbl>,
    ## #   gross_square_feet <dbl>, year_built <dbl>, tax_class_at_time_of_sale <chr>,
    ## #   building_class_at_time_of_sale <chr>, sale_price <dbl>, sale_date <dttm>

5 of the top sellers are in Brooklyn with one in Queens. The highest
price of $9,000,000 was mostly land (looking on realtor.com doesn’t show
a house but zillow.com does show a house there)

``` r
NYC_sm_md_two_story %>% filter(borough == "Brooklyn") %>% arrange(desc(gross_square_feet)) %>% head()
```

    ## # A tibble: 6 x 20
    ##   borough neighborhood building_class_~ tax_class_at_pr~ block   lot
    ##   <chr>   <chr>        <chr>            <chr>            <dbl> <dbl>
    ## 1 Brookl~ Bay Ridge    01 One Family D~ 1                 6133    57
    ## 2 Brookl~ Borough Park 01 One Family D~ 1                 5466    40
    ## 3 Brookl~ Ocean Parkw~ 01 One Family D~ 1                 6520     1
    ## 4 Brookl~ Ocean Parkw~ 01 One Family D~ 1                 6519    27
    ## 5 Brookl~ Flatbush-Ce~ 01 One Family D~ 1                 5093    22
    ## 6 Brookl~ Gerritsen B~ 01 One Family D~ 1                 8907   634
    ## # ... with 14 more variables: building_class_at_present <chr>, address <chr>,
    ## #   apartment_number <chr>, zip_code <dbl>, residential_units <dbl>,
    ## #   commercial_units <dbl>, total_units <dbl>, land_square_feet <dbl>,
    ## #   gross_square_feet <dbl>, year_built <dbl>, tax_class_at_time_of_sale <chr>,
    ## #   building_class_at_time_of_sale <chr>, sale_price <dbl>, sale_date <dttm>

The largest gross square foot property was converted from a 3 familiy
apartment to a single family home

``` r
NYC_sm_md_two_story %>% arrange(desc(gross_square_feet)) %>% head()
```

    ## # A tibble: 6 x 20
    ##   borough neighborhood building_class_~ tax_class_at_pr~ block   lot
    ##   <chr>   <chr>        <chr>            <chr>            <dbl> <dbl>
    ## 1 Bronx   Riverdale    01 One Family D~ 1                 5944   150
    ## 2 Staten~ Pleasant Pl~ 01 One Family D~ 1                 7710   310
    ## 3 Bronx   Bedford Par~ 01 One Family D~ 1                 3311    29
    ## 4 Brookl~ Bay Ridge    01 One Family D~ 1                 6133    57
    ## 5 Brookl~ Borough Park 01 One Family D~ 1                 5466    40
    ## 6 Queens  Holliswood   01 One Family D~ 1                10501    31
    ## # ... with 14 more variables: building_class_at_present <chr>, address <chr>,
    ## #   apartment_number <chr>, zip_code <dbl>, residential_units <dbl>,
    ## #   commercial_units <dbl>, total_units <dbl>, land_square_feet <dbl>,
    ## #   gross_square_feet <dbl>, year_built <dbl>, tax_class_at_time_of_sale <chr>,
    ## #   building_class_at_time_of_sale <chr>, sale_price <dbl>, sale_date <dttm>

However looking at gross square feet here shows a different story but
then it depends on the borough the property is in.

We will not remove the outlier for now as we want to confirm the sale is
legitimate

Linear Regression Model for Boroughs in New York City Combined
==============================================================

We will now generate a linear model using `sale_price` explained by
`gross_square_feet` and `land_square_feet`

``` r
NYC_sm_md_two_story_lm <- lm(sale_price ~ gross_square_feet + land_square_feet, data = NYC_sm_md_two_story)

summary(NYC_sm_md_two_story_lm)
```

    ## 
    ## Call:
    ## lm(formula = sale_price ~ gross_square_feet + land_square_feet, 
    ##     data = NYC_sm_md_two_story)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -1491507  -157622   -24579   108987  6836803 
    ## 
    ## Coefficients:
    ##                    Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)       1.346e+05  1.429e+04   9.420   <2e-16 ***
    ## gross_square_feet 3.272e+02  9.367e+00  34.932   <2e-16 ***
    ## land_square_feet  6.489e+00  2.563e+00   2.532   0.0114 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 321300 on 4076 degrees of freedom
    ## Multiple R-squared:  0.2975, Adjusted R-squared:  0.2972 
    ## F-statistic: 863.2 on 2 and 4076 DF,  p-value: < 2.2e-16

If we remove the highest listing

``` r
NYC_sm_md_two_story_modified <- NYC_sm_md_two_story %>% filter(address != "704 Avenue I")
```

``` r
NYC_sm_md_two_story_modified_lm <- lm(sale_price ~ gross_square_feet + land_square_feet, data = NYC_sm_md_two_story_modified)

summary(NYC_sm_md_two_story_modified_lm)
```

    ## 
    ## Call:
    ## lm(formula = sale_price ~ gross_square_feet + land_square_feet, 
    ##     data = NYC_sm_md_two_story_modified)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -1369545  -155512   -26950   106644  3692512 
    ## 
    ## Coefficients:
    ##                    Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)       1.635e+05  1.352e+04  12.087  < 2e-16 ***
    ## gross_square_feet 3.027e+02  8.888e+00  34.061  < 2e-16 ***
    ## land_square_feet  8.960e+00  2.416e+00   3.708 0.000212 ***
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 302600 on 4075 degrees of freedom
    ## Multiple R-squared:  0.2955, Adjusted R-squared:  0.2951 
    ## F-statistic: 854.6 on 2 and 4075 DF,  p-value: < 2.2e-16

Removing the highest listing caused a reduction in the estimate for
gross\_square\_feet but increased land\_square\_feet. For standard error
it was reduced for both gross\_square\_feet and land\_square\_feet

Replotting using facet wrap without the outlier

``` r
ggplot(data = NYC_sm_md_two_story_modified,
       aes(x = gross_square_feet, y = sale_price)) +
  geom_point(alpha = 0.3) +
  facet_wrap(~ borough, scales = "free", ncol = 2) +
  scale_y_continuous(labels = scales::comma) +
  geom_smooth(method = "lm", se = FALSE) +
  theme_minimal() +
  labs(title = "Single Family Home Sale Price increases with Size",
       x = "Size (Gross Square Feet)",
       y = "Sale Price (USD)")
```

    ## `geom_smooth()` using formula 'y ~ x'

![](/images/Project_Notebook_files/figure-markdown_github/unnamed-chunk-26-1.png)

Linear regression model for each borough - Coefficient Estimates
================================================================

We have a linear model for all of the boroughs but would be more useful
is if we had a model for each one. We will use the dataset that contains
the outlier as that is still a sale (and was confirmed through
realtor.com)

We will use the `broom` workflow which is the following:

1.  Nest a dataframe by a categorical variable with the `nest()`
    function from the `tidyr` package - we will nest by `borough`.
2.  Fit models to nested dataframes with the `map()` function from the
    `purrr` package.
3.  Apply the `broom` functions `tidy()`, `augment()`, and/or `glance()`
    using each nested model - we’ll work with `tidy()` first.
4.  Return a tidy dataframe with the `unnest()` function - this allows
    us to see the results.

``` r
library(broom)
library(tidyr)
library(purrr)
```

    ## 
    ## Attaching package: 'purrr'

    ## The following object is masked from 'package:magrittr':
    ## 
    ##     set_names

``` r
NYC_sm_md_two_story_nested <- NYC_sm_md_two_story %>% group_by(borough) %>% nest()
print(NYC_sm_md_two_story_nested)
```

    ## # A tibble: 5 x 2
    ## # Groups:   borough [5]
    ##   borough       data                 
    ##   <chr>         <list>               
    ## 1 Bronx         <tibble [336 x 19]>  
    ## 2 Brooklyn      <tibble [497 x 19]>  
    ## 3 Manhattan     <tibble [1 x 19]>    
    ## 4 Queens        <tibble [2,463 x 19]>
    ## 5 Staten Island <tibble [782 x 19]>

Looking at Manhattan we have

``` r
head(NYC_sm_md_two_story_nested$data[[3]])
```

    ## # A tibble: 1 x 19
    ##   neighborhood building_class_~ tax_class_at_pr~ block   lot building_class_~
    ##   <chr>        <chr>            <chr>            <dbl> <dbl> <chr>           
    ## 1 Inwood       01 One Family D~ 1                 2215   453 A1              
    ## # ... with 13 more variables: address <chr>, apartment_number <chr>,
    ## #   zip_code <dbl>, residential_units <dbl>, commercial_units <dbl>,
    ## #   total_units <dbl>, land_square_feet <dbl>, gross_square_feet <dbl>,
    ## #   year_built <dbl>, tax_class_at_time_of_sale <chr>,
    ## #   building_class_at_time_of_sale <chr>, sale_price <dbl>, sale_date <dttm>

We will now fit a linear model to each dataframe (borough)

``` r
NYC_sm_md_two_story_coefficients <- NYC_sm_md_two_story %>% group_by(borough) %>% nest() %>%
  mutate(linear_model = map(.x = data,
                            .f = ~lm(sale_price ~ gross_square_feet + land_square_feet, data = .)
                            ))
```

``` r
print(NYC_sm_md_two_story_coefficients)
```

    ## # A tibble: 5 x 3
    ## # Groups:   borough [5]
    ##   borough       data                  linear_model
    ##   <chr>         <list>                <list>      
    ## 1 Bronx         <tibble [336 x 19]>   <lm>        
    ## 2 Brooklyn      <tibble [497 x 19]>   <lm>        
    ## 3 Manhattan     <tibble [1 x 19]>     <lm>        
    ## 4 Queens        <tibble [2,463 x 19]> <lm>        
    ## 5 Staten Island <tibble [782 x 19]>   <lm>

Looking at the summary for Manhattan

``` r
summary(NYC_sm_md_two_story_coefficients$linear_model[[3]])
```

    ## 
    ## Call:
    ## lm(formula = sale_price ~ gross_square_feet + land_square_feet, 
    ##     data = .)
    ## 
    ## Residuals:
    ## ALL 1 residuals are 0: no residual degrees of freedom!
    ## 
    ## Coefficients: (2 not defined because of singularities)
    ##                   Estimate Std. Error t value Pr(>|t|)
    ## (Intercept)         649000         NA      NA       NA
    ## gross_square_feet       NA         NA      NA       NA
    ## land_square_feet        NA         NA      NA       NA
    ## 
    ## Residual standard error: NaN on 0 degrees of freedom

That is because there is only a single entry for Manhattan looking at
Brooklyn

``` r
summary(NYC_sm_md_two_story_coefficients$linear_model[[2]])
```

    ## 
    ## Call:
    ## lm(formula = sale_price ~ gross_square_feet + land_square_feet, 
    ##     data = .)
    ## 
    ## Residuals:
    ##      Min       1Q   Median       3Q      Max 
    ## -1514579  -244635   -31472   187012  5551040 
    ## 
    ## Coefficients:
    ##                     Estimate Std. Error t value Pr(>|t|)    
    ## (Intercept)       -108124.95   67684.87  -1.597   0.1108    
    ## gross_square_feet     519.06      39.73  13.063   <2e-16 ***
    ## land_square_feet       66.31      26.35   2.516   0.0122 *  
    ## ---
    ## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
    ## 
    ## Residual standard error: 516800 on 494 degrees of freedom
    ## Multiple R-squared:  0.4289, Adjusted R-squared:  0.4265 
    ## F-statistic: 185.5 on 2 and 494 DF,  p-value: < 2.2e-16

Transforming this dataframe into a tidy format

``` r
NYC_sm_md_two_story_coefficients <- NYC_sm_md_two_story %>% group_by(borough) %>% nest() %>%
  mutate(linear_model = map(.x = data,
                            .f = ~lm(sale_price ~ gross_square_feet + land_square_feet, data = .)
                            )) %>%
  mutate(tidy_coefficients = map(.x = linear_model,
                                 .f = tidy,
                                 conf.int = TRUE))
```

    ## Warning in qt(a, object$df.residual): NaNs produced

``` r
NYC_sm_md_two_story_coefficients
```

    ## # A tibble: 5 x 4
    ## # Groups:   borough [5]
    ##   borough       data                  linear_model tidy_coefficients
    ##   <chr>         <list>                <list>       <list>           
    ## 1 Bronx         <tibble [336 x 19]>   <lm>         <tibble [3 x 7]> 
    ## 2 Brooklyn      <tibble [497 x 19]>   <lm>         <tibble [3 x 7]> 
    ## 3 Manhattan     <tibble [1 x 19]>     <lm>         <tibble [1 x 7]> 
    ## 4 Queens        <tibble [2,463 x 19]> <lm>         <tibble [3 x 7]> 
    ## 5 Staten Island <tibble [782 x 19]>   <lm>         <tibble [3 x 7]>

First inspect for Brooklyn

``` r
print(NYC_sm_md_two_story_coefficients$tidy_coefficients[[2]])
```

    ## # A tibble: 3 x 7
    ##   term               estimate std.error statistic  p.value  conf.low conf.high
    ##   <chr>                 <dbl>     <dbl>     <dbl>    <dbl>     <dbl>     <dbl>
    ## 1 (Intercept)       -108125.    67685.      -1.60 1.11e- 1 -241111.     24861.
    ## 2 gross_square_feet     519.       39.7     13.1  1.04e-33     441.       597.
    ## 3 land_square_feet       66.3      26.4      2.52 1.22e- 2      14.5      118.

And let’s get a tidy dataframe out using `unest()`

``` r
NYC_sm_md_two_story_tidy <- NYC_sm_md_two_story_coefficients %>% 
  select(borough, tidy_coefficients) %>% 
  unnest(cols = tidy_coefficients)

print(NYC_sm_md_two_story_tidy)
```

    ## # A tibble: 13 x 8
    ## # Groups:   borough [5]
    ##    borough   term     estimate std.error statistic    p.value conf.low conf.high
    ##    <chr>     <chr>       <dbl>     <dbl>     <dbl>      <dbl>    <dbl>     <dbl>
    ##  1 Bronx     (Interc~   2.04e5  28059.        7.28   2.42e-12   1.49e5  259468. 
    ##  2 Bronx     gross_s~   1.61e2     17.1       9.41   8.60e-19   1.27e2     194. 
    ##  3 Bronx     land_sq~   2.22e1      3.26      6.82   4.42e-11   1.58e1      28.6
    ##  4 Brooklyn  (Interc~  -1.08e5  67685.       -1.60   1.11e- 1  -2.41e5   24861. 
    ##  5 Brooklyn  gross_s~   5.19e2     39.7      13.1    1.04e-33   4.41e2     597. 
    ##  6 Brooklyn  land_sq~   6.63e1     26.4       2.52   1.22e- 2   1.45e1     118. 
    ##  7 Manhattan (Interc~   6.49e5    NaN       NaN    NaN        NaN          NaN  
    ##  8 Queens    (Interc~   9.72e4  15981.        6.08   1.37e- 9   6.59e4  128541. 
    ##  9 Queens    gross_s~   2.49e2     12.3      20.2    4.14e-84   2.25e2     273. 
    ## 10 Queens    land_sq~   5.50e1      4.00     13.7    1.89e-41   4.72e1      62.9
    ## 11 Staten I~ (Interc~   2.26e5  17513.       12.9    1.15e-34   1.92e5  260361. 
    ## 12 Staten I~ gross_s~   1.90e2      9.84     19.3    5.72e-68   1.70e2     209. 
    ## 13 Staten I~ land_sq~   9.75e0      2.23      4.37   1.41e- 5   5.37e0      14.1

Gross square feet has a higher estimate but our models include both as
these are homes and not condominiums. However, we will use gross square
feet as that has a higher estimate

``` r
NYC_sm_md_two_story_slope <- NYC_sm_md_two_story_tidy %>% filter(term == "gross_square_feet") %>% arrange(estimate)
print(NYC_sm_md_two_story_slope)
```

    ## # A tibble: 4 x 8
    ## # Groups:   borough [4]
    ##   borough    term       estimate std.error statistic  p.value conf.low conf.high
    ##   <chr>      <chr>         <dbl>     <dbl>     <dbl>    <dbl>    <dbl>     <dbl>
    ## 1 Bronx      gross_squ~     161.     17.1       9.41 8.60e-19     127.      194.
    ## 2 Staten Is~ gross_squ~     190.      9.84     19.3  5.72e-68     170.      209.
    ## 3 Queens     gross_squ~     249.     12.3      20.2  4.14e-84     225.      273.
    ## 4 Brooklyn   gross_squ~     519.     39.7      13.1  1.04e-33     441.      597.

The highest estimate for gross square feet is in Brooklyn (which also
has the highest sale price)

Looking at land square feet

``` r
NYC_sm_md_two_story_slope_land <- NYC_sm_md_two_story_tidy %>% filter(term == "land_square_feet") %>% arrange(estimate)
print(NYC_sm_md_two_story_slope_land)
```

    ## # A tibble: 4 x 8
    ## # Groups:   borough [4]
    ##   borough    term       estimate std.error statistic  p.value conf.low conf.high
    ##   <chr>      <chr>         <dbl>     <dbl>     <dbl>    <dbl>    <dbl>     <dbl>
    ## 1 Staten Is~ land_squa~     9.75      2.23      4.37 1.41e- 5     5.37      14.1
    ## 2 Bronx      land_squa~    22.2       3.26      6.82 4.42e-11    15.8       28.6
    ## 3 Queens     land_squa~    55.0       4.00     13.7  1.89e-41    47.2       62.9
    ## 4 Brooklyn   land_squa~    66.3      26.4       2.52 1.22e- 2    14.5      118.

This also confirms that brooklyn has the highest estimate for land
square feet

Linear Regression Models for each Borough - Regression Summary Statistics
=========================================================================

We will apply the same workflow to generate tidy summary statistics for
each of the five boroughs

``` r
NYC_sm_md_two_story_summary <- NYC_sm_md_two_story %>% 
  group_by(borough) %>%
  nest() %>% mutate(linear_model = map(.x = data,
                                       .f = ~lm(sale_price ~ gross_square_feet + land_square_feet, data = .))) %>%
  mutate(tidy_summary_stats = map(.x = linear_model,
                                  .f = glance))

print(NYC_sm_md_two_story_summary)
```

    ## # A tibble: 5 x 4
    ## # Groups:   borough [5]
    ##   borough       data                  linear_model tidy_summary_stats
    ##   <chr>         <list>                <list>       <list>            
    ## 1 Bronx         <tibble [336 x 19]>   <lm>         <tibble [1 x 11]> 
    ## 2 Brooklyn      <tibble [497 x 19]>   <lm>         <tibble [1 x 11]> 
    ## 3 Manhattan     <tibble [1 x 19]>     <lm>         <tibble [1 x 11]> 
    ## 4 Queens        <tibble [2,463 x 19]> <lm>         <tibble [1 x 11]> 
    ## 5 Staten Island <tibble [782 x 19]>   <lm>         <tibble [1 x 11]>

Now let’s use the `tidy_summary_stats` to get our conclusion

``` r
NYC_sm_md_two_story_tidy <- NYC_sm_md_two_story_summary %>% 
  select(borough, tidy_summary_stats) %>%
  unnest(cols = tidy_summary_stats) %>%
  arrange(r.squared)

print(NYC_sm_md_two_story_tidy)
```

    ## # A tibble: 5 x 12
    ## # Groups:   borough [5]
    ##   borough r.squared adj.r.squared   sigma statistic    p.value    df  logLik
    ##   <chr>       <dbl>         <dbl>   <dbl>     <dbl>      <dbl> <int>   <dbl>
    ## 1 Manhat~     0             0        NaN        NA  NA             1    Inf 
    ## 2 Queens      0.375         0.374 247750.      737.  1.99e-251     3 -34084.
    ## 3 Bronx       0.418         0.414 196013.      120.  7.59e- 40     3  -4570.
    ## 4 Staten~     0.420         0.418 179133.      282.  7.94e- 93     3 -10567.
    ## 5 Brookl~     0.429         0.427 516795.      185.  8.25e- 61     3  -7242.
    ## # ... with 4 more variables: AIC <dbl>, BIC <dbl>, deviance <dbl>,
    ## #   df.residual <int>

Conclusion
==========

We can see that gross square feet and land square feet are good
predictors of sales prices with the highest R squared value in Brooklyn
followed by Staten Island and the Bronx. This is the lower in Queens and
Manhattan has a value of 0 simply because there was a single A1 property
sale there.
