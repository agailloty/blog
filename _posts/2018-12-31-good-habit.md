---
layout:            post
title:             "Une petite introduction aux bonnes manières"
menutitle:         "Content styles"
category:          Data Science
author:            agailloty
tags:              Python
excerpt_separator: "<!--more-->"
---
# Good Data scientist habit

I am writing this notebook because I find it important to reference myself when using the Python programming language for data science task. The goal here is to prepare a kind of `template` which I will use when dealing with some problems in future. 

I've so far learnt a lot about Data Science and Python but I want to make use of these skills in the best way I could imagine. The idea of writing this notebook comes as I read great Kernels on Kaggle. I want to extract knowledge from these kernels and keep them for myself.
<!--more-->

```python
# If we want to deal with the environment we can import the os module
import os
```


```python
# To check files that are in the directory
print(os.listdir())
```

    ['.DS_Store', '.ipynb_checkpoints', 'data_preprocessing_template.py', 'data_preprocessing_template.R', 'Good habit wth Python.ipynb', 'Polynomial Regression with Py.ipynb', 'polynomial_regression.py', 'polynomial_regression.R', 'Position_Salaries.csv', 'Regression_Template']
    


```python
# import the libraries we'll need
import pandas as pd
import numpy as np
# Visualization libraries
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline
```

When we have a dataframe, it's a good way to print the shape of the dataset in order to present it. 
print("Size of the dataset ": dataset.shape)
# Exploratory Data Analysis

Exploratory Data Analysis (EDA) is an open-ended process where we calculate statistics and make figures to find trends, anomalies, patterns, or relationships within the data. The goal of EDA is to learn what our data can tell us. It generally starts out with a high level overview, then narrows in to specific areas as we find intriguing areas of the data. The findings may be interesting in their own right, or they can be used to inform our modeling choices, such as by helping us decide which features to use.


```python
# Function to calculate missing values by column# Funct 
def missing_values_table(df):
        # Total missing values
        mis_val = df.isnull().sum()
        
        # Percentage of missing values
        mis_val_percent = 100 * df.isnull().sum() / len(df)
        
        # Make a table with the results
        mis_val_table = pd.concat([mis_val, mis_val_percent], axis=1)
        
        # Rename the columns
        mis_val_table_ren_columns = mis_val_table.rename(
        columns = {0 : 'Missing Values', 1 : '% of Total Values'})
        
        # Sort the table by percentage of missing descending
        mis_val_table_ren_columns = mis_val_table_ren_columns[
            mis_val_table_ren_columns.iloc[:,1] != 0].sort_values(
        '% of Total Values', ascending=False).round(1)
        
        # Print some summary information
        print ("Your selected dataframe has " + str(df.shape[1]) + " columns.\n"      
            "There are " + str(mis_val_table_ren_columns.shape[0]) +
              " columns that have missing values.")
        # Return the dataframe with missing information
        return mis_val_table_ren_columns
```

# Column Types
Let's look at the number of columns of each data type. int64 and float64 are numeric variables (which can be either discrete or continuous). object columns contain strings and are categorical features. .


```python
# Number of each type of column
dataset.dtypes.value_counts()
# Number of unique classes in each object column
dataset.select_dtypes('object').apply(pd.Series.nunique, axis = 0)
```


```python
print("There are %0.3f%% of the population concerned" % (100*(78/230)))
```

    There are 33.913% of the population concerned
    


```python
print("We need to wait for %d days before trying attempting" % 36 )
```

    We need to wait for 36 days before trying attempting
    

I just learnt something that must be very interesting for data analysis. It the Pandas  `cut` function that lets us create a new data frame in order to put the values in a bin


```python
data = pd.DataFrame(np.random.normal(15, 8, size = (100,4)), columns= ["First", "Second", "Third", "Fourth"])
```


```python
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
      <th>First</th>
      <th>Second</th>
      <th>Third</th>
      <th>Fourth</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10.518234</td>
      <td>33.030952</td>
      <td>21.052855</td>
      <td>21.325771</td>
    </tr>
    <tr>
      <th>1</th>
      <td>22.228194</td>
      <td>18.375177</td>
      <td>14.307931</td>
      <td>22.436546</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2.662737</td>
      <td>13.922576</td>
      <td>8.017028</td>
      <td>-0.628594</td>
    </tr>
    <tr>
      <th>3</th>
      <td>22.752390</td>
      <td>22.896198</td>
      <td>20.347720</td>
      <td>11.623985</td>
    </tr>
    <tr>
      <th>4</th>
      <td>16.598792</td>
      <td>20.896751</td>
      <td>24.484828</td>
      <td>15.015869</td>
    </tr>
  </tbody>
</table>
</div>



Let now say for a purpose we want to divide the Fourth column in 3 bins : [-10,10[, [10,30[, [30, 100[


```python
data["Bins"] = pd.cut(data["Fourth"], bins= [-10,10,30,100])
```


```python
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
      <th>First</th>
      <th>Second</th>
      <th>Third</th>
      <th>Fourth</th>
      <th>Bins</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>10.518234</td>
      <td>33.030952</td>
      <td>21.052855</td>
      <td>21.325771</td>
      <td>(10, 30]</td>
    </tr>
    <tr>
      <th>1</th>
      <td>22.228194</td>
      <td>18.375177</td>
      <td>14.307931</td>
      <td>22.436546</td>
      <td>(10, 30]</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2.662737</td>
      <td>13.922576</td>
      <td>8.017028</td>
      <td>-0.628594</td>
      <td>(-10, 10]</td>
    </tr>
    <tr>
      <th>3</th>
      <td>22.752390</td>
      <td>22.896198</td>
      <td>20.347720</td>
      <td>11.623985</td>
      <td>(10, 30]</td>
    </tr>
    <tr>
      <th>4</th>
      <td>16.598792</td>
      <td>20.896751</td>
      <td>24.484828</td>
      <td>15.015869</td>
      <td>(10, 30]</td>
    </tr>
  </tbody>
</table>
</div>



Instead of writing the bins by ourselves, we can use numpy functions to automate it


```python
np.linspace(20, 70, num = 11)
```




    array([20., 25., 30., 35., 40., 45., 50., 55., 60., 65., 70.])




```python
np.arange(20, 75, 5)
```




    array([20, 25, 30, 35, 40, 45, 50, 55, 60, 65, 70])




```python
np.linspace(5, 30, 5)
```




    array([ 5.  , 11.25, 17.5 , 23.75, 30.  ])


