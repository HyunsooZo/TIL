# 🤠 Python : Django

## Django?

Django는 파이썬(Python)으로 작성된 오픈소스 웹 프레임워크이다.\
"The web framework for perfectionists with deadlines."라는 슬로건처럼, \
빠른 개발(rapid development)과 깔끔한 설계(clean design)를 핵심 목표로 한다.\
많은 반복적인 작업을 자동화하며, 보안, 확장성, 유지보수성을 고려한 구조를 가지고 있다.

***

### How Django Works

Django는 전통적인 MVC 아키텍처를 기반으로 하지만, \
Django 내부적으로는 이를 **MTV(Model-Template-View)** 구조라고 부른다.\
단, 여기서의 View는 Spring이나 Java 진영에서 말하는 Controller의 역할에 해당하며, \
이름만 다를 뿐 MVC 구조와 본질적으로 동일하다.

* **Model**: 데이터베이스와 연동되는 ORM 모델. (Spring의 Entity와 유사)
* **Template**: 사용자에게 보여줄 HTML 화면을 담당. (JSP/Thymeleaf와 유사)
* **View**: 요청을 받아 비즈니스 로직을 처리하고 결과를 반환. (Spring의 Controller에 해당)

### Request-Response Flow

<figure><img src="../../.gitbook/assets/스크린샷 2025-03-22 오후 11.31.28.png" alt=""><figcaption></figcaption></figure>



***

## How to Use Django

#### 1. 프로젝트 생성 및 실행

```bash
pip install django
django-admin startproject mysite
cd mysite
python manage.py runserver
```

`http://127.0.0.1:8000`으로 기본 서버 실행 확인 가능.

#### 2. 앱 생성 및 등록

Django는 기능 단위를 **App**으로 분리해서 구성한다.

```bash
python manage.py startapp blog
```

생성된 앱은 `settings.py`에 등록해야 한다.

```python
INSTALLED_APPS = [
    ...
    'blog',
]
```

#### 3. 모델 정의 및 마이그레이션

```python
# blog/models.py
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=100)
    content = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
```

```bash
python manage.py makemigrations
python manage.py migrate
```

Django의 ORM은 모델 클래스만 작성하면, 이를 기반으로 SQL을 자동 생성한다.

#### 4. View 작성 및 URL 연결

```python
# blog/views.py
from django.shortcuts import render
from .models import Post

def post_list(request):
    posts = Post.objects.all()
    return render(request, 'blog/post_list.html', {'posts': posts})
```

```python
# blog/urls.py
from django.urls import path
from . import views

urlpatterns = [
    path('', views.post_list),
]
```

```python
# mysite/urls.py
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('admin/', admin.site.urls),
    path('blog/', include('blog.urls')),
]
```

#### 5. 템플릿 작성

```html
<!-- templates/blog/post_list.html -->
<h1>Blog Posts</h1>
{% raw %}
{% for post in posts %}
    <h2>{{ post.title }}</h2>
    <p>{{ post.content }}</p>
{% endfor %}
{% endraw %}
```

***

## Django as a Backend Server

Django는 HTML을 렌더링하는 전통적인 서버 역할 외에도, \
**REST API 서버**로도 많이 사용된다. \
이 경우 Django 자체보다는 **Django REST Framework(DRF)** 를 함께 사용한다.

#### API 기반 서버 구성 예시

```bash
pip install djangorestframework
```

```python
# settings.py
INSTALLED_APPS = [
    ...
    'rest_framework',
]
```

```python
# blog/views.py
from rest_framework.decorators import api_view
from rest_framework.response import Response

@api_view(['GET'])
def api_post_list(request):
    data = {"message": "Hello, API!"}
    return Response(data)
```

```python
# blog/urls.py
from django.urls import path
from .views import api_post_list

urlpatterns = [
    path('api/posts/', api_post_list),
]
```

이처럼 Django는 REST API 서버, JSON 응답 처리, 모바일/SPA 백엔드로도 널리 활용된다.

