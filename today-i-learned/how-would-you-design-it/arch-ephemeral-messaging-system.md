---
description: >-
  사내 메신저에 사라지는 메시지 기능을 보며 어떻게 구현한건지 궁금해 알아보았다. 스냅챗이나 다른 메신저에도 간혹 보이는 기능인데, 상대방이
  읽은 시점부터 카운팅이 된다는점에서 흥미로워 검색해보았다.
---

# 👻 Arch : Ephemeral Messaging System

## Problem Statement

메신저 서비스에서 사용자가 상대방에게 보낸 메시지를:

* **상대방이 읽은 후 10초 뒤에 삭제**
* 또는 **전송한 후 24시간 뒤 자동 삭제**

되는 방식으로 구현하고자 한다.

***

## Requirements

* 메시지는 DB에 저장되며, 상대방이 **읽기 전까지는 삭제되면 안 된다.**
* 상대방이 메시지를 **읽은 순간부터 타이머가 동작**하여 삭제된다.
* 일정 주기마다 **삭제 대상 메시지를 찾아 제거**하는 프로세스가 필요하다.

***

## System Architecture

<figure><img src="../../.gitbook/assets/스크린샷 2025-05-26 23.38.46.png" alt=""><figcaption></figcaption></figure>

## Table Design

<table><thead><tr><th width="169.83984375">Column</th><th>Type</th><th>Description</th></tr></thead><tbody><tr><td>message_id</td><td>UUID</td><td>메시지 고유 ID</td></tr><tr><td>sender_id</td><td>UUID</td><td>발신자 ID</td></tr><tr><td>receiver_id</td><td>UUID</td><td>수신자 ID</td></tr><tr><td>content</td><td>TEXT</td><td>메시지 본문</td></tr><tr><td>sent_at</td><td>DATETIME</td><td>보낸 시간</td></tr><tr><td>read_at</td><td>DATETIME NULL</td><td>수신자가 읽은 시간</td></tr><tr><td>expire_after_read</td><td>INTEGER</td><td>읽은 후 몇 초 뒤에 삭제할지</td></tr><tr><td>auto_expire_at</td><td>DATETIME NULL</td><td>자동 삭제 시각 (계산된 값)</td></tr></tbody></table>

***

## How to Implement?

**1. 메시지 저장 시**

* `read_at`은 NULL로 저장
* `expire_after_read`에 기본값 설정 (ex: 10초)

**2. 메시지 읽을 때**

* `read_at`을 현재 시각으로 업데이트
* `auto_expire_at = read_at + expire_after_read` 계산하여 업데이트

```java
fun readMessage(messageId: UUID, readerId: UUID) {
    val message = messageRepository.findById(messageId)
    if (message.receiverId != readerId) throw UnauthorizedException()

    val now = LocalDateTime.now()
    message.readAt = now
    message.autoExpireAt = now.plusSeconds(message.expireAfterRead)
    messageRepository.save(message)
}
```

**3. 메시지 삭제 스케줄러**

* 일정 주기(예: 1분마다)로 `auto_expire_at <= now` 인 메시지를 찾아 삭제

```java
@Scheduled(fixedDelay = 60000)
fun deleteExpiredMessages() {
    val now = LocalDateTime.now()
    val expired = messageRepository.findAllByAutoExpireAtBefore(now)
    messageRepository.deleteAll(expired)
}
```

***

## Pros & Cons

| 장점                        | 단점                        |
| ------------------------- | ------------------------- |
| 메시지 노출 시간 제어 가능           | 정확한 "삭제 타이밍" 보장 어렵다       |
| 스케일 아웃 구조에서 잘 동작          | 많은 메시지 삭제 시 DB 성능 고려 필요   |
| 확장(24시간 자동 삭제 등)에 유연하게 대응 | 읽지 않은 메시지의 삭제 시점이 명확하지 않음 |

***

## Tips for Scaling

* **읽은 메시지만 삭제 대상**으로 하여 쿼리 범위 축소
* `auto_expire_at` 에 인덱싱
* 메시지를 **Soft Delete** 후 실제 삭제는 나중에 (ex. Kafka에 delete 이벤트 발행)
* Redis TTL을 활용한 **메모리 캐시 기반 임시 메시지 처리도 고려 가능**

***

#### 결론

Snapchat이나 Telegram의 사라지는 메시지는 단순히 프론트에서 "숨기는" 게 아니라,\
**DB + 스케줄러 + TTL + 이벤트 기반 아키텍처**가 유기적으로 동작해야 하는 복합적인 백엔드 기능이다.
