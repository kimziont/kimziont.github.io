---
title:  "[Intermediate] 타입 힌트"
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


## 타입 힌트
파이썬은 대표적인 동적 타이핑 언어임에도, 타입을 지정할 수 있는 타입 힌트를 파이썬 3.5부터 사용할 수 있게 되었다.  
타입 힌트가 중요한 이유는 나중에 프로젝트의 규모가 커지게 되면, 다른 사람이 (나를 포함) 정의한 함수의 리턴 값이 무엇인지, 파라미터로 어떤 값을 넣어야 하는지
어렵게 된다. 다시 말해 코드의 가독성이 떨어지게 된다. 예를 들면,   

```python
# 가독성이 떨어지는 코드
def fn(a):
    ...

# 타입 힌트를 사용한 코드
def fn(a: int) -> bool:
    ...

# 변수 정의할 때도 사용할 수 있다
a: str = "1"
```  

### mypy
mypy를 사용하면 타입 힌트에 오류가 없는지 자동으로 확인할 수 있습니다.  

```python
!pip install mypy

mypy 파일이름.py
```