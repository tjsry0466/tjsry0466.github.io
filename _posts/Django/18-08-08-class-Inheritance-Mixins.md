---
layout: post
title: Django - 클래스 상속/Mixins을 통해 CBV 조합하기
category: Django
tags: [Django, Python, 클래스, 상속, CBV, Inheritance, Decorators, Mixins]
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

## Mixins을 통해 여러 CBV를 한 번에 조합

- CBV는 재사용성에 초점을 둔 뷰
    - 기존 패턴을 사용할 경우에는 CBV가 단순하지만,
    - 새로운 루틴의 뷰를 구현하실 때에는 FBV를 사용하시는 것이 구현이 더 단순해지실 경우가 많습니다.
- 파이썬은 다중상속을 지원합니다.
- 동일 멤버함수가 여러 CBV에 걸쳐서 구현되었을 경우, MRO(Method Resolution Order) 순서대로 호출됩니다.

## 예시(FBV)

```python
def home_page(request): # 요청이 들어왔을 때
    # 템플릿에서 사용할 인자를 준비하고
    context = {}
    # 템플릿 파일을 선택한 다음
    template_name = 'home.html'
    # HttpResponse응답을 생성
    return render(request, template_name, context)
```

## 예시(CBV)

```python
class TemplateResponseMixin:
    def render_to_response(self, context, **response_kwargs):
        'HttpResponse 응답 생성'
        
    def get_template_names(self):
        '사용할 템플릿 파일 이름 정하기'
  
  
class ContextMixin:
    def get_context_data(self, **kwargs):
        '템플릿에서 아용할 변수목록 만들기'
        
class View:
    @classmethod
    def as_view(cls, **initkwargs):
        '뷰 함수를 생성'
        
    def dispatch(self, request, *args, (**kwargs):
        'Http 요청이 들어오면 호출되어, 요청을 처리'
        
        
class TemplateView(TemplateResponseMixin, ContextMixin, View):
    def get(self, request, *args, **kwargs):
        context = self.get_context_data(**kwargs) # 부모 CBV의 Context 데이터를 받아와서
        return self.render_toresponse(context) #HttpResponse 응답 생성
        
class HomePageView(TemplateView):
    template_name = 'home.html' # get_template_names 함수에서 참조하는 값
    
#as_view 호출을 통해 뷰 함수 생성

home_page = HomePageView.as_view()
```

## 장고 기본 패키지 내의 CBV
>django.views.generic.base

- ContextMixin
- TemplateResponseMixin
- TemplateView(TemplateResponseMixin,ContextMixin, View)

```python
from django.views.generic.base import TemplateView # __init__ 에 정의가 되어있어서 base를 제거하고 import해도 됌

class HomePageView(TemplateView):
    template_name = 'home.html'
    
    def get_get_context_data(self, **kwargs):
        context = super().get_context_data(**kwargs)
        context['latest_articles'] = Article.objects.all()[:5]
        return context
```
<br>
>django.views.generic.dates

- YearMixin, MonthMixin, DayMixin, WeekMixin
- BaseDateListView(MultipleObjectMixin, DateMixin, View)
- ArchivelndexView(MultipleObjectTemplateResponseMixin, BaseArchiveIndexView) #쓸만함
- BaseYearArchiveView(YearMixin, BaseDateListView)
- YearArchiveView(MultipleObjectTemplateResponseMixin, BaseYearArchiveView) #쓸만함

### 예제 소스

```python
from django.views.generic.dates import ArchivelndexView, YearArchiveView, MonthArchiveView

class PostArchiveIndexView(ArchivelndexView):
    model = Article
    date-field = 'pub_date'
    
class ArticleYearArchiveView(YearArchiveView):
    queryset = Article.objects.all()
    date_field = 'pub_date'
    
class ArticleMonthArchiveView(MonthArchiveView):
    queryset = Article.objects.all()
    date_field = "pub_date"
    allow_future = True
```

