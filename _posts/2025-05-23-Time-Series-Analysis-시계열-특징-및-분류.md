---
layout: post
title: 시계열 특징 및 분류 [tsfresh, Logistic Regression]
date: 2025-05-23 19:04:00 +0900
categories: [Time_Series_Analysis]
tags: [Time_Series_Analysis]
---

## 1. 시계열 데이터의 특징

### 1-1. 주요한 시계열 요약 통계 특징

- 평균과 분산
- 최댓값과 최솟값
- 시작과 마지막 값의 차이
- 국소적 최소와 최대의 개수
- 시계열의 평활 정도
    - 평활 정도(Smoothness) : 무작위적 변화로 생기는 효과를 줄이는 방법 중 하나 
        - ex. 주어진 시계열 자료에 평균을 취하는 것은 가장 단순한 평활법
- 시계열의 주기성과 자기상관
    - 시계열 주기성(Time series cycle) : 시계열이 증가하고 감소하는 패턴이 반복적으로 나타나지만, 그 빈도가 고정되지 않았을 때 시계열의 주기가 있다고 말함.

### 1-2. tsfresh

- 시계열의 feature를 자동으로 추출한다.
    - 기술 통계학 지표, 비선형성과 복잡도 지표, 기록 압축 지표 등등 각종 지표를 자동으로 추출
- 논문에 따르면 63개의 시계열 특징 추출 방법론을 활용해 794개의 특징을 포착할 수 있다.
- 현재는 1200개 이상의 특징을 지원한다.


```python
# 1. tsfresh 라이브러리 설치
!pip install tsfresh
```



```python
# 2. 라이브러리 버전 충돌을 피하기 위해 런타임을 재시작합니다.
import os
os.kill(os.getpid(), 9)
```


```python
# 3. robot execution 데이터셋 다운로드 및 불러오기
from tsfresh.examples.robot_execution_failures import download_robot_execution_failures, load_robot_execution_failures
download_robot_execution_failures()
```

4. 불러온 데이터 확인

- timeseries : 데이터셋의 feature. (독립변수)
- y : True, False로 되어있는 binary classification (종속변수)


```python
# 4. 불러온 데이터 확인
timeseries, y = load_robot_execution_failures()
```


