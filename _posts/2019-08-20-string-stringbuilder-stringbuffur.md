---
layout: post
title: Java String, StringBuffer, StringBuilder
tags: [string, stringbuffer, stringbuilder, java ]
---

Java에서 문자열을 이용해 이것 저것을 할 경우가 있다. 성능을 내려면 String가지고는 부족하다. 그렇다.... 부족하다.

## String
* String은 immutable(변경불가)이다.
* String에 문자열을 더하거나 편집 할 경우 그 결과가 새로운 메모리 공간에 할당된다.
* 기존 문자열은 가비지 컬렉터의 대상이다. GG
* 조회는 빠르다.

## StringBuffer
* StringBuffer는 mutable(변경가능)이다.
* 문자열을 편집할 경우 메모리 영역을 늘려 문자열을 저장한다.
* thread safe 하다.

## StringBuilder
* StringBuilder는 StringBuffer와 유사하다.
* 단, thread safe 하지 않다.
* StringBuffer 보다 연산이 좀 더 빠르다.


## 기타
* 문자열을 연산할 때 JDK 1.5 이후에서는 String을 StringBuilder로 바꾸어 준다고하길래 마음껏 String을 사용하였으나....
* 아래 출처를 보면 알 수 있듯이 반복문 내부에서 StringBuilder 객체를 계속 만들기 때문에 반복문 안에서는 절대로 String을 쓰지 말아야 한다!!


출처: https://jeong-pro.tistory.com/85  
https://gist.github.com/benelog/b81b4434fb8f2220cd0e900be1634753
