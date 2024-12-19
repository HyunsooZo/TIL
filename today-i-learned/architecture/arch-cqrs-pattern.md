---
description: 요즘 개인 프로젝트에 도입해보고 있는 CQRS 패턴에 대해 포스팅 해보았다.
---

# ✍️ Arch : CQRS pattern

## CQRS?

CQRS(Command Query Responsibility Segregation)는 명령과 조회의 책임을 분리하는 패턴이다. \
이 패턴은 시스템의 성능과 확장성을 높이면서 복잡한 비즈니스 로직을 더 명확하게 구조화하는 데 도움을 준다.

***

## Key Concepts

CQRS는 크게 두 가지 핵심 개념으로 나뉘는데

* **Command**: 데이터를 변경하는 작업 (Create, Update, Delete).
* **Query**: 데이터를 조회하는 작업 (Read).

이 두 가지 책임을 분리하면 각 작업에 맞게 최적화할 수 있다.

***

## Why CQRS?

CQRS를 적용하는 주요 이유는 다음과 같다:

* **책임 분리**: 쓰기와 읽기 로직이 분리되면 유지보수가 쉬워진다.
* **성능 최적화**: 조회 성능과 쓰기 성능을 개별적으로 최적화할 수 있다.
* **확장성**: 읽기와 쓰기 시스템을 독립적으로 확장할 수 있다.
* **유연성**: 데이터 모델을 읽기용과 쓰기용으로 다르게 설계할 수 있다.

***

## Flow&#x20;

<figure><img src="../../.gitbook/assets/스크린샷 2024-12-19 오후 3.10.17.png" alt=""><figcaption></figcaption></figure>

***

## Command Service

Command Service는 데이터를 변경하는 책임을 가져. 비즈니스 로직과 트랜잭션 관리가 여기에 포함된다.

```java
@Service
public class OrderCommandService {
    @Transactional
    public void createOrder(OrderCreateRequest request) {
        // 데이터 생성 및 비즈니스 로직
    }
}
```

***

## Query Service

Query Service는 데이터를 조회하는 책임을 가진다. \
읽기 전용이므로 성능 최적화를 위해 캐시나 별도의 읽기 전용 데이터베이스를 사용할 수도 있다.

```java
@Service
public class OrderQueryService {
    public List<OrderResponse> getOrders() {
        // 데이터 조회 로직
        return orderRepository.findAll();
    }
}
```

***

## When to Use CQRS?

다음과 같은 상황에서 CQRS를 적용하는 게 좋:

* **읽기/쓰기 성능 차이가 클 때**\
  읽기 작업이 매우 빈번하고 쓰기 작업이 드문 경우.
* **확장성이 필요한 경우**\
  읽기 시스템과 쓰기 시스템을 독립적으로 확장해야 할 때.
* **복잡한 도메인 로직**\
  비즈니스 로직이 복잡해서 읽기와 쓰기 모델을 다르게 관리해야 할 때.
* **유연한 데이터 모델**\
  쓰기 작업과 읽기 작업의 데이터 구조가 다를 필요가 있을 때.

***

## Considerations

CQRS를 적용할 때 주의해야 할 점은 다음과 같다:

* **복잡도 증가**: 시스템 구조가 분리되면서 초기 설계와 구현이 복잡해질 수 있다.
* **데이터 일관성**: Command와 Query DB를 물리적으로 분리하면 동기화 지연(최종적 일관성)이 발생할 수 있다.
* **동기화 관리**: 데이터 변경 사항을 읽기 전용 데이터베이스로 동기화하는 전략이 필요하다.

***

## Conclusion

CQRS는 명령과 조회의 책임을 분리하여 시스템의 성능과 확장성을 극대화할 수 있는 강력한 패턴이다. \
하지만 무조건 적용하기보다는 프로젝트의 규모와 필요에 따라 적절히 도입하는 게 중요하다.
