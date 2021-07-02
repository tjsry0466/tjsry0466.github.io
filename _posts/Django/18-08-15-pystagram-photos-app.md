---
layout: post
title: Django - pystagram photo 앱 과 모델 만들기
category: Django
tags: [Django, pystagram]
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

> [pystagram 만들기](https://blog.hannal.com/2014/8/start_with_django_webframework_01/) 를 참고하였습니다.

## photo앱 모델 만들기

> photos/models.py

```python
class Photo(models.Model):
    image = models.ImageField()
    filtered_image = models.ImageField()
    content = models.TextField(max_length=500, null=True, blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
```

## photo앱 admin 만들기

> photos/admin.py

```python
from django.contrib import admin
from .models import Photo

admin.site.register(Photo)
```