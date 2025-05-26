---
layout: post
title: 금융 시계열의 특징 [ARIMA]
date: 2025-05-26 17:04:00 +0900
categories: [Time_Series_Analysis]
tags: [Time_Series_Analysis]
---

## 1. 시계열 예측을 위한 대표적인 모델, ARIMA

- 2017년까지 LSTM과 Transformer를 이용한 접근이 ARIMA를 비롯한 통계 모델에게 우세를 점하지 못하고 있음.
- 현재도 복잡성이 낮은 예측문제에서 높은 예측 성능을 보이고 있음.
- ARIMA = AR + Integraion + MA
    - AR (AutoRegressive Model): 자기 회귀 
    - Integration : 차분
    - MA (Moving Average Model): 이동평균


### Auto Regressive Model

- AR(p) 모델은 과거가 미래를 예측한다는 직관적인 사실에 의존하는 모델
    - 과거의 값이 미래를 설명한다.
   
- 특정시점 t의 값은 이전 시점을 구성하는 값들의 함수라는 시계열 과정을 상정함.
- 시계열이 자체적으로 반복되거나 추세가 있는 경우에 강함
- 양의 자기상관이 있으면 AR 모델이 잘 작동

![250526_5.png](/assets/img/posts/250526_5.png)



### Moving Average Model

- MA(q) 모델은 각 시점의 데이터가 최근의 과거 값에 대한 오차항으로 구성된 함수로 표현된 과정에 의존한다고 볼 수 있다.
- 각 오차항은 서로 독립적이다.
    - 오차항 간의 상관관계로 패턴을 잡음
- 이는 과거의 오차(그 시점에 발생한 독립적인 사건)이 현재의 과정에 영향을 미친다고 보는 것이다.
- 단기적인 충격이나 잡음 패턴을 모델링하는 데 유리
- 시계열이 추세 없이 평균 주위에서 변동한다면 효과적

![250526_6.png](/assets/img/posts/250526_6.png)



### ARIMA equation

> 자기회귀 + 이동평균의 조합 + 필요시 차분(d)으로 비정상성 보정까지 하는 강력한 모델

![250526_7.png](/assets/img/posts/250526_7.png)


**ARIMA(p,d,q)**
- `p`: AR lagged values
- `d`: 차분의 개수
- `q`: MA lagged errors

### 파라미터 p, d, q의 적합

박스-젠킨스 법(Box-Jenkins): ACF, PACF 함수를 보며 수동으로 파라미터를 적합시키는 방법

1. 시각화 및 도메인 지식을 바탕으로 초기 파라미터(p,d,q)를 추정
2. 모델 적합 후 시각화 및 성능 평가를 수행
3. 부적합하다고 생각 될 경우 파라미터를 조정
4. 이를 반복

### Auto-Arima와 Overfitting의 가능성
Log likelihood를 이용한 Step-wise 방법인 Auto-Arima가 존재함.

|그래프의 종류|AR(p)|MA(q)|ARMA|
|---|---|---|---|
|ACF plot|천천히 감소<br>점점 과거로 갈수록 영향이 줄어든다. |Lag q 이후 빠르게 감소<br>이전 q개의 오차가 영향을 준다.|가파른 절단 없음|
|PACF plot|Lag p 이후 빠르게 감소<br>딱 p시점 전까지의 과거가 중요하다.|천천히 감소<br>점점 영향이 줄어들긴 하는데 명확한 끊김은 없음.|가파른 절단 없음|

- PACF에서 뚝 끊기면 -> AR
- ACF에서 뚝 끊기면 -> MA
- 둘 다 점점 줄어드는 모습 -> ARMA 또는 복잡한 모델


---
*보충 설명*

| 용어                 | 쉽게 말하면                                     |
| ------------------ | ------------------------------------------ |
| **ACF (자기상관함수)**   | 과거의 값들과 **얼마나 닮았는지**를 보여줌 (멀리 있는 과거까지)     |
| **PACF (편자기상관함수)** | **바로 직전의 과거만 놓고** 얼마나 닮았는지를 보여줌 (중간 값 제거함) |
| **Lag**            | "몇 시점 전?" (예: lag=1 → 1시간 전, 하루 전 등)       |

- 자기의 과거 값이랑 비슷하게 움직임 -> AR 모델
- 예천 오차가 영향을 줌 -> MA 모델
- 이걸 그래프로 쉽게 판단할 수 있게 도와주는 게 ACF / PACF

---