```python
timeseries
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
      <th>id</th>
      <th>time</th>
      <th>F_x</th>
      <th>F_y</th>
      <th>F_z</th>
      <th>T_x</th>
      <th>T_y</th>
      <th>T_z</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>0</td>
      <td>-1</td>
      <td>-1</td>
      <td>63</td>
      <td>-3</td>
      <td>-1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1</td>
      <td>0</td>
      <td>0</td>
      <td>62</td>
      <td>-3</td>
      <td>-1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>2</td>
      <td>-1</td>
      <td>-1</td>
      <td>61</td>
      <td>-3</td>
      <td>0</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>3</td>
      <td>-1</td>
      <td>-1</td>
      <td>63</td>
      <td>-2</td>
      <td>-1</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1</td>
      <td>4</td>
      <td>-1</td>
      <td>-1</td>
      <td>63</td>
      <td>-3</td>
      <td>-1</td>
      <td>0</td>
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
    </tr>
    <tr>
      <th>1315</th>
      <td>88</td>
      <td>10</td>
      <td>-10</td>
      <td>2</td>
      <td>39</td>
      <td>-21</td>
      <td>-24</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1316</th>
      <td>88</td>
      <td>11</td>
      <td>-11</td>
      <td>2</td>
      <td>38</td>
      <td>-24</td>
      <td>-22</td>
      <td>6</td>
    </tr>
    <tr>
      <th>1317</th>
      <td>88</td>
      <td>12</td>
      <td>-12</td>
      <td>3</td>
      <td>23</td>
      <td>-24</td>
      <td>-24</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1318</th>
      <td>88</td>
      <td>13</td>
      <td>-13</td>
      <td>4</td>
      <td>26</td>
      <td>-29</td>
      <td>-27</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1319</th>
      <td>88</td>
      <td>14</td>
      <td>-13</td>
      <td>2</td>
      <td>15</td>
      <td>-25</td>
      <td>-25</td>
      <td>6</td>
    </tr>
  </tbody>
</table>
<p>1320 rows × 8 columns</p>
</div>




```python
y
```




    1      True
    2      True
    3      True
    4      True
    5      True
          ...  
    84    False
    85    False
    86    False
    87    False
    88    False
    Length: 88, dtype: bool



5. 특징 추출(feature extraction)

- 그룹화 및 정렬 기준에 맞게 추출할 수 있다.


```python
# 5. 특징 추출(feature extraction)
from tsfresh import extract_features
extracted_features = extract_features(timeseries, column_id="id", column_sort="time")
```

    Feature Extraction: 100%|██████████| 528/528 [00:21<00:00, 24.03it/s]



```python
extracted_features
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
      <th>F_x__variance_larger_than_standard_deviation</th>
      <th>F_x__has_duplicate_max</th>
      <th>F_x__has_duplicate_min</th>
      <th>F_x__has_duplicate</th>
      <th>F_x__sum_values</th>
      <th>F_x__abs_energy</th>
      <th>F_x__mean_abs_change</th>
      <th>F_x__mean_change</th>
      <th>F_x__mean_second_derivative_central</th>
      <th>F_x__median</th>
      <th>...</th>
      <th>T_z__fourier_entropy__bins_5</th>
      <th>T_z__fourier_entropy__bins_10</th>
      <th>T_z__fourier_entropy__bins_100</th>
      <th>T_z__permutation_entropy__dimension_3__tau_1</th>
      <th>T_z__permutation_entropy__dimension_4__tau_1</th>
      <th>T_z__permutation_entropy__dimension_5__tau_1</th>
      <th>T_z__permutation_entropy__dimension_6__tau_1</th>
      <th>T_z__permutation_entropy__dimension_7__tau_1</th>
      <th>T_z__query_similarity_count__query_None__threshold_0.0</th>
      <th>T_z__mean_n_absolute_max__number_of_maxima_7</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>-14.0</td>
      <td>14.0</td>
      <td>0.142857</td>
      <td>0.000000</td>
      <td>-0.038462</td>
      <td>-1.0</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>-0.000000</td>
      <td>-0.000000</td>
      <td>-0.000000</td>
      <td>-0.000000</td>
      <td>-0.000000</td>
      <td>NaN</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>-13.0</td>
      <td>25.0</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>-0.038462</td>
      <td>-1.0</td>
      <td>...</td>
      <td>1.073543</td>
      <td>1.494175</td>
      <td>2.079442</td>
      <td>0.937156</td>
      <td>1.234268</td>
      <td>1.540306</td>
      <td>1.748067</td>
      <td>1.831020</td>
      <td>NaN</td>
      <td>0.571429</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>-10.0</td>
      <td>12.0</td>
      <td>0.714286</td>
      <td>0.000000</td>
      <td>-0.038462</td>
      <td>-1.0</td>
      <td>...</td>
      <td>1.386294</td>
      <td>1.732868</td>
      <td>2.079442</td>
      <td>1.265857</td>
      <td>1.704551</td>
      <td>2.019815</td>
      <td>2.163956</td>
      <td>2.197225</td>
      <td>NaN</td>
      <td>0.571429</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>-6.0</td>
      <td>16.0</td>
      <td>1.214286</td>
      <td>-0.071429</td>
      <td>-0.038462</td>
      <td>0.0</td>
      <td>...</td>
      <td>1.073543</td>
      <td>1.494175</td>
      <td>2.079442</td>
      <td>1.156988</td>
      <td>1.907284</td>
      <td>2.397895</td>
      <td>2.302585</td>
      <td>2.197225</td>
      <td>NaN</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>5</th>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>-9.0</td>
      <td>17.0</td>
      <td>0.928571</td>
      <td>-0.071429</td>
      <td>0.038462</td>
      <td>-1.0</td>
      <td>...</td>
      <td>0.900256</td>
      <td>1.320888</td>
      <td>2.079442</td>
      <td>1.156988</td>
      <td>1.863680</td>
      <td>2.271869</td>
      <td>2.302585</td>
      <td>2.197225</td>
      <td>NaN</td>
      <td>0.857143</td>
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
      <th>84</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>-1073.0</td>
      <td>96833.0</td>
      <td>7.142857</td>
      <td>-5.428571</td>
      <td>-0.038462</td>
      <td>-98.0</td>
      <td>...</td>
      <td>0.735622</td>
      <td>0.735622</td>
      <td>1.386294</td>
      <td>1.585771</td>
      <td>2.253858</td>
      <td>2.397895</td>
      <td>2.302585</td>
      <td>2.197225</td>
      <td>NaN</td>
      <td>24.285714</td>
    </tr>
    <tr>
      <th>85</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>143.0</td>
      <td>1683.0</td>
      <td>1.357143</td>
      <td>1.071429</td>
      <td>0.076923</td>
      <td>8.0</td>
      <td>...</td>
      <td>0.735622</td>
      <td>0.735622</td>
      <td>1.667462</td>
      <td>1.332245</td>
      <td>1.589027</td>
      <td>1.893788</td>
      <td>2.163956</td>
      <td>2.197225</td>
      <td>NaN</td>
      <td>5.571429</td>
    </tr>
    <tr>
      <th>86</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>961.0</td>
      <td>83497.0</td>
      <td>9.071429</td>
      <td>9.071429</td>
      <td>0.807692</td>
      <td>52.0</td>
      <td>...</td>
      <td>0.735622</td>
      <td>1.073543</td>
      <td>1.732868</td>
      <td>0.687092</td>
      <td>0.983088</td>
      <td>1.159589</td>
      <td>1.227529</td>
      <td>1.303092</td>
      <td>NaN</td>
      <td>9.285714</td>
    </tr>
    <tr>
      <th>87</th>
      <td>1.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>4509.0</td>
      <td>1405437.0</td>
      <td>12.928571</td>
      <td>12.214286</td>
      <td>-1.038462</td>
      <td>338.0</td>
      <td>...</td>
      <td>0.735622</td>
      <td>0.735622</td>
      <td>1.386294</td>
      <td>0.535961</td>
      <td>0.836988</td>
      <td>1.159589</td>
      <td>1.497866</td>
      <td>1.581094</td>
      <td>NaN</td>
      <td>40.285714</td>
    </tr>
    <tr>
      <th>88</th>
      <td>1.0</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>-143.0</td>
      <td>1427.0</td>
      <td>0.785714</td>
      <td>-0.500000</td>
      <td>0.038462</td>
      <td>-9.0</td>
      <td>...</td>
      <td>1.255482</td>
      <td>1.494175</td>
      <td>2.079442</td>
      <td>0.830518</td>
      <td>1.242453</td>
      <td>1.414279</td>
      <td>1.609438</td>
      <td>1.831020</td>
      <td>NaN</td>
      <td>5.428571</td>
    </tr>
  </tbody>
