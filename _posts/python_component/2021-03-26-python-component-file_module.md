---
title:  "[Component] 파일과 디렉토리 관련 모듈"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - component
tags:
  - Python
last_modified_at: 2021-03-26
---

## 외장 함수(Library) 
전세계 파이썬 사용자들이 만들어 놓은 유용한 프로그램들을 모아놓은 것을 파이썬 라이브러리라고 한다.  
이 라이브러리를 통해 사용하는 함수들을 외장 함수라고 한다.  
파이썬 라이브러리는 파이썬을 설치할 때 자동으로 같이 설치되기 때문에 따로 install할 필요는 없다. (All you need is __import__)  


파일은 단지 파일 이름으로 저장된 바이트 시퀀스입니다. 파일에서 데이터를 읽어 메모리에 적재하고, 메모리에서 파일로 데이터를 씁니다. 디렉터리(폴더)는 파일 또는 디렉터리의 모음입니다.  


## 파일 명령어  


#### 파일 생성하기/열기: open()  
파일을 열면 데이터를 읽거나 쓰기 위한 함수를 호출할 수 있습니다. 파일을 열 때는  
`1. 어떤 파일명의 파일을 열 것인지, 2. 어떤 용도로 파일을 열 것인지 모드(mode)를 명시해야 합니다.`  

```python
fileobj = open(filename, mode)
```  

|mode|설명|안전성|
|---|---|---|
|r|파일 읽기|
|x|파일 쓰기(파일이 존재하지 않는 경우)-> 파일 생성에 가깝다|융통성이 없지만 제일 안전하다|
|w|파일 쓰기(파일의 존재 유무에 상관 없는 경우)-> 이전 파일이 있든 말든 지금 쓰는 걸로 생성한다|제일 위험하다|
|a|파일 추가하기-> 없으면 새로 만들고, 있으면 뒤에 추가한다|제일 무난해 보인다|  

|mode|설명|
|---|---|
|t|텍스트 타입|
|b|이진 타입|  

🔔 파일을 열고 다 사용했다면, 사용한 메모리를 헤제하기 위해 파일을 닫아야 합니다.  

#### 파일 읽기: read(), readline(), readlines()  

```python
# read()
fout = open('relativity', 'rt')
contents = fout.read()
print(contents)
fout.close()
```  
파일을 끝까지 다 읽고 나면 read() 함수는 빈 문자열('')을 반환합니다. 그래서 다시 fout.read()를 하면 그 때는 빈 문자열 입니다.  

```python
# readline()
fout = open('relativity', 'rt')
while True:
  if line:= fout.readline():
    print(line)
  else:
    break
fout.close()

# 이터레이터를 사용한 더 짧은 코드
fout = open('relativity', 'rt')
for line in fout:
  print(line)
fout.close()
```

```python
# readlines() 호출은 한 번에 모든 줄을 읽고, 한 줄로 된 리스트를 반환한다
fout = open('relativity', 'rt')
lines = fout.readlines()
for line in lines:
  print(line)
fout.close()

```

#### 파일 쓰기: write(), print()  

파일을 쓰기 위한 목적으로 열었다면 이제 파일에 원하는 내용을 어떻게 쓰는지 알아봅시다.  

```python
poem = '''There was a young lady named Bright,
Whose speed was far faster than light.
She started one day'''

# write()를 사용한 파일 쓰기
fout = open('relativity', 'wt')
fout.write(poem)
fout.close()

# print()를 사용한 파일 쓰기
fout = open('relativity', 'wt')
print(poem, file=fout)
fout.close()
```

둘의 차이는 print()의 경우 한 번 실행되고 나면 끝에 줄바꿈이 추가된다는 것입니다. 그러니 다음에 실행될 문자열과 줄바꿈을 통해 구분이 될 것입니다.  

