---
title: "Predicting Bike Rentals Project"
date: 2020-05-04
tags: [machine learning, Bike Rentals]
excerpt: "Predicting Bike Rentals"
mathjax: "true"
---

# Introduction

The purpose of this project to predict the total number of bikes people rented in a given hour

The dataset can be downloaded from [here](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset)


```python
import pandas as pd

bike_rentals = pd.read_csv('bike_rental_hour.csv')

bike_rentals.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>instant</th>
      <th>dteday</th>
      <th>season</th>
      <th>yr</th>
      <th>mnth</th>
      <th>hr</th>
      <th>holiday</th>
      <th>weekday</th>
      <th>workingday</th>
      <th>weathersit</th>
      <th>temp</th>
      <th>atemp</th>
      <th>hum</th>
      <th>windspeed</th>
      <th>casual</th>
      <th>registered</th>
      <th>cnt</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>2011-01-01</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>1</td>
      <td>0.24</td>
      <td>0.2879</td>
      <td>0.81</td>
      <td>0.0</td>
      <td>3</td>
      <td>13</td>
      <td>16</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2011-01-01</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>1</td>
      <td>0.22</td>
      <td>0.2727</td>
      <td>0.80</td>
      <td>0.0</td>
      <td>8</td>
      <td>32</td>
      <td>40</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>2011-01-01</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>2</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>1</td>
      <td>0.22</td>
      <td>0.2727</td>
      <td>0.80</td>
      <td>0.0</td>
      <td>5</td>
      <td>27</td>
      <td>32</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>2011-01-01</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>3</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>1</td>
      <td>0.24</td>
      <td>0.2879</td>
      <td>0.75</td>
      <td>0.0</td>
      <td>3</td>
      <td>10</td>
      <td>13</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>2011-01-01</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>1</td>
      <td>0.24</td>
      <td>0.2879</td>
      <td>0.75</td>
      <td>0.0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>




```python
import matplotlib.pyplot as plt
%matplotlib inline

plt.hist(bike_rentals['cnt'])
```




    (array([6972., 3705., 2659., 1660.,  987.,  663.,  369.,  188.,  139.,
              37.]),
     array([  1. ,  98.6, 196.2, 293.8, 391.4, 489. , 586.6, 684.2, 781.8,
            879.4, 977. ]),
     <a list of 10 Patch objects>)




![png](Basics_files/Basics_2_1.png)



```python
bike_rentals.corr()['cnt']
```




    instant       0.278379
    season        0.178056
    yr            0.250495
    mnth          0.120638
    hr            0.394071
    holiday      -0.030927
    weekday       0.026900
    workingday    0.030284
    weathersit   -0.142426
    temp          0.404772
    atemp         0.400929
    hum          -0.322911
    windspeed     0.093234
    casual        0.694564
    registered    0.972151
    cnt           1.000000
    Name: cnt, dtype: float64



# Calculating Features


```python
def assign_label(hour):
    if hour >= 0 and hour < 6:
        return 4
    elif hour >=6 and hour < 12:
        return 1
    elif hour >=12 and hour < 18:
        return 2
    elif hour >= 18 and hour < 24:
        return 3

# this function assigns labels to each hour
bike_rentals['time_label'] = bike_rentals['hr'].apply(assign_label)
```


```python
bike_rentals.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>instant</th>
      <th>dteday</th>
      <th>season</th>
      <th>yr</th>
      <th>mnth</th>
      <th>hr</th>
      <th>holiday</th>
      <th>weekday</th>
      <th>workingday</th>
      <th>weathersit</th>
      <th>temp</th>
      <th>atemp</th>
      <th>hum</th>
      <th>windspeed</th>
      <th>casual</th>
      <th>registered</th>
      <th>cnt</th>
      <th>time_label</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>2011-01-01</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>1</td>
      <td>0.24</td>
      <td>0.2879</td>
      <td>0.81</td>
      <td>0.0</td>
      <td>3</td>
      <td>13</td>
      <td>16</td>
      <td>4</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>2011-01-01</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>1</td>
      <td>0.22</td>
      <td>0.2727</td>
      <td>0.80</td>
      <td>0.0</td>
      <td>8</td>
      <td>32</td>
      <td>40</td>
      <td>4</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>2011-01-01</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>2</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>1</td>
      <td>0.22</td>
      <td>0.2727</td>
      <td>0.80</td>
      <td>0.0</td>
      <td>5</td>
      <td>27</td>
      <td>32</td>
      <td>4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>2011-01-01</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>3</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>1</td>
      <td>0.24</td>
      <td>0.2879</td>
      <td>0.75</td>
      <td>0.0</td>
      <td>3</td>
      <td>10</td>
      <td>13</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>2011-01-01</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
      <td>4</td>
      <td>0</td>
      <td>6</td>
      <td>0</td>
      <td>1</td>
      <td>0.24</td>
      <td>0.2879</td>
      <td>0.75</td>
      <td>0.0</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
      <td>4</td>
    </tr>
  </tbody>
