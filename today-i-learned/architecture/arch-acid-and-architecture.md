---
description: 백엔드 공부를 하다보면 기초단계에서 반드시 나오는 개념인 ACID. 실무에서 ACID를 보장하는 것은 생각보다 "많이" 중요하다.
---

# 🗿 Arch : ACID and Architecture

## ACID?

ACID는 데이터베이스 트랜잭션에서 **Atomicity, Consistency, Isolation, Durability**의 네 가지 특성을 의미한다. \
이는 **데이터의 무결성과 안정성을 보장하기 위한 핵심 개념**이다.

* **Atomicity (원자성)**\
  트랜잭션은 모두 수행되거나 전혀 수행되지 않아야 한다. 중간 실패는 허용되지 않는다.
* **Consistency (일관성)**\
  트랜잭션이 실행되기 전과 후에 데이터는 항상 정해진 규칙(제약조건, 외래키 등)을 만족해야 한다.
* **Isolation (격리성)**\
  동시에 실행되는 트랜잭션은 서로 간섭하지 않아야 하며, 독립적으로 수행되어야 한다.
* **Durability (지속성)**\
  트랜잭션이 성공적으로 완료되면, 그 결과는 영구적으로 저장되어야 한다.

***

## ACID-aware Architecture Design

아키텍쳐 설계 시 ACID를 고려한다는 것은 단순히 RDB를 사용한다는 의미를 넘어 \
**트랜잭션이 보장되어야 할 경계와 책임을 명확히 나누는 것**을 의미한다.

**어떻게 고려할 수 있을까?**

1. **도메인 주도 설계(DDD)** 를 통해 트랜잭션 경계를 명확히 나눈다.
   * 하나의 Aggregate는 단일 트랜잭션 내에서 처리되어야 한다.
   * 다른 Aggregate 간에는 eventually consistent(최종 일관성)을 허용할 수 있다.
2. **CQRS 분리 적용**
   * Command(쓰기)는 강한 일관성, Query(읽기)는 eventual consistency로 유연하게 분리 가능하다.
3. **SAGA 패턴 도입**
   * 마이크로서비스처럼 분산 시스템에서는 분산 트랜잭션이 어렵기 때문에, \
     보상 트랜잭션으로 일관성을 유지하는 SAGA 패턴을 고려한다.
4. **Fail-fast 원칙**
   * 트랜잭션 실패 가능성이 높은 요소는 초기에 검증하여 리소스 낭비를 줄인다.

***

## ACID In Spring

Spring 프레임워크에서는 ACID를 보장하기 위해 다양한 기술을 지원한다.

### **1. @Transactional**

* 메서드 단위 트랜잭션 적용
* 선언적 트랜잭션 관리로 코드 간결화
* `REQUIRES_NEW`, `NESTED` 등의 propagation 설정으로 트랜잭션 분리/중첩 가능

```java
@Service
@RequiredArgsConstructor
public class OrderService {

    private final OrderRepository orderRepository;
    private final PaymentService paymentService;

    @Transactional
    public void placeOrder(OrderRequest request) {
        Order order = new Order(request.getUserId(), request.getItems());
        orderRepository.save(order);

        paymentService.pay(order); // 내부 호출도 같은 트랜잭션 내에서 동작
    }
}

@Service
public class PaymentService {

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void pay(Order order) {
        // 결제 처리 및 예외 발생 시 트랜잭션 분리
    }
}
```

### **2. Entity vs Domain**

* **Entity는 JPA를 위한 데이터 영속 객체**, DB 테이블에 매핑
* **Domain은 도메인 로직 중심의 모델**로, 영속성보다 **비즈니스 불변성**에 집중
* DDD에선 Entity 내부에 비즈니스 로직 포함 or Domain Model로 감싸는 방식 사용

```java
@Entity
public class Order {

    @Id
    @GeneratedValue
    private Long id;

    @Enumerated(EnumType.STRING)
    private OrderStatus status;

    public void complete() {
        if (this.status != OrderStatus.CREATED) {
            throw new IllegalStateException("이미 완료된 주문입니다.");
        }
        this.status = OrderStatus.COMPLETED;
    }
}
```

### **3. ACIDtic Domain Archtecture**

* 하나의 트랜잭션 경계 내에서 변경되어야 할 데이터는 하나의 Aggregate로 묶기
* `@Transactional`을 Application Layer 또는 \
  UseCase에 명확히 적용하여 도메인 계층은 트랜잭션을 의존하지 않게 한다
* 외부 시스템 호출은 트랜잭션 외부에서 수행하거나 보상 트랜잭션 처리

***

## Conclusion

ACID는 단순히 트랜잭션에 국한된 개념이 아니라, \
**도메인 설계와 아키텍쳐 설계 전반에 걸쳐 고려해야 하는 핵심 원칙**이다. \
스프링에서는 `@Transactional`을 중심으로 이를 잘 활용할 수 있으며, \
DDD나 CQRS 같은 아키텍쳐 패턴을 병행함으로써 현실적이고 유연하게 ACID를 달성할 수 있다.
