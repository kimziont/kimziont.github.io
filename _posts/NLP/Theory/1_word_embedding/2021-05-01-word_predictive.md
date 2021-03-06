---
title:  "[NLP] 학습을 통한 예측 기반 방법"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/NLP.jpg

categories:
  - word_embedding
tags:
  - NLP
last_modified_at: 2021-05-01
---

## 단어 표현

단어 표현은 모든 자연어 처리 문제의 기본 바탕이 되는 개념입니다. 자연어를 어떻게 표현할지 정하는 것이 각 문제를 해결하기 위한 출발점이 되는 것입니다. 자연어 처리는 컴퓨터가 인간의 언어를 이해하고 분석 가능한 모든 분야를 말합니다. 따라서 자연어 처리의 가장 기본적인 문제는 '어떻게 자연어를 컴퓨터에게 인식시킬 수 있을까?'입니다.  
컴퓨터는 텍스트뿐만 아니라 모든 값을 읽을 때 이진화된 값으로 받아들입니다. 즉 0과 1로만 구성된 값으로 인식할 수 있는데, 텍스트는 그 중 '유니코드' 혹은 영어의 경우 '아스키 코드'라는 방식을 통해 인식 할 수 있습니다. 예를 들어 '언어'를 유니코드로 나타내면 다음과 같습니다.  

`'언': 1100010110111000`  
`'어': 1100010110110100`  

자연어 처리에서는 이러한 방식을 그대로 사용하기에는 문제가 있습니다. 왜냐하면 유니코드로 인코딩된 값(1100010110111000)에는 전혀 언어적인 특성이 없기 때문입니다. 그렇다면 텍스트를 어떤 식으로 인코딩해야 자연어 처리모델에 적용가능한 이진값이 될까요? 이러한 질문의 답을 찾는 것이 단어표현 분야입니다.  
`단어의 의미와 중요도가 잘 담기며`  
`기계가 이해할 수 있는 이진값`  


## 단어 임베딩  

텍스트를 자연어 처리를 위한 모델에 적용할 수 있게 언어적인 특성을 반영해서 단어를 수치화하는 방법을 찾는 것입니다. 그리고 이렇게 단어를 수치화 할 때는 단어를 주로 벡터로 표현합니다. 따라서 단어 표현을 '단어 임베딩' 또는 '단어 벡터'로 표현하기도 합니다.  

여기서 소개할 방법은 분포 가설에 기반한 단어 표현 방법으로 대표적으로 카운트 기반 방법과 예측 방법이 있습니다. 분포 가설이란 '같은 문맥의 단어, 즉 비슷한 위치에 나오는 단어는 비슷한 의미를 가진다.'라는 개념입니다. 따라서 어떤 글에서 비슷한 위치에 존재하는 단어는 단어 간의 유사도가 높다고 판단하게 됩니다.  


### 예측 방법  
신경망 구조 또는 어떠한 모델을 사용해 특정 문맥에서 어떤 단어가 나올지를 예측하면서 단어를 벡터로 만드는 방식입니다.  
대표적으로 Word2Vec, NNLM(Neural Network Language Model), RNNLM(RNN Language Model)이 있습니다. 그 중 가장 많이 사용되는 Word2vec에 대해 자세히 살펴보겠습니다.  

#### Word2vec  
Word2vec은 크게 CBOW(Continuous Bag of Words)와 Skip-Gram이라는 두 가지 모델로 나뉘게 됩니다. CBOW는 어떤 단어를 문맥 안의 주변 단어들을 통해 예측하는 것이고, 반대로 Skip-Gram은 어떤 단어를 가지고 문맥 안의 주변 단어들을 예측하는 방법입니다.  


