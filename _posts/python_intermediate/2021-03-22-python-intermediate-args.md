---
title:  "[Intermediate] *asterisk"
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

파이썬에서 asterisk는 주로 unpacking하는 용도로 사용됩니다.  
그러나 예외적으로 정의한 함수 안의 파라미터 앞에 쓰일 때는 packing을 위한 용도로 사용 됩니다.

## 1. unpacking

### print

```python
fruits = ['lemon', 'apple', 'banana']

>>> print(fruits)
['lemon', 'apple', 'banana']

>>> print(*fruits)
lemon apple banana
```

### zip  
zip함수는 우리가 원하는 방식으로 사용하려면 입력으로 zip(x, y, z) 이런 식으로 되어야 한다.  
( zip([x, y, z]) 이렇게 묶이면 안된다. )
```python
a = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]

>>> list(zip(a))
[([1, 2, 3],), ([4, 5, 6],), ([7, 8, 9],)]

>>> list(zip(*a))
[(1, 4, 7), (2, 5, 8), (3, 6, 9)]
```

## 2. packing

### *args
여기서 핵심은 args가 아니라 *(asterisk)입니다.  
(args가 아닌 parameters, values 이런식으로 써도 상관은 없지만 개발자들끼리의 약속이니 args로 쓰자.)  
*args는 우리가 작성한 함수의 인자(파라미터)의 갯수를 가변적으로 할 수 있도록 해줍니다.  
(사용자가 함수에 인자로 값을 1개를 넣든 2개를 넣든, 100개를 넣든 괜찮다)  


예시 1
```python
def fruits(*args):
  return args

>>> print(fruits('바나나', '사과', '레몬'))
('바나나', '사과', '레몬')

>>> print(fruits('바나나'))
('바나나')
```  

예시 2
```python
def fruits(*args):
    fruit_list = ""
    for fruit in args:
        fruit_list += fruit + ', '
    print(fruit_list[:-2] + "을 골랐습니다.")

>>> fruits('바나나', '사과', '레몬')
바나나, 사과, 레몬을 골랐습니다.

>>> fruits('바나나', '사과', '레몬', '오렌지', '망고', '귤')
바나나, 사과, 레몬, 오렌지, 망고, 귤을 골랐습니다.
```
  
예시 3 (앞에 customer인자 추가)
```python
def fruits_order(customer, *args):
    fruit_list = ""
    for fruit in args:
        fruit_list += fruit + ', '
    print(customer+"님께서 " + fruit_list[:-2] + "을 골랐습니다.")

>>> fruits_order('Peter', '바나나', '사과', '레몬') # 가장 앞에 있는 값 1개를 자동으로 customer로 지정한다.
Peter님께서 바나나, 사과, 레몬을 골랐습니다.
```  

예시 4 (앞에 customer, 뒤에 message 인자 추가)
```python
def fruits_order(customer, *args, message="감사합니다!"):
    fruit_list = ""
    for fruit in args:
        fruit_list += fruit + ', '
    print(customer+"님께서 " + fruit_list[:-2] + "을 골랐습니다. " + message)

>>> fruits_order('김재영', '바나나', '사과', '레몬') # 따로 message를 정하지 않으면 모두 args로 들어간다.
Peter님께서 바나나, 사과, 레몬을 골랐습니다. 감사합니다!

# 이렇게 인자값을 뒤에 추가하여 사용하려면 함수를 사용할 때 표기를 해줘야 한다 그렇지 않으면 그냥 다 args값으로 들어간다.
>>> fruits_order('김재영', '바나나', '사과', '레몬', message = "주문하시겠습니까?") 
Peter님께서 바나나, 사과, 레몬을 골랐습니다. 주문하시겠습니까?
```  


### **kwargs  

여기서 핵심은 kwargs가 아니라 **입니다.  
인자로 key = value 형태의 값을 넣으면 함수 안에서 딕셔너리 형태로 동작합니다.  

```python
def profile(**kwargs):
    for KEY in kwargs:
        print(f'{KEY}: {kwargs[KEY]}')
>>> profile(name="Peter", age="28", location="Pusan")
name: Peter
age: 28
location: Pusan
```