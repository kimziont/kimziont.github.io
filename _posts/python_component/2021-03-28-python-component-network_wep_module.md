---
title:  "[Component] 네트워크, 웹 관련 모듈"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/python.png

categories:
  - component
tags:
  - Python
last_modified_at: 2021-03-28
---



## 2-7 인터넷 프로토콜과 지원 requests 패키지, urllib 모듈 (urllib.request)  

## requests 패키지  
requests는 파이썬으로 HTTP 호출하는 프로그램을 작성할 때 가장 많이 사용되는 라이브러리입니다.  

```python
import requests

req = requests.get("https://www.google.com")
html = req.text()
```  

어떤 방식(method)의 HTTP 요청을 하느냐에 따라서 해당하는 이름의 함수를 사용하면 됩니다. (Request method)  
![](/assets/images/request_method.png){: width="100%" height="70%"}  
  * GET 방식: requests.get()
  * POST 방식: requests.post()
  * PUT 방식: requests.put()
  * DELETE 방식: requests.delete()


## urllib 모듈  

#### urllib.request  

urllib.request 모듈을 이용하여 간단하게 웹 페이지 요청 및 데이터를 가져올 수 있습니다.  
urlopen 함수를 이용하면 웹 페이지를 불러올 수 있습니다. 리턴값으로 호출하여 얻은 데이터에 대한 객체를 반환합니다.  
실제로 결과를 출력하려면 반환된 객체의 read 함수를 실행하면 됩니다.  
아래 코드에서 decode를 하지 않으면 인코딩된 페이지의 결과가 보이기 때문에 읽기 어렵습니다.  

```python
import urllib
req = urllib.request.urlopen("https://www.google.com")
html = req.read().decode("utf-8")
```

#### urllib.parse  
urllib.parse 모듈을 이용하면 간단하게 URL과 파라미터를 다룰 수 있습니다.  

URL을 파싱 하기 위해서는 urlparse 함수를 이용하면 됩니다. 아래 코드의 실행 결과와 같이 입력한 URL 정보를 각각의 변수에 할당된 객체가 반환됩니다.  

```python
from urllib import parse
url = parse.urlparse("https://www.google.com/.")
print(url)
-----------------------------------------------
ParseResult(scheme='https', netloc='www.google.com', path='/.', params='', query='', fragment='')
```

## requests와 urllib.request의 차이점  

1. 데이터를 보낼때 requests는 딕셔너리 형태, urllib는 인코딩하여 바이너리 형태로 전송합니다.  

2. requests는 요청 메소드(get, post)를 명시하지만 urllib는 데이터의 여부에 따라 get과 post 요청을 구분합니다.  

3. 없는 페이지 요청시 requests는 에러를 띄우지 않지만 urllib는 에러를 띄웁니다.  


## pickle 

<https://dojang.io/mod/page/view.php?id=2327>  

<https://rfriend.tistory.com/525>  

<https://docs.python.org/ko/3/library/pickle.html>