</table>
<p>88 rows × 4698 columns</p>
</div>



6. impute

- 특징 추출된 모든 값을 동일한 열의 중앙/극단값으로 바꿉니다.
    - -inf -> min
    - +inf -> max
    - NaN -> median
    
7. select_features(X, y)

- 특징 행렬 X의 모든 특징(열)의 중요성을 확인하고 관련 특징만 포함하는 축소된 특징 행렬을 반환한다.(필터링)


```python
from tsfresh import select_features
from tsfresh.utilities.dataframe_functions import impute

# 6. impute
impute(extracted_features)

# 7. select_features(X, y)
features_filtered = select_features(extracted_features, y)
```


```python
# 8. 필터링된 값 확인
features_filtered
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
      <th>F_x__value_count__value_-1</th>
      <th>F_x__abs_energy</th>
      <th>F_x__root_mean_square</th>
      <th>T_y__absolute_maximum</th>
      <th>F_x__mean_n_absolute_max__number_of_maxima_7</th>
      <th>F_x__range_count__max_1__min_-1</th>
      <th>F_y__abs_energy</th>
      <th>F_y__root_mean_square</th>
      <th>F_y__mean_n_absolute_max__number_of_maxima_7</th>
      <th>T_y__variance</th>
      <th>...</th>
      <th>T_x__change_quantiles__f_agg_"var"__isabs_True__qh_0.2__ql_0.0</th>
      <th>F_z__change_quantiles__f_agg_"mean"__isabs_True__qh_1.0__ql_0.8</th>
      <th>T_x__quantile__q_0.1</th>
      <th>F_y__has_duplicate_max</th>
      <th>T_y__lempel_ziv_complexity__bins_3</th>
      <th>T_y__quantile__q_0.1</th>
      <th>F_z__time_reversal_asymmetry_statistic__lag_1</th>
      <th>F_x__quantile__q_0.2</th>
      <th>F_y__quantile__q_0.7</th>
      <th>T_x__change_quantiles__f_agg_"var"__isabs_False__qh_0.2__ql_0.0</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>14.0</td>
      <td>14.0</td>
      <td>0.966092</td>
      <td>1.0</td>
      <td>1.000000</td>
      <td>15.0</td>
      <td>13.0</td>
      <td>0.930949</td>
      <td>1.000000</td>
      <td>0.222222</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>-3.0</td>
      <td>1.0</td>
      <td>0.400000</td>
      <td>-1.0</td>
      <td>-5.960000e+02</td>
      <td>-1.0</td>
      <td>-1.0</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>7.0</td>
      <td>25.0</td>
      <td>1.290994</td>
      <td>5.0</td>
      <td>1.571429</td>
      <td>13.0</td>
      <td>76.0</td>
      <td>2.250926</td>
      <td>3.000000</td>
      <td>4.222222</td>
      <td>...</td>
      <td>0.000000</td>
      <td>1.0</td>
      <td>-9.2</td>
      <td>1.0</td>
      <td>0.533333</td>
      <td>-3.6</td>
      <td>-6.803846e+02</td>
      <td>-1.0</td>
      <td>-1.0</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>11.0</td>
      <td>12.0</td>
      <td>0.894427</td>
      <td>5.0</td>
      <td>1.000000</td>
      <td>14.0</td>
      <td>40.0</td>
      <td>1.632993</td>
      <td>2.142857</td>
      <td>3.128889</td>
      <td>...</td>
      <td>0.000000</td>
      <td>3.0</td>
      <td>-6.6</td>
      <td>0.0</td>
      <td>0.533333</td>
      <td>-4.0</td>
      <td>-6.170000e+02</td>
      <td>-1.0</td>
      <td>0.0</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5.0</td>
      <td>16.0</td>
      <td>1.032796</td>
      <td>6.0</td>
      <td>1.285714</td>
      <td>10.0</td>
      <td>60.0</td>
      <td>2.000000</td>
      <td>2.428571</td>
      <td>7.128889</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>-9.0</td>
      <td>0.0</td>
      <td>0.533333</td>
      <td>-4.6</td>
      <td>3.426308e+03</td>
      <td>-1.0</td>
      <td>1.0</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>5</th>
      <td>9.0</td>
      <td>17.0</td>
      <td>1.064581</td>
      <td>5.0</td>
      <td>1.285714</td>
      <td>13.0</td>
      <td>46.0</td>
      <td>1.751190</td>
      <td>2.285714</td>
      <td>4.160000</td>
      <td>...</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>-9.6</td>
      <td>0.0</td>
      <td>0.466667</td>
      <td>-5.0</td>
      <td>-2.609000e+03</td>
      <td>-1.0</td>
      <td>0.8</td>
      <td>0.000000</td>
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
      <th>84</th>
      <td>0.0</td>
      <td>96833.0</td>
      <td>80.346334</td>
      <td>167.0</td>
      <td>105.285714</td>
      <td>0.0</td>
      <td>42780.0</td>
      <td>53.404120</td>
      <td>71.428571</td>
      <td>1563.528889</td>
      <td>...</td>
      <td>64.000000</td>
      <td>46.0</td>
      <td>203.2</td>
      <td>0.0</td>
      <td>0.533333</td>
      <td>36.4</td>
      <td>-7.700628e+07</td>
      <td>-105.0</td>
      <td>66.8</td>
      <td>64.000000</td>
    </tr>
    <tr>
      <th>85</th>
      <td>0.0</td>
      <td>1683.0</td>
      <td>10.592450</td>
      <td>14.0</td>
      <td>13.714286</td>
      <td>0.0</td>
      <td>1523.0</td>
      <td>10.076375</td>
      <td>12.142857</td>
      <td>14.755556</td>
      <td>...</td>
      <td>4.666667</td>
      <td>4.5</td>
      <td>-41.6</td>
      <td>0.0</td>
      <td>0.466667</td>
      <td>1.0</td>
      <td>-1.050785e+04</td>
      <td>5.8</td>
      <td>10.6</td>
      <td>13.555556</td>
    </tr>
    <tr>
      <th>86</th>
      <td>0.0</td>
      <td>83497.0</td>
      <td>74.608757</td>
      <td>191.0</td>
      <td>98.142857</td>
      <td>0.0</td>
      <td>21064.0</td>
      <td>37.473546</td>
      <td>47.714286</td>
      <td>2788.595556</td>
      <td>...</td>
      <td>0.250000</td>
      <td>7.0</td>
      <td>-84.8</td>
      <td>0.0</td>
      <td>0.466667</td>
      <td>19.6</td>
      <td>-5.544922e+06</td>
      <td>30.4</td>
      <td>38.4</td>
      <td>0.250000</td>
    </tr>
    <tr>
      <th>87</th>
      <td>0.0</td>
      <td>1405437.0</td>
      <td>306.097697</td>
      <td>471.0</td>
      <td>340.000000</td>
      <td>0.0</td>
      <td>308658.0</td>
      <td>143.447551</td>
      <td>157.285714</td>
      <td>6415.715556</td>
      <td>...</td>
      <td>0.000000</td>
      <td>90.5</td>
      <td>-139.2</td>
      <td>0.0</td>
      <td>0.466667</td>
      <td>272.6</td>
      <td>-9.881845e+07</td>
      <td>246.8</td>
      <td>154.8</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>88</th>
      <td>0.0</td>
      <td>1427.0</td>
      <td>9.753632</td>
      <td>27.0</td>
      <td>11.428571</td>
      <td>0.0</td>
      <td>113.0</td>
      <td>2.744692</td>
      <td>3.428571</td>
      <td>6.906667</td>
      <td>...</td>
      <td>4.666667</td>
      <td>0.0</td>
      <td>-24.6</td>
      <td>0.0</td>
      <td>0.533333</td>
      <td>-26.2</td>
      <td>-1.340477e+04</td>
      <td>-11.2</td>
      <td>3.0</td>
      <td>13.555556</td>
    </tr>
  </tbody>
</table>
<p>88 rows × 671 columns</p>
</div>



## 2. 시계열 데이터 분류

계열 데이터의 특징을 추출하고 시각화해보며, Logistic Regression을 사용하여 모델이 잘 학습(train)되었는지 score를 확인하자.

마지막으로 classification report를 통해 classification 평가지표를 만들어보자.


```python
from tsfresh.examples.robot_execution_failures import download_robot_execution_failures, load_robot_execution_failures


