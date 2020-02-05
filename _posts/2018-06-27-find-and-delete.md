---
layout: post
title: 특정 문자열을 포함하는 파일 삭제 하기 
tags: [find, delete]
---

특정 문자열을 포함하는 파일들을 일괄적으로 지우고 싶으면    

  
```
find . -name "*특정문자열*" -delete
```

특정 문자열을 포함하는 파일들을 찾는 명령어가 아래와 같다.
```
find . -name "*특정문자열*"
```
거기에 -delete 옵션만 추가 한것

-끝-
