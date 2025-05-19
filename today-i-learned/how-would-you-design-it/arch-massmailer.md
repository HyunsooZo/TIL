---
description: 하루 수백만 통 이메일 발송 요구사항을 안정적이고 확장성 있게 처리하는 프로그램을 만들어보자!
---

# 📤 Arch : MassMailer

## Problem statement

대량 이메일(마케팅·알림) 발송 시스템을 설계해야 한다.

* **하루 수백만 통** 발송 목표
* SMTP 서버는 **장애**, **속도 제한**이 존재하므로 **다중 공급자(provider)** 활용
* **재시도(retry)** 정책과 **우선순위 큐잉(priority queueing)** 필요

***

## Architecture

서비스는 **API tier**, **Message Broker**, **Worker pool**, **Multiple SMTP Providers**, **RDB** 로 구성한다.

* **API tier**: 이메일 발송 요청 수집 및 DB에 저장
* **Message Broker**: Kafka 또는 RabbitMQ로 **priority topic**과 **retry topic** 관리
* **Worker pool**: 브로커에서 메시지를 소비하고 SMTP로 전송
* **SMTP Providers**: 장애 대응과 부하 분산을 위한 다중 외부 공급자
* **RDB**: 메타데이터(tracking, status) 저장용

***

## Database Design

```sql
CREATE TABLE email_jobs (
  job_id       BIGINT AUTO_INCREMENT PRIMARY KEY,
  recipient    VARCHAR(255) NOT NULL,
  subject      VARCHAR(255) NOT NULL,
  body         TEXT         NOT NULL,
  priority     INT          NOT NULL DEFAULT 0,  -- 높을수록 우선
  provider     VARCHAR(50)  NULL,               -- 실제 사용된 공급자
  status       VARCHAR(20)  NOT NULL,            -- 'PENDING','SENT','FAILED'
  attempts     INT          NOT NULL DEFAULT 0,
  created_at   DATETIME     NOT NULL,
  updated_at   DATETIME     NOT NULL
);
```

* **priority**: 우선순위 큐잉에 사용
* **attempts**: 재시도 횟수 추적
* **status**: PENDING, SENT, FAILED 상태 관리

***

## Message Queue Design

* **priority\_queue** 토픽: 우선순위별 파티션 분리
* **retry\_queue** 토픽: 실패 후 지연 전송용

```
// API tier publishes to priority_queue
produce(topic='priority_queue', key=job_id, value=job_payload, headers={priority})

// Worker sends 실패 시 retry_queue로 전송
produce(topic='retry_queue', key=job_id, value=job_payload, headers={delay=backoff})
```

***

## Worker Implementation

```java
public void consume(Message msg) {
  EmailJob job = parse(msg);
  try {
    // SMTP provider 순환 선택
    SmtpClient client = selectProvider(job.attempts);
    client.send(job.recipient, job.subject, job.body);

    // 전송 성공
    updateStatus(job.id, "SENT", client.getName());
  } catch (Exception e) {
    job.attempts++;
    if (job.attempts < MAX_RETRY) {
      // 재시도 지연(backoff) 적용
      long delay = computeBackoff(job.attempts);
      produce("retry_queue", job.id, job, Map.of("delay", delay));
      updateStatus(job.id, "PENDING");
    } else {
      // 최대 재시도 초과
      updateStatus(job.id, "FAILED");
    }
  }
}
```

* **selectProvider**: 라운드로빈 혹은 가중치 기반
* **computeBackoff**: 지수적 증가(backoff)

***

## Workflow Diagram

<figure><img src="../../.gitbook/assets/스크린샷 2025-05-19 23.28.06.png" alt=""><figcaption></figcaption></figure>

***

## Summary

* **Priority queue**로 중요도 높은 메시지 우선 처리
* **Retry queue**와 **Backoff** 정책으로 장애 복구
* **다중 SMTP 공급자**로 가용성 및 속도 분산
* **RDB**로 상태 추적 및 모니터링 가능