download_robot_execution_failures()
timeseries, y = load_robot_execution_failures()
```


```python
# 추가로 필요한 라이브러리 불러오기
import pandas as pd
import matplotlib.pyplot as plt
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import classification_report
```

### 2-1. 데이터셋 가공


```python
def custom_classification_split(x, y, test_size=0.3):
    num_true = int(y.sum()*test_size)           # int(21 * 0.3) = 6
    num_false = int((len(y)-y.sum())*test_size) # int((88 - 21)*0.3) = 20

    id_list = y[y==False].head(num_false).index.to_list() + y[y==True].head(num_true).index.to_list()
    # y==False인것과 y==True인것의 인덱스값을 리스트로 변환하여 더해줍니다.
    # y[y==False].head(num_false).index.to_list()는 19~38까지의 값이 리스트로
    # y[y==True].head(num_true).index.to_list()는 1~6까지의 값이 리스트로 
    # id_list는 19~38 + 1~6이 더해진 리스트입니다.

    y_train = y.drop(id_list)                            # y에서 id_list를 drop합니다.  
    y_test = y.iloc[id_list].sort_index()                # 19~38, 1~6이 합쳐진 리스트를 정렬합니다.
    X_train = x[~x['id'].isin(id_list)] # 대괄호 안에 있는 timeseries의 id와 id_list가 일치하는 것만 사용하고 물결표시는 안에 조건이 포함되어 있지 않는것만 사용하는 것입니다.
    X_test = x[x['id'].isin(id_list)]   # timeseries의 id와 id_list가 일치하는 것만 사용해서 timeseries에 적용

    return X_train, y_train, X_test, y_test
