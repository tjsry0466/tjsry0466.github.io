---
layout: post
title: flutter widgets - indexedStack
category: flutter
tags: [flutter, widget, indexedStack]
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

> flutter 위젯의 indexedStack에 대해서 알아보겠습니다.

플러터 관련해서 첫번째 게시물을 업로드 하네요.
앞으로 제가 플러터를 공부하면서 익히는 여러 위젯들의 기본적인 설명과 사용법들을 정리해서 올리려고 합니다.
많은 관심 부탁드립니다 !

이번 게시글에서는 flutter의 위젯중에 indexedStack 에 대해서 알아보겠습니다.

### indexedStack이란?

indexedStack. 이름에서도 알수있듯이 index를 가진 Stack이라고 생각해볼수 있을것 같습니다.

여기서 index와 stack에 대해서 잘 모르시는 분들을 위해 간단하게 짚고 넘어가자면
`index`는 어떤 목록이 있을때 그 목록에 대해 `순서`를 붙여주는 용도로 사용합니다.
`stack`은 자료구조에서 한 쪽 끝에서만 자료를 넣거나 뺄 수 있는 선형 구조(LIFO - Last In First Out)으로 되어 있는데 flutter의 UI 측면에서는 한화면을 위아래로 겹처서 작성할수 있도록 도와준다고 이해 하면 될것 같습니다.

### indexedStack 사용법

indexedStack 위젯은 `index`와 `children`을 인자로 받게됩니다.

index는 children에 있는 화면 중에서 어느 index에 있는 위젯을 보여줄지 결정할수 있도록 int형 변수를 넣어주면 되고,
children 에는 Widget이 담긴 List를 작성해서 넘겨주면 됩니다.

아래 예시를 통해 보여드리겠습니다

```dart
int _selectedIndex = 0;
static List<Widget> _screens = <Widget>[
    Container(
        color: Colors.amberAccent,
    ),
    Container(
        color: Colors.blueAccent,
    ),
    Container(
        color: Colors.greenAccent,
    ),
    Container(
        color: Colors.deepPurpleAccent,
    ),
    Container(
        color: Colors.cyanAccent,
    ),
]

IndexedStack(
    index: _selectedIndex,
    children: _screens,
)
```

위와 같이 작성할수 있습니다.

### indexedStack을 사용할 때 주의할점
위와 같이 index와 children을 넘겨주게되면 _screens[_selectedIndex]와 같이 _screens의 위젯을 참조하게 되는데 _screens에 static 옵션을 안주게되면 매번 _screens의 위젯을 새로 생성하여 화면을 그려주게됩니다.
이를 방지하기 위해서는 꼭 widget List 변수에 static 옵션을 적용하여 작성하여야 합니다.

