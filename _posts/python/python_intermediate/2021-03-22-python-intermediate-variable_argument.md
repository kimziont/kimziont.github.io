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

# 가변 인자 Asterisk

## 1. 함수 안에서 정의하는 경우: packing

```python
def purchase(*product):
    print(f"{product}를 구매하였으며 수량은 {len(product)}개 입니다.")


purchase("사과", "바나나", "딸기")
-----------------------------------------------
('사과', '바나나', '딸기')를 구매하였으며 수량은 3개 입니다.
```

`purchase` 함수의 인자는 `product` 1개 뿐이지만, 함수를 사용할 때는 3개의 값을 넣었습니다. 원래는 `TypeError: purchase() takes 1 positional argument but 3 were given`과 같은 에러가 나지만 앞에 *(asterisk)를 붙여줌으로써 여러 개의 값을 넣어주더라도 product하나로 패킹해버립니다. 근데 지금 출력문을 보면 패킹되어있는 모습이 약간 불편합니다. 조금 더 예쁘게 출력하기 위해 패킹되어 있는 상태를 언패킹하고 싶습니다.  

## 2. 함수 실행할 때 사용하는 경우: unpacking

```python
def purchase(*product):
    print(*product, end="")
    print(f"를 구매하였으며 총 개수는 {len(product)}개입니다.")


purchase("사과", "바나나", "딸기")
-----------------------------------------------
사과 바나나 딸기를 구매하였으며 총 개수는 3개입니다.
```

print()함수 내에서 *(asterisk)를 사용해 언패킹을 시행하였습니다.  

unpacking은 `zip` 함수와 함께 자주 사용됩니다.  

```python
seq = [['가', '나', '다'], ['A', 'B', 'C'], [1, 2, 3]]

for i in zip(*seq):
    print(i)
-------------------------------------
('가', 'A', 1)
('나', 'B', 2)
('다', 'C', 3)
```

## 3. 변수명 앞에 붙인 경우: 부분적 unpacking

```python

# first, second, third, fourth, fifth = ['철수', '영구', '영미', '짱구', '순애']


first, *rest = ['철수', '영구', '영미', '짱구', '순애']

print(first)

print(rest)
-------------------------------------
철수
['영구', '영미', '짱구', '순애']
```

['철수', '영구', '영미', '짱구', '순애']로 묶여있는 데이터를 부분적으로 unpacking 하였습니다.

```python
first, *rest, last = ['철수', '영구', '영미', '짱구', '순애']

print(first)

print(rest)

print(last)
----------------------------------------
철수
['영구', '영미', '짱구']
순애
```

```python
*first, rest, last = ['철수', '영구', '영미', '짱구', '순애']

print(first)

print(rest)

print(last)
---------------------------------------------
['철수', '영구', '영미']
짱구
순애
```

## 4. 가변 키워드 인자

```python
def checklist(**kwargs):
    for key in kwargs:
        print(f"{key}: {kwargs[key]}")


checklist(mike="check", john="not check")
---------------------------------------------
mike: check
john: not check
```

인자에 값을 넣어줄 때, 마치 mike, john이라는 인자가 있는 것처럼 편하게 사용하면 됩니다.
