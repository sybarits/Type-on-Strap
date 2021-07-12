---
layout: post
title: 불편추정량
tags: [sample, unbiased, estimate, statistics]
---

  불편추정량(unbiased estimate)에 대해 알아본다.
  몬테카를로 학습에 대해 알아보던 중 이론적 배경으로 불편추정량에 대한 이야기가 나오는데 불편추정량의 이론적 배경을 알지 못하니 이를 알아보고자 한다.

## 불편추정량
추정량에 편의가 없다는 것을 의미한다. 이 의미에 대해 예시를 하나 들고 차근차근 알아보자. 인도에 거주중인 사람들의 소득수준 대해 통계학적으로 알고싶은 상황으 예로 들어 보자.
### 모집단
인도에 거주중인 사람들 (소득수준이라는 특징을 가진)
### 모수
인도에 거주중인 사람들 소득수준을 대표할 수 있는 값(주로 평균과 분산을 지칭, 모평균, 모분산이라고도 한다.)
### 표본
모집단의 부분집합으로 모집단을 전수조사하는 것은 실용적이지 않은 규모이거나 불가능할 때가 많다. 표본은 다룰 수 있을 만한 크기의 부분집합을 나타낸다.
### 추정량
모집단의 모수를 추정하기 위한 값이다. 이 값은 표본으로 부터 구한다. 표본으로 부터 구한 평균을 표본평균, 분산을 표본분산이라한다. 이 표본평균은 모집단의 모평균을 추정하기 위한 추정량이고, 모분산을 추정하기위해 표본분산을 추정량으로 사용한다.
### 편의
E(x’) - E(x) 로 나타낼 수 있다. x’은 표본집단 x 는 모집단이다.
### 기대값
위의 식에서 E를 나타낸다. Expectation의 약자. 확률론에서, 확률 변수의 기댓값은 각 사건이 벌어졌을 때의 이득과 그 사건이 벌어질 확률을 곱한 것을 전체 사건에 대해 합한 값이다. 이건은 어떤 확률적 사건에 대한 평균의 의미로 생각할 수 있다.

## 결론
불편추정량이란 모든 경로를 구하는 것은 현실적으로 어려우니 표본을 MC(Monte Carlo) 혹은 TD(temporal difference) 방법을 이용해 구하고 기대값을 구하겠다는 것이다. 표본을 충분히 많이 추출 할 수록 이 기대값은 결국 모집단의 평균 즉, 모든 경로의 리턴을 평균낸 값과 같아진다는 의미로 볼 수 있겠다.
  
  
출처: https://datacookbook.kr/59
  https://wiserloner.tistory.com/1096?category=850046
  https://wiserloner.tistory.com/1097
  https://ko.wikipedia.org/wiki/표본
  https://m.blog.naver.com/PostView.nhn?blogId=yunjh7024&logNo=220821500772&proxyReferer=https:%2F%2Fwww.google.com%2F