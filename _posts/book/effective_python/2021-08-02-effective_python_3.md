---
title:  "[책] 파이썬 코딩의 기술 part3: 함수"
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

# 함수
프로그래머들이 파이썬에서 처음으로 사용하는 정리 도구는 함수(function)다. 함수를 사용하면 큰 프로그램을 더 작고 간단한 조각으로 나눔으로써 코드의 재사용성과 리팩터링이 쉬워진다.  

## Better Way 21: 변수 영역과 클로저의 상호작용 방식을 이해하라

```python
def sort_priority(values, group):
    def helper(x):
        if x in group:
            return (0, x)
        return (1, x)
    values.sort(key=helper)

numbers = [8, 3, 1, 2, 5, 4, 7, 6]
group = {2, 3, 5, 7}
sort_priority(numbers, group)
print(numbers)
------------------------------------
[2, 3, 5, 7, 1, 4, 6, 8]
```

이 함수가 예상대로 작동하는 세 가지 이유가 있다.  

1. 파이썬의 함수는 기본적으로 클로저를 지원한다  
🔔 클로저란 자신이 정의된 영역 밖의 변수를 참조하는 함수  

2. 파이썬에서 함수는 일급 시민 객체이다.  
🔔 일급 시민 객체는 이를 직접 가리킬 수 있고, 변수에 대입하거나 다른 함수에 인자로 전달할 수 있다

3. 파이썬에서는 시퀀스형 데이터를 원소의 인덱스 별로 비교한다  


이번에는 우선순위가 높은 원소의 여부를 확인하는 플래그를 설정해보자  

```python
def sort_priority(numbers, group):
    found = False
    def helper(x):
      if x in group:
        found = True
        return (0, x)
      return (1, x)
    numbers.sort(key=helper)
return found
```

여기서 언뜻 보면 found가 True가 될 것 같지만 그렇지 않다. 그 이유는 파이썬 인터프리터는 참조한 변수를 다음과 같은 영역을 순서대로 뒤진다

1. 현재 함수의 영역  
2. 현재 함수를 둘러싼 영역(nonlocal)  
3. 현재 코드가 들어있는 모듈의 영역(global)  
4. 내장 영역  

다음 네 가지 영역에 없으면 NameError가 발생한다.   

따라서 위의 helper함수 안의 found = True는 대입문이 아니라 정의하는 것으로 취급된다. 이러한 동작은 종종 헷갈리기는 하지만 분명 의도에 따른 결과다. 의도는 함수에서 사용한 지역 변수가 모듈 영역을 더럽히지 못하게 막는 것이다.  

이를 해결(?)하기 위해 nonlocal문이라는 것을 사용할 수 있다. nonlocal문은 대입할 데이터가 클로저 밖에 정의되어 있어서 다른 영역에 속한다는 사실을 알려주는 역할을 한다.  

```python
def sort_priority2(numbers, group):
  found = False
  def helper(x):
    nonlocal found
    if x in group:
      found = True
      return (0, x)
    return (1, x)
  numbers.sort(key=helper)
  return found

```

하지만 필자는 코드가 긴 경우에는 nonlocal문을 사용하면 가독성이 떨어지고, 코드간 독립성이 깨지기 때문에 클래스를 정의하는 것을 추천한다.  

```python
class Sorter:
  def __init__(self, group):
    self.group = group
    self.found = False
  
  def __call__(self, x):
    if x in self.group:
      self.found = True
      return (0, x)
    return (1, x)

sorter = Sorter(group)
numbers.sort(key=sorter)
assert sorter.found is True
```  

```python
world = 1000
def outter():
    # 여기서 world = 1000 이라고 적으면 NameError: name 'world' is not defined 발생
    # global은 전역 영역에 있는 변수에 대해서만 적용가능한듯
    local = 1
    def inner():
        global world
        nonlocal local

        world += 10000
        local += 100

        print(local, world)
    inner()
    return local, world

print(outter())
```

## Better Way 22: 가변 인자(*args)를 사용해 시각적인 잡음을 줄여라

위치 인자(positional argument)를 가변적으로 받을 수 있으면 함수 호출이 더 깔끔해지고, 시각적 잡음도 줄어든다.  

먼저 인자 수가 고정되어 있는 경우부터 살펴보면,  

```python
def log(message, values):
  if not values:
    print(message)
  else:
    values_str = ', '.join(str(x) for x in values)
    print(f'{message}: {values_str}')

log('내 숫자는', [1, 2])
log('안녕', [])
```
values 인자에 값을 주고 싶지 않으면 빈 리스트를 넘겨야 한다. 이는 귀찮을 뿐 아니라 코드 잡음도 많다.  

이번에는 가변 인자를 써서 코드를 개선해 보겠다.  

```python
def log(message, *values):
  if not values:
    print(message)
  else:
    values_str = ', '.join(str(x) for x in values)
    print(f'{message}: {values_str}')

log('내 숫자는', 1, 2)
log('안녕')
```

🔔 *(star)의 쓰임새  

1. 변수 할당  

```python
first, second, *other = [97, 90, 87, 79, 65, 32]
```

2. 함수의 가변 인자  

```python
def log(message, *values):
  if not values:
    print(message)
  else:
    print(f'{message}: {values}')

log("내 숫자는", 1, 2, 3, 4, 5, 6, 7, 8, 9)
---------------------------------------------
내 숫자는: (1, 2, 3, 4, 5, 6, 7, 8, 9)
```

3. 함수 호출시 언패킹  

```python
def log(message, *values):
  if not values:
    print(message)
  else:
    print(f'{message}: {values}')

favorites = [7, 8, 9]

log("내가 가장 좋아하는 숫자는", favorites)
log("내가 가장 좋아하는 숫자는", *favorites) #언패킹
-----------------------------------------------------
내가 가장 좋아하는 숫자는: ([7, 8, 9],)
내가 가장 좋아하는 숫자는: (7, 8, 9)
```

## Better Way 23: 키워드 가변 인자

```python
def remainder(number, divisor):
  return number % divisor

remainder(20, 7)
remainder(20, divisor=7)
remainder(number=20, divisor=7)
remainder(divisor=7, number=20)

remainder(number=20, 7) # SyntaxError: positional argument follows keyword argument (위치 기반 인자가 키워드 인자보다 앞에와야 한다)
remainder(20, number=7) # TypeError: remainder() got multiple values for argument
```

딕셔너리를, 함수 호출할 때 파라미터로 사용하고 싶다면 ** 연산자를 사용할 수 있다.  

```python
my_kwargs = {'number': 20, 'divisor': 7}

remainder(**my_kwargs)
```

가변 키워드 인자로 사용하고 싶다면,

```python
def print_parameters(**kwargs):
  for key, value in kwargs.items():
    print(f'{key} = {value}')

print_parameters(apple=5, banana=10)
------------------------------------
apple = 5
banana = 10
```

```python
def print_parameters(**kwargs):
  for key, value in kwargs.items():
    print(f'{key} = {value}')

dic = {'apple': 5, 'banana': 10}

print_parameters(**dic)
------------------------------------
apple = 5
banana = 10
```