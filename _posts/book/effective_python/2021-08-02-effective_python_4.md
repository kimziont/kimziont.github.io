---
title:  "[책] 파이썬 코딩의 기술 part4: 컴프리헨션과 제너레이터"
toc: true
toc_sticky: true
author_profile: true
sidebar_main: true
header:
  teaser: /assets/images/python.png

categories:
  - effective_python
tags:
  - python
last_modified_at: 2021-08-02
---  

# 컴프리헨션과 제너레이터

파이썬에서는 컴프리헨션이라는 특별한 구문을 사용해 리스트, 딕셔너리, 집합과 같은 타입의 자료형을 이터레이션 하면서 원소를 변형해 새로운 데이터를 만들 수 있다. 컴프리헨션을 사용하면 이런 작업을 수행하는 코드를 더욱 가독성을 높여 작성할 수 있다.  

컴프리헨션 코딩 스타일은 제너레이터를 사용하는 함수로 확장할 수 있다. 이터레이터를 사용할 수 있는 곳(for 루프, 별표 식 등)이라면 어디서나 제너레이터 함수를 호출한 결과를 사용할 수 있다. 또한 제너레이터를 사용하면 성능을 향상시키고, 메모리 사용을 줄이고, 가독성을 높일 수 있다.


## Better Way 27: map과 filter 대신 컴프리헨션을 사용하라

```python
# 1번째 방법
a = [1, 2, 3, 4, 5, 6, 7]
squares = []
for i in a:
  squares.append(i ** 2)
print(squares)
------------------------------
[1, 4, 9, 16, 25, 36, 49]


# 2번째 방법
squares = list(map(lambda x: x ** 2, a))
print(squares)
------------------------------
[1, 4, 9, 16, 25, 36, 49]


# 3번째 방법
squares = [i ** 2 for i in a]
print(squares)
------------------------------
[1, 4, 9, 16, 25, 36, 49]
```

여기까지 봤을 때는, map을 사용해도 가독성이 괜찮아 보인다. 하지만 조건이 조금 더 까다로워지면 컴프리헨션이 빛을 발한다.  

짝수만 제곱하는 리스트를 만들고 싶을때를 살펴보자.  

```python
a = [1, 2, 3, 4, 5, 6, 7]
even_squares = map(lambda x: x**2, filter(lambda x: x % 2 == 0, a))

even_squares = [x**2 for x in a if x % 2 == 0]
```

리스트 뿐 아니라 딕셔너리와 집합에도 컴프리헨션이 있다.  

```python
a = ['apple', 'banana', 'lemon']
b = [1, 5, 3]
dic = {key: value for key, value in zip(a, b)}

print(dic)
---------------------------------------------
{'apple': 1, 'banana': 5, 'lemon': 3}
```

참고로 2차원 배열 리스트를 플랫한 단일 리스트로 만들고 싶을 때 리스트 컴프리헨션을 사용할 수 있다.  

```python
matrix = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

flat = [x for row in matrix for x in row]
print(flat)
```

## Better Way 29: 대입식(왈러스 연산자)을 사용해 컴프리헨션 안에서 반복 작업을 피하라

```python
stock = {'못': 125, '나사못': 35, '나비너트': 8, '와셔': 24}
order = ['나사못', '나비너트', '클립']

def get_batches(count, size):
  return count // size

# 기본적인 방법
found = {name: get_batches(stock.get(name, 0), 8) for name in order if get_batches(stock.get(name, 0), 8)}

# 왈러스 연산자 이용했을 때
found = {name: batches for name in order if (batches := get_batches(stock.get(name, 0), 8))}
```

## Better Way 30: 리스트를 반환하기보다는 제너레이터를 사용하라

많은 데이터의 양을 처리할 때 처리한 데이터를 리스트에 한 번에 다 저장하면 메모리 사용량이 크게 증가한다. 꼭 저장할 필요가 없다면 제너레이터를 사용함으로써 개선할 수 있다. 제너레이터는 yield식을 사용하는 함수에 의해 만들어진다.  

`제너레이터 함수가 호출되면 제너레이터가 실제로 실행되지 않고, 즉시 이터레이터를 반환한다.` 이터레이터는 __next__() 메소드가 호출될 때마다 제너레이터 함수를 다음 yield 식까지 진행시킨다. yield에서 반환된 값은 호출된 쪽으로 전달된다. (type은 확인해보면 제너레이터다. 이터레이터를 반환한다고 해서 type이 이터레이터일꺼라 생각했는데..)  

```python
import sys


def index_words(text):
    if text:
        yield 0
        for index, letter in enumerate(text, 1):
            if letter == ' ':
                yield index


address = '컴퓨터(영어: Computer, 문화어: 콤퓨터, 순화어: 전산기)는 진공관'

it = index_words(address)
print(f'type: {type(it)}')
print(f'generator size: {sys.getsizeof(it)}')

list1 = list(it)
print(list1)
print(f'list size: {sys.getsizeof(list1)}')
------------------------------------------------------------------------
type: <class 'generator'>
generator size: 112
[0, 8, 18, 23, 28, 33, 39]
list size: 152
```

