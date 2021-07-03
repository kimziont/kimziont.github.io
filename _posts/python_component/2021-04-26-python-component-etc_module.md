---
title:  "[Component] 그 외 파이썬 표준 라이브러리의 중요한 모듈 (collections, itertools, heapq)"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - component
tags:
  - Python
last_modified_at: 2021-03-24
---
## 1. collections 모듈

### 1-1 deque 객체  
데크는 스택과 큐를 일반화 한 것입니다 (이름은 《deck》이라고 발음하며 《double-ended queue》의 약자입니다). 데크는 스레드 안전하고 메모리 효율적인 데크의 양쪽 끝에서의 추가(append)와 팝(pop)을 양쪽에서 거의 같은 O(1) 성능으로 지원합니다.  

list 객체는 유사한 연산을 지원하지만, 빠른 고정 길이 연산에 최적화되어 있으며, 하부 데이터 표현의 크기와 위치를 모두 변경하는 pop(0)과 insert(0, v) 연산에 대해 O(n) 메모리 이동 비용이 발생합니다.  
  
#### deque 객체 메소드  

* append(x)  
데크의 오른쪽에 x를 추가합니다.

* appendleft(x)  
데크의 왼쪽에 x를 추가합니다.

* clear()  
데크에서 모든 요소를 제거하고 길이가 0인 상태로 만듭니다.

* copy()  
데크의 얕은 복사본을 만듭니다.

* count(x)  
x 와 같은 데크 요소의 수를 셉니다.

* extend(iterable)  
iterable 인자에서 온 요소를 추가하여 데크의 오른쪽을 확장합니다.

* extendleft(iterable)  
iterable에서 온 요소를 추가하여 데크의 왼쪽을 확장합니다. 일련의 왼쪽 추가는 iterable 인자에 있는 요소의 순서를 뒤집는 결과를 줍니다.

* index(x[, start[, stop]])  
데크에 있는 x의 위치를 반환합니다 (인덱스 start 또는 그 이후, 그리고 인덱스 stop 이전). 첫 번째 일치를 반환하거나 찾을 수 없으면 ValueError를 발생시킵니다.



* insert(i, x)  
    x를 데크의 i 위치에 삽입합니다.  

    삽입으로 인해 제한된 길이의 데크가 maxlen 이상으로 커지면, IndexError가 발생합니다.



* pop()  
데크의 오른쪽에서 요소를 제거하고 반환합니다. 요소가 없으면, IndexError를 발생시킵니다.

* popleft()  
데크의 왼쪽에서 요소를 제거하고 반환합니다. 요소가 없으면, IndexError를 발생시킵니다.

* remove(value)  
value의 첫 번째 항목을 제거합니다. 찾을 수 없으면, ValueError를 발생시킵니다.

* reverse()  
데크의 요소들을 제자리에서 순서를 뒤집고 None을 반환합니다.



* rotate(n=1)  
    데크를 n 단계 오른쪽으로 회전합니다. n이 음수이면, 왼쪽으로 회전합니다.  

    데크가 비어 있지 않으면, 오른쪽으로 한 단계 회전하는 것은 d.appendleft(d.pop())과 동등하고, 왼쪽으로 한 단계 회전하는 것은 d.append(d.popleft())와 동등합니다.  


* maxlen  
데크의 최대 크기 또는 제한이 없으면 None.  

