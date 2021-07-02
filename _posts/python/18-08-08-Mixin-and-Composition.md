---
layout: post
title: Python 문법 - Mixin 그리고 Composition
category: Python
tags: [Django, Mixin, Composition, MultipleInheritance]
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

>askcompany Mixin에 대한 강의를 듣고 mixin과 coposition에 궁금증이 생겨 정리했습니다.

## Mixin

- 클래스에서 제공해야 하는 추가적인 메서드만 정의하는 작은 클래스
- 인스턴스 속성(attribue)를 정의하지 않으며 __init__생성자를 호출하도록 요구하지 않습니다
- 한 클래스에 대해 많은 선택 기능을 제공할때 사용함
- 많은 다른 클래스에서 하나의 특정 기능을 사용하려고 할때 사용함
- 클래스에서 상속받은 Mixin의 메소드를 포함하고 있는 것처럼 행동하는것이 믹스인의 핵심
- Mixin을 위한 특별한 키워드는 없으며, 단지 다중상속을 통해서 만들기 때문에 이 과정에서 문제가 생길 소지가 있음
- 스칼라의 경우 stackable traits pattern이라고 동일한 메소드가 있을 경우 순서대로 하나씩 실행되지만 파이썬의 경우 덮어 써버린다.

```python
class Mixin1(object):
    def test(self):
        print "Mixin1"

class Mixin2(object):
    def test(self):
        print "Mixin2"

class MyClass(BaseClass, Mixin1, Mixin2):
    pass
```

파이썬 에서는 MRO에 따라 Mixin2 -> Mixin1 -> BaseClass 순으로 계승이 되는대 위 처럼 메소드 명이 같을 경우 가장 하위 클래스가 적용되어 overriding 된다

```python
>>> obj = MyClass()
>>> obj.test()
Mixin1
```

## Composition

- 포함
- 다른 클래스의 일부 기능을 그대로 이용하고 싶으나, 전체 기능 상속은 피하고 싶을 때 사용
Composition 또는 Aggregation 이라고도 한다.
- 상속관계가 복잡할 경우, 코드 이해가 어려운 경우가 많음
- EX) B 에서 A의 객체의 메소드만 가져와서 사용

```python
class Calc:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
    def add(self):
        return self.x + self.y
 
    def subtract(self):
        return self.x - self.y
 
class Calc2:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    
    def add(self):
        return self.x + self.y
 
    def multiply(self):
        return self.x * self.y
```

- Calc 클래스에서 Calc2의 multiply() 메서드를 활용하고 싶지만, Calc2 전체를 상속 받고 싶지는 않음

```python
class Calc:
    def __init__(self, x, y):
        self.x = x
        self.y = y
        self.calc2 = Calc2(x, y) # 해당 클래스의 객체를 명시적으로 가져옴
    
    def add(self):
        return self.x + self.y
 
    def subtract(self):
        return self.x - self.y
    
    def multiply(self):
        return self.calc2.multiply() # 해당 클래스의 객체에 있는 메서드를 명시적으로 활용함
```

## Mixin 과 Compositon의 차이점

- mixin은 IS-A를 의미합니다.
    - B IS A (B는 A이다)
- Composition은 HAS-A를 의미합니다.
    - B HAS A (B는 A를 가지고 있다)