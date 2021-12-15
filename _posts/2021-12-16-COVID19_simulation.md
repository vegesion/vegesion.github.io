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

SEIR 모델을 전세계적으로 유행 중인 COVID-19에 적용(델타 변이 x)  

R0 = 2.5  
incubation period = 4  
infectious period = 8  

모든 초기 정보는 출처가 CDC.  
R0의 경우 CDC가 간주한 다섯 가지 시나리오 중 현재에 가장 적합한 시나리오 5를 채택하였음  

## 결과


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



