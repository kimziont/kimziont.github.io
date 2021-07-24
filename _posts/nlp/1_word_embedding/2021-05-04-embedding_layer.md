---
title:  "[NLP] Embedding Layer"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/NLP.jpg

categories:
  - word_embedding
tags:
  - NLP
last_modified_at: 2021-05-04
---

## Embedding layer  

저는 지금껏 코퍼스를 이용해 Word2vec을 해주는 Embedding layer를 만듭니다. 그리고 우리의 task(예를 들면, 텍스트 분류)을 위한 모델을 학습시키기 위해 원-핫 인코딩 벡터를 임베딩 레이어에 인풋으로 넣어주고 아웃풋으로 나오는 임베딩 벡터를 모델의 입력으로 사용한다고 생각했었습니다.  

![](/assets/images/embedding_layer.png){: width="100%"}  

그런데 이렇게 하면 임베딩 벡터가 우리의 task에 적합한 벡터가 아닌 pre-training에 의한 벡터가 되게 됩니다. 그래서 그냥 원-핫 벡터만을 쓰게 됩니다.  
(이해가 안된다. 원-핫 벡터도 어떤 태스크에 적합하다기 보다는 그냥 벡터로 나타낸 것이고, 임베딩 벡터도 어떤 태스크를 위한 벡터라기 보다는 최대한 언어적 특성을 반영해서 만들어진 벡터이기 때문에 차라리 임베딩 벡터가 더 dense하기도 하고 언어적 특성도 반영되어 당연히 임베딩 벡터를 입력으로 써야할 것 같은데, 그냥 원-핫 인코딩 벡터를 쓰라니,,, 그리고 실제로는 NLP 분야를 다루면서 word2vec을 쓸 일이 잘 없다니... 그냥 원-핫 인코딩 벡터를 쓰면 된다니...무슨 의미일까)