- 하지만 해당 방법을 이용할 경우, 과도하게 복잡한 모델을 적합할 위험성이 존재한다.
- 각 시계열의 도메인과 특징에 따라 다르지만, 일반적으로 차분(d)이 2를 넘어가면 오버피팅으로 보며, AR(p) MA(q)도 3을 넘기지 않는 것이 바람직함.

### 왜 ARIMA는 높은 성능을 보이는 걸까?

**윌드의 정리**
- 정상성 시계열이 결정론적 시계열과 확률론적 시계열의 합으로 작성될 수 있다고 말함
    - 백색잡음의 선형결합으로 표현 가능하다.
- 이 원리를 바탕으로 모델을 적합하는 것은 어렵지만, ARMA 모델을 통해 정상과정에 꽤 가깝게 근사할 수 있다.

## 2. ARIMA의 파생 모델

- ARIMA + seasonality = `SARIMA`
- ARIMA + Time series regression = `ARIMAX` (dynamic regression)
- SARIMA + ARIMAX = `SARIMAX`
- ...

### SARIMA

- ARIMA 모델에 계절성의 요소를 추가한 모델
- 계절(m)에 대한 AR(P), 차분(D), MA(Q)을 ARIMA 모델에 포함시킴으로써 계절성이 있는 데이터도 모델링할 수 있음.

### ARIMAX

- 일반적으로 회귀분석은 시계열 회귀에 적합하지 않음.
    - 그 이유는, 선형회귀는 독립항등분포(iid)를 가정하기 때문에 시계열의 특성과 다르기 때문
- 하지만 회귀의 오차항이 ARIMA 시계열이라고 가정할 경우, 예측 작업에 회귀분석을 보다 손쉽게 적용할 수 있음.


### ARIMA 실습

- 여러 컬럼들 중에 Target(Total orders)를 바탕으로 ARIMA 모델을 Fitting 할 예정


```python
import pandas as pd
import matplotlib.pyplot as plt
from statsmodels.graphics.tsaplots import plot_acf, plot_pacf
from statsmodels.tsa.arima.model import ARIMA
import pmdarima as pm
```


```python
# ③ 데이터 불러오기
df = pd.read_csv('~/aiffel/time_series_basic/Daily_Demand_Forecasting_Orders.csv', delimiter=';')
```


```python
df.columns
```




    Index(['Week of the month (first week, second, third, fourth or fifth week',
           'Day of the week (Monday to Friday)', 'Non-urgent order',
           'Urgent order', 'Order type A', 'Order type B', 'Order type C',
           'Fiscal sector orders', 'Orders from the traffic controller sector',
           'Banking orders (1)', 'Banking orders (2)', 'Banking orders (3)',
           'Target (Total orders)'],
          dtype='object')




```python
data = df['Target (Total orders)']
plt.plot(data)
plt.show()
```


    
![output_18_0](/assets/img/posts/output_18_0.png)
    


- 추세, 계절성을 가지고 있는 것 같지 않다.
- 분산도 튀는 것이 없어보임.


```python
fig, axes = plt.subplots(1, 2, figsize=(14, 4))  # 1행 2열로 나란히 배치

plot_acf(data, ax=axes[0])                       # 왼쪽 그래프
axes[0].set_title("ACF Plot")

plot_pacf(data, ax=axes[1], method='ywm')        # 오른쪽 그래프
axes[1].set_title("PACF Plot")

plt.tight_layout()
plt.show()

```


![output_20_0](/assets/img/posts/output_20_0.png)
    


- 파란색 부분을 넘어가야 유의미함.
    - 유의미한 값이 하나도 없음.
    
- ARIMA를 적용시켜 패턴을 찾아볼까?


```python
# 모델별로 p,d,q를 다르게 적용해보자.
model1 = ARIMA(data, order=(1,0,0))
model2 = ARIMA(data, order=(0,0,1))
model3 = ARIMA(data, order=(1,0,1))

res1 = model1.fit()
res2 = model2.fit()
res3 = model3.fit()
```


