---
layout: post
title: Django SPA 프로젝트 - 기본 API 서버 구성하기
category: Django
tags: [Django, API server, rest-framework]
comments: true
---
<!----------------- 탬플릿
## forEach
### 설명
[MDN]()
### 문법
```javascript

```
### 예시
```javascript

```
------------------->

> django rest-framework로 API서버를 구성해보겠습니다.

### django 프로젝트 생성

```shell
# commend line
>>> pip3 install django~=2.0.0
>>> pip3 install django-rest-framework
>>> django-admin startproject spa
>>> cd spa
>>> django-admin startapp board
>>> python manage.py migrate
>>> python manage.py runserver
```

장고 개발 서버가 정상적으로 실행되었다면 127.0.0.1:8000로 접속시 이와 비슷한 화면이 보여지게 됩니다.
![Django 기본 페이지](https://code.visualstudio.com/assets/docs/python/django/django-empty-project-success.png)

### settings.py 설정
```python
# spa/spa/settings.py
...
allowed_hosts = ['*']

INSTALLED_APPS = (
    ...
    'board',
    'rest_framework',
)
# 선택에 따라 언어는 ko, 타임존은 Asia/Seoul로 설정하시면 됩니다.
```

