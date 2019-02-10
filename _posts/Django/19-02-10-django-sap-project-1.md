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
> 핵심적인 내용만 간추려 장고의 기본적인 내용은 담지 않았습니다.

## API 서버 구성

## models.py
```python
# spa/board/models.py
from django.conf import settings
from django.db import models


class Post(models.Model):
    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    title = models.CharField(max_length=50)
    content = models.TextField()
    # like = models.ManyToManyField() # 추후 추가
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.title

class Comment(models.Model):
    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    post = models.ForeignKey('post', related_name='post_comment', on_delete=models.CASCADE)
    comment = models.CharField(max_length=50)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.post.title+': ' +self.comment # 게시물: 댓글
```

## views.py
```python
# spa/board/views.py
from django.shortcuts import render
from .serializers import PostSerializer, CommentSerializer
from .models import Post, Comment
from rest_framework import viewsets

class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.all()
    serializer_class = PostSerializer

class CommentViewSet(viewsets.ModelViewSet):
    queryset = Comment.objects.all()
    serializer_class = CommentSerializer
```

## urls.py
```python
# spa/board/urls.py
from django.urls import path, include
from rest_framework import routers
from .views import PostViewSet, CommentViewSet

router = routers.DefaultRouter()
router.register(r'posts', PostViewSet)
router.register(r'comments', CommentViewSet)

app_name="board"
urlpatterns = [
    path('api', include(router.urls)),
]
```

위와 같이 구성하고 개발서버를 실행하게되면 아래와같이 API서버를 구성할수 있게 됩니다.
<center>
 <figure>
 <img src="..\..\assets\post-img\django\restframework_list.png" alt="views">
 <figcaption>rest framework list view</figcaption>
 </figure>
 </center>

