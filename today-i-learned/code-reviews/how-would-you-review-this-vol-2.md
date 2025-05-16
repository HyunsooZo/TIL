---
description: >-
  Microsoft에서 공개한 CQRS Journey Sample Application 중 주문 등록 흐름(Command side)의 구조를
  바탕으로, CQRS 패턴의 전형적인 형태를 갖춘 코드에 대한 리뷰를 진행 해보았다... 내가 감히..(?)
---

# 😕 How Would You Review This? - vol 2

## Code Overview

```java
public class PlaceOrderCommandHandler implements CommandHandler<PlaceOrderCommand> {

    private final OrderRepository orderRepository;
    private final OrderFactory orderFactory;
    private final EventBus eventBus;

    public PlaceOrderCommandHandler(OrderRepository orderRepository, OrderFactory orderFactory, EventBus eventBus) {
        this.orderRepository = orderRepository;
        this.orderFactory = orderFactory;
        this.eventBus = eventBus;
    }

    @Override
    public void handle(PlaceOrderCommand command) {
        Order order = orderFactory.createOrder(command.customerId(), command.items());
        orderRepository.save(order);

        eventBus.publish(new OrderPlacedEvent(order.getId(), order.getTotalAmount()));
    }
}
```

***

## CQRS Structure - Good

* Command와 Query가 완전히 분리되어 있고, CommandHandler가 명확하게 책임을 가지고 있다.
* 도메인 모델(Order)은 외부에서 생성되지 않고, `OrderFactory`를 통해 생성되도록 되어 있어 불변성과 생성 규칙을 보장한다.

명령-쿼리 책임 분리를 제대로 실천하고 있음. 도메인 생성도 전용 Factory에서 처리됨

***

## Unclear Handling of Side Effects and Transaction Boundaries

* `eventBus.publish()`가 save 이후에 바로 호출되고 있지만, 트랜잭션 경계에 대한 설명이 없다.
* publish가 외부 시스템(MQ 등)과 연계될 경우, **DB 커밋 전에 이벤트가 발송될 수 있음** → 데이터 불일치 위험

이벤트 발행을 트랜잭션 commit 이후로 처리하거나, Outbox 패턴 도입 필요

***

## Responsibility Coupling and Overloaded Handler

* `handle()` 내부에서 도메인 생성 + 저장 + 이벤트 발행까지 모두 처리하고 있다.
* CQRS 특성상 CommandHandler는 단순 흐름을 제어하는 정도가 적절하며,\
  복잡한 정책이 들어간다면 UseCase 레이어나 Application Service로 분리하는 것이 더 유연한 설계다.

CommandHandler는 UseCase나 CommandService를 호출하도록 얇게 두고, 책임 분리 가능성 고려

***

## Event Publication Without Clear Subscription Handling

* `eventBus.publish(...)`는 발행만 할 뿐, \
  어떤 구독자에게 어떤 순서로 이벤트가 전달되는지는 이 코드만으로는 알 수 없다.
* CQRS에서는 Query 모델 갱신을 위한 이벤트 핸들러가 필요함

이벤트 구독자(EventHandler)와 Projection을 어떤 방식으로 구현하는지 함께 리뷰되어야 전체 흐름 파악 가능

***

## Conclusion

CQRS Journey 프로젝트의 코드 구조는 **CQRS 아키텍처 구현의 대표적 예시로 꼽힌다.**\
Command-Query 분리, 도메인 중심의 생성 방식, 이벤트 발행 등의 흐름은 잘 반영되어 있다.\
다만, 트랜잭션 경계와 이벤트 전파의 안정성을 확보하는 설계가 더 명확히 드러나면 전체적인 신뢰성이 높아질 것 같다.

다음 리뷰에서는 이 CommandHandler와 연결된 QueryHandler, Projection 구현 구조도 함께 살펴보자.
