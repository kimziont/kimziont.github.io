---
title:  "[NLP] 텍스트 분류를 위한 데이터 분석과 전처리"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/NLP.jpg

categories:
  - text_classification
tags:
  - NLP
last_modified_at: 2021-05-01
---  

## 데이터 분석  

### 1. 라이브러리 준비하기  

```python
import numpy as np
import pandas as pd
import os
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline
```
### 2. 데이터 불러오기  

![](/assets/images/text_classification_1.png){: width="60%" height="70%"}  

```python
DATA_IN_PATH = '../input/kumarmanoj-bag-of-words-meets-bags-of-popcorn/'
```

```python
train_data = pd.read_csv(DATA_IN_PATH+'labeledTrainData.tsv', header=0, delimiter="\t", quoting=3)
```

```python
train_data.head()
---------------------------------------------------------------------------
	id	sentiment	review
0	"5814_8"	1	"With all this stuff going down at the moment ...
1	"2381_9"	1	"\"The Classic War of the Worlds\" by Timothy ...
2	"7759_3"	0	"The film starts with a manager (Nicholas Bell...
3	"3630_4"	0	"It must be assumed that those who praised thi...
4	"9495_8"	1	"Superbly trashy and wondrously unpretentious ...
```

### 3. 파일 크기 확인하기  

```python
print("파일 크기: ")
for file in os.listdir(DATA_IN_PATH):
    if 'tsv' in file and 'zip' not in file:
        print(file.ljust(30)+str(round(os.path.getsize(DATA_IN_PATH+file)/1000000, 2))+'MB')
-----------------------------------------------------------------------------------------------
파일 크기: 
testData.tsv                  32.72MB
labeledTrainData.tsv          33.56MB
unlabeledTrainData.tsv        67.28MB
```

### 4. 데이터 분포  

```python
print('전체 학습 데이터의 개수: {}'.format(len(train_data)))
------------------------------------
전체 학습 데이터의 개수: 25000
```

```python
train_length = train_data['review'].apply(len)
train_length.head()
------------------------------------------------
0    2304
1     948
2    2451
3    2247
4    2233
Name: review, dtype: int64
```

```python
plt.hist(train_length, bins=200, alpha=0.5, color='r', label='word')

plt.yscale('log', nonposy='clip')
plt.title('Log-Histogram of length of review')
plt.xlabel('Length of review')
plt.ylabel('Number of review')
```
![](/assets/images/text_classification_2.png){: width="100%" height="70%"}  

```python
print('리뷰 길이 최댓값: {}'.format(np.max(train_length)))
print('리뷰 길이 최솟값: {}'.format(np.min(train_length)))
print('리뷰 길이 평균값: {:.2f}'.format(np.mean(train_length)))
print('리뷰 길이 표준편차: {:.2f}'.format(np.std(train_length)))
print('리뷰 길이 제 1사분위: {}'.format(np.percentile(train_length, 25)))
print('리뷰 길이 제 3사분위: {}'.format(np.percentile(train_length, 75)))
------------------------------------------------------------------------------
리뷰 길이 최댓값: 13710
리뷰 길이 최솟값: 54
리뷰 길이 평균값: 1329.71
리뷰 길이 표준편차: 1005.22
리뷰 길이 제 1사분위: 705.0
리뷰 길이 제 3사분위: 1619.0
```

```python
plt.figure(figsize=(12, 5))

plt.boxplot(train_length, labels=['counts'], showmeans=True)
```
![](/assets/images/text_classification_3.png){: width="100%" height="70%"}  

```python
from wordcloud import WordCloud
cloud = WordCloud(width=800, height=600).generate(" ".join(train_data['review']))
plt.figure(figsize=(20, 15))
plt.imshow(cloud)
plt.axis('off')
```
![](/assets/images/text_classification_5.png){: width="100%" height="70%"}  


```python
sns.countplot(train_data['sentiment'])
```
![](/assets/images/text_classification_4.png){: width="100%" height="70%"}  

```python
print("긍정 리뷰 갯수: {}".format(train_data['sentiment'].value_counts()[1]))
print("부정 리뷰 갯수: {}".format(train_data['sentiment'].value_counts()[0]))
---------------------------------------------------------------------------------
긍정 리뷰 갯수: 12500
부정 리뷰 갯수: 12500
```

```python
qmarks = np.mean(train_data['review'].apply(lambda x: '?' in x))
numbers = np.mean(train_data['review'].apply(lambda x: max([y.isdigit() for y in x])))

print('물음표가 있는 질문: {:.2f}%'.format(qmarks*100))
print('숫자가 있는 질문: {:.2f}%'.format(numbers*100))
------------------------------------------------------------------------------------------
물음표가 있는 질문: 29.55%
숫자가 있는 질문: 56.66%
```
## 데이터 전처리  

