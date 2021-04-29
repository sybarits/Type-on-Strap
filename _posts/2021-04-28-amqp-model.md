---
layout: post
title: RabbitMQ Basic Model
tags: [mq, rabbitmq, queue, brocker, amqp, model]
---

## AMQP 90-9-1 Model Explain
RabbitMQ는 AMQP의 구현체로 본문은 AMQP 모델을 설명하고 있는 https://www.rabbitmq.com/tutorials/amqp-concepts.html 의 내용을 요약함

# AMQP 0-9-1
* Advanced Message Queueing Protocol 의 준말(이후 AMQP)
* 응용프로그램이 메시징 미들웨어 브로커와 통신하기 위해 정한 프로토콜
* 브로커는 publisher로부터 메시지를 수신하여 subscriber에게 라우팅
* binding이라 불리는 rule을 이용해 Exchange는 queue에 메시지를 복사하여 전달
* 메시지를 받고 난 후 응답을 통해 큐 대기열의 메시지를 남기거나 제거하여 전달을 보장
* AMQP는 브로커 서버가 아닌 응용프로그램에서 exchange 정의나 routing 규칙등을 declare 하기 때문에 프로그래머블 한 프로토콜이라 함

# Exchange
* Exchange는 AMQP의 한 구성 요소
* 메시지를 받아 규칙에 따라 queue에 전달
* 네가지 exchange type이 존재
  * Direct exchange
  * Fanout exchange
  * Topic exchange
  * Headers exchange
* 이 외에 exchange는 다음과 같은 속성을 가진다.
  * name
  * Durability (exchange survive broker restart)
  * Auto-delete (exchange is deleted when last queue is unbound from it)
  * Arguments (optional, used by plugins and broker-specific feautres)

## Default Exchange
* 기본 exchange는 이름을 별도로 명하지 않고 브로커내에 이미 존재하는 exchange를 사용
* 큐생성시 입력하는 이름으로 라우팅 키가 자동 설정 됨
* 기본 exchange는 동일한 큐이름으로 메시지를 전달

## Direct Exchange
* 라우팅 키를 기반으로 큐에 메시지 전달
* 유니캐스트 라우팅에 적합
* 라우팅 키를 보고 라우팅 키가 같은 큐에 메시지를 라우팅

## Fanout Exchange
* 바인딩 된 모든 큐로 메시지를 라우팅
* 브로드 캐스트 라우팅에 이상적

## Toplic Exchange
* 라우팅 키의 패턴이 일치하는 하나 이상의 큐에 메시지를 전달
* Publish/Subscribe 패턴을 구현하는데 사용
* 멀티 캐스트 라우팅에 사용
* 라우팅 키는 .을 구분자로 사용 (예: aaa.bbb.ccc)
* Subscriber는 와일드카드 문자를 사용해 구독 가능 (예: aaa.bbb.* aaa.#)
* Publisher는 구체적 토픽만 발행 가능, 와일드카드 문자 사용 불가

## Headers Exchange
* 라우팅 키보다 더 많은 규칙을 쉽게 적용할 수 있는 헤더 사용
* 헤더에는 x-match 인자에 any, all 을 적용 가능
* 또한 key와 value 값을 헤더에 추가하여 x-match의 규칙에 따라 라우팅 적용
* any는 key와 value 쌍 중에 하나만 일치해도 라우팅
* all은 모든 키와 value가 일치해야 메시지 라우팅

# Queues
* AMQP에서 말하는 큐는 우리들이 익히 알고있는 그것
* 그외에 아래와 같은 속성을 지님
  * Name
  * Exclusive (used by only one connection and the queue will be deleted when that connection closes)
  * Auto-delete (queue that has had at least one consumer is deleted when last consumer unsubscribes)
  * Arguments (optional; used by plugins and broker-specific features such as message TTL, queue length limit, etc)
* 큐 사용은 declare를 통해 가능하며 큐가 없을 경우 생성됨
* 이미 존재하는 큐와 다른 속성의 큐를 declare 할 경우 exception 발생
* Queue Name
  * UTF-8 문자로 255 bytes 이하
  * Queue Name은 브로커 내에서 unique 함
* Queue Durability
  * durable queue의 경우 메타데이터가 디스크에 저장, transient queue 경우 메모리 저장
  * 꼭 전달해야할 중요 메시지의 경우 durable queue를 사용하고 publish mark를 persisted로 전달

# Bindings
* 바인딩이란 exchange가 메시지 라우팅에 사용하는 규칙을 의미
* queue는 exchange에 biding되어야 메시지를 라우팅 받을 수 있음
* 바인딩 할 때 routing key 가 추가 됨
* routing key는 exchange에 바인딩된 queue에 메시지를 라우팅 하기 위한 필터 역할

# Consumers
* 소비자가 메시지를 소비하는 두가지 방법
  * subscribe message (push API): recommended
  * Polling (pull API): highly inefficient
* 큐 하나에 하나 이상의 소비자를 가짐 

# Message Acknowlgements
* 메시지를 받고 처리하는 도중 응용프로그램이 중단되거나 네트워크 문제가 발생할 수 있음
* 브로커가 큐에서 메시지를 전달 후 제거하는 것을 자동 승인 모델, 어플리케이션 수행 로직 상에 제거 알림을 보낸 후 제거하는 것을 명시적 승인 모델
* 큐에 메시지가 남아 있으면 브로커는 다른 소비자에게 재전송 하거나 다른 소비자가 큐에 등록 될 때까지 기다렸다가 재전송

# Message Atrributes and Payload
* AMQP 0-9-1 model 메시지에는 속성이 있음
  * 예) Content type, content encoding, routing key, delivery mode(persistent or not), message priority, message publishing timestamp, expiration period etc.
* 일부 속성은 브로커가 사용하고 응용프로그램에서 사용할 수 도 있음
* 일부 속성은 선택사항이고 헤더라고함
* 메시지 속성은 메시지가 publish 될 때 설정
* 브로커는 페이로드를 확인 하지 않음
* 메시지는 페이로드가 없을 수 있음
* 메시지 페이로드는 JSON, Thrift, Protocol Buffers, MessagePack 등 serialization format 구조화를 통해 보내는 것이 일반적임


# Connections
* AMQP가 TCP를 사용하는 애플리케이션 수준 프로토콜
* 클라이언트 어플리케이션과 브로커 서버 사이 연결
* 인증을 통해 TLS사용 가능

# Channels
* 브로커에 대한 다중 연결이 필요할 때 여러 TCP연결을 사용하지 않고 channel로 멀티플랙스 
* 각각의 채널은 독립적으로 분리되어 있고 채널아이디를 통해 구분
* 어플리케이션에서 여러 스레드/프로세서를 사용하는 경우 각 스레드/프로세서에 하나의 channel이 연결되고 각 연결은 서로 독립적으로 동작하도록 쓰일 수 있음
* 채널은 커낵션 상에 존재하며 커낵션이 닫히면 채널도 닫힘

# Virtual Hosts
* 하나의 브로커 내에 격리된 환경을 호스팅 할 수 있는 개념
* linux에서 namespace와 유사한 개념 
* AMQP 구성요소들(exchange, queue, user etc.)을 완전히 격리 된 환경을 제공
* 클라이언트 연결 시 사용할 가상 호스트 지정


출처: https://www.rabbitmq.com/tutorials/amqp-concepts.html  
https://sejoung.github.io/2020/05/2020-05-22-amqp_concepts  
  