```


```python
# 커스텀한 함수를 적용한 데이터셋이 어떤 차이가 있는지 확인해봅시다.
X_train, y_train, X_test, y_test = custom_classification_split(timeseries, y)
print(X_train)
print('-'*50)
print(y_train)
print('-'*50)
print(X_test)
print('-'*50)
print(y_test)
```

          id  time  F_x  F_y  F_z  T_x  T_y  T_z
    90     7     0   -3    1   53  -10   -4    0
    91     7     1    0   -2   65   -4   -1    0
    92     7     2   -1   -1   56   -7   -3    0
    93     7     3    0   -2   60   -6    0    0
    94     7     4   -1   -1   57   -7   -4    0
    ...   ..   ...  ...  ...  ...  ...  ...  ...
    1315  88    10  -10    2   39  -21  -24    5
    1316  88    11  -11    2   38  -24  -22    6
    1317  88    12  -12    3   23  -24  -24    5
    1318  88    13  -13    4   26  -29  -27    5
    1319  88    14  -13    2   15  -25  -25    6
    
    [930 rows x 8 columns]
    --------------------------------------------------
    7      True
    8      True
    9      True
    10     True
    11     True
          ...  
    84    False
    85    False
    86    False
    87    False
    88    False
    Length: 62, dtype: bool
    --------------------------------------------------
         id  time  F_x  F_y  F_z  T_x  T_y  T_z
    0     1     0   -1   -1   63   -3   -1    0
    1     1     1    0    0   62   -3   -1    0
    2     1     2   -1   -1   61   -3    0    0
    3     1     3   -1   -1   63   -2   -1    0
    4     1     4   -1   -1   63   -3   -1    0
    ..   ..   ...  ...  ...  ...  ...  ...  ...
    565  38    10   -2   -1   58   -7   -6   -2
    566  38    11   -4    0   60   -8   -9   -1
    567  38    12   -3    0   59   -9   -6   -1
    568  38    13   -1    1   61   -8   -4   -1
    569  38    14    1    0   60   -8   -2   -1
    
    [390 rows x 8 columns]
    --------------------------------------------------
    2      True
    3      True
    4      True
    5      True
    6      True
    7      True
    20    False
    21    False
    22    False
    23    False
    24    False
    25    False
    26    False
    27    False
    28    False
    29    False
    30    False
    31    False
    32    False
    33    False
    34    False
    35    False
    36    False
    37    False
    38    False
    39    False
    dtype: bool


### 2-2. 특징 추출하기


- MinimalFCParameters : feature를 최소(minimal)로 하여 계산을 수행합니다. 데이터셋 크기가 큰 경우 모든 feature를 계산하기 전에 설정을 minimal로 변경하여 빠르게 테스트하기 위해 사용합니다.
    - train과 test 데이터셋을 minimal하게 계산한 값으로 변경해줍니다.


```python
from tsfresh import extract_features
from tsfresh.feature_extraction import MinimalFCParameters

