---
title:  "[Intermediate] 파이썬에서 객체의 속성 얻기"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - intermediate
tags:
  - Python
last_modified_at: 2021-09-19
---
# 객체의 속성 얻기 (\__dict__, \__getattribute__, \__getattr__,  getattr)

이번 포스트에서는 파이썬에서 객체의 속성을 얻는 과정을 조금 자세히 살펴보겠습니다.  

## 1. 속성을 얻는 과정  

```
a.num을 호출하면

a.__dict__에서 num을 찾는다
  num이 있으면
    a.__getattribute__("num") 호출
  
  num이 없으면
    __getattr__가 정의되어 있으면
      a.__getattr__(num) 호출
    
    정의되어 있지 않으면
      AttributeError: 'Apple' object has no attribute 'num'
```

### 1) __getattr__이 정의되어 있지 않은 경우

```python
class Apple:
    def __init__(self, num, price):
        self.num = num
        self.price = price


a = Apple(num=5, price=2000)

print(a.num)

print(a.__dict__)
print(a.__getattribute__("num"))

print(a.nu)
-------------------------------------
5
{'num': 5, 'price': 2000}
5
AttributeError: 'Apple' object has no attribute 'nu'
```

### 2) __getattr__이 정의되어 있는 경우  

```python
class Apple:
    def __init__(self, num, price):
        self.num = num
        self.price = price

    def __getattr__(self, attr):
        print(f"{attr}이라는 속성은 없습니다")


a = Apple(num=5, price=2000)


print(a.num)

print(a.__dict__)
print(a.__getattribute__("num"))

print(a.nu)
--------------------------------------
5
{'num': 5, 'price': 2000}
5
nu이라는 속성은 없습니다
None
```

### 3) getattr 내장함수
우리는 얻고자 하는 속성에 접근할 때 속성이 없으면 \__getattr__을 호출한다고 배웠습니다. 그래서 원하는 속성이 없을 때 다른 것(예를 들면 default)을 리턴하고 싶으면 \__getattr__에 정의함으로써 해결할 수 있습니다.  

하지만 접근하고자 하는 속성마다 없을 때 다를 default값을 리턴해주고 싶으면 어떻게 해야할까요? \__getattr__을 이용해 구현할 수도 있겠지만 if분기문을 이용해 미리 모든 케이스를 정의해야 합니다. 조금 더 동적으로 이 문제를 해결하고 싶은데 그 때 사용하는 방법이 바로 `getattr` 내장함수입니다. 예시를 보면 더 이해가 잘 될겁니다.  

```python
class Apple:
    def __init__(self, num, price):
        self.num = num
        self.price = price

    def __getattr__(self, attr):
        print(f"{attr}이라는 속성은 없습니다")
        raise AttributeError


a = Apple(num=5, price=2000)


print(getattr(a, "nu", "default"))

print(a.nu)
-----------------------------------------
nu이라는 속성은 없습니다
default
nu이라는 속성은 없습니다
raise AttributeError
```

getattr 내장함수를 이용해 속성을 얻을 경우, 없으면 3번 째 인자에 넣어준 파라미터(default)를 리턴합니다.  

참고로 gettattr 내장함수는 객체가 \__getattr__메소드가 없어도 됩니다.  

```python
class Apple:
    def __init__(self, num, price):
        self.num = num
        self.price = price



a = Apple(num=5, price=2000)

print(getattr(a, "nu", "default"))
print(getattr(a, "nu"))
-------------------------------
AttributeError: 'Apple' object has no attribute 'nu'
default
```
## 2. 정리

- 속성에 접근할 경우 \__dict__에서 속성 유무 확인, 있으면 \__getattribute__, 없으면 \__getattr__ 호출
- AttributeError만 해결하고자 하는 경우 -> \__getattr__ 구현
- AttributeError 해결 뿐만 아니라 default값까지 동적으로 리턴하고 싶은 경우 -> \__getattr__에서 AttributeError 발생