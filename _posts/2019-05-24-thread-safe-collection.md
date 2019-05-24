---
layout: post
title: Thread-safe collection
tags: [java, thread, safe, collection]
excerpt_separator: <!--more-->
---

자바의 컬렌션은 대부분 싱글 스레드 환경에서 사용할 수 있도록 설계 되었다. - ArrayList, HashSet, HashMap

멀티 스레드 환경에서 사용해도 안전한 컬렉션은 Vector, Hashtable이다.

 ArrayList, HashSet, HashMap이 컬렉션을 멀티 스레드 환경에서 안전하게 사용하기 위해서 아래와 같이 쓴다.
 ```java
 List list = Collections.synchronizedList(new ArrayList<String>());
 Map map = Collections.synchronizedList(new HashMap<String,String>());
 Set set = Collections.synchronizedList(new HashSet())
```
위와 같은 collection을 사용하면 상황에 따라 ConcurrentModificationException이 발생하기도 한다.

java.util.concurrent 패키지를 사용해도 된다.
concurrent 패키지와 멀티스레드 환경 코딩은 다음기회에!
  
출처: https://cornswrold.tistory.com/209  
https://whiteship.tistory.com/773
