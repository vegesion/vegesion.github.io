---
layout: single
title: "감염 시뮬레이션 - 기본"
excerpt: "전염병 감염 모델 processing 소스코드"
date: 2021-11-29
toc: true
toc_sticky: true
toc_label: "감염 시뮬레이션"
---

## 개요 

심심해서 작성한 전염병 감염 시뮬레이션 모델 코드 

아마 8,9월달에 작성했던 것으로 기억한다. 

이거 많이 보완해서 arc 하고 있는데 어렵더라;;


## 초기 설정 

**processing python**으로 구현 

초기 개체 200마리 

개체들 간의 거리가 (한 변의 길이/2) 이하일 때 일정확률로 감염 

그래프 : 시간에 따른 감염 개체 수 비율 

개체 죽는거랑 교배시키는거랑 설정하지 않은 기본 감염 모델이니 dead 신경쓰지 마셈 

(breed랑 die 함수 다 꺼놓은 것 볼 수 있을 것임)


## 소스코드 

```python

import time

'- <CLASS> -------------------------------------------------------------------------------------'

class people(object):    
    def __init__(self, r, age, sex, xp, yp, st):   
        self.age = age
        self.sex = sex
        self.p = PVector(xp, yp)
        speed = 10 - self.age/50
        self.velocity = PVector(random(-speed, speed),random(-speed, speed))
        self.viv = 5 - 0.15*self.age
        self.st = st # 0 = 건강, 1 = 감염, 2 = 사망
        self.r = r

        
    def generate(self):
        noStroke()
        if self.st == 0 and self.sex == 0:
            fill(255,200,200)
        elif self.st == 0 and self.sex == 1:
            fill(200,200,255)
        elif self.st == 1: 
            fill(255,0,0)

    
                    
    def move(self):
        rectMode(CENTER)
        rect(self.p.x, self.p.y, self.r, self.r)
        self.p = self.p.add(self.velocity)
        if self.p.x >= canvas_size-self.r/2 or self.p.x <= self.r/2:
            self.velocity.x = -self.velocity.x

        if self.p.y >= canvas_size-self.r/2 or self.p.y <= self.r/2:
            self.velocity.y = -self.velocity.y 

        
    def aging(self):
        self.age += 0.01
        if self.age >= 30:
            self.velocity *= 0.999
            
    def infect(self):
        for a in ppl[ppl.index(self)+1:]:
            if dist(self.p.x, self.p.y, a.p.x, a.p.y) < r and random(0,1) < 0.05:
                if self.st == 1 and a.st == 0:
                    a.st = 1
                    ppl_inf.append(a)
                elif self.st == 0 and a.st == 1:
                    self.st= 1
                    ppl_inf.append(self)          
            
    # def breed(self):
    #     for a in ppl[ppl.index(self)+1:]:
    #         if dist(self.p.x, self.p.y, a.p.x, a.p.y) < r and random(0,1) < 0.007:
    #             if self.sex != a.sex:
    #                 new_ppl = people(r, 0, round(random(0,2)), self.p.x, self.p.y, 0)
    #                 if self.st + a.st != 0:
    #                     new_ppl.st =1
    #                     ppl_inf.append(new_ppl)
    #                 ppl.append(new_ppl)
                
    # def die(self): 
    #     if self.st == 1:
    #         if self.age > age_lim: # 임시적인 것임!
    #             ppl_inf.remove(self)
    #             ppl.remove(self)
    #             death.append(self)
    #             self.st = 2
    #     else:
    #         if self.age > age_lim:
    #             ppl.remove(self)
    #             death.append(self)  
    #             self.st = 2
        

'- <FUNCTION> --------------------------------------------------------------------------------------'
                
def show():  
    frameRate(30)
    for people in ppl:    
        people.aging()
        people.generate()
        people.move()              
        people.infect()
        people.breed()
        people.die()
        
def time():
    passed_time = millis() - start_time
    textSize(20)
    fill(0)
    text(passed_time/1000, 20, 50)
    text('passed_time', 20, 20)
    text('total(alive)', canvas_size - 170, 20)
    text(len(ppl), canvas_size - 50, 20)
    text('infected', canvas_size - 170, 50)
    text(len(ppl_inf), canvas_size - 50, 50)
    text('dead', canvas_size - 170, 80)
    text(len(death), canvas_size - 50, 80)
    
    rectMode(CORNER)
    fill(150,255,150)
    rect(canvas_size - 170, 100, 150, 8)
    fill(255, 100,100)
    rect(canvas_size - 170, 100, 150*(len(ppl_inf)+len(death))/(len(ppl)+len(death)), 8)
    fill(100, 100,100)
    rect(canvas_size - 170, 100, 150*(len(death))/(len(ppl)+len(death)), 8)
    
    fill(0)
    rect(canvas_size, 0, 1, canvas_size)
    
    time_rate.append([passed_time, (200*len(ppl_inf))/((len(ppl)+len(death)))])
    noFill()
    stroke(0)
    rect(canvas_size + 20, canvas_size - 300, 300,200)
    fill(255,0,0)
    for i in range(len(time_rate)):
        noStroke()
        ellipseMode(CENTER)
        ellipse(canvas_size + 20 + time_rate[i][0]/300, canvas_size - 100 - time_rate[i][1], 3, 3)
        
'- <INITIATION> --------------------------------------------------------------------------------------'

number = 200
canvas_size = 800
r = 15
age_lim = 50
age_lim_infected = 50
start_time = millis()
passed_time = millis() - start_time    
ppl = []
ppl_inf = []
death = []
time_rate = []                        
for i in range(number):
    ppl.append(people(r, random(age_lim), round(random(0,1)), random(r/2, canvas_size-r/2), random(r/2, canvas_size-r/2), 0))
    
ppl[0].st = 1
ppl_inf.append(ppl[0])
ppl[0].age = 0

'- <RUN> ---------------------------------------------------------------------------------------------'
    
def setup():
    size(canvas_size + 400, canvas_size)
    
def draw():
    background(255)
    time()
    show()

```

## 결과


결과는 다음과 같다. 

![infection_simulation_default_result](/assets/images/infection_simulation_default_result.png) 

예측했듯이 시그모이드 함수 개형 그래프 도출

