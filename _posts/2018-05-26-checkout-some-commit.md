---
layout: post
title: Git 특정 커밋으로 가기
tags: [git, checkout, ]
excerpt_separator: <!--more-->
---

작업중인 사본을 1단계 전 커밋으로 돌려준다. 숫자에 따라 해당하는 전 커밋으로 간다.
  
```
git checkout HAED~1
git checkout HAED~12
...
```
특정 커밋으로 가고 싶을경우
```
git checkout 7432f3
```
위와 같이 commit의 해시 값 앞 6자리만 적어준다.  

다시 돌아오는 방법은 아래와 같다.
```
git checkout master
```

  -끝-
