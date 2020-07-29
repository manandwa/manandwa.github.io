---
title: "Predicting Car Prices"
date: 2020-07-16
tags: [machine learning, cars, UCI Data]
excerpt: "Predicting Car Prices"
mathjax: "true"
---

# Introduction

This project we will predict car prices using various features.  The dataset is from [here](https://archive.ics.uci.edu/ml/datasets/automobile)

```{r eval=FALSE, message=FALSE}
library(tidyverse)
library(caret)
```


Loading in the data

```{r}
cars <- read_csv("imports-85.data", col_names = FALSE)
```

Updating column names
```{r}
column_names = c("symboling", "normalized_losses", "make", "fuel_type", "aspiration", "num_doors", "body_style", "drive_wheels", "engine_location", "wheel_base", "length", "width", "height", "curb_weight", "engine_type", "num_cylinders", "engine_size", "fuel_system", "bore", "stroke", "compression_ratio", "horsepower", "peak_rpm", "city_mpg", "highway_mpg", "price")
typeof(column_names)
colnames(cars) <- column_names
```

Displaying the first few rows
```{r}
head(cars, 10)
```

We will first replace the `?` with `NA` to start off and we can do this using this [stackoverflow](https://stackoverflow.com/questions/47060014/replace-values-throughout-a-tibble) found from this google search: replace all values in a tibble r

```{r}
cars <- cars %>% mutate_all(~ replace(., .=="?", NA))
```

The columns that we need are the following:

1. normalized_losses
2. wheel_base
3. length
4. width
5. height
6. curb_weight
7. engine_size
8. bore
9. stroke
10. compression_ratio
11. horsepower
12. peak_rpm
13. city_mpg
14. highway_mpg
15. price


```{r}
numeric_columns <- c("normalized_losses", "wheel_base", "length", "width", "height", "curb_weight", "engine_size", "bore", "stroke", "compression_ratio", "horsepower", "peak_rpm", "city_mpg", "highway_mpg", "price")
cars <- cars %>% select(all_of(numeric_columns))
head(cars)
```


Let's look at the type for each column as we need to convert everything to numeric
Using the `map` function we can do this as stated in this [stackoverflow](https://stackoverflow.com/questions/21125222/determine-the-data-types-of-a-data-frames-columns) found from this google search: get type of each column in a tibble r

```{r}
map(cars,class)
```

We can use the `mutate_at` function as detailed in this [stackoverflow](https://stackoverflow.com/questions/49926705/how-to-convert-column-types-in-r-tidyverse) found from this google search: convert columns to a specific type in tibble r

```{r}
cars <- cars %>% mutate_at(vars(all_of(numeric_columns)), as.numeric)
```

Checking type again
```{r}
map(cars,class)
```

We can remove rows that contain missing data by using the `complete.cases` function found from [here](http://uc-r.github.io/na_exclude) found from this google search: remove missing values in r

```{r}
clean_up <- complete.cases(cars)
cars <- cars[clean_up,]
head(cars)
```

# Examing Relationships between Predictors

We will now look at how each feature is associated with price (which is what we want to predict).  We will do this using the `featurePlot` function provided in the `caret` library

```{r}
featurePlot(cars, cars$price)
```
 
The price column has a few outliers that are above 25000 so those will need to be looked at.  city_mpg and highway_mpg show a negative relationship compared to curb_weight which has a positive relationship with price along with horsepower.

# Setting up the Train-Test split

Now that we have our dataset we will first save it to a csv file so that we have not lost our previous work
```{r eval=FALSE}
write_csv(cars, "cars.csv")
```

```{r}
# Setting up train_index with an 80-20 split
train_index <- createDataPartition(
  y = cars[["price"]],
  p = 0.8,
  list = FALSE
)
training_cars <- cars[train_index,]
testing_cars <- cars[-train_index,]
```

Checking sizes of each dataset
```{r}
dim(cars)
```

```{r}
dim(training_cars)
```
```{r}
dim(testing_cars)
```

```{r}
128/160
```

```{r}
32/160
```

The sizes are what we expected

# Cross Validation and Hyperparameter Optimization

Here we will create our k-nearest-neighbors model using a grid of k ranging from 1 to 15 (due to 15 columns) and we will use the following features:

1. curb_weight
2. horsepower
3. city_mpg
4. highway_mpg

We will use 5 folds for this model

```{r}
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
```{r}
plot(knn_model)
```

The graph shows that the lowest RMSE is just about 3-4 neighbors with the highest at 15 neighbors.  This means that more neighbors in *this* particular model increases root mean squared error (RSME)

```{r}
table(knn_model["results"])
```

# Experimenting with different models

Now that we have cross validation we will start with the complete feature set

```{r}
colnames(cars)
```

```{r}
knn_model_full <- train(price ~ normalized_losses + wheel_base + length + width + height + curb_weight + engine_size + bore + stroke + compression_ratio + horsepower + peak_rpm + city_mpg + highway_mpg,
                   data = training_cars,
                   method = "knn",
                   trControl = train_control,
                   preProcess = c("center", "scale"),
                   tuneGrid = knn_grid)       
```

```{r}
plot(knn_model_full)
```

Using all the features gives us an RMSE of 2500

# Final model predictions

As the model with the full feature set gave a better RMSE then a 4 feature model we will go with this one `knn_model_full` and obtain the predictions
```{r}
prediction <- predict(knn_model_full, newdata = testing_cars)
```

Comparing predictions with the actual data from the testing set using the `postResample` function

```{r}
postResample(pred = prediction, obs = testing_cars$price)
```