settings = MinimalFCParameters() # 계산 효율을 위해 minimal 셋팅
minimal_features_train = extract_features(
                                X_train,
                                column_id="id",
                                column_sort="time",
                                default_fc_parameters=settings # minimal 적용
                           )

minimal_features_test = extract_features(
                                X_test,
                                column_id="id",
                                column_sort="time",
                                default_fc_parameters=settings # minimal 적용
                           )
```

    Feature Extraction: 100%|██████████| 372/372 [00:00<00:00, 2655.24it/s]
    Feature Extraction: 100%|██████████| 156/156 [00:00<00:00, 2072.65it/s]


### 2-3. 추출된 특징 확인


```python
minimal_features_train
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
      <th>F_x__sum_values</th>
      <th>F_x__median</th>
      <th>F_x__mean</th>
      <th>F_x__length</th>
      <th>F_x__standard_deviation</th>
      <th>F_x__variance</th>
      <th>F_x__root_mean_square</th>
      <th>F_x__maximum</th>
      <th>F_x__absolute_maximum</th>
      <th>F_x__minimum</th>
      <th>...</th>
      <th>T_z__sum_values</th>
      <th>T_z__median</th>
      <th>T_z__mean</th>
      <th>T_z__length</th>
      <th>T_z__standard_deviation</th>
      <th>T_z__variance</th>
      <th>T_z__root_mean_square</th>
      <th>T_z__maximum</th>
      <th>T_z__absolute_maximum</th>
      <th>T_z__minimum</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>7</th>
      <td>-13.0</td>
      <td>-1.0</td>
      <td>-0.866667</td>
      <td>15.0</td>
      <td>0.805536</td>
      <td>0.648889</td>
      <td>1.183216</td>
      <td>0.0</td>
      <td>3.0</td>
      <td>-3.0</td>
      <td>...</td>
      <td>-1.0</td>
      <td>0.0</td>
      <td>-0.066667</td>
      <td>15.0</td>
      <td>0.442217</td>
      <td>0.195556</td>
      <td>0.447214</td>
      <td>1.0</td>
      <td>1.0</td>
      <td>-1.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>-10.0</td>
      <td>-1.0</td>
      <td>-0.666667</td>
      <td>15.0</td>
      <td>1.135292</td>
      <td>1.288889</td>
      <td>1.316561</td>
      <td>2.0</td>
      <td>2.0</td>
      <td>-2.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>15.0</td>
      <td>1.032796</td>
      <td>1.066667</td>
      <td>1.032796</td>
      <td>3.0</td>
      <td>3.0</td>
      <td>-1.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>-10.0</td>
      <td>-1.0</td>
      <td>-0.666667</td>
      <td>15.0</td>
      <td>1.074968</td>
      <td>1.155556</td>
      <td>1.264911</td>
      <td>2.0</td>
      <td>3.0</td>
      <td>-3.0</td>
      <td>...</td>
      <td>3.0</td>
      <td>0.0</td>
      <td>0.200000</td>
      <td>15.0</td>
      <td>1.045626</td>
      <td>1.093333</td>
      <td>1.064581</td>
      <td>3.0</td>
      <td>3.0</td>
      <td>-1.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>-14.0</td>
      <td>-1.0</td>
      <td>-0.933333</td>
      <td>15.0</td>
      <td>0.249444</td>
      <td>0.062222</td>
      <td>0.966092</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>-1.0</td>
      <td>...</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.000000</td>
      <td>15.0</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>0.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>-13.0</td>
      <td>-1.0</td>
      <td>-0.866667</td>
      <td>15.0</td>
      <td>0.956847</td>
      <td>0.915556</td>
      <td>1.290994</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>-3.0</td>
      <td>...</td>
      <td>-3.0</td>
      <td>0.0</td>
      <td>-0.200000</td>
      <td>15.0</td>
      <td>0.400000</td>
      <td>0.160000</td>
      <td>0.447214</td>
      <td>0.0</td>
      <td>1.0</td>
      <td>-1.0</td>
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
      <th>84</th>
      <td>-1073.0</td>
      <td>-98.0</td>
      <td>-71.533333</td>
      <td>15.0</td>
      <td>36.585729</td>
      <td>1338.515556</td>
      <td>80.346334</td>
      <td>-25.0</td>
      <td>110.0</td>
      <td>-110.0</td>
      <td>...</td>
      <td>-232.0</td>
      <td>-21.0</td>
      <td>-15.466667</td>
      <td>15.0</td>
      <td>9.659998</td>
      <td>93.315556</td>
      <td>18.235497</td>
      <td>0.0</td>
      <td>28.0</td>
      <td>-28.0</td>
    </tr>
    <tr>
      <th>85</th>
      <td>143.0</td>
      <td>8.0</td>
      <td>9.533333</td>
      <td>15.0</td>
      <td>4.616877</td>
      <td>21.315556</td>
      <td>10.592450</td>
      <td>19.0</td>
      <td>19.0</td>
      <td>4.0</td>
      <td>...</td>
      <td>-52.0</td>
      <td>-2.0</td>
      <td>-3.466667</td>
      <td>15.0</td>
      <td>2.156128</td>
      <td>4.648889</td>
      <td>4.082483</td>
      <td>0.0</td>
      <td>7.0</td>
      <td>-7.0</td>
    </tr>
    <tr>
      <th>86</th>
      <td>961.0</td>
      <td>52.0</td>
      <td>64.066667</td>
      <td>15.0</td>
      <td>38.235179</td>
      <td>1461.928889</td>
      <td>74.608757</td>
      <td>148.0</td>
      <td>148.0</td>
      <td>21.0</td>
      <td>...</td>
      <td>-81.0</td>
      <td>-8.0</td>
      <td>-5.400000</td>
      <td>15.0</td>
      <td>5.462600</td>
      <td>29.840000</td>
      <td>7.681146</td>
      <td>8.0</td>
      <td>10.0</td>
      <td>-10.0</td>
    </tr>
    <tr>
      <th>87</th>
      <td>4509.0</td>
      <td>338.0</td>
      <td>300.600000</td>
      <td>15.0</td>
      <td>57.753268</td>
      <td>3335.440000</td>
      <td>306.097697</td>
      <td>342.0</td>
      <td>342.0</td>
      <td>171.0</td>
      <td>...</td>
      <td>475.0</td>
      <td>35.0</td>
      <td>31.666667</td>
      <td>15.0</td>
      <td>9.903983</td>
      <td>98.088889</td>
      <td>33.179311</td>
      <td>44.0</td>
      <td>44.0</td>
      <td>13.0</td>
    </tr>
    <tr>
      <th>88</th>
      <td>-143.0</td>
      <td>-9.0</td>
      <td>-9.533333</td>
      <td>15.0</td>
      <td>2.061283</td>
      <td>4.248889</td>
      <td>9.753632</td>
      <td>-6.0</td>
      <td>13.0</td>
      <td>-13.0</td>
      <td>...</td>
      <td>73.0</td>
      <td>5.0</td>
      <td>4.866667</td>
      <td>15.0</td>
      <td>0.884433</td>
      <td>0.782222</td>
      <td>4.946379</td>
      <td>6.0</td>
      <td>6.0</td>
      <td>3.0</td>
    </tr>
  </tbody>
