---
layout: post
title: Django - 위젯 만들기 (자동완성 Select 위젯)
category: Django
tags: [Django, 자동완성, 위젯]
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

## 기본 구현
> blog/forms.py

```python
from django.import forms
from django.urls import reverse_lazy
from .models import Post
from .widgets import AutoCompleteSelect, RateitjsWidget

class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = '__all__'
        widgets = {
            'country': AutoCompleteSelect,
            'rating' : RateitjsWidget,
        }
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
        
    def build_attrs(self, *args, **kwargs):
    context = super().build_attrs(*args, **kwargs):
    context['style'] = 'min-width: 202px;
    return context
```

> blog/templates/widgets/autocomplete_select.html

```python
{% raw %}
{% include "django/forms/widgets/select.html" %}

<script>
$(function() {
    var options = {minimumInputLenghth: 1};
    $('#{{ widget.attrs.id }}').select2(options);
});
</script>
{% endraw %}
```