next() 내장함수가 사용되면 이터레이터의 매직메소드인 __next__() 메소드가 호출되며, 제너레이터가 yield에서 반환한 값을 받아온다  

```python
while True:
    try:
        print(next(it))
  
    except StopIteration:
        break
----------------------------------------
0
8
18
23
28
33
39
```

이터레이터는 반환할 수 있는 데이터를 모두 소진하고 나면 StopIteration 예외가 발생한다. 그 후 다시 이터레이터를 for루프, 리스트 생성자를 포함한 표준라이브러리에 있는 많은 함수가 이터레이션 시켜도 StopIteration이 일어나지 않는다. (다만 , next()로 직접 호출하는 거는 StopIteration 예외 발생) 이거는 개발자의 편의(?)를 위해 for문과 같은 반복문을 예외없이 알아서 종료시키도록 하기 위해 그런 것 같다.  

참고로 이터레이터를 리스트로 만들고 싶으면 다음과 같이 코드를 작성하면 된다.  

```python
# 이터레이터 생성
it = index_words(address)

# 리스트로 변환
list(it)
```

## Better Way 31: 인자에 대해 이터레이션 할 때는 방어적이 돼라  

앞에서 이터레이터는 한 번 데이터를 소진하고 나면 더 이상 다시 데이터를 받아오지 않는다. 이를 해결하는 방법으로는 크게 두 가지가 있다.  

1. 이터레이터가 호출되고 데이터를 모두 소진하고 하면 다시 이터레이터를 호출하도록 만든다. 

```python
def read_visits(data_path):
    with open(data_path, 'r') as f:
        for line in f:
            yield int(line)


def normalize_func(get_iter):
    # 제너레이터 함수 호출 통한 새로운 이터레이션 생성
    # sum()같은 함수도 사용되고 나면 이터레이션 모두 소진함
    total = sum(get_iter())
    result = []

    # 제너레이터 함수 호출 통한 새로운 이터레이션 생성
    for value in get_iter():
        percent = 100 * value / total
        result.append(percent)
    return result

# travel_data.csv는 그냥 숫자값 쭉 있는 csv파일
path = 'travel_data.csv'

# normalize_func()안에 그냥 read_visits()를 넣으면 이터레이터가 생성되는 것이 아니라 그냥 값이 반환됨
# 내 목적은 제너레이터 자체가 get_iter로 들어가길 원한다
# 그러면 get_iter() 식을 통해 이터레이터가 만들어질 것이다
# 따라서 read_visits(path) 자체가 입력으로 들어가도록 하기위해 lambda식을 써준다
percentages = normalize_func(lambda: read_visits(path))
print((percentages))
-------------------------------------------------------------------
[0.0124, 0.0285, 0.03829, 0.0106, 0.0311, 0.0596,...]
```


2. 이터레이터 프로토콜을 구현한 새로운 컨테이너 클래스를 제공

조금 더 고수들은 람다 식이 보기 좋지 않아 제너레이터를 클래스로 만든다  

```python
class ReadVisits:
    def __init__(self, data_path):
        self.data_path = data_path

    def __iter__(self):
        with open(self.data_path) as f:
            for line in f:
                yield int(line)


def normalize(numbers):
    total = sum(numbers)
    result = []
    for value in numbers:
        percent = 100 * value / total
        result.append(percent)
    return result


visits = ReadVisits(path)
percentages = normalize(visits)
print(percentages)
-------------------------------------------------------------------
[0.0124, 0.0285, 0.03829, 0.0106, 0.0311, 0.0596,...]
```

이제 여기서 제목과 같이 방어적이 돼라는 의미가 등장한다. 첫 번째 방법에서와 두 번쨰 방법에서 numbers를 인자로 받는 방법이 다르다. 우리는 여기서 이터레이터 자체를 받아오지 않고 매번 새로운 이터레이터 객체를 반환하는 컨테이너를 제공하도록 해줄 것이다.  

```python
from collections.abc import Iterator

def normalize_defensive(numbers):
    if isinstance(numbers, Iterator):
        raise TpyeError('컨테이너를 제공해야 합니다')
    
    total = sum(numbers)
    result = []
    for value in numbers:
        percent = 100 * value / total
        result.append(percent)
    return result
```  

## Better Way 36: 이터레이터나 제너레이터를 다룰 때는 itertools를 사용하라  

### 1) 여러 이터레이터 연결하기

### 2) 이터레이터에서 원소 거르기

### 3) 이터레이터에서 원소의 조합 만들어내기 