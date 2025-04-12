---
layout: post
title: 데이터 다루기
date: 2025-04-12 17:04:00 +0900
categories: [SQL]
tags: [data_handling]
---

### 1. 학습 준비


```python
!pip install --upgrade pip 
```


```python
# pandasql 라이브러리 설치
!pip3 install pandasql
```


```python
# 데이터와 라이브러리가 준비 되었다면 아래 코드로 데이터가 잘 불러지는지 확인해보자.
import os 
import pandas as pd 
from pandasql import sqldf
# pandasql을 전역 환경으로 지정
mysql = lambda q: sqldf(q, globals())

data_dir = 'C:/Users/daydr/data/sql_basic/'

bike = pd.read_csv(os.path.join(data_dir, 'bike_dataset.csv'))
orders = pd.read_csv(os.path.join(data_dir, 'orders.csv'))
customers = pd.read_csv(os.path.join(data_dir, 'customers.csv'))
employees = pd.read_csv(os.path.join(data_dir,'employees.csv'))
ds_2020 = pd.read_csv(os.path.join(data_dir, 'DS_JobsSalaries_2020.csv'))
ds_2021 = pd.read_csv(os.path.join(data_dir, 'DS_JobsSalaries_2021.csv'))

print("데이터 준비 완료!")
```

    데이터 준비 완료!
    

---

### 2. 쿼리문 작성과 데이터 필터링

#### 2-1. 쿼리문 작성하기(SELECT, FROM) 

**전체 컬럼 조회하기**
- `SELECT` 구문에 `*` 기호를 사용하면 해당 테이블의 모든 컬럼을 조회할 수 있다.

```
SELECT *
FROM 테이블명
```


```python
# bike 테이블에서 전체 컬럼을 조회해보자.
mysql("""

SELECT *
FROM bike
LIMIT 5;

""")
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>0</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>1</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>2</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>3</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>4</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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



#### 2-2. 전체 데이터 필터링하기(WHERE)
- `WHERE`는 `FROM` 절 다음에 위치
- `WHERE` 절에는 연산자를 같이 사용해야함.

```
SELECT 원하는 컬럼
FROM 테이블명
WHERE 조건절
```


```python
# 2012년을 기준으로 데이터를 확인해보자.
mysql("""

SELECT *
FROM bike
WHERE yr = 2012
LIMIT 5;

""")
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
      <td>2012-01-01</td>
      <td>spring</td>
      <td>2012</td>
      <td>Jan</td>
      <td>0</td>
      <td>0</td>
      <td>Sunday</td>
      <td>0</td>
      <td>Clear</td>
      <td>0.36</td>
      <td>0.3788</td>
      <td>0.66</td>
      <td>0.0000</td>
      <td>5</td>
      <td>43</td>
      <td>48</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2012-01-01</td>
      <td>spring</td>
      <td>2012</td>
      <td>Jan</td>
      <td>1</td>
      <td>0</td>
      <td>Sunday</td>
      <td>0</td>
      <td>Clear</td>
      <td>0.36</td>
      <td>0.3485</td>
      <td>0.66</td>
      <td>0.1343</td>
      <td>15</td>
      <td>78</td>
      <td>93</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2012-01-01</td>
      <td>spring</td>
      <td>2012</td>
      <td>Jan</td>
      <td>2</td>
      <td>0</td>
      <td>Sunday</td>
      <td>0</td>
      <td>Clear</td>
      <td>0.32</td>
      <td>0.3485</td>
      <td>0.76</td>
      <td>0.0000</td>
      <td>16</td>
      <td>59</td>
      <td>75</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2012-01-01</td>
      <td>spring</td>
      <td>2012</td>
      <td>Jan</td>
      <td>3</td>
      <td>0</td>
      <td>Sunday</td>
      <td>0</td>
      <td>Clear</td>
      <td>0.30</td>
      <td>0.3333</td>
      <td>0.81</td>
      <td>0.0000</td>
      <td>11</td>
      <td>41</td>
      <td>52</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2012-01-01</td>
      <td>spring</td>
      <td>2012</td>
      <td>Jan</td>
      <td>4</td>
      <td>0</td>
      <td>Sunday</td>
      <td>0</td>
      <td>Clear</td>
      <td>0.28</td>
      <td>0.3030</td>
      <td>0.81</td>
      <td>0.0896</td>
      <td>0</td>
      <td>8</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>



