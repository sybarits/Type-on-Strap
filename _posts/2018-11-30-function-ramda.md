---
layout: post
title: 함수와 함수형 인터페이스 
tags: [function, ramda]
excerpt_separator: <!--more-->
---


* 람다 대수에서 함수의 표현  
    함수가 반드시 이름을 가질 필요 없음  
    항등의 표현을 등호 대신 화살표로 할 수 있음  
    위의 내용을 통해 자바에서 람다 표현식 문법이 나옴  

```
String mergeString(str1,str2) {
    return str1+str2;
}
```
위의 내용을 아래 처럼 쓸 수 있음
```
(str1, str2) -> { return (str1 + str2)};
```
더 줄이면 아래처럼 쓸 수 있음
```
(str1, str2) -> str1 + str2;
```

자바에서 람다 표현식은 구현해야될 추상 메서드가 1개인 인터페이스를 구현한 것  
BinaryOperator는 apply라는 추상메서드 하나를 가진 인터페이스  
apply는 동일한 특정 타입의 인자 두개를 받아 같은 타입으로 리턴하는 추상메서드  
  
BinaryOperator 인터페이스에서 apply 추상메서드를 정의 하면서 인스턴스를 만드는 방법  

```java
BinaryOperator<String> b = (str1, str2) -> str1 + str2;
```
정의 된 apply 메서드 사용 방법
```
String aaa = b.apply("Hello, ", "World");
```


* 순수 함수  
    외부의 상태를 변경 하지 않으며, 외부의 상태를 참조 하지 않음  
    동인한 인자에 대해 항상 같은 값을 리턴

* 일급 함수  
    함수를 인자처럼 다룰 수 있는 것  

* 일급 객체  
    변수나 데이터 구조안에 담을 수 있다.  
    파라미터로 전달 할 수 있다.  
    반환값(return value)으로 사용할 수 있다.  
    할당에 사용된 이름과 관계없이 고유한 구별이 가능하다.  
    동적으로 프로퍼티 할당이 가능하다.  

* 함수와 메서드의 차이  
    자바에서 메서드는 객체에 종속 되기 때문에 순수 함수의 성격을 가지지 못할 수 있음  
  
  
람다 표현식을 쓰면서 자바의 메서드는 마치 일급 함수 처럼 인터페이스를 이용해 인자로 넘기는 것이 가능해짐
  

더 많은 내용과 상세한 코드는 아래 출처에서 보기
  
출처: http://multifrontgarden.tistory.com/124?category=471239  
http://multifrontgarden.tistory.com/125?category=471239  
http://palpit.tistory.com/673
-끝-
