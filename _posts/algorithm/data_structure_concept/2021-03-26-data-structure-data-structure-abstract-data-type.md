---
title:  "[Data_structure] 추상자료"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/data-structure.jpg

categories:
  - data_structure
tags:
  - data_structure
last_modified_at: 2021-03-26
---

## 1. 추상자료(Abstract data type)란?
- 자료구조를 추상화 한 것
- 추상자료는 특정 방법(배열, 연결, 테이블, 계층적)으로 데이터를 구조화 하여 편하게 사용, 관리(접근, 삽입, 삭제)하게 해주는 기능을 가지는데, 
  그 기능들이 어떻게 구현되었는지 몰라도 사용자들이 쉽게 사용할 수 있도록 만들어 놓은 Data type
- 추상자료에는 대표적으로 리스트, 큐, 스택, 딕셔너리, 세트가 있습니다.

## 2. 리스트(List)
- 데이터 간 순서 관계를 유지할 수 있게 하는 자료형
- 동적배열, 링크드 리스트로 구현할 수 있다.
- 리스트에서 많이 사용되는 기능의 시간 복잡도가 자료구조에 따라 어떤지 보고 더 낮은 쪽을 선택하면 된다.  


  |연산|시간 복잡도|
  ||동적 배열|링크드 리스트|
  |:---:|:---:|:---:|
  |접근|O(1)|O(n)|
  |삽입|O(n)|O(n)|
  |삭제|O(n)|O(n)|  

- 리스트의 경우 임의의 데이터에 대한 __접근 연산의 사용이 빈번__ 하기 때문에 __동적 배열__ 을 이용해 구현하는 것이 시간복잡도 측면에서 훨씬 효율적이다.

  ```python
  brand = []
  brand.insert(0, "adidas") # 삽입
  brand.insert(1, "nike")
  brand.insert(2, "puma")
  brand.remove("nike") # 삭제
  brand[1] # 접근
  ```

## 3. 큐(Queue)  
- First In First Out(FIFO) : 마트 계산대와 비슷한 느낌이다. 먼저 들어간 것부터 먼저 나올 수 있다.
- 그렇기 때문에 맨 뒤 삽입, 맨 앞 삭제 연산의 시간 복잡도가 낮은 자료구조가 좋다. => 링크드 리스트

## 4. 스택(Stack)
- Last In First Out(LIFO) : 쌓여 있는 접시를 사용할 때와 비슷한 느낌이다. 나중에 들어온 데이터가 먼저 나올 수 있다.
- 그렇기 때문에 맨 뒤 삽입, 맨 뒤 삭제 연산의 시간 복잡도가 낮은 자료구조가 좋다. => 더블리 링크드 리스트  

  |연산|싱글 링크드 리스트|더블리 링크드 리스트|
  |:---:|:---:|:---:|
  |맨 앞 삽입| O(1) | O(1) |
  |맨 앞 삭제| O(1) | O(1) |
  |맨 뒤 삽입| O(1) | O(1) |
  |맨 뒤 삭제| O(n) | O(1) |  

  ```python
  from collections import deque
  """큐"""
  product = deque()
  product.append("water")
  product.append("milk")
  product.append("chocolate")
  product.popleft()
  
  """스택"""
  dishes = deque()
  dishes.append(1)
  dishes.append(5)
  dishes.append(10)
  dishes.pop()
  ```

## 5. 딕셔너리(Dictionary)

- 데이터 간의 순서 관계가 없으며 데이터는 key와 value가 쌍으로 이루어져 있다.
- 순서 관계가 없기 때문에 해시 테이블을 사용하면 가장 낮은 시간 복잡도를 얻을 수 있다.  

## 6. 세트(Set)

- 데이터 간의 순서 관계가 없으며 데이터는 key값만을 가지고 있다. (그래서 중복된 데이터가 저장될 수 없다)
- 딕셔너리와 마찬가지로 해시 테이블을 이용해 구현하면 된다.  

  |연산|시간 복잡도 (최악)|시간 복잡도 (평균)
  |---|:---:|:---:|
  |탐색| O(n) | O(1) |
  |삽입| O(n) | O(1) |
  |삭제| O(n) | O(1) |  

  ```python
  """딕셔너리"""
  dict1 = {}
  dict1["a"] = "apple" # 삽입
  dict1["b"] = "banana"
  dict1["c"] = "car"
  dict1["b"] = "DELETED" # 삭제
  dict1["a"] # 접근

  """세트"""
  set1 = set()
  set1.add(1) # 삽입
  set1.add(3)
  set1.add(5)
  set1.remove(3) # 삭제

  # 접근이란게 없는 것 같다. 접근과 유사해 보이는 것은 in, not in 을 통해 값이 있는지 확인하는 정도

  ```