***

## Key Features of Django

<table><thead><tr><th width="177.4375">Feature</th><th>Description</th></tr></thead><tbody><tr><td>ORM</td><td>SQL을 작성하지 않고도 DB를 조작할 수 있는 객체 지향적 데이터베이스 접근 방식</td></tr><tr><td>Admin</td><td>Model을 기반으로 자동으로 생성되는 강력한 관리자 페이지</td></tr><tr><td>Form</td><td>HTML Form 생성 및 유효성 검사를 자동 처리</td></tr><tr><td>Authentication</td><td>로그인, 권한, 세션 등을 손쉽게 구현할 수 있는 시스템 내장</td></tr><tr><td>Middleware</td><td>요청과 응답을 가로채 가공할 수 있는 훅 시스템</td></tr><tr><td>Routing</td><td>URLConf 기반의 명확한 URL 매핑 구조</td></tr><tr><td>Template Engine</td><td>Django Template Language(DTL) 기반의 HTML 렌더링</td></tr><tr><td>Security</td><td>XSS, CSRF, SQL Injection 등 보안기능 기본 내장</td></tr><tr><td>Internationalization</td><td>다국어(i18n) 및 지역화(l10n) 지원</td></tr><tr><td>Scalability</td><td>대규모 프로젝트 및 API 서버에 적합한 구조 확장성</td></tr></tbody></table>

***

## Django vs Spring Framework

<table><thead><tr><th width="126.03125">Aspect</th><th width="303.43359375">Django</th><th>Spring</th></tr></thead><tbody><tr><td>Language</td><td>Python</td><td>Java</td></tr><tr><td>구조</td><td>MTV (Model-Template-View)</td><td>MVC (Model-View-Controller)</td></tr><tr><td>ORM</td><td>Django ORM 내장</td><td>JPA, Hibernate 등 외부 ORM 사용</td></tr><tr><td>설정 방식</td><td>Convention 기반, 설정 적음</td><td>명시적 설정과 구성 강조 (자유도 높음)</td></tr><tr><td>생산성</td><td>빠른 개발에 강함 (startup-friendly)</td><td>대규모 시스템에 강함 (enterprise-friendly)</td></tr><tr><td>템플릿</td><td>DTL</td><td>Thymeleaf, JSP 등 다양함</td></tr><tr><td>테스트</td><td>파이썬의 unittest 기반</td><td>JUnit 기반 (Mockito 등 통합 가능)</td></tr><tr><td>진입 장벽</td><td>상대적으로 낮음</td><td>상대적으로 높음</td></tr><tr><td>API 개발</td><td>Django REST Framework</td><td>Spring Web + Spring Data REST 등</td></tr><tr><td>비동기 지원</td><td>Django 3.1+부터 ASGI로 지원</td><td>Spring WebFlux (Reactor 기반)</td></tr><tr><td>배포</td><td>WSGI/ASGI, Gunicorn, uWSGI</td><td>Tomcat/Jetty, Spring Boot 내장 서버 등</td></tr></tbody></table>

***

## Summary

Django는 빠르게 웹 애플리케이션을 만들고자 하는 개발자에게 매우 적합한 프레임워크이다.\
ORM, Admin, Form, Authentication 같은 기능들이 모두 내장되어 있어 별도 설정 없이 바로 사용 가능하다.

또한 REST API 서버, 모바일 백엔드, 관리자 도구, 대시보드 등 다양한 용도로도 활용이 가능하다.\
최근에는 Django Channels를 활용한 WebSocket 처리, Django + Celery를 통한 \
비동기 작업 처리 등 확장성도 높아지고 있다.

반면 Spring은 복잡하지만 세밀한 제어가 가능하여, 규모가 크고 다양한 요구사항을 가진 프로젝트에 적합하다.

둘 중 어떤 프레임워크가 더 낫다기보다는, **프로젝트의 성격과 팀의 개발 환경에 맞는 선택**이 중요하다.
