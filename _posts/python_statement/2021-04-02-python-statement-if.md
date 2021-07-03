---
title:  "[Statement] 제어문(if)"
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


## 1. if, elif, else  

if, elif, else는 코드의 흐름을 제어할 때 사용하는 제어문입니다. 바로 예를 들어 설명하겠습니다.  

```python
result = True
if result:
    print('Pass')
else:
    print('Fail')
```

if 다음에 나오는 올 수 있는 값은 bool타입의 자료형으로 True이면 if문을 실행하고, False이면 else문을 실행합니다. 만약 조건을 좀 더 세부적으로 나누고 싶을 때는 elif문을 추가하면 됩니다.  

```python
score = 85

if score>90:
    print('A')
elif score>60:
    print('B')
else:
    print('C')
```

## 2. True와 False  

if 다음에 오는 값의 타입이 불리언이 아니면 어떻게 될까요? 파이썬은 자동적으로 그 값을 불리언 타입으로 변환시켜줍니다. 그렇다면 어떤 값이 True로 변환되고, 어떤 값이 False로 변환되는지 아는 것이 굉장히 중요합니다. 대부분의 경우 True변환되는 경우에 해당하기 때문에 False로 변환되는 값들만 적고 나머지는 True가 된다고 생각하시면 될 것 같습니다.  

🔔 False로 인식되는 다른 자료형 값  

|자료형|값|
|:---|:---|
|null|none|
|int|0|
|float|0.0|
|str|''|
|list|[]|
|tuple|()|
|dict|{}|
|set|Set()|  

## 3. 여러 가지 예시  

### 3-1 True 또는 False  
True가 오면 if문 실행 False가 오면 if문을 실행하지 않고 else가 있으면 else문 실행, 뒤에 아무 것도 없다면 그냥 아무것도 실행하지 않습니다.  

```python
if False:
    print('나는 절대 볼 수 없어요')
# else문이 있으면 print문 출력, 없으면 아무 것도 나오지 않는다
else:
    print('나는 무조건 봐요`)
```

### 3-2 다른 자료형 값  
bool(값) == False 이면 if문 실행되지 않고, True이면 실행될 것입니다. False에 해당하는 값은 위의 표에서 살펴보았습니다.  

```python
if 0:
    print('나는 절대 볼 수 없어요')
else:
    print('나는 무조건 봐요')
```  

### 3-3 비교연산자  
비교 연산의 결과가 True이면 if문 실행, False이면 if문은 실행되지 않습니다.  

```python
if 15 > 10:
    print('나는 무조건 봐요`)
else:
    print('나는 절대 볼 수 없어요')
```

### 3-4 논리연산자  
조건을 하나가 아니라 여러 개를 비교하고 싶은 경우 논리 연산자를 사용할 수 있습니다.  

```python
if 10 > 5 and 'a'>'b':
    print('나는 절대 볼 수 없어요')
else:
    print('나는 무조건 봐요')
```
```python
letter = 'o'
if letter =='a' or letter == 'e':
    print('나는 절대 볼 수 없어요')
elif letter =='i' or letter =='o':
    print('나는 무조건 봐요)
else:
    print('나는 절대 볼 수 없어요')
```

### 3-5 멤버십 연산자 in  
시퀀스 자료형이 가지는 값이 있는지 확인하고 싶을 때 사용하기 좋다. 물론 시퀀스 자료형 아니어도 실행된다.  

```python
vowels = 'aeiou' # ['a', 'e', 'i', 'o', 'u'], }{'a', 'e', 'i', 'o', 'u'}, ...
letter = 'o'
if letter in vowels:
    print(letter, 'is a vowel')
```
```python
vowel_o = 'o'
letter = 'o'
if letter in vowel_o:
    print(letter, 'is o')
```  

## 4. 새로운 기능: 바다코끼리 연산자(walrus operator)
파이썬3.8의 새로운 바다코끼리 연산자는 할당과 테스트를 한 단계로 줄여줍니다.  

```python
tweet_limit = 280
tweet_string = "Blah"*50

# diff 정의와 동시에 if문에 사용
if diff:=tweet_limit-len(tweet_string) > 0:
    print('A fitting tweet')
else:
    print('Too long)
```