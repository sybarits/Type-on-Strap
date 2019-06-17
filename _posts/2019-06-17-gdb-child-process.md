---
layout: post
title: GDB Child Process Debugging
tags: [gdb, child, process ]
excerpt_separator: <!--more-->
---

GDB에서 디버깅을 하는데 fork() 이후에 원하는데로 디버깅 할 수 없었다.  
fork() 이후에 parent process 만 계속 디버깅 된다.  

아래와 같이 GDB에서 명령을 내리면 child process를 디버깅 할 수 있다. 완전 좋다.  

set follow-fork-mode child  

위와 같이 설정하면 fork() 후에 child process를 디버깅 상태로 만들고 parent는 계속 실행시킨다.  
follow-fork-mode의 기본값은 parent이며 이는 다음과 같이 확인할 수 있다.  
  
show follow-fork-mode  
  
만약 parent와 child process를 모두 디버깅해야한다면  
이들을 모두 제어하기 위해 다음 명령을 이용할 수 있다.  
  
set detach-on-fork off  
  
만약 fork/vfork/exec와 같은 특정 이벤트 발생 시 실행을 중지시키고 싶다면  
catchpoint를 설정할 수 있다.  
  
catch exec  
  

* 부록 - gdb 명령어
  
break -- 브레이크 포인트 설정 (b 와 같음)  
b [함수명] -- 함수명으로 설정  
b [파일명]:[함수명] -- 파일과 함수명으로 설정  
b [파일명]:[라인 번호] -- 파일과 라인 번호로 설정  
b *0x7932700 -- 메모리 주소에 설정  
  
info break -- 현재 설정된 브레이크 포인트 확인  
  
clear -- 브레이크 포인트 제거 (cl과 같음)  
cl [함수명] -- 해당 함수명의 브레이크 포인트 제거  
cl 10 -- 해당 라인 브레이크 포인트 제거  
  
d -- 모든 브레이크 포인트 제거  
  
disable b - 모든 브레이크 포인트 비활성화  
enable b - 모든 브레이크 포인트 활성화  
  
  
출처: http://egloos.zum.com/studyfoss/v/5120049
