---
layout: post
title: RabbitMQ Routing, Topic
tags: [mq, message, rabbitmq, queue, brocker, topic, routing]
---

RabbitMQ Use Case Test Code 분석
* 본문에서는 공식 문서에서 제공하는 튜토리얼을 기반으로 내용을 정리했으며, 자바로 짜여진 코드에 대해서만 다룬다.

## RabbitMQ Routing
RabbitMQ는 Exchange와 Queue를 가지고 있다. Exchange에 바인딩을 한 후 Queue에 직접 메시지를 전달하는 방법을 Routing이라고 부른다. 아래는 로그를 단계별로 나누어 queue에 보내는 방법을 sender와 reciever로 나누어 작성한 예제이다.

```java
import com.rabbitmq.client.Channel;
import com.rabbitmq.client.Connection;
import com.rabbitmq.client.ConnectionFactory;

public class EmitLogDirect {

  private static final String EXCHANGE_NAME = "direct_logs";

  public static void main(String[] argv) throws Exception {
    ConnectionFactory factory = new ConnectionFactory();
    factory.setHost("localhost");
    try (Connection connection = factory.newConnection();
         Channel channel = connection.createChannel()) {
        channel.exchangeDeclare(EXCHANGE_NAME, "direct");

        String severity = getSeverity(argv);
        String message = getMessage(argv);

        channel.basicPublish(EXCHANGE_NAME, severity, null, message.getBytes("UTF-8"));
        System.out.println(" [x] Sent '" + severity + "':'" + message + "'");
    }
  }
  //출처: https://www.rabbitmq.com/tutorials/tutorial-four-java.html
```
로그를 보내는 쪽, 즉 Sender 혹은 Producer는 커낵션 팩토리를 이용해 커낵션을 맺는다. 위 예제에서는 setHost만 있지만 setPort, setUsername, setPassword를 추가해 연결 옵션을 줄 수 있다. 커낵션을 맺음으로 채널을 생성하고 exchange를 선언한다. exchange type을 "direct"로 선언함으로 routing key가 일치하는 queue에 메시지를 전달한다.

```java
import com.rabbitmq.client.*;

public class ReceiveLogsDirect {

  private static final String EXCHANGE_NAME = "direct_logs";

  public static void main(String[] argv) throws Exception {
    ConnectionFactory factory = new ConnectionFactory();
    factory.setHost("localhost");
    Connection connection = factory.newConnection();
    Channel channel = connection.createChannel();

    channel.exchangeDeclare(EXCHANGE_NAME, "direct");
    String queueName = channel.queueDeclare().getQueue();

    for (String severity : argv) {
        channel.queueBind(queueName, EXCHANGE_NAME, severity);
    }
    System.out.println(" [*] Waiting for messages. To exit press CTRL+C");

    DeliverCallback deliverCallback = (consumerTag, delivery) -> {
        String message = new String(delivery.getBody(), "UTF-8");
        System.out.println(" [x] Received '" +
            delivery.getEnvelope().getRoutingKey() + "':'" + message + "'");
    };
    channel.basicConsume(queueName, true, deliverCallback, consumerTag -> { });
  }
}
//출처: https://www.rabbitmq.com/tutorials/tutorial-four-java.html
```
로그는 받는 쪽도 역시 rabbitmq와 연결을 맺고 exchangeDeclare를 이용해 어떤 exchange에 연결할 것인지 설정 한다. queueBind를 이용해 어떤 queue에 어떤 routing key로 bind 할 지 설정한 후 메시지를 기다린다.

## RabbitMQ Topic
RabbitMQ에서 "topic"은 exchangeDeclare 할 때 exchange type 설정 값 중 하나로, direct와 비슷하지만 routing key를 서버에서 메시지를 보낼 때 패턴을 주어 메시지를 선택적으로 줄 수 있도록 하는 기능이다. 예제코드는 거의 유사하며, routing key를 aa.bb.cc 이런식으로 주고 메시지를 보내는 쪽에서는 aa.*.cc 혹은 aa.# 이런식으로 보낼 수 있다. *은 .을 기준으로 단어 하나를 대체하는 와일드 카드 문자로 쓰인다. #은 0개 이상의 단어를 대체하는 와일드 카드 문자로 사용한다. 이런 방법을 이용해 패턴을 사용하여 topic별 메시지를 전달 할 수 있다.

## 기타
다른 예제는 출처 사이트를 직접 가서 확인 해보자 차근차근 보다보면 어렵지 않게 사용할 수 있을 것이다.

  

출처: https://www.rabbitmq.com/tutorials/tutorial-four-java.html  
  