#### 자동으로 파일 닫기: with  
열려 있는 파일을 닫지 않았을 때, 이 파일이 더 이상 참조되지 않게 되면 자동으로 파일이 닫힙니다. 예를 들어, 함수 안에 파일을 열어놓고 이를 명시적으로 닫지 않더라도 함수가 끝날 때 자동으로 파일이 닫힌다는 것을 의미합니다. 그러나 오랫동안 작동하는 함수 혹은 메인 프로그램에 파일을 열어 놓았다면, 명시적으로 파일을 닫아야 합니다.  

파이썬은 파일을 여는 것과 같은 일을 수행하는 컨텍스트 매니저가 있습니다.   
```python
with open('relativity', 'wt') as fout:
  fout.write(poem)
```
블록 안의 코드가 실행되고 나서 자동으로 파일을 닫아줍니다.  


#### 파일 복사하기: copy()  
copy()함수는  shutil이라는 다른 모듈에 있습니다. 다음 예제는 oops.txt를 ohno.txt.로 복사합니다.  
```python
import shutil
shutil.copy('oops.txt', 'ohno.txt')

# 원본 파일은 삭제
shotil.move('oops.txt', 'ohno.txt')
```  

#### 파일 지우기: remove()  
```python
os.remove('oops.txt')
```

## 디렉터리 명령어  

#### 현재 작업 디렉토리 확인: getcwd()  
```python
>>> os.getcwd()
'C:\\Users\\User'
```

#### 디렉터리 생성하기: mkdir()

```python
os.mkdir('poems')
```

#### 디렉터리 삭제하기: rmdir()

```python
os.rmdir('poems')
```

#### 입력 경로 내의 모든 파일과 폴더명 리스트 반환: listdir()  

```python
os.listdir('poems')
```

#### 현재 디렉터리 위치 바꾸기: chdir()  
```python
os.chdir('poems_2')
```

#### 모든 하위 폴더 생성: makedirs()  
```python
os.makdeirs('train/train_2/train_3')
```

#### 경로, 폴더명, 파일명 모두 반환: walk()  
```python
for path, dirs, files in os.walk(dir_name):
  print(path, dirs, files) 
```

## 파일과 디렉터리 엑세스 glob  
원하는 형태의 파일들을 리스트로 볼 수 있도록 해준다.  
조건에 정규식을 사용할 수 없으며 '*'와 '?'같은 와일드카드만을 지원한다.  
   

#### 현재 디렉토리에서 파일들을 찾는 방법  

```python
from glob import glob

>>> glob('*.exe') # 현재 디렉토리에서 exe 형식의 파일을 리스트로 보여준다.
['python.exe', 'pythonw.exe']

>>> glob('2021*.md') # 현재 디렉토리에서 2021로 시작하고 md 파일 형식을 갖는 파일들의 리스트를 보여준다.
['2021-03-13-python-data-type-number.md',
 '2021-03-14-blog-making.md',
 '2021-03-14-blog-thanks-to.md',
 '2021-03-14-python-data-type-string.md',
 '2021-03-15-python-data-type-list.md',
 '2021-03-16-python-component-package.md',
 '2021-03-16-python-data-type-dictionary.md',
 '2021-03-17-python-component-function.md',
 '2021-03-17-python-keyword.md']
```  

#### 다른 디렉토리에서 파일들을 찾는 방법  

```python
>>> glob('C:/K*')  # C드라이브에서 K로 시작하는 파일을 보여준다.
['C:/kimziont.github.io', 'C:/Kotlin']
```

## os.path 모듈  

#### 존재 여부 확인하기: os.path.exists()  
```python
>>> import os
>>> os.path.exists('oops.txt')
True
```

#### 유형 확인하기: os.path.isfile(), os.path.isdir()  
```python
>>> os.path.isfile('oops.txt')
True

>>> os.path.isdir('oops.txt')
False
```

#### 절대 경로 얻기: os.path.abspath()
```python
>>> os.path.abspath('oops.txt')
'/usr/gaberlunzie/oops.txt'
```