```python
print(res1.summary())
```

                                     SARIMAX Results                                 
    =================================================================================
    Dep. Variable:     Target (Total orders)   No. Observations:                   60
    Model:                    ARIMA(1, 0, 0)   Log Likelihood                -354.181
    Date:                   Mon, 26 May 2025   AIC                            714.362
    Time:                           03:08:12   BIC                            720.645
    Sample:                                0   HQIC                           716.819
                                        - 60                                         
    Covariance Type:                     opg                                         
    ==============================================================================
                     coef    std err          z      P>|z|      [0.025      0.975]
    ------------------------------------------------------------------------------
    const        301.0429     17.323     17.378      0.000     267.090     334.995
    ar.L1          0.0804      0.187      0.430      0.667      -0.286       0.447
    sigma2      7848.5795   1363.825      5.755      0.000    5175.532    1.05e+04
    ===================================================================================
    Ljung-Box (L1) (Q):                   0.00   Jarque-Bera (JB):                32.60
    Prob(Q):                              0.99   Prob(JB):                         0.00
    Heteroskedasticity (H):               0.54   Skew:                             1.40
    Prob(H) (two-sided):                  0.18   Kurtosis:                         5.28
    ===================================================================================
    
    Warnings:
    [1] Covariance matrix calculated using the outer product of gradients (complex-step).



```python
print(res2.summary())
```

                                     SARIMAX Results                                 
    =================================================================================
    Dep. Variable:     Target (Total orders)   No. Observations:                   60
    Model:                    ARIMA(0, 0, 1)   Log Likelihood                -354.143
    Date:                   Mon, 26 May 2025   AIC                            714.286
    Time:                           03:08:19   BIC                            720.569
    Sample:                                0   HQIC                           716.743
                                        - 60                                         
    Covariance Type:                     opg                                         
    ==============================================================================
                     coef    std err          z      P>|z|      [0.025      0.975]
    ------------------------------------------------------------------------------
    const        301.3222     17.628     17.094      0.000     266.773     335.872
    ma.L1          0.0982      0.179      0.548      0.583      -0.253       0.449
    sigma2      7837.9609   1382.427      5.670      0.000    5128.453    1.05e+04
    ===================================================================================
    Ljung-Box (L1) (Q):                   0.02   Jarque-Bera (JB):                32.80
    Prob(Q):                              0.88   Prob(JB):                         0.00
    Heteroskedasticity (H):               0.54   Skew:                             1.41
    Prob(H) (two-sided):                  0.17   Kurtosis:                         5.28
    ===================================================================================
    
    Warnings:
    [1] Covariance matrix calculated using the outer product of gradients (complex-step).



```python
print(res3.summary())
```

                                     SARIMAX Results                                 
    =================================================================================
    Dep. Variable:     Target (Total orders)   No. Observations:                   60
    Model:                    ARIMA(1, 0, 1)   Log Likelihood                -354.075
    Date:                   Mon, 26 May 2025   AIC                            716.150
    Time:                           03:08:26   BIC                            724.528
    Sample:                                0   HQIC                           719.427
                                        - 60                                         
    Covariance Type:                     opg                                         
    ==============================================================================
                     coef    std err          z      P>|z|      [0.025      0.975]
    ------------------------------------------------------------------------------
    const        301.2370     17.534     17.180      0.000     266.871     335.603
    ar.L1         -0.2589      1.442     -0.180      0.857      -3.085       2.567
    ma.L1          0.3581      1.375      0.261      0.794      -2.336       3.052
    sigma2      7820.2936   1396.862      5.598      0.000    5082.494    1.06e+04
    ===================================================================================
    Ljung-Box (L1) (Q):                   0.03   Jarque-Bera (JB):                33.62
    Prob(Q):                              0.87   Prob(JB):                         0.00
    Heteroskedasticity (H):               0.54   Skew:                             1.43
    Prob(H) (two-sided):                  0.18   Kurtosis:                         5.31
    ===================================================================================
    
    Warnings:
    [1] Covariance matrix calculated using the outer product of gradients (complex-step).


| 모델               | AIC         | BIC         |
| ---------------- | ----------- | ----------- |
| **ARIMA(1,0,0)** | 714.362     | 720.645     |
| **ARIMA(0,0,1)** | **714.286** | **720.569** |
| ARIMA(1,0,1)     | 716.150     | 724.528     |


- 셋 중에서는 두번째 모델 (ARIMA(0,0,1))이 AIC, BIC 기준으로 가장 적절한 것으로 보임.

---

*보충설명*

- Log-Likelihood (로그 가능도)
    - 모델이 데이터를 얼마나 잘 설명하는지. 값이 클수록 (즉, 덜 음수일수록) 좋음.

- AIC (Akaike Information Criterion)
    - 모델의 성능과 복잡도를 함께 고려한 지표
        - 모델이 데이터를 잘 설명하면서도 얼마나 단순한가를 평가
    - 작을수록 좋음

