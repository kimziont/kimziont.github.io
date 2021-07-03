---
title:  "[Intermediate] print, input"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - intermediate
tags:
  - Python
last_modified_at: 2021-03-20
---


## print
코딩 테스트 문제 풀이 과정에서 디버깅을 할 때 가장 자주 쓰는 명령이 바로 print()다. print()는 일반적으로 화면에 객체를 출력하는 파이썬의 내장 함수다.

### sep 파라미터  
```python
# 한 칸 공백이 디폴트로 설정되어 있어 띄어쓰기로 값이 구분된다.
>>> print('A','B')
A B

>>> print('A','B', sep=',')
A,B
```  

### end 파라미터  
```python
# print() 함수는 항상 끝에 줄바꿈이 디폴트로 설정되어 있다.
>>> print('A', 'B')
>>> print('C', 'D')
A B
C D

>>> print('A', 'B', end=' ')
>>> print('C', 'D')
A B C D
```  
### 출력문 포맷팅

```python
>>> a = ['apple', 123.4567]


>>> print('%s is %.1f'%(a[0], a[1]))
apple is 123.5

>>> print('{} is {}'.format(a[0], a[1]))
apple is 123.4567

>>> print('{1} {0}'.format(a[0], a[1]))
123.4567 apple

# f-string 파이썬 3.6 이상부터 지원
>>> print(f'{a[0]} is {a[1]}')
apple is 123.4567
```

## input

```python
>>> input()
>>> apple is good
'apple is good'

>>> input().split()
>>> apple is good
['apple', 'is', 'good']

# input의 결과는 항상 문자열이기 때문에 정수형 타입을 원할 경우 다음과 같이 코드를 작성할 수 있다.
>>> list(map(int, input().split()))
>>> 1 2 3 4
[1, 2, 3, 4]
```