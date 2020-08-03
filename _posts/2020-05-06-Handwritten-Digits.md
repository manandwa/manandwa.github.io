---
title: "Building a Handwritten Digits Classifier"
date: 2020-05-06
tags: [deep learning, Digits]
excerpt: "Handwritten Digits Classifier"
mathjax: "true"
---

# Introduction

We will use deep feedforward networks to classify images 

# Working With Image Data


```python
from sklearn.datasets import load_digits
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
%matplotlib inline
```


```python
digits_data = load_digits()
```


```python
digits_data
```




    {'data': array([[ 0.,  0.,  5., ...,  0.,  0.,  0.],
            [ 0.,  0.,  0., ..., 10.,  0.,  0.],
            [ 0.,  0.,  0., ..., 16.,  9.,  0.],
            ...,
            [ 0.,  0.,  1., ...,  6.,  0.,  0.],
            [ 0.,  0.,  2., ..., 12.,  0.,  0.],
            [ 0.,  0., 10., ..., 12.,  1.,  0.]]),
     'target': array([0, 1, 2, ..., 8, 9, 8]),
     'target_names': array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9]),
     'images': array([[[ 0.,  0.,  5., ...,  1.,  0.,  0.],
             [ 0.,  0., 13., ..., 15.,  5.,  0.],
             [ 0.,  3., 15., ..., 11.,  8.,  0.],
             ...,
             [ 0.,  4., 11., ..., 12.,  7.,  0.],
             [ 0.,  2., 14., ..., 12.,  0.,  0.],
             [ 0.,  0.,  6., ...,  0.,  0.,  0.]],
     
            [[ 0.,  0.,  0., ...,  5.,  0.,  0.],
             [ 0.,  0.,  0., ...,  9.,  0.,  0.],
             [ 0.,  0.,  3., ...,  6.,  0.,  0.],
             ...,
             [ 0.,  0.,  1., ...,  6.,  0.,  0.],
             [ 0.,  0.,  1., ...,  6.,  0.,  0.],
             [ 0.,  0.,  0., ..., 10.,  0.,  0.]],
     
            [[ 0.,  0.,  0., ..., 12.,  0.,  0.],
             [ 0.,  0.,  3., ..., 14.,  0.,  0.],
             [ 0.,  0.,  8., ..., 16.,  0.,  0.],
             ...,
             [ 0.,  9., 16., ...,  0.,  0.,  0.],
             [ 0.,  3., 13., ..., 11.,  5.,  0.],
             [ 0.,  0.,  0., ..., 16.,  9.,  0.]],
     
            ...,
     
            [[ 0.,  0.,  1., ...,  1.,  0.,  0.],
             [ 0.,  0., 13., ...,  2.,  1.,  0.],
             [ 0.,  0., 16., ..., 16.,  5.,  0.],
             ...,
             [ 0.,  0., 16., ..., 15.,  0.,  0.],
             [ 0.,  0., 15., ..., 16.,  0.,  0.],
             [ 0.,  0.,  2., ...,  6.,  0.,  0.]],
     
            [[ 0.,  0.,  2., ...,  0.,  0.,  0.],
             [ 0.,  0., 14., ..., 15.,  1.,  0.],
             [ 0.,  4., 16., ..., 16.,  7.,  0.],
             ...,
             [ 0.,  0.,  0., ..., 16.,  2.,  0.],
             [ 0.,  0.,  4., ..., 16.,  2.,  0.],
             [ 0.,  0.,  5., ..., 12.,  0.,  0.]],
     
            [[ 0.,  0., 10., ...,  1.,  0.,  0.],
             [ 0.,  2., 16., ...,  1.,  0.,  0.],
             [ 0.,  0., 15., ..., 15.,  0.,  0.],
             ...,
             [ 0.,  4., 16., ..., 16.,  6.,  0.],
             [ 0.,  8., 16., ..., 16.,  8.,  0.],
             [ 0.,  1.,  8., ..., 12.,  1.,  0.]]]),
     'DESCR': ".. _digits_dataset:\n\nOptical recognition of handwritten digits dataset\n--------------------------------------------------\n\n**Data Set Characteristics:**\n\n    :Number of Instances: 5620\n    :Number of Attributes: 64\n    :Attribute Information: 8x8 image of integer pixels in the range 0..16.\n    :Missing Attribute Values: None\n    :Creator: E. Alpaydin (alpaydin '@' boun.edu.tr)\n    :Date: July; 1998\n\nThis is a copy of the test set of the UCI ML hand-written digits datasets\nhttps://archive.ics.uci.edu/ml/datasets/Optical+Recognition+of+Handwritten+Digits\n\nThe data set contains images of hand-written digits: 10 classes where\neach class refers to a digit.\n\nPreprocessing programs made available by NIST were used to extract\nnormalized bitmaps of handwritten digits from a preprinted form. From a\ntotal of 43 people, 30 contributed to the training set and different 13\nto the test set. 32x32 bitmaps are divided into nonoverlapping blocks of\n4x4 and the number of on pixels are counted in each block. This generates\nan input matrix of 8x8 where each element is an integer in the range\n0..16. This reduces dimensionality and gives invariance to small\ndistortions.\n\nFor info on NIST preprocessing routines, see M. D. Garris, J. L. Blue, G.\nT. Candela, D. L. Dimmick, J. Geist, P. J. Grother, S. A. Janet, and C.\nL. Wilson, NIST Form-Based Handprint Recognition System, NISTIR 5469,\n1994.\n\n.. topic:: References\n\n  - C. Kaynak (1995) Methods of Combining Multiple Classifiers and Their\n    Applications to Handwritten Digit Recognition, MSc Thesis, Institute of\n    Graduate Studies in Science and Engineering, Bogazici University.\n  - E. Alpaydin, C. Kaynak (1998) Cascading Classifiers, Kybernetika.\n  - Ken Tang and Ponnuthurai N. Suganthan and Xi Yao and A. Kai Qin.\n    Linear dimensionalityreduction using relevance weighted LDA. School of\n    Electrical and Electronic Engineering Nanyang Technological University.\n    2005.\n  - Claudio Gentile. A New Approximate Maximal Margin Classification\n    Algorithm. NIPS. 2000."}




