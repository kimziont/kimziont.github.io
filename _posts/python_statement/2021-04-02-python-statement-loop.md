---
title:  "[Statement] 반복문(for/while)"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - statement
tags:
  - Python
last_modified_at: 2021-04-02
---


## 1. while 문  
가장 간단한 반복 매커니즘은 while 문입니다. 예를 들어 보겠습니다.  
```python
count = 1 
while count < 5:
  print(count)
  count += 1
-----------------
1
2
3
4
```  
### 1-1 break  
무한 반복될 가능성이 있는 경우 break를 통해 빠져나올 수 있도록 탈출구를 만들 수 있습니다.  
```python
while True:
  stuff = input("String to capitalize [type q to quit]: ")
  if stuff == 'q':
    break
  print(stuff.capitalize())
```

### 1-2 continue  
아무것도 하고 싶지 않고 그냥 다음 반복으로 넘어가고 싶은 경우에 사용할 수 있습니다.  
```python
while True:
  stuff = input("홀수 출력[type q to quit]: ")
  if stuff == 'q':
    break
  if stuff % 2 == 0:
    continue
  print(stuff) 
```

### 1-3 while-else
while과 else의 조합은 그냥 브레이크 체커라고 생각하면 됩니다. 이게 무슨 말이냐면 while문이 모두 실행되었지만 break 문을 만나지 못한 경우 else 문이 실행된다.  
```python
numbers = [1, 3, 5]
position = 0
while position < len(numbers):
  number = numbers[position]
  if number % 2 == 0:
    print("Found even number", number)
    break
  positoin += 1
else:
  print('No even number found')
``` 

## 2. for 문  

```python
word = 'thunder'
for letter in word:
  print(letter)
```

### 2-1 break  
```python
word = 'thunder'
for letter in word:
  if letter =='u':
    break
  print(letter)
```

### 2-2 continue  
```python
word = "thunder"
for letter in word:
  if letter in 'aeiou':
    continue
  print(letter)
```  

### 2-3 for-else  
```python
word = 'thunder'
for letter in word:
  if letter == 'x':
    print("There is x", end=' ')
    break
  print(letter)
else:
  print("There is no x")
----------------------------
t h u n d e r There is no x
```
for문에서 print문 실행되지만 break만나지 못했기 때문에 else 문이 또한 실행됩니다.  

### 2-4 range  
range()함수는 리스트나 튜플 같은 자료구조를 생성해 저장하지 않더라도 특정 범위 내에서 숫자 스트림을 반환합니다. 따라서 컴퓨터의 메모리를 전부 사용하지 않고, 아주 큰 범위의 스트림을 생성 할 수 있게 해줍니다.  
range()는 순회 가능한 객체를 반환합니다. 그러므로 for in 형태로 값을 순회할 수 있습니다. 또 list()함수를 이용해 리스트로 형변환 할 수 도있습니다. 