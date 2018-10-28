---
layout: post
title: Ajax with Django #1
category: Django
tags: [Django, Ajax]
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

>askcompany EP 05 - Ajax with Django #1 을 보고 정리했습니다.

## 코드 구현

- List Pagination
    - HTML만을 통한 페이징 처리
    - Ajax를 통한 페이징 처리
- 뷰에서의 응답 포맷: HTML or JSON
- 무한 스크롤(infinite Scroll)

## List Pagination

- List 뷰에서의 페이징 처리: ListVuew CBV 에서 paginate_by 인자를 지정하면, 페이징 처리를 해줍니다.
    - 관련 context data : paginator, page_obj, is_paginated
    
```python
from django.views.generic import LiveView

class PostListView(ListView):
    model = Post
    template_name = 'blog/index.html'
    paginate_by = 10
    
    # ajax시에 특정 템플릿을 호출함
    def get_template_names(self):
        if self.request.is_ajax():
            return ['blog/_post_list.html']
        return ['blog/index.html']
```

> blog/templates/blog/_post_list.html

```html
{% raw %}
<table class="table table-bordered table-hover">
    <tbody>
        {% for post in post_list %}
        <tr>
            <td>
                <a href="{{ post.get_absolute_url}}">
                    {{ post.title }}
                </a>
            </td>
        </tr>
        {% endfor %}
    </tbody>
</table>

<hr/>

{% if is_paginated %}
    {% if page_obj.has_previous %}
        <a href="?page={{ page_obj.previous_page_number }}" class="btn btn-defualt">이전</a>
    {% endif %}
    
    {{ page_obj.number }} 페이지
    
    {% if page_obj.has_next %}
        <a href="?page={{ page_obj.next_page_number }}" class="btn btn-default">다음</a>
    {% endif %}
{% endif %}


<hr/>

<a id="page-2-btn" href="#">2페이지 로딩</a>

<a href="{% url "blog:post_new" %}" class="btn btn-primary">새 글쓰기</a>
{% endraw %}
```

## 브라우저 히스토리 조작하기

페이지 전환없이 URL만 조작하기
<strong>HTML5</strong>, <strong>history</strong>객체의 <strong>pushState</strong>를 활용

```javascript
var state_obj = {}; // pushstat 후에 history.state로 접근 가능
var title = "";
var url = "?page=2; // 이동할 URL
history.pushState(state_obj, title, url);
```  

> blog/templates/blog/index.html

```javascript
<script>
$(function() {
    $('#page-2-btn').click(function() {
        $.get('?page=2')
            .done(function(html) {
                console.log(html);
                $('#post-list-wrapper').html(html);
                
                var state_obj = {}; // pushState 후에 history.state로 접근 가능
                var title = "";
                var url = "?page=2"; // 이동할 URL
                history.pushState(state_obj, title, url);
            })
            .fail(finction() {
                console.log('fail');
            })
            .always(function() {
                console.log('always');
            });
        return false;
    });
});
</script>

(...)
```

## 응답포맷
### 브라우저에서는 HTML 포맷의 데이터가 필요합니다.

### 1. HTML 포맷을 서버에서 만들어서 응답으로 주거나

```python
def my_view_fn_1(request):
    response = render(request, 'myapp/my_view_fn_1.html', {
        'post_list': Post.objects.all(),
    })
    return response
```

### 2. 서버에서 Raw 데이터 응답을 하면(주로 JSON포맷), 웹 프론트엔드 JavaScript 단에서 이를 HTML 포맷으로 변환

> myapp/views.py

```python
from django.http import JsonResponse

def my_view_fm_2(request):
    qs = Post.objects.all()
    
    # list comprehension 문법을 통해, 수동 직렬화
    post_list = [
        {'id': post.id, 'title': post.title }
        for post in qs]
    return JsonResponse(post_list, safe=False) # safe=True일 때에는 dict타입만 받고, 아닐 경우 TypeError 예외 발생
```

## JSON응답을 하기 위해서는, JSON 직렬화가 필요
### ex) QuerySey/Model 객체를 list/tuple/dict으로 변환

- 직접 직렬화 코딩을 하거나
- django-rest-framework 활용

> myapp/serializers.py

```python
from rest_framework.serializers import ModelSerializer

class PostSerializer(ModelSerializer): # Django Form/ModelForm과 유사
    class Meta:
        model = Post
        fields = '__all__'
```

> myapp/views.py

```python
from django.http import HttpResponse
from rest_framework.renders import JSONRenderer
from .serializers import PostSerializer

def post_list(request):
    qs = Post.objects.all()
    serializer = PostSerializer(qs, many=True)
    json_utf8_string = JSONRenderer().render(serializer.data)
    # return HttpResponse(json_utf8_string) # Content-Type헤더가 text/html; charset=utf-8 로 디폴트 지정
    return HttpResponse(json_utf8_string, context='application/json; charset=utf8') #커스텀 지정
```

> myapp/urls.py

```python
urlpatterns = [
    path('posts\.json', views.post_list_json),
]
```

## Ajax HTTP 요청 여부 판단
- Ajax 요청에는 X-Requeted-With헤더에 "XMLHttpRequest"값이 전달
- django 뷰에서는 request.is_ajax()로 판단

```python
class PostLIstView(ListView):
    def render_to_response(self, context):
        # Ajax요청이 아니면, 템플릿 응답을 하고
        if not self.request.is_ajax():
            return super().render_to_response(context)
            
            # Ajax요청일 경우에는 JSON응답을 하겠습니다.
            qs= context['post_list']
            serializer = PostSerializer(qs, many=True)
            json_utf8_string = JSONRenderer().render(serializer.data)
            return HttpResponse(json_utf8_string, content_type='aaplication/json; charset=utf8') # 커스텀 지정
```

