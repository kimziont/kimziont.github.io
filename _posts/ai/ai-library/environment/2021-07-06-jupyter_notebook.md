---
title:  "[AI를 위한 환경설정] 주피터 노트북에서 가상환경과 커널의 관계에 대한 의문점"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/deep_learning.jpg

categories:
  - Dev_environment
tags:
  - AI
last_modified_at: 2021-07-06
---

# Jupyter notebook의 가상환경과 커널

## 1. 가상환경과 커널 만들기

### 1) 가상환경 만들기
![](/assets/images/jupyter_1.png){: width="100%"}  

### 2) 가상환경 활성화하기  
![](/assets/images/jupyter_2.png){: width="100%"}  

### 3) 주피터 노트북 설치하기  
![](/assets/images/jupyter_3.png){: width="100%"} 

### 4) 가상환경과 Kernel 연결하기  
![](/assets/images/jupyter_4.png){: width="100%"}  



## 2. 의문점  

Kernel이 가지는 패키지는 그 Kernel이 만들어졌던 가상환경의 패키지를 가지고 있다. 그래서 각 가상환경 별로 커널을 만들고 나면 나중에 주피터에서 다른 환경으로 바꿔서 테스트를 하고 싶으면 그냥 Kernel만 change하면 될 줄 알았다. 그런데 다른 가상환경에서 만들어진 Kernel은 패키지를 import해오지 못한다. '지정된 모듈을 찾을 수 없습니다'라고 한다.  
그러면 다른 환경에서 테스트를 원할 때 마다 계속 다른 가상환경으로 activate 해줘야 한다는 건데 그러면 Kernel을 change할 수 있는게 무슨 의미가 있을까? 같은 가상환경에서 만들어진 Kernel 간에서 change가 가능해도 어차피 같은 가상환경에서 만들어졌기에 패키지 버전이 모두 같을텐데..  

![](/assets/images/jupyter_5.png){: width="60%"}  