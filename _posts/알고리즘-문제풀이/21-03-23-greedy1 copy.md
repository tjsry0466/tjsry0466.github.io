---
layout: post
title: 01. 그리디 알고리즘 - 큰 수의 법칙
category: 알고리즘-문제풀이
tags: [문제풀이, 알고리즘, 그리디 알고리즘]
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

> 이것이 코딩테스트다 책에 나오는 알고리즘 문제를 풀고 해결방법을 정리했습니다.

# 문제
- 당신은 음식점의 계산을 도와주는 점원이다. 카운터에는 거스름돈으로 사용할 500원, 100원, 50원, 10원짜리 동전이 무한히 존재한다고 가정한다. 손님에게 거슬러 줘야 할 돈이 N원 일 때 거슬러 줘야 할 동전의 최소 개수를 구하라. 단 거슬러 줘야 할 돈은 항상 10의 배수이다.

# 내가 생각해본 풀이 방법

거슬러 줘야할 `최소` 동전의 갯수를 구하려면 큰 금액의 동전을 많이 거슬러 줘야할 것이다.
그러기 위해서는 금액이 나누어 떨어질 때까지 큰금액으로 나누어서 몫을 구하면 큰 금액 순서대로 답이 나올것 같다.

# 내가 짜본 풀이  소스

```
receivedMoney = int(input())

def returnMoney(receivedMoney):
    result = []
    
    for i in [500,100,50,10]:
        mok = receivedMoney // i
        receivedMoney -= mok * i
        result.append(mok)
    return result


result = returnMoney(receivedMoney)
result
```

# 정답 소스

```
n = 1260
count = 0

coin_types = [500, 100, 50, 10]
for coin in coin_types:
    count +=n
    n %= coin

print(count)
```

# 느낀점
- 동전의 갯수를 구하는 문제인데 각각 몇개 거슬러줘야 하는지 파악하는 소스를 짜서 훨씬 복잡해 진것 같다.
- 간단한 알고리즘 같지만 쉽게 풀어내진 못했던것 같다.


