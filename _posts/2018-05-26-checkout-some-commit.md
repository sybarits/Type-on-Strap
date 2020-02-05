---
layout: post
title: Git 특정 커밋으로 가기, 수정 되돌리기
tags: [git, checkout, ]
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

파일을 수정 했는데 마지막 커밋으로 돌아가고 싶을 때  
수정한것 되돌리기
```
git checkout [filename]
```
특정 폴더 아래의 모든 수정 되돌리기
```
git checkout [dir]
```
repository 내 모든 수정 되돌리기(root dir 에서)
```
git checkout .
```

모든 untracked file 지우기
```
git clean -fdx
```

  -끝-
