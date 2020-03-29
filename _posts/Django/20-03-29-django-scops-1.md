---
layout: post
title: Django [Two Scops] - 코딩 스타일
category: Django
tags: [Django, Coding-style, Two-Scops-of-django]
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

>Two Scops of Django 의 코딩 스타일을 읽고 정리한 글입니다.

# 읽기 쉬운 코드를 만드는 것이 왜 중요한가?
- 일관된 스타일로 쓴 코드는 모든 이에게 유용한 정보가 된다.
- 전에 작업 했던 것들을 쉽고 빠르게 다시 이해할 수 있게 도와준다.

## 코드 작성시에 생각해야 하는것

코드는 최대한 읽기 쉽게 만들어야 다음과 같은 원칙을 지켜야 한다.
- 축약적이거나 함축적인 변수명은 피한다.
- 함수 인자의 이름들은 꼭 써준다.
- 클래스와 메서드를 **문서화**한다.
- 코드에 **주석**은 꼭 달도록 한다.
- 재사용 가능한 함수 또는 메서드 안에서 반복되는 코드들은 **리팩토링**을 해둔다.
- 함수와 메서드는 **가능한 한 작은 크기**를 유지한다.

# PEP8

PEP8은 파이썬 공식 스타일 가이드이다.

## PEP8에서는 다음과 같은 코딩 관례들을 다루고 있다.
- 들여쓰기에는 **스페이스 네칸**을 이용한다.
- 최상위 함수와 클래스 선언 사이를 구분 짓기 위해 두 줄을 띄운다.
- 클래스 안에서 메서드들을 나누기 위해 한줄을 뛰운다.
- 오픈소스에서는 79컬럼 제약을 두고 프로그램을 작성하기도 한다.

## import
pip8은 임포트(import)를 할 때 다음과 같은 순서로 그룹을 지을 것을 제안하고 있다.

1. 표준 라이브러리 임포트
2. 연관 외부 라이브러리 임포트
3. 로컬 어플리케이션 또는 라이브러리에 한정된 임포트

장고 프로젝트를 할 때에는 다음과 같은 순서로 임포트 문들을 구성한다.

```python
# 표준 라이브러리 임포트
from math import sqrt
# 코어 장고 임포트
from django.db import models
# 서드 파티 앱 임포트
from django_extensions.db.models improt TimeStampedModel
# 프로젝트 앱 임포트
from splits.models import BananaSplit
```

1. 표준 라이브러리 임포트
2. 코어 장고 임포트
3. 장고와 무관한 외부 앱 임포트
4. 프로젝트 앱 임포트

## 명시적 성격의 상대 임포트 이용하기
- 재구성을 손쉽게 할 수 있도록 구성하는 것은 매우 중요하다.
- 파이썬에서는 명시적 성격의 상대 임포트를 통해 모듈의 패키지를 하드코딩하거나 구조적으로 종속된 모듈을 어렵게 분리해야 하는 경우들을 피해 갈 수 있다.
- 하드코딩된 임포트 문들은 이식성 면에서나 재사용성 면에서 문제가 된다.

```python
# cones/views.py
from django.views.generic import CreateView
# 'cones' 패키지 상대 임포트
from .modes import WaffleCone
from .forms import WaffleConeForm
```

## improt *는 피하자
- 다른 파이썬 모듈의 이름공간들이 현재 우리가 작업하는 모듈의 이름공간에 추가로 로딩되거나 기존 것 위에 덮여 로딩 되는 일을 예방할 수 있다.
- 다음과 같이 중복된 이름의 파이썬 모듈이 있는 경우 *을 통해 전체 임포트를 할경우엔 원치 않게 다른 모듈이 로드될 수 있다.
```python
from django.forms import CharField
from django.db.models import CharField
```


