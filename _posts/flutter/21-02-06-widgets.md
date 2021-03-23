---
layout: post
title: flutter widgets - 여러가지 위젯들
category: flutter
tags: [flutter, widgets]
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

> flutter 여러가지 위젯들에 대해서 간단하게 한줄로 설명하겠습니다. 추후에 중요한 위젯들 같은 경우에는 개별적인 포스트로 다뤄보도록 하겠습니다.

- ClipOval -> 하위 아이템을 동그랗게 만들어주는 위젯
- IconButton -> Icon과 함께 버튼을 사용하고 싶은경우에 사용하는 위젯
- Spacer -> 위젯 목록 사이에 공간을 차지하게 해주는 위젯
- ReachText -> 여러 스타일의 텍스트 목록을 그려주는 위젯. 텍스트 스타일을 명시해야 한다.
- [Positioned](https://api.flutter.dev/flutter/widgets/Positioned-class.html) -> Stack 내부에 원하는 위치에 위젯을 배치할수 있는 위젯
- AnimatedIcon -> Icon을 특정 행위에 따라서 변경할수 있도록 도와주는 위젯 
- AnimatedSwicher -> 위젯간 변경을 가능하게 해주는 위젯
- FadeTransition -> 트랜지션 애니메이션을 할때 Fade효과를 줄수 있게 도와주는 위젯
- 모든 컨트롤러는 dispose()를 해주어야 메모리를 잡아먹지 않음 !!
- Scaffold의 ResizeToAvoidBottomInset -> 화면이 키보드에 의해 가려질때 위로 올려질수 있는지 없는지 !!! 기본값은 true
- Text 위젯과 같이 alignment가없는경우 Align위젯을 Wrapping할수 있음
- PageView - onPageChanged
- InkWell - gestureDetector 같은거 .. ?
- fittedBox - 차피할 공간을 정해주는 위젯?