---

### 3. 비교 연산자 사용하기

#### 3-1. 비교 연산자

| 연산자 | 의미 |
| --- | --- |
| = | 같다 |
| !=, <> | 같지 않다 |
| > | 크다 |
| >= | 크거나 같다 |
| < | 작다 |
| <= | 작거나 같다 |


`!=` 또는 `<>` 는 '같지 않다'라는 뜻.
특정 데이터를 제외하여 선택할 때 사용함.


```python
# 계절이 봄이 아닌 전체 데이터를 확인해보자.
mysql("""  

SELECT *
FROM bike
WHERE season != 'spring'
""")
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
      <td>2011-03-21</td>
      <td>summer</td>
      <td>2011</td>
      <td>Mar</td>
      <td>0</td>
      <td>0</td>
      <td>Monday</td>
      <td>1</td>
      <td>LightRain</td>
      <td>0.34</td>
      <td>0.3030</td>
      <td>0.66</td>
      <td>0.3881</td>
      <td>2</td>
      <td>11</td>
      <td>13</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2011-03-21</td>
      <td>summer</td>
      <td>2011</td>
      <td>Mar</td>
      <td>1</td>
      <td>0</td>
      <td>Monday</td>
      <td>1</td>
      <td>Cloudy</td>
      <td>0.34</td>
      <td>0.3030</td>
      <td>0.71</td>
      <td>0.3881</td>
      <td>1</td>
      <td>6</td>
      <td>7</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2011-03-21</td>
      <td>summer</td>
      <td>2011</td>
      <td>Mar</td>
      <td>2</td>
      <td>0</td>
      <td>Monday</td>
      <td>1</td>
      <td>Cloudy</td>
      <td>0.34</td>
      <td>0.3030</td>
      <td>0.71</td>
      <td>0.3284</td>
      <td>1</td>
      <td>5</td>
      <td>6</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2011-03-21</td>
      <td>summer</td>
      <td>2011</td>
      <td>Mar</td>
      <td>3</td>
      <td>0</td>
      <td>Monday</td>
      <td>1</td>
      <td>Cloudy</td>
      <td>0.34</td>
      <td>0.3030</td>
      <td>0.71</td>
      <td>0.3284</td>
      <td>0</td>
      <td>1</td>
      <td>1</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2011-03-21</td>
      <td>summer</td>
      <td>2011</td>
      <td>Mar</td>
      <td>5</td>
      <td>0</td>
      <td>Monday</td>
      <td>1</td>
      <td>Clear</td>
      <td>0.32</td>
      <td>0.3030</td>
      <td>0.81</td>
      <td>0.2985</td>
      <td>1</td>
      <td>1</td>
      <td>2</td>
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
    </tr>
    <tr>
      <th>13132</th>
      <td>2012-12-20</td>
      <td>winter</td>
      <td>2012</td>
      <td>Dec</td>
      <td>19</td>
      <td>0</td>
      <td>Thursday</td>
      <td>1</td>
      <td>LightRain</td>
      <td>0.34</td>
      <td>0.3182</td>
      <td>0.71</td>
      <td>0.2537</td>
      <td>3</td>
      <td>115</td>
      <td>118</td>
    </tr>
    <tr>
      <th>13133</th>
      <td>2012-12-20</td>
      <td>winter</td>
      <td>2012</td>
      <td>Dec</td>
      <td>20</td>
      <td>0</td>
      <td>Thursday</td>
      <td>1</td>
      <td>LightRain</td>
      <td>0.34</td>
      <td>0.3333</td>
      <td>0.76</td>
      <td>0.1642</td>
      <td>1</td>
      <td>49</td>
      <td>50</td>
    </tr>
    <tr>
      <th>13134</th>
      <td>2012-12-20</td>
      <td>winter</td>
      <td>2012</td>
      <td>Dec</td>
      <td>21</td>
      <td>0</td>
      <td>Thursday</td>
      <td>1</td>
      <td>LightRain</td>
      <td>0.34</td>
      <td>0.3333</td>
      <td>0.76</td>
      <td>0.1642</td>
      <td>1</td>
      <td>25</td>
      <td>26</td>
    </tr>
    <tr>
      <th>13135</th>
      <td>2012-12-20</td>
      <td>winter</td>
      <td>2012</td>
      <td>Dec</td>
      <td>22</td>
      <td>0</td>
      <td>Thursday</td>
      <td>1</td>
      <td>LightRain</td>
      <td>0.34</td>
      <td>0.3333</td>
      <td>0.87</td>
      <td>0.1940</td>
      <td>1</td>
      <td>20</td>
      <td>21</td>
    </tr>
    <tr>
      <th>13136</th>
      <td>2012-12-20</td>
      <td>winter</td>
      <td>2012</td>
      <td>Dec</td>
      <td>23</td>
      <td>0</td>
      <td>Thursday</td>
      <td>1</td>
      <td>LightRain</td>
      <td>0.40</td>
      <td>0.4091</td>
      <td>0.82</td>
      <td>0.2985</td>
      <td>0</td>
      <td>13</td>
      <td>13</td>
    </tr>
  </tbody>
</table>
<p>13137 rows × 16 columns</p>
</div>



---

### 4. 논리 연산자 사용하기


- 여러가지 조건을 주고 싶을 때 사용
- 논리 연산자를 계속 추가하면 복잡한 조건도 쉽게 필터링 가능

|연산자 | 의미 |
| --- | --- |
| AND | 앞의 조건과 뒤의 조건을 둘 다 만족해야 참 -> 여러개의 조건 존재 |
| OR | 앞의 조건이나 뒤의 조건 중 하나라도 만족해야 참 -> 여러개의 조건 존재 |
| IN | 여러개 값 리스트 중 하나의 값이라도 만족하는 해당 결과를 출력할 때 사용 |
| NOT | 논리 연산자의 의미를 반전시킴 -> 조건을 부정으로 만드는 역할 |


```python
# bike 테이블에서 2011년도 데이터를 추출해 봅시다.
mysql("""

SELECT *
FROM bike
WHERE yr = 2011
LIMIT 5;

""")
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>0</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>1</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>2</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>3</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>4</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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



