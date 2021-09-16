---
title:  "[Intermediate] Makefile"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - intermediate
tags:
  - Python
last_modified_at: 2021-09-16
---
# Makefile  

## 1. Makefile tutorial

소프트웨어를 빌드하는 것은 여러가지 단계를 거쳐야 한다 (installing or updating dependencies, compiling the source code, testing and so on) 어떤 규모의 프로젝트이든 이러한 단계를 매번 수동으로 수행하는 것은 어렵다. 이런 상황에서 우리에게 도움을 주는 것이 바로 `make`이다.  

`make`는 소프트웨어를 source code로 부터 자동적으로 complie해주는 tool이다. 이러한 `make`를 실행하게 되면, 컴퓨터는 Makefile 또는 makefile이라는 파일을 같은 디렉토리에서 찾는다. 원한다면 이름을 바꿀 수도 있지만 그렇게 되면 `make`에게 변경된 이름을 알려주어야 한다.  

```
make -f some_other_makefile
```

Makefile은 몇 가지 규칙을 따르는 format이 있습니다.

|name|description|rule|
|:---:|---|---|
|target|빌드 대상 이름 또는 명령에 의해 생성되는 결과 파일|1개 이상의 target|
|prerequisite|대상을 만들 때 의존되는 파일들|0개 이상의 prerequisite|
|recipe|target을 생성하는 쉘 명령어|0개 이상의 recipe|


```
# []는 선택적임을 의미합니다
target1 [target2 ..]: [pre-req1 pre-req2 ..]
  [recipe1
   recipe2 
   ..
  ]
```

만약 prerequisites이 모두 target file보다 전에 생성되었으면 `make`는 recipe는 실행되지 않는다. 만약 하나라도 target file보다 새로운 파일이 prerequisites에 있다면 recipe는 실행된다. 또한 타겟 파일이 프로젝트 폴더 안에 없는 경우 recipe가 실행된다.  

예시를 살펴보자.  

```
all: count.txt

count.txt: data.txt
    wc -c data.txt > count.txt # Count characters
    wc -w data.txt >> count.txt # Count words
    wc -l data.txt >> count.txt # Count lines
```  

이 Makefile은 target이 2개 있다. 첫 번째 target은 `all`이다. 이 target응ㄴ 마치 전체 build의 target처럼 행동한다. 사실 지금 예시에서는 필요한 target은 아니지만 연습을 적어본다.  

이 `all` target은 `count.txt`에 의존하며 recipe는 없다. 이 말은 `all`은 `count.txt`가 준비되는 즉시 마찬가지로 준비된다는 것이다.  

`count.txt` target은 `data.txt`파일에 의존한다. 그리고 문자와 단어의 개수를 count하는 recipe를 가지고 있다. 이제 `make`를 실행하게 되면 recipes의 명령어들을 실행하고 `count.txt`파일을 생성한다.  

```
한마디로 data.txt 파일에 변경사항이 있을 때, make를 실행할 경우 명령어들을 실행한 후 count.txt 파일을 생성한다
```

만약 `make`를 한 번 더 실행하게 되면 다음과 같은 출력문이 나오게 된다.  

```
make: Nothing to be done for 'all'.
```

## 2. Creating a Python Makefile

### 1) Interpreted language Python
대개 모든 프로그래밍 언어는 컴파일 언어와 인터프리터 언어로 분류된다. 컴파일 언어는 프로그램을 실행하기 위해 소스코드를 기계어로 변환하는 과정(컴파일)을 거치게 되고, 인터프리터 언어는 소스코드를 한 줄씩 읽고 바로 실행한다.  

파이썬이라는 프로그래밍 언어는 대표적인 인터프리터 언어이다. 하지만 파이썬 파일을 실행할 때 파이썬의 인터프리터는 소스코드를 내부적으로 바이트 코드(bytecode)로 컴파일 하고 VM(Virtual Machine)에서 이를 해석(interpret)한다. 파이썬이 인터프리터 언어로 여겨지는 이유는 이러한 컴파일 과정이 내부적으로 실행되어 컴파일러를 수동적으로 사용할 필요가 없기 때문이다. 우리가 파이썬에서 module을 import할 때, 파이썬은 caching을 목적으로 그러한 module을 바이트 코드로 컴파일한다. 그리고 이 바이트 코드는 `__pycache__`라는 디렉토리에 `.pyc`형식의 컴파일된 파일로 저장된다. 우리는 비록 `make`를 이용해서 module을 컴파일 할 수는 없지만, 이미 컴파일 되어 `.pyc`형태로 저장된 파일들을 `make`를 이용해서 running test, installing dependencies과 같은 명령을 수행할 수는 있다.  

### 2) Using Make with Python

```
run:
    python app.py

setup: requirements.txt
    pip install -r requirements.txt

clean:
    rm -rf __pycache__
```