---
layout: post
title: 다양한 데이터 활용법
date: 2025-04-26 16:04:00 +0900
categories: [SQL, Basics]
tags: [data_handling]
---



## 1. 학습 준비


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

## 2. 특정 데이터 조건과 별칭

### 2-1. 특정 데이터 조건 주기(LIKE, Wildcard)

**LIKE** 

- 조회 조건 값이 명확하지 않을 때 특정 내용을 찾을 수 있음.
- 조건에 문자나 숫자를 포함할 수 있다.
- 주로 Wildcard와 함께 사용

**Wildcard** 

- `%` : 조건을 포함하는 모든 문자를 의미한다. (0개 이상의 문자)
- `_` : 한 글자를 의미한다.
    - `_` 의 개수에 따라 뒤에 오는 문자열의 개수를 정할 수 있다.
    - ex. `to_` 라고 지정하면 `tom`은 추출되지만 `tomi`는 추출되지 않음


```python
# hr이 0이고, weekday에 'ur'이란 단어가 들어간 데이터를 추출해보자.

mysql("""

SELECT *
FROM bike
WHERE hr = 0
AND weekday LIKE '%ur%'
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
      <td>0.0000</td>
      <td>3</td>
      <td>13</td>
      <td>16</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2011-01-06</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>0</td>
      <td>0</td>
      <td>Thursday</td>
      <td>1</td>
      <td>Clear</td>
      <td>0.18</td>
      <td>0.2424</td>
      <td>0.55</td>
      <td>0.0000</td>
      <td>0</td>
      <td>11</td>
      <td>11</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2011-01-08</td>
      <td>spring</td>
      <td>2011</td>
      <td>Jan</td>
      <td>0</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Cloudy</td>
      <td>0.18</td>
      <td>0.1970</td>
      <td>0.51</td>
      <td>0.1642</td>
      <td>1</td>
      <td>24</td>
      <td>25</td>
    </tr>
  </tbody>
</table>
</div>



- weathersit이 'Cl'로 시작하는 데이터를 추출해 보자.
    - `AND weathersit LIKE 'Cl%'`
 
    

- mnth이 'ma?'인 데이터를 확인해 보자.
    - `AND mnth LIKE 'ma_'`


```python
# 응용해보자.
# hr이 0이고, mnth이 'ma?'이며 weekday에 'ur'이란 단어가 들어간 데이터를 추출해 보자.

