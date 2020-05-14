---
layout: post
title: SQLite AUTOINCREMENT, ROWID
tags: [sqlite, autoincrement, rowid]
---

## AUTOINCREMENT
테이블 생성시 기본키에 autoincrement를 설정하면 insert 문에서 명시적으로 값을 입력할 수도 있도, 기본키를 생략할 수도 있다. 기본키가 생략된 경우에는 테이블에 입력된 기본키 중 가장 큰 값에 1이 증가된 값으로 입력된다. autoincrement가 기본키에 설정됐더라도 기본키 조건만 만족하면 순차적으로 입력되지 않아도 된다. 즉 값이 겹치지않으면 임의로 값을 입력 가능하다.

```
create table table_name (
	column1 integer primary key,
	column2 integer autoincrement,
	column3 text
);

create table table_name (
	column1 integer primary key autoincrement,
	column2 integer,
	column3 text
);
```

```
select * from sqlite_sequence; 
```
위 구문을 이용해 조회를 하면 autoincrement가 적용된 테이블에 가장 큰 값을 확인 할 수 있다.

## rowid
SQLite에는 rowid라는 테이블 기본 값이 존재한다. 이 값은 테이블이 생성됨과 동시에 각 row에 자동으로 부여되는 값으로 최대 값이 9,223,372,036,854,775,807 이다. SQLite에서는 autoincrement를 사용하지 말것을 권고한다. 유사한 기능인 rowid를 사용하도록 권한다.

```
CREATE TABLE t1(c text);

INSERT INTO t1(c) VALUES('A');
INSERT INTO t1(c) values('B');
INSERT INTO t1(c) values('C');
INSERT INTO t1(c) values('D');

select rowid, c from t1;
```
위처럼 구문을 실행하면 1~4의 값이 각 row에 부여되었음을 알 수 있다. 


출처: https://lovedb.tistory.com/356
  https://www.sqlitetutorial.net/sqlite-autoincrement/
