---
description: 외부 통신이 많거나 장애 복구가 중요한 모듈을 효율적으로 관리하기 위한 패턴
---

# 📌 Arch : Versioned Immutable Data Pattern

## Versioned Immutable Data?

Versioned Immutable Data는 **데이터를 절대 수정하거나 삭제하지 않고**, \
**새로운 버전의 데이터를 계속 추가(insert)하는 방식**을 의미한다.

각 데이터는 **비즈니스 키**(Business Key)와 **버전 정보**(Version 또는 Timestamp)로 관리된다.

이 방식은 기존 레코드를 갱신하는 대신, \
**새로운 레코드를 추가**하여 과거 모든 기록을 보존하고, 필요 시 과거 데이터로 복구할 수 있게 한다.

***

## Why Versioned Immutable Data?

Versioned Immutable Data 패턴을 사용하면 다음과 같은 이점이 있다.

* **Audit & Traceability**: 변경 이력이 모두 남기 때문에 감사(Audit)와 이력 추적이 쉽다.
* **Resilience**: 외부 통신 실패나 장애가 발생해도 복구가 빠르고 안전하다.
* **Concurrency Control**: 업데이트 충돌(Deadlock, Lost Update) 위험이 줄어든다.
* **History Recovery**: 특정 시점 상태로 쉽게 롤백하거나 재구성할 수 있다.

특히 **외부 API 통신이 많거나**, **고가용성과 장애 복구가 중요한 시스템**에서는 이 방식이 굉장히 큰 장점을 가진다.

***

## How to Design Versioned Immutable Data

Versioned Immutable Data를 설계할 때 핵심은 **"변경"이 아닌 "추가"**&#xB97C; 기본 동작으로 만든다는 점이다.

### Table Design Example

```sql
CREATE TABLE order_status_history (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    order_number VARCHAR(50) NOT NULL,        -- 비즈니스 키
    status VARCHAR(20) NOT NULL,               -- 주문 상태
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP, -- 생성 시각
    version INT NOT NULL,                      -- 버전 번호
    UNIQUE (order_number, version)             -- 복합 유니크 키
);
```

* `order_number` : 주문을 식별하는 비즈니스 키
* `version` : 상태 변경될 때마다 증가하는 버전
* `status` : 주문의 상태 (ex: CREATED, PAID, SHIPPED 등)
* `created_at` : 레코드가 삽입된 시간

**포인트는** `UPDATE` 없이, **새로운 버전**을 계속 `INSERT`만 한다는 것이다.

***

## How to Query Latest Version

최신 상태를 가져오고 싶다면, 다음과 같이 쿼리할 수 있다.

```sql
SELECT *
FROM order_status_history
WHERE order_number = 'ORD123456'
ORDER BY version DESC
LIMIT 1;
```

또는 `created_at`을 기준으로 가져올 수도 있다.

***

## Additional Considerations

Versioned Immutable Data를 적용할 때 고려할 사항은 다음과 같다.

* **디스크 용량 증가**: 레코드 수가 많아질 수 있으므로 적절한 데이터 정리 정책 필요 (ex: 오래된 버전 아카이빙)
* **쿼리 최적화**: 최신 버전 조회 쿼리에 인덱스 추가 (`order_number`, `version`)
* **스냅샷 사용**: 이벤트가 많을 경우, 중간 스냅샷을 저장해 복원 성능 최적화 가능

***

## Transaction Impact and CQRS Combination

Versioned Immutable Data를 적용하면 트랜잭션 관리가 훨씬 가벼워진다.

* 기존 레코드를 수정하지 않고 새로운 레코드를 삽입하기 때문에, 동시성 문제가 거의 발생하지 않는다.
* 트랜잭션 충돌(Deadlock, Lost Update) 위험이 줄어들어 복잡한 트랜잭션 관리가 필요 없어지고, \
  트랜잭션을 짧고 단순하게 유지할 수 있다.

특히 CQRS 패턴과 결합할 경우, Command와 Query를 명확히 분리할 수 있다.

| 역할              | 동작             |
| --------------- | -------------- |
| Command Service | 새로운 버전을 INSERT |
| Query Service   | 최신 버전을 SELECT  |

Command Service는 항상 새로운 데이터를 삽입하고, \
Query Service는 최신 버전만 조회한다. \
따라서 두 서비스 간 충돌이나 복잡한 트랜잭션 처리가 필요 없어지고, \
시스템 확장성과 유지보수성이 크게 향상된다.

## Conclusion

Versioned Immutable Data는 **업데이트 없이 데이터를 쌓아가며** **복구성, 감사성, 장애내성**을 \
모두 갖출 수 있는 강력한 패턴이다.

특히 트랜잭션이 복잡하거나, 외부 시스템 통신이 빈번한 시스템에서 매우 유용하다.

추가적인 최적화(아카이빙, 인덱싱, 스냅샷)만 잘 설계하면 대규모 트래픽 시스템에서도 \
충분히 적용 가능한 현실적인 설계 방식이다.