그렇다면 두가지의 조건을 주고 싶을때는 어떻게 해야할까?

#### 4-1. AND

- 앞의 조건과 뒤의 조건 둘 다를 만족해야 참(True)이다.
- 조건을 계속 추가해야 할 때 사용

```WHERE [조건1] AND [조건2]```


```python
# bike 테이블에서 2011년도 봄시즌의 22시에 발생한 데이터 추출
mysql(""" 

SELECT *
FROM bike
WHERE yr = 2011
AND season = 'spring'
AND hr = 22
LIMIT 5;

""")
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>22</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Cloudy</td>
      <td>0.40</td>
      <td>0.4091</td>
      <td>0.94</td>
      <td>0.2239</td>
      <td>11</td>
      <td>17</td>
      <td>28</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2011-01-02</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>22</td>
      <td>0</td>
      <td>Sunday</td>
      <td>0</td>
      <td>Clear</td>
      <td>0.24</td>
      <td>0.2121</td>
      <td>0.44</td>
      <td>0.2985</td>
      <td>0</td>
      <td>9</td>
      <td>9</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2011-01-03</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>22</td>
      <td>0</td>
      <td>Monday</td>
      <td>1</td>
      <td>Clear</td>
      <td>0.14</td>
      <td>0.1515</td>
      <td>0.69</td>
      <td>0.1343</td>
      <td>0</td>
      <td>20</td>
      <td>20</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2011-01-04</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>22</td>
      <td>0</td>
      <td>Tuesday</td>
      <td>1</td>
      <td>Clear</td>
      <td>0.22</td>
      <td>0.2576</td>
      <td>0.64</td>
      <td>0.0896</td>
      <td>1</td>
      <td>34</td>
      <td>35</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2011-01-05</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>22</td>
      <td>0</td>
      <td>Wednesday</td>
      <td>1</td>
      <td>Clear</td>
      <td>0.18</td>
      <td>0.1970</td>
      <td>0.55</td>
      <td>0.1343</td>
      <td>1</td>
      <td>41</td>
      <td>42</td>
    </tr>
  </tbody>
</table>
</div>



#### 4-2. OR

- 앞의 조건을 만족하거나 뒤의 조건을 만족하거나, 한쪽이라도 만족해야 참(True)이다.
- 조건을 계속 추가해야 할 때 사용한다.

```WHERE [조건1] OR [조건2]```


```python
# bike 테이블에서 2011년 또는 봄시즌의 데이터 추출
mysql("""

SELECT *
FROM bike
WHERE yr = 2011
OR season = 'spring'
LIMIT 5;

""")
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>0</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>1</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>2</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>3</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>4</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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