```python
data = pd.DataFrame(digits_data['data']) # features in dataframe
labels = pd.Series(digits_data['target']) # labels in series
data.head()
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
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4</th>
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>54</th>
      <th>55</th>
      <th>56</th>
      <th>57</th>
      <th>58</th>
      <th>59</th>
      <th>60</th>
      <th>61</th>
      <th>62</th>
      <th>63</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>5.0</td>
      <td>13.0</td>
      <td>9.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>6.0</td>
      <td>13.0</td>
      <td>10.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>1</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>12.0</td>
      <td>13.0</td>
      <td>5.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>11.0</td>
      <td>16.0</td>
      <td>10.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>2</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>4.0</td>
      <td>15.0</td>
      <td>12.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>5.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>11.0</td>
      <td>16.0</td>
      <td>9.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>3</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>7.0</td>
      <td>15.0</td>
      <td>13.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>8.0</td>
      <td>...</td>
      <td>9.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>7.0</td>
      <td>13.0</td>
      <td>13.0</td>
      <td>9.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <td>4</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>11.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>2.0</td>
      <td>16.0</td>
      <td>4.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 64 columns</p>
</div>




```python
first_image = data.iloc[0]
np_image = first_image.values
np_image = np_image.reshape(8,8)
plt.imshow(np_image, cmap='gray_r')
```




    <matplotlib.image.AxesImage at 0x9f6d832848>




![png](Basics_files/Basics_6_1.png)



```python
figure, axis = plt.subplots(2,4)
# First column
axis[0, 0].imshow(data.iloc[0].values.reshape(8,8), cmap='gray_r')
axis[0, 1].imshow(data.iloc[99].values.reshape(8,8), cmap='gray_r')
axis[0, 2].imshow(data.iloc[199].values.reshape(8,8), cmap='gray_r')
axis[0, 3].imshow(data.iloc[299].values.reshape(8,8), cmap='gray_r')

axis[1, 0].imshow(data.iloc[999].values.reshape(8,8), cmap='gray_r')
axis[1, 1].imshow(data.iloc[1099].values.reshape(8,8), cmap='gray_r')
axis[1, 2].imshow(data.iloc[1199].values.reshape(8,8), cmap='gray_r')
axis[1, 3].imshow(data.iloc[1299].values.reshape(8,8), cmap='gray_r')
```




    <matplotlib.image.AxesImage at 0x9f6dbf4d48>




![png](Basics_files/Basics_7_1.png)


# K-Nearest Neighbors Model


```python
from sklearn.neighbors import KNeighborsClassifier
from sklearn.model_selection import KFold