##### CBOW  
CVOW는 주변 단어를 통해 하나의 단어를 예측하는 모델입니다. 예를 들어,  
`예문 : "The fat cat sat on the mat"`  
가운데 단어를 예측하는 것이 CBOW라고 했습니다. {"The", "fat", "cat", "on", "the", "mat"}으로부터 sat을 예측하는 것은 CBOW가 하는 일입니다. 이 때 예측해야하는 단어 sat을 중심 단어(center word)라고 하고, 예측에 사용되는 단어들을 주변 단어(context word)라고 합니다.  
중심 단어를 예측하기 위해서 앞, 뒤로 몇 개의 단어를 볼지를 결정했다면 이 범위를 윈도우(window)라고 합니다. 예를 들어서 윈도우 크기가 2이고, 예측하고자 하는 중심 단어가 sat이라고 한다면 앞의 두 단어인 fat와 cat, 그리고 뒤의 두 단어인 on, the를 참고합니다. 윈도우 크기가 n이라고 한다면, 실제 중심 단어를 예측하기 위해 참고하려고 하는 주변 단어의 개수는 2n이 될 것입니다.  
윈도우 크기를 정했다면, 윈도우를 계속 움직여서 주변 단어와 중심 단어 선택을 바꿔가며 학습을 위한 데이터 셋을 만들 수 있는데, 이 방법을 슬라이딩 윈도우(sliding window)라고 합니다.  
![](/assets/images/word2vec_1.png){: width="80%" height="70%"}  

CBOW의 인공 신경망을 간단히 도식화하면 아래와 같습니다. 입력층(Input layer)의 입력으로서 앞, 뒤로 사용자가 정한 윈도우 크기 범위 안에 있는 주변 단어들의 원-핫 벡터가 들어가게 되고, 출력층(Output layer)에서 예측하고자 하는 중간 단어의 원-핫 벡터가 필요합니다. 뒤에서 설명하겠지만, Word2Vec의 학습을 위해서 이 중간 단어의 원-핫 벡터가 필요합니다.

또한 위 그림에서 알 수 있는 사실은, Word2Vec은 딥 러닝 모델(Deep Learning Model)은 아니라는 점입니다. 보통 딥 러닝이라함은, 입력층과 출력층 사이의 은닉층의 개수가 충분히 쌓인 신경망을 학습할 때를 말하는데 Word2Vec는 입력층과 출력층 사이에 하나의 은닉층만이 존재합니다. 이렇게 은닉층(hidden Layer)이 1개인 경우에는 일반적으로 심층신경망(Deep Neural Network)이 아니라 얕은신경망(Shallow Neural Network)이라고 부릅니다. 또한 Word2Vec의 은닉층은 일반적인 은닉층과는 달리 활성화 함수가 존재하지 않으며 `룩업 테이블`이라는 연산을 담당하는 층으로 일반적인 은닉층과 구분하기 위해 투사층(projection layer)이라고 부르기도 합니다.  
![](/assets/images/word2vec_2.png){: width="80%" height="70%"}  

CBOW의 인공 신경망을 좀 더 확대하여, 동작 메커니즘에 대해서 상세하게 알아보도록 하겠습니다. 아래 그림에서 주목해야할 것은 두 가지 입니다. 하나는 투사층의 크기가 M이라는 점입니다. CBOW에서 투사층의 크기 M은 임베딩하고 난 벡터의 차원이 됩니다. 다시 말해, 위의 그림에서 투사층의 크기는 M=5이기 때문에 CBOW를 수행하고나서 얻는 각 단어의 임베딩 벡터의 차원은 5가 될 것입니다.

두번째는 입력층과 투사층 사이의 가중치 W는 V × M 행렬이며, 투사층에서 출력층사이의 가중치 W'는 M × V 행렬이라는 점입니다. 여기서 V는 단어 집합의 크기를 의미합니다. 즉, 위의 그림처럼 원-핫 벡터의 차원이 7이고, M은 5라면 가중치 W는 7 × 5 행렬이고, W'는 5 × 7 행렬이 될 것입니다.  
![](/assets/images/word2vec_3.png){: width="80%" height="70%"}  

입력으로 들어오는 주변 단어의 원-핫 벡터와 가중치 W 행렬의 곱이 어떻게 이루어지는지 보겠습니다. 위 그림에서는 각 주변 단어의 원-핫 벡터를 x로 표기하였습니다. 입력 벡터는 원-핫 벡터입니다. i번째 인덱스에 1이라는 값을 가지고 그 외의 0의 값을 가지는 입력 벡터와 가중치 W 행렬의 곱은 사실 W행렬의 i번째 행을 그대로 읽어오는 것과(lookup) 동일합니다. 그래서 이 작업을 룩업 테이블(lookup table)이라고 부릅니다. 앞서 CBOW의 목적은 W와 W'를 잘 훈련시키는 것이라고 언급한 적이 있는데, 사실 그 이유가 여기서 lookup해온 <span style="color:#723434;"><u>W의 각 행벡터가 사실 Word2Vec을 수행한 후의 각 단어의 M차원의 크기를 갖는 임베딩 벡터</u></span>들이기 때문입니다.  
![](/assets/images/word2vec_4.png){: width="80%" height="70%"}  

