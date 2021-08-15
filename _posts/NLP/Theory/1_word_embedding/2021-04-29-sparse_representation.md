---
title:  "[NLP] 원-핫 인코딩"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/NLP.jpg

categories:
  - word_embedding
tags:
  - NLP
last_modified_at: 2021-04-29
---

### 1. 원-핫 인코딩  
단어를 표현하는 가장 기본적인 방법으로 모든 단어를 벡터의 하나의 성분으로 나타내고 해당하는 단어의 성분만 1, 나머지는 0의 값을 갖는 벡터로 표현하는 것입니다.  
![](/assets/images/one_hot.png){: width="80%" height="70%"}  

방법 자체가 매우 간단하고 이해하기도 쉽지만 가장 큰 문제는 표현해야 할 단어가 보통 자연어 처리문제에서 수백만 개에 달한다는 것입니다. 그리고 단어 사이의 관계나 의미가 전혀 표현되지 않기 때문에 사실상 원-핫 인코딩은 적합하지 않습니다.  


