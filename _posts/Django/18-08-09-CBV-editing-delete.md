---
layout: post
title: Django - Generic CBV View - editing / delete
category: Django
tags: [Django, CBV, Editing, Delete, Generic, View]
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

# Generic Editing Views

## FormView 
### form을 처리하는 CBV

- 유효성 검사에 실패하면 오류정보를 노출하고
- 성공하면 form_valid()를 처리하고 Success URL로 이동
- 옵션
- form_class 옵션 (필수) : Form 클래스 지정
- success_url 옵션 (필수) : 유효성 검사 성공 시 이동할 URL 지정
- url reverse를 수행해주지 않습니다.
- template_name 옵션 (필수)
- form_valid, form_invalid 멤버함수를 통해 form valid/invalid 시의 처리 구현


## 뷰 구현
> blog/views.py

```python
from django.urls import reverse
from django.shortcuts import resolve_url
from django.views.generic import FormView
from .forms import PostForm

class PostCreateView(FormView):
    form_class = PostForm    
    template_name = 'blog/post_form.html'
    
    def form_valid(self, form):
        'form.is_valid() 시에 호출'
        self.post = form.save()
    return super().form_valid(form)

    def get_success_url(self):
        'form_valid(form) 처리 후에, 이동할 URL을 획득'
    return resolve_url(self.post) # Post모델에 get_absolute_url() 멤버함수 구현 필요

# return self.post.get_absolute_url() # 대안 1
# return reverse('blog:post_detail', args=[self.post.id]) # 대안 2
```

## URLConf 구현
> blog/urls.py

```python
from django.conf.urls import path
from . import views

urlpatterns = [
    path('new/', views.PostCreateView.as_view(), name='post_new'),
]
```

## 템플릿 구현
> blog/templates/blog/post_form.html

```python
{% raw %}
<form action="" method="post">
    {% csrf_token %}
        <table>
            {{ form }}
        </table>
    <input type="submit" />
</form>
{% endraw %}
```

## CreateView
### Model Instance를 생성
- 옵션
- model 옵션 (필수)
- form_class 옵션 : 제공하지 않을 경우, Model로부터 ModelForm을 생성/적용
- fields 옵션 : 지정 field에 대해서만 Form 처리
- success_url 옵션 : 제공하지 않을 경우,
model_instance.get_absolute_url() 획득을 시도
- 디폴트 템플릿 경로 : "앱이름/모델명소문자_form.html"


## 뷰 구현
> blog/views.py

```python
from django.views.generic import CreateView
from .models import Post
from .forms import PostForm


    class PostCreateView(CreateView):
        model = Post
        form_class = PostForm
        
        #urls/template은 위 FormView 구성을 이용 가능
```


## UpdateView
### Model Instance를 수정
- 옵션은 위 CreateView와 동일


## URLConf 구현
> blog/urls.py

```python
from django.conf.urls import path
from . import views


urlpatterns = [
    path('<int:pk>/edit/', views.PostUpdateView.as_view(), name='post_edit'),
]
```

## 뷰 구현
> blog/views.py

```python
from django.views.generic import UpdateView
from .models import Post
from .forms import PostForm


class PostUpdateView(UpdateView):
    model = Post
    form_class = PostForm
```

### 템플릿은 범용 템플릿을 그대로 활용

## DeleteView 
### 지정 Model Instance 삭제확인 및 삭제
- 옵션
- model 옵션 (필수)
- success_url 옵션 (필수)
- 디폴트 템플릿 경로 : "앱이름/모델명소문자_confirm_delete.html"



## URLConf 구현
> blog/urls.py

```python
from django.conf.urls import path
from . import views

urlpatterns = [
    path('<int:pk>/delete/', views.PostDeleteView.as_view(), name='post_delete'),
]
```


## 뷰 구현
> blog/views.py

```python
from django.views.generic import DeleteView
from .models import Post


class PostDeleteView(DeleteView):
    model = Post
    success_url = reverse_lazy('blog:post_list')
    
    #Tip: 전역변수/클래스 변수에서 url reverse가 필요할 때 reverse_lazy를 사용
```

## 템플릿 구현
>blog/templates/blog/postconfirmdelete.html

```html
{% raw %}

<form action="" method="post">
    {% csrf_token %}
        정말 삭제하시겠습니까?
    <input type="submit" value="삭제하겠습니다." />
</form>
{% endraw %}
```