---
title: "Predicting House Prices Project"
date: 2020-07-29
tags: [machine learning, missing data, NYC Condomimum]
header:
  image: "/images/perceptron/percept.jpg"
excerpt: "Predicting house prices in NYC"
mathjax: "true"
---
This project will explore the following questions:

1. How well does the size of a property (measured in gross square feet) explain or predict sale price across New York City as a whole? 
2. How well does the size of a property explain or predict sale price for each individual borough?
3. How does land square feet play into sale price for houses


To answer question 1 we will explore property data for all five boroughs

To answer question 2 we will build, analyze and compare linear models for each borough

# Understanding the data

The data that we are using from this project come from five separate excel files (one for each borough) and can be obtained from [here](https://www1.nyc.gov/site/finance/taxes/property-rolling-sales-data.page)

The data is for June 2019-May 2020

```{r message=FALSE}
library(readxl)
library(readr)
library(stringr)
library(dplyr)
library(tidyr)
library(magrittr)
library(ggplot2)
```

```{r}
# loading in the data from each excel file setting skip = 4 to avoid header rows
queens <- read_excel("rollingsales_queens.xls", skip = 4)
staten_island <- read_excel("rollingsales_statenisland.xls", skip = 4)
brooklyn <- read_excel("rollingsales_brooklyn.xls", skip = 4)
bronx <- read_excel("rollingsales_bronx.xls", skip = 4)
manhattan <- read_excel("rollingsales_manhattan.xls", skip = 4)
```

Each Borough in New York has a specific number which we can get from the `BOROUGH` variable in each dataframe

```{r}
head(queens$BOROUGH, 1)
head(staten_island$BOROUGH, 1)
head(brooklyn$BOROUGH, 1)
head(bronx$BOROUGH, 1)
head(manhattan$BOROUGH, 1)
```

```{r}
# The Borough numbers are as follows:
# Manhattan = 1
# Bronx = 2
# Brooklyn = 3
# Queens = 4
# Staten Island = 5
```

We will now bind the five dataframes into a single dataframe
```{r}
NYC_property_sales <- bind_rows(manhattan, bronx, brooklyn, queens, staten_island)
```

Since we have a single dataframe with all NYC property sales, we don't need the 5 individual dataframes and can remove then using the `rm` function
```{r}
rm(brooklyn, bronx, manhattan, queens, staten_island)
```

Since we have the numbers for each borough we can replace them with the borough names so that it is more clear
```{r}
NYC_property_sales <- NYC_property_sales %>% mutate(BOROUGH = 
                                                      case_when(BOROUGH == 1 ~ "Manhattan",
                                                                BOROUGH == 2 ~ "Bronx",
                                                                BOROUGH == 3 ~ "Brooklyn",
                                                                BOROUGH == 4 ~ "Queens",
                                                                BOROUGH == 5 ~ "Staten Island"))
```

Now that we have our data in a single dataframe with the `BOROUGH` column taken care of there are a few more steps to get this into the format that we need
```{r}
# Convert all column names to lowercase and replace spaces with underscores
colnames(NYC_property_sales) %<>% str_replace_all("\\s", "_") %>% tolower()
# Convert CAPTIALIZED case to Title case (the NEIGHBORHOOD,BUILDING CLASS CATEGORY and ADDRESS columns)
NYC_property_sales <- NYC_property_sales %>% mutate(neighborhood = str_to_title(neighborhood)) %>% mutate(building_class_category = str_to_title(building_class_category)) %>% mutate(address = str_to_title(address))
```


```{r}
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
```{r eval= FALSE}
write_csv(NYC_property_sales, "NYC_property_sales.csv")
```

As I found out for condominums `gross_square_feet` isn't being collected for condominiums (this uses the latest data obtained on July 20th 2020) so we need to find out what building types we can use

```{r}
sort(table(NYC_property_sales$building_class_at_present))
```

The most listed is type A1 which according to the building codes is this: TWO STORIES - DETACHED SM OR MID

According to further research found [here](https://www.propertyshark.com/info/class-buildings-one-family-dwellings/#a1) it is a single familiy home and thus we will need to include land_square_feet

```{r}
NYC_property_sales <- NYC_property_sales %>% filter(land_square_feet > 0) %>% drop_na(land_square_feet)
```

We will now filter according to A1
```{r}
NYC_sm_md_two_story <- NYC_property_sales %>% filter(building_class_at_time_of_sale == "A1")
head(NYC_sm_md_two_story)
```

# Explore Bivarate Relationships with Scatterplots

```{r}
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

Let's adjust our y limits by looking at the maximum sale price
```{r}
max(NYC_sm_md_two_story$sale_price)
```

We see in general as gross square feet increases sale price increases

Let's look at land square feet to see if that holds true
```{r}
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

We see this also holds up for land square feet (which is included in gross square feet as homes include the square footage of the house as well as land)

We will also zoom in on a smaller subset of data with a max sale price of \$2,500,000 and a max gross square feet of 5000
```{r}
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

Zooming in also shows for single family homes as the size increases so does the price

Looking at land values
```{r}
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

This trend also shows that as land size increases the sale price also increaes

However, these graphs are cluttered in that all five boroughs are shown.  

```{r}
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

For each borough we see that homes with larger gross square feet have higher sale prices

# Outliers and Data Integrity Issues

To see any outliers we first need to sort sale prices by highest to lowest
```{r}
NYC_sm_md_two_story %>% arrange(desc(sale_price)) %>% head()
```

5 of the top sellers are in Brooklyn with one in Queens.  The highest price of \$9,000,000 was mostly land (looking on realtor.com doesn't show a house but zillow.com does show a house there)

```{r}
NYC_sm_md_two_story %>% filter(borough == "Brooklyn") %>% arrange(desc(gross_square_feet)) %>% head()
```
The largest gross square foot property was converted from a 3 familiy apartment to a single family home

```{r}
NYC_sm_md_two_story %>% arrange(desc(gross_square_feet)) %>% head()
```

However looking at gross square feet here shows a different story but then it depends on the borough the property is in.

We will not remove the outlier for now as we want to confirm the sale is legitimate

# Linear Regression Model for Boroughs in New York City Combined

We will now generate a linear model using `sale_price` explained by `gross_square_feet` and `land_square_feet`

```{r}
NYC_sm_md_two_story_lm <- lm(sale_price ~ gross_square_feet + land_square_feet, data = NYC_sm_md_two_story)
summary(NYC_sm_md_two_story_lm)
```

If we remove the highest listing
```{r}
NYC_sm_md_two_story_modified <- NYC_sm_md_two_story %>% filter(address != "704 Avenue I")
```

```{r}
NYC_sm_md_two_story_modified_lm <- lm(sale_price ~ gross_square_feet + land_square_feet, data = NYC_sm_md_two_story_modified)
summary(NYC_sm_md_two_story_modified_lm)
```

Removing the highest listing caused a reduction in the estimate for gross_square_feet but increased land_square_feet.  For standard error it was reduced for both gross_square_feet and land_square_feet

Replotting using facet wrap without the outlier
```{r}
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

# Linear regression model for each borough - Coefficient Estimates

We have a linear model for all of the boroughs but would be more useful is if we had a model for each one.  We will use the dataset that contains the outlier as that is still a sale (and was confirmed through realtor.com)

We will use the `broom` workflow which is the following:

1. Nest a dataframe by a categorical variable with the `nest()` function from the `tidyr` package - we will nest by `borough`.
2. Fit models to nested dataframes with the `map()` function from the `purrr` package.
3. Apply the `broom` functions `tidy()`, `augment()`, and/or `glance()` using each nested model - we'll work with `tidy()` first.
4. Return a tidy dataframe with the `unnest()` function - this allows us to see the results.

```{r}
library(broom)
library(tidyr)
library(purrr)
```
```{r}
NYC_sm_md_two_story_nested <- NYC_sm_md_two_story %>% group_by(borough) %>% nest()
print(NYC_sm_md_two_story_nested)
```

Looking at Manhattan we have
```{r}
head(NYC_sm_md_two_story_nested$data[[3]])
```

We will now fit a linear model to each dataframe (borough)
```{r}
NYC_sm_md_two_story_coefficients <- NYC_sm_md_two_story %>% group_by(borough) %>% nest() %>%
  mutate(linear_model = map(.x = data,
                            .f = ~lm(sale_price ~ gross_square_feet + land_square_feet, data = .)
                            ))
```

```{r}
print(NYC_sm_md_two_story_coefficients)
```

Looking at the summary for Manhattan
```{r}
summary(NYC_sm_md_two_story_coefficients$linear_model[[3]])
```

That is because there is only a single entry for Manhattan looking at Brooklyn
```{r}
summary(NYC_sm_md_two_story_coefficients$linear_model[[2]])
```

Transforming this dataframe into a tidy format
```{r}
NYC_sm_md_two_story_coefficients <- NYC_sm_md_two_story %>% group_by(borough) %>% nest() %>%
  mutate(linear_model = map(.x = data,
                            .f = ~lm(sale_price ~ gross_square_feet + land_square_feet, data = .)
                            )) %>%
  mutate(tidy_coefficients = map(.x = linear_model,
                                 .f = tidy,
                                 conf.int = TRUE))
NYC_sm_md_two_story_coefficients
```

First inspect for Brooklyn
```{r}
print(NYC_sm_md_two_story_coefficients$tidy_coefficients[[2]])
```


And let's get a tidy dataframe out using `unest()`

```{r}
NYC_sm_md_two_story_tidy <- NYC_sm_md_two_story_coefficients %>% 
  select(borough, tidy_coefficients) %>% 
  unnest(cols = tidy_coefficients)
print(NYC_sm_md_two_story_tidy)
```

Gross square feet has a higher estimate but our models include both as these are homes and not condominiums.  However, we will use gross square feet as that has a higher estimate

```{r}
NYC_sm_md_two_story_slope <- NYC_sm_md_two_story_tidy %>% filter(term == "gross_square_feet") %>% arrange(estimate)
print(NYC_sm_md_two_story_slope)
```

The highest estimate for gross square feet is in Brooklyn (which also has the highest sale price)

Looking at land square feet
```{r}
NYC_sm_md_two_story_slope_land <- NYC_sm_md_two_story_tidy %>% filter(term == "land_square_feet") %>% arrange(estimate)
print(NYC_sm_md_two_story_slope_land)
```

This also confirms that brooklyn has the highest estimate for land square feet

# Linear Regression Models for each Borough - Regression Summary Statistics

We will apply the same workflow to generate tidy summary statistics for each of the five boroughs
```{r}
NYC_sm_md_two_story_summary <- NYC_sm_md_two_story %>% 
  group_by(borough) %>%
  nest() %>% mutate(linear_model = map(.x = data,
                                       .f = ~lm(sale_price ~ gross_square_feet + land_square_feet, data = .))) %>%
  mutate(tidy_summary_stats = map(.x = linear_model,
                                  .f = glance))
print(NYC_sm_md_two_story_summary)
  
```

Now let's use the `tidy_summary_stats` to get our conclusion
```{r}
NYC_sm_md_two_story_tidy <- NYC_sm_md_two_story_summary %>% 
  select(borough, tidy_summary_stats) %>%
  unnest(cols = tidy_summary_stats) %>%
  arrange(r.squared)
print(NYC_sm_md_two_story_tidy)
```

# Conclusion

We can see that gross square feet and land square feet are good predictors of sales prices with the highest R squared value in Brooklyn followed by Staten Island and the Bronx.  This is the lower in Queens and Manhattan has a value of 0 simply because there was a single A1 property sale there.
