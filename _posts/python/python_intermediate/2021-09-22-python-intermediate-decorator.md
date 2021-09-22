---
title:  "[Intermediate] 파이썬의 데코레이터(Decorator)"
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

# 데코레이터(Decorator)

## 1. 데코레이터
파이썬에서 데코레이터는 함수와 메서드의 기능을 쉽게 수정하기 위한 수단으로 소개 되었습니다. 만약 함수나 메서드에 약간의 변형을 원할 경우 그럴 때마다 원래 함수를 직접 수정하는 것은 바람직하지 않습니다. 또한 기존의 함수들에 모두 같은 기능을 추가하려고 하는 상황에서 중복되는 코드를 모두 붙여넣는 것은 `DRY(Do not Repear Yourself) 원칙`을 위배합니다.  

예를 들어 original이라는 함수가 있고, 우리가 업데이트 하고자 하는 코드가 있는 modifier라고 하는 함수가 있는 경우에 데코레이터를 사용하지 않으면 다음과 같이 작성해야 합니다.  

```python

def original():
    ...

def modifier():
    ...

original = modifier(original)
```

이 방법은 DRY원칙은 준수하지만 가독성이 떨어지고 오류가 발생하기 쉽습니다. 이러한 이유로 데코레이터를 사용해 DRY원칙은 그대로 준수하며 오류를 줄일 수 있습니다.  

```python
def modfier():
    ...

@modifier
def original():
    ...
```

간단한 예시를 살펴보겠습니다.  

```python
def add_print(original):
    def wrapper():
        print("Hello")
        original()
        print("Good bye")
    return wrapper


@add_print
def profile():
    print("My name is Peter")


profile()
---------------------------------
Hello
My name is Peter
Good bye
```

이 데코레이터는 다음과 같이 동작합니다.  

```python
profile = add_print(profile)
```

## 2. 인자를 받는 함수의 데코레이터

인자를 받는 함수에 데코레이터를 적용할 경우에는 어떻게 데코레이터를 작성해야하는지 살펴보겠습니다.  

```python
def add_print(original):
    def wrapper(x, y):
        print("Hello")
        original(x, y)
        print("Good bye")
    return wrapper


@add_print
def add(x, y):
    print(f"{x} + {y} = {x+y}")


add(5, 2)
---------------------------
Hello
5 + 2 = 7
Good bye
```

그러면 만약에 처음에 작성했던 인자가 필요없는 profile 함수와, 인자가 필요한 add 함수에 모두 데코레이터를 적용하고 싶으면 어떻게 해야 할까요?  

```python
def add_print(original):
    def wrapper(*args):
        print("Hello")
        original(*args)
        print("Good bye\n")
    return wrapper


@add_print
def profile():
    print("My name is Peter")


@add_print
def add(x, y):
    print(f"{x} + {y} = {x+y}")


profile()
add(5, 2)
-------------------------------
Hello
My name is Peter
Good bye

Hello
5 + 2 = 7
Good bye
```

다음과 같이 가변 인자 `*args`를 이용해 해결할 수 있습니다.  

## 3. 데코레이터에 인자를 전달하는 경우
데코레이터에 인자를 넣어줄 수 있게되면 데코레이터를 그때 그때마다 상황에 맞게 동적으로 적용할 수 있게 됩니다. 만약 3의 배수인지 확인하는 데코레이터, 5의 배수인지 확인하는 데코레이터와 같은 경우, 데코레이터에 인자를 넣어줌으로써 이를 해결할 수 있는 것이지요. 그리고 이렇게 데코레이터에 인자를 넣어줄 수 있도록 하는 방법에는 2가지가 있습니다. 하나는 기존의 데코레이터에 하나의 함수를 더 중첩시키는 방법과, 클래스를 이용해 데코레이터를 구현하는 방법이 있습니다. 일반적으로 두 번째 방법이 더 가독성이 좋지만 여기서는 2개 모두 배워보도록 하겠습니다.  

### 1) 중첩 함수를 이용한 데코레이터

```python
def is_multiple(x):
    def real_decorator(func):
        def wrapper(a, b):
            r = func(a, b)
            if r % x == 0:
                print(f'{func.__name__}의 반환값은 {x}의 배수입니다.')
            else:
                print(f'{func.__name__}의 반환값은 {x}의 배수가 아닙니다.')
            return r
        return wrapper
    return real_decorator


@is_multiple(3)
def add(a, b):
    return a + b
 
print(add(10, 20))
print(add(2, 5))
---------------------------------------
add의 반환값은 3의 배수입니다.
30
add의 반환값은 3의 배수가 아닙니다.
7
```

### 2) 클래스를 이용한 데코레이터
클래스를 이용한 데코레이터에서 파이썬 구문이 어떻게 처리되는지 살펴보겠습니다. 먼저 예시 코드를 보고 설명을 드리도록 하겠습니다.  

```python
class IsMultiple:
    def __init__(self, x):
        self.x = x

    def __call__(self, func):
        def wrapper(a, b):
            r = func(a, b)
            if r % self.x == 0:
                print(f'{func.__name__}의 반환값은 {self.x}의 배수입니다.')
            else:
                print(f'{func.__name__}의 반환값은 {self.x}의 배수가 아닙니다.')
            return r

        return wrapper


@IsMultiple(3)
def add(a, b):
    return a + b


print(add(10, 20))
print(add(2, 5))
-----------------------------------
add의 반환값은 3의 배수입니다.
30
add의 반환값은 3의 배수가 아닙니다.
7
```

`@IsMultiple(3)`에서 @ 연산 전에 전달된 파라미터를 사용해 데코레이터 객체가 생성됩니다. 그 다음 @ 연산이 호출됩니다. 데코레이터 객체는 add함수를 래핑하여 \__call__ 매직 메서드를 호출합니다.