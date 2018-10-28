---
layout: post
title: Django - CBV에 장식자 적용하기
category: Django
tags: [Django, Python, 장식자, Decorators]
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

## 장식자(Decorators)

- 어떤 함수를 감싸는(Wrapping)목적의 함수

```python
def base_10(fn):
    def wrap(x, y):
        return x + y + 10
    return wrap
    
@base_10
def mysum2(x, y):
    return x + y
    
# 이는 아래 코드와 동일

def mysum1(x, y):
    return x + y
mysum1 = base_10(mysum1)
```

## FBV) 뷰 함수에 장식자 적용

```python
from django.contrib.auth.decorators import login_required
from django.shortcuts import render


@login_required
def protected_view(request):
    return render(request, 'myapp/secret.html')
```

## CBV) FBV스타일로 장식자 적용

```python
from django.contrib.auth.decorators import login_required
from django.views.generic import TemplateView

class SecretView(TemplateView):
    template_name = 'myapp/secret.html'
    
view_fn = SecretView.as_view()

secret = login_requred(view_fn) # 생선된 함수에 장식자 입히기
```

## CBV) 클래스에 직접 장식자 입히기 #1
>django.utils.decorators.method_decorator 활용

- 모든 요청은 idspatch 함수를 통해 처리되므로, dispatch 멤버함수를 꾸며야합니다.

```python
from django.contrib.auth.decorators import login_required
from django.utils.decorators import method_decorator
from django.views.generic import TemplateView


class ProtectedView(TemplateView):
    template_name = 'myapp/secret.html'
    
    @method_decorator(login_required) # 인스턴스 함수를 꾸밀 때
    def dispatch(self, *args, **kwargs):
        return super().dispatch(*args, **kwargs)
        
protected = ProtectedView.as_view()
```

## CBV) 캘르세이 직접 장식자 입히기 #2 (추천)

```python
from django.contrib.auth.decorators import login_required
from django.utils.decorators import method_decorator
from django.views.generic import TemplateView


@method_decorator(login_required, name='dispatch) # 클래스를 꾸밀 때, 인스턴스 함수명을 지칭
class AnotherProtectedView(TemplateView):
    template_name = 'myapp/scret.html'
    
another_protected = AnotherProtectedView.as_view()
```