---
title:  "[Intermediate] Iterable, Iterator, Generator, 그리고 for문"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - intermediate
tags:
  - Python
last_modified_at: 2021-09-21
---

# Iterable, Iterator, Generator, 그리고 for문

## 1. 이터레이터(Iterator)
이터레이터(iterator)는 값을 차례대로 꺼낼 수 있는 객체(object)입니다. 더 이상 꺼낼 값이 없을 경우 StopIteration 예외를 발생시킵니다. 

### 이터레이터 구현하기
이터레이터는 \__next__ 매직 메서드를 구현한 객체입니다.  

```python
class SequenceOfNumbers:

    def __init__(self, start=0):
        self.current = start

    def __next__(self):
        current = self.current
        self.current += 1
        return current


seq = SequenceOfNumbers()

print(next(seq))
print(next(seq))
-------------------------
0 1
```

하지만 이터레이터를 이렇게만 구현하게 되면 다음 값을 꺼내기 위해 명시적으로 next함수를 계속 호출해야 합니다. 이를 해결하기 위해 \__iter__ 함수를 함께 구현합니다.  

```python
class SequenceOfNumbers:

    def __init__(self, start=0):
        self.current = start

    def __next__(self):
        current = self.current
        self.current += 1
        return current

    def __iter__(self):
        return self


seq = SequenceOfNumbers()

for i in seq:
    if i > 10:
        break
    print(i, end=' ')
---------------------------
0 1 2 3 4 5 6 7 8 9 10
```

for문은 iter()함수를 통해 seq의 \__iter__() 메서드를 호출하고 \__iter__() 메서드는 인스턴스 자기 자신을 반환합니다. 반환된 이터레이터는 for문을 돌며 자동으로 \__next__() 메서드를 호출합니다.

## 2. 이터러블(Iterable) 객체
이터러블 객체라는 것은  이터레이터(Iterator)를 생성할 수 있는 객체라는 뜻입니다. 다시 말해, 이터러블 객체는 \__iter__ 매직 메서드가 구현되어 있고, 이 \__iter__ 매직 메서드가 이터레이터를 반환합니다. 이터러블 객체는 예를 들어, list객체도 이터러블 객체이고, tuple객체, dict객체도 이터러블 객체라고 할 수 있습니다. 또한 range객체도 이터러블 객체입니다. 제가 말씀드린 객체들에는 모두 \__iter__ 매직 메서드가 구현되어 있습니다.  

```python
a = list()
print(dir(a))
--------------------------------------------------------------------------------
['__add__', '__class__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__gt__', '__hash__', '__iadd__', '__imul__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__rmul__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'append', 'clear', 'copy', 'count', 'extend', 'index', 'insert', 'pop', 'remove', 'reverse', 'sort']
```
iterable한 객체는 메소드로 \__iter__ 를 가지고 있습니다. 이 메소드를 이용해 다음과 같이 이터레이터를 만들 수 있습니다.  

```python
[1, 2, 3].__iter__()

range(10).__iter__()
---------------------------------
<list_iterator object at 0x03616630>
<range_iterator at 0x28f03daf390>
```

이터레이터는 어떤 메소드를 가지고 있는지 살펴보겠습니다.  

```python
print(dir([1, 2, 3].__iter()))
--------------------------------------------
['__class__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__length_hint__', '__lt__', '__ne__', '__new__', '__next__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__setstate__', '__sizeof__', '__str__', '__subclasshook__']
```  
여기서 \__next__를 이용해 순차적으로 다음 값에 접근할 수 있는 것입니다.  

```python
it = [1, 2, 3].__iter__()

it.__next__()
it.__next__()
it.__next__()
it.__next__()
--------------------------------------------
1
2
3
Traceback (most recent call last):
  File "<pyshell#48>", line 1, in <module>
    it.__next__()
StopIteration
```  
이처럼 이터레이터는 \__next__로 요소를 계속 꺼내다가 꺼낼 요소가 없으면 StopIteration 예외를 발생시켜서 반복을 끝냅니다.  

끝으로 이터레이터, 이터러블 객체 모두 \__iter__ 매직 메서드를 가지고 있는데, 뭔가 용도가 다르게 느껴집니다. 이터러블 객체의 \__iter__는 이터레이터를 생성하기 위한 용도이고, 이터레이터의 \__iter__는 반복을 위한 용도입니다.  

참고로 반복을 위해 반드시 이터레이터 객체가 필요한 것은 아닙니다. for문에서 iter()를 이용해 이터레이터를 호출하려 했으나 없을 경우 for문은 다른 옵션을 사용하는데 바로 \__getitem__과 \__len__입니다. 그래서 \__iter__ 매직 메서드가 없어서 이터레이터를 생성할 수 없더라도 \__getitem__과 \__len__이 구현되어 있다면 for문에서 반복 생성자로 이용할 수 있습니다.  

## 3. 제너레이터(Generator)  
제너레이터는 이터레이터와 마찬가지로 요소를 하나씩 꺼내기 위해 만들어진 아이디어이지만 이터레이터와 조금 다릅니다. 처음부터 하나씩 살펴보겠습니다.  

