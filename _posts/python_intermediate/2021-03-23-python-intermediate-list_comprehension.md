---
title:  "[Intermediate] List comprehension"
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


## 리스트 컴프리헨션
리스트 컴프리헨션은 파이썬의 매우 강력한 기능 중 하나며, 파이썬을 대표하는 특징 중 하나입니다.  
하지만 지나치게 남발하게 되면 가독성을 떨어트릴 수 있기 때문에 적절하게 사용하는 것이 중요합니다.  
또는 줄바꿈을 통해 가독성을 높이는 방법도 있습니다.  
  
in, for, if와 함께 많이 사용됩니다.
```python
>>> a = [1, 2, 3, 4, 5]
>>> [num+10 for num in a]
[11, 12, 13, 14, 15]
```

```python
>>> [num+10 for num in range(1,6)]
[11, 12, 13, 14, 15]
```

```python
>>> a = [1, 2, 3, 4, 5]
>>> [num for num in a if num%2==0]
[2, 4]
```

```python
>>> a = [1, 2, 3, 4, 5]
>>> [num in [2, 4] for num in a]
[False, True, False, True, False]
```  

```python
>>> zero_list = [0]*4
[0, 0, 0, 0]
```  

## map, filter, lambda
파이썬 내장함수를 이용해 리스트를 만들 수도 있습니다. 이 때는 리스트 컴프리헨션이 아닌 list() 내장 함수를 사용합니다. 『하지만 파이썬 코딩의 기술』이라는 책에서는 map과 filter 대신 리스트 컴프리헨션을 권장합니다.  

```python
>>> list(map(lambda x: x**2, range(10)))
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
```

## 딕셔너리 컴프리헨션

```python
>>> id_name = {1: 'apple', 2: 'banana', 3: 'lemon'}
>>> name_id = {key:val for key,val in id_name.items()}
>>> print(name_id) 
{'apple': 1, 'banana': 2, 'lemon': 3}
```