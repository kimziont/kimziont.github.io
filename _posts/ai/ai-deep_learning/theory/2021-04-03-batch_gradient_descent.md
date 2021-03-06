---
title:  "[Deep_learning-Theory] 배치 경사 하강법"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/deep_learning.jpg

categories:
  - dl_theory
tags:
  - AI
last_modified_at: 2021-04-03
---

# 배치 경사 하강법

## 1. 확률적 경사 하강법(Stochastic Gradient Descent)  
확률적 경사 하강법은 데이터 세트에서 무작위로 균일하게 하나의 데이터를 추출해 그래디언트를 계산합니다. 즉 가중치를 한 번 업데이트 하기 위해 샘플을 1개씩만 사용합니다. 그렇기 때문에 굉장히 빠른 속도로 가중치를 업데이트 할 수 있게 됩니다. 하지만 이러한 방법은 다소 가중치를 성급하게 바꾸는 듯한 느낌이 납니다. 자칫 노이즈가 많은 데이터에 대해 가중치를 업데이트를 하게되면 그러한 데이터를 만날 때 마다 가중치가 잘못된 방향으로 업데이트 될 것입니다. 그래서 느리지만 조금 더 신중하게 가중치를 업데이트 하기 위해 나온 방법이 바로 배치 경사 하강법입니다.

## 2. 배치 경사 하강법(Batch Gradient Descent)  

배치 경사 하강법은 가중치를 한 번 업데이트 하기 위해 데이터 샘플을 64, 128개 정도 사용해 각 샘플마다 그래디언트를 계산 후, 가중치를 업데이트 할 때는 그들의 평균을 구해 그 평균값을 가중치 업데이트에 사용합니다. 다시 말해 가중치를 한 번 업데이트 하기 위해 데이터 1개가 아닌 64개씩 묶음(batch)해서 그 평균 그래디언트를 사용하겠다는 겁니다.  
또한 생각해보면 우리는 데이터에 대해 그래디언트를 계산하기 위해 그렇게 어려운 계산 과정을 겪지 않았습니다. 따라서 그래디언트 계산은 GPU에 있는 코어로도 충분히 해결이 가능합니다. GPU는 비교적 단순한 연산을 하는 코어가 수 십개에서 수 천개 있는 하드웨어입니다. 따라서 우리는 GPU를 사용해 가중치 업데이트를 할 것이고, 이 때 배치 경사 하강법을 사용하게 되면 훨씬 더 GPU를 효율적으로 사용할 수 있게 됩니다.  



확률적 경사 하강법과 배치 경사 하강법    
![](/assets/images/sgd_bgd.png){: width="100%"}


### 배치 경사 하강법 수식 과정

#### (1) 데이터를 Batch size(e.g. 64, 128)만큼 Forward propagation시킨다  
Forward propagation은 앞에서 했던 데이터와 가중치를 곱하고 합하는 과정들을 일컫는 말입니다.  

![](/assets/images/batch_1.png){: width="100%"}  

![](/assets/images/batch_2.png){: width="100%"}  

#### (2) Error를 구한다  

![](/assets/images/batch_3.png){: width="50%"}  


#### (3) 각 특성(노드)의 가중치를 업데이트 하기 위한 평균 그래디언트를 구한다  

![](/assets/images/batch_4.png){: width="100%"}  

![](/assets/images/batch_5.png){: width="50%"}  

#### (4) 가중치를 업데이트 한다  

![](/assets/images/batch_6.png){: width="50%"}  