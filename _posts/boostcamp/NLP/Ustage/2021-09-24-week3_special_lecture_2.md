---
title:  "[부스트캠프 Ai-tech] NLP 3주차 특강: Full Stack ML Engineer"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/computer.jpg


categories:
  - NLP_ustage
tags:
  - Boostcamp
last_modified_at: 2021-09-24
---

# 부스트캠프 NLP 3주차 특강: Full Stack ML Engineer
AI를 공부하다보면 `실제 현업에서는 어떤 프로세스를 거쳐 인공지능 모델이 개발되고 서비스`까지 나아갈 수 있을까?에 대한 고민을 한 적이 있을겁니다. 이번 포스트는 업스테이지 CTO 이활석님께서 강연하신 서비스향 AI모델 개발하기를 바탕으로 포스트를 정리하였습니다.  


## 1. Full stack ML engineer란?  
Full stack ML engineer란 ML 기술을 이해하고, 연구하고, product를 만드는 Engineer로 ML Research와 software engineer의 영역에서 공존하는 engineer입니다.  

![](/assets/images/special_lec_10.png){: width="90%" height="70%"}  



### 1) Full stack engineer  
Full stack engineer는 client/server단에서 software를 개발할 수 있는 사람들을 말합니다.
![](/assets/images/special_lec_11.png){: width="90%" height="70%"}  

### 2) Full Stack ML engineer
Full Stack ML engineer는 Deep learning research를 이해하고 client/server단에서 ML product를 만들 수 있는 개발자를 말합니다.  

![](/assets/images/special_lec_12.png){: width="90%" height="70%"}  

## 2. Full stack ML engineer의 구체적인 업무

### 1) 실생활 문제를 ML문제로 formulation

![](/assets/images/special_lec_14.png){: width="90%" height="70%"}  

### 2) Raw data 수집
- 웹에서 학습데이터를 모아야 하는 경우도 있음
- Web Crawler (Scraper) 개발해서 데이터 수집 (저작권 주의 )
![](/assets/images/special_lec_13.png){: width="90%" height="70%"}  

### 3) Annotation 툴 개발  
- 제공 받은 데이터의 정답을 입력하는 작업을 수행하는 web application 개발 작업  
- 다수의 Annotator 들이 Client 를 통해 동시에 서버로 접속해 Annotation 작업을 수행  
![](/assets/images/special_lec_15.png){: width="90%" height="70%"}  
![](/assets/images/special_lec_16.png){: width="90%" height="70%"}  


### 4) Model 개발 및 논문 작성  
- 기존 연구 조사 및 재현 (재현 성능은 Public benchmark 데이터로 검증 )
- 수집된 서비스 데이터 적용
- 모델 개선 작업 + 아이디어 적용 → 논문 작성  

### 5) Evaluation(Demo) 개발
- 모델의 Prediction 결과를 채점하는 Web application 개발

### 6) 서버 배포  
- 연구용 코드를 Production server 에서 사용 가능하도록 정리하는 작업
- File server 에 코드 + Weight 파일 압축해서 Version 관리
- Production server 에서는 Python worker 에게 MQ 를 통해 job 을 전달  

![](/assets/images/special_lec_17.png){: width="100%" height="70%"}  

## 3. 어떻게 시작하면 좋을까?  

- ML Engineer 라면 , 하나의 논문을 구현하고 , Demo page 를 만들어보는 것을 추천합니다

![](/assets/images/special_lec_18.png){: width="100%" height="70%"}  
