---
layout : post
author : agailloty
title : "Data visualization with Pandas"
---

```python
import pandas as pd
import seaborn as sns
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
```


```python
sns.set_style('darkgrid')
```


```python
df = pd.read_csv('df1', index_col = 0)
```


```python
df.head(3)
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
      <th>A</th>
      <th>B</th>
      <th>C</th>
      <th>D</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2000-01-01</th>
      <td>1.339091</td>
      <td>-0.163643</td>
      <td>-0.646443</td>
      <td>1.041233</td>
    </tr>
    <tr>
      <th>2000-01-02</th>
      <td>-0.774984</td>
      <td>0.137034</td>
      <td>-0.882716</td>
      <td>-2.253382</td>
    </tr>
    <tr>
      <th>2000-01-03</th>
      <td>-0.921037</td>
      <td>-0.482943</td>
      <td>-0.417100</td>
      <td>0.478638</td>
    </tr>
  </tbody>
</table>
</div>




```python
df2 = pd.read_csv('df2')
```


```python
plt.figure(figsize = (10,6), dpi = 80)
df['A'].hist(bins = 30)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1e875499978>




![png]({{/_posts/notebooks/output_10_1.png}})


```python
df['A'].plot(kind = 'hist', bins = 20)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1e875c96198>




![png]({{/_posts/notebooks/output_10_1.png}})



```python
plt.figure(figsize = (10,6))
df['A'].plot.hist(bins = 30)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1e875f73550>




![png]({{/_posts//notebook/soutput_7_1.png}})



```python
sns.set_context('notebook')
plt.figure(dpi = 100)
df2.plot.area(alpha = 0.5)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1e8763d7240>




    <Figure size 600x400 with 0 Axes>



![png]({{/_posts/blog/notebook/soutput_8_2.png}})



```python
df2.plot.bar(figsize = (12,6))
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1e877cda208>




![png]({{/blog/notebooks/output_9_1.png}})



```python
df2.plot.bar(figsize = (12,6), stacked = True)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1e8778bae80>




![png]({{/blog/notebooks/output_10_1.png}})



```python
df2.plot.area(figsize = (12,6), alpha = 0.5)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1e877bfd0f0>




![png]({{/blog/notebooks/output_11_1.png}})



```python
df.head()
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
      <th>A</th>
      <th>B</th>
      <th>C</th>
      <th>D</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2000-01-01</th>
      <td>1.339091</td>
      <td>-0.163643</td>
      <td>-0.646443</td>
      <td>1.041233</td>
    </tr>
    <tr>
      <th>2000-01-02</th>
      <td>-0.774984</td>
      <td>0.137034</td>
      <td>-0.882716</td>
      <td>-2.253382</td>
    </tr>
    <tr>
      <th>2000-01-03</th>
      <td>-0.921037</td>
      <td>-0.482943</td>
      <td>-0.417100</td>
      <td>0.478638</td>
    </tr>
    <tr>
      <th>2000-01-04</th>
      <td>-1.738808</td>
      <td>-0.072973</td>
      <td>0.056517</td>
      <td>0.015085</td>
    </tr>
    <tr>
      <th>2000-01-05</th>
      <td>-0.905980</td>
      <td>1.778576</td>
      <td>0.381918</td>
      <td>0.291436</td>
    </tr>
  </tbody>
</table>
</div>




```python
df.plot.scatter(x = 'A', y = 'D', figsize = (12,6))
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1e877bfda20>




![png]({{/blog/notebooks/output_13_1.png}})



```python
df.plot.scatter(x = 'A', y = 'D', figsize = (12,6), c = 'B', cmap = 'coolwarm')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1e8781f9ef0>




![png]({{/blog/notebooks/output_14_1.png}})



```python
df2.plot.box(figsize = (12,4))
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1e879868710>




![png]({{/blog/notebooks/output_15_1.png}})



```python
rd = pd.DataFrame({'a': np.random.randn(1000), 'b': np.random.rand(1000)})
```


```python
rd.head()
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
      <th>a</th>
      <th>b</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-1.574936</td>
      <td>0.984074</td>
    </tr>
    <tr>
      <th>1</th>
      <td>-0.012043</td>
      <td>0.033844</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-1.989635</td>
      <td>0.446734</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.564688</td>
      <td>0.807032</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-0.015281</td>
      <td>0.355330</td>
    </tr>
  </tbody>
</table>
</div>




```python
rd.plot.hexbin('a','b', gridsize = 30, figsize = (12,6))
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1e879b81898>




![png]({{/blog/notebooks/output_18_1.png}})



```python
rd.plot.density(figsize = (12,5))
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1e879d217b8>




![png]({{/blog/notebooks/output_19_1.png}})