```python
>>> from collections import deque
>>> d = deque('ghi')                 # make a new deque with three items
>>> for elem in d:                   # iterate over the deque's elements
...     print(elem.upper())
G
H
I

>>> d.append('j')                    # add a new entry to the right side
>>> d.appendleft('f')                # add a new entry to the left side
>>> d                                # show the representation of the deque
deque(['f', 'g', 'h', 'i', 'j'])

>>> d.pop()                          # return and remove the rightmost item
'j'
>>> d.popleft()                      # return and remove the leftmost item
'f'
>>> list(d)                          # list the contents of the deque
['g', 'h', 'i']
>>> d[0]                             # peek at leftmost item
'g'
>>> d[-1]                            # peek at rightmost item
'i'

>>> list(reversed(d))                # list the contents of a deque in reverse
['i', 'h', 'g']
>>> 'h' in d                         # search the deque
True
>>> d.extend('jkl')                  # add multiple elements at once
>>> d
deque(['g', 'h', 'i', 'j', 'k', 'l'])
>>> d.rotate(1)                      # right rotation
>>> d
deque(['l', 'g', 'h', 'i', 'j', 'k'])
>>> d.rotate(-1)                     # left rotation
>>> d
deque(['g', 'h', 'i', 'j', 'k', 'l'])

>>> deque(reversed(d))               # make a new deque in reverse order
deque(['l', 'k', 'j', 'i', 'h', 'g'])
>>> d.clear()                        # empty the deque
>>> d.pop()                          # cannot pop from an empty deque
Traceback (most recent call last):
    File "<pyshell#6>", line 1, in -toplevel-
        d.pop()
IndexError: pop from an empty deque

>>> d.extendleft('abc')              # extendleft() reverses the input order
>>> d
deque(['c', 'b', 'a'])

```


### 1-2 defaultdict 객체
딕셔너리를 효율적으로 생성하기 위한 모듈                                        
defaultdict 객체는 존재하지 않는 키를 조회할 경우, 에러 메세지 대신 디폴트 값을 기준으로 해당 키에 대한 딕셔너리 아이템을 생성해준다.  

```python
>>> a = collections.defaultdict(int)
>>> a['A'] = 5
>>> a['B'] = 4
>>> a
defaultdict(<class 'int'>, {'A':5, 'B':4})

# 없는 키 값의 value 수정
>>> a['C'] += 1
>>> a
defaultdict(<class 'int'>, {'A':5, 'B':4, 'C':1})
```
#### defaultdict 객체 메소드  
dict 객체와 같다.  

* keys()  
    딕셔너리의 key값들을 보여준다.  
    값 하나하나를 읽어오기 위해서는 list로 형 변환 시켜줘야 한다.
    ```python
    >>> dict1.keys()
    dict_keys(['a', 'b', 'c'])
    >>> list(dict1.keys())
    ['a', 'b', 'c']
    ```

* values()  
    딕셔너리의 value들을 보여준다.
    마찬가지로 값을 하나씩 읽어오기 위해서는 list로 형 변환 시켜준다. (코드 생략)

* items()  
    딕셔너리의 (key, value)쌍들을 보여준다.
    ```python
    >>> dict1.items()
    dict_items([('a', 'apple'), ('b', 'banana'), ('c', 'car')])
    ```

* pop()  
    없애고자 하는 (key, value)쌍의 key값을 입력해주면 value값을 리턴하고 해당하는 쌍을 pop해준다 (코드 생략)

* popitem()  
    한 번 실행할 때 마다 가장 뒤에 저장된 (key, value)쌍을 리턴하고 딕셔너리에서 pop해준다.  
    (딕셔너리는 순서가 없는데 어떤 쌍이 가장 뒤에 있는 값인지 어떻게 알까?)

* clear()  
    딕셔너리를 비운다 (코드 생략)

* copy()  



### 1-3 Counter 객체

시퀀스형 데이터 안의 각 요소들을 key, 요소들의 갯수를 value로 하는 딕셔너리를 리턴해준다.

```python
>>> a = [1, 2, 3, 4, 5, 5, 5, 6, 6]
>>> b = collections.Counter(a)
>>> b
Counter({1: 1, 2: 1, 3: 1, 4: 1, 5: 3, 6: 2})
```

#### Counter 객체 메소드

* most_common()
    가장 빈도가 높은 아이템 추출

    ```python
    >>> b.most_common(2)
    [(5, 3), (6, 2)]
    ```  


## 2. itertools 모듈  
itertools는 특수 목적의 이터레이터 함수를 포함하고 있습니다.  

