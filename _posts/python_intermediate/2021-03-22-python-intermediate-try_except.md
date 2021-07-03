---
title:  "[Intermediate] 예외 처리"
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


## 예외처리
오류가 나더라도 일단 실행한 코드가 정상적으로 종료될 수 있게 해준다.  
이전까지는 if문을 통해 선처리 후실행 되도록 했는데, try문을 사용하면 선실행 후처리하도록 프로그래밍 할 수 있습니다.

### try, except
try문을 무조건 실행하고 오류가 발생하면 except문이 실행된다.
```python
emp = []
for i in [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, "ten"]:
    try :
        emp.append(round(10/i, 2))

    except ZeroDivisionError:
        emp.append(i)
    except TypeError:
        emp.append(i)

>>> print(emp)
[0, 10.0, 5.0, 3.33, 2.5, 2.0, 1.67, 1.43, 1.25, 1.11, 'ten']
```
0으로 나누면 ZeroDivisionError가 발생하여 코드가 실행 도중 멈추게 되지만, 예외처리를 함으로써 정상적으로 종료시켜준다.   
문자열 "ten"으로 나누는 것은 지원하지 않기 때문에 TypeError가 발생한다. 이것도 예외처리를 하면 정상적으로 종료시켜준다.

### else, finally
else는 오류가 나지 않으면 실행되고, finally는 무조건 실행된다.  

```python
emp = []
for i in [0, 1, 2, "car"]:
    try :
        emp.append(10/i)

    except ZeroDivisionError:
        print("0으로 나눌 수 없습니다.")
        emp.append(i)
    except TypeError:
        print("지원하지 않는 타입입니다.")
        emp.append(i)
    else:
        print("정상 실행되었습니다.")
    finally:
        print("수고하셨습니다.\n")

>>> print(emp)
0으로 나눌 수 없습니다.
수고하셨습니다.

정상 실행되었습니다.
수고하셨습니다.

정상 실행되었습니다.
수고하셨습니다.

지원하지 않는 타입입니다.
수고하셨습니다.

[0, 10.0, 5.0, 'car']
```