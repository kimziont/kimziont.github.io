---
title:  "[Intermediate] Context manager type"
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
# Context manager 내장 타입  

Context manager라는 것은 파이썬에서 제공하는 내장 타입(예를 들면, int, float, list, dict, ..) 중 하나로 파이썬의 모든 것이 객체이듯, Context manager 또한 객체로 만들어져 있습니다.  

저희는 Int형, Float형과 같은 내장 타입에 대해서는 많이 사용도 해보아서 잘 알고 익숙합니다. 그러면 Context manager는 어떤 역할을 하는 내장 타입일까요?  

제가 생각했을 때는, Context manager는 말 그대로, 구문(Context)를 관리(manager)해주는 역할을 한다고 봅니다. 여기서 구문은 어떤 것도 올 수 있습니다. Context manager는 그냥 구문의 시작(enter)과 끝(exit)만 담당하여 구문을 관리할 뿐입니다.  

Context manager 클래스는 2개의 매직 메소드로 구성됩니다.  

```
1. __enter__
2. __exit__
```

그리고 이 2개의 매직 메소드를 알아서 호출해주는 구문이 바로 `with`statement 입니다. `with`statement는 뒤에 나오는 Context manager의 `__enter__`메소드를 호출합니다. \__enter__()는 어떤 value를 return 하기도 합니다. 만약 어떤 value를 return하고 그 값을 사용하고 싶다면 `as` 뒤에 나오는 변수에 할당할 수 있습니다. 그리고 난 후 `with`statement 블록 안의 context를 실행하며 블록 끝에 다다르면 자동으로 Context manager의 `__exit__`메소드가 호출됩니다. 간단한 예시를 한번 보겠습니다.  

```python
# 인사말을 해주는 Context manager
class SayHello:
    def __enter__(self):
        print("Hello nice to meet you")

    def __exit__(self, exc_type, ex_value, ex_traceback):
        print("Goodbye")

# with문을 이용해 Context manager의 __enter__, __exit__ 자동 호출
with SayHello():
    x = 1
    y = 2
    z = x + y
    print(z)
------------------------------------------------------------
Hello nice to meet you
3
Goodbye
```  

```python
class SayHello:
    def __enter__(self):
        print("Hello nice to meet you")
        return "안녕하세요!" # return문 생성

    def __exit__(self, exc_type, ex_value, ex_traceback):
        print("Goodbye")

with SayHello() as f: # f에 return 되는 값 할당하기
    print(f)
    x = 1
    y = 2
    z = x + y
    print(z)
------------------------------------------------------------
Hello nice to meet you
안녕하세요
3
Goodbye
```  

🔔 __open 함수__
with statement와 정말 자주 사용되는 것이 바로 `open`함수 입니다. `open`함수는 파일을 열고 해당하는 file object를 return합니다. 여기서 [file object](https://docs.python.org/3/glossary.html#term-file-object)는 [io](https://docs.python.org/3/library/io.html) module을 통해 file object를 다룬다.  

> There are actually three categories of file objects: raw binary files, buffered binary files and text files. Their interfaces are defined in the io module  

또한 io module의 가장 상단에 위치한 abstract base class는 바로 [IOBase](https://docs.python.org/3/library/io.html#io.IOBase)인데 이 추상클래스는 메소드로 `__enter__`와 `__exit__`메소드를 가지고 있다. 따라서 IOBase는 Context manager이기도 하다.  

> IOBase is also a context manager and therefore supports the with statement. In this example, file is closed after the with statement’s suite is finished—even if an exception occurs  

결론은 `open`함수는 file object를 return하고 file object는 Context manager이다. 그렇기 때문에 with statement와 함께 사용될 수 있는 것이고, 파일을 열면 파일 디스크립터 누수를 막기 위해 닫아주는 것이 중요하다는 점에서 `open`함수가 `with`statement와 자주 사용되는 것이다.  

🔔 __contextlib 모듈__  
\__enter__와 \__exit__를 이용해서 Context manager를 구현할 수도 있지만, 파이썬에서는 이를 모듈로 제공하고 있다. 제너레이터 형태의 함수를 `contextlib.contextmanager`데코레이터로 감싸주면 그 함수는 Context manager로 사용할 수 있다.  

```python
import contextlib


def say_hi():
    print("Hello nice to meet you")
    return "안녕하세요"


def say_bye():
    print("Goodbye")


@contextlib.contextmanager
def greet():
    say_hi()
    yield
    say_bye()


with greet():
    a = 1
    b = 2
    c = a + b
    print(c)

------------------------------
Hello nice to meet you
3
Goodbye
```