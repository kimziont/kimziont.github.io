---
title:  "[Django] 장고 템플릿(Template)과 뷰(View)"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/django.png

categories:
  - django
tags:
  - Server
last_modified_at: 2021-06-03
---  



### 정적 파일(static files)  

- 렌더링(Rendering)하는 과정에서 필요한 추가적인 파일로 템플릿에서 css, 사진, 폰트와 같은 것들은 정적 파일(static files)에 따로 모아 관리해야 합니다.  

    ![](/assets/images/d_temp_view_1.png){: width="70%"}  

- 정적파일을 위한 디렉토리 구조는 템플릿과 같은 샌드위치 구조(foods/static/foods) 입니다.  

    ※ 샌드위치 구조를 사용해야 하는 이유  

        ex). A라는 앱의 templates 폴더의 index.html
            B라는 앱의 templates 폴더의 index.html

        settings.py 파일에 TEMPLATES = ['APP_DIRS': True]는 각각의 앱에 templates 폴더가 있으니 template(index.html)을 찾을 때는 templates 폴더에서 찾으세요 라는 의미  

        그리고 INSTALLED_APPS = ['A', 'B'] 이런식으로 되어있으면 템플릿(index.html)을 찾을때 항상 A앱의 templates 폴더를 먼저 탐색하게 되어 B앱의 templates 폴더의 template에 접근이 불가능합니다.  

        이런 문제를 해결하기 위해 각각의 templates폴더에 각각의 앱 이름을 한 번 더 사용해 폴더를 만들어줍니다.  


- 정적 파일을 템플릿에 사용하고자 할 때는 다음과 같이 적어줍니다.  
    ```
    # index.html
    {% raw %}{% load static %}{% endraw %}
    
    ```  
    `{% raw %}{% load static %}{% endraw %}` 이런 것을 템플릿 언어(Template language) 중에서 템플릿 태그라고 합니다.  

- 그리고 이미지, 폰트등의 소스를 템플릿 파일에 다음과 같이 입력하면 됩니다.  
    ```
    # index.html

    <link rel="stylesheet" href={% raw %}{% static 'foods/css/styles_detail.css' %}{% endraw %}>
    ```

### 템플릿 언어(Template Language)  

- HTML에 동적인 능력을 부여해줍니다.  
- 템플릿 언어에는 크게 `템플릿 변수`, `템플릿 태그`, `템플릿 필터`, `템플릿 주석`이 있습니다.  
    - 템플릿 변수: 템플릿에 `{% raw %}{{ 변수명 }}{% endraw %}`라고 적으면 view에서 넘겨 받은 값이 여기로 쏙 들어가게 됩니다. 또한 {% raw %}{{ 변수명.속성 }}{% endraw %} 으로 변수에 속성이 있으면 속성에도 다음과 같이 접근할 수 있습니다.  

    - 템플릿 필터: `{% raw %}{{ 변수명|필터 }}{% endraw %}`라고 적으면 템플릿 변수를 특정 형식으로 변환할 수 있습니다. 예를 들면 `{% raw %}{{ bread|upper }}{% endraw %}` 라고 적으면 문자열 전체를 대문자로 변수를 변환시켜 줍니다.

    - 템플릿 태그: `{% raw %}{% 태그 %}{% end태그 %}{% endraw %}`라고 적으면 템플릿에 로직을 적용할 수 있습니다.  
        ![](/assets/images/d_temp_view_2.png){: width="70%"}  

### 템플릿 변수 사용해서 동적인 느낌 가져보기  
- view에서는 자신이 가지고 있는 데이터를 클라이언트에게 보내기 전에 템플릿에 한 번 보내서 렌더링 한 후에 클라이언트에게 응답(Response)합니다.  

#### 데이터를 view에서 직접 만든 경우  

```python
# view.py

def index(request):
    today = datetime.today().date()
    context = {% raw %}{"date":today}{% endraw %}
    return render(request, 'foods/index.html', context=context)
```  

만약 유저가 `domain/foods/index` 라고 요청을 보냈을 때 오늘 날짜를 돌려주는게 이 앱의 목적이라고 하면 view에서는 일단 오늘의 날짜를 가질 것이고 이 값을 템플릿에 보내주면 됩니다. 이 때 `render함수`의 세 번째 인자로 `context`에 그 변수를 전달해줍니다. 그러면 템플릿에 그 변수를 템플릿 변수를 이용해 저장하면 우리는 응답(Response)으로 실시간으로 변하는 변수를 유저에게 전달해줄 수 있습니다.  

```
# index.html

<h2>{% raw %}{{ date }}{% endraw %}</h2>
``` 

#### 데이터를 유저로부터 받아온 경우  

🔔 동적 URL (Dynamic URL)  
: 유저가 누르는 버튼에 따라 <u>응답(Response)의 미세한 값만 다를 뿐, 템플릿은 같게 하고 싶은 경우</u>에 저희는 동적 URL을 사용할 수 있고 이를 우아한 URL이라고도 부릅니다.  