#### 파일명과 경로를 합치기: os.path.join() 
운영체제에 적합한 경로 구분 문자로 경로를 결합시켜 줍니다. macOS, 리눅스에서의 경로 구분문자는 /이고, 윈도우는 \입니다.  
```python
>>> win_file = os.path.join('eek', 'urk')
>>> win_file = os.path.join(win_file, 'snort.txt')

# macOS, 리눅스
'eek/urk/snort.txt'

# 윈도우
'eek\\urk\\snort/txt'
```  

#### 파일의 크기(size) 반환: os.path.getsize()  

```python
print("파일 크기: ")
for file in os.listdir(DATA_IN_PATH):
    if 'tsv' in file and 'zip' not in file:
        print(file.ljust(30)+str(round(os.path.getsize(DATA_IN_PATH+file)/1000000, 2))+'MB')
-----------------------------------------------------------------------------------------------
파일 크기: 
testData.tsv                  32.72MB
labeledTrainData.tsv          33.56MB
unlabeledTrainData.tsv        67.28MB
```

#### 경로와 파일 분리: os.path.split() os.path.splitext()

## 실행시간 서비스 sys
This module provides access to some variables used or maintained by the interpreter  
and to functions that interact strongly with the interpreter.    


#### Import search order  

![](/assets/images/import.png){: width="60%"}  

#### sys.modules  
import된 모듈과 package들을 저장한다.  
이미 로드된 모듈을 모듈 이름으로 확인(map)하고, 모듈들을 다시 부르도록 할 수 있다.  

#### built-in functions  
파이썬을 설치할 때 자동으로 설치되는 패키지 라이브러리이다.  이 라이브러리 안에 내가 import한 모듈이 있는지 확인한다.

#### sys.path  
현재 스크립트 파일이 있는 경로, PYTHONPATH 환경 변수에 저장되어 있는 경로, 패키지가 설치되면 자동으로 생성되는 설치의존적인 초기값 경로순으로 확인하며 
경로에 모듈이나 패키지가 있는지 확인한다.  

내 생각에는 어떤 모듈을 __import__ 하면,  
먼저 __built-in functions__ 인지를 확인하고 맞으면 built-in functions이 저장되어 있는 경로에서 모듈을 불러온다.  
built-in functions이 아니면 그 다음으로 __sys.path__ 에 저장되어 있는 경로를 차례대로 돌며 import한 모듈이 있는지 확인하고 있으면 모듈을 불러온다.  
불러온 모듈은 __sys.modules__ 에 저장되어 호출할 때마다 sys.modules에 저장되어 있는 모듈을 불러온다.  

## 데이터 압축 및 보관 zlib gzip zipfile tarfile  
   

#### 압축파일 만들기  
```python
>>> my_zip = zipfile.ZipFile("test_zip.zip", 'w')
>>> my_zip.write('test.txt')
>>> my_zip.close()
```

#### 압축 해제하기  
```python
>>> zipfile.ZipFile('test_zip.zip').extract('test.txt') # test_zip.zip 파일에서 text.txt 파일만 압축 풀기

>>> zipfile.ZipFile('test_zip.zip').extractall() # test_zip.zip 파일 내 모든 파일 압축 풀기
```

#### 압축 파일내 파일명 읽기  
```python
>>> my_zip.namelist()
['test1.txt', 'test2.txt', 'test3.txt', 'testtest.csv']
```

#### 압축 파일정보 확인  
```python
>>> zp_info = my_zip.getinfo('testtest.csv')   # csv파일의 Zipinfo객체 생성
>>> print(zp_info.filename)                    # 파일명
>>> print(zp_info.file_size)                   # 파일용량
>>> print(zp_info.date_time)                   # 작성일자
>>> print(zp_info.compress_size)               
>>> print(zp_info.comment)                     # 주석문

```

## 그 외 내가 공부하며 마주친 것들  

* sys.getsizeof()  
: 객체가 점유하고 있는 메모리의 크기를 반환합니다.  

    ```python
    >>> a = [n for n in range(1000000)]
    >>> b = range(1000000)

    >>> sys.getsizeof(a)
    8697464

    sys.getsizeof(b)
    48
    ```