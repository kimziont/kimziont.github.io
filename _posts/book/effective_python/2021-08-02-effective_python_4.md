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
