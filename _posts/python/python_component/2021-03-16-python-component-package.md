---
title:  "[Component] 패키지, 모듈, 클래스"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - component
tags:
  - Python
last_modified_at: 2021-03-16
---


## 1. 파이썬 패키지

파이썬에서는 여러 소스코드들을 모듈 단위로 관리하는데 이러한 모듈을 더 큰 범위로 묶은 것이 패키지이다.  
![](/assets/images/component.png){: width="50%"}  
파이썬은 프로그래밍, 데이터 분석 등 넓은 범위에서 사용되는 언어이기 때문에 여러 분야에서 필요한 패키지와 모듈들을 만들어 관리하고 있다.  
그렇기 때문에 예를들어 우리가 데이터 분석을 한다고 할 때,  그래프를 만드는 코드, 통계를 내주는 코드 등등의 소스코드들을  
직접 만들지 않고 import 하여 사용할 수 있다.  
우선 import하는 방법을 보기 전에 패키지, 모듈, 클래스 단위로 간단하게 한 번 만들어 보자.


## 2. 패키지 생성

### 2-1 루트 디렉토리 정하기
C드라이브에 project라는 폴더를 만들어 루트 디렉토리로 정한다고 합시다.

### 2-2 패키지 생성하기
project폴더(루트)에서 animal_pkg라는 폴더를 만듭니다. 이게 패키지입니다.
C:/project/animal_pkg

### 2-3 모듈 만들기
animal_pkg폴더에서 내가 구현하고자 하는 소스코드를 만듭니다.  
고양이에 관한 모듈을 만든다고 해봅시다. animal_pkg폴더에 cat.py라는 파일을 만듭니다. 이게 모듈입니다.
C:/project/animal_pkg/cat.py

### 2-4 클래스 만들기
cat.py 파일에 고양이의 move를 관리하는 클래스를 만들겠습니다.
```python
# cat.py
class cat_move:
```  
이제 cat_move 클래스 안에 걷기 walk, 뛰기 run 함수를 만들어 고양이를 걸었다 뛰었다 하도록 해보겠습니다.
 ```python
 # cat.py
 class cat_move:
    def walk(self):
         self.velocity = 5
         return self.velocity
    
    def run(self):
        self.velocity = 20
        return self.velocity
 ```  

## 3. 패키지 임포트하기  

이제 다른 사람들이 만들어 놓은 패키지들을 가져오는 방법을 알아보겠습니다.  
그렇게 되면 패키지 또한 여러개가 되는데 이러한 패키지들을 모아놓은 곳을 라이브러리(Library)라고 합니다.  
저는 주로 머신러닝에 많은 시간을 할애하기 때문에 머신러닝에 사용되는 대표적인 numpy 패키지를 임포트 해보도록 하겠습니다.  
(정확히 numpy는 패키지가 아닌 라이브러리입니다.  
대부분 임포트 해오는 유용한 집합들은 패키지가 아닌 패키지의 모음인 라이브러리입니다.)
```python
import numpy
```  

이렇게 하면 numpy 패키지가 가지고 있는 모듈과 그 모듈 안에 있는 함수, 클래스에 속한 변수, 메소드를 모두 사용할 수 있습니다.  
(패키지, 라이브러리 뭔가 헷갈리는데 내 생각에는 numpy라는 라이브러리를 설치하면 그 안에 많은 패키지들이 있고  
그 중에 numpy라는 패키지도 있는 것 같다. 다른 패키지들은 설치는 되지만 사용은 안되는 무언가?)  
이제 불러온 패키지에서 실제로 사용되는 메소드, 함수를 사용해 봅시다.  

```python
import numpy
a = numpy.random.rand(1, 10)
```  

numpy패키지에 random모듈에 있는 rand함수는 인자로 받은 값의 shape을 가지며(여기서는 1행 10열) 0과1사이의 값을 갖는 데이터를 
리턴해 줍니다.  
패키지를 임포트하는지 임포트.모듈을 임포트하는지 임포트.모듈.함수를 임포트하는지에 따라 표현법이 조금씩 달라집니다.  

```python
import numpy as np # 이렇게 as 를 사용하면 줄여서 나타낼 수 있습니다.
import numpy.random as r
from numpy import random as r
from numpy import * # numpy패키지에 있는 모든 모듈을 사용하고 싶을 때 (메모리 많이 차지한다.)

>>> np.random.rand(1,10)
>>> r.rand(1,10)
# 이 둘은 같습니다
import numpy.random.rand # 이런식으로 함수를 한 번에 임포트 할 수는 없다.
```  

## 4. 모듈과 패키지를 찾는 경로
지금까지 모듈과 패키지는 현재 폴더(디렉터리)에 만들었습니다.  
파이썬에서는 현재 폴더에 모듈, 패키지가 없으면 다음 경로에서 모듈, 패키지를 찾습니다.
```python
>>> import sys
>>> sys.path
```  
sys 모듈의 path 변수에는 모듈, 패키지를 찾는 경로가 들어있습니다. 여기서 site-packages 폴더에는 pip로 설치한 패키지가 들어갑니다.  
그리고 자기가 만든 모듈, 패키지도 site-packages 폴더에 넣으면 스크립트 파일이 어디에 있든 모듈, 패키지를 사용할 수 있습니다.

만약 가상 환경(virtual environment)를 만들어서 모듈과 패키지를 관리한다면 가상환경/Lib/site-packages 폴더에 모듈과 패키지가 들어갑니다. 