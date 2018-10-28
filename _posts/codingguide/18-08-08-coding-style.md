---
layout: post
title: Python - 코딩 스타일
category: codingguide
tags: [Coding Guide, Python, Two Scoops of Django, PEP8]
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
> Two Scoops of Djnago책을 보고 내용을 정리했습니다.

## 읽기 쉬운 코드를 만들자!

코드의 특성은 한번 작성되면 여러번 읽힌다. 
일관된 스타일로 쓴 코드는 모든 이에게 유요한 정보가 된다.
코드가 가독성이 좋고 이해하기 쉬우면 이리저리 흩어진 퍼즐을 짜 맞추는 작업을 덜 해도 되므로 개발자의 뇌가 덜 피곤해지고, 프로젝트 규모에 상관없이 유지 관리가 쉬워지며 프로젝트를 개선하기 위한 작업 또한 훨씬 수월해진다.

- 축약적이거나 함축적인 변수명은 피한다
- 함수 인자의 이름들은 꼭 써준다.
- 클래스와 메서드를 문서화한다.
- 코드에 주석은 꼭 달도록 한다.
- 재사용 가능한 함수 또는 메서드 안에서 반복되는 코드들은 리팩토링을 해둔다.
- 함수와 메서드는 가능한 작은 크기를 유지한다. 어림잡아 스크롤 없이 읽을수 있는 길이가 적당하다

## PEP8

PEP8은 파이썬 공식 스타일 가이드 이다[Ref](http://www.pytho.org/dev/peps/pep-0008)

PEP8 에서는 다음과 같은 코딩 관례들을 다루고 있따
장고 프로젝트의 파이썬 파일들은 모두 이 PEP8의 관례를 따르도록 하자.


- 들여쓰기에는 스페이스를 네 칸 이용한다.
- 최상위 함수와 클래스 선언 사이를 구분 짓기 위해 두 줄을 띄운다.
- 클래스 안에서 메서드들을 나누기 위해 한줄을 띄운다.

## 79칼럼의 제약 [Ref](http://www.python.org/dev/peps/pep-0008/#maximum-line-length)
> "농담이 아니라 정말로 난 여전히 화면에 80칼럼 제약이 있는 콘솔에서 작업을 한다." - 베리 모리슨

-오픈 소스 프로젝트에서는 79칼럼 제약을 반드시 지킨다. 경험상 프로젝트의 기여자나 방문자들은 이 줄 길이 제약에 대해 끊임없이 불평할 것이다.
- 프라이빗 프로젝트에 한해서는 99칼럼까지 제약을 확장함으로써 요즘 나오는 모니터들의 장점을 좀 더 누릴 수 있다.

## About Import

PEP8은 Import를 할 때 다음과 같은 순서로 그룹을 지을 것을 제안하고 있다.

1. 표준라이브러리 임포트
2. 연관 외부 라이브러리 임포트
3. 로컬 애플리케이션 또는 라이브러리에 한정된 임포트

>우리는 장고 프로젝트를 할 때 다음과 같은 순서로 임포트 문들을 구성한다.

1. 표준 라이브러리 임포트
2. 코어 장고 임포트
3. 장고와 무관한 외부 앱 임포트
4. 프로젝트 앱 임포트

## 명시적 성격의 상대 임포트 이용하기

하드 코딩된 임포트 문들은 이식성 면에서나 재사용성 면에서 문제가 된다. 따라서 명시적 성격의 상대 임포트를 하는게 좋다.


> cones/views.py

```python
from __future__ import absolute_import
from django.views.generic import CreateView
# 'cones' 패키지 상대 임포트
from .models import WaffleCone
from .forms import WaffleConfForm
from core.views import FoodMixin
```

## import *는 피하자
작업하는 모듈의 이름공간에 추가로 로딩되거나 기존것 위에 덮여 로딩되는 일을 막기 위해 import *는 피해야 한다

## Django Coding Style[[guide]](http://2scoops.co/1.8-coding-style)

- URL 패턴 이름에는 대시(-)대신 밑줄(_)을 이용한다.