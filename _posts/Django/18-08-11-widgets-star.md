---
layout: post
title: Django - 위젯 만들기 (별점)
category: Django
tags: [Django, 위젯, 별점]
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

>askcompany 03 - 위젯 만들기 (별점)를 보고 정리했습니다.

## Form 렌더링 시의 기본 템플릿 코드

```python
{% raw %}
{{ form.media }}

<form action="" method="post">
    {% csrf_token %}
    <table>
        {{form.as_table }}
    </table>
    <input type="submit" />
</form>
{% endraw %}
```



> blog/widgets.py

#### 15-jquery-rating-plugins[[site]](https://www.learningjquery.com/2016/09/15-jquery-rating-plugins)

```python
class RatejsWidget(forms.Numberwidget):
    template_name = 'widgets/rateitjs_number.html'
    
    class Media:
        css = {
            'all' : [
                'rateit.js/rateit.css',
            ],
        }
        js = [
            JQUERY_URL,
            'rateit.js/jquery.rateit.min.js',
        ]
        
    def build_attrs(self, *args, **kwargs):
        attrs = super().build_attrs(*args, **kwargs)
        attrs.update({
            'min' : '0',
            'max' : 5,
            'step' : 1,
        })
        return attrs
```

## 전체 코드 

> blog/forms.py

```python
from django import forms
from .models import Post
from .widgets import RateitjsWidget

class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = '__all__'
        widgets = {
            'rating' : RateitjsWidget,
        }
```

> blog/templates/widgets/rateitjs_widget.html

```python
{% raw %}
{% include "django/forms/widgets/input.html" %}

<div class="rateit" date-rateit-backingfld="#{{ widget.attrs.id }}"></div>
{% endraw %}
```