---
title:  "[책] 파이썬 코딩의 기술 part2: 리스트와 딕셔너리"
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
last_modified_at: 2021-06-29
---  


# 리스트와 딕셔너리

반복적인 작업을 자동화 하도록 도와주는 자료형이 있는데 대표적으로 리스트와 딕셔너리이다. 딕셔너리는 키와 값을 해시 테이블이라는 데이터 구조로 저장해서 분할상환 복잡도로 원소를 찾아준다. 따라서 동적인 정보를 관리하는 데는 딕셔너리가 가장 이상적이다.  

🔔 분할상환 방식 계산복잡도: 각 연산이 발생하는 빈도와 시간을 함께 고려해 평균적으로 비용을 계산함으로써 산정한 복잡도

## Better Way 11: 시퀀스를 슬라이싱하는 방법을 익혀라 

### 슬라이스 대입에서 슬라이스와 대입되는 리스트이 길이가 같을 필요가 없다  

```python
>>> a = [1, 2, 3, 4, 5, 6, 7, 8, 9]

>>> a[1:6] = [10, 11]
[1, 10, 11, 7, 8, 9]
```  

### 슬라이싱에서 시작과 끝 인덱스를 모두 생략하면 원래 리스트를 복사한 새 리스트를 얻는다  

```python
>>> a = [1, 2, 3, 4, 5]
>>> b = a[:]

>>> b == a
True

>>> b is a
False
```  

```python
>>> a = [1, 2, 3, 4, 5]
>>> id(a)
1618724033408

>>> a[:] = [1, 2, 3]
>>> id(a)
1618724033408
```  

## Better Way 13: 슬라이싱보다는 나머지를 모두 잡아내는 언패킹을 사용하라  

starred expression을 이용하면 인덱스로 인해 발생할 수 있는 오류를 방지할 수 있다. 이 구문을 사용하면 언패킹 패턴의 다른 부분에 들어가지 못하는 모든 값을 별이 붙은 부분에 다 담을 수 있다.  

```python
>>> car_ages = [0, 9, 4, 8, 7, 21, 19, 1, 6, 15]
>>> car_ages_descending = sorted(car_ages, reverse=True)

>>> oldest, second_oldest, *others = car_ages_descending

>>> print(oldest, second_oldest, others)
20 19 [15, 9, 8, 7, 6, 4, 1, 0]
```  

하지만 starred expression을 이용해 언패킹 대입을 처리하려면 필수인 부분이 적어도 하나는 있어야 한다.  

```python
>>> *others = car_ages_descending

Traceback ...
SyntaxError: starred assignment target must be in a list or tuple
```  

또한 한 수준의 언패킹 패턴에 starred expression 을 두 개 이상 쓸 수 없다.  

```python
>>> first, *middle, *second_middle, last = [1, 2, 3, 4]
Traceback ...
SyntaxError: two starred expressions in assignment
```