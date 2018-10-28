---
layout: post
title: Django - CBV 상속을 통한 특정 루틴 재정의(Overriding)
category: Django
tags: [Django, CBV, Overriding]
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

>[상속을 통해 CBV 특정 루틴 재정의(Overriding)](https://www.askcompany.kr/vod/cbv/139/)

## CBV에서의 Overriding 활용
### Overriding 대상 :클래스 변수, 클래스 함수, 인스턴스 함수
```python
from django.http import HttpResponse
from django.views import View


class GreetingView(View):
    message = 'Good Day' # Class Variable
    
    def get(self, *args, **kwargs):
        return HttpResponse(self.message)
        
greeting = GreetingView.as_view()
    
    
class MorningGreetingView(GreetingView):
    message = 'Morning to ya' # Class variable 재정으
    
morning_greeting = MorningGreetingView.as_view()

evening_grrting = GreetingView.as_view(message='Evening to ya')
```

## view_fn= CBV.as_view(**init_kwargs)의 동작방식
### 주요 루틴만 간략하게 기술함.

```python
class View(object):
    def __init__(self, **kwargs):
        for key, value in kwargs.items(): # 3) 인스턴스 변수 세팅
            setattr(self, key, value)     # => 이는 클래스 변수값 변경이 아닙니다.
            
    @classmethod
    def as_view(cls, **initkwargs):       # 1) as_view 클래스함수의 인자로 주어진 keyword arguments는
        def view(request, *args, **kwargs):
            self = cls(**initkwargs)      # 2) 내부적으로 CBV클래스 생성자의 인자로 넘겨지며,
            return self.dispatch(request, *args, **kwargs)
        return view
```

## 함수로 재구현(스타일 1)
```python
def greeting(request, message='Good Day'):
    return HttpResponse(message)
    
def morning_greeting(request):
    return greeting(request, 'Morning to ya')
    
def evening_greeting(request):
    return greeting(request, 'Evening to ya')
```

## 함수로 재구현(스타일 2)

```python
def greeting_view(message):
    def view_fn(request):
        return HttpResponse(message)
    return view_fn
    
greeting = greeting_view('Good Day')
morning_greeting = greeting_view('Morning to ya')
evening_greeting_view('Evening to ya')
```

## 복잡한 뷰를 여러 함수에 나눠 구현하고, 

```python
    instance = get_object_or_404(Post, id=id) #Case 1) 인스턴스 획득 조건을 커스텀
    
    if request.method == 'POST' :
        form = PostForm(request.POST, request.FILES, instance=instance)
        if form.is_valid():
            post= form.save()
            return redirect('/success_url/') # Case 2) 이동할 URL 변경
    else:
        form = PostForm()
    return render(request, 'myapp/post_form.html, {'form': form}) # Case 3) 추가 파라미터 지정


```

## 재사용이 가능토록, CBV 패턴으로 재구현

```python
def django.views import View

class EditFormView(View):
    model = None
    form_class = None
    success_url = None
    template_name = None
    
    def get_object(self):
        pk = self.kwargs['pk'] # url패턴에서의 pk인자
        return get_objects_or_404(self.model, id=pk)
        
    def get_success_url(self):
        return self.success_url
        
    def get_template_name(self):
        return self.template_name
        
    def get_form(self): # form instance 생성
        form_kwargs = {
            'instance' : self.get_object(),
        }
        if self.request.method == 'POST':
            form_kwargs.update({
                'date': self.request.POST,
                'files': self.request.FILES,
            })
        return self.form_class(**form_kwargs)
        
    def get_context_date(self, **kwargs): # 템플릿에 넘길 인자들을 사전 형태로 세팅
        if 'form' not in kwargs:
            kwargs{'form'} = self.get_form()
        return kwargs
        
    def get(self, *args, **kwargs)
        return render(self.request, self.get_template_name(), self.get_context_date())
        
    def post(self, *args, **kwargs):
        form = self.get_form()
        if form.is_valid():
            form.save()
            return redirect(self.get_get_success_url())
        return render(self.request, self.get_template_name(), self.get_context_date(form=form))
        
```

## 활용 1) 클래스 변수값만 재정의

```python
post_edit = EditFormView.as_view(
    model=post,
    form_class=PostForm,
    success_url='/weblog/',
    template_name='blog/post_form.html')

# 혹은

class PostEditFormView(EditFormView):
    model =Post
    form_class=PostForm
    success_url = '/'
    template_name = 'blog/post_form.html'

post_edit = PostEditFormView.as_view()
```

## 활용 2) 각 인스턴스 함수들을 재정의

```python
from django.shortcuts import resolve_url

class PostEditFormView(EditFormView):
    model = Post
    template_name='blog/post_form.html'
    
    def get_object(self):
        pk= self.kwargs['pk']
        return get_object_or_404(self.model, id=pk, created_at__year=2018) #2018 포스팅 중에서 가져옴 (is_pulic 같은 옵션을 사용하면 유용함)
        
    def get_context_date(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['dummy_params'] = '더미 인자'
        return context
        
    def get_succeess_url(self):
        return resolve_url('blog:post_detail', self.kwargs['pk'])
        
post_edit = PostEditFormView.as_view()
```

# 마지막으로,

- 각 CBV 동작은 소스코드를 직접 살펴보고 이해하도록 합니다
- 파이썬에서 멤버함수 호출순서는 MRO 순서를 따릅니다.
- 이번 게시글의 코드는 클래스 상속의 설명을 위한 코드일뿐, 실제 코드에서는 django.views.generic.edit에 updateview, daleteview와 같은 장고 내장 클래스를 사용합니다.