# Create training model using training features and labels with defined nearest neighbors
def train(nneighbors,train_features,train_labels):
    knn = KNeighborsClassifier(n_neighbors=nneighbors)
    knn.fit(train_features, train_labels)
    return knn

# Create test model to generate predictions
def test(model,test_features,test_labels):
    predictions = model.predict(test_features)
    model_predict_df = pd.DataFrame()
    model_predict_df['correct_label'] = test_labels # true values
    model_predict_df['predicted_label'] = predictions
    # Compute overal accuracy where the predictions match the true value
    accuracy = sum(model_predict_df['predicted_label'] == model_predict_df['correct_label']) / len(model_predict_df)
    return accuracy

def cross_validate(k):
    accuracies = []
    kf = KFold(n_splits = 4, random_state = 2)
    for train_index, test_index in kf.split(data):
        train_features, test_features = data.loc[train_index], data.loc[test_index]
        train_labels, test_labels = labels.loc[train_index], labels.loc[test_index]
        model = train(k, train_features, train_labels)
        accuracy = test(model, test_features, test_labels)
        accuracies.append(accuracy)
    return accuracies

knn_one_accuracy = cross_validate(1)
np.mean(knn_one_accuracy)
```




    0.9677233358079684




```python
def overall_accuracy(k_values):
    k_mean_accuracies = []
    for k in k_values:
        k_accuracy = cross_validate(k)
        k_mean_accuracy = np.mean(k_accuracy)
        k_mean_accuracies.append(k_mean_accuracy)
    return k_mean_accuracies

k_values = list(range(1,10))
k_mean_accuracies = overall_accuracy(k_values)

plt.figure(figsize=(8,4))
plt.title('Mean Accuracy vs k')
plt.plot(k_values, k_mean_accuracies)
```




    [<matplotlib.lines.Line2D at 0x9f6e1e9ec8>]




![png](Basics_files/Basics_10_1.png)


# Neural Network with One Hidden Layer


```python
from sklearn.neighbors import KNeighborsClassifier
from sklearn.model_selection import KFold
from sklearn.neural_network import MLPClassifier

def train_nn(neuron_arch, train_features, train_labels):
    mlp = MLPClassifier(hidden_layer_sizes=neuron_arch)
    mlp.fit(train_features, train_labels)
    return mlp

# Create test model to generate predictions
def test(model,test_features,test_labels):
    predictions = model.predict(test_features)
    model_predict_df = pd.DataFrame()
    model_predict_df['correct_label'] = test_labels # true values
    model_predict_df['predicted_label'] = predictions
    # Compute overal accuracy where the predictions match the true value
    accuracy = sum(model_predict_df['predicted_label'] == model_predict_df['correct_label']) / len(model_predict_df)
    return accuracy

def cross_validate(neuron_arch):
    nn_accuracies = []
    kf = KFold(n_splits = 4, random_state = 2)
    for train_index, test_index in kf.split(data):
        train_features, test_features = data.loc[train_index], data.loc[test_index]
        train_labels, test_labels = labels.loc[train_index], labels.loc[test_index]
        model = train_nn(neuron_arch, train_features, train_labels)
        nn_accuracy = test(model, test_features, test_labels)
        nn_accuracies.append(nn_accuracy)
    return nn_accuracies
```


```python
nn_one_neurons = [(8,),(16,),(32,),(64,),(128,),(256,)]

def overall_accuracy_nn(nn_values):
    nn_one_accuracies = []
    for n in nn_values:
        nn_accuracy = cross_validate(n)
        nn_mean_accuracy = np.mean(nn_accuracy)
        nn_one_accuracies.append(nn_mean_accuracy)
    return nn_one_accuracies