우선 앞에서 이터레이터를 구현할 때에는 제가 직접 \__next__와 필요에 따라 \__iter__도 구현하여 클래스로 정의하였습니다. 하지만 `제너레이터는 함수로 구현이 가능`합니다. `제너레이터 함수만 구현하면 이 함수가 알아서 제너레이터 객체를 생성`하며 `제너레이터 객체는 이터레이터 객체와 역할이 같습니다.` 그래서 `제너레이터를 이터레이터의 일종`이라고 하며, 어떤 곳에서는 `그냥 제너레이터는 이터레이터`라고 설명하기도 합니다. 참고로 제너레이터 함수인지 아닌지는 yield문의 유무입니다. `yield문이 있다면 그 함수는 제너레이터`입니다. 예시는 조금 있다가 살펴보도록 하겠습니다.  

제너레이터는 이처럼 이터레이터를 만들기 위해 클래스를 정의하고 매직 메서드를 정의하는 수고를 덜고, 간단히 yield문을 사용한 함수를 이용해 구현할 수 있습니다. 하지만 훨씬 더 중요한 제너레이터의 장점은 바로 `메모리를 훨씬 절약`할 수 있다는 것입니다. 위에서 살펴본 이터레이터는, 예를 들어 리스트의 경우 반복을 위해 리스트가 먼저 생성되어야 하며 이는 리스트의 크기가 커질 경우 점점 더 메모리에 부담을 줍니다. 하지만 제너레이터는 반복을 위해, 모든 값들을 메모리에 올려놓는 것이 아니라 필요한 값들만 그때 그때 가지고 옵니다. 그래서 제너레이터를 사용했을 경우 훨씬 메모리 부담을 줄일 수 있습니다.  
(하지만 어떤 시퀀스의 특정 값을 가져오기 위해 제너레이터는 시간 복잡도가 O(n)이고, 리스트는 O(1)입니다. 그래서 제너레이터는 시간 복잡도를 포기하고 메모리 효율을 늘린 방법이라고 할 수 있습니다. 그래서 상황에 맞게 데이터를 가져오는 것만이 목적이면 제너레이터를, 임의의 값에 접근해야 한다면 리스트를 사용해야 합니다.)

### 제너레이터 구현하기
```python
def number_generator():
    yield 0
    yield 1
    yield 2
 
for i in number_generator():
    print(i)
--------------------------
0
1
2
```

```python
g = number_generator()

g
---------------------------------------------------
<generator object number_generator at 0x03A190F0>

>>> dir(g)
['__class__', '__del__', '__delattr__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__iter__', '__le__', '__lt__', '__name__', '__ne__', '__new__', '__next__', '__qualname__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', 'close', 'gi_code', 'gi_frame', 'gi_running', 'gi_yieldfrom', 'send', 'throw']
```

```
제너레이터 함수 -> 제너레이터 객체 생성(제너레이터에 __iter__와 __next__있음 -> 제너레이터는 이터레이터)
```

### 제너레이터 프로세스

```python
def number_generator():
    yield 0
    print("\n1~4초간 휴식 중")
    yield 5
    print("\n6~9초간 휴식 중")
    yield 10
```

위의 예시를 이용해 제너레이터가 어떤 프로세스를 통해 이터레이터 역할을 하는지 알아보겠습니다.  

```python
gen = number_generator()

print(next(gen))
print(next(gen))
print(next(gen))
print(next(gen))
---------------------------
0

1~4초간 휴식 중
5

6~9초간 휴식 중
10
StopIteration
```

처음 next()함수를 호출하면 gen 객체의 \__next__ 매직 메서드가 호출됩니다. (제너레이터는 이터레이터의 일종으로 \__iter__와 \__next__ 매직 메서드가 모두 구현되어 있습니다.) \__next__매직 메서드는 yield문이 실행될 때 까지 number_generator() 제너레이터 함수 안의 코드를 실행합니다. 그러다가 yield문을 만나면 number_generator 제너레이터 함수는 잠시 실행을 중지하고, 0을 반환합니다. 0을 받은 \__next__ 매직 메서드는 호출한 쪽 (print(next(gen)))에 전달해줍니다. 만약 print(next(gen))가 한 번 뿐이었다면 코드는 여기서 종료됩니다. (다시 number_generator() 함수로 돌아와 yield 0 밑에 있는 코드들을 실행하지는 않습니다.) 근데 위와 같이 print(next(gen))가 다시 실행되면 아까 양보했던 yield 0 이후 부터 다시 다음 yield문 까지 코드를 실행합니다. 이러한 반복은 제너레이터 함수의 끝에 도달하거나, 호출하는 쪽에서 그 전에 멈추는 방법으로 끝을 맺습니다.  

![](/assets/images/iter_genr_1.png){: width="80%" height="70%"}  

## 4. 정리
- 이터레이터는 하나씩 값을 꺼내오는 객체로 최소한 \__next__ 매직 메서드가 구현되어야 하며, 반복문에서 사용할 경우 \__iter__도 구현되어야 함

- 이터러블 객체는 이터레이터 객체를 만들어주는 객체로 \__iter__ 매직 메서드가 구현되어야 함 (리스트, 튜플, 딕셔너리, range 등)

- 제너레이터 함수는 제너레이터 객체를 생성하며 제너레이터 객체는 이터레이터와 같음

- 제너레이터도 마찬가지로 이터레이터처럼 값을 하나씩 꺼내오지만 메모리를 효율적으로 사용함

## 5. 참고

[파이썬 코딩 도장](https://dojang.io/mod/page/view.php?id=2405)