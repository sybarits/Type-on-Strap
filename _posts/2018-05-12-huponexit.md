---
layout: post
title: huponexit
tags: [session, process, shopt, huponexit]
excerpt_separator: <!--more-->
---

원격 서버에 로그인 후 프로세스들을 실행하고 세션을 종료하면 실행한 프로세스들이 모두 죽는다. 왜냐하면 세션으로 접속 후 실행한 프로세스들은 세션 프로세스의 하위 프로세스들이기 때문이다.  
세션이 종료되어도 계속하여 프로세스 를 실행하기 위해서 shopt 명령어를 이용한다.  
  
shopt 를 입력하면 리스트가 쭉 나온다.  
그 중 huponexit off 를 확인할 수 있다.  
shopt -s huponexit 입력 후  
shopt 로 리스트를 확인하면 huponexit on 를 확인할 수 있다.
