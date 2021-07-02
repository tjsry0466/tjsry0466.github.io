---
layout: post
title: Django - Static Files
category: Django
tags: [Django, Static, serving]
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

> askcompany 장고 프론트엔드 EP 04 - Static Files in Django강의를 보고 정리했습니다.

## 참고 VOD 요약
> [장고 기본편] "Static Files - CSS/JavaScript 파일을 어떻게 관리해야 할까요?

- 장고는 One Project Multi App 구조
- 한 App을 위한 static 파일을 app/static/app경로에 두세요.
- 프로젝트 전반적으로 사용되는 static 파일을 settings.STATICFILES_DIRS에서 참조하는 경로에 두세요.

> myproject/settings.py

```python
STATIC_URL = '/static/' # Static 파일 요청에 대한 URL Prefix
STICFILES_DIRS = [
os.path.join(BASE_DIR, 'myproject', 'static'),
]
```

## 장고에서의 STATIC 파일 서빙
### 장고의 개발서버에서는 정적파일 서빙기능을 지원

> myproject/static/main.css 
- http://localhost:8000/static/main.css 경로로 접근 가능

## 브라우져 캐시
브라우저 캐시 기간을 설정해 주면 그 기간 동안은 웹브라우저가 해당 파일을 다시 다운받지 않고 캐싱된 내용을 사용하기 때문에 트래픽이 줄어들고, 속도도 발라집니다.

- Expires 헤더: 만료일시를 지정
    - Expires: Wed, 21 Oct 2015 07:28:00 GMT
    - 응답 내에 "max-age" 혹은 "s-max-age" directive를 지닌 Cache-Control 헤더가 존재할 경우, Expires 헤더는 무시
-Cache-Control

## 클라이언트측 캐싱과 빠른 업데이트를 할려면
### 리소스의 URLㅇㄹ 변경하고 콘텐츠가 변경될 때마다 사용자가 새 응답을 다운로드하도록 하면 됩니다.

1 GET인자 붙이기: 실제 파일명은 ㅁ\변경하지 않으면서, 브라우저가 인지하는 URL만 변경
- <strong>개발 시에 유용</strong>
- 버전을 숫자로 붙이거나
    - http://localhost:8000/static/main.css?v=1
- 버전을 날짜로 붙이거나
    - http://localhost:8000/static/main.css?v=20180618
- 더미로 현재시각의 timestamp을 붙입니다.
    - http://localhost:8000/static/main.css?_=1503808011

2   파일명 변경하기
- <strong>서비스 배포 시에 유용</strong>

## 커스텀 템플릿 태그를 통해 STATIC URL에 더미 GET인자 붙이기

>myapp/templatetags/static_tags.py

```python 
from django import template
from django.conf import settings
from django.templatetags import StaticNode

register = template.Library()

class VersioningStaticNode(StaticNode):
    def url(self, context):
        url = super().url(context)
        if settings.DEBUG:
            t= str(int(time.time()))
            if '?' not in url:
                url += '?_=' + t
            else:
                url += '&_=' + t
        return url

@register.tag('static_t')
def static.t(parser, token)
    return VersioningStaticNode(parser, token)
    
```

## 다양한 STATIC 리소스

- 직접 생성/등록한 CSS/JavaScript/Image 파일들
- 외부 CSS/JavaScript 라이브러리
    - CDN(Contents Delivery Network) 배포판 활용
    - 직접 다운로드&서빙
    - 자바스크립트 팩키지 관리자를 활용하여 다운로드&서빙
    
## CDN 배포판 활용

- 유명 라이브러리일 경우, 대개 CDN 배포판을 제공
- 개발 시에 빠른 적용을 위해서는 편리
- 안정적인 실서비스 제공을 위해서는 다운로드&서빙을 추천
    - 정적 파일 서빙을 "관리할 수 없는 외부 서비스"에 의존할 경우, 특정 유저의 해외망 접속이 원활하지 않거나, 해당 서비스 장애일 경우, 의도치 않게 서비스 이용에 차질이 발생하게 됩니다.
    
## 직접 다운로드&서빙

- 프로젝트 전반적으로 사용될 파일들이므로, filesystem static finder에서 접근하는 경로에 넣어두고, 버전관리 대상에도 추가
    - "프로젝트/static/" 경로
    
