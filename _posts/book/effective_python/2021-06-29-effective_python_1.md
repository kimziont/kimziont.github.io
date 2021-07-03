---
title:  "[책] 파이썬 코딩의 기술 part1: 파이썬답게 생각하기"
toc: true
toc_sticky: true
author_profile: true
sidebar_main: true
header:
  teaser: /assets/images/python.png

categories:
  - effective_python
tags:
  - python
last_modified_at: 2021-06-29
---  


# 파이썬답게 생각하기

파이썬 커뮤니티는 어떤 고유한 스타일을 따르는 코드를 가리킬 때 '파이썬다운(Pythonic)'이라는 형용사를 사용합니다.  

개인적으로 파이썬으로 코드를 작성할 때 가장 신경써야할 부분은 `가독성`이라고 생각합니다. 가독성은 사실 협업을 위해서라면 모든 코드에서 반드시 갖춰야 할 요소입니다. 그러나 그중에서도 파이썬으로 코드를 작성할 때 만큼은 더욱 이 가독성을 신경써줘야 한다 생각하는데 그 이유는, 실무에서 파이썬은 어떤 새로운 아이디어를 본격적으로 제품화하기 전에 `간단히 프로토타입을 만들기 위한 언어로 참 좋은 언어`입니다. 그래서 저는 개인적으로 파이썬은 다음에 누가 보더라도 명확하게 이해할 수 있도록 코드를 작성해야 한다고 생각합니다.  

따라서 가독성이 높은 코드를 작성하기 위해서는 자기만 이해할 수 있는 코드가 아닌 `파이썬 개발자들이 모두 지향하는 방식`으로 코드를 작성해야 한다고 생각하며, 이 책이 그러한 관점에서 좋은 길잡이가 될 수 있을거라 생각합니다.  

## Better Way 1: 사용 중인 파이썬의 버전을 알아두라  

사용하는 파이썬의 버전을 정확히 알고 싶으면 --version 플래그를 사용하라.  

```
$ python --version
Python 2.7.10
```

파이썬3는 보통 python3라는 이름을 사용한다.  

```
$ python3 --version
Python 3.8.0
```  

파이썬 코드에서 현재 실행 중인 파이썬 버전을 알고 싶다면  

```python
import sys

# 첫 번쨰 방법
print(sys.version)

# 두 번째 방법
print(sys.version_info)
```  

## Better Way 2: PEP 8 스타일 가이드를 따르라  

PEP(Python Enhancement Proposal) #8은 파이썬 코드를 어떤 형식으로 작성할 지 알려주는 스타일 가이드다. 문법만 올바르다면 어떤 방식으로든 원하는 파이썬 코드를 작성해도 좋지만, 일관된 스타일을 사용하면 가독성을 더 높일 수 있다.  
PEP 8은 깔끔한 파이썬 코드를 작성하는 방법을 아주 자세히 알려준다. [PEP 8 온라인 가이드](https://www.python.org/dev/peps/pep-0008/)  

## Better Way 4: 문자열 포맷팅을 할 때는  f-문자열을 통한 인터폴레이션을 사용하라  

포맷팅은 미리 정의된 문자열에 데이터 값을 끼워 넣어서 사람이 보기 좋은 문자열로 저장하는 과정이다.  

### C스타일 형식 문자열 

이 방식은 형식 문자열(ex. "안녕 ~")에 있는 형식 지정자(format specifier)(ex. %s, %d)와 데이터 값의 순서를 바꾸면 오류가 발생해서 좌우를 잘 확인하여야 한다. 가독성도 떨어져서 좋은 방식이 아니다.  
```python
'안녕 나는 %s라고 해' % 'Peter'

'안녕 내 이름은 %s이고, 나이는 %d야' % ('Peter', 20)
```  

### 내장 함수 format과 문자열 메소드 format  

파이썬 3부터는 %를 사용하는 오래된 C스타일 형식화 문자열 보다 더 표현력이 좋은 고급 문자열 형식화 기능이 도입됐다.  

```python
# 내장 함수 format
>>> a = 1234.5678
>>> formatted = format(a, '.2f')
>>> print(formatted)
1234.57

# str타입의 format메소드
>>> key = 'my_var'
>>> value = 1.234
>>> formatted = '{}={}'.format(key, value)
>>> print(formatted)
my_var = 1.234

# 위치 인덱스를 전달할 수도 있다.
formatted = '{1} = {0}'.format(key, value)
```

### f-문자열  

파이썬 3.6부터는 인터폴레이션을 통한 형식 문자열(줄여서 f-문자열이라고 부름)이 도입됐다. 현재 가장 선호되는 방식이다.  

```python
key = 'my_var'
value = 1.234

formatted = f'{key} = {value}'

# 옵션 주는 방법

>>> f_string = f'{key:<10} = {value:.2f}'
>>> print(f_string)
my_var     = 1.23
```  

## Better Way 6: 인덱스를 사용하는 대신 대입을 사용해 데이터를 언패킹하라

인덱스로 시퀀스 데이터에 접근하면 명시적이지 않아 가독성이 떨어진다. 언패킹하면 훨씬 가독성이 좋아진다.  

```python
person_list = [('kevin', 20), ('mike', 21), ('suzan', 20), ('steve', 25)]

# 지양되는 방식
for i in range(len(person_list)):
    name = person_list[i][0]
    age = person_list[i][1]

# 더 나은 방식
for name, age in person_list:
```  

## Better Way 9: for-else, while-else문 대신 함수(def-return)를 사용하라  

for나 while과 같은 반복문이 break에 의해 중간에 종료되지 않고 끝까지 순회한 후에 비로소 종료되면 뒤에 else문을 실행하는 구문이 있다. (저는 사실 이 방법을 자주 사용합니다..)

```python
a, b = 4, 9

for i in range(2, min(a, b) + 1):
    if a % i == 0 and b % i == 0:
        print('서로소 아님')
        break
else:
    print('서로소')
```

근데 이러한 방식으로 작성하는 코드는 가독성이 떨어진다. (그런가..?) 그래서 for-else같은 구문 보다는 함수를 사용하는 편이 낫다. 함수는 return문을 만나면 값을 return하고 함수를 곧바로 종료하기 때문에 break와 같은 효과를 가진다고 볼 수 있다.  

```python
def coprime(a, b):
    for i in range(2, min(a, b) + 1):
        if a % i == 0 and b % i == 0:
            return False
    return True
```