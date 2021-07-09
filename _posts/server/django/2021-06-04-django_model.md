---
title:  "[Django] 장고 모델(Model)"
toc: true
toc_sticky: true
header:
  teaser: /assets/images/django.png

categories:
  - django
tags:
  - Server
last_modified_at: 2021-06-04
---  

## 모델에서의 키워드  

### 데이터의 구조  
- 저장할 정보의 형태

### 데이터베이스  
- 실제로 데이터를 저장하는 곳

### ORM(Object-Relational Mapper)  
- SQL문 없이 데이터베이스에 접근 가능하도록 해주는 존재


## 모델 작성하기

- 사용할 데이터를 살펴보고 모델링 한 후, 그에 맞는 field를 작성합니다.  

```python
# models.py

class Menu(models.Model):
    name = models.CharField(max_length=50)
    description = models.CharField(max_length=100)
    price = models.IntegerField()
    img_path = models.CharField(max_length=255)

    # Menu 객체를 하나의 문자열로 표현
    def __str__(self):
        return self.name
```  

- 새로 생성된 모델, 변경된 모델은 장고에 반영해 줍니다.  

```
# CLI

python manage.py makemigrations
python manage.py migrate
```  

![](/assets/images/d_model_1.png){: width="70%"}  


- migration 파일  

![](/assets/images/d_model_2.png){: width="100%"}  


- migration 파일 목록 확인하고 싶을 때  

```
# CLI

python manage.py showmigrations
```

- 특정 migration 파일이 변환된 SQL문 보고 싶을 때

```
# CLI

python manage.py sqlmigrate {앱 이름} {번호}
python manage.py sqlmigrate foods 0001
-----------------------------------------------------
CREATE TABLE "foods_menu" ("id" integer NOT NULL PRIMARY KEY AUTOINCREMENT, "name" varchar(50) NOT NULL, "description" varchar(100) NOT NULL, "price" integer NOT NULL, "img_path" varchar(255) NOT NULL);
COMMIT;
```

### 모델 필드 정리  

#### 필드 유형  

![](/assets/images/d_model_6.png){: width="100%"}  

#### 필드 옵션
![](/assets/images/d_model_7.png){: width="100%"}    







## 데이터베이스와의 소통: CRUD(Create, Read, Update, Delete)  

### 데이터 생성: Create

```python
# 데이터 생성과 동시에 데이터베이스에 반영
data_model = Menu.objects.create(price=10000)

# 데이터 생성과 데이터베이스에 반영 분리
data_model = Menu(price=10000)
data_model.save()
```

### 데이터 조회: Read  

```python
from foods.models import Menu

# 모든 데이터 조회
Menu.objects.all()

# 모든 데이터의 값까지 조회
Menu.objects.all().values()

# 모든 데이터의 특정 field값 조회
Menu.objects.all().values('price')

# 모든 데이터를 특정 field값 기준으로 정렬해서 조회
Menu.objects.order_by('price')

#  내림차순
Menu.objects.order_by('-price')

# 특정조건을 만족하는 하나의 데이터를 조회
# 조건을 만족하는 데이터가 2개 이상인 경우 오류 발생
Menu.objects.get({필드명}__조건키워드="조건")

# 오류 발생할 가능성 높다
Menu.objects.get(name__contains="코")

# 주로 이런 id값을 통해 접근할 때 많이 사용된다
Menu.objects.get(id=1)

# 특정조건을 만족하는 여러 데이터를 조회
Menu.objects.filter({필드명}__조건키워드="조건")

Menu.objects.filter(name__contains="코")
Menu.objects.filter(price__range=(2000, 10000))
```  

- 그 밖의 조건 키워드  

![](/assets/images/d_model_11.png){: width="100%"}  

### 데이터 수정: Update

```python
data = Menu.objects.get(id=1)

# 데이터 수정
data.name = '가나다'

# 데이터 저장
data.save()
```

### 데이터 삭제: Delete

```python
data = Menu.objects.get(id=3)

# 데이터 삭제
data.delete()
```  

### 모델 API 정리하기  

#### Queryset을 반환하는 API (여러개의 데이터)  

![](/assets/images/d_model_8.png){: width="100%"}  

#### 하나의 데이터 객체를 반환하는 API  

![](/assets/images/d_model_9.png){: width="100%"}  

#### 그 외 API  

![](/assets/images/d_model_10.png){: width="100%"}  

## 관리자 도구(Admin) 사용하기

### 관리자 도구가 모델을 관리할 수 있도록 모델 추가  

```python
# admin.py

# 추가해줄 모델 가져오기
from foods.models import Menu

# 모델 등록
admin.site.register(Menu)
```

```
# CLI
# 관리자 계정 생성하기
python manage.py createsuperuser
id, email, pw  입력

# 서버 실행
python manage.py runserver
```  

```
# web_browser
domain/admin으로 접속
```  

- 관리자 도구를 통해 데이터를 CRUD할 수 있습니다.  

![](/assets/images/d_model_3.png){: width="100%"}  

![](/assets/images/d_model_4.png){: width="100%"}  


## 모델 적용하기  

🔔 하드 코딩(HardCoding)  
: 소스 코드 안에 데이터가 직접 입력되어 있는 코딩 형태  

![](/assets/images/d_model_5.png){: width="100%"}  

이렇게 데이터가 템플릿에 바로 적용되어 있는 경우를 하드 코딩되어 있다고 하며 이런 경우에는 프로그램의 유지 보수가 어렵기 때문에 지양되어야 합니다. 따라서 이를 해결하기 위해 우리는 View가 Model로 부터 데이터를 받아 Template에 적용시킬 수 있도록 만드는 것이 좋습니다.  

### View에서 Model로 부터 데이터 가져오기  

```python
# views.py

# 모델 불러오기
from foods.models import Menu

# 모델로 부터 데이터 가져오기
menus = Menu.objects.all()
# menus = [Menu, Menu, Menu, ...]과 같은 queryset 저장되어 있음

# 템플릿에 적용되는 context에 담기
context = dict()

context["menus"] = menus

# 전체 코드
def index(request):
    today = datetime.today().date()
    menus = Menu.objects.all()
    context = dict()
    context["date"] = today
    context["menus"] = menus
    return render(request, 'foods/index.html', context=context)
```

### 템플릿에 적용하기

```
# indext.html

{% raw %}{% block food-container %}{% endraw %}
  {% raw %}{% for menu in menus %}{% endraw %}
    <div class="food">
      <img src={% raw %}{% get_static_prefix %}{{menu.img_path}}{% endraw %} width="300px" height="200px"/>
      <div class="info">
        <h3>{% raw %}{{menu.name}}{% endraw %}</h3>
        <P>{% raw %}{{menu.description}}{% endraw %}</p>
        <a href="{% raw %}{{menu.id}}{% endraw %}">메뉴 보기</a>
      </div>
    </div>
  {% raw %}{% endfor %}{% endraw %}
{% raw %}{% endblock food-container %}{% endraw %}
```
