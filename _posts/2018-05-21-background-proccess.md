---
layout: post
title: background proccess
tags: [ssh, nohup, disown]
excerpt_separator: <!--more-->
---

ssh로 접속한 후 터미널을 닫으면 터미널을 통해 실행하던 작업들이 중지되는데...  
>> $ nohup ./test.sh &
이렇게 nohup 과 & 를 추가하면 된다.  
이렇게 해도 끊기면
>> $ disown -h
명령어를 추가 실행한다.  
  
처음에 백그라운드 작업으로 실행 하지 못했다면  
Ctrl + z 를 눌러 프로세스를 정지시키고 shell로 돌아가 bg 를 입력하면 작업이 백그라운드로 간다.  
그다음 disown -h 를 입력한다. -끝-
