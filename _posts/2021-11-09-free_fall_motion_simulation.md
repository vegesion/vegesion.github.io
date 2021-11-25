---
layout: single
title: "물리 자유낙하 운동 시뮬레이션"
excerpt: "물리 시뮬레이션 자유낙하 운동 모델 processing 소스코드"
date: 2021-11-09
toc: true
toc_sticky: true
toc_label: "자유낙하운동"
---

## 소스코드  

수행평가로 작성한 자유낙하 운동 모델 코드  
**processing python**으로 구현  
포물선 운동 모델 코드에서 vx를 0으로만 설정, 공의 x위치 조정  

```python
canvas_x_size = 1000
canvas_y_size = 800
r= 20

# 1m = 100px, 100fps (1frame = 1/100sec)

acc = PVector(0.0,0.1)

class Ball(object):
    def __init__(self, r, color, xpos, ypos, vx, vy):

        self.color = color
        self.pos = PVector(xpos, ypos)
        self.v = PVector(vx, vy)
        self.r = r
    
    def show(self):
        stroke(0)
        fill(self.color)
        ellipseMode(CENTER)
        ellipse(self.pos.x, self.pos.y, self.r, self.r)
    
    def move(self):
        self.pos = self.pos.add(self.v)
        self.v.y += acc.y
        self.v.x += acc.x
        if self.pos.x > width:
            self.pos.x = 0
        if self.pos.y >= canvas_y_size - 5*r/2.0:
            self.v.y = - self.v.y + acc.y 
            # 프레임 차이 때문에 가속도 한번 빼줌
            
ball1 = Ball(r, color(255,0,0), canvas_x_size/2-20, canvas_y_size - 5*r/2, 0, -12.0)
ball2 = Ball(r, color(0,255,255), canvas_x_size/2+20, canvas_y_size - 5*r/2, 0, -5.0)

start_time = millis()
graph_element = []
    
def setup():
    size(canvas_x_size,canvas_y_size)
                
def draw():
    frameRate(100)
    background(255)
    
    passed_time = float((millis() - start_time))/1000
    
    rectMode(CORNER)
    fill(100,100,100)
    rect(0,canvas_y_size - 2*r ,canvas_x_size,300)
    
    ball1.show()
    ball2.show()
    ball1.move()
    ball2.move()
       
    rect(10,70,1,100)
    fill(0)
    
    textSize(20)
    fill(0)
    text('1m = 100px, 100fps, g = 10m/s^2', 20, 125)
    ellipseMode(CENTER)
    fill(255,0,0)
    ellipse(canvas_x_size-400-r, 53, r,r)
    fill(0,255,255)
    ellipse(canvas_x_size-400-r, 93, r,r)
    fill(0)
    text('ball1 v : {:.1f}m/s'.format(sqrt(ball1.v.x**2 + ball1.v.y**2)), canvas_x_size-400, 60)
    text(', vx : {:.2f},  vy : {:.1f}'.format(ball1.v.x, -ball1.v.y), canvas_x_size-240, 60)
    text('ball2 v : {:.1f}m/s'.format(sqrt(ball2.v.x**2 + ball2.v.y**2)), canvas_x_size-400, 100)
    text(', vx : {:.2f},  vy : {:.1f}'.format(ball2.v.x, -ball2.v.y), canvas_x_size-240, 100)
    text(passed_time, canvas_x_size-270, 20)
    text('passed_time', canvas_x_size-400, 20)
    
    textSize(30)
    text("vegesion's physics simulation", 10, 40)

    graph_element.append([passed_time, round(sqrt(ball1.v.x**2 + ball1.v.y**2),2),round(ball1.v.x,2),round(ball1.v.y,2),round(sqrt(ball2.v.x**2 + ball2.v.y**2),2),round(ball2.v.x,2),round(ball2.v.y,2)])
    if passed_time >= 9:
            print(graph_element)
            exit()
```

## 분석 


전체 시행 분석 그래프는 다음과 같다.

![freefall_motion_all_graph](/assets/images/freefall_motion_all_graph.png)  
