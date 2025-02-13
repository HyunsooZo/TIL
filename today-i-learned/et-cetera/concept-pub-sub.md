---
description: >-
  Pub/Sub은 Publish-Subscribe의 줄임말로, 메시지를 발행하는 Publisher와 이를 구독하는 Subscriber로 구성된
  비동기 메시징 패턴이다. 이는 이벤트 기반 아키텍처에서 핵심적인 역할을 하며, 분산 시스템 간 효율적인 데이터 전달을 가능하게 한다.
---

# 📥 Concept : Pub/Sub

## How to Use Pub/Sub in Spring Boot

Spring Boot에서 Pub/Sub을 구현하는 방법은 여러 가지가 있다.\
&#x20;대표적으로 **Spring Cloud Stream**과 **Kafka, RabbitMQ, Redis** 등의 메시지 브로커를 활용할 수 있다.

### 1. Spring Boot + Kafka 설정

**의존성 추가 (build.gradle)**

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter'
    implementation 'org.springframework.kafka:spring-kafka'
}
```

**Producer (메시지 발행)**

```java
@Service
public class KafkaProducer {
    private final KafkaTemplate<String, String> kafkaTemplate;

    public KafkaProducer(KafkaTemplate<String, String> kafkaTemplate) {
        this.kafkaTemplate = kafkaTemplate;
    }

    public void sendMessage(String topic, String message) {
        kafkaTemplate.send(topic, message);
    }
}
```

**Consumer (메시지 구독)**

```java
@Service
public class KafkaConsumer {
    @KafkaListener(topics = "example-topic", groupId = "group_id")
    public void listen(String message) {
        System.out.println("Received message: " + message);
    }
}
```

## 2. Spring Boot + Redis Pub/Sub

Redis를 이용한 Pub/Sub은 메시지를 Redis의 채널을 통해 발송하고 수신하는 방식으로 동작한다.

**의존성 추가 (build.gradle)**

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-redis'
}
```

**Redis Publisher (메시지 발행)**

```java
@Service
public class RedisPublisher {
    private final StringRedisTemplate redisTemplate;

    public RedisPublisher(StringRedisTemplate redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

    public void publish(String channel, String message) {
        redisTemplate.convertAndSend(channel, message);
    }
}
```

**Redis Subscriber (메시지 구독)**

```java
@Component
public class RedisSubscriber {
    @EventListener
    public void onMessage(Message message, byte[] pattern) {
        System.out.println("Received Redis message: " + new String(message.getBody()));
    }
}
```

## Internal Mechanism of Pub/Sub

Pub/Sub은 다음과 같은 원리로 동작한다.

<figure><img src="../../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

1. **발행(Publish):** Publisher는 메시지를 특정 주제(Topic)에 발행.
2. **저장(Enqueue):** 메시지 브로커(Kafka, RabbitMQ, Redis 등)는 메시지를 저장하고 관리.
3. **전달(Deliver):** 구독 중인 Subscriber에게 메시지를 전달.
4. **처리(Process):** Subscriber는 수신한 메시지를 비즈니스 로직에 따라 처리.
5. **확인(Acknowledge):** Subscriber가 정상적으로 메시지를 처리했음을 브로커에 전달해 메시지 삭제 여부를 결정.

## Advantages of Pub/Sub

Pub/Sub의 주요 장점은 다음과 같다.

* **비동기 처리**: 메시지를 즉시 전달하지 않고, 필요할 때 수신할 수 있다.
* **확장성**: 다수의 Subscriber가 동일한 메시지를 받아도 성능 저하가 적다.
* **유연성**: Publisher와 Subscriber가 직접적으로 연결되지 않아 독립적으로 개발 및 운영이 가능하다.
* **내결함성**: 메시지 큐를 활용하면 일시적인 장애에도 메시지가 손실되지 않는다.

## Use Cases of Pub/Sub

Pub/Sub은 다양한 분야에서 활용된다.

* **이벤트 드리븐 아키텍처**: 실시간 데이터 스트리밍, 로그 분석
* **알림 시스템**: 푸시 알림, 이메일 발송
* **IoT**: 센서 데이터 수집 및 처리
* **마이크로서비스 통신**: 서비스 간 비동기 데이터 교환

## Conclusion

Pub/Sub은 이벤트 기반 시스템에서 필수적인 메시징 패턴이다. \
비동기 메시징을 통해 확장성과 유연성을 보장하며, 다양한 서비스에서 널리 사용된다. \
Spring Boot에서는 Kafka, RabbitMQ, Redis와 같은 메시지 브로커를 활용하여 쉽게 구현할 수 있다. \
이를 활용하면 시스템 간 결합도를 낮추고, 안정적인 데이터 처리가 가능하다.
