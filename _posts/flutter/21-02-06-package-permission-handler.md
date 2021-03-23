---
layout: post
title: flutter package - permission handler
category: flutter
tags: [flutter, widget, package, library, permission, handler]
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

> flutter cookbook의 [build a form with a validation](https://flutter.dev/docs/cookbook/forms/validation)에 대해서 번역하여 정리했습니다.

### 콘텐츠 목록
1. GlobalKey와 함께 Form 생성
2. 유효성 검사 로직과 함께 TextFormField 추가
3. 유효성 검사를 위한 버튼 생성 및 폼 제출
    - 이것은 어떻게 동작하는가?
4. 인터랙티브한 예제

### 서론

어플리케이션에서는 종종 사용자가 텍스트 필드에 정보를 입력해야 합니다.
예를들어, 사용자가 이메일 주소와 비밀번호 조합으로 로그인하도록 요구할 수 있습니다.
앱을 안전하고 사용하기 쉽게 만드려면 사용자가 제공한 정보가 유효한지 확인해야 합니다.
사용자가 폼을 올바르게 작성했다면 정보를 처리하고 잘못된 정보를 제출했다면 오류메시지를 나타내 줄수 있습니다.

이번 포스트에서는 다음 단계를 통해서 단일 텍스트 필드가 있는 폼에 유효성 검사를 추가하는 방법을 알아보겠습니다.

### 1. GlobalKey와 함께 폼 생성

첫번째로, 폼을 생성합니다.
폼 위젯은 그룹화 및 여러 폼 필드를 검증하기 위한 컨테이너 역할을 합니다.
폼을 만들 때 GlobalKey를 통해서 교유하게 식별하고 이후 단계에서 유효성 검사를 할 수 있습니다.

```dart
// 폼 위젯을 정의합니다.
class MyCustomForm extends StatefulWidget {
  @override
  MyCustomFormState createState() {
    return MyCustomFormState();
  }
}

// 해당 State 클래스를 정의합니다.
// 이 클래스는 양식과 관련된 데이터를 보유합니다.
class MyCustomFormState extends State<MyCustomForm> {
  // 폼 위젯을 고유하게 식별하는 GlobalKey를 생성합니다.
  // 폼의 유효성을 검사할수 있습니다.
  //
  // GlobalKey<FormState> 입니다.
  // GlobalKey<MyCustomFormState> 가 아닙니다.
  final _formKey = GlobalKey<FormState>();

  @override
  Widget build(BuildContext context) {
    // 위에서 만든 _formKey 를 이용하여 위젯을 만듭니다,
    return Form(
      key: _formKey,
      child: Column(
        children: <Widget>[
              // 여기에 ElevatiedButton과 TextFormFields를 추가합니다.
        ]
     )
    );
  }
}

> form을 만들때 추천되는 방법은 GlobalKey를 사용하는 것입니다. 그러나, 더 복잡한 위젯 트리를 가지고 있다면 Form.of() 메소드를 사용하여 중첩된 위젯 내의 폼에 접근할 수 있습니다.

### 2. 유효성 검사 로직과 함께 TextFormField 추가

- TextFormField 위젯은 머티리얼 디자인 텍스트 필드를 렌더링하고 유효성 검사 오류가 발생할 때 표시 할 수 있습니다.

- TextFromField에 validator 함수를 제공하여 유효성 검사를 할수 있습니다.
- 만약 유저의 입력이 올바르지 않다면 String이 포함된 에러 메시지를 반환합니다.
- 에러가 없다면 validator은 null을 반환해야합니다.

아래 예제 에서는 TextFormField가 비어있지 않은지 확인하는 validator를 만듭니다. 비어있다면 에러 메시지를 반환합니다.

```dart
TextFormField(
  // The validator receives the text that the user has entered.
  validator: (value) {
    if (value.isEmpty) {
      return 'Please enter some text';
    }
    return null;
  },
);


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

### 3. 폼의 유효성을 검사하고 제출하는 버튼를 만듭니다.

```dart
ElevatedButton(
  onPressed: () {
    // Validate returns true if the form is valid, otherwise false.
    if (_formKey.currentState.validate()) {
      // If the form is valid, display a snackbar. In the real world,
      // you'd often call a server or save the information in a database.

      Scaffold
          .of(context)
          .showSnackBar(SnackBar(content: Text('Processing Data')));
    }
  },
  child: Text('Submit'),
);
```

### 이것은 어떻게 작동하나요?
- 폼의 유효성을 검사하려면 1단계에서 만든 _formKey를 사용하십시오.
- _formKey.currentState()메소드를 사용하여 Form의 FormState에 접근 할 수 있습니다.

- FormState 클래스는 validate  메소드를 포함합니다. 
- validate() 메소드가 호출될때 폼의 각 텍스트 필드에 대해서 validator()가 실행 됩니다.
- 만약 모든것이 올바르다면 validate()는 true를 리턴합니다.
- 만약 에러가 있는 필드가 있다면 에러 메시지를 포함하여 form을 재 생성하고 false를 반환합니다.