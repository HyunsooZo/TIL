---
description: >-
  Redis를 활용한 분산락 구현은 간단하면서도 강력한 동기화 솔루션을 제공한다.  본 포스팅에서는 Redis와 Spring AOP를 이용해
  분산락을 구현하는 방법과 코드 흐름을 설명하려 한다.
---

# 🔏 Redis : Distributed Lock

## Why Use Distributed Lock?

분산 환경에서 여러 애플리케이션 인스턴스가 동일한 리소스에 접근할 때 \
동기화 문제를 방지하기 위해 분산락이 필요하다. \
Redis는 빠른 성능과 TTL(Time To Live) 설정을 지원하여 이러한 락 구현에 적합하다.

## Implementation Overview

개인 프로젝트에서 Redis와 Spring AOP를 사용해 분산락을 구현 해보았다.\
`@DistributedLock` 어노테이션을 정의하고, \
AOP를 활용해 락 획득, 릴리즈 로직을 처리한다. \
Redis는 `saveIfAbsent` 메서드를 통해 락 획득을 시도하며, 키와 식별자를 저장한다.

### Common Use Cases

**Inventory Management**

여러 사용자가 동시에 재고를 조회하거나 주문할 때, 동일한 재고 수량에 대해 중복 차감이 발생하지 않도록 보장한다.

**Payment Processing**

결제 시스템에서 동일한 결제 요청이 여러 번 처리되지 않도록 방지한다.

**Job Scheduling**

분산 환경에서 동일한 작업(Job)이 여러 노드에서 중복 실행되지 않도록 제어한다.

**API Rate Limiting**

여러 클라이언트가 특정 API를 초과 호출하지 않도록 요청을 제한한다.

**Session Management**

다중 인스턴스 환경에서 동일한 사용자 세션 데이터를 동기화한다.

## Code Explanation

### DistributedLock Annotation

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface DistributedLock {
    String key();
    String identifier();
    int expireTime() default 10;
}
```

* **key**: Redis에서 락을 구분하기 위한 키.
* **identifier**: 락 소유자를 식별하기 위한 값.
* **expireTime**: 락의 만료 시간(초 단위).

### DistributedLockAspect

**주요 메서드**

1. `applyLock`: 어노테이션 적용 시 락을 획득하고 릴리즈까지 관리.
2. `evaluateExpression`: SpEL 표현식을 평가하여 동적으로 값 계산.
3. `tryAcquireLock`: Redis에 락 저장 시도.
4. `releaseLock`: Redis에서 락 해제.

```java
@Around("@annotation(distributedLock)")
public Object applyLock(
        ProceedingJoinPoint joinPoint,
        DistributedLock distributedLock
) throws Throwable {
    String key = evaluateExpression(distributedLock.key(), joinPoint);
    String identifier = evaluateExpression(distributedLock.identifier(), joinPoint);
    int expireTime = distributedLock.expireTime();

    boolean lockAcquired = tryAcquireLock(key, identifier, expireTime);
    if (!lockAcquired) {
        log.warn("Unable to acquire lock for key: {}", key);
        throw new IllegalStateException("Failed to acquire lock for key: " + key);
    }
    try {
        return joinPoint.proceed();
    } finally {
        releaseLock(key, identifier);
    }
}
```

## Flow

<figure><img src="../../.gitbook/assets/스크린샷 2025-01-05 오후 11.37.46.png" alt=""><figcaption></figcaption></figure>

## Considerations

* **TTL 설정**: 작업 시간이 만료 시간을 초과하지 않도록 주의해야 한다.
* **Race Condition**: 락 획득과 릴리즈 중 발생할 수 있는 경합을 최소화해야 한다.
* **Redis 가용성**: Redis 장애 시 시스템 동작을 보장하기 위해 대체 메커니즘을 고려해야 한다.

## Conclusion

Redis와 Spring AOP를 사용한 분산락은 효율적이고 구현이 간단하다. \
이 포스팅에서는 Redis의 기본적인 락 획득 및 릴리즈 메커니즘과 AOP를 활용한 분산락 구현 방법을 다뤘다. \
실제 운영 환경에서는 Redis의 가용성과 TTL 설정에 주의하여 안정적인 시스템을 설계해야 한다.
