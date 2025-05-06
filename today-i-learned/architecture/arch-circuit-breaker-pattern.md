# ⭕ Arch : Circuit Breaker Pattern

## Circuit Breaker?

서킷 브레이커(Circuit Breaker)는 마이크로서비스 아키텍처나 외부 API 통신 환경에서, \
**지속적인 실패를 감지하고 시스템 전체로의 장애 확산을 막기 위해 사용하는 보호 패턴**이다.

기본 원리는 전기 회로 차단기와 비슷하다. \
외부 시스템의 오류가 일정 횟수를 넘어서면 "회로를 끊어" 더 이상 해당 시스템을 호출하지 않게 된다. \
이로 인해 전체 시스템 자원을 보호하고, 응답 지연이나 장애 전파를 막을 수 있다.

즉, 외부 시스템에서 문제가 발생했을 때, 우리 시스템이 그것에 계속 의존하고 있으면\
&#x20;전체 서비스가 느려지거나 멈출 수 있다. \
이를 방지하기 위해 일정 횟수 이상의 오류가 발생하면 외부 시스템과의 연결을 끊고, \
일정 시간 후 다시 연결을 시도해본다. 이 과정에서 시스템 전체의 탄력성(resilience)이 향상된다.

## State Diagram

<figure><img src="../../.gitbook/assets/스크린샷 2025-05-06 15.09.47.png" alt=""><figcaption></figcaption></figure>

### Why Use It?

### **Prevent cascading failures**

* 외부 시스템이 응답하지 않더라도 내부 시스템 리소스를 보호할 수 있음
* DB Connection Pool, Thread Pool 고갈 등의 상황 예방

### **Fail Fast**

* 실패가 뻔한 요청을 빠르게 차단하여 사용자 응답 속도 개선
* 사용자는 무한 로딩 상태가 아니라 빠르게 오류 메시지를 받게 됨

### **Self-healing**

* 일정 시간 후 재시도하여 시스템이 복구되었는지 확인 가능
* 상태 기반 로직으로 자동 복구 흐름을 기대할 수 있음

### In Practice

```java
@CircuitBreaker(name = "tossPayment", fallbackMethod = "fallback")
public PaymentResult pay(PaymentRequest req) {
    return tossPaymentAdapter.execute(req);
}

public PaymentResult fallback(PaymentRequest req, Throwable t) {
    log.warn("결제 실패 - fallback 처리: {}", t.getMessage());
    return PaymentResult.fail("결제 서비스가 일시적으로 불안정합니다.");
}
```

### Spring Boot + Resilience4j&#x20;

```yaml
resilience4j:
  circuitbreaker:
    instances:
      tossPayment:
        failure-rate-threshold: 50
        wait-duration-in-open-state: 10s
        permitted-number-of-calls-in-half-open-state: 3
        minimum-number-of-calls: 5
        sliding-window-size: 10
        sliding-window-type: COUNT_BASED
```

#### 의미

* 실패율 50% 이상이면 회로 차단 (Open)
* Open 상태로 10초 기다린 뒤 HalfOpen
* HalfOpen 상태에서는 3개의 요청만 허용
* 최근 10개의 호출 기준으로 성공/실패 판단

## When to Avoid

* **단순한 내부 메서드 호출에는 불필요**
  * 내부 연산 로직에서 실패가 나더라도 회로 차단은 오히려 복잡도만 높임
* **성공/실패 기준이 애매한 경우**
  * 예: 느린 응답은 실패로 볼 것인가? HTTP 5xx만 실패인가?
  * 실패 조건이 명확하지 않으면 예측할 수 없는 동작 유발 가능
* **Fallback이 진짜 안전한지 검증 필요**
  * fallback이 단순 로그만 찍는다면 실제 사용자 경험은 나아지지 않음

## Conclusion

Circuit Breaker는 단순한 예외 처리를 넘어서, \
**시스템 안정성과 사용자 경험을 동시에 지키기 위한 핵심 설계 전략**이다. \
특히 외부 의존성이 많은 시스템에서 반드시 고려해야 하며, \
Spring Boot에서는 Circuit Breaker 패턴을 직접 구현하는 것보다는, \
**Resilience4j와 같은 라이브러리를 사용하는 것이 대부분의 경우 더 안전하고 생산적이다.**\
특히 마이크로서비스 구조에서는 구성과 모니터링 측면에서도 표준화된 라이브러리의 장점이 크다.