nn_one_mean_accuracies = overall_accuracy_nn(nn_one_neurons)
plt.figure(figsize=(8,4))
plt.title('Mean Accuracy vs Neurons in a Single Layer')
x = [i[0] for i in nn_one_neurons]
plt.plot(x, nn_one_mean_accuracies)
```

    C:\Users\Mobin\Anaconda3\lib\site-packages\sklearn\neural_network\multilayer_perceptron.py:566: ConvergenceWarning: Stochastic Optimizer: Maximum iterations (200) reached and the optimization hasn't converged yet.
      % self.max_iter, ConvergenceWarning)
    C:\Users\Mobin\Anaconda3\lib\site-packages\sklearn\neural_network\multilayer_perceptron.py:566: ConvergenceWarning: Stochastic Optimizer: Maximum iterations (200) reached and the optimization hasn't converged yet.
      % self.max_iter, ConvergenceWarning)
    C:\Users\Mobin\Anaconda3\lib\site-packages\sklearn\neural_network\multilayer_perceptron.py:566: ConvergenceWarning: Stochastic Optimizer: Maximum iterations (200) reached and the optimization hasn't converged yet.
      % self.max_iter, ConvergenceWarning)
    C:\Users\Mobin\Anaconda3\lib\site-packages\sklearn\neural_network\multilayer_perceptron.py:566: ConvergenceWarning: Stochastic Optimizer: Maximum iterations (200) reached and the optimization hasn't converged yet.
      % self.max_iter, ConvergenceWarning)
    C:\Users\Mobin\Anaconda3\lib\site-packages\sklearn\neural_network\multilayer_perceptron.py:566: ConvergenceWarning: Stochastic Optimizer: Maximum iterations (200) reached and the optimization hasn't converged yet.
      % self.max_iter, ConvergenceWarning)
    C:\Users\Mobin\Anaconda3\lib\site-packages\sklearn\neural_network\multilayer_perceptron.py:566: ConvergenceWarning: Stochastic Optimizer: Maximum iterations (200) reached and the optimization hasn't converged yet.
      % self.max_iter, ConvergenceWarning)
    C:\Users\Mobin\Anaconda3\lib\site-packages\sklearn\neural_network\multilayer_perceptron.py:566: ConvergenceWarning: Stochastic Optimizer: Maximum iterations (200) reached and the optimization hasn't converged yet.
      % self.max_iter, ConvergenceWarning)
    C:\Users\Mobin\Anaconda3\lib\site-packages\sklearn\neural_network\multilayer_perceptron.py:566: ConvergenceWarning: Stochastic Optimizer: Maximum iterations (200) reached and the optimization hasn't converged yet.
      % self.max_iter, ConvergenceWarning)
    C:\Users\Mobin\Anaconda3\lib\site-packages\sklearn\neural_network\multilayer_perceptron.py:566: ConvergenceWarning: Stochastic Optimizer: Maximum iterations (200) reached and the optimization hasn't converged yet.
      % self.max_iter, ConvergenceWarning)
    C:\Users\Mobin\Anaconda3\lib\site-packages\sklearn\neural_network\multilayer_perceptron.py:566: ConvergenceWarning: Stochastic Optimizer: Maximum iterations (200) reached and the optimization hasn't converged yet.
      % self.max_iter, ConvergenceWarning)
    C:\Users\Mobin\Anaconda3\lib\site-packages\sklearn\neural_network\multilayer_perceptron.py:566: ConvergenceWarning: Stochastic Optimizer: Maximum iterations (200) reached and the optimization hasn't converged yet.
      % self.max_iter, ConvergenceWarning)
    




    [<matplotlib.lines.Line2D at 0x9f6e28b348>]




![png](Basics_files/Basics_13_2.png)


Increasing the number of neurons in a single hidden layer neural network increased the accuracy from 86% to about 95%.  However, the K-Nearest Neighbors model also had a mean accuracy of 96% so there doesn't seem to be a beneift to using a single hidden layer neural network

# Neural Network with Two Hidden Layers


```python
nn_two_neurons = [(64,64),(128,128),(256,256)]

nn_two_mean_accuracies = overall_accuracy_nn(nn_two_neurons)
plt.figure(figsize=(8,4))
plt.title('Mean Accuracy vs Neurons in Two Hidden Layers')
x = [i[0] for i in nn_two_neurons]
plt.plot(x, nn_two_mean_accuracies)
```




    [<matplotlib.lines.Line2D at 0x9f6f840508>]




![png](Basics_files/Basics_16_1.png)



```python
nn_two_mean_accuracies
```




    [0.9493603068547388, 0.9465738678544914, 0.9577023014105419]



Using two hidden layers improved accuracy to 95%.  While overfitting is a concern using a four fold cross validation provide reassurance that the model is generalizing properly

# Neural Network with Three Hidden Layers


```python
from sklearn.neighbors import KNeighborsClassifier
from sklearn.model_selection import KFold
from sklearn.neural_network import MLPClassifier

