---
layout: post
title: Django-rest-framework 를 활용한 무한스크롤
category: Django
tags: [Django, django-rest_framework, ajax]
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

## django-rest-framework 간단 활용

> myapp/api.py

```python
from rest_framework import generics
from rest_framework.pagination import PageNumberPegination

class PostPagination(PageNumberPegination):
    page_size = 10
    
class PostListAPIView(Generics.ListAPIView): # CBV
    queryset = Post.objects.all()
    serializer_class = PostSerializer
    pagination_class = PostPagination
    
urlpatterns = [
    path('posts/', PostListApiView.as_view(), anme='post_list'),
]
```

> project/urls.py

```python 
urlpatterns = [
    path('api/v1/', include('myapp.api', anmespace='api')),
]

```

## Infinite Scroll
### 스크롤을 내리면, 다음 페이지를 로딩해서, 페이지 하단에 추가

```javascript
$(function() {
    var $win = $(window);
    var is_loading = false;
    
    / 매 화면 스크롤마다 호출
    $win.scroll(function() {
    // 문서의 끝에 도달했는가?
    var diff = $(document).height() - $win.height();
    if ( (!is_loading) && diff == $win.scrollTop() ) {
        var search_params = new URLSearchParams(window.location.search); // 현재 페이지의 GET인자를 가공
        var current_page = parseInt(search_params.get('page')) || 1; // GET인자 page를 획득하고 없으면 1을 반환
        var next_page_url = '?page-' + (current_page + 1); // 다음 페이지를 요청하기 위한 URL 생성
        
        is_loading = true;
        
        $.get(next_page_url).
            done(function(html) {
                $('#post_list').append(html);
                history.pushState({}, '', next_page)url);      
            }).
            fail(function(xhr, textStatus, error) {
                console.log(textStatus);
            })
            .always(function() {
                console.log("always");
                is_loading = false;
            });
        } 
    });
});
```