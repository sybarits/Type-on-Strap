---
layout: post
title: Starting RabbitMQ
tags: [mq, message, rabbitmq, queue, brocker]
---

RabbitMQ 시작하기


## RabbitMQ
RabbitMQ는 오픈 소스 메시지 브로커 소프트웨어로 AMQP의 구현체이다. STOMP, MQTT등의 프로토콜도 지원을 하며 기능을 확장하고 있다. 메시지 생산자(producer)와 메시지를 수신하는 소비자(consumer) 사이에서 메세지 전달자(brocker) 역할을 한다.

## AMQP
AMQP는 Advanced Message Queuing Protocol의 약자로 메시지 지향 미들웨어(응용 소프트웨어 간의 데이터 통신을 위한 소프트웨어)를 위한 프로토콜이다. 와이어 레벨 프로토콜로서 바이트 스트림으로 네트워크를 경유하며 솔신되는 데이터의 형식을 기술하기 때문에 이런 형식을 따를는 메시지를 만들고 해석할 수 있으면 구현 언어와 관계없이 다른 시스템과 상호 운용이 가능하다.

## MQTT
MQTT는 Medssage Queuing Telemetry Transport의 약자로 pub-sub 기반 메시징 프로토콜이다. ISO 표준이며 TCP/IP 프로토콜 위에서 동작한다. 프로토콜의 페이로드 형식이나 동작을 보면 알 수 있듯이 아주 경량화된 프로토콜이다. 프로세싱 파워가 작은 기기에서 메시지 전달이 필요하거나, 네트워크 환경이 좋지 않더라도 운용이 가능하는 것이 이 프로토콜 설계 의도이다.

## RabbitMQ 설치 및 환경 구성
데비안에서 RabbitMQ를 설치해보자. 아래와 같이 명령어를 실행한다.
```
sudo apt install rabbitmq-server
```
RabbitMQ를 설정하기 위해 아래 처럼 플러그인 하나를 활성화 하자
```
sudo rabbitmq-plugins enable rabbitmq_management
sudo systemctl restart rabbitmq-server
```
rabbitmq를 재시작 해야 설정이 적용 된다.  
관리자 계정을 추가해서 브라우저를 통해 RabbitMQ를 관리해보자
```
sudo rabbitmqctl add_user {id} {password}
sudo rabbitmqctl set_user_tags {id} administrator
```
아이디를 만들어 관리자 권한을 주면 브라우저로 관리 페이지에 접근이 가능하다. 
http://ip:15672로 접속하고 좀전에 만든 아이디와 패스워드를 이용하자.  
기본 포트는 5672 이다.
  
  이제 시작이다.
  

출처: https://ko.wikipedia.org/wiki/AMQP  
https://ko.wikipedia.org/wiki/MQTT  
http://abh0518.net/tok/?p=384  
https://kamang-it.tistory.com/entry/AMQPRabbitMQRabbitMQ%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0%EC%99%80-%EC%84%A4%EC%B9%98%EB%B0%A9%EB%B2%95-1  
  
