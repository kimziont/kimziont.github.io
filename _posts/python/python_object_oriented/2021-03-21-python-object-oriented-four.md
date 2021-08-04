---
title:  "[Object-oriented] 객체 지향 프로그래밍 4가지 특성"
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

## 1. 추상화  

> 프로그래머들이 특정 코드를 사용할 때 필수적인 정보를 제외한 세부사항을 가리는 것  

클래스, 메소드, 변수가 추상화의 예시  

### 코드를 작성할 때 협업을 위한 일종의 센스 같은 것  

1. 클래스, 메소드, 변수 이름 잘 짓기  
2. Doctstring """~하는 클래스(메소드) ~을 리턴하는 메소드"""  
3. Type hinting  

```python
class BankAccount:
		"""은행 계좌 클래스"""
		interest: float = 0.02

		def deposit(self, amount: float) -> None:
				"""잔액 인스턴스 변수 balance를 파라미터 amount만큼 늘린다"""
				self.balance += amount
```  
  
help()함수를 실행하면 상황을 잘 보여준다.  

(내가 적은 Doctstring과 Type hinting이 출력된다)  

클래스를 넣어도 되고 메소드를 넣어도 되고 리스트를 넣어도 되고 다 된다.  


## 2. 캡슐화

- 외부로부터의 직접적인 접근 차단
- 객체의 속성과 그것을 사용하는 행동을 하나로 묶는 것

### __를 통한 캡슐화

__변수, __메소드 이런식으로 외부에서 직접적으로 접근할 수 없도록 하고 싶은 변수나 메소드 앞에 언더바를 두개 쓰면 캡슐화가 된다.

```python
class User:
		def __init__(self, name, age, bloodtype):
				self.name = name
				self.__age = age
				self.__bloodtype = bloodtype

		def __random_method(self):
				.........
```

근데 사실 접근 가능하다. user_1.\__age 아닌 user_1._User__ age 로 접근하면 접근된다 (just Name mangling)**

### getter setter 메소드

외부에서 직접적인 접근을 피하고 메소드를 통해 접근(get), 수정(set), 행동(기타 메소드)하도록 한다.

![](/assets/images/capsule.png){: width="30%" height="30%"}  
(출처: 코드잇)


```python
def __init__(self, name, age):
		self.name = name
		self._age = age # __사용해도 결국 접근 가능, 그래서 그냥 약속으로 _를 사용, 별다른 기능은 없다

"""getter메소드"""
def get_age(self):
		return self._age

"""setter메소드"""
def set_age(self, age):
		if age < 0:
				print("나이는 음수가 될 수 없습니다.")
		else:
				self._age = age

"""행동(기타 메소드)"""
def alchol_age(self):
		if self._age > 20:
				print("음주 가능한 나이입니다.")
		else:
				print("미성년자는 음주가 불가합니다.")
		
```

### 데코레이터를 이용한 캡슐화

밖에서 사용할 때는 마치 캡슐화 안된 것처럼 사용할 수 있다.

아예 캡슐화를 안한 것과의 차이점은 최소한의 방어막(?) 역할을 한다. ex) 음수로 수정 못하게 한다.

#### @property 데코레이터

```python
@property
def age(self):
		return self._age
```

age로 바로 변수에 접근 가능 → 캡슐화 해도 별도의 코드 수정 없이 ( 마치 캡슐화 안된 것처럼 사용가능)

#### @age.setter 데코레이터

```python
class User:

		def __init__(self, age):
				self._age = age

		@property
		def age(self):
				return self._age

		@age.setter
		def age(self, age):
				if age < 0 :
						print("나이는 음수가 될 수 없습니다.")
				else:
						self._age = age

peter = User()
"""별도의 수정 필요없이 그대로 사용가능함을 보여준다."""
print(peter.age)
peter.age = 28 # peter.age(28) 아닌가? 해본 결과 안됨
# age 메소드가 진짜 메소드로 사용한다기 보다는 값을 수정하는 코드를 실행하면 내부적으로 age메소드가 실행이 되는듯

```

## 3. 상속  

> 두 클래스 사이의 부모-자식 관계

중복(공통)되는 코드(클래스 변수, 인스턴스 변수, 인스턴스 메소드)를 모아 부모 클래스로 만든다.

### 상속 관계 설정하기

```python
class Employee:
		. . .

class Cashier(Employee):
		. . .
```

### 상속과 관련된 메소드, 함수

