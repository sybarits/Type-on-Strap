---
layout: post
title: Proxy Pattern
tags: [pattern, proxy, design]
---

## Proxy Pattern
어떤 객체에 대한 접근을 제어하는 용도로 대리인이나 대변인에 해당하는 객체를 제공하는 패턴
1. virtual proxy
클래스가 사용하는 자원이 많을 경우 작업이 필요할 때에만 클래스를 사용하도록 프록시를 사용
2. Protection proxy
클래스에 대한 접근을 제어하기 위한 경우 프록시 클래스에서 주체 클래스에 대한 접근권한을 확인하여 메소드 호출을 제어
3. remote proxy
프록시 클래스는 로컬에 있고 주체 클래스는 remote로 존재하는 경우 사용

## 구현 예시
아주 간단한 예제 하나만 들어보도록 한다. 프록시의 구현에서 프록시 메소드에 본 서비스 객체를 생성하는 경우 virtual proxy 이고, 접근 권한을 확인하여 본 서비스의 메소드 호출을 제어하는 경우 protection proxy, 리모트 통신을 통해 본 서비스를 호출하는 경우 remote proxy라 한다. 아래는 서비스가 있고 이 서비스를 호출하기 위한 프록시가 존재하는 경우이다. 

```java
public interface Service {
	String run();
}
```
```java
public class ServiceImpl implements Service {
	@Override
  public String run() {
    return "call service";
  }
}
```
```java
public class ProxyService implements Service {
	Service service;
  
  @Override
  public String run() {
    service = new Service();
    return service.run();
  }
}
```
```java
public class ProxyMain {
	public static void main(String[] args) {
    Service proxy = new ProxyService();
    System.out.println(proxy.run());
  }
}
```
메인 함수에서 프록시를 호출하여 결과적으로 서비스 구현체를 호출하는 것을 볼 수 있다.




출처: https://yaboong.github.io/design-pattern/2018/10/17/proxy-pattern/  
  https://limkydev.tistory.com/79
  https://jdm.kr/blog/235
  