#### 4-3. IN

- 컬럼이 어떤 값들의 집합에 속할 경우 `IN`을 사용
- 조회하고자 하는 데이터 값이 여러 개일 때 사용
- 데이터 값을 여러 개의 list로 지정할 수 있다.


```python
# IN을 사용하여 1월(Jan), 3월(Mar), 5월(May)에 대한 전체 데이터 추출
mysql("""

SELECT *
FROM bike
WHERE mnth IN ('Jan', 'Mar', 'May') 
LIMIT 5;

""")
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>0</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>1</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>2</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>3</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>4</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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



- `OR`로도 동일한 데이터를 추출할 수 있지만 하나의 컬럼에서 같은 인자를 조건으로 줄 때 `IN`을 사용하는 것이 효율적이다.

#### 4-4. NOT

- 논리 연산자의 의미를 반전시킨다.
 - 조건을 부정으로 만들어 부정 연산자라고도 불림
- 선언한 조건에 대해 반대 결과를 반환한다.


```python
# 1월(Jan), 3월(Mar), 5월(May)이 아닌 전체 데이터 추출
mysql("""

SELECT *
FROM bike
WHERE mnth IN ('Jan', 'Mar', 'May')
LIMIT 5;

""")
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>0</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>1</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>2</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>3</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>4</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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



#### 4-4. NULL

- 값이 지정되지 않아 알 수 없는 값
- NULL은 0이나 공백과는 다르다.
    - 0 : 숫자
    - " "(공백) : 문자열