</table>
<p>62 rows × 60 columns</p>
</div>




```python
minimal_features_train.columns
```




    Index(['F_x__sum_values', 'F_x__median', 'F_x__mean', 'F_x__length',
           'F_x__standard_deviation', 'F_x__variance', 'F_x__root_mean_square',
           'F_x__maximum', 'F_x__absolute_maximum', 'F_x__minimum',
           'F_y__sum_values', 'F_y__median', 'F_y__mean', 'F_y__length',
           'F_y__standard_deviation', 'F_y__variance', 'F_y__root_mean_square',
           'F_y__maximum', 'F_y__absolute_maximum', 'F_y__minimum',
           'F_z__sum_values', 'F_z__median', 'F_z__mean', 'F_z__length',
           'F_z__standard_deviation', 'F_z__variance', 'F_z__root_mean_square',
           'F_z__maximum', 'F_z__absolute_maximum', 'F_z__minimum',
           'T_x__sum_values', 'T_x__median', 'T_x__mean', 'T_x__length',
           'T_x__standard_deviation', 'T_x__variance', 'T_x__root_mean_square',
           'T_x__maximum', 'T_x__absolute_maximum', 'T_x__minimum',
           'T_y__sum_values', 'T_y__median', 'T_y__mean', 'T_y__length',
           'T_y__standard_deviation', 'T_y__variance', 'T_y__root_mean_square',
           'T_y__maximum', 'T_y__absolute_maximum', 'T_y__minimum',
           'T_z__sum_values', 'T_z__median', 'T_z__mean', 'T_z__length',
           'T_z__standard_deviation', 'T_z__variance', 'T_z__root_mean_square',
           'T_z__maximum', 'T_z__absolute_maximum', 'T_z__minimum'],
          dtype='object')



### 2-4. 추출된 특징 시각화하기


```python
plt.plot(minimal_features_train['F_x__sum_values'])
plt.show()
```


    
![20250523_output_32_0](/assets/img/posts/20250523_output_32_0.png)
    



