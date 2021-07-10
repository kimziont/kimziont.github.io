---
title:  "[Intermediate] 정규 표현식 re"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - intermediate
tags:
  - Python
last_modified_at: 2021-03-26
---  



## 정규표현식 re 
정규 표현식(Regular Expressions)은 복잡한 문자열을 처리할 때 사용하는 기법이다.  
만약 찾으려는 문자열 또는 바꾸어야 할 문자열의 규칙이 매우 복잡하다면 정규식의 효용은 더 커지게 된다.  
파이썬은 정규 표현식을 지원하기 위해 re(regular expression의 약어) 모듈을 제공한다.  
   

사용 방법
```python
>>> import re
>>> p = re.compile('ab*') # a로 시작하며 b가 0번 이상 반복되는 문자열
>>> p = re.compile('[a-z]+') # 소문자 알파벳이 1번이상 반복되는 문자열
```  

이제 컴파일된 패턴 객체를 사용하여 문자열 검색을 수행해 보자. 컴파일된 패턴 객체는 다음과 같은 4가지 메서드를 제공한다.  

|Method|목적|
|:---:|:---|
|match()|문자열의 처음부터 정규식과 매치되는지 조사해 정규식과 매치될 때는 match 객체를 돌려주고, 매치되지 않을 때는 None을 돌려준다. |
|search()|문자열 전체를 검색하여 정규식과 매치되는지 조사해 정규식과 매치될 때는 match 객체를 돌려주고, 매치되지 않을 때는 None을 돌려준다.|
|findall()|정규식과 매치되는 모든 문자열(substring)을 리스트로 돌려준다.|
|finditer()|정규식과 매치되는 모든 문자열(substring)을 반복 가능한 match 객체를 돌려준다.|  

하나씩 살펴보겠습니다.  

### match  


```python
>>> import re
>>> p = re.compile('[a-z]+')
```
```python
>>> m = p.match("python")
>>> print(m)
<_sre.SRE_Match object at 0x01F3F9F8>
```
"python" 문자열은 [a-z]+ 정규식에 부합되므로 match 객체를 돌려준다.  
```python
>>> m = p.match("3 python")
>>> print(m)
None
```  
"3 python" 문자열은 처음에 나오는 문자 3이 정규식 [a-z]+에 부합되지 않으므로 None을 돌려준다.  

### search  
컴파일된 패턴 객체 p를 가지고 이번에는 search 메서드를 수행해 보자.  

```python
>>> m = p.search("python")
>>> print(m)
<_sre.SRE_Match object at 0x01F3FA68>
```
"python" 문자열에 search 메서드를 수행하면 match 메서드를 수행했을 때와 동일하게 매치된다.  

```python
>>> m = p.search("3 python")
>>> print(m)
<_sre.SRE_Match object at 0x01F3FA30>
```
"3 python" 문자열의 첫 번째 문자는 "3"이지만 search는 문자열의 처음부터 검색하는 것이 아니라  
문자열 전체를 검색하기 때문에 "3 " 이후의 "python" 문자열과 매치된다.  
이렇듯 match 메서드와 search 메서드는 문자열의 처음부터 검색할지의 여부에 따라 다르게 사용해야 한다.  

### match 객체의 메서드  

자, 이제 match 메서드와 search 메서드를 수행한 결과로 돌려준 match 객체에 대해 알아보자.  

|Method|목적|
|:---:|:---|
|group()|매치된 문자열을 돌려준다.|
|start()|매치된 문자열의 시작 위치를 돌려준다.|
|end()|매치된 문자열의 끝 위치를 돌려준다.|
|span()|매치된 문자열의 (시작, 끝)에 해당하는 튜플을 돌려준다.|  

```python
>>> m = p.match("python")
>>> m.group()
'python'
>>> m.start()
0
>>> m.end()
6
>>> m.span()
(0, 6)
```
```python
>>> m = p.search("3 python")
>>> m.group()
'python'
>>> m.start()
2
>>> m.end()
8
>>> m.span()
(2, 8)
```

🔔 축약 형태  
```python
>>> m = re.match('[a-z]+', "python")
```

### findall  
```python
>>> result = p.findall("life is too short")
>>> print(result)
['life', 'is', 'too', 'short']
```

### finditer
```python
>>> result = p.finditer("life is too short")
>>> print(result)
<callable_iterator object at 0x01F5E390>
>>> for r in result: print(r)
...
<_sre.SRE_Match object at 0x01F3F9F8>
<_sre.SRE_Match object at 0x01F3FAD8>
<_sre.SRE_Match object at 0x01F3FAA0>
<_sre.SRE_Match object at 0x01F3F9F8>
```
finditer는 findall과 동일하지만 그 결과로 반복 가능한 객체(iterator object)를 돌려준다. 반복 가능한 객체가 포함하는 각각의 요소는 match 객체이다.

### 메타 문자  
정규 표현식에서 사용하는 메타 문자(meta characters)에는 다음과 같은 것이 있다.  

※ 메타 문자란 원래 그 문자가 가진 뜻이 아닌 특별한 용도로 사용하는 문자를 말한다.  

[ ] . * + { }  ? | ^ $ \ ( ) 
{: .notice--primary}
### []
[ ] 사이의 문자들과 매치  
문자 클래스를 만드는 메타 문자인 [ ] 사이에는 어떤 문자도 들어갈 수 있다.  
즉 정규 표현식이 [abc]라면 이 표현식의 의미는 "a, b, c 중 한 개의 문자와 매치  
[ ] 안의 두 문자 사이에 하이픈(-)을 사용하면 두 문자 사이의 범위(From - To)를 의미한다.  
예를 들어 [a-c]라는 정규 표현식은 [abc]와 동일하고 [0-5]는 [012345]와 동일하다.
* [a-zA-Z] : 알파벳 모두  
* [0-9] : 숫자  