#### itertools 모듈 내에 있는 함수  

* chain()
    순회 가능한 인수(리스트, 문자열 등)들을 차례로 반복합니다.  

    ```python
    import itertools
    for item in itertools.chain([1, 2], ['가', '나'], '다라', {'a':0, 'b':1}):
        print(item, end=', ')
    -----------------------------------------------------------------------
    1, 2, 가, 나, 다, 라, a, b, 
    ```

* cycle()
    인수를 순환하는 무한 이터레이터를 반환합니다.  
    ```python
    import itertools
    for iter in itertools.cycle([1, 2]):
        print(item, end=' ')
    -------------------------------------
    1 2 1 2 1 2 1 2 1 2 1 2 1 2 1 2 1 ...
    ```  

    ```python
    import itertools
    list1 = [1,2,3,4]
    list2 = ['a','b','c','d','e','f','g']
    for list1_item , list2_item in zip(itertools.cycle(list1) , list2) : 
        
        print((list1_item),list2_item))
    ---------------------------------------
    1 a
    2 b
    3 c
    4 d
    1 e
    2 f
    3 g
    ```

* accumulate()  
    축적된 값을 계산합니다.  
    ```python
    import itertools
    for item in itertools.accumulate([1, 2, 3, 4]):
        print(item, end=' ')
    ------------------------------------------
    1 3 6 10
    ```

    ```python
    def multiply(a, b):
        return a*b
    
    for item in itertools.accumulate([1, 2, 3, 4], multiply):
        print(item, end=' ' )
    -----------------------------------------------
    1, 2, 6, 24
    ```

## 3. heapq 모듈  

#### heapq 모듈 내에 있는 함수  

* heappush()  
    힙에 원소를 추가할 수 있습니다. 첫번째 인자는 원소를 추가할 대상 리스트이며 두번째 인자는 추가할 원소를 넘깁니다.  
    ```python
    import heapq
    heap = []

    heapq.heappush(heap, 4)
    heapq.heappush(heap, 1)
    heapq.heappush(heap, 7)
    heapq.heappush(heap, 3)
    print(heap)
    -----------------------------------
    [1, 3, 7, 4]
    ```  

* heappop()
    힙에서 원소를 삭제할 수 있습니다.  원소를 삭제할 대상 리스트를 인자로 넘기면, 가장 작은 원소를 삭제 후에 그 값을 리턴합니다.  
    ```python
    print(heapq.heappop(heap))
    print(heap)
    ------------------------------
    1
    [3, 4, 7]
    ```  

* heapify()  
    기존의 리스트를 힙으로 만들어줍니다. 함수에 리스트를 인자로 넘기면 리스트 내부의 원소들의 위에서 다룬 힙 구조에 맞게 재배치되며 최소값이 0번째 인덱스에 위치됩니다.  
    ```python
    heap = [4, 1, 7, 3, 8, 5]
    heapq.heapify(heap)
    print(heap)
    -----------------------------------
    [1, 3, 5, 4, 8, 7]
    ```  

#### 응용  

##### 최대 힙  

```python
import heapq

nums = [4, 1, 7, 3, 8, 5]
heap = []

for num in nums:
  heapq.heappush(heap, (-num, num))  # (우선 순위, 값)

while heap:
  print(heapq.heappop(heap)[1])  # index 1
------------------------------------------------------
8
7
5
4
3
1
```  

##### 힙 정렬  

```python
import heapq

def heap_sort(nums):
  heap = []
  for num in nums:
    heapq.heappush(heap, num)

  sorted_nums = []
  while heap:
    sorted_nums.append(heapq.heappop(heap))
  return sorted_nums

print(heap_sort([4, 1, 7, 3, 8, 5]))
--------------------------------------------
[1, 3, 4, 5, 7, 8]
```

## 참조  

[Engineering Blog by Dale Seo](https://www.daleseo.com/python-heapq/)  