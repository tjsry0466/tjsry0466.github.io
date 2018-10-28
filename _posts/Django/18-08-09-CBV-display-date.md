---
layout: post
title: Django - Generic CBV View - display / Date
category: Django
tags: [Django, CBV, Display, Date, Generic, View, paging]
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

## built-in CBV API

- Generic <strong>Display</strong> Views
    - ListView, DatailView
- Generic <strong>Date</strong> Views
    - ArchiveIndexView, YearArchive View, MonthArchiveView, Week ArchiveView, DayArchiveView, TodayArchiveView
    DateDetailView
- Generic <strong>Editing</strong> Views
    - FormView, CreateVuewm UpdateView, DeleteView
    
## CBV에서 공통적으로 지원하는 옵션

- 템플릿을 사용하는 CBV
    - template_name: 디폴트 템플릿경로가 제공되지만 이를 변경하고자 할 때
- 리스트 형
    - allow_empty(디폴트:False): 조회할 데이터가 없을 경우, 404 예외 발생
    - paginate_by(디플트:None): 페이지당 갯수 지정
        - 지정시 페이징 처리(페이지 인자 예: ?page=1)
- 페이징 관련 클래스 : Page, Paginator

## ListView
### 지정 모델에 대한 전체 목록을 조회

- 디폴트 템플릿 경로: "앱이름/모델명소문자_list.html"
- 디폴트 context_object_name
    - "모델명소문자_list" : ex)post_list, comment_list, tag_list
    - 혹은 "object_list"

## 뷰 구현
> blog/views.py

```python
from django.http import Http404
from django.views.generic import LisView
from .models import Post

# 구현 1-1) FBV로 직접 간단 구현
def post_list (request):
    qa = Post.objects.all()
    allow_empty = False
    if not allow_empty and not qs.exists():
        raise Http404
    return render(request, 'blog/post_list.html', {
    'post_list' : Post.objects.all(),
    })
    
# 구현 1-2) CBV로 페이징 없이 구현 QuerySet은 Model.objects.all()로 지정됌
post_list = ListView.as_view(model=Post)

# 구현 2) QuerySet을 직접 지정
post_list = LIstView.as_view(model=POst, queryset=Post.objects.all().order_by('-id'))

# 구현 3-1) CBV로 페이징 처리
post_list = ListView.as_view(model=Post, paginate_by=10)

# 구현 3-2) 상속을 통한 CBV 구현 (모든 CBV에서 가능)
class PostListView(ListView):
    model = Post
    paginate_by = 10
    
post_list = PostListView.as_view()
```

## 템플릿 구현
> blog/templates/blog/post_list.html

```html
<ul>
{% raw %}
    {% for post in post_list %}
        <li>{{ post.title }}</li>
    {% endfor %}

</ul>

{% if is_paaginated %}

    {% if page_obj.has_previous %}
        <a href="?page={{ post_obj.previous_page_number }}">이전</a>
    {% endif %}
    
    {{ page_obj.number }}페이지
    
    {% if page_obj.has_next %}
        <a href="?page={{ page_obj.next_page_number }}">다음</a>
    {% endif %}
    
{% endif %}
{% endraw %}
```

## DetailView
### 지정 pk 혹은 slug의 모델 인스턴스에 대한 Detail
- 디폴트 템플릿 경로 : "앱이름/모델명소문자_detail.html"
- 디폴트 context_object_name
    - "모델명소문자" : ex)post, comment,tag
    - 혹은"object"
    
## URLConf 구현
> blog/urls.py

urlpatterns - [
    path('posts/<int:pk>', views.post_detail),
]

## 뷰 구현
> blog/views.py

```python
from django.views.generic import DetailView
from django.shortcuts import get_object_or_404
from .models import Post
# 구현 1) FBV로 구현
def post_detail(request, pk):
    post = get_object_or_404(Post, pk=pk)
    return render(request, 'blog/post_list.html', {
        'post': post,
    })
    
# 구현 2) CBV로 구현
post_detail = DetailView.as_view(model=Post)
```

## 템플릿 구현

> blog/templates/blog/post_detail.html

```html
{% raw %}
<h1>{{ post.title }}</h1>
{{ post.content|linebreaks }}
{% endraw %}
```

## Generic Date Views
- 공통 옵션
    - allow_future(디폴트:False)
        - False: 현재시간 이후의 Record는 제외
        
## ArchiveIndexView
### 지정 날짜필드 역순으로 정렬된 목록
- 필수 옵션
    - date_field: 지정 날짜필드
- 디폴트 템플릿 경로
    - "앱이름/모델명소문자_archive.html"
        - ex) "blog/post_archive.html"
- context
    - litest(디폴트 context_object_name): 지정날짜 필드 역순으로 정렬된 QuerySet
    - date_list: 년도별 포스팅의 지정 날짜필드
        -ex)[2016-01-01 00:00:00, 2017-01-01 00:00:00, ...]
          
## URLConf 구현
> blog/urls.py

```python
urlpatterns = [
    path('archive/', views.post_archive)
]
```

## View 구현
>blog/views.py

```python
from django.views.generic import ArchiveIndexView
from .models import Post
post_archive = ArchiveIndexView.as_view(model=Post, date_date_field)
```
## 템플릿 구현
> blog/templates/blog/post_archive.html

```html
{% raw %}
<h3>등록된 포스팅의 년도 (모두 1월 1일)</h3>
{% for date in date_list %}
    {{ date|date:"Y"}} <!-- ex)2017 -->
{% endfor %}
<h3>created_at 역순으로 정렬된 전체 포스팅</h3>
<ul>
    {% for post in latest %}
        <li>
            {{post.title}}
        </li>
    {% endfor %}
</ul>
{% endraw %}
```

## YearArchiveView
- URL Rule의 지정 Year년도의 목록
- 디폴트 옵션
    - date_list_period(디폴트:'month'): 지정 년도에서 월단위로 레코드가 있는 날짜를 뽑음
    - make_object_list(디폴트:False): 거짓일 경우, object_list를 비움(본 CBV에만 있는 옵션)
- 디폴트 템플릿 경로: "앱이름/모델명소문자_archive_year.html"
- context
    - date_list: 해당 년도의 월별 포스팅의 지정 날짜필드
        -ex)[2016-01-01 00:00:00, 2017-02-01 00:00:00, ...]
        
## URLConf 구현
> blog/urls.py

```python
from . import views


urlpatterns = [
    path(r'^archive/(?<year>\d{4})/$, views.PostArchiveYearView.as_view(), name='post_archive_year),
]
```

## 뷰 구현
>blog/views.py

```python 
django.views.generic.dates import YearArchiveView
from .models import Post
class PostArchiveYearView(YearArchiveView):
    model = Post
    date_field = 'created_at'
    # make_object_list = False
    
```
## 템플릿 구현
>blog/teampltes/blog/post_archive_year.html

```html
{% raw %}
<h3>{{year|date:"Y"}}</h3>
{% for post in object_list %}
    {{ post }}
{% endfor %}
<h4> 레코드가 있는 월</h4>
{% for date in date_list }
    {{ date|date:"Y-m"}} <!-- ex) 2017-01 -->
{% endfor %}
<hr/>
{% if previous_year %}
    <a href="{% url "blog:post_archive_year" previous_year|date "Y" %}",
        {{ previous_year|date:"Y년" }}
    </a>
{% endif %}
{% if next_year %}
    <a href="{% url "blog:post_archive_year|date:"Y" %}",
        {{ next_year|date:
"Y" }}
    </a>
{% endif %}
{% endraw %}
```

# 중략 . 이어서씀
