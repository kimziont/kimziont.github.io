---
title:  "[Django] 장고 Intro"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/django.png

categories:
  - django
tags:
  - django
last_modified_at: 2021-06-02
---  

![](/assets/images/django_intro_2.png){: width="70%"}  


## 1. Django 프로젝트

### 프로젝트 생성하기  

```
# CLI
django-admin startproject {프로젝트 이름}
django-admin startproject {costaurant}
```  

### 서버 실행  

```
# CLI
python manage.py runserver
```  
runserver로 실행한 서버는 '개발 서버'(Development Server)라고 하여 보안과 관련된 사항이나 성능을 보장하지 않는 확인용 서버입니다. 그래서 실제로 배포 시에는 이러한 방법이 아닌 웹 서버를 사용해서 배포해야 합니다.  

우리가 로컬에서 개발할 때 Terminal 또는 WSL에서  python manage.py runserver라는 명령어를 통해 개발 서버를 실행해서 사용하지만 사실은 runserver 뒤에 IP와 Port 라는 인자가 숨어 있습니다.  

우리가 따로 적어 주지 않으면 기본적으로 127.0.0.1이라는 IP주소와 8000번 포트를 사용해서 서버가 실행되며, 여기서 127.0.0.1은 지금 내 컴퓨터를 가리키는 주소라고 생각하면 됩니다. 그러니까 내 컴퓨터를 서버로 사용해서 Django 프로젝트를 실행한다는 뜻이죠. 그리고 당연히 다른 IP 주소와 포트를 지정할 수도 있습니다.  

IP와 Port에 대해 간단히 알아보면 아이피 주소(IP Address)는 Internet Protocol의 앞 글자를 딴 것으로 네트워크 상에서 다른 컴퓨터와 내 컴퓨터를 구별하는 일종의 주소입니다. 그리고 포트(Port)는 IP로 찾은 컴퓨터 내부에서 실행되고 있는 프로그램이나 서비스를 구분하는 값인데요, 그러니까 IP로 컴퓨터를 찾고 그 안에서 포트를 가지고 프로그램을 찾는 거죠.  

우리가 매번 실행하는 개발 서버를 예로 들면 python manage.py runserver로 실행한 개발 서버는 127.0.0.1의 IP 주소, 즉 내 컴퓨터에서 8000번 포트로 Django 서비스를 실행한 뒤 유저의 접속을 기다립니다. 그러면 우리는 웹 브라우저를 켜서 해당 주소값과 포트를 이용해 우리 개발 서버의 Django 프로젝트로 들어가게 되는 거죠.  

### Django 프로젝트의 구조  

![](/assets/images/django_intro_4.png){: width="100%"}  


#### Project Root(Costaurant)  
Django 프로젝트의 모든 파일이 담겨 있는 최상위 디렉토리  

- __manage.py__  
Django 프로젝트 관리를 위한 명령어를 지원  
앱 생성, 데이터베이스 관련 명령, 개발 서버 실행 등  

- __db.sqlite3__
데이터베이스 파일  

#### Project App(Costaurant)
우리 프로젝트의 가장 중심이 되는 앱  

- __ __init__ __.py  
각각의 앱이 하나의 파이썬 패키지로 인식되기 위해 존재하는 파일  
파이썬 3.3 버전 이상 부터는 없어도 되지만 하위버전과의 호환을 위해 존재  

- __settings.py__  
Django 프로젝트의 전반적인 설정을 담당
시간대, 데이터베이스, 경로 설정 등  

- __urls.py__  
URL을 보고 알맞은 페이지로 연결해 주는 역할

- __wsgi.py__  
WebServer Gateway Interface  
웹 서버와 Django가 소통하는데 필요한 일종의 프로토콜



## 2. Django 앱  

![](/assets/images/django_intro_5.png){: width="70%"}  

### 앱 생성하기  

```
# CLI  
python manage.py startapp {앱 이름}
python manage.py startapp foods
```

### 앱 기본 구조  

![](/assets/images/django_intro_6.png){: width="100%"}  

- __migrations__  
데이터베이스의 변경 사항 히스토리를 누적

- __ __init__ __.py  
각각의 앱이 하나의 파이썬 패키지로 인식되기 위해 존재하는 파일  

- __admin.py__  
앱을 Django 관리자와 연동하기 위해 필요한 설정 파일  

- __apps.py__  
앱에 대한 설정을 넣어두는 파일  

- __models.py__  
django 앱에서 사용할 데이터 모델을 정의하고, 데이터베이스와 연동하는 역할을 하는 파일  

- __tests.py__  
프로젝트의 테스트 코드를 작성하는 곳  

- __views.py__  
django 앱의 메인 로직 처리와 관련된 파일  

### Django에게 앱 만들었다는 사실 알리기  

```python
# settings.py  

INSTALLED_APPS = ['foods']
```

