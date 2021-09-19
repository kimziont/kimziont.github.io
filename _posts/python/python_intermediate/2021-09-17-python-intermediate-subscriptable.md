---
title:  "[Intermediate] 파이썬의 Subscriptable 객체"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - intermediate
tags:
  - Python
last_modified_at: 2021-09-17
---
# 파이썬의 Subscriptable 객체

## 1. not subscriptable 에러  

파이썬을 사용하다 보면 다음과 같은 에러를 자주 보게 됩니다.  

``` 
TypeError : 'int' object is not subscriptable
```

subscriptable이라는 단어는 실제로 영어권 국가에서 쓰는 단어는 아닌 것 같고, 파이썬에서 사용하는 표현 중 하나인 것 같습니다. 대략 뜻은 `인덱싱이나 슬라이싱이 가능한` 정도로 이해하면 될 것 같습니다.  

그러면 subscriptable한 객체를 만들려면 어떻게 해야할까요?  

`__getitem__ 매직 메소드를 클래스에서 정의`하면 됩니다. 간단한 예제를 한번 보도록 하겠습니다.  

```python
class MyList:
    def __init__(self, *values):
        self._values = list(values)

    def __getitem__(self, item):
        return self._values.__getitem__(item)


mylist = MyList(1, 3, 5, 7, 9)

print(mylist[-1])
print(mylist[1:3])

for i in mylist:
    print(i)
-----------------------------------------
9
[3, 5]
1 3 5 7 9 
```

## 2. 내장(built-in) 타입 커스텀하기  
내장 타입을 커스텀하여 사용하고 싶은 경우 올바른 방법은 `collections` 모듈을 사용하는 것입니다. 이를 사용하면 예상치 못한 결과를 사전에 방지할 수 있습니다. 사실 직접 커스텀해서 생기는 문제에 대해서는 아직 잘 모르겠지만, 이 문제를 살펴보려면 CPython이나 PyPy와 같은 곳에서 세부 구현 사항들이 어떻게 되는지 확인해봐야 할 것 같습니다. 여기서는 `collections`모듈을 사용하면 이러한 걱정을 안해도 된다는 정도만 짚고 넘어가도록 하고, 예제를 하나 보도록 하겠습니다.  

```python
from collections import UserList

# UserList 상속받음
class MyList(UserList):
    def __init__(self, *values):
        self._values = list(values)

    def __getitem__(self, item):
        return self._values.__getitem__(item)


mylist = MyList(1, 3, 5, 7, 9)

print(mylist[-1])
print(mylist[1:3])

for i in mylist:
    print(i, end=' ')
-----------------------------------------
9
[3, 5]
1 3 5 7 9 
```

결과는 처음에 살펴본 예제와 같지만, `UserList를 상속 받음으로써`, 커스텀한 MyList에 조금 더 복잡한 기능을 구현하면서 생기는 이해하기 힘든 에러들을 방지할 수 있습니다. 참고로 UserList뿐만 아니라 `UserDict, UserString`도 있습니다.  