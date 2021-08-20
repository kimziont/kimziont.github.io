---
title:  "[Odds and Ends] Batch Normalization과 Layer Normalization"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/deep_learning.jpg

categories:
  - odds-deep_learning
tags:
  - AI
last_modified_at: 2021-08-17
---


# Batch Normalization, Layer Normalization

일단 Batch Normalization(이하 BN)이나 Layer Normalization(이하 LN) 모두 값들이 심하게 차이나는 정도를 줄이기 위해서 인데 그 방향이 서로 다르다.  

먼저 BN은 “각 feature의 평균과 분산”을 구해서 batch에 있는 “각 feature 를 정규화” 한다.  
반면 LN은 “각 input의 feature들에 대한 평균과 분산”을 구해서 batch에 있는 “각 input을 정규화” 한다.  