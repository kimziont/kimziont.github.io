---
title:  "[Deep_learning-Theory] 다층 신경망 이론"
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

# 다층 신경망 이론

## 1. 인공신경망  

새가 비행기의 발명에 영감이 되었다면, 사람의 뇌는 인공신경망의 영감이 되었습니다. 인공 신경망은 뇌에 있는 생물학적 뉴런의 네트워크에서 영감을 받은 머신러닝 모델입니다. 그러나 비행기가 새처럼 날개를 펄럭거릴 필요가 없듯이, 인공 신경망이 사람의 뇌와 똑같이 동작해야 할 이유는 없습니다. 최근 연구자들은 인공 신경망 연구의 창의성을 위해 이러한 비교 자체를 모두 버려야 한다고 주장합니다.  
인공 신경망은 딥러닝의 핵심입니다. 인공 신경망은 다재다능하고 강력하며 확장성 또한 좋아서 이미지 분류, 음성 인식, 비디오 추천 등 아주 복잡한 문제를 다루는 데 적합합니다.  

### 퍼셉트론  
퍼셉트론은 가장 간단한 인공 신경망 구조 중 하나로 1957년 프랑크 로젠블라트가 제안했습니다. 퍼셉트론은 TLU(Threshhold Logic Unit)이라고 불리는 인공 뉴런을 기반으로 합니다.  
![](/assets/images/TLU.png){: width="60%"}  

그렇다면 퍼셉트론은 어떻게 훈련 될까요? 여기서 실제 생물학적 뉴런에 약간의 영감을 받았다고 할 수 있습니다. 도널드 헤브는 1949년에 '서로 활성화되는 세포가 서로 연결된다.'라는 아이디어를 제안합니다. 즉 두 뉴런이 동시에 활성화될 때마다 이들 사이의 연결 가중치가 증가한다는 것입니다. 여기서 퍼셉트론은 네트워크가 예측할 때 만드는 오차를 반영하도록 조금 변형된 규칙을 사용하여 훈련됩니다. 퍼셉트론 학습규칙은 오차가 감소되도록 연결을 강화시킵니다.  
![](/assets/images/TLU_1.png){: width="30%"}  

그러나 이러한 퍼셉트론은 XOR과 같은 비선형 분류 문제를 해결하지 못하는 약점이 지적되었습니다.  

## 2. 비선형 분류 문제
XOR문제는 대표적인 비선형 분류 문제입니다. 이 문제는 1970년대 민스키의 『Perceptrons』에서 지적되었고, 이 후 한동안 신경망 연구가 정체기를 겪었습니다. 이 후 이를 해결하기 위한 방법이 몇 가지 발표되며 신경망 연구가 부활하는 계기가 되었습니다. 새로 도입한 기법을 요약하면,  
`은닉층을 둔다.`  
`시그모이드 활성함수를 도입한다.`  
`오류 역전파 알고리즘을 사용한다.`  
![](/assets/images/multi_layer_0.png){: width="50%"}  

시그모이드 함수와 역전파 알고리즘은 살펴봤기 때문에 여기서는 은닉층의 필요성에 대해 알아보겠습니다. 다시 XOR문제로 돌아와 보면, XOR문제는 주어진 x1, x2 공간에서는 데이터를 분류하는 모델을 만들 수 없습니다. 따라서 분류가 가능하도록 해주는 특징공간으로 옮겨야 하는데, 이를 가능하게 해주는 것이 바로 은닉층의 역할입니다. 밑에 그림과 같이 두 개의 퍼셉트론을 이용해 새로운 특징공간 z1, z2로 옮기면 우리의 데이터를 분류할 수 있게 됩니다.  

![](/assets/images/multi_layer_1.png){: width="100%"}

![](/assets/images/multi_layer_2.png){: width="100%"}  

### 다층 퍼셉트론(MLP)  

퍼셉트론을 여러 개 쌓아올린 인공 신경망을 다층 퍼셉트론이라 합니다. 다층 퍼셉트론은 하나의 입력층, 하나 이상의 은닉층 그리고 출력층으로 구성됩니다.  

![](/assets/images/MLP.png){: width="60%"}  



## 3. 딥러닝의 등장  
은닉층을 여러 개 쌓아 올린 인공 신경망을 심층 신경망이라고 합니다. 딥러닝은 이러한 심층 신경망을 연구하는 분야입니다. 깊은 층을 통해 비선형 분류 문제를 해결하게 되며 이를 계기로 다양한 문제에 층을 깊이 쌓은 신경망 구조가 주목을 받기 시작했습니다. 우리의 생각으로는 뚜렷한 구별 방법이 떠오르지 않지만, 신경망을 깊게 쌓음으로써 기계가 여러 가지 특징 공간에서 데이터를 볼 수 있게 되었고 이 방법은 실제로 비정형 데이터(음성, 사진 등)를 다루는 데에 굉장한 성능을 보여주었습니다. 또한 깊은 층의 의미가 있기 위해 각 층마다 활성화 함수를 사용했는데, 미분 연산이 간단한 ReLU함수가 등장하게 되면서 층을 더 깊이 쌓는 것이 실제로 가능해지게 되었습니다. 이 때 부터 본격적으로 비약적인 발전을 하게 되었습니다.  

## 4. 다층 신경망에서의 경사 하강법  

![](/assets/images/multi_layer_3.png){: width="100%"}  

![](/assets/images/multi_layer_4.png){: width="100%"}  

다음과 같이 구한 W1, W2에 대한 각각의 그래디언트를 이용해 각각의 가중치를 업데이트 한다. 행렬로 표기된 이유는 배치 경사 하강법을 가정했기 때문이다.  

