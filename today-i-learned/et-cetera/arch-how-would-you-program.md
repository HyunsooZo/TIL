---
description: >-
  개발자 커뮤니티에서 재밌는 문제를 보았다. (정답은 안 적혀있었다 ㅜㅜ) 아래의 조건에 맞춰 어떻게 설계를 할지에 대한 문제였는데, 내
  수준으로는 간단한 설계밖에 할 수 없어 ChatGPT에게 설계를 맡겨보았다. 물론 정답은 없지만 나보다 나은 GPT의 의견이므로 충분한
  참고가 되었다.
---

# 🤔 Arch : How would you program?

## Problem statement

1만명의 유저에게 선착순으로 10원씩 돈을 주는 서비스를 개발해야 한다. \
‘1만명’, ‘선착순’이라는 조건을 반드시 지켜야 하며, 지급 트랜잭션은 실패할 수 있고,\
&#x20;갑작스러운 트래픽 폭주가 발생할 수 있다.

***

## Architecture

서비스는 **API tier**, **Redis slot queue**, **Worker tier**, **RDB** 로 구성한다. \
Redis의 원자 명령을 이용해 선착순 슬롯을 관리하고, \
Worker에서 비동기 송금 처리와 실패 복구를 담당한다.

***

## Database Design

```sql
CREATE TABLE user_grants (
  user_id   BIGINT      PRIMARY KEY,      -- 중복 지급 방지
  grant_seq INT         NOT NULL,         -- 선착순 순번 (1..10000)
  status    VARCHAR(20) NOT NULL,         -- 'PENDING', 'SUCCESS', 'FAILED'
  created_at DATETIME   NOT NULL,         -- 요청 수신 시간
  updated_at DATETIME   NOT NULL          -- 상태 변경 시간
);
```

***

## Slot Management

**Redis**를 이용해 선착순 슬롯을 관리한다. \
초기화 시 `grant:available` 리스트에 1부터 10000까지 넣어두고, \
**RPOPLPUSH**로 가용 슬롯을 꺼내 `grant:processing` 큐로 이동시킨다.

```
-- 초기화
RPUSH grant:available 1 2 … 10000

-- 요청 처리(API)
slot = RPOPLPUSH grant:available grant:processing
if slot == null:
  return "마감"
INSERT INTO user_grants(user_id, grant_seq, status='PENDING', ...) VALUES (...)
PUBLISH to worker: {user_id, slot}
return "접수 완료"
```

***

## Worker Implementation

Worker는 메시지를 받아 **트랜잭션**으로 송금을 시도하고, \
성공·실패에 따라 Redis와 DB를 업데이트한다.

```java
public void processGrant(long userId, int slot) {
  try (Connection conn = dataSource.getConnection()) {
    conn.setAutoCommit(false);
    // 송금 비즈니스 로직
    boolean ok = moneyService.transfer(userId, 10);
    if (ok) {
      updateStatus(conn, userId, slot, "SUCCESS");
      redis.lrem("grant:processing", 1, String.valueOf(slot));
    } else {
      updateStatus(conn, userId, slot, "FAILED");
      // 슬롯 복구
      redis.lrem("grant:processing", 1, String.valueOf(slot));
      redis.rpush("grant:available", String.valueOf(slot));
    }
    conn.commit();
  }
}
```

<figure><img src="../../.gitbook/assets/스크린샷 2025-05-18 23.16.14.png" alt=""><figcaption></figcaption></figure>

***

## Failure Handling

* **Worker crash** 시 `grant:processing` 큐를 주기적으로 스캔해 일정 시간 경과 슬롯을 \
  `grant:available`로 복구한다.
* **중복 요청** 시 `user_id PK` 위반으로 이미 지급 처리됨을 감지한다.

***

## Summary

* Redis 슬롯 큐로 **선착순 1만명**을 보장한다.
* API/Tier와 Worker를 수평 확장해 **고부하 대응**이 가능하다.
* 트랜잭션 실패 시 **슬롯 복구**로 다른 유저에게 기회를 제공한다.
* DB 제약으로 **중복 지급**을 방지한다.

이 구조로 설계하면 요구사항을 만족하면서도 성능과 안정성을 모두 확보할 수 있다.
