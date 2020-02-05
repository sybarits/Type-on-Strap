---
layout: post
title: Domain Socket 
tags: [unix, domainsocket]
---

# Unix Domain Socket

Unix Domain Socket(이하 UDS) 는 socket API를 수정없이 이용며,
port 기반의 Inernet Domain Socket에 비해서 로컬 시스템의 파일시스템을 이용해서 내부프로세스간의 통신을 위해 사용한다는 점이 다르다고 할수 있다. 

파일을 통해서 통신을 하며, 커널내부에서 메시지를 관리한다는 점에서 FIFO와 매우 유사한면을 보여주지만, FIFO와는 달리 양방향 통신이 가능하다는 특징을 가지고 있다. 그러므로 다중의 클라이언트를 받아들이는 서버/클라이언트 모델을 만들기가 매우 쉽다.
또한 Inet 소켓을 통한 외부통신에 비해서 2배 이상의 효율을 보여준다라는 장점을 가지고 있다. 

많은 경우 약간 복잡한 내부프로세스간 통신을 해야된다고 했을때 UDS을 많이 사용한다.
INET 계층에서의 통신이 TCP/IP 4계층을 모두 거치는것과는 다르게, UDS 은 어플리케이션 계층에서 TCP 계층까지만 메시지가 전달되고,
다시 곧바로 어플리케이션 계층으로 메시지가 올라가게 된다.

위에서 INET 소켓보다 2배이상의 효율을 가진다고 했는데,
4계층의 레이어를 모두 거쳐야하는 INET 소켓에 비해서 단지 2개의 레이어만 사용한다는 점도 그 이유중 하나로 작용한다.

UDS를 생성 할 때   unix_socket = socket(AF_UNIX, type, 0);  
type에는 SOCK_STREAM(TCP), SOCK_DGRAM(UDP), SOCK_SEQPACKET(2.6.4 ) 이 있다.

internet 을 위한 socket 과는 달리 unix domain socket은 udp에서 패킷이 누실 되거나 순서가 바뀔 염려가 없다.
TCP는 패킷의 바운드를 따로 두지 않으며, UDP는 메시지 경계를 가지는 것이 차이점이다.
  
  
  
출처: https://www.joinc.co.kr/w/Site/system_programing/IPC/Unix_Domain_Socket  
http://man7.org/linux/man-pages/man7/unix.7.html  
-끝-
