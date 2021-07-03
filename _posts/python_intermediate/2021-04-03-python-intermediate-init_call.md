---
title:  "[Intermediate] 클래스의 init, call 매직 메소드"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - intermediate
tags:
  - Python
last_modified_at: 2021-04-02
---  


```python
class Train:
    def __init__(self):
        print("객체 생성시 실행되는 init 메소드")

    def __call__(self):
        priont("인스턴스 생성시 실행되는 call 메소드")

>>> a = Train()
객체 생성시 실행되는 init 메소드

>>> a()
인스턴스 생성시 실행되는 call 메소드
```


## __init__  

* 객체가 생성될 때 자동으로 실행되는 매직 메소드  

## __call__  

* 인스턴스가 생성될 때 자동으로 실행되는 매직 메소드  