</table>
</div>



# Splitting the Data into Train and Test sets

We will use mean squared error here as it works for the data we have (continuous data)


```python
train = bike_rentals.sample(frac=0.8)
```


```python
test = bike_rentals.loc[~bike_rentals.index.isin(train.index)]
```

# Applying Linear Regression


```python
predict_columns = list(train.columns)
predict_columns.remove('cnt')
predict_columns.remove('casual')
predict_columns.remove('dteday')
predict_columns.remove('registered')
```


```python
from sklearn.linear_model import LinearRegression
lr = LinearRegression()
lr.fit(train[predict_columns],train['cnt'])

predictions = lr.predict(test[predict_columns])

from sklearn.metrics import mean_squared_error

mse = mean_squared_error(test['cnt'],predictions)
print('Mean Squared error is :',mse)
```

    Mean Squared error is : 17520.968344517692
    


```python
error = test['cnt'] - predictions
import numpy as np
mse_calc = np.mean(error ** 2)
mse_calc
```




    17520.968344517692




```python
if mse == mse_calc:
    print('Validated!')
```

    Validated!
    

Calculated the mse using both the sklearn module and the equation to validate that what is being returned is the same

The error will be high if the difference between true value and predicted value is high which is why we need to explore a decision tree

# Applying Decision Trees


```python
from sklearn.tree import DecisionTreeRegressor
dt = DecisionTreeRegressor(min_samples_leaf=5)
dt.fit(train[predict_columns],train['cnt'])
prediction_dt = dt.predict(test[predict_columns])
mse_leaf_5 = mean_squared_error(test['cnt'],prediction_dt)
print('mse with min_samples_leaf=5 is ',mse_leaf_5)
```

    mse with min_samples_leaf=5 is  2528.8522070293643
    


```python
dt2 = DecisionTreeRegressor(min_samples_leaf=2)
dt2.fit(train[predict_columns],train['cnt'])
prediction2_dt = dt2.predict(test[predict_columns])
mse_leaf_2 = mean_squared_error(test['cnt'],prediction2_dt)
print('mse with min_samples_leaf=2 is ',mse_leaf_2)
```

    mse with min_samples_leaf=2 is  2690.3229766014574
    


```python
percent_mse_error = ((mse_leaf_2 - mse_leaf_5)/(mse_leaf_5)) * 100
print(percent_mse_error)
```

    6.385140623214687
    

By increasing the minimum sample leaf from 2 to 5 reduces the mse error by 6.39%

In addition the mean squared error was lower when using a decision tree

# Applying Random Forests


```python
from sklearn.ensemble import RandomForestRegressor
rf = RandomForestRegressor(min_samples_leaf=5)
rf.fit(train[predict_columns],train['cnt'])
prediction_rf = rf.predict(test[predict_columns])
mse_rf_leaf_5 = mean_squared_error(test['cnt'],prediction_rf)
print('mse with min_samples_leaf=5 is ',mse_rf_leaf_5)
```

    mse with min_samples_leaf=5 is  1902.781306153977
    


```python
rf2 = RandomForestRegressor(min_samples_leaf=2)
rf2.fit(train[predict_columns],train['cnt'])
prediction2_rf = rf2.predict(test[predict_columns])
mse_rf_leaf_2 = mean_squared_error(test['cnt'],prediction2_rf)
print('mse with min_samples_leaf=2 is ',mse_rf_leaf_2)
```

    mse with min_samples_leaf=2 is  1808.5410421277859
    


```python
percent_mse_error_rf = ((mse_rf_leaf_5 - mse_rf_leaf_2)/(mse_rf_leaf_2)) * 100
print(percent_mse_error_rf)
```

    5.210844643885746
    

By decreasing the minimum sample leaf from 5 to 2 reduces the mse error by 5.21%

The mse for random forest is lower versus decision trees.  This suggests that some overfitting was removed
