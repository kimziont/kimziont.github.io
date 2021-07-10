---
title:  "[Component] 내장함수"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - component
tags:
  - Python
last_modified_at: 2021-03-17
---


##  내장함수(Built-in function)
> Don’t Reinvent The Wheel (이미 있는 것을 다시 만드느라 쓸데없이 시간을 낭비하지 말라)  

이미 잘 만들어놓고 검증까지 해놓은 프로그램(소스코드)를 다시 만드는 것은 불필요한 행동이다.  
파이썬에서는 중요한 역할을 하는 몇가지 함수들을 이미 만들어 놓았다.  
이런 함수들은 import구문을 쓰지 않고 그냥 바로 사용할 수 있다.  
이러한 함수들을 __내장함수(Built-in function)__ 라고 한다.  
[Python3 built-in function documentary 참고](https://docs.python.org/3/library/functions.html)

여기서는 쉽고 자주 사용되는 것들을 위주로 알아보자  
### 1-1 abs, max, min, pow, round, len, sum, sorted, reversed

```python
>>> abs(-1) # 절댓값(absolute)
1
>>> max([1, 3, 2, 5]) # 최댓값
5
>>> min([1, 3, 2, 5]) # 최솟값
1
>>> pow(3, 2) # power
9
>>> round(5.43, 1) # 소수점 첫째자리 까지 반올림
5.4
>>> len([1, 2, 4, 5, 3]) # 요소의 갯수
5
>>> sum([1, 2, 3, 4, 5]) # 요소의 합
15
>>> sorted([1, 5, 2, 3]) # 정렬
[1, 2, 3, 5]
>>> reversed([1, 5, 2, 3]) # 뒤집기
[3, 2, 5, 1]
```

### 1-2 list, str, tuple, dict, set, bin, oct, hex, int, float, bool
  

```python
>>> a = list('car') # iterable한 객체가 인자로 받아서 list로 형변환
>>> a
['c', 'a', 'r']
>>> bin(5)
'0b101'
>>> int('0b101', 2) # x진수로 표현된 값을 갖는 문자열과 x를 입력해주면 그에 맞는 10진수 정수로 변환해준다.
5

>>> bool(1)
True
>>> bool(0)
False
>>> bool("False")
True
>>> bool(False)
False
>>> bool([1, 2, 0])
True
>>> bool([0, 0, 0])
True
>>> bool("")
False
```


### 1-3 map, filter, zip, enumerate, range


#### - map(function, iterable)  

iterable에 있는 모든 요소를 function에 적용하고 그 결과를 돌려주는 iterator를 돌려준다  

  ```python
  >>> map(lambda x: x**2, [1, 2, 3])
  <map at 0x23645348608>
  >>> list(map(lambda x: x**2, [1, 2, 3]))
  [1, 4, 9]
  ```  

#### - filter(function, iterable)  

iterable에 있는 요소들을 function에 넣었을 때 결과값이 나오고 그 결과값을 bool()에 넣었을 때 참이 나오는 iterable 요소들만 돌려주는 iterator를 돌려준다.  

  ```python

  >>> list(filter(lambda x: x, [0, 1, 2, 3]))
    [1, 2, 3]
  

  >>> list(filter(lambda x: True, [0, 1, 2, 3]))
    [0, 1, 2, 3]
  

  >>> list(filter(lambda x: x>1, [0, 1, 2, 3]))
    [2, 3]
  

  >>> list(filter(lambda x: len(x)>3, ['car', 'banana', 'apple']))
    ['banana', 'apple']
  ```  

#### - zip(iterable, iterable, ...)  

  ```python
  >>> list(zip([1, 2, 3], ['하나', '둘', '셋'], ['one', 'two', 'three']))
  [(1, '하나', 'one'), (2, '둘', 'two'), (3, '셋', 'three')]

  >>> list(zip([1, 2, 3], ['하나', '둘', '셋'], ['one', 'two']))
  [(1, '하나', 'one'), (2, '둘', 'two')]
  ```  

#### - enumerate(iterable)  

  ```python
  >>> list(enumerate(['apple', 'banana', 'lemon']))
  [(0, 'apple'), (1, 'banana'), (2, 'lemon')]
  >>> list(enumerate(['apple', 'banana', 'lemon'], start = 1))
  [(1, 'apple'), (2, 'banana'), (3, 'lemon')]
  ```
#### - range  
제너레이터의 방식을 활용하는 대표적인 함수(함수 안에 yield문이 있다)로 range()함수를 사용하면 range클래스 객체를 리턴합니다. range() 함수가 제너레이터 방식을 활용하기 때문에 range클래스 객체 안에도 분명 yield문이 있을 것입니다.  

 ![](/assets/images/range.png){: width="100%"}  

```python
>>> range(5)
range(0, 5)

>>> type(range(5))
<class 'range'>

>>> list(range(5))
[0, 1, 2, 3, 4]

# for 문에서 사용할 경우 내부적으로 next메소드 호출
>>> for i in range(5):
      print(i, end=' ')
0 1 2 3 4
```

```python
>>> a = [n for n in range(1000000)]
>>> b = range(1000000)

>>> sys.getsizeof(a)
8697464

sys.getsizeof(b)
48
```

### 1-4 input, print, open
 

#### - open(name, mode)  

파일(name)을 열고 파일 객체를 돌려준다  
  
  ```python
  >>> open("hi.txt")
  <_io.TextIOWrapper name='hi.txt' mode='r' encoding='cp949'>
  ```




### 1-5 help, dir, id, type

#### - help()  

  ```python
  def good():
      """인사하는 함수"""
      print("안녕")

  >>> help(good)
  Help on function good in module __main__:
  good()
      인사하는 함수
  ```

#### - dir()  

객체가 가지고 있는 변수나 메소드를 출력한다  

```python
>>> a = [1, 2, 3]
>>> dir(a)
['__add__', '__class__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__',   
'__getattribute__', '__getitem__', '__gt__', '__hash__', '__iadd__', '__imul__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__',  
'__lt__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__rmul__', '__setattr__', '__setitem__',  
  '__sizeof__', '__str__', '__subclasshook__', 'append', 'clear', 'copy', 'count', 'extend', 'index', 'insert', 'pop', 'remove', 'reverse', 'sort']
```

#### - id()  

객체를 입력받아 객체의 고유 주소 값(레퍼런스)을 돌려주는 함수이다.

#### - type()  

객체의 자료형을 알려준다. 


### 1-6 isinstance, issubclass  


#### - isinstance(_instance_, _class_)  

_instance_ 가 _class_ 로 만들어진 인스턴스인지 알려준다.  

#### - issubclass(_subclass_, _class_)  

_subclass_ 가 _class_ 의 자식 클래스인지 알려준다.



