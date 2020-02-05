---
layout: post
title: Java Singleton
tags: [singleton, java]
---



singleton  
인스턴스가 사용 될때 마다 새로운 객체를 만들지않는다. 하나의 인스턴스만 사용한다.  
  
다음 몇가지 singleton 예제가 있다.

```java
public class Singleton {
	private static Singleton singletonObject;
	private Singleton() {}
	public static Singleton getSingletonObject() {
		if (singletonObject == null) {
			singletonObject = new Singleton();
		}
		return singletonObject;
	}
}
```
멀티쓰레드 환경에서 singleton 으로 동작 하지 않는다. 위 코드는 쓰지 말자

```java
public class Singleton {
	private static Singleton singletonObject;
	private Singleton() {}
	public static synchronized Singleton getSingletonObject() {
		if (singletonObject == null) {
			singletonObject = new Singleton();
		}
		return singletonObject;
	}
}
```
함수 앞에 synchronized 키워드를 이용해 멀티쓰레드 환경에서도 singleton 이 보장 되도록 했다.  
덕분에 느리다.

```java
public class Singleton {
	private static volatile Singleton singletonObject = new Singleton();
	private Singleton() {}
	public static Singleton getSingletonObject() {
		return singletonObject;
	}
}
```
Bill pugh 라는 사람이 제시한 singleton pattern 구현 방법이다. jvm의 class loader 매커니즘과 class load 시점을 이용했다는데 잘 모르겠다.
이 코드가 가장 좋아보인다. 사용하자.

```java
public enum Singleton {
	INSTANCE;
	static String test = "";
	public static Singleton getInstance() {
		return INSTANCE;
	}
}
```
effective java 책에 소개되어있는 enum을 이용한 singleton 방법이다.
enum 이 singleton으로 사용될 수 있는 이유는 다음과 같다.
* INSTANCE 가 생성될 때, multi thread 로 부터 안전하다.
* 단 한번의 인스턴스 생성을 보장한다.
* enum value는 자바 프로그램 전역에서 접근이 가능하다.

출처 : http://asfirstalways.tistory.com/335
https://blog.seotory.com/post/2016/03/java-singleton-pattern
