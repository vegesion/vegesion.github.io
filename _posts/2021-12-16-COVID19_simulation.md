---
layout: single
title: "COVID-19 SEIR 모델"
excerpt: "코로나 바이러스에 대한 SEIR 모델의 적용"
date: 2021-12-16
toc: true
toc_sticky: true
toc_label: "COVID-19 SEIR model"
---


## 개요

SEIR 모델을 COVID-19의 특성을 반영한 공간에서 구현  

초기 인구 800  
초기 감염자 1  
이동속도 = 2  


## 실행 동영상 

실행 동영상  

{% include video id="HUZ47suI5Kw" provider="youtube" %}



## 결과

![infection_simulation_refined_result1](/assets/images/SEIR_model_simulation.png)


밑의 결정론적 해석으로 그린 그래프와 거의 일치한다


![infection_simulation_refined_result1](/assets/images/SEIR_model_COVID19.png)



## 소스코드

```python

import numpy as np
from scipy.integrate import odeint
import matplotlib.pyplot as plt

u = 0.0 # social distancing

t_incubation = 4 # 평균 지연시간(일), 원래 잠복기 자체는 6일인데 증상 발현 이틀전부터 감염 가능성 확인, 4로 조정
t_infectious = 8
R0 = 2.5

N = 800 # population 시뮬레이션의 인구와 동일하게 지정

# initial number of infectious and recovered individuals
e0 = 1/N
i0 = 0.00
r0 = 0.00
s0 = 1 - e0 - i0 - r0
x0 = [s0,e0,i0,r0]

alpha = 1/t_incubation
gamma = 1/t_infectious
beta = R0*gamma

def covid(x,t):
    s,e,i,r = x
    dx = np.zeros(4)
    dx[0] = -(1-u)*beta*s*i
    dx[1] = (1-u)*beta*s*i - alpha*e
    dx[2] = alpha*e - gamma*i
    dx[3] = gamma*i
    return dx

t = np.linspace(0,300,201)
x = odeint(covid, x0, t)
s = x[:,0]; e = x[:,1]; i = x[:,2]; r = x[:,3]

# 그래프
plt.figure(figsize = (8,5))

plt.title('social distancing = {}%'.format(u*100))
plt.plot(t,s,color = 'blue', lw = 2, label = 'Susceptible')
plt.plot(t,r,color = 'lightgreen', lw = 2, label = 'Recovered')
plt.plot(t,i, color = 'red', lw = 2, label = 'Infectious')
plt.plot(t,e, color = 'purple', lw = 2, label = 'Exposed')
plt.xlabel('Time(days)')
plt.ylabel('Fraction')
plt.grid(linestyle = '--')
plt.legend()
plt.show()


```