- BaseMonthArchiveView(YearMixin, MonthMixin, BaseDateListView)
- MonthArchiveView(MultipleObjectTemplateResponseMixin, BaseMonthArchiveView)
- BaseWeekArchiveView(YearMixin, WeekMixin, BaseDateListView)
- WeekarchiveView(MultipleObjectTemplateResponseMixin, BaseWeekArchiveView)
- BaseDayArchiveView(YearMixin, MonthMixin, DayMixin, BaseDateListView)
- DayArchiveView(MultipleObjectTemplateResponseMixin, BaseDayArchiveView)
- TodayArchiveView(MultipleObjectTemplateResponseMixin, BaseTodayArchiveView)
- BaseDateDetailView(YearMixin, MonthMixin, DayMixin, DateMixin, BaseDetailView)
- DateDetailView(SingleObjectTemplateResponseMixin, BaseDateDetailView)

<br>
> django.views.generic.detail

-SingleObjectMixin(ContextMixin)
- BaseDateDetailView(SingleObjectMixin, View)
- SingleObjectTemplateResponseMixin(TemplateResponseMixin)
- DetailView(SingleObjectTemplateResponseMixin, BaseDateListView)

<br>
> django.views.generic.edit

- FormMixin(ContextMixin)
- ModelFormMixin(FormMixin, SingleObjectMixin)
- BaseFormView(FormMixin, ProcessForm View)
- FormView(TemplateResponseMixin, BaseFormView)
- BaseCreateView(ModelFormMixin, ProcessFormView)
- CreateView(SingleObjectTemplateResponseMixin, BaseCreateView)
- BaseUpdateView(ModelFormMixin, ProcessFormView)
- UpdateView(SingleObjectTemplateResponseMixin, BaseUpdateView)
- DeletionMixin
- BaseDeleteView(DeletionMixin, BaseDeleteView)
- DeleteView(SingleObjectTemplateResponseMixin, BaseDetailView)

<br>
>django.views.generic.list

- MultipleObjectMixin(ContextMixin)
- BaseListView(MultipleObjectMixin, View)
- MultipleObjectTemplateResponseMixin(TemplateResponseMixin)
- ListView(MultipleObjectTemplateResponseMixin, BaseListView)

<br>
```python
from django.views.generic.list import ListView


class ArticleListView(ListView):
    model = Article
```

## django-braces
### 써드파티 Class Based View

## Access Mixins

- LoginRequiredMixin: 로그아웃 시에 로그인 URL로 이동
    - 장고 1.9부터 django.contrib.auth.mixins.LoginRequiredMixin에서 동일 기능을 제공
- PermissionRequiredMixin(AccessMixin)
- MultiplePermissionsRequiredMixin(PermissionRequiredMixin)
- GroupRequiredMixin(AccessMixin)
UserPassesTestMixin(AccessMixin)
- SuperuserRequiredMixin(AccessMixin)
AnonymousRequredMixin(object)
- StaffuserRequiredMixin(AccessMixin)
    - staff제한
- SSLRequiredMixin(object)
    - 현재 페이지의 https URL로 이동 응답(301).raise_exception=True 설정으로 404 응답도 가능
- RecentLoginRequiredMixin(LoginRequiredMixin)
    -  지정 시간(디폴트 30분)내 로그인 여부 체그
    - 시간을 넘겼다면, 로그아웃/로그인 요청
    
> Form Mixins

- CsrfExemptMixin
- UserFormKwargsMixin
- UserKwargModelFormMixin
- SuccessURLRedirectListMixin
- FormValidMessageMixin
- FormInvalidMessageMixin
- FormMessagesMixin

> Other Mixins

- SetHeadlineMixin
- StaticContextMixin
- SelectRelatedMixin
- PerfetchRelatedMixin
- JSONResponseMixin
- JsonRequestResponseMixin
- AjaxResponseMixin
- OrderableListMixin
- CanonicaSlugDetailMixin
- MessageMixin
- ALLVerbsMixin
- HeaderMixin