mysql(""" 

SELECT *
FROM bike
WHERE hr = 0
AND mnth LIKE 'ma_'
AND weekday LIKE '%ur%'
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
      <td>2011-03-03</td>
      <td>spring</td>
      <td>2011</td>
      <td>Mar</td>
      <td>0</td>
      <td>0</td>
      <td>Thursday</td>
      <td>1</td>
      <td>Clear</td>
      <td>0.24</td>
      <td>0.1970</td>
      <td>0.30</td>
      <td>0.4627</td>
      <td>3</td>
      <td>10</td>
      <td>13</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2011-03-05</td>
      <td>spring</td>
      <td>2011</td>
      <td>Mar</td>
      <td>0</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Cloudy</td>
      <td>0.28</td>
      <td>0.2879</td>
      <td>0.81</td>
      <td>0.1045</td>
      <td>4</td>
      <td>15</td>
      <td>19</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2011-03-10</td>
      <td>spring</td>
      <td>2011</td>
      <td>Mar</td>
      <td>0</td>
      <td>0</td>
      <td>Thursday</td>
      <td>1</td>
      <td>LightRain</td>
      <td>0.34</td>
      <td>0.3182</td>
      <td>0.00</td>
      <td>0.2537</td>
      <td>3</td>
      <td>0</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2011-03-12</td>
      <td>spring</td>
      <td>2011</td>
      <td>Mar</td>
      <td>0</td>
      <td>0</td>
      <td>Saturday</td>
      <td>0</td>
      <td>Clear</td>
      <td>0.26</td>
      <td>0.2727</td>
      <td>0.60</td>
      <td>0.1343</td>
      <td>4</td>
      <td>30</td>
      <td>34</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2011-03-17</td>
      <td>spring</td>
      <td>2011</td>
      <td>Mar</td>
      <td>0</td>
      <td>0</td>
      <td>Thursday</td>
      <td>1</td>
      <td>Clear</td>
      <td>0.38</td>
      <td>0.3939</td>
      <td>0.66</td>
      <td>0.2537</td>
      <td>4</td>
      <td>19</td>
      <td>23</td>
    </tr>
  </tbody>
</table>
</div>



### 2-2. 별칭 사용하기 (Alias)

- 임시로 컬럼명을 변경할 때 별칭을 사용할 수 있음. 

<div style="background-color:#fff4e5; padding:10px; border-radius:5px; border-left: 5px solid #ff9800; color:black;">
⚠️ <b>WARNING</b><br>
기존의 컬럼명이 물리적으로 변경되는 것은 아님.
</div>



- 별칭은 컬럼명 바로 뒤에 작성함.
- 컬럼명과 별칭 사이에는 AS를 작성합니다. AS는 생략할 수 있음.
- 특수문자, 대소문자, 공백을 사용하려면 따옴표(" " 또는 ' ')로 묶어주어야 함. 
- SELECT, FROM절에서 사용함. 
- 조인과 서브쿼리에서 테이블과 서브쿼리를 호출하기 위해 필수로 사용됨.


```python
# bike테이블 이름을 'b'로 지정하고, b 테이블의 temp와 hum를 추출해보자.
# hum에 'humidity'이란 별칭을 주어보자.

mysql(""" 

SELECT temp, hum AS 'humidity'
FROM bike AS b
LIMIT 5

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
      <th>humidity</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.24</td>
      <td>0.81</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.22</td>
      <td>0.80</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.22</td>
      <td>0.80</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.24</td>
      <td>0.75</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.24</td>
      <td>0.75</td>
    </tr>
  </tbody>
</table>
</div>



---

## 3. 데이터 정렬과 중복 제거 

### 3-1. 데이터 정렬하기(ORDER BY)

**작성 순서**

```
SELECT
FROM
WHERE
ORDER BY
LIMIT
```

- SQL 구문 하단에 작성
- 데이터 추출 시, 테이블에 입력된 순서대로 출력되는 것이 기본
- 데이터가 오름차순, 내림차순으로 정렬된 형태가 필요할 때가 있음.
- 오름차순 : 컬럼명 뒤에 `ASC` 적기.
    - `ASC` 생략해도 오름차순으로 정렬됨.
 
`ORDER BY 컬럼명`

- 내림차순 : 컬럼명 뒤에 `DESC` 적기.

`ORDER BY 컬럼명 DESC`

- `SELECT` 구문에 지정한 컬럼의 순서를 입력해도 실행 가능

` [예시] ORDER BY 1 DESC, 2 `

- [해설] `SELECT`에 작성한 첫번째 컬럼 기준으로 내림차순으로 정렬하고, 같은 값이 있다면 두번째 컬럼 기준으로 정렬됨.



```python
# dteday, temp, cnt 컬럼을 추출하고, cnt가 높은 순서대로 100개만 정렬 해보자.
# 순서가 같은 것이 있다면 temp가 낮은 순서대로 정렬하자

mysql("""

SELECT dteday, temp, cnt
FROM bike
ORDER BY cnt DESC, temp
LIMIT 100

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
      <th>temp</th>
      <th>cnt</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2012-09-12</td>
      <td>0.66</td>
      <td>977</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2012-09-20</td>
      <td>0.64</td>
      <td>976</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2012-09-11</td>
      <td>0.70</td>
      <td>970</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2012-09-10</td>
      <td>0.62</td>
      <td>968</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2012-09-25</td>
      <td>0.66</td>
      <td>967</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>2012-06-11</td>
      <td>0.80</td>
      <td>831</td>
    </tr>
    <tr>
      <th>96</th>
      <td>2012-03-23</td>
      <td>0.70</td>
      <td>830</td>
    </tr>
    <tr>
      <th>97</th>
      <td>2012-07-11</td>
      <td>0.80</td>
      <td>830</td>
    </tr>
    <tr>
      <th>98</th>
      <td>2012-10-11</td>
      <td>0.50</td>
      <td>827</td>
    </tr>
    <tr>
      <th>99</th>
      <td>2012-05-31</td>
      <td>0.76</td>
      <td>827</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 3 columns</p>
</div>




```python
# 컬럼 순서대로 입력하여 정렬
mysql("""

SELECT dteday, temp, cnt
FROM bike
ORDER BY 3 DESC, 2
LIMIT 100

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
      <th>temp</th>
      <th>cnt</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2012-09-12</td>
      <td>0.66</td>
      <td>977</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2012-09-20</td>
      <td>0.64</td>
      <td>976</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2012-09-11</td>
      <td>0.70</td>
      <td>970</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2012-09-10</td>
      <td>0.62</td>
      <td>968</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2012-09-25</td>
      <td>0.66</td>
      <td>967</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>95</th>
      <td>2012-06-11</td>
      <td>0.80</td>
      <td>831</td>
    </tr>
    <tr>
      <th>96</th>
      <td>2012-03-23</td>
      <td>0.70</td>
      <td>830</td>
    </tr>
    <tr>
      <th>97</th>
      <td>2012-07-11</td>
      <td>0.80</td>
      <td>830</td>
    </tr>
    <tr>
      <th>98</th>
      <td>2012-10-11</td>
      <td>0.50</td>
      <td>827</td>
    </tr>
    <tr>
      <th>99</th>
      <td>2012-05-31</td>
      <td>0.76</td>
      <td>827</td>
    </tr>
  </tbody>
</table>
<p>100 rows × 3 columns</p>
</div>



### 3-2. 중복제거하기(DISTINCT)

- 출력에서 고유한 값을 반환하기 위한 키워드
- `DISTINCT` 키워드는 옆에 온 모든 컬럼을 고려하여 중복을 제거함.
- 집계함수에도 `DISTINCT`를 사용할 수 있다. 
    - [참고] 집계함수 : `COUNT`, `SUM`, `AVG`, `MAX`, `MIN`


```python
# weekday, weathersit 컬럼에서 중복을 제거해보자. 

mysql(""" 

SELECT DISTINCT weekday, weathersit
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
      <th>weekday</th>
      <th>weathersit</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Saturday</td>
      <td>Clear</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Saturday</td>
      <td>Cloudy</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Saturday</td>
      <td>LightRain</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Sunday</td>
      <td>Cloudy</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Sunday</td>
      <td>LightRain</td>
    </tr>
  </tbody>
</table>
</div>



---

## 4. 조건문 활용하기(IF, CASE WHEN ~ THEN ~)

### 4-1. IF(조건, 참일 때 반환 값, 거짓일 때 반환 값)

- 단일 조건문
- `SELECT`, `WHERE` 절에서 사용 가능합니다.
- `IF` 내에 조건을 줄 때 함수와 함께 사용 가능합니다.
- [참고] pandasql에서는 `if` 함수를 `iif`로 표현합니다.


```python
#1990년 이전과 이후에 태어난 고객을 분리해 보자.

mysql("""
 
SELECT customerName AS "고객명"
    , birthday AS "생년월일"
    , iif(birthday >= '1990-01-01' , '이후', '이전') AS "1990년 기준"
FROM customers

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
      <th>고객명</th>
      <th>생년월일</th>
      <th>1990년 기준</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Unai Bustinza</td>
      <td>1992. 2. 2</td>
      <td>이후</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Igor Bubnjic</td>
      <td>1992. 7. 17</td>
      <td>이후</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Gideon Jung</td>
      <td>1994. 9. 12</td>
      <td>이후</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Alberto Aquilani</td>
      <td>1984. 7. 7</td>
      <td>이전</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Nicola Strambelli</td>
      <td>1988. 9. 6</td>
      <td>이전</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>118</th>
      <td>Jeroen Vanthournout</td>
      <td>1989. 6. 29</td>
      <td>이전</td>
    </tr>
    <tr>
      <th>119</th>
      <td>Raul Rusescu</td>
      <td>1988. 7. 9</td>
      <td>이전</td>
    </tr>
    <tr>
      <th>120</th>
      <td>Konstantinos Fortounis</td>
      <td>1992. 10. 16</td>
      <td>이후</td>
    </tr>
    <tr>
      <th>121</th>
      <td>Sami</td>
      <td>1988. 12. 18</td>
      <td>이전</td>
    </tr>
    <tr>
      <th>122</th>
      <td>Szymon Drewniak</td>
      <td>1993. 7. 11</td>
      <td>이후</td>
    </tr>
  </tbody>
</table>
<p>123 rows × 3 columns</p>
</div>



### 4-2. CASE WHEN ~ THEN

- 다중 조건문
- 조건에 따른 값을 다르게 출력하고 싶은 경우 사용
    - 조건에 따라 기존에 있는 컬럼을 이용해 새로운 컬럼을 만들기도 함
- 작성 순서

```
CASE WHEN '조건1' THEN '조건1 반환값'
     WHEN '조건2' THEN '조건2 반환값'
     ELSE '만족하는 조건이 없을 때 반환 값'
END
```

- CASE문은 `WHEN`의 조건을 순차적으로 체크하고 조건이 충족되면 `THEN`에 명시된 값을 반환함.
    - `WHEN`과 `THEN`은 한 쌍으로 존재함.
    - `WHEN`과 `THEN`은 여러 개가 존재할 수 있음.
    - `ELSE`는 앞의 조건들에 모두 해당하지 않았을 때 정해줄 기본 값이다.
    - `ELSE`가 없고 조건에 맞는 값이 없으면 `NULL`값을 반환함.

<!-- TIP 박스 -->
<div style="background-color:#e6ffed; padding:10px; border-radius:5px; border-left: 5px solid #4CAF50;">
💡 <b>TIP</b><br>
CASE WHEN, THEN, END를 먼저 작성한 후에 조건을 적으면 문법 실수를 줄일 수 있음. 
</div>




```python
# 고객 한도를 5만원, 십만원 단위로 분리해 보자.

mysql("""
SELECT customerName, creditLimit
   , CASE WHEN creditLimit <= 50000 THEN '5만원이하'
           WHEN creditLimit <= 100000 THEN '10만원이하'
           ELSE '10만원초과'
   END AS '고객한도분리'
FROM customers

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
      <th>customerName</th>
      <th>creditLimit</th>
      <th>고객한도분리</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Unai Bustinza</td>
      <td>21000</td>
      <td>5만원이하</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Igor Bubnjic</td>
      <td>71800</td>
      <td>10만원이하</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Gideon Jung</td>
      <td>117300</td>
      <td>10만원초과</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Alberto Aquilani</td>
      <td>118200</td>
      <td>10만원초과</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Nicola Strambelli</td>
      <td>81700</td>
      <td>10만원이하</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>118</th>
      <td>Jeroen Vanthournout</td>
      <td>60300</td>
      <td>10만원이하</td>
    </tr>
    <tr>
      <th>119</th>
      <td>Raul Rusescu</td>
      <td>43300</td>
      <td>5만원이하</td>
    </tr>
    <tr>
      <th>120</th>
      <td>Konstantinos Fortounis</td>
      <td>85100</td>
      <td>10만원이하</td>
    </tr>
    <tr>
      <th>121</th>
      <td>Sami</td>
      <td>110000</td>
      <td>10만원초과</td>
    </tr>
    <tr>
      <th>122</th>
      <td>Szymon Drewniak</td>
      <td>88900</td>
      <td>10만원이하</td>
    </tr>
  </tbody>
</table>
<p>123 rows × 3 columns</p>
</div>



---

## 5. 데이터 타입 변환하기 (CAST)

### 5-1. CAST

- 데이터의 타입을 변환하는 함수
- 작성 순서

`CAST(컬럼 AS 변환할 타입)`

- 변환할 타입에는 int, float, varchar(문자열), datetime(날짜) 등이 있음. 
    - [예시]
`SELECT CAST(14.58 AS int) //14(결과값)`


```python
# 데이터 bike의 정보를 확인하자.

bike.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 17379 entries, 0 to 17378
    Data columns (total 16 columns):
     #   Column      Non-Null Count  Dtype  
    ---  ------      --------------  -----  
     0   dteday      17379 non-null  object 
     1   season      17379 non-null  object 
     2   yr          17379 non-null  int64  
     3   mnth        17379 non-null  object 
     4   hr          17379 non-null  int64  
     5   holiday     17379 non-null  int64  
     6   weekday     17379 non-null  object 
     7   workingday  17379 non-null  int64  
     8   weathersit  17378 non-null  object 
     9   temp        17379 non-null  float64
     10  atemp       17379 non-null  float64
     11  hum         17379 non-null  float64
     12  windspeed   17379 non-null  float64
     13  casual      17379 non-null  int64  
     14  registered  17379 non-null  int64  
     15  cnt         17379 non-null  int64  
    dtypes: float64(4), int64(7), object(5)
    memory usage: 2.1+ MB
    

`yr`은 int 타입!


```python
# 먼저 2011년 데이터 5개만 추출해 보자.

mysql("""
SELECT *
FROM bike
WHERE yr = 2011 
LIMIT 5
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




```python
# 현재 int 타입인 yr을 varchar 타입으로 변경해 보자.

mysql("""
SELECT CAST(yr AS varchar) AS yr
FROM bike
LIMIT 5
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
      <th>yr</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2011</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2011</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2011</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2011</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2011</td>
    </tr>
  </tbody>
</table>
</div>




```python
# yr을 문자열타입으로 변환한 후 2011년 데이터 5개만 추출해 보자.

mysql("""
SELECT CAST(yr AS varchar) AS yr
FROM bike
WHERE CAST(yr AS varchar)= '2011'
LIMIT 5
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
      <th>yr</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2011</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2011</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2011</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2011</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2011</td>
    </tr>
  </tbody>
</table>
</div>




```python
bike.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 17379 entries, 0 to 17378
    Data columns (total 16 columns):
     #   Column      Non-Null Count  Dtype  
    ---  ------      --------------  -----  
     0   dteday      17379 non-null  object 
     1   season      17379 non-null  object 
     2   yr          17379 non-null  int64  
     3   mnth        17379 non-null  object 
     4   hr          17379 non-null  int64  
     5   holiday     17379 non-null  int64  
     6   weekday     17379 non-null  object 
     7   workingday  17379 non-null  int64  
     8   weathersit  17378 non-null  object 
     9   temp        17379 non-null  float64
     10  atemp       17379 non-null  float64
     11  hum         17379 non-null  float64
     12  windspeed   17379 non-null  float64
     13  casual      17379 non-null  int64  
     14  registered  17379 non-null  int64  
     15  cnt         17379 non-null  int64  
    dtypes: float64(4), int64(7), object(5)
    memory usage: 2.1+ MB
    

-> 하지만 변경은 되지 않았다. 
왜? 

<!-- INFO 박스 -->
<div style="background-color:#e7f3fe; padding:10px; border-radius:5px; border-left: 5px solid #2196F3;">
ℹ️ <b>INFO</b><br>
SQL에서 CAST() 함수는 조회(Query) 시점에서만 데이터를 변환하는 것
    
변경된 타입으로 결과를 보여줄 뿐, 원본 테이블 안에 있는 데이터 자체는 변하지 않음.
</div>
    