### 1. 라이브러리 준비하기  
```python
import re
import pandas as pd
import numpy as np
import json
from bs4 import BeautifulSoup
from nltk.corpus import stopwords
from tensorflow.python.keras.preprocessing.sequence import pad_sequences
from tensorflow.python.keras.preprocessing.text import Tokenizer
```

### 2. 데이터 불러오기  
```python
DATA_IN_PATH = '../input/kumarmanoj-bag-of-words-meets-bags-of-popcorn/'
train_data = pd.read_csv(DATA_IN_PATH+'labeledTrainData.tsv', header=0, delimiter='\t', quoting=3)
```
```python
print(train_data['review'][0])
-----------------------------------------------------------------------------------------------
'"With all this stuff going down at the moment with MJ i\'ve started listening to his music, watching the odd documentary here and there, watched The Wiz and watched Moonwalker again. Maybe i just want to get a certain insight into this guy who i thought was really cool in the eighties just to maybe make up my mind whether he is guilty or innocent. Moonwalker is part biography, part feature film which i remember going to see at the cinema when it was originally released. Some of it has subtle messages about MJ\'s feeling towards the press and also the obvious message of drugs are bad m\'kay.<br /><br />Visually impressive but of course this is all about Michael Jackson so unless you remotely like MJ in anyway then you are going to hate this and find it boring. Some may call MJ an egotist for consenting to the making of this movie BUT MJ and most of his fans would say that he made it for the fans which if true is really nice of him.<br /><br />..."'
```

### 3. 데이터 다듬기(HTML태그, 특수문자, 불용어 제거)  
```python
def preprocessing(review, remove_stopwords = False):
    review_text = BeautifulSoup(review, "html5lib").get_text()
    review_text = re.sub("[^a-zA-Z]", " ", review_text)
    words = review_text.lower().split()
    
    # 불용어 제거는 옵션
    if remove_stopwords:
        stops = set(stopwords.words("english"))
        words = [w for w in words if w not in stops]
    clean_review = " ".join(words)
    
    return clean_review
```

```python
print(clean_review)
--------------------------------------------------------------------------------------
stuff going moment mj started listening music watching odd documentary watched wiz watched moonwalker maybe want get certain insight guy thought really cool eighties maybe make mind whether guilty innocent moonwalker part biography part feature film remember going see cinema originally released subtle messages mj feeling towards press also obvious message drugs bad kay visually impressive course michael jackson unless remotely like mj anyway going hate find boring may call mj egotist consenting making movie mj fans would say made fans true really nice ...
```
```python
# 데이터 전체에 적용하기  
clean_train_reviews=[]
for review in train_data['review']:
    clean_train_reviews.append(preprocessing(review, remove_stopwords=True))
```

```python
# 데이터 프레임으로 저장해두기
clean_train_df = pd.DataFrame({'review': clean_train_reviews, 'sentiment':train_data['sentiment']})
```

```python
clean_train_df.head()
----------------------------

review	sentiment
0	stuff going moment mj started listening music ...	1
1	classic war worlds timothy hines entertaining ...	1
2	film starts manager nicholas bell giving welco...	0
3	must assumed praised film greatest filmed oper...	0
4	superbly trashy wondrously unpretentious explo...	1
```

### 4. 토큰화(Tokenizer): 각 단어를 인덱스로 벡터화  

```python
tokenizer = Tokenizer()
tokenizer.fit_on_texts(clean_train_reviews)
text_sequences = tokenizer.texts_to_sequences(clean_train_reviews)
```