예를 들어 다음과 같이 각각의 메뉴에 대한 이름, 가격, 이미지를 보여주는 템플릿을 제공한다고 할 때, 이를 위해 각각의 URL 경로를 다 따로 적으면 굉장히 비효율적이게 됩니다.  

```python
# urls.py

urlpatterns = [
    path('menu/', views.index),
    path('menu/chicken', views.chicken),
    path('menu/pizza', views.pizza),
    path('menu/pasta', views.pasta)
]
```
이를 해결하기 위해 동적 URL을 도입하면 다음과 같이 나타낼 수 있습니다.  

```python
# urls.py 

urlpatterns = [
    path('menu/', views.index),
    path('menu/<str:food>', views.food_detail),
]
```
이렇게 하면 `menu/문자열food`을 요청하면 `food`가 `food_detail`에 인자로 들어가게 됩니다. 그러면 이 값을 받아 하나의 템플릿으로 여러 응답(Response)를 만들 수 있는 것입니다.  

```python
# views.py

def food_detail(request, food):
    context = {"name":food}
    return render(request, 'foods/detail.html', context=context)
```  

```
# detail.html

<h2>{% raw %}{{ food }}{% endraw %}</h2>
```

또 다른 예시로 만약 요청된 문자열이 치킨일 경우 이름, 가격, 이미지를 응답한다고 해보겠습니다. 이 때 유의할 점이 이미지는 정적 파일에 저장되어 있어서 `{% raw %}{% static {{img_path}} %}{% endraw %}` 이런식으로 저장해야 될 것 같은데 템플릿 언어는 이렇게 템플릿 태그 안에 템플릿 변수가 들어올 수 없습니다. 그래서 이 때는 특별한 템플릿 태그를 사용해야 합니다. 바로 `{% raw %}{% get_static_prefix %}{{변수}}{% endraw %}`입니다.
```
<img src={% raw %}{% get_static_prefix %}{{img_path}}{% endraw %}>
```  

### 템플릿 중복 코드 없애기(feat.상속)  
- `{% raw %}{% include '부품 템플릿' %}{% endraw %}`, `{% raw %}{% extends '부모 템플릿' %}{% endraw %}`과 같은 템플릿 태그를 이용해 템플릿을 조금 더 구조적으로 작성할 수 있습니다.  
    ![](/assets/images/d_temp_view_3.png){: width="100%"}  

```
# base.html (부모 템플릿)  

<html lang="en">
{% raw %}{% include 'head.html' %}{% endraw %}

<body style="font-family: 'NanumBarunpenR';">

    {% raw %}{% include 'header.html' %}{% endraw %}

    <hr>

    {% raw %}{% block content %}{% endraw %}
    {% raw %}{% endblock %}{% endraw %}

    <hr>

    {% raw %}{% include 'footer.html' %}{% endraw %}

</body>
</html>
```  

```
# create.html (자식 템플릿)  

{% raw %}{% extends 'base.html' %}{% endraw %}
{% raw %}{% load bootstrap4 %}{% endraw %}

{% raw %}{% block content %}{% endraw %}

  <div style="text-align:center; max-width: 500px; margin: 4rem auto">
      <div class="mb-4">
          <h4>Signup</h4>
      </div>
      <form action="{% raw %}{% url 'accountapp:create' %}{% endraw %}" method="post">
        {% raw %}{% csrf_token %}{% endraw %}
        {% raw %}{% bootstrap_form form %}{% endraw %}
        <input type="submit" class="btn btn-dark rounded-pill col-6 mt-3">
      </form>

  </div>

{% raw %}{% endblock %}{% endraw %}
```  

### 에러 페이지 처리하기  

🔔상태코드
: 요청에 대한 처리 결과가 어떻게 되었는지 알려주는 코드  

	1XX: 요청을 받아 진행중  
	2XX: 요청에 대한 처리 결과가 정상  
	3XX: 요청을 완료하기 위해 추가적인 동작 필요  
	4XX: 클라이언트의 요청에 문제가 있음 (404: 요청한 자원이 없다, 403: 접근 권한이 없다)  
	5XX: 서버가 요청을 처리하는 과정에서 문제가 발생  

```python
# views.py
from django.http import Http404

if ~:
else:
    raise Http404("이런 음식은 없습니다")
```

### URL에 이름 부여하기  
  
- URL에 이름을 부여해주면 나중에 구조가 바뀌더라도 모든 소스코드를 보지 않고도 URL을 관리할 수 있습니다.  

```python
# urls.py  

from django.urls import path
from . import views

urlpatterns = [
    path('diary/page/<int:page_id>/', views.page_detail, name='page-detail'),
```  

```
# page_list.html

<a href="{% raw %}{% url 'page-detail' obj.id %}{% endraw %}">
```  



