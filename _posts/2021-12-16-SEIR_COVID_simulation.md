---
layout: single
title: "COVID-19 SEIR 모델 pygame 시뮬레이션"
excerpt: "코로나 바이러스에 대한 SEIR 모델 적용 시뮬레이션"
date: 2021-12-16
toc: true
toc_sticky: true
toc_label: "COVID-19 SEIR model simulation"
---


## 개요

SEIR 모델을 전세계적으로 유행 중인 COVID-19에 적용(델타 변이 x)  

R0 = 2.5  
incubation period = 4  
infectious period = 8  

모든 초기 정보는 출처가 CDC.  
R0의 경우 CDC가 간주한 다섯 가지 시나리오 중 현재에 가장 적합한 시나리오 5를 채택하였음  



## 실행 동영상 

실행 동영상  

{% include video id="QVb6vhzsmGc" provider="youtube" %}



## 결과

![infection_simulation_refined_result1](/assets/images/SEIR_model_simulation.png)


밑의 결정론적 해석으로 그린 그래프와 거의 일치한다


![infection_simulation_refined_result1](/assets/images/SEIR_model_COVID19.png)


## 소스코드


```python

import time
import pygame as pg
from math import dist
from random import *
import numpy as np
from infection_graph import graph
from glob import glob
import copy


##### 18sec = lDay !!!!!
# text = open('C:/Users/helis/Desktop/python/arc/infection_histogram/a.txt', 'a')

# file_list  = glob("*.py")

c_size_x = 1200
c_size_y = 800


def timeshow():
    return time.time()

age_lim = 50
number = 800
r = 2
start_time = timeshow()
frame = 0

'- <Infection information> ------------------------------------------------------------------------------'

u = 0.0 # social distancing

t_incubation = 4*18*60 # 평균 지연시간 (4일)
t_infective = 8*18*60 # 평균 전염 가능 시간

R0 = 2.5

# initial number of infectious and recovered individuals
e0 = 1/number
i0 = 0.00
r0 = 0.00
s0 = 1 - e0 - i0 - r0
x0 = [s0,e0,i0,r0]

alpha = 1/t_incubation # 감염 가능성
gamma = 1/t_infective # 회복률
beta = R0*gamma # 감염률 


'- <Pygame initation> -----------------------------------------------------------------------------------'

pg.init()
screen = pg.display.set_mode([c_size_x,c_size_y])
title = 'infection_simulation'
pg.display.set_caption(title)

clock = pg.time.Clock()

SB = 0 # 상태함수, 0이 되면 프로그램 종료

'- <CLASS> ----------------------------------------------------------------------------------------------'

class people(object):    
    def __init__(self, r, age, sex, xp, yp, st):   
        self.age = age
        self.sex = sex
        self.p = np.array([xp, yp])
        self.v = 2
        self.st = st # S = 취약군, E = 접촉군, I = 감염군, R = 회복군 
        self.r = r
        self.dst = np.array([randint(0,c_size_x), randint(0,c_size_y)])
        self.since_infectious = 0
        self.since_exposed = 0
        self.c = []


    def color(self):
        if self.st == 'S':
            self.c = [120,120,120]

        elif self.st == 'E':
            self.c = [255,200,0]
        
        elif self.st == 'I':
            self.c = [255,0,0]

        elif self.st == 'R':
            self.c = [100,220,100]

    
    def show(self):
        pg.draw.rect(screen, self.c, [self.p[0]-self.r/2, self.p[1]-self.r/2, self.r, self.r])
    
    def state(self):
        if self.st == 'I':
            self.since_infectious += 1
            if self.since_infectious >= t_infective:
                self.st = 'R'
                ppl_R.append(self)
                ppl_I.remove(self)
            
        
        elif self.st == 'E':
            self.since_exposed += 1
            if self.since_exposed >= t_incubation:
                self.st = 'I'
                ppl_I.append(self)
                ppl_E.remove(self)

             
    def move(self):

        if dist(self.p, self.dst) < self.v:
            self.new_dst()
        else:
            delta_dist = np.array([round((self.dst[0] - self.p[0])*self.v, 1), round((self.dst[1] - self.p[1])*self.v,1)])
            self.p += delta_dist/dist(self.p, self.dst)

    def new_dst(self):
        self.dst = np.array([round(uniform(0, c_size_x),1), round(uniform(0, c_size_y),1)])

            
    def infect(self):
        if self.st == 'I':
            for a in ppl_S:
                if dist([self.p[0], self.p[1]], [a.p[0], a.p[1]]) < r and uniform(0,1) < beta*60: # 감염확률적용
                    if self.st == 'I' and a.st == 'S':
                        a.st = 'E'
                        ppl_S.remove(a)
                        ppl_E.append(a)
                
                    elif self.st == 'S' and a.st == 'I':
                        self.st = 'E'
                        ppl_E.append(self)
                        ppl_S.remove(self)
                
            


'- <FUNCTION> --------------------------------------------------------------------------------------'
                
def show():  
    for people in ppl:  
        people.color()
        people.state()
        people.infect()
        people.show()
        people.move()              
        
    
'- <INITIATION> --------------------------------------------------------------------------------------'

ppl = list()

for i in range(number):
    ppl.append(people(r, randint(0,age_lim), choice([0,1]), uniform(r/2, c_size_x-r/2), uniform(r/2, c_size_y-r/2), 'S'))

ppl_S = copy.copy(ppl)
ppl_I = list()
ppl_E = list()
ppl_R = list()
time_rate = list()  

data = list()

ppl_S[0].st = 'I'
ppl_I.append(ppl_S[0])
ppl_S.remove(ppl_S[0])


'- <RUN> ---------------------------------------------------------------------------------------------'
  
while SB == 0:
    
    clock.tick(60) # fps 설정
    passed_time = timeshow() - start_time  

    screen.fill([240,240,240])

    font = pg.font.Font("C:\Windows\Fonts\Arial.ttf",20)
    text = font.render("S : {}".format(len(ppl_S)),True, [0,0,0])  #텍스트가 표시된 Surface 를 만듬
    screen.blit(text,(1100,10))
   
    text = font.render("E : {}".format(len(ppl_E)),True, [0,0,0])  #텍스트가 표시된 Surface 를 만듬
    screen.blit(text,(1100,30)) 
 
    text = font.render("I : {}".format(len(ppl_I)),True, [0,0,0])  #텍스트가 표시된 Surface 를 만듬
    screen.blit(text,(1100,50)) 
  
    text = font.render("R : {}".format(len(ppl_R)),True, [0,0,0])  #텍스트가 표시된 Surface 를 만듬
    screen.blit(text,(1100,70))  
    

    for event in pg.event.get(): # User did something
        if event.type == pg.QUIT:
            SB = 1
        elif event.type == pg.MOUSEBUTTONDOWN:
            print([len(ppl_S), len(ppl_E), len(ppl_I), len(ppl_R)])
            print(data)
             
   

    frame += 1

    if frame % 10 == 0:
        data.append([frame, len(ppl_I), len(ppl_S), len(ppl_R), len(ppl_E)])

    if len(ppl_R) == 800:
        print(data)
        SB = 1
 

    show()

    pg.display.flip()
    
```




 







