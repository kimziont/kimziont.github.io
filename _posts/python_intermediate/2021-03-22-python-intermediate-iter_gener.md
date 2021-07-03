---
title:  "[Intermediate] 이터레이터, 제너레이터"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - intermediate
tags:
  - Python
last_modified_at: 2021-03-22
---


## 1. 이터레이터(Iterator)
이터레이터(iterator)는 값을 차례대로 꺼낼 수 있는 객체(object)입니다.  
지금까지 for 반복문을 사용할 때 range를 사용했습니다. 만약 100번을 반복한다면 for i in range(100):처럼 만들었습니다. 이 for 반복문을 설명할 때 for i in range(100):은 0부터 99까지 연속된 숫자를 만들어낸다고 했는데, 사실은 숫자를 모두 만들어 내는 것이 아니라 0부터 99까지 값을 차례대로 꺼낼 수 있는 이터레이터를 하나만 만들어냅니다. 이후 반복할 때마다 이터레이터에서 숫자를 하나씩 꺼내서 반복합니다.  

만약 연속된 숫자를 미리 만들면 숫자가 적을 때는 상관없지만 숫자가 아주 많을 때는 메모리를 많이 사용하게 되므로 성능에도 불리합니다. 그래서 파이썬에서는 이터레이터를 이용해 값이 필요한 시점이 되었을 때 값을 호출하는 방식으로 메모리를 효율적으로 관리할 수 있게 해줍니다.  

### 이터레이터 만들기
어떤 객체가 iterable하면 그 객체를 통해 이터레이터를 만들 수 있다.   
이터레이터는 요소가 눈에 보이는 __리스트, 문자열, 딕셔너리, 세트__ 와 요소가 눈에 보이지 않는 __range__ 를 통해 만들 수 있습니다.  
(이들의 공통점은 모두 iterable하다는 것입니다.)  

객체가 iterable한지 어떻게 알 수 있을까? => dir() 함수를 이용하면 알 수 있다.

```python
>>> a = [1, 2, 3]
>>> print(dir(a))
['__add__', '__class__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__gt__', '__hash__', '__iadd__', '__imul__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__rmul__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'append', 'clear', 'copy', 'count', 'extend', 'index', 'insert', 'pop', 'remove', 'reverse', 'sort']
```
iterable한 객체는 메소드로 __iter__ 를 가지고 있습니다. 이 메소드를 이용해 다음과 같이 이터레이터를 만들 수 있습니다.  

```python
>>> [1, 2, 3].__iter__()
<list_iterator object at 0x03616630>

>>> range(10).__iter__()
<range_iterator at 0x28f03daf390>
```

이터레이터는 어떤 메소드를 가지고 있는지 살펴보겠습니다.  

```python
>>> print(dir([1, 2, 3].__iter()))
['__class__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__length_hint__', '__lt__', '__ne__', '__new__', '__next__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__setstate__', '__sizeof__', '__str__', '__subclasshook__']
```  
여기서 __next__를 이용해 순차적으로 다음 값에 접근할 수 있는 것입니다.  

```python
>> it = [1, 2, 3].__iter__()
>>> it.__next__()
1
>>> it.__next__()
2
>>> it.__next__()
3
>>> it.__next__()
Traceback (most recent call last):
  File "<pyshell#48>", line 1, in <module>
    it.__next__()
StopIteration
```  
이처럼 이터레이터는 __next__로 요소를 계속 꺼내다가 꺼낼 요소가 없으면 StopIteration 예외를 발생시켜서 반복을 끝냅니다.  

### for와 반복 가능한 객체

이제 for에 반복 가능한 객체를 사용했을 때 동작 과정을 알아보겠습니다. 다음과 같이 for에 range(3)을 사용했다면 먼저 range에서 __iter__로 이터레이터를 얻습니다. 그리고 한 번 반복할 때마다 이터레이터에서 __next__로 숫자를 꺼내서 i에 저장하고, 지정된 숫자 3이 되면 StopIteration을 발생시켜서 반복을 끝냅니다.  

![](/assets/images/iter.jpg){: width="70%" height="70%"}  
(출처: 파이썬 코딩 도장)  

## 2. 제너레이터(Generator)  
제너레이터는 이터레이터를 생성해주는 함수입니다.  
함수 안에서 yield를 사용하면 함수는 제너레이터가 되며 yield에는 값(변수)을 지정합니다.

```python
def number_generator():
    yield 0
    yield 1
    yield 2
 
>>> for i in number_generator():
        print(i)

0
1
2
```

```python
>>> g = number_generator()

>>> g
<generator object number_generator at 0x03A190F0>

>>> dir(g)
['__class__', '__del__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__lt__', '__name__', '__ne__', '__new__', '__next__', '__qualname__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'close', 'gi_code', 'gi_frame', 'gi_running', 'gi_yieldfrom', 'send', 'throw']
```
이 객체를 dir 함수로 살펴보면 이터레이터에서 볼 수 있는 __iter__, __next__ 메서드가 들어있습니다.

```python
>>> g.__next__()
0
>>> g.__next__()
1
>>> g.__next__()
2
>>> g.__next__()
Traceback (most recent call last):
  File "<pyshell#29>", line 1, in <module>
    g.__next__()
StopIteration
```
제너레이터는 제너레이터 객체에서 __next__ 메서드를 호출할 때마다 함수 안의 yield까지 코드를 실행하며 yield에서 값을 발생시킵니다(generate). 그래서 이름이 제너레이터(generator)입니다.  
그런데 generate라는 키워드를 사용하면 되지 왜 yield라고 이름을 지었을까요? yield는 생산하다라는 뜻과 함께 양보하다라는 뜻도 가지고 있습니다. 즉, yield를 사용하면 값을 함수 바깥으로 전달하면서 코드 실행을 함수 바깥에 양보합니다. 따라서 yield는 현재 함수를 잠시 중단하고 함수 바깥의 코드가 실행되도록 만듭니다.  
제너레이터는 함수를 끝내지 않은 상태에서 yield를 사용하여 값을 바깥으로 전달할 수 있습니다. 즉, return은 반환 즉시 함수가 끝나지만 yield는 잠시 함수 바깥의 코드가 실행되도록 양보하여 값을 가져가게 한 뒤 다시 제너레이터 안의 코드를 계속 실행하는 방식입니다.  

## 참고

이 포스트는 [파이썬 코딩 도장](https://dojang.io/mod/page/view.php?id=2405)에 있는 자료를 압축하여 가져온 것으로 더 자세한 내용을 원하시면 참고하시기 바랍니다.  
