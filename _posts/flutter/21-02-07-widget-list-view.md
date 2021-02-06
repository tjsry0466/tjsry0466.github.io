---
layout: post
title: flutter widgets - listview
category: flutter
tags: [flutter, widget, listview]
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

> flutter 위젯의 [listview](https://api.flutter.dev/flutter/widgets/ListView-class.html)에 대해서 알아보겠습니다.

### listview이란?

listview는 어떤한 위젯들을 일렬로 나열하고 스크롤을 가능하게 해주는 위젯입니다.

### listview 사용법

listview 위젯은 4가지 방법으로 사용할 수 있습니다.
1. 기본 생성자 List<Widget> 사용
   - 목록의 수가 적은 경우에 사용

```dart
ListView(
  padding: const EdgeInsets.all(8),
  children: <Widget>[
    Container(
      height: 50,
      color: Colors.amber[600],
      child: const Center(child: Text('Entry A')),
    ),
    Container(
      height: 50,
      color: Colors.amber[500],
      child: const Center(child: Text('Entry B')),
    ),
    Container(
      height: 50,
      color: Colors.amber[100],
      child: const Center(child: Text('Entry C')),
    ),
  ],
)
```
2. ListView.builder
   - IndexWidgetBuilder에 의해 성성되며 실제로 표시되는 위젯에 대해서만 빌더가 호출되기 때문에 많은 수의 위젯목록이 있는 경우에 적합합니다.

```dart
final List<String> entries = <String>['A', 'B', 'C'];
final List<int> colorCodes = <int>[600, 500, 100];

ListView.builder(
  padding: const EdgeInsets.all(8),
  itemCount: entries.length,
  itemBuilder: (BuildContext context, int index) {
    return Container(
      height: 50,
      color: Colors.amber[colorCodes[index]],
      child: Center(child: Text('Entry ${entries[index]}')),
    );
  }
);
```
3. ListView.separated
   - IndexWidgetBuilder에 itemBuilder에 필요에 따라 위젯이 생성되며 생성된 목록 사이에 구분선이 나타납니다.
   - ListView.separated는 고정된 수의 자식이 있는 목록보기에 적합합니다.

```dart
final List<String> entries = <String>['A', 'B', 'C'];
final List<int> colorCodes = <int>[600, 500, 100];

ListView.separated(
  padding: const EdgeInsets.all(8),
  itemCount: entries.length,
  itemBuilder: (BuildContext context, int index) {
    return Container(
      height: 50,
      color: Colors.amber[colorCodes[index]],
      child: Center(child: Text('Entry ${entries[index]}')),
    );
  },
  separatorBuilder: (BuildContext context, int index) => const Divider(),
);
```

1. ListView.custom
   - SilverChildDelegate로 추가적으로 사용자 정의 기능을 제공합니다.
   - 예를들어 SilverChildDelegate는 실제로 표시되지 않는 자식의 크기를 추정하는 데 사용되는 알고리즘을 제어할 수 있습니다.

```dart
CustomScrollView(
  shrinkWrap: true,
  slivers: <Widget>[
    SliverPadding(
      padding: const EdgeInsets.all(20.0),
      sliver: SliverList(
        delegate: SliverChildListDelegate(
          <Widget>[
            const Text("I'm dedicating every day to you"),
            const Text('Domestic life was never quite my style'),
            const Text('When you smile, you knock me out, I fall apart'),
            const Text('And I thought I was so smart'),
          ],
        ),
      ),
    ),
  ],
)
```