### Django앱의 철학: 'Reusable App'  

내가 전에 만든 앱, 또는 다른 개발자가 만들어 놓은 좋은 앱을 잘 활용하면 또 다른 프로젝트를 빠른 시간에 완성할 수 있습니다. 이렇게 여러 프로젝트에서 사용할 수 있도록 만들어진 앱을 Reusable 앱이라고 합니다.  

#### DjangoCon에서 제시한 Reusable App  
- 한 가지 앱은 한 가지 기능을 하고, 그 기능을 잘 수행해야 한다.  
- 장고 개발자는 프로젝트를 많은 앱으로 구성하는 것을 두려워하면 안된다.  
- 각각의 앱을 유연하게 작성해야 한다.  
- 다른 사람에게 배포가 가능하도록 만들어야 한다.




## 3. Django와 URL  

### 클라이언트와 서버  
![](/assets/images/django_intro_7.png){: width="70%"}  

- URL= Domain + Path  
    - Domain: 도메인에는 DNS가 적혀있으며 DNS를 보고 서버를 찾아줍니다 
    - Path: 서버 안에 요청한 내용이 있는 위치를 나타냅니다.  

- 클라이언트(유저)가 웹 브라우저에 URL을 통해 우리의 서버(Django)에 요청을 보내면 Django는 가장 먼저 urls.py를 보고 다음 행선지를 정합니다.  

```python
# urls.py

urlpatterns = [
    path('admin/', admin.site.urls)
]
```
URL에 `admin/`이 나오면 `admin.site.urls`를 보고 처리하세요

### 새로운 URL 처리  

```python
# urls.py

from django.urls import include

urlpatterns = [
    path('foods/', include('foods.urls'))
]
```
URL에 `foods/`가 나오면 `foods.urls`을 살펴보세요 `include()`는 보통 path가 `foods/index/...`이런 식으로 되어있기 때문에 `foods/` 뒷부분을 처리하기 위해 필요한 함수입니다.  

```python
# foods/urls.py 파일 생성

from django.urls import path
from . import views

urlpatterns = [
    path('index/', views.index)
]
```
URL에 `foods/index`가 나오면 views 모듈에 index 함수로 가세요

```python
# views.py  

from django.http import HttpResponse

def index(request):
    return HttpResponse("<h2>Hello, Django !</h2>")
```

### URL Routing  
URL: 우리Django의 DNS/foods/index인 경우
```python
# settings.py

# Django가 URL을 보고 가장 먼저 어떤 파일을 봐야할지 설정하는 부분
ROOT_URLCONF = 'costaurant.urls'


# urls.py

urlpatterns = [
    path('foods/', include('foods.urls'))
]

# foods/urls.py

# 끝에 도달했기 때문에 include함수는 필요없다
urlpatterns = [
    path('index/', views.index)
]
```

### URL 작성시 유의할 점

- urls.py에 적는 URL 패턴은 슬래시(/)를 붙여서 작성  

- 앞에 슬래시(/)가 있으면 도메인으로 부터의 경로를 의미한다  

![](/assets/images/django_intro_8.png){: width="100%"}  




## 4. Django 아키텍쳐: MVT

### 템플릿(Template)

```python
# views.py  

from django.http import HttpResponse

def index(request):
    return HttpResponse("<h2>Hello, Django !</h2>")
```
이런 식으로 views.py가 요청받은 것을 바로 처리하려면 views.py의 파일 하나가 너무 많은 HTML 파일을 담당해야 하기 때문에 이러한 화면 구성을 다른 곳에서 담당하는게 좋습니다.  
이렇게 `화면 구성을 담당하는 역할을 하는 곳을 템플릿(Template)`이라고 합니다.  

#### 특정 앱의 템플릿 만들기  

템플릿을 위한 폴더를 만드는 구조가 조금 독특합니다(샌드위치 구조)
```
# foods/templates/foods/index.html

<h2>Hello, Django!</h2>
```

#### 템플릿 렌더링하기  

`render()`함수는 HttpResponse 객체를 리턴함으로써 템플릿을 유저에게 보여줍니다.  
```python
# views.py

def index(request):
    return render(request, 'foods/index.html')
```  

🔔 render() 함수  
- render 함수는 인자로 주어진 템플릿을 사전형(dict) 인자인 context와 결합해서 렌더링을 거친 다음 HttpResponse 객체로 반환하는 함수입니다. 쉽게 말하면 인자로 넘겨주는 템플릿과 context 데이터를 합쳐서 HttpResponse 객체로 돌려주는 함수인 것입니다.  

### MVT 아키텍쳐  

![](/assets/images/django_intro_12.png){: width="100%"}  

#### Model  

![](/assets/images/django_intro_9.png){: width="70%"}  

#### Template

![](/assets/images/django_intro_10.png){: width="70%"}  

#### View

![](/assets/images/django_intro_11.png){: width="70%"}  