- mro메소드: help()를 쓰면 메소드, 변수, 상속관계를 모두 확인할 수 있다. mro()메소드는 상속하는 부모 클래스를 보여준다.
- isinstance 함수: isinstance(확인하고 싶은 인스턴스 이름, 확인하고 싶은 클래스 이름)  
- issubclass 함수: issubclass(검사할 클래스, 기준 클래스)

(자식 클래스로 만들어진 인스턴스는 부모 클래스의 인스턴스이기도 하다.) 

### 오버라이딩(Overriding)

> 자식 클래스에서 +알파로 추가하고 싶을 때

```python
class Employee:
		raise_percentage = 1.03
		def __init__(self, name, wage):
				self.name = name
				self.wage = wage

class Cashier(Employee):
		raise_percentage = 1.05 # 변수 오버라이딩
		def __init__(self, name, wage, number_sold): #메소드 오버라이딩
				super().__init__(name, wage) #self를 쓰지 않는다.
				self.number_sold = number_sold
```

> 상속 받았지만 상속을 사용하지 않은 경우

```python
class Employee:
    raise_percentage = 1.03

    def __init__(self, name, wage):
        self.name = name
        self.wage = wage


class Cashier(Employee):
    raise_percentage = 1.05

    def __init__(self, nickname, number_sold):
        self.name = nickname
        self.number_sold = number_sold


cashier_1 = Cashier('Goat', 5)

assert isinstance(cashier_1, Cashier)
assert isinstance(cashier_1, Employee)
assert issubclass(Cashier, Employee)
```

> 상속을 받아서 오버라이딩 하기로 했으면 부모 클래스의 메소드는 완전히 가져와야 한다  

```python
super().__init__(name, wage)
에서 name은 가져오고 wage는 안가져오고 이런식으로 하면 안된다
```

```python
class Employee:
    raise_percentage = 1.03

    def __init__(self, name, wage):
        self.name = name
        self.wage = wage


class Cashier(Employee):
    raise_percentage = 1.05

    def __init__(self, name, wage, number_sold):
        super().__init__(name, wage)
        self.number_sold = number_sold


cashier_1 = Cashier('Goat', 10000, 5)
```

> 부모 클래스가 1개 이상인 경우  

```python
class Employee:
    raise_percentage = 1.03

    def __init__(self, name, wage):
        self.name = name
        self.wage = wage


class Cashier(Employee):
    raise_percentage = 1.05

    def __init__(self, name, wage, number_sold):
        super(Cashier, self).__init__(name, wage=wage)
        self.number_sold = number_sold


cashier_1 = Cashier('Goat', 10000, 5)

assert isinstance(cashier_1, Cashier)
assert isinstance(cashier_1, Employee)
assert issubclass(Cashier, Employee)
```

> 부모 클래스와 인자명을 달리주려면 서로 매핑시켜줘야 한다  

```python
class Employee:
    raise_percentage = 1.03

    def __init__(self, name, wage):
        self.name = name
        self.wage = wage


class Cashier(Employee):
    raise_percentage = 1.05

    def __init__(self, nickname, wage, number_sold):
        super(Cashier, self).__init__(name=nickname, wage=wage)
        self.number_sold = number_sold


cashier_1 = Cashier('Goat', 10000, 5)

assert isinstance(cashier_1, Cashier)
assert isinstance(cashier_1, Employee)
assert issubclass(Cashier, Employee)
```


## 4. 다형성 

> 클래스에서 변수가 상황에 따라 다른 클래스의 인스턴스를 갖는  경우

이 변수(인스턴스)가 가질 수 있는 속성, 메소드는 인스턴스가 만들어진 클래스에서 공통적으로 정의가 되어 있어야 한다.

다형성은 말로 설명하는 것 보다 코드를 통해 개념을 파악하는 것이 더 낫다.

```python
class Rectangular:
		def __init__(self, width, height):
				self.width = width
				self.height = height

		def area(self):
				return self.width*self.height

		def perimeter(self):
				return 2*(self.width + self.height)
```

```python
from math import pi

class Circle:
		def __init__(self, radius):
				self.radius = radius

		def area(self):
				return pi*self.radius*self.radius

		def perimeter(self):
				return 2*pi*self.radius
```

