---
title:  "[NLP] 텍스트 분류를 위한 단어 임베딩과 모델에 적용하기"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/NLP.jpg

categories:
  - text_classification
tags:
  - NLP
last_modified_at: 2021-05-02
---  

<!-- * 단어 임베딩: CountVectorizer, TF-IDF, word2vec
* 머신러닝 모델: 로지스틱 회귀, 랜덤 포레스트
* 딥러닝 모델: 순환 신경망(RNN), 합성곱 신경망(CNN)  

![](/assets/images/text_classification_6.png){: width="100%" height="70%"}   -->


## TF-IDF를 활용한 임베딩 구현

### TF-IDF 벡터화  

```python
from sklearn.feature_extraction.text import TfidfVectorizer

reviews = list(train_data['review'])
sentiments = list(train_data['sentiment'])

vectorizer = TfidfVectorizer(min_df=0.0, analyzer='char', sublinear_tf=True, ngram_range=(1,3), max_features=5000)

X = vectorizer.fit_transform(reviews)
```

#### TfidfVectorizer 객체의 파라미터 살펴보기  
* min_df: 설정한 값보다 특정 토큰의 df값이 더 적게 나오면 벡터화 과정에서 제거한다는 의미
* analyzer: 분석하기 위한 기준 단위, 'word'(단어)와 'char'(문자) 2가지 옵션 제공
* sublinear_tf: 문서의 단어 빈도 수에 대한 스무딩 여부
* ngram_range: 빈도의 기본 단위를 어느 범위의 n-gram으로 설정한 것인지를 보여주는 인자
* max_features: 각 벡터의 최대 길이, 특징의 길이를 설정

### 훈련, 검증 데이터셋 분리  

```python
from sklearn.model_selection import train_test_split
import numpy as np


RANDOM_SEED = 42
TEST_SPLIT = 0.2

y = np.array(sentiments)

X_train, X_eval, y_train, y_eval = train_test_split(X, y, test_size = TEST_SPLIT, random_state=RANDOM_SEED)
```

### 모델 선언 및 학습  

```python
from sklearn.linear_model import LogisticRegression

lgs = LogisticRegression(class_weight='balanced')
lgs.fit(X_train, y_train)
```

### 성능 평가

```python
print("Accuracy: {}".format(lgs.score(X_eval, y_eval)))
```

## word2vec을 활용한 임베딩 구현

### 라이브러리  

```python
import numpy as np
import pandas as pd
import os
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline
```

### 데이터 불러오기 및 준비 

```python
DATA_IN_PATH = '../input/kumarmanoj-bag-of-words-meets-bags-of-popcorn/'
train_data = pd.read_csv(DATA_IN_PATH+'labeledTrainData.tsv', header=0, delimiter="\t", quoting=3)


reviews = list(train_data['review'])
sentiments = list(train_data['sentiment'])

sentences = []
for review in reviews:
    sentences.append(review.split())
```

### word2vec를 이용한 벡터화  

```python
!pip install gensim

from gensim.models import word2vec
print("Training model...")

num_features = 300
min_word_count = 40
num_workers = 4
context = 10
downsampling = 1e-3

model = word2vec.Word2Vec(sentences, workers=num_workers, vector_size=num_features, min_count = min_word_count, window=context, sample=downsampling)
```

```python
model_name = "300features_40numwords_10context"
model.save(model_name)
```

### 데이터 다듬기  

리뷰마다 단어의 개수가 모두 다르기 때문에 입력값을 하나의 형태로 만들어야 합니다. 가장 단순한 방법으로는 문장에 있는 모든 단어의 벡터값에 대해 평균을 내서 리뷰 하나당 하나의 벡터로 만드는 방법이 있습니다  

```python
# words: 단어의 모음인 하나의 리뷰, model: 학습한 word2vec모델, num_features: 임베딩된 벡터의 차원
def get_features(words, model, num_features):
    
    # 출력 벡터 초기화
    feature_vector = np.zeros((num_features), dtype=np.float32)
    
    num_words = 0
    
    # 어휘사전 준비
    index2word_set = set(model.wv.index2word)
    
    for w in words:
        if w in index2word_set:
            num_words += 1
            
            # 사전에 해당하는 단어에 대해 단어 벡터를 더함
            feature_vector = np.add(feature_vector, model[w])
    
    # 문장의 단어 수만큼 나누어 단어 벡터의 평균값을 문장 벡터로 함 
    feature_vector = np.divide(feature_vector, num_words)
    return feature_vector
```

전체 리뷰에 대해 각 리뷰의 평균 벡터 구하기  

```python
def get_dataset(reviews, model, num_features):
    dataset = list()

    for s in reviews:
        dataset.append(get_features(s, model, num_features))

    revuesFeatureVecs = np.stack(dataset)

    return reviewFeatures
```

```python
X = get_dataset(sentences, model, num_features)
```

## CountVectorizer를 활용한 임베딩 구현  
```python
from sklearn.feature_extraction.text import CountVectorizer

vectorizer = CountVectorizer(analyzer = "word", max_features = 5000)

train_data_features = vectorizer.fit_transform(reviews)
```