```python
# 날씨 데이터의 값이 NULL값이 아닌 전체 데이터 추출
mysql("""

SELECT *
FROM bike
WHERE weathersit IS NOT NULL
LIMIT 5;

""")
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>0</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>1</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>2</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>3</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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
      <td>2011-01-01</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>4</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
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



#### 4-5. Between A and B

- A와 B 사이에 값이 존재할 때 사용.
    - 주의할 점 : **A와 B를 포함**
- 두 값의 범우에 해당하는 행을 출력할 때 사용
- 주로 날짜 사이에 간격을 줄 때 자주 사용하는 편
- 최소값과 최대값을 지정한 값의 범위 내에 있는 값들을 검색하기 위해 사용
    - `number Between 3 AND 5`와 `number >= 3 AND number <= 5` 는 동일한 결과를 출력


```python
# 2011년 3월 30일 ~ 31일 사이의 데이터를 확인해보자.
mysql("""

SELECT *
FROM bike
WHERE dteday >= '2011-03-30' 
AND dteday <= '2011-03-31'
LIMIT 5;

""")
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
      <td>2011-03-30</td>
      <td>summer</td>
      <td>2011</td>
      <td>Mar</td>
      <td>0</td>
      <td>0</td>
      <td>Wednesday</td>
      <td>1</td>
      <td>Cloudy</td>
      <td>0.32</td>
      <td>0.3485</td>
      <td>0.57</td>
      <td>0.0</td>
      <td>3</td>
      <td>8</td>
      <td>11</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2011-03-30</td>
      <td>summer</td>
      <td>2011</td>
      <td>Mar</td>
      <td>1</td>
      <td>0</td>
      <td>Wednesday</td>
      <td>1</td>
      <td>Clear</td>
      <td>0.32</td>
      <td>0.3485</td>
      <td>0.39</td>
      <td>0.0</td>
      <td>1</td>
      <td>9</td>
      <td>10</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2011-03-30</td>
      <td>summer</td>
      <td>2011</td>
      <td>Mar</td>
      <td>2</td>
      <td>0</td>
      <td>Wednesday</td>
      <td>1</td>
      <td>Cloudy</td>
      <td>0.32</td>
      <td>0.3485</td>
      <td>0.57</td>
      <td>0.0</td>
      <td>0</td>
      <td>4</td>
      <td>4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2011-03-30</td>
      <td>summer</td>
      <td>2011</td>
      <td>Mar</td>
      <td>3</td>
      <td>0</td>
      <td>Wednesday</td>
      <td>1</td>
      <td>Cloudy</td>
      <td>0.32</td>
      <td>0.3485</td>
      <td>0.49</td>
      <td>0.0</td>
      <td>0</td>
      <td>4</td>
      <td>4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2011-03-30</td>
      <td>summer</td>
      <td>2011</td>
      <td>Mar</td>
      <td>4</td>
      <td>0</td>
      <td>Wednesday</td>
      <td>1</td>
      <td>Cloudy</td>
      <td>0.32</td>
      <td>0.3485</td>
      <td>0.57</td>
      <td>0.0</td>
      <td>1</td>
      <td>0</td>
      <td>1</td>
    </tr>
  </tbody>
</table>
</div>



---

### 5. 산술 연산자 사용하기

- 데이터 값을 계산하고자 할 때 사용
- 산술연산자는 `+`, `-`, `*`, `/` 등이 있다.
    - 우선순위 `()` > `*`, `/` > `+`, `-`
- `SELECT`, `WHERE` 문에서 사용됨.


```python
# dteday 값에 2를 더하고, weekday에 3을 더하면 어떻게 될까?
mysql("""  

SELECT dteday, dteday + 2, weekday , weekday + 3
FROM bike
LIMIT 3;

""")
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
      <th>dteday</th>
      <th>dteday + 2</th>
      <th>weekday</th>
      <th>weekday + 3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2011-01-01</td>
      <td>2013</td>
      <td>Saturday</td>
      <td>3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2011-01-01</td>
      <td>2013</td>
      <td>Saturday</td>
      <td>3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2011-01-01</td>
      <td>2013</td>
      <td>Saturday</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>



> 주의: 위의 코드는 DBMS(Database Management system)에 따라 실행했을 때 에러가 날 수 있기 때문에 날짜나 문자 타입의 경우에는 산술 연산자를 사용하지 않는 것이 좋다.


```python
# 온도(temp)에 +2, -2, *2, /2 추가해보자.
mysql("""
 
SELECT temp
    , temp + 2 
    , temp - 2 
    , temp * 2 
    , temp / 2 
FROM bike
LIMIT 3;

""")
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
      <th>temp</th>
      <th>temp + 2</th>
      <th>temp - 2</th>
      <th>temp * 2</th>
      <th>temp / 2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.24</td>
      <td>2.24</td>
      <td>-1.76</td>
      <td>0.48</td>
      <td>0.12</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.22</td>
      <td>2.22</td>
      <td>-1.78</td>
      <td>0.44</td>
      <td>0.11</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.22</td>
      <td>2.22</td>
      <td>-1.78</td>
      <td>0.44</td>
      <td>0.11</td>
    </tr>
  </tbody>
</table>
</div>


