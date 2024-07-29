---
title: "工作时长和收入间的线性关系 Linear Regression"
description: 用单变量线性回归对KGSS 2021年的调查数据中 收入 与 工作时长 两个变量间的线性关系进行分析
toc: true
authors:
  - jmsu
tags:
  - Linear Regression
  - Python
  - statsmodels
  - Statictic
categories:
series:
date: '2023-06-21'
lastmod: '2023-06-21'
draft: false
---


本文对KGSS 2021年的调查数据中的 Y `RINCOM0`收入变量 和 X `WGWKHR`工作时长间的线性关系进行统计检验



## Data Source
- 分析工具: Python `statsmodels`统计包
- 数据来源: `KGSS` 
 

<br>

[Go statsmodel Document 📄](https://www.statsmodels.org/stable/stats.html)  [Go KGSS Data 📊](https://kgss.skku.edu/kgss/data.do)

## Method

- 假设 Y：`RINCOM0` X：`WGWKHR`
- 使用Python包 statsmodels 中的 OLS 线性回归函数



### Import Library

```python
import statsmodels.api as sm
import statsmodels.graphics.regressionplots as sgr
import pandas as pd
import numpy as np

```

### Load Data

```python
df = pd.read_spss("2003-2021_KGSS_public_v3_20230210.sav", convert_categoricals=False)
df.describe()
```





<div style="overflow: scroll">
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
      <th>YEAR</th>
      <th>RESPID</th>
      <th>FINALWT</th>
      <th>KGSSAGIN</th>
      <th>SURVEYN</th>
      <th>SURVEFRQ</th>
      <th>SEX</th>
      <th>AGE</th>
      <th>MARITAL</th>
      <th>EMPLY</th>
      <th>...</th>
      <th>HHEMPLY</th>
      <th>HHWHYNOE</th>
      <th>HHDNO</th>
      <th>HOMPOP</th>
      <th>SEPAPOP</th>
      <th>UNRELAT</th>
      <th>COHAP</th>
      <th>REGION</th>
      <th>URBAN</th>
      <th>SAMPLEAB</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>20841.000000</td>
      <td>20841.000000</td>
      <td>20841.000000</td>
      <td>20841.000000</td>
      <td>20841.000000</td>
      <td>20841.000000</td>
      <td>20841.000000</td>
      <td>20841.000000</td>
      <td>20841.000000</td>
      <td>20841.000000</td>
      <td>...</td>
      <td>20841.000000</td>
      <td>20841.000000</td>
      <td>20841.000000</td>
      <td>20841.000000</td>
      <td>20841.000000</td>
      <td>20841.000000</td>
      <td>20841.000000</td>
      <td>20841.000000</td>
      <td>20841.000000</td>
      <td>20841.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>2010.049326</td>
      <td>30028.407802</td>
      <td>1.000000</td>
      <td>-0.720215</td>
      <td>-0.709707</td>
      <td>-0.954561</td>
      <td>1.540857</td>
      <td>45.698575</td>
      <td>2.104266</td>
      <td>1.424164</td>
      <td>...</td>
      <td>1.209011</td>
      <td>0.613454</td>
      <td>6.709707</td>
      <td>2.900293</td>
      <td>0.345665</td>
      <td>0.023319</td>
      <td>-0.992083</td>
      <td>3.429538</td>
      <td>0.829327</td>
      <td>-0.605345</td>
    </tr>
    <tr>
      <th>std</th>
      <td>4.841248</td>
      <td>22688.763126</td>
      <td>0.459471</td>
      <td>1.248061</td>
      <td>0.880524</td>
      <td>0.583604</td>
      <td>0.498340</td>
      <td>16.926672</td>
      <td>1.696678</td>
      <td>0.498577</td>
      <td>...</td>
      <td>0.686990</td>
      <td>3.517450</td>
      <td>3.840734</td>
      <td>1.303156</td>
      <td>0.767434</td>
      <td>0.241026</td>
      <td>0.150127</td>
      <td>1.824271</td>
      <td>0.376232</td>
      <td>0.933411</td>
    </tr>
    <tr>
      <th>min</th>
      <td>2003.000000</td>
      <td>101.000000</td>
      <td>0.203211</td>
      <td>-8.000000</td>
      <td>-8.000000</td>
      <td>-8.000000</td>
      <td>1.000000</td>
      <td>-8.000000</td>
      <td>-8.000000</td>
      <td>-8.000000</td>
      <td>...</td>
      <td>-8.000000</td>
      <td>-8.000000</td>
      <td>1.000000</td>
      <td>-8.000000</td>
      <td>-8.000000</td>
      <td>-8.000000</td>
      <td>-1.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>-1.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>2006.000000</td>
      <td>11307.000000</td>
      <td>0.692861</td>
      <td>-1.000000</td>
      <td>-1.000000</td>
      <td>-1.000000</td>
      <td>1.000000</td>
      <td>33.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>...</td>
      <td>1.000000</td>
      <td>-1.000000</td>
      <td>4.000000</td>
      <td>2.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>-1.000000</td>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>-1.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2010.000000</td>
      <td>25312.000000</td>
      <td>0.910562</td>
      <td>-1.000000</td>
      <td>-1.000000</td>
      <td>-1.000000</td>
      <td>2.000000</td>
      <td>44.000000</td>
      <td>1.000000</td>
      <td>1.000000</td>
      <td>...</td>
      <td>1.000000</td>
      <td>-1.000000</td>
      <td>7.000000</td>
      <td>3.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>-1.000000</td>
      <td>4.000000</td>
      <td>1.000000</td>
      <td>-1.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2013.000000</td>
      <td>53405.000000</td>
      <td>1.247668</td>
      <td>-1.000000</td>
      <td>-1.000000</td>
      <td>-1.000000</td>
      <td>2.000000</td>
      <td>58.000000</td>
      <td>3.000000</td>
      <td>2.000000</td>
      <td>...</td>
      <td>1.000000</td>
      <td>-1.000000</td>
      <td>10.000000</td>
      <td>4.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>-1.000000</td>
      <td>5.000000</td>
      <td>1.000000</td>
      <td>-1.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2021.000000</td>
      <td>70124.000000</td>
      <td>4.591092</td>
      <td>2.000000</td>
      <td>2.000000</td>
      <td>10.000000</td>
      <td>2.000000</td>
      <td>99.000000</td>
      <td>6.000000</td>
      <td>2.000000</td>
      <td>...</td>
      <td>2.000000</td>
      <td>20.000000</td>
      <td>24.000000</td>
      <td>11.000000</td>
      <td>9.000000</td>
      <td>9.000000</td>
      <td>2.000000</td>
      <td>7.000000</td>
      <td>1.000000</td>
      <td>2.000000</td>
    </tr>
  </tbody>
</table>
<p>8 rows × 3212 columns</p>
</div>


提取2021年份的数据进行分析

```python
df_2021 = df[df.YEAR==2021]
df_2021
```




<div style="overflow: scroll">
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
      <th>YEAR</th>
      <th>RESPID</th>
      <th>FINALWT</th>
      <th>KGSSAGIN</th>
      <th>KGSSINTR</th>
      <th>KGSSDIFF</th>
      <th>SURVEYN</th>
      <th>SURVEFRQ</th>
      <th>SEX</th>
      <th>AGE</th>
      <th>...</th>
      <th>HHEMPLY</th>
      <th>HHWHYNOE</th>
      <th>HHDNO</th>
      <th>HOMPOP</th>
      <th>SEPAPOP</th>
      <th>UNRELAT</th>
      <th>COHAP</th>
      <th>REGION</th>
      <th>URBAN</th>
      <th>SAMPLEAB</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>19636</th>
      <td>2021.0</td>
      <td>101.0</td>
      <td>0.567077</td>
      <td>1.0</td>
      <td>-1</td>
      <td>-1</td>
      <td>2.0</td>
      <td>-1.0</td>
      <td>2.0</td>
      <td>36.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>-1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>-1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>19637</th>
      <td>2021.0</td>
      <td>102.0</td>
      <td>0.342096</td>
      <td>2.0</td>
      <td>-1</td>
      <td>-1</td>
      <td>2.0</td>
      <td>-1.0</td>
      <td>2.0</td>
      <td>63.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>-1.0</td>
      <td>2.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>19638</th>
      <td>2021.0</td>
      <td>104.0</td>
      <td>0.588853</td>
      <td>1.0</td>
      <td>-1</td>
      <td>-1</td>
      <td>2.0</td>
      <td>-1.0</td>
      <td>2.0</td>
      <td>26.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>-1.0</td>
      <td>4.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>19639</th>
      <td>2021.0</td>
      <td>105.0</td>
      <td>0.689820</td>
      <td>-8.0</td>
      <td>-1</td>
      <td>-1</td>
      <td>-8.0</td>
      <td>-1.0</td>
      <td>1.0</td>
      <td>32.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>-1.0</td>
      <td>5.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>19640</th>
      <td>2021.0</td>
      <td>106.0</td>
      <td>0.850229</td>
      <td>-8.0</td>
      <td>-1</td>
      <td>-1</td>
      <td>2.0</td>
      <td>-1.0</td>
      <td>1.0</td>
      <td>27.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>-1.0</td>
      <td>6.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>20836</th>
      <td>2021.0</td>
      <td>70104.0</td>
      <td>1.009292</td>
      <td>2.0</td>
      <td>-1</td>
      <td>-1</td>
      <td>2.0</td>
      <td>-1.0</td>
      <td>2.0</td>
      <td>35.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>-1.0</td>
      <td>4.0</td>
      <td>4.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-1.0</td>
      <td>4.0</td>
      <td>1.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>20837</th>
      <td>2021.0</td>
      <td>70105.0</td>
      <td>1.107223</td>
      <td>2.0</td>
      <td>-1</td>
      <td>-1</td>
      <td>2.0</td>
      <td>-1.0</td>
      <td>2.0</td>
      <td>47.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>-1.0</td>
      <td>5.0</td>
      <td>4.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-1.0</td>
      <td>4.0</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>20838</th>
      <td>2021.0</td>
      <td>70106.0</td>
      <td>1.604723</td>
      <td>2.0</td>
      <td>-1</td>
      <td>-1</td>
      <td>2.0</td>
      <td>-1.0</td>
      <td>1.0</td>
      <td>58.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>-1.0</td>
      <td>6.0</td>
      <td>4.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-1.0</td>
      <td>4.0</td>
      <td>1.0</td>
      <td>2.0</td>
    </tr>
    <tr>
      <th>20839</th>
      <td>2021.0</td>
      <td>70109.0</td>
      <td>1.255510</td>
      <td>2.0</td>
      <td>-1</td>
      <td>-1</td>
      <td>2.0</td>
      <td>-1.0</td>
      <td>1.0</td>
      <td>33.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>-1.0</td>
      <td>9.0</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-1.0</td>
      <td>4.0</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
    <tr>
      <th>20840</th>
      <td>2021.0</td>
      <td>70111.0</td>
      <td>1.107223</td>
      <td>-8.0</td>
      <td>-1</td>
      <td>-1</td>
      <td>1.0</td>
      <td>-8.0</td>
      <td>2.0</td>
      <td>47.0</td>
      <td>...</td>
      <td>1.0</td>
      <td>-1.0</td>
      <td>11.0</td>
      <td>3.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>-1.0</td>
      <td>4.0</td>
      <td>1.0</td>
      <td>1.0</td>
    </tr>
  </tbody>
</table>
<p>1205 rows × 3215 columns</p>
</div>


### Pre-process

#### WGWKHR


```python
df_2021.WGWKHR.describe()
```




    count    1205.000000
    mean       13.727801
    std        20.376129
    min        -8.000000
    25%        -1.000000
    50%        -1.000000
    75%        40.000000
    max        80.000000
    Name: WGWKHR, dtype: float64


统计异常值总和

```python
sum(df_2021["WGWKHR"]==-8)
```




    7


将异常值替换为空(`np.nan`)

```python
WGWKHR = df_2021["WGWKHR"]
WGWKHR.replace((-8,-1), np.nan, inplace=True)
WGWKHR.describe()
```

    /var/folders/pm/fx5ldf494tqd843k9mpzck740000gn/T/ipykernel_2294/3763845172.py:2: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      WGWKHR.replace((-8,-1), np.nan, inplace=True)





    count    476.000000
    mean      36.386555
    std       14.185602
    min        2.000000
    25%       35.000000
    50%       40.000000
    75%       45.000000
    max       80.000000
    Name: WGWKHR, dtype: float64


#### RINCOM

```python
RINCOM = df_2021["RINCOM0"]
RINCOM.describe()
```




    count    1205.000000
    mean      174.807469
    std       211.070790
    min        -8.000000
    25%        -1.000000
    50%       150.000000
    75%       300.000000
    max      2400.000000
    Name: RINCOM0, dtype: float64


替换异常值


```python
RINCOM.replace((-8,-1), np.nan, inplace=True)
RINCOM.describe()
```

    /var/folders/pm/fx5ldf494tqd843k9mpzck740000gn/T/ipykernel_2294/3581387875.py:1: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame
    
    See the caveats in the documentation: https://pandas.pydata.org/pandas-docs/stable/user_guide/indexing.html#returning-a-view-versus-a-copy
      RINCOM.replace((-8,-1), np.nan, inplace=True)





    count     730.000000
    mean      289.547945
    std       200.331463
    min         0.000000
    25%       190.000000
    50%       250.000000
    75%       350.000000
    max      2400.000000
    Name: RINCOM0, dtype: float64


收入数据方差过大，容易对线性模型产生影响，因此将`RINCOM`log标准化


```python
RINCOM = np.log(RINCOM).replace(-np.inf,0)
RINCOM.describe()

```

    /Users/jmsu/anaconda3/envs/KGSS/lib/python3.10/site-packages/pandas/core/arraylike.py:402: RuntimeWarning: divide by zero encountered in log
      result = getattr(ufunc, method)(*inputs, **kwargs)





    count    730.000000
    mean       5.394227
    std        1.001719
    min        0.000000
    25%        5.247024
    50%        5.521461
    75%        5.857933
    max        7.783224
    Name: RINCOM0, dtype: float64



## Simaple Linear Regression （OLS）

建立模型，拟合并输出结果

```python
model = sm.OLS(RINCOM, sm.add_constant(WGWKHR),missing='drop')
result = model.fit()
result.summary()
```




<table class="simpletable">
<caption>OLS Regression Results</caption>
<tr>
  <th>Dep. Variable:</th>         <td>RINCOM0</td>     <th>  R-squared:         </th> <td>   0.117</td>
</tr>
<tr>
  <th>Model:</th>                   <td>OLS</td>       <th>  Adj. R-squared:    </th> <td>   0.115</td>
</tr>
<tr>
  <th>Method:</th>             <td>Least Squares</td>  <th>  F-statistic:       </th> <td>   60.54</td>
</tr>
<tr>
  <th>Date:</th>             <td>Wed, 21 Jun 2023</td> <th>  Prob (F-statistic):</th> <td>4.81e-14</td>
</tr>
<tr>
  <th>Time:</th>                 <td>18:14:13</td>     <th>  Log-Likelihood:    </th> <td> -464.72</td>
</tr>
<tr>
  <th>No. Observations:</th>      <td>   461</td>      <th>  AIC:               </th> <td>   933.4</td>
</tr>
<tr>
  <th>Df Residuals:</th>          <td>   459</td>      <th>  BIC:               </th> <td>   941.7</td>
</tr>
<tr>
  <th>Df Model:</th>              <td>     1</td>      <th>                     </th>     <td> </td>   
</tr>
<tr>
  <th>Covariance Type:</th>      <td>nonrobust</td>    <th>                     </th>     <td> </td>   
</tr>
</table>
<table class="simpletable">
<tr>
     <td></td>       <th>coef</th>     <th>std err</th>      <th>t</th>      <th>P>|t|</th>  <th>[0.025</th>    <th>0.975]</th>  
</tr>
<tr>
  <th>const</th>  <td>    4.8424</td> <td>    0.086</td> <td>   56.061</td> <td> 0.000</td> <td>    4.673</td> <td>    5.012</td>
</tr>
<tr>
  <th>WGWKHR</th> <td>    0.0172</td> <td>    0.002</td> <td>    7.781</td> <td> 0.000</td> <td>    0.013</td> <td>    0.021</td>
</tr>
</table>
<table class="simpletable">
<tr>
  <th>Omnibus:</th>       <td>306.448</td> <th>  Durbin-Watson:     </th> <td>   1.707</td>
</tr>
<tr>
  <th>Prob(Omnibus):</th> <td> 0.000</td>  <th>  Jarque-Bera (JB):  </th> <td>6747.756</td>
</tr>
<tr>
  <th>Skew:</th>          <td>-2.490</td>  <th>  Prob(JB):          </th> <td>    0.00</td>
</tr>
<tr>
  <th>Kurtosis:</th>      <td>21.069</td>  <th>  Cond. No.          </th> <td>    109.</td>
</tr>
</table><br/><br/>Notes:<br/>[1] Standard Errors assume that the covariance matrix of the errors is correctly specified.


> 可以看出$R^2=0.117$ 说明模型的拟合程度不足，很好解释，因为很明显自变量`工作时长`不是影响因变量`收入`的唯一因素， 需要其他变量的介入，拟合多元线性模型以提高解释能力。

> 根据参数t检验结果，可以看出`工作时长`与`收入`间的存在着显著的线性关系



画个回归拟合图：


```python
sgr.plot_fit(result, 1)
```



  
![output](/imgs/plot_ols1.png)

