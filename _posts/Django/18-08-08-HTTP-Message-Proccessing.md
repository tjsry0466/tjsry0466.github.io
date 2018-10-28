---
layout: post
title: HTTP 메소드별 처리
category: Django
tags: [Django, Method, HTTP, HEAD, Proccessing]
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

>askcompany 강의를 보고 내용을 정리했습니다.

## 다양한 HTTP 메소드

- <strong>GET</strong>: 요청 URI의 정보
- <strong>POST</strong>: 요청 URI에 새로운 정보 보냄
- PUT: 요청 URI에 갱신할 정보 보냄(전체를 교체)
- PATCH: 요청URI에 갱신할 정보 보냄(일부를 교체)
- DELETE: 요청 URI의 리소스를 삭제
- HEAD: GET요청에서 body는 제외하고 헤더만 응답
- OPTIONS: 요청 URI에서 사용할 수 있는 Method를 응답
- TRACE: 보낸 메시지를 다시 돌려보낸다.

## HTTP 메소드별 인스턴스함수 지원

### 하나의 뷰(즉 하나의URL)에서 다양한 HTTP 메소드를 지원해야할 때

> django.views.generic.View

```python
class View(object):
    http_method_names = ['get','post','put','patch','delete','head','options','trace']
    #중략
    
    def dispatch(self, request, *args, **kwargs)
    # 본 함수를 통해 각 인스턴스 함수로의 분기를 처리
    # CBV에서는 모든 HTTP 요청은 dispatch 인스턴스 함수를 통해 처리됩니다.
    
    if request.method.lower() in self.http_method_names:
        handler = getattr(self, request.method.lower(), self.http_method_not_allowed) # 2번쨰 인자가 없다면 3번째 인자를 default값으로 사용
    else:
        handler = self.http_method_not_allowed
    return handler(request, *args, **kwargs)
```

## HEAD 요청을 통한 object update 여부 확인하기 (CBV)


```python
from django.http import Http404, HttpResponse

class PostListView(ListView):
    model = Post
    
    # 쿼리셋 변형 가능
    # queryset = Post.objects.all().order_by('id')
    # def get_queryset(self):
    #   pass
    
    def head(self, *args, **kwargs):
        try:
            post = self.get_queryset().latest('updated_at')
        except Post.DoesNotExist:
            raise Http404
            
        response = HttpResponse()
        # RFC 1123 date format
        response['Last-Modified'] = post.updated_at.striftime('%a, %d %b %Y %Y %H:%M:%S GMT') #언제 마지막으로 변경되었니?
        return response
        
    def delete(self, *args, **kwargs)
        raise NotImplementedError # 구현안됫어!
        
post_list =PostListView.as_view()
```

## FBV 스타일로 구현

```python

def post_list(request):
    if request.method == 'HEAD':
        try:
            post = Post.objects.all().latest('created_at')
        except Post.DoesNotExist:
            raise Http404
            
        response = HttpResponse()
        # RFC 1123 date format
        response['last_modified] = post.created_at.strftime('%a, %d %b %Y %H:%M:%S GMT')
        return response
        
    elif request.method == 'DELETE':
        raise NotImplementedError #구현 안햇어
        
    return render(request, 'myapp/post_list.html', {
        'post_list': Post.objects.all(),
    })
```

### python shell에서의 head 요청

```python
>>> import requests

>>> requests.head('주소').headers
{'server':wsgiserver/0,2-' ---}
```