- BIC (Bayesian Information Criterion)
    - AIC보다 복잡도에 더 큰 패널티를 줌
    - 작을수록 좋음


**AIC vs BIC**

| 기준      | 특징                   | 언제 더 선호?                 |
| ------- | -------------------- | ------------------------ |
| **AIC** | 예측 성능 중시, 일반적으로 더 관대 | 데이터 많고 **예측 정확도**에 집중할 때 |
| **BIC** | 단순성(모델 간소화) 중시       | 변수 선택, **설명력과 해석** 중요할 때 |



```python
predictions = res2.fittedvalues
```


```python
plt.figure()
plt.plot(data)
plt.plot(predictions)
plt.show()
```


    
![output_29_0](/assets/img/posts/output_29_0.png)
    


-  ARIMA 모델의 값과 실제 데이터를 비교해보면 전혀 비슷하지 않음.

Auto ARIMA를 적용하여 나오는 결과와 summary를 통해 나온 결과를 비교, 분석해보자.


```python
model = pm.AutoARIMA(seasonal=False, stepwise=True, suppress_warnings=True, trace=True)
```


```python
res = model.fit(data)
```

    Performing stepwise search to minimize aic
     ARIMA(2,0,2)(0,0,0)[0]             : AIC=inf, Time=0.20 sec
     ARIMA(0,0,0)(0,0,0)[0]             : AIC=862.092, Time=0.01 sec
     ARIMA(1,0,0)(0,0,0)[0]             : AIC=747.489, Time=0.02 sec
     ARIMA(0,0,1)(0,0,0)[0]             : AIC=818.332, Time=0.04 sec
     ARIMA(2,0,0)(0,0,0)[0]             : AIC=741.019, Time=0.04 sec
     ARIMA(3,0,0)(0,0,0)[0]             : AIC=735.047, Time=0.09 sec
     ARIMA(4,0,0)(0,0,0)[0]             : AIC=inf, Time=0.09 sec
     ARIMA(3,0,1)(0,0,0)[0]             : AIC=724.987, Time=0.16 sec
     ARIMA(2,0,1)(0,0,0)[0]             : AIC=723.725, Time=0.19 sec
     ARIMA(1,0,1)(0,0,0)[0]             : AIC=722.186, Time=0.08 sec
     ARIMA(1,0,2)(0,0,0)[0]             : AIC=inf, Time=0.17 sec
     ARIMA(0,0,2)(0,0,0)[0]             : AIC=791.913, Time=0.07 sec
     ARIMA(1,0,1)(0,0,0)[0] intercept   : AIC=716.399, Time=0.09 sec
     ARIMA(0,0,1)(0,0,0)[0] intercept   : AIC=714.286, Time=0.07 sec
     ARIMA(0,0,0)(0,0,0)[0] intercept   : AIC=712.710, Time=0.01 sec
     ARIMA(1,0,0)(0,0,0)[0] intercept   : AIC=714.363, Time=0.06 sec
    
    Best model:  ARIMA(0,0,0)(0,0,0)[0] intercept
    Total fit time: 1.417 seconds


- 최선의 모델은 ARIMA(0,0,0)이었던 것으로 확인됨.
    - ARIMA(0,0,0)은 백색잡음으로서 의미 있는 예측이 불가능. 


```python
print(res.summary())
```

                                   SARIMAX Results                                
    ==============================================================================
    Dep. Variable:                      y   No. Observations:                   60
    Model:                        SARIMAX   Log Likelihood                -354.355
    Date:                Mon, 26 May 2025   AIC                            712.710
    Time:                        03:09:54   BIC                            716.898
    Sample:                             0   HQIC                           714.348
                                     - 60                                         
    Covariance Type:                  opg                                         
    ==============================================================================
                     coef    std err          z      P>|z|      [0.025      0.975]
    ------------------------------------------------------------------------------
    intercept    300.8733     15.236     19.747      0.000     271.011     330.736
    sigma2      7894.7169   1313.366      6.011      0.000    5320.567    1.05e+04
    ===================================================================================
    Ljung-Box (L1) (Q):                   0.33   Jarque-Bera (JB):                31.06
    Prob(Q):                              0.57   Prob(JB):                         0.00
    Heteroskedasticity (H):               0.57   Skew:                             1.36
    Prob(H) (two-sided):                  0.22   Kurtosis:                         5.25
    ===================================================================================
    
    Warnings:
    [1] Covariance matrix calculated using the outer product of gradients (complex-step).