```python
print(text_sequences[0])
-------------------------------------------------------
[404, 70, 419, 8815, 506, 2456, 115, 54, 873, 516, 178, 18686, 178, 11242, 165, 78, 14, 662, 2457, 117, 92, 10, 499, 4074, 165, 22, 210, 581, 2333, 1194, 11242, 71, 4826, 71, 635, 2, 253, 70, 11, 302, 1663, 486, 1144, 3265, 8815, 411, 793, 3342, 17, 441, 600, 1500, 15, 4424, 1851, 998, 146, 342, 1442, 743, 2424, 4, 8815, 418, 70, 637, 69, 237, 94, 541, 8815, 26055, 26056, 120, 1, 8815, 323, 8, 47, 20, 323, 167, 10, 207, 633, 635, 2, 116, 291, 382, 121, 15535, 3315, 1501, 574, 734, 10013, 923, 11578, 822, 1239, 1408, 360, 8815, 221, 15, 576, 8815, 22224, 2274, 13426, 734, 10013, 27, 28606, 340, 16, 41, 18687, 1500, 388, 11243, 165, 3962, 8815, 115, 627, 499, 79, 4, 8815, 1430, 380, 2163, 114, 1919, 2503, 574, 17, 60, 100, 4875, 5100, 260, 1268, 26057, 15, 574, 493, 744, 637, 631, 3, 394, 164, 446, 114, 615, 3266, 1160, 684, 48, 1175, 224, 1, 16, 4, 8815, 3, 507, 62, 25, 16, 640, 133, 231, 95, 7426, 600, 3439, 8815, 37248, 1864, 1, 128, 342, 1442, 247, 3, 865, 16, 42, 1487, 997, 2333, 12, 549, 386, 717, 6920, 12, 41, 16, 158, 362, 4392, 3388, 41, 87, 225, 438, 207, 254, 117, 3, 18688, 18689, 316, 1356]
```

```python
# 각 인덱스가 어떤 단어를 의미하는지 확인하기 위한 단어 사전
word_vocab = tokenizer.word_index

# word_index에는 패딩 정보값이 정의되어 있지 않기 때문에 '<PAD>'에 대한 인덱스 값을 0으로 입력
word_vocab["<PAD>"] = 0
```

```python
print(word_vocab)
--------------------------------------------------
{'<PAD>': 0, 'movie': 1, 'film': 2, 'one': 3, 'like': 4, 'good': 5 ...}
```

```python
print("전체 단어 개수: ", len(word_vocab))
--------------------------------
전체 단어 개수: 74066
```

```python
# 단어 사전, 단어 개수와 같은 정보는 필요할 수 있으므로 저장
data_configs = {}
data_configs['vocab'] = word_vocab
data_configs['vocab_size'] = len(word_vocab)
```  

### 5. 데이터 길이 제한(패딩)  

```python
MAX_SEQUENCE_LENGTH = 174

train_inputs = pad_sequences(text_sequences, maxlen=MAX_SEQUENCE_LENGTH, padding='post')
```

```python
print('Shape of train data: ', train_inputs.shape)
--------------------------------------------------------
Shape of train data:  (25000, 174)
```

### 6. 라벨 데이터
```python
train_labels = np.array(train_data['sentiment'])
```

### 7. 데이터 저장하기  

* 정제된 텍스트 데이터 - csv파일
* 벡터화한 데이터 - numpy
* 정답 라벨 - numpy
* 데이터 정보 - json  

```python
DATA_IN_PATH = './'
TRAIN_INPUT_DATA = 'train_input.npy'
TRAIN_LABEL_DATA = 'train_label.npy'
TRAIN_CLEAN_DATA = 'train_clean.csv'
DATA_CONFIGS = 'data_configs.json'
```  

```python
if not os.path.exists(DATA_IN_PATH):
    os.makedirs(DATA_IN_PATH)
np.save(open(DATA_IN_PATH + TRAIN_INPUT_DATA, 'wb'), train_inputs)
np.save(open(DATA_IN_PATH + TRAIN_LABEL_DATA, 'wb'), train_labels)
clean_train_df.to_csv(DATA_IN_PATH+TRAIN_CLEAN_DATA, index=False)
json.dump(data_configs, open(DATA_IN_PATH+DATA_CONFIGS, 'w'), ensure_ascii=False)
```

### 8. 평가 데이터 전처리하기 

```python
test_data = pd.read_csv('../input/kumarmanoj-bag-of-words-meets-bags-of-popcorn/'+'testData.tsv', header=0, delimiter='\t', quoting=3)

clean_test_reviews=[]
for review in test_data['review']:
    clean_test_reviews.append(preprocessing(review, remove_stopwords=True))
clean_test_df = pd.DataFrame({'review':clean_test_reviews, 'id':test_data['id']})
test_id = np.array(test_data['id'])

tokenizer.fit_on_texts(clean_test_reviews)
text_sequences = tokenizer.texts_to_sequences(clean_test_reviews)
test_inputs = pad_sequences(text_sequences, maxlen=MAX_SEQUENCE_LENGTH, padding='post')

TEST_INPUT_DATA = 'test_iput.npy'
TEST_CLEAN_DATA = 'text_clean.csv'
TEST_ID_DATA = 'test_id.npy'

np.save(open(DATA_IN_PATH+TEST_INPUT_DATA, 'wb'), test_inputs)
np.save(open(DATA_IN_PATH+TEST_ID_DATA, 'wb'), test_id)
clean_test_df.to_csv(DATA_IN_PATH+TEST_CLEAN_DATA, index=False)
```
