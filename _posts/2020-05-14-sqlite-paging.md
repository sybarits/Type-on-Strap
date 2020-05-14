---
layout: post
title: SQLite Paging
tags: [sqlite, paging, limit, offset]
---

SQLite에는 ROWNUM() 기능이 없다. 페이징 처리를 하려면  LIMIT & OFFSET 을 이용한다.

```
SELECT column1, column2, column3 from table_name LIMIT 10 OFFSET 20;
```
위 구문은 20만큼 건너뛰고 10 만큼 출력한다는 의미이다. 이 구문을 이용해 paging 처리가 가능하다.

출처: https://ddolcat.tistory.com/72  

https://www.sqlitetutorial.net/sqlite-limit/
