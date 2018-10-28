---
layout: post
title: Django - 위젯 만들기 (자동완성 Select 위젯) - Ajax로 개선하기
category: Django
tags: [Django, 자동완성, 위젯, Ajax]
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

>askcompany 03 - 위젯 만들기 (자동완성 Select 위젯)를 보고 정리했습니다.


## 개선 포인트

- ForeignKey를 통한 Select 위젯이기에, 매번 그려질 때마다 전체 Country Record에 대해 DB쿼리
    - django-debug-toolbar를 통해 확인해봅시다.
    
## Ajax를 통한 자동완성

> blog/forms.py

```python
class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = '__all__'
        widgets = {
            'country': AutoCompleteSelect(ajax_url=reverse_lazy('country_list')),
            'rating': RateitjsWidget,
        }
``` 

> blog/views.py

```python
from django.http import JsonResponse
from .models import Country

def country_list(request):
    qs = Country.objects.all()
    q = request.GET.get('q')
    qs = qs.filter(name__icontains=q)
    results = [{'id': country.id, 'text': country.name} for country in qs]
    return JsonResponse({
        'results': results,
    })
```

> blog/widgets.py

```python
class AuthCompleteSelect(forms.Select):
    template_name = 'widgets/autocaplete_select.html'
    
    class Media:
        css = {
            'all': [
                '//cdnjs.cloudflare.com/ajax/libs/select2/4.0.6-rc.0/css/select2.min.css',
            ],
        }
        js = [
            '//code.jquery.com/jquery-2.2.4.min.js',
            '//cdnjs.cludeflare.com/ajax/libs/select2/4.0.6-rc.0/js/select2.min.js',
        ]
    
    # ajax_url을 인자로 받기 위해서 씀
    def __init__(self, ajax_url, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.ajax_url = ajax_url
        
    def build_attrs(self, *args, **kwargs):
    context = super().build_attrs(*args, **kwargs):
    context['style'] = 'min-width: 202px;
    return context
    
    get_context(self, *args, **kwargs):
        context['ajax_url'] = self.ajax_url
        return context
       
    # ajax로 값을 가져오기 위해 기본적으로 값을 가져오지 못하게 막음.
    def optgroups(self, *args, **kwargs):
        exists_ids = [_id for _id in vlaue if _id]
        self.choices.queryset = self.choices.queryset.filter(id__in=exists_ids)
        return super().optgroups(name, value, attrs=None)
    
```

> blog/templates/widgets/autocomplete_select.html

```python
{% raw %}
{% include "django/forms/widgets/select.html" %}

<script>
$(function() {
    var options = {minimumInputLenghth: 1};
    
    var ajax_url = "{{ ajax_url|defualt:"" }}";
    if ( ajax_url.length > 0 ) {
        options['ajax'] = {
            'url' : ajax_url,
            'dataType': 'json'
        };
    }
    
    $('${{ widget.attrs.id }}').select2(options);
});
</script>
{% endraw %}
```

> blog/urls.py

```python
from django.urls import path
from . import views

urlpatterns = [
    path('country/', views.country_list, name='country_list'),
]