def train_nn(neuron_arch, train_features, train_labels):
    mlp = MLPClassifier(hidden_layer_sizes=neuron_arch)
    mlp.fit(train_features, train_labels)
    return mlp

# Create test model to generate predictions
def test(model,test_features,test_labels):
    predictions = model.predict(test_features)
    model_predict_df = pd.DataFrame()
    model_predict_df['correct_label'] = test_labels # true values
    model_predict_df['predicted_label'] = predictions
    # Compute overal accuracy where the predictions match the true value
    accuracy = sum(model_predict_df['predicted_label'] == model_predict_df['correct_label']) / len(model_predict_df)
    return accuracy

def cross_validate_six(neuron_arch):
    nn_accuracies = []
    kf = KFold(n_splits = 6, random_state = 2)
    for train_index, test_index in kf.split(data):
        train_features, test_features = data.loc[train_index], data.loc[test_index]
        train_labels, test_labels = labels.loc[train_index], labels.loc[test_index]
        model = train_nn(neuron_arch, train_features, train_labels)
        nn_accuracy = test(model, test_features, test_labels)
        nn_accuracies.append(nn_accuracy)
    return nn_accuracies

def overall_accuracy_nn_six(nn_values):
    nn_mean_accuracies = []
    for n in nn_values:
        nn_accuracy = cross_validate_six(n)
        nn_mean_accuracy = np.mean(nn_accuracy)
        nn_mean_accuracies.append(nn_mean_accuracy)
    return nn_mean_accuracies
```


```python
nn_three_neurons = [(10,10,10),(64,64,64),(128,128,128)]

nn_three_mean_accuracies = overall_accuracy_nn_six(nn_three_neurons)
plt.figure(figsize=(8,4))
plt.title('Mean Accuracy vs Neurons in Three Hidden Layers')
x = [i[0] for i in nn_three_neurons]
plt.plot(x, nn_three_mean_accuracies)
```

    C:\Users\Mobin\Anaconda3\lib\site-packages\sklearn\neural_network\multilayer_perceptron.py:566: ConvergenceWarning: Stochastic Optimizer: Maximum iterations (200) reached and the optimization hasn't converged yet.
      % self.max_iter, ConvergenceWarning)
    C:\Users\Mobin\Anaconda3\lib\site-packages\sklearn\neural_network\multilayer_perceptron.py:566: ConvergenceWarning: Stochastic Optimizer: Maximum iterations (200) reached and the optimization hasn't converged yet.
      % self.max_iter, ConvergenceWarning)
    C:\Users\Mobin\Anaconda3\lib\site-packages\sklearn\neural_network\multilayer_perceptron.py:566: ConvergenceWarning: Stochastic Optimizer: Maximum iterations (200) reached and the optimization hasn't converged yet.
      % self.max_iter, ConvergenceWarning)
    C:\Users\Mobin\Anaconda3\lib\site-packages\sklearn\neural_network\multilayer_perceptron.py:566: ConvergenceWarning: Stochastic Optimizer: Maximum iterations (200) reached and the optimization hasn't converged yet.
      % self.max_iter, ConvergenceWarning)
    C:\Users\Mobin\Anaconda3\lib\site-packages\sklearn\neural_network\multilayer_perceptron.py:566: ConvergenceWarning: Stochastic Optimizer: Maximum iterations (200) reached and the optimization hasn't converged yet.
      % self.max_iter, ConvergenceWarning)
    C:\Users\Mobin\Anaconda3\lib\site-packages\sklearn\neural_network\multilayer_perceptron.py:566: ConvergenceWarning: Stochastic Optimizer: Maximum iterations (200) reached and the optimization hasn't converged yet.
      % self.max_iter, ConvergenceWarning)
    




    [<matplotlib.lines.Line2D at 0x9f6b27adc8>]




![png](Basics_files/Basics_21_2.png)



```python
nn_three_mean_accuracies
```




    [0.9003920475659607, 0.9504663693794129, 0.957718320327016]



Using three hidden layers improved the model to 96% even with 6-fold cross validation which is in line with what is in literature.  We can add more layers and more neurons to improve the network performance
