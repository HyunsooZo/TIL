---
description: >-
  오늘 회사에서 직장 동료가 리뷰해보자고 보내준 코드이다. 나는 upsert , 동료를 @Transactional 미동작에 대해 말했다. 너무
  궁금해서 집에와서 chatGPT에게 리뷰를 요청 해보았고, 아래와 같은 답변을 받았다. 결론적으로 두 의견 모두 포함되어있지만, 생각지 못한
  부분이 대부분이어서 기록해본다.
---

# 😕 Java : How Would You Review This?

## Code Overview

```java
@Component
@RequiredArgsConstructor
public class OrderApplication {

    private final OrderService orderService;
    private final OrderResultPublisher publisher;

    public void orderGenerateWithPublish(OrderVo orderVo) {
        OrderMessage message = this.generateOrder(orderVo);

        // 비동기 Queue 발행
        publisher.send(message);
    }

    @Transactional
    public OrderMessage generateOrder(OrderVo orderVo) {
        // 중복 주문 제거
        if (orderService.existsByOrderNumber(orderVo.getOrderNumber())) {
            orderService.deleteByOrderNumber(orderVo.getOrderNumber());
        }

        Order order = orderService.save(orderVo.toEntity());
        return OrderMessage.from(order);
    }
}
```

***

## Separation of Responsibilities

OrderApplication 클래스는 유즈케이스 실행과 도메인 저장 로직을 함께 갖고 있다.\
`generateOrder()`에 @Transactional이 선언되어 있어 DB 처리 책임이 애플리케이션 계층에 위치한다.\
설계 철학에 따라 도메인 서비스로 save/delete 위임하고 \
Application은 orchestrator 역할에 집중하는 게 나을 수 있다.

즉, save/delete 로직은 도메인 서비스로 옮기고, Application은 순서 제어만 담당하도록 분리

***

## Transaction Boundary Management

`generateOrder()`는 삭제 → 저장 → 메시지 생성까지 하나의 트랜잭션으로 묶여 있지만,\
`orderGenerateWithPublish()`는 트랜잭션 외부에서 메시지를 발행한다. \
따라서 메시지 발송 실패는 롤백되지 않는다.

즉,  메시지 발행 시점이 트랜잭션 이후여야 한다면 의도된 설계인지 확인 필요\
커밋 이후 보장을 위해 `@TransactionalEventListener` 또는 Outbox 패턴 사용 고려

***

## Atomicity of Deduplication

`exists → delete → save` 순서의 로직은 동시성 이슈가 발생할 수 있다.\
동시에 두 요청이 들어오면 둘 다 exists 조건을 통과할 수 있기 때문.

즉,  하나의 SQL 로직(upsert) 또는 DB unique 제약 조건으로 해결하는 것이 안정적

***

## Method Naming Clarity

`orderGenerateWithPublish()`는 기능은 전달하지만 도메인 의도를 표현하진 못한다.

`placeOrder()`, `processOrderAndNotify()` 등 도메인 의미에 맞는 명칭으로 변경

***

## Clarity of Publishing Timing

메시지 발행 시점이 트랜잭션 이후인데, \
이게 비즈니스 상 중요한 제약인지 여부가 드러나 있지 않다.\
만약 저장된 주문만 발행되어야 한다면 이 흐름은 좀 더 명시적이어야 한다.

즉, Outbox 패턴 등으로 커밋 이후의 발행을 확실히 보장하는 구조 고려

***

## Conclusion

ChatGPT로 리뷰를 요청한 결과 내가 말한 내용과 동료가 말한 내용 모두 들어있었다.\
하지만 내가 실제로 이런 질문을 받았을때 바로 위와 같은 리뷰를 할 수 있을까?\
단순히 코드를 읽고 내 스타일에 맞춰 리뷰 하는 것 보다 더 넓은 시각에서의 리뷰가 필요함을 느꼈다.
