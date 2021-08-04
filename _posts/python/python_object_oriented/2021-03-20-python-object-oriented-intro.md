---
title:  "[Object-oriented] 객체 지향 프로그래밍 Intro"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - object-oriented
tags:
  - Python
last_modified_at: 2021-03-22
---

## 객체지향 프로그래밍이란?

프로그램을 여러 개의 독립된 객체들과 그 객체들 간의 상호작용으로 파악하는 프로그래밍 접근법

### 객체 지향 프로그래밍으로 프로그램을 만들려면

1. 어떤 객체들이 필요할지 정한다.
2. 객체들의 속성과 행동을 정한다.
3. 객체들이 서로 어떻게 소통할지 정한다.

#### 객체(인스턴스)를 만들려면?

클래스라는 틀을 이용해 객체(인스턴스)를 만든다.  

![](/assets/images/class.png){: width="70%" height="70%"}  
(출처: 코드잇)

```python
"""User 클래스, User클래스를 이용해 인스턴스 생성시 name, email과 같은 변수 자동으로 지정"""
class User:
	# 인스턴스 초기화 메소드 -> 인스턴스 생성시 인스턴스의 속성으로 지정된다
	def __init__(self,name, email):
		self.name = name
		self.email = email


"""인스턴스 user_1 생성"""
user_1 = User("Peter", "ziont0510@naver.com")
```

```python
class User:
    def __init__(self,name, email):
		self.name = name
		self.email = email

"""print문으로 인스턴스 출력시 자동으로 리턴"""
	def __str__(self):
		return "사용자:{} \n 이메일:{}".format(self.name, self.email


user_1 = User("김재영", "ziont0510@naver.com")
print(user_1)
```

#### 클래스 변수, 클래스 메소드

같은 클래스로 만들어진 인스턴스들이 함께 공유하고 싶은 변수는 클래스 변수로 지정

```python
class User:
"""클래스 변수 count 생성, 인스턴스 생성될때마다 count 1씩 증가"""
	count = 0
	def __init__(self,name, email):
		self.name = name
		self.email = email
		User.count += 1

user_1 = User("김재영", "ziont0510@naver.com")
user_2 = User("Jay kim", "orange0510@abc.com")
```

메소드가 인스턴스 변수를 전혀 사용하지 않을 경우에는 클래스 메소드로 만들어 준다.

```python
class User:
	count = 0

	@classmethod
	def num_instance(cls):
		return cls.count

	def __init__(self,name, email):
		self.name = name
		self.email = email
		User.count += 1

user_1 = User("김재영", "ziont0510@naver.com")
print(user_1.num_instance())

```

#### 정적메소드

인스턴스, 클래스 변수 모두 사용하지 않는 경우에는 정적 메소드로 만들어준다.

```python
@staticmethod
def hello():
		return "안녕하세요"

# @staticmethod 안적어주면 무조건 hello(self) 이렇게 적어야 한다.
# 이렇게 해도 출력하는데는 문제 없음
```

#### 데코레이터

여러함수들 간에 겹치는 공통적인 부분을 데코레이터를 통해 간단하게(?) 할 수 있다.

```python
class Download:
	def add_print_to(original):
		def wrapper():
			print("컴퓨터 전원을 켰습니다."
			original()
			print("컴퓨터 전원을 끕니다.\n")
		return wrapper

	@add_print_to
	def python_download():
		print("파이썬을 다운로드 중입니다.")
	
	@add_print_to
	def league_of_legend_download():
		print("리그오브레전드를 다운로드 중입니다.")

down_1 = Download()
down_1.python_download()
down_1.league_of_legend_download()

---------------------------------------------
컴퓨터 전원을 켰습니다.
파이썬을 다운로드 중입니다.
컴퓨터 전원을 끕니다.

컴퓨터 전원을 켰습니다.
리그오브레전드를 다운로드 중입니다.
컴퓨터 전원을 끕니다.
```