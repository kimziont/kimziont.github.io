---
title:  "[Intermediate] 파이썬의 Container 객체"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - intermediate
tags:
  - Python
last_modified_at: 2021-09-18
---
# 파이썬의 Container 객체

컨테이너는 `__contains__` 메소드를 구현한 객체로 `__contains__` 메소드는 일반적으로 boolean 값을 반환합니다. 이 메소드는 파이썬에서 `in 키워드`가 사용될 때 호출됩니다.  

```python
element in container는 다음과 같이 해석됩니다.

container.__contains__(element)
```

예제를 하나 살펴보겠습니다.  

```python
class Boundary:
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def __contains__(self, coord):
        x, y = coord
        return 0 <= x < self.width and 0 <= y < self.height


boundary = Boundary(5, 10)

print((4, 2) in boundary)
print((3, 20) in boundary)
--------------------------------------------------
True
False
```