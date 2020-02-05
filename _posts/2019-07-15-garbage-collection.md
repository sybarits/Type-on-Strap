---
layout: post
title: Garbage Collection
tags: [garbage, collection, java ]
---

자바의 가비지 컬렉터가 어떻게 돌아가는지 알아보자.  
자바의 힙은 아래의 그림과 같이 여러 부분으로 나뉜다.

![Image of java heap]({{ site.baseurl }}/assets/img/post/java_heap.PNG)

먼저 Permanent 라고 써있는 부분에는 자바에서 가지고 있어야 할 메타데이터를 보관하고 있다.
어플리케이션에서 사용되는 클래스와 메소드들의 정보들이다.
자바 SE 라이브러리의 클래스와 메소드들도 저장되어있다.
  

  
## 가비지 컬랙션이 진행되는 과정
1. 새로운 객체는 eden에 들어간다. S0, S1는 비어있다.
2. eden이 가득 차면 minor 가비지 컬랙션이 진행된다.
3. 참조되고 있는 객체는 eden에서 S0으로 이동하고, 참조되지 않는 객체는 eden에서 지워진다.
4. 두번째 minor GC가 일어나면 eden에 있던 참조되고 있는 객체는 S1으로 이동하고, S0에 있던 객체중 참조되고 있는 객체는 S1으로 이동하면서 age가 올라간다. eden과 servivor 영역에 있는 참조되지 않는 객체는 지워진다.
5. 그 다음 minor GC에서는 위와 같은 동작이 일어나지만 객체는 S0으로 이동하고 S1영역은 비워진다.
6. minor GC가 일어난 후 servivor 영역에 있는 객체 중 일정 age 이상인 객체들은 Tenured(old ganeration)으로 이동한다.
7. minor GC가 계속 일어나면 young generation에 있는 객체들이 old generation으로 이동한다.
8. 대부분의 GC는 minor GC를 통해 해결이 되고, old generation 영역을 정리하기 위해 major GC가 실행된다. 


minor/major garbage collection은 기본적으로 stop the world events 이다. major garbage collection이 오래걸린다.
가비지 컬랙션의 종류에 따라 major garbage collection이 진행되는 시간이 영향을 받는다.

### The Serial GC
* Serial GC는 Java SE 5 and 6의 기본 GC다. 
* minor GC와 major GC가 차례로 실행된다.
* mark compact collection method가 사용된다. 이 방법은 사용되고 있는 메모리를 힙의 시작 부분에 모아 놓고 그 뒤로 메로리를 새로 할당한다. 새로운 메모리 할당이 빠른 장점이 있다.
* Serial GC는 stop the world에 걸리는 시간이 길어도 별 상관 없는 client-style machine에서 사용된다. GC를 위해 single virtual processor를 사용할 경우 이점이 있는 방법이다. heap을 수백MBs 를 사용하는 보통의 어플리케이션에서 효과적인 GC 방법이 될 수 있다.
* 실제 프로세서보다 JVM의 수가 더 많을 경우에도 좋은 선택이 될 수 있다.

### The Parallel GC
* minor GC가 여러 쓰래드에 의해서 수행된다.
* 호스트 하나의 CPU가 하나라도 Parallel GC가 권장된다.
* Parallel collector는 Throughput collector로도 불린다.
* 다중 CPU에서 좋은 성능을 낸다.
* 많은 일을 해야 하고 긴 중지시간을 가져도 좋은 환경(ex. batch processing like printing reports or bills or performing a large number of database queries)에서 선택된다.
* -XX:+UseParallelGC 이 명령으로 minor GC는 multi-thread로 major GC는 single-thread로 수행한다.
* -XX:+UseParallelOldGC 이 명령을 이용해 minor/major GC 모두 multi-thread로 수행한다.메모리 정리는 old generation에서만 한다.

### The Concurrent Mark Sweep (CMS) Collector
* the concurrent low pause collector라고 불리기도 한다.
* 어플리케이션이 돌아가면서 동시에 GC를 수행하여 중지 상태를 최소화하려고 한다.
* live object를 복사하거나 compact하지 않는다.
* 조각화가 문제가 된다면 더 큰 힙을 할당한다.
* CMS collector는 적은 정지시간을 요하면서 GC와 자원을 공유할 수 있는 환경(UI application, webserver)에서 사용된다.

### The G1 Garbage Collector
* Java 7 부터 지원한다. CMS collector를 대체하기 위해 만들어 졌다.
* 그냥 제일 좋다고 보면된다.(대부분의 경우에서...)
* 위에서 언급한 heap의 구조와는 다른 heap 구조를 가진다.
* 메모리 영역을 바둑판 모양으로 나누어놓고 각 영역에 대해 GC를 진행한다.
* 각 영역(region)은 1MB ~ 32MB 이다.
* 여러구역으로 나뉜 메모리 영역은 각각의 영역이 eden, servivor, tenured 이 모두가 될 수 있다.
* 이 외에 homongous region과 available/unused region 이 있다.
* 객체가 region의 영역보다 일정 크기 이상이라면 homongous region으로 지정한다.(큰 객체가 존재할 경우 G1 GC는 적합하지 않을 수 있다.)
* 메모리 영역들의 각각의 객체들의 참조 상태를 스캔한다.
* 비어있는 영역에 aging을 거친 객체들을 옮겨놓는다.

![Image of java heap]({{ site.baseurl }}/assets/img/post/g1-garbage-collector-details-and-tuning-44-638.jpg)


지금까지 대략적으로 Java의 Garbage collection에 대해 알아보았다. G1 GC는 좀 더 공부가 필요할 것 같다..

출처: https://www.oracle.com/webfolder/technetwork/tutorials/obe/java/gc01/index.html  
https://yaboong.github.io/java/2018/06/09/java-garbage-collection/  
https://d2.naver.com/helloworld/1329  
https://initproc.tistory.com/entry/G1-Garbage-Collection
