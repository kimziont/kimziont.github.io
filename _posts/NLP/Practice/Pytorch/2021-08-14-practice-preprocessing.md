---
title:  "[NLP] 파이토치를 이용한 텍스트 전처리"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/NLP.jpg

categories:
  - nlp_pytorch
tags:
  - NLP
last_modified_at: 2021-08-14
---  
## 0. NLP, 텍스트 분석 패키지

```
NLTK(Natural Language ToolKit): 파이썬의 가장 대표적인 NLP 패키지. NLP 거의 모든 영역을 커버하고 있지만 수행 속도 측면에서의 아쉬움으로 실제 대량의 데이터 기반에서는 제대로 활용되지 못하고 있다.

Gensim: 토픽 모델링 분야에서 두각을 나타내는 패키지

Spacy: 뛰어난 수행 성능으로 최근 가장 주목받고 있는 NLP 패키지
```


## 1. 클렌징(Cleansing)

### 1) HTML 태그 제거

```
from bs4 import BeautifulSoup

bs = BeautifulSoup(제거할 텍스트, "html5lib")

text = bs.get_text()
```

### 2) 문자, 기호 제거

```python
# 정규표현식 이용
```

## 2. 토큰화(Tokenization)


주어진 텍스트를 단어 또는 문자 단위로 자르는 것을 토큰화라고 합니다.

### 1) 단어 토큰화  

```python
en_text = 'A dog Run back corner near spare bedrooms'

# split() 메소드를 이용한 방법
en_text.split()


# NLTK
import nltk
from nltk.tokenize import word_tokenize

nltk.download('punkt')
print(word_tokenize(en_text))
-------------------------------------------------
['A', 'Dog', 'Run', 'back', 'corner', 'near', 'spare', 'bedrooms']


# spacy
import spacy
spacy_en = spacy.load('en')

def tokenize(en_text):
    return [tok.text for tok in spacy_en.tokenizer(en_text)]

print(tokenize(en_text))
---------------------------------------------------------
['A', 'Dog', 'Run', 'back', 'corner', 'near', 'spare', 'bedrooms']
```

### 2) 문장 토큰화

```python
from nltk.tokenize import sent_tokenize
```

### 3) n-gram을 이용한 토큰화

```python
from nltk import ngrams
from nltk.tokenize import word_tokenize

sentence = 'The Matrix is everywhere its all around us, here even in this room.'
words = word_tokenize(sentence)

all_ngrams = ngrams(words, 3)
ngrams = [ngram for ngram in all_ngrams]
print(ngrams)
-------------------------------------------------------------------
[('The', 'Matrix', 'is'), ('Matrix', 'is', 'everywhere'), ('is', 'everywhere', 'its'), ('everywhere', 'its', 'all'), ('its', 'all', 'around'), ('all', 'around', 'us'), ('around', 'us', ','), ('us', ',', 'here'), (',', 'here', 'even'), ('here', 'even', 'in'), ('even', 'in', 'this'), ('in', 'this', 'room'), ('this', 'room', '.')]
```

## 3. 불용어(Stopword) 제거

```python
# 불용어
['i', 'me', 'my', 'myself', 'we', 'our', 'ours', 'ourselves', 'you', "you're", "you've", "you'll", "you'd", 'your', 'yours', 'yourself', 'yourselves', 'he', 'him', 'his', 'himself', 'she', "she's", 'her', 'hers', 'herself', 'it', "it's", 'its', 'itself', 'they', 'them', 'their', 'theirs', 'themselves', 'what', 'which', 'who', 'whom', 'this', 'that', "that'll", 'these', 'those', 'am', 'is', 'are', 'was', 'were', 'be', 'been', 'being', 'have', 'has', 'had', 'having', 'do', 'does', 'did', 'doing', 'a', 'an', 'the', 'and', 'but', 'if', 'or', 'because', 'as', 'until', 'while', 'of', 'at', 'by', 'for', 'with', 'about', 'against', 'between', 'into', 'through', 'during', 'before', 'after', 'above', 'below', 'to', 'from', 'up', 'down', 'in', 'out', 'on', 'off', 'over', 'under', 'again', 'further', 'then', 'once', 'here', 'there', 'when', 'where', 'why', 'how', 'all', 'any', 'both', 'each', 'few', 'more', 'most', 'other', 'some', 'such', 'no', 'nor', 'not', 'only', 'own', 'same', 'so', 'than', 'too', 'very', 's', 't', 'can', 'will', 'just', 'don', "don't", 'should', "should've", 'now', 'd', 'll', 'm', 'o', 're', 've', 'y', 'ain', 'aren', "aren't", 'couldn', "couldn't", 'didn', "didn't", 'doesn', "doesn't", 'hadn', "hadn't", 'hasn', "hasn't", 'haven', "haven't", 'isn', "isn't", 'ma', 'mightn', "mightn't", 'mustn', "mustn't", 'needn', "needn't", 'shan', "shan't", 'shouldn', "shouldn't", 'wasn', "wasn't", 'weren', "weren't", 'won', "won't", 'wouldn', "wouldn't"]
```

```python
import nltk
nltk.download('stopwords')

stopwords = nltk.corpus.stopwords.words('english')
word_tokens = ['I', 'love', 'KFC', 'zzzz']
filtered_words = []
for word in word_tokens:
  word = word.lower()
  if word not in stopwords:
    filtered_words.append(word)
-----------------------------------------------
['love', 'kfc', 'zzzz']
```

## 4. 어간(Stemming) 추출, 표제어(Lemmatization) 추출

```python
from nltk.stem import LancasterStemmer
stemmer = LancasterStemmer()

print(stemmer.stem("working"))
-----------------------------
work
```

```python
from nltk.stem import WordNetLemmatizer
# nltk.download('wordnet') (다운로드)

lemma = WordNetLemmatizer()
print(lemma.lemmatize('amusing'))
------------------------------
amusing

print(lemma.lemmatize('amusing', 'v'))
-------------------------------
amuse

print(lemma.lemmatize('amusing', 'a'))
---------------------------------
amusing
```