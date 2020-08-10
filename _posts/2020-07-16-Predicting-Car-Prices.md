---
title: "Predicting Car Prices"
date: 2020-07-16
tags: [machine learning, cars, UCI Data]
excerpt: "Predicting Car Prices"
mathjax: "true"
---

This project we will predict car prices using various features. The
dataset is from
[here](https://archive.ics.uci.edu/ml/datasets/automobile)

``` r
library(tidyverse)
```

    ## Warning: package 'ggplot2' was built under R version 4.0.2

    ## Warning: package 'stringr' was built under R version 4.0.2

``` r
library(caret)
```

    ## Warning: package 'caret' was built under R version 4.0.2

Loading in the data

``` r
cars <- read_csv("imports-85.data", col_names = FALSE)
```

    ## Parsed with column specification:
    ## cols(
    ##   .default = col_character(),
    ##   X1 = col_double(),
    ##   X10 = col_double(),
    ##   X11 = col_double(),
    ##   X12 = col_double(),
    ##   X13 = col_double(),
    ##   X14 = col_double(),
    ##   X17 = col_double(),
    ##   X21 = col_double(),
    ##   X24 = col_double(),
    ##   X25 = col_double()
    ## )

    ## See spec(...) for full column specifications.

Updating column names

``` r
column_names = c("symboling", "normalized_losses", "make", "fuel_type", "aspiration", "num_doors", "body_style", "drive_wheels", "engine_location", "wheel_base", "length", "width", "height", "curb_weight", "engine_type", "num_cylinders", "engine_size", "fuel_system", "bore", "stroke", "compression_ratio", "horsepower", "peak_rpm", "city_mpg", "highway_mpg", "price")
typeof(column_names)
```

    ## [1] "character"

``` r
colnames(cars) <- column_names
```

Displaying the first few rows

``` r
head(cars, 10)
```

    ## # A tibble: 10 x 26
    ##    symboling normalized_loss~ make  fuel_type aspiration num_doors body_style
    ##        <dbl> <chr>            <chr> <chr>     <chr>      <chr>     <chr>     
    ##  1         3 ?                alfa~ gas       std        two       convertib~
    ##  2         3 ?                alfa~ gas       std        two       convertib~
    ##  3         1 ?                alfa~ gas       std        two       hatchback 
    ##  4         2 164              audi  gas       std        four      sedan     
    ##  5         2 164              audi  gas       std        four      sedan     
    ##  6         2 ?                audi  gas       std        two       sedan     
    ##  7         1 158              audi  gas       std        four      sedan     
    ##  8         1 ?                audi  gas       std        four      wagon     
    ##  9         1 158              audi  gas       turbo      four      sedan     
    ## 10         0 ?                audi  gas       turbo      two       hatchback 
    ## # ... with 19 more variables: drive_wheels <chr>, engine_location <chr>,
    ## #   wheel_base <dbl>, length <dbl>, width <dbl>, height <dbl>,
    ## #   curb_weight <dbl>, engine_type <chr>, num_cylinders <chr>,
    ## #   engine_size <dbl>, fuel_system <chr>, bore <chr>, stroke <chr>,
    ## #   compression_ratio <dbl>, horsepower <chr>, peak_rpm <chr>, city_mpg <dbl>,
    ## #   highway_mpg <dbl>, price <chr>

We will first replace the `?` with `NA` to start off and we can do this
using this
[stackoverflow](https://stackoverflow.com/questions/47060014/replace-values-throughout-a-tibble)
found from this google search: replace all values in a tibble r

``` r
cars <- cars %>% mutate_all(~ replace(., .=="?", NA))
```

The columns that we need are the following:

1.  normalized\_losses
2.  wheel\_base
3.  length
4.  width
5.  height
6.  curb\_weight
7.  engine\_size
8.  bore
9.  stroke
10. compression\_ratio
11. horsepower
12. peak\_rpm
13. city\_mpg
14. highway\_mpg
15. price

``` r
numeric_columns <- c("normalized_losses", "wheel_base", "length", "width", "height", "curb_weight", "engine_size", "bore", "stroke", "compression_ratio", "horsepower", "peak_rpm", "city_mpg", "highway_mpg", "price")
cars <- cars %>% select(all_of(numeric_columns))
head(cars)
```

    ## # A tibble: 6 x 15
    ##   normalized_loss~ wheel_base length width height curb_weight engine_size bore 
    ##   <chr>                 <dbl>  <dbl> <dbl>  <dbl>       <dbl>       <dbl> <chr>
    ## 1 <NA>                   88.6   169.  64.1   48.8        2548         130 3.47 
    ## 2 <NA>                   88.6   169.  64.1   48.8        2548         130 3.47 
    ## 3 <NA>                   94.5   171.  65.5   52.4        2823         152 2.68 
    ## 4 164                    99.8   177.  66.2   54.3        2337         109 3.19 
    ## 5 164                    99.4   177.  66.4   54.3        2824         136 3.19 
    ## 6 <NA>                   99.8   177.  66.3   53.1        2507         136 3.19 
    ## # ... with 7 more variables: stroke <chr>, compression_ratio <dbl>,
    ## #   horsepower <chr>, peak_rpm <chr>, city_mpg <dbl>, highway_mpg <dbl>,
    ## #   price <chr>

Let’s look at the type for each column as we need to convert everything
to numeric Using the `map` function we can do this as stated in this
[stackoverflow](https://stackoverflow.com/questions/21125222/determine-the-data-types-of-a-data-frames-columns)
found from this google search: get type of each column in a tibble r

``` r
map(cars,class)
```

    ## $normalized_losses
    ## [1] "character"
    ## 
    ## $wheel_base
    ## [1] "numeric"
    ## 
    ## $length
    ## [1] "numeric"
    ## 
    ## $width
    ## [1] "numeric"
    ## 
    ## $height
    ## [1] "numeric"
    ## 
    ## $curb_weight
    ## [1] "numeric"
    ## 
    ## $engine_size
    ## [1] "numeric"
    ## 
    ## $bore
    ## [1] "character"
    ## 
    ## $stroke
    ## [1] "character"
    ## 
    ## $compression_ratio
    ## [1] "numeric"
    ## 
    ## $horsepower
    ## [1] "character"
    ## 
    ## $peak_rpm
    ## [1] "character"
    ## 
    ## $city_mpg
    ## [1] "numeric"
    ## 
    ## $highway_mpg
    ## [1] "numeric"
    ## 
    ## $price
    ## [1] "character"

We can use the `mutate_at` function as detailed in this
[stackoverflow](https://stackoverflow.com/questions/49926705/how-to-convert-column-types-in-r-tidyverse)
found from this google search: convert columns to a specific type in
tibble r

``` r
cars <- cars %>% mutate_at(vars(all_of(numeric_columns)), as.numeric)
```

Checking type again

``` r
map(cars,class)
```

    ## $normalized_losses
    ## [1] "numeric"
    ## 
    ## $wheel_base
    ## [1] "numeric"
    ## 
    ## $length
    ## [1] "numeric"
    ## 
    ## $width
    ## [1] "numeric"
    ## 
    ## $height
    ## [1] "numeric"
    ## 
    ## $curb_weight
    ## [1] "numeric"
    ## 
    ## $engine_size
    ## [1] "numeric"
    ## 
    ## $bore
    ## [1] "numeric"
    ## 
    ## $stroke
    ## [1] "numeric"
    ## 
    ## $compression_ratio
    ## [1] "numeric"
    ## 
    ## $horsepower
    ## [1] "numeric"
    ## 
    ## $peak_rpm
    ## [1] "numeric"
    ## 
    ## $city_mpg
    ## [1] "numeric"
    ## 
    ## $highway_mpg
    ## [1] "numeric"
    ## 
    ## $price
    ## [1] "numeric"

We can remove rows that contain missing data by using the
`complete.cases` function found from
[here](http://uc-r.github.io/na_exclude) found from this google search:
remove missing values in r

``` r
clean_up <- complete.cases(cars)
cars <- cars[clean_up,]
head(cars)
```

    ## # A tibble: 6 x 15
    ##   normalized_loss~ wheel_base length width height curb_weight engine_size  bore
    ##              <dbl>      <dbl>  <dbl> <dbl>  <dbl>       <dbl>       <dbl> <dbl>
    ## 1              164       99.8   177.  66.2   54.3        2337         109  3.19
    ## 2              164       99.4   177.  66.4   54.3        2824         136  3.19
    ## 3              158      106.    193.  71.4   55.7        2844         136  3.19
    ## 4              158      106.    193.  71.4   55.9        3086         131  3.13
    ## 5              192      101.    177.  64.8   54.3        2395         108  3.5 
    ## 6              192      101.    177.  64.8   54.3        2395         108  3.5 
    ## # ... with 7 more variables: stroke <dbl>, compression_ratio <dbl>,
    ## #   horsepower <dbl>, peak_rpm <dbl>, city_mpg <dbl>, highway_mpg <dbl>,
    ## #   price <dbl>

Examing Relationships between Predictors
========================================

We will now look at how each feature is associated with price (which is
what we want to predict). We will do this using the `featurePlot`
function provided in the `caret` library

``` r
featurePlot(cars, cars$price)
```

![](/images/predicting-cars/unnamed-chunk-11-1.png)

The price column has a few outliers that are above 25000 so those will
need to be looked at. city\_mpg and highway\_mpg show a negative
relationship compared to curb\_weight which has a positive relationship
with price along with horsepower.

Setting up the Train-Test split
===============================

Now that we have our dataset we will first save it to a csv file so that
we have not lost our previous work

``` r
write_csv(cars, "cars.csv")
```

``` r
# Setting up train_index with an 80-20 split
train_index <- createDataPartition(
  y = cars[["price"]],
  p = 0.8,
  list = FALSE
)

training_cars <- cars[train_index,]
```

    ## Warning: The `i` argument of ``[`()` can't be a matrix as of tibble 3.0.0.
    ## Convert to a vector.
    ## This warning is displayed once every 8 hours.
    ## Call `lifecycle::last_warnings()` to see where this warning was generated.

``` r
testing_cars <- cars[-train_index,]
```

Checking sizes of each dataset

``` r
dim(cars)
```

    ## [1] 160  15

``` r
dim(training_cars)
```

    ## [1] 128  15

``` r
dim(testing_cars)
```

    ## [1] 32 15

``` r
128/160
```

    ## [1] 0.8

``` r
32/160
```

    ## [1] 0.2

The sizes are what we expected

Cross Validation and Hyperparameter Optimization
================================================

Here we will create our k-nearest-neighbors model using a grid of k
ranging from 1 to 15 (due to 15 columns) and we will use the following
features:

1.  curb\_weight
2.  horsepower
3.  city\_mpg
4.  highway\_mpg

We will use 5 folds for this model

``` r
knn_grid <- expand.grid(k = 1:15)
train_control <- trainControl(method = "cv", number = 5)
knn_model <- train(price ~ curb_weight + horsepower + city_mpg + highway_mpg,
                   data = training_cars,
                   method = "knn",
                   trControl = train_control,
                   preProcess = c("center", "scale"),
                   tuneGrid = knn_grid)                   
```

We will plot this to see how the `knn_model` looks like

``` r
plot(knn_model)
```

![](/images/predicting-cars/unnamed-chunk-20-1.png)

The graph shows that the lowest RMSE is just about 3-4 neighbors with
the highest at 15 neighbors. This means that more neighbors in *this*
particular model increases root mean squared error (RSME)

``` r
table(knn_model["results"])
```

    ## 
    ##                                                                                                                                                                                                                                                                                                         1:15 
    ##                                                                                                                                                                                                                                                                                                            1 
    ##                                c(2826.81568812752, 2504.24315298682, 2737.20100076888, 2770.73032200166, 2786.99457343645, 2843.65750100018, 2849.05805971245, 2846.21556694768, 2937.88510097231, 2944.71420206242, 2988.1020674569, 3074.3324428169, 3093.40552402516, 3118.55567900193, 3143.10334189193) 
    ##                                                                                                                                                                                                                                                                                                            1 
    ##                  c(0.77420846396621, 0.821892122704789, 0.796173811036434, 0.807948386404993, 0.820581239512669, 0.814940999482915, 0.819494686261921, 0.82397912466999, 0.81369959130782, 0.820145298014334, 0.811439115035298, 0.795289608359413, 0.792684363131977, 0.786254211346697, 0.780476266909835) 
    ##                                                                                                                                                                                                                                                                                                            1 
    ##                              c(1702.42158974359, 1529.10917948718, 1754.47292307692, 1736.76769230769, 1784.32871355311, 1859.60872893773, 1858.94358974359, 1859.99711111111, 1922.73310644911, 1913.04201398601, 1945.83650277927, 1998.65042688081, 2001.69239898563, 2031.82301684982, 2055.81331523379) 
    ##                                                                                                                                                                                                                                                                                                            1 
    ##                               c(932.375240489394, 435.170467152374, 509.848952830167, 749.06225745334, 849.833645819096, 933.828164635731, 1058.82302489051, 1116.63620307023, 1040.71533966348, 1070.30299755486, 1027.78233392835, 1059.72846902296, 1081.95877420689, 1075.52016376429, 1103.04586663826) 
    ##                                                                                                                                                                                                                                                                                                            1 
    ## c(0.0915421503912294, 0.0553943012787175, 0.0638982721667744, 0.0421573288411698, 0.0333247166751934, 0.0512577093465644, 0.0418193429396849, 0.028805742391094, 0.0215550768188394, 0.0269476955595073, 0.0359549440890032, 0.0297759535381583, 0.0362422605021033, 0.0229761507506586, 0.0223140331210049) 
    ##                                                                                                                                                                                                                                                                                                            1 
    ##                                c(172.860074537887, 217.321157713542, 211.641153298021, 369.273304987719, 393.568029842871, 445.699035084876, 525.086449369004, 542.507694378913, 509.984679898063, 503.29905863451, 513.559631105243, 524.649415066219, 530.201037485222, 539.739673291184, 550.43802022753) 
    ##                                                                                                                                                                                                                                                                                                            1

Experimenting with different models
===================================

Now that we have cross validation we will start with the complete
feature set

``` r
colnames(cars)
```

    ##  [1] "normalized_losses" "wheel_base"        "length"           
    ##  [4] "width"             "height"            "curb_weight"      
    ##  [7] "engine_size"       "bore"              "stroke"           
    ## [10] "compression_ratio" "horsepower"        "peak_rpm"         
    ## [13] "city_mpg"          "highway_mpg"       "price"

``` r
knn_model_full <- train(price ~ normalized_losses + wheel_base + length + width + height + curb_weight + engine_size + bore + stroke + compression_ratio + horsepower + peak_rpm + city_mpg + highway_mpg,
                   data = training_cars,
                   method = "knn",
                   trControl = train_control,
                   preProcess = c("center", "scale"),
                   tuneGrid = knn_grid)       
```

``` r
plot(knn_model_full)
```

![](/images/predicting-cars/unnamed-chunk-24-1.png)

Using all the features gives us an RMSE of 2500

Final model predictions
=======================

As the model with the full feature set gave a better RMSE then a 4
feature model we will go with this one `knn_model_full` and obtain the
predictions

``` r
prediction <- predict(knn_model_full, newdata = testing_cars)
```

Comparing predictions with the actual data from the testing set using
the `postResample` function

``` r
postResample(pred = prediction, obs = testing_cars$price)
```

    ##         RMSE     Rsquared          MAE 
    ## 1545.1174131    0.9071903 1011.8593750