이렇게 각 주변 단어의 원-핫 벡터에 대해서 가중치 W가 곱해서 생겨진 결과 벡터들은 투사층에서 만나 이 벡터들의 평균인 벡터를 구하게 됩니다. 만약 윈도우 크기 n=2라면, 입력 벡터의 총 개수는 2n이므로 중간 단어를 예측하기 위해서는 총 4개가 입력 벡터로 사용됩니다. 그렇기 때문에 평균을 구할 때는 4개의 결과 벡터에 대해서 평균을 구하게 됩니다. 투사층에서 벡터의 평균을 구하는 부분은 CBOW가 Skip-Gram과 다른 차이점이기도 합니다. 뒤에서 보게되겠지만, Skip-Gram은 입력이 중심 단어 하나이기때문에 투사층에서 벡터의 평균을 구하지 않습니다.  
![](/assets/images/word2vec_5.png){: width="80%" height="70%"}  

이렇게 구해진 평균 벡터는 두번째 가중치 행렬 W'와 곱해집니다. 곱셈의 결과로는 원-핫 벡터들과 차원이 V로 동일한 벡터가 나옵니다. 만약 입력 벡터의 차원이 7이었다면 여기서 나오는 벡터도 마찬가지입니다.

이 벡터에 CBOW는 소프트맥스(softmax) 함수를 취하는데, 소프트맥스 함수로 인한 출력값은 0과 1사이의 실수로, 각 원소의 총 합은 1이 되는 상태로 바뀝니다. 이렇게 나온 벡터를 스코어 벡터(score vector)라고 합니다. 스코어 벡터의 각 차원 안에서의 값이 의미하는 것은 아래와 같습니다.

스코어 벡터의 j번째 인덱스가 가진 0과 1사이의 값은 j번째 단어가 중심 단어일 확률을 나타냅니다. 그리고 이 스코어 벡터는 우리가 실제로 값을 알고있는 벡터인 중심 단어 원-핫 벡터의 값에 가까워져야 합니다. 스코어 벡터를 y^라고 하겠습니다. 중심 단어를 y로 했을 때, 이 두 벡터값의 오차를 줄이기위해 CBOW는 손실 함수(loss function)로 cross-entropy 함수를 사용합니다.  
![](/assets/images/word2vec_6.png){: width="80%" height="70%"}  

이제 역전파(Back Propagation)를 수행하면 W와 W'가 학습이 되는데, 학습이 다 되었다면 M차원의 크기를 갖는 W의 행이나 W'의 열로부터 어떤 것을 임베딩 벡터로 사용할지를 결정하면 됩니다. 때로는 W와 W'의 평균치를 가지고 임베딩 벡터를 선택하기도 합니다.


##### Skip-Gram  
Skip-Gram은 하나의 단어를 가지고 주변에 올 단어를 예측하는 모델입니다. 앞서 CBOW에서는 주변 단어를 통해 중심 단어를 예측했다면, Skip-gram은 중심 단어에서 주변 단어를 예측합니다. 앞서 언급한 예문에 대해서 동일하게 윈도우 크기가 2일 때, 데이터셋은 다음과 같이 구성됩니다.  
![](/assets/images/word2vec_7.png){: width="80%" height="70%"}  

중심 단어에 대해서 주변 단어를 예측하므로 투사층에서 벡터들의 평균을 구하는 과정은 없습니다. 여러 논문에서 성능 비교를 진행했을 때, 전반적으로 Skip-gram이 CBOW보다 성능이 좋다고 알려져 있습니다.  
![](/assets/images/word2vec_8.png){: width="80%" height="70%"}  


![](/assets/images/skip_gram.png){: width="80%" height="70%"}  



출처: <https://wikidocs.net/22660>