```python
plt.plot(minimal_features_train['T_z__maximum'])
plt.show()
```


    
![20250523_output_33_0](/assets/img/posts/20250523_output_33_0.png)
    


### 2-5. Logistic Regression 사용

- 회귀분석이라는 명칭과는 다르게 분류(Classification)를 할 때도 사용할 수 있다.
- 현재 사용중인 robot_execution_failures 데이터셋에서 y가 True와 False로 이루어져 있다.
    - 이진 분류(binary classification) 문제이며 Logistic Regression을 사용할 수 있음.
- Logistic Regression은 어떤 범주에 속할 확률을 0(False)에서 1(True) 사이의 값으로 예측할 수 있다.
- x가 0.5보다 높거나 같은 경우 1, x가 0.5보다 낮은 경우는 0으로 분류함.
    - 여기서 x는 독립변수인 feature라고 생각하시면 된다.






```python
logistic = LogisticRegression() 
logistic.fit(minimal_features_train, y_train)
```




    LogisticRegression()



### 2-6. Logistic Regression score 확인


```python
logistic.score(minimal_features_test, y_test)
```




    0.6923076923076923



### 2-7. 분류 성능 평가 지표 확인

- Classification report는 scikit-learn에서 제공되는 기능
    - 모델 혹은 알고리즘은 score로만 결과를 신뢰할 수 없다. 
        - 여기서 말하는 모델 혹은 알고리즘은 Logistic Regression
    - 모델을 거쳐 나온 결과값(output)을 분석하여 이유가 있고 설명 가능한 상태가 되어야 함.
- Classification의 대표적인 검증 지표로 Precision, Recall, F1-score를 사용
- 아래의 지표를 해석해보면 이진 분류이므로 True와 False 각각 정밀도(Precision), 재현율(Recall), 조화평균(F1-score)의 값을 보여줌.
- 정확도(accuracy)는 score와 같다
- 매크로 평균(macro avg)은 평균의 평균.
    - (각 클래스별 평균 / 클래스 = 매크로 평균) 
        - 여기서 클래스란 minimal_feature_train의 feature
        - 특징 추출 후 나온 feature의 개수는 60개 
- 가중 산술 평균(weighted avg)은 자료의 평균을 구할 때 자료 값의 중요도나 영향 정도에 해당하는 가중치를 반영하여 구한 평균값


```python
# y는 True, False는 Target으로도 표현할 수 있습니다. 
classification_report(y_test, logistic.predict(minimal_features_test), target_names=['true', 'false'], output_dict=True)
# y_test 내부에 있는 true, false와 일치
```




    {'true': {'precision': 1.0,
      'recall': 0.6,
      'f1-score': 0.7499999999999999,
      'support': 20},
     'false': {'precision': 0.42857142857142855,
      'recall': 1.0,
      'f1-score': 0.6,
      'support': 6},
     'accuracy': 0.6923076923076923,
     'macro avg': {'precision': 0.7142857142857143,
      'recall': 0.8,
      'f1-score': 0.6749999999999999,
      'support': 26},
     'weighted avg': {'precision': 0.868131868131868,
      'recall': 0.6923076923076923,
      'f1-score': 0.7153846153846153,
      'support': 26}}



**전체 정확도(Accuracy)**:69.2% — 전체 예측 중 약 69%가 정답.

클래스별 성능:
- true (총 20개 중)
    - 정밀도(Precision): 1.00 → 예측이 맞으면 항상 정답!
    - 재현율(Recall): 0.60 → 실제 true 중 60%만 맞춤
    - F1-score: 0.75 → 정밀도와 재현율 균형 지표

- false (총 6개 중)

    - 정밀도: 0.43 → 예측이 자주 틀림
    - 재현율: 1.00 → 실제 false는 모두 잘 맞춤.
    - F1-score: 0.60

종합 요약:
- 모델은 true를 잘 예측하지만, false는 자주 헷갈림
- 정밀도는 높지만 재현율 불균형


+) 분류 모델 평가 지표 요약

| 지표         | 설명                                                                 |
|--------------|----------------------------------------------------------------------|
| 정확도 (Accuracy) | 전체 중에서 맞춘 비율. 데이터가 불균형할 때는 신뢰하기 어려움.         |
| 정밀도 (Precision) | 양성으로 예측한 것 중 실제로 양성인 비율. 거짓 양성 줄이는 데 중요.    |
| 재현율 (Recall)    | 실제 양성 중에서 양성으로 예측한 비율. 놓치는 양성 줄이는 데 중요.     |
| F1-score     | 정밀도와 재현율의 조화 평균. 불균형 데이터에서 성능 평가에 적합.         |


> 본 문서는 Aiffel LMS 강의 내용을 바탕으로 개인 학습 목적으로 정리하였습니다.  
> 상업적 이용 목적은 없으며, 원 저작권은 Aiffel에 있습니다.
