---
layout: post
title: Java Collection and Iterator
tags: [colection, iterator, java]
---


  
```java  
for (Character c : Carray) {
  if (Character.isDigit(c)) {
    Carray.remove(c);
  }
}
```  
위와 같이 ArrayList 에 들어있는 요소를 for 문을 이용해 삭제하면 ConcurrentModificationException 발생한다.  

```java  
for (Iterator<Character> iter = Carray.iterator(); iter.hasNext(); ) {
  Character c = iter.next();
  if (Character.isDigit(c)) {
    iter.remove();
  }
}

```  

이 처럼 이터레이터를 사용하면 해결 된다. 자바 공식 문서에서는 유일한 해결 방법이라고 한다.  

출처  
http://www.daleseo.com/how-to-remove-from-list-in-java/

  -끝-
