---
layout: single
title: "물리 등속도 운동 시뮬레이션"
excerpt: "물리 시뮬레이션 등속도 운동 모델 processing 소스코드"
date: 2021-11-05
toc: true
toc_sticky: true
toc_label: "물리 시뮬레이션"
---

## 물리시뮬레이션 수행 소스코드  

수행평가로 작성한 등속도 운동 모델 코드  
**processing python**으로 구현  

```python
canvas_x_size = 800
canvas_y_size = 400

class Car(object):
    def __init__(self, color, xpos, ypos, xspeed):
        self.color = color
        self.xpos = xpos
        self.ypos = ypos
        self.xspeed = xspeed
        
    def display(self):
        stroke(0)
        fill(self.color)
        rectMode(CENTER)
        rect(self.xpos, self.ypos, 30, 15)
    
    def drive(self):
        self.xpos = self.xpos + 0.83*self.xspeed #(프레임 고려 5/6배)
        if self.xpos > width:
            self.xpos = 0
            
myCar1 = Car(color(255,0,0), 0, 300, 8)
myCar2 = Car(color(0,255,255), 0, 200, 4)
start_time = millis()
graph_elements = []

def setup():
    size(canvas_x_size,canvas_y_size)
    
def draw():
    background(255)
    
    passed_time = (millis() - start_time)/1000
    
    myCar1.display()
    myCar2.display()
    myCar1.drive()
    myCar2.drive()
    
    rectMode(CORNER)
    rect(10,70,50,1)
    fill(0)
    
    textSize(20)
    fill(0)
    text('car1 v : {}m/s'.format(myCar1.xspeed), canvas_x_size-230, 60)
    text('car2 v : {}m/s'.format(myCar2.xspeed), canvas_x_size-230, 100)
    text(passed_time, canvas_x_size-100, 20)
    text('passed_time', canvas_x_size-230, 20)
    text('1m = 50px, 60fps', 70, 77)
    rectMode(CENTER)
    fill(255,0,0)
    rect(canvas_x_size-260, 55, 20, 10)
    fill(0,255,255)
    rect(canvas_x_size-260, 95, 20, 10)
    
    textSize(30)
    fill(0)
    text("vegesion's physics simulation", 10, 40)
    
    # graph_elements.append([passed_time, myCar1.xspeed, myCar1.xpos, myCar2.xspeed, myCar2.xpos])
    
    # if mousePressed == True:
    #     print(graph_elements)

```


전체 시행 그래프는 다음과 같다.

![uniform_motion_all_graph](/assets/images/uniform_motion_all_graph.png)  