```python
class Paint:
		def __init__(self):
				self.shapes = []

		def add_shape(self, shape):
				self.shapes.append(shape)

		def total_area(self):
				return sum([shape.area() for shape in self.shapes])

		def total_perimeter(self):
				return sum([shape.perimeter() for shape in self.shapes])
```

```python
circle_1 = Circle(5)
rectangular_1 = Rectangular(3,4)

paint_1 = Paint()
paint_1.add_shape(circle_1)
paint_1.add_shape(rectangular_1)

paint_1.total_area()
paint_1.total_perimeter()
```

Paint클래스 인스턴스 paint_1에 circle_1과 rectangular_1을 넣고 total_area, total_perimeter을 사용할 때 잘 호출이 되는 이유는 circle_1과 rectangular_1이 모두 area()와 perimeter() 메소드를 가지고 있기 때문이다.

> 만약 추가된 인스턴스에 호출할 메소드가 없으면?  → 오류가 발생할 것이다.  
—> 상속을 통해 area(), perimeter() 메소드를 갖도록 하자  
상속을 이용한 다형성 문제 해결

area()와 perimeter() 메소드를 갖는 부모 클래스 Shape을 정의하고 상속하자.

```python
class Shape:
		def area(self):
				pass

		def perimeter(self):
				pass
```

```python
class Circle(Shape):
		def area(self):
				return pi*self.radius*self.radius

		def perimeter(self):
				return 2*pi*self.radius
```

### 만약 Circle의 area()와 perimeter()가 오버라이딩하지 않으면?

```python
class Circle(Shape):
		. . . 
```

부모 클래스의 Shape에서 area() perimeter()를 호출하는데 그냥 pass여서 sum 안된다.

→ 무조건 오버라이딩 하도록 해야한다.

→ 추상 클래스 사용

### 추상 클래스

```python
from abc import ABC, abstractmethod

class Shape(ABC):
		@abstractmethod
		def area(self):
				pass

		@abstractmethod
		def perimeter(self):
				pass
```

이렇게 Shape 클래스가 ABC클래스 (Abstract Base Class) 를 상속받도록 하고 강제로 오버라이딩 하고자 하는 메소드들을 abstractmethod로 데코레이팅 해준다.

그렇게 하면 Shape클래스를 상속받는 자식 클래스들(Circle, Rectangular)은 추상 메소드인 area와 perimeter 메소드를 반드시 오버라이딩 해야한다.

### 추상 클래스에서 유의할 점

1. 추상 클래스는 인스턴스를 생성할 수 없다.
2. 추상 클래스도 일반 메소드를 가질 수 있다.
3. 추상 메소드에도 내용을 채울 수 있다.
4. 자식 클래스가 특정 변수를 갖도록 유도할 수 있다.

```python
class Shape(ABC):
    """도형 클래스"""
    @abstractmethod
    def area(self) -> float:
        """도형의 넓이를 리턴한다: 자식 클래스가 오버라이딩할 것"""
        print("도형 넓이 계산 중!")

    @abstractmethod
    def perimeter(self) -> float:
        """도형의 둘레를 리턴한다: 자식 클래스가 오버라이딩할 것"""
        pass

    def __str__(self):
        return "추상 클래스라고 해서 모든 메소드가 추상 메소드일 필요는 없습니다!"

    @property
    @abstractmethod
    def x(self):
        """도형의 x 좌표 getter 메소드"""
        pass

    @property
    @abstractmethod
    def y(self):
        """도형의 y 좌표 getter 메소드"""
        pass
```

```python
class EquilateralTriangle(Shape):
     """정삼각형 클래스"""
    def __init__(self, x, y, side):
        self._x = x
        self._y = y
        self.side = side

    def area(self):
        """정삼각형의 넓이를 리턴한다"""
        return sqrt(3) * self.side * self.side / 4

    def perimeter(self):
        """정삼각형의 둘레를 리턴한다"""
        return 3 * self.side

    @property
    def x(self):
        """_x getter 메소드"""
        return self._x

    @x.setter
    def x(self, value):
        """_x setter 메소드"""
        self._x = value

    @property
    def y(self):
        """_y getter 메소드"""
        return self._y

    @y.setter
    def y(self, value):
        """_y setter 메소드"""
        self._y = value

equilateral_triangle = EquilateralTriangle(5, 6, 4) # 에러가 나지 않는다
equilateral_triangle.x = 10
print(equilateral_triangle.x) # 출력: 10

equilateral_triangle.y = 5
print(equilateral_triangle.y) # 출력: 5
```