### .  
정규 표현식의 Dot(.) 메타 문자는 줄바꿈 문자인 \n을 제외한 모든 문자와 매치됨을 의미한다.  

a.b ==> aab, a0b, a#b, a&b



a[.]b ==> a.b



### *
*문자는 바로 앞에 있는 문자가 0번 이상 반복될 수 있다는 의미이다.  
ca*t  ==> ct, cat, caat, caaaaaat


### +
+문자는 바로 앞에 있는 문자가 1번 이상 반복될 수 있다는 의미이다.  
ca+t  ==> cat, caat, caaaaaat  

### {}
{m, n}문자는 앞에 있는 문자가 m번 이상 반복 n번 이하 반복할 수 있다는 의미이다.
ca{2}t ==> caat  
ca{,3}t ==> ct, cat, caat, caaat  
ca{2,5}t ==> caat, caaat, caaaat, caaaaat  

### ?
? 메타문자가 의미하는 것은 {0, 1} 이다.  
ab?c ==> ac, abc

### |
| 메타 문자는 or과 동일한 의미로 사용된다. A|B라는 정규식이 있다면 A 또는 B라는 의미가 된다.  
```python
>>> p = re.compile('Crow|Servo')
>>> m = p.match('CrowHello')
>>> print(m)
<re.Match object; span=(0, 4), match='Crow'>
```

### ^  
^ 메타 문자는 문자열의 맨 처음과 일치함을 의미한다.  
```python
>>> print(re.search('^Life', 'Life is too short'))
<re.Match object; span=(0, 4), match='Life'>
>>> print(re.search('^Life', 'My Life'))
None

```

### $  
$ 메타 문자는 ^ 메타 문자와 반대의 경우이다. 즉 $는 문자열의 끝과 매치함을 의미한다.  
```python
>>> print(re.search('short$', 'Life is too short'))
<re.Match object; span=(12, 17), match='short'>
>>> print(re.search('short$', 'Life is too short, you need python'))
None
```
※ ^ 또는 $ 문자를 메타 문자가 아닌 문자 그 자체로 매치하고 싶은 경우에는 \ ^, \ $ 로 사용하면 된다.

### \
\d ==> 숫자와 매치, [0-9]와 동일한 의미  
\w ==> 문자+숫자(alphanumeric)와 매치, [a-zA-Z0-9_]와 동일한 의미  
\s ==> whitespace 문자와 매치, [ \t\n\r\f\v]와 동일한 표현식이다. 맨 앞의 빈 칸은 공백문자(space)를 의미  
\b ==> \b는 단어 구분자(Word boundary)이다. 보통 단어는 whitespace에 의해 구분된다.  

```python
>>> p = re.compile(r'\bclass\b')
>>> print(p.search('no class at all'))  
<re.Match object; span=(3, 8), match='class'>

>>> print(p.search('the declassified algorithm'))
None
```

※ \D, \W, \S \B (대문자)는 소문자와 반대되는 의미를 갖는다.  
※ \b 메타 문자를 사용할 때 주의해야 할 점이 있다. \b는 파이썬 리터럴 규칙에 의하면 백스페이스(BackSpace)를 의미하므로 백스페이스가 아닌 단어 구분자임을 알려 주기 위해 r'\bclass\b'처럼 Raw string임을 알려주는 기호 r을 붙여 주어야 한다.  
※ Raw string  
예를 들어 어떤 파일 안에 있는 "\section" 문자열을 찾기 위한 정규식을 만든다고 가정해 보자.  
```python
>>> p = re.compile('\section')
```  
이 정규식은 \s 문자가 whitespace로 해석되어 의도한 대로 매치가 이루어지지 않는다.  ==> '[ \t\n\r\f\v]ection'  
즉 위 정규식에서 사용한 \ 문자가 문자열 자체임을 알려 주기 위해 백슬래시 2개를 사용하여 이스케이프 처리를 해야 한다.  
```python
>>> p = re.compile('\\section')
```
그런데 여기에서 또 하나의 문제가 발견된다. 위처럼 정규식을 만들어서 컴파일하면 실제 파이썬 정규식 엔진에는 파이썬 문자열 리터럴 규칙에 따라 \\이 \로 변경되어 \section이 전달된다.  
이러한 문제로 인해 파이썬 정규식에는 Raw String 규칙이 생겨나게 되었다.  
정규식 앞에 r을 써줌으로써 컴파일해야 하는 정규식이 Raw String임을 알려준다.  
```python
>>> p = re.compile(r'\\section')
```

### ()  
ABC 문자열이 계속해서 반복되는지 조사하는 정규식을 작성하고 싶다고 하자.  
이럴 때 필요한 것이 바로 그루핑(Grouping) 이다.  
```python
>>> p = re.compile('(ABC)+')
>>> m = p.search('ABCABCABC OK?')
>>> print(m)
<re.Match object; span=(0, 9), match='ABCABCABC'>
>>> print(m.group())
ABCABCABC
```  

또한 그룹을 사용하는 더 큰 이유는 매치된 문자열 중에서 특정 부분의 문자열만 뽑아내기 위해서인 경우가 더 많다.  
```python
>>> p = re.compile(r"(\w+)\s+\d+[-]\d+[-]\d+")
>>> m = p.search("park 010-1234-1234")
>>> print(m.group(1))
park
```

|group(인덱스)|의미|
|---|---|
|group(0)|매치된 전체 문자열|
|group(1)|첫 번째 그룹에 해당되는 문자열|
|group(2)|두 번째 그룹에 해당되는 문자열|
|group(n)|n 번째 그룹에 해당된든 문자열|
