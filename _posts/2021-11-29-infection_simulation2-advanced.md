---
layout: single
title: "감염 시뮬레이션 - 교배 및 사망"
excerpt: "전염병 감염 모델 processing 소스코드"
date: 2021-11-29
toc: true
toc_sticky: true
toc_label: "감염 시뮬레이션"
---

## 개요 

솔직히 죽지도 않고 번식하지도 않는 생물을 가지고 실험한다는 것은  
상당히 현실과의 괴리가 큼

그렇기에 breed, die 함수를 활성화시켜 다시 프로그램을 돌려보았다 

소스코드는 저번 게시물의 코드와 동일 (함수 설정만 빼고) 

사실 소스코드 같은건 repository에 올리는게 어떻겠냐는 생각을 해봤는데  
너무 귀찮다 ㅎ  
그냥 여기에다 복붙하는게 더 편함 


## 초기 설정 

**processing python**으로 구현 

초기 개체 200마리 

개체들 간의 거리가 (한 변의 길이/2) 이하일 때 일정확률로 감염 및 번식 

성별 존재, 초기 성별 1:1 비율 

감염 개체 사이의 자손 또한 감염된 채 발생

그래프 : 시간에 따른 감염 개체 수 비율 (죽은 개체 포함)


## 실행 동영상


실행 동영상  

{% include video id="qPHI5_OSd5Y" provider="youtube" %}


## 결과


결과는 다음과 같다. 

![infection_simulation_default_result](/assets/images/infection_simulation_advanced_result.png) 

일반적인 시그모이드 개형과는 다른 그래프가 도출됨 (당연한거긴 하지만) 

죽은 개체 수 (수명)과 새로 태어난 개체 수 (번식확률)이 각각 어떤 영향을 끼쳤는지는  
지금 해석하기엔 시간이 없고 

나~중에 수명, 번식확률, 감염확률, 첫 번째 감염 시기에 따른 그래프 모양을 공부해볼 생각이다

