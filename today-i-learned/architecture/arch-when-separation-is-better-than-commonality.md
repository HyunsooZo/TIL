---
description: >-
  Whe개발할 때 우리는 항상 "중복을 없애자(DRY)"는 원칙을 배우고, 실천하려고 노력한다. 하지만 모든 상황에서 공통화가 최선일까?
  때로는 중복보다 분리가 훨씬 나은 경우도 분명히 존재한다.  이번 포스팅에서는 "공통화의 함정" 과, "분리의 용기" 가 필요한 순간에 대해
  알아보았다.
---

# 🔪 Arch : When Separation is Better Than Commonality

## The Trap of Premature Generalization

> **너무 이른 공통화가 낳는 문제**

### 너무 이른 공통화가 낳는 문제들

* **불필요한 복잡성 증가**\
  공통 코드를 맞추기 위해 억지로 추상화하면, 오히려 읽기 어렵고 관리하기 힘들어진다.
* **서로 다른 변화 요구를 억지로 묶음**\
  비슷해 보이는 기능도 시간이 지나면 다른 방향으로 진화하게 된다.\
  이때 공통화한 부분이 발목을 잡기 시작한다.
* **사소한 수정도 대규모 영향**\
  한쪽의 요구사항 변경이 다른 쪽에도 영향을 미쳐,\
  수정이 대규모 리스크를 동반하게 된다.

***

## When Separation is Wiser

> **다음과 같은 상황에서는 "분리"가 더 현명한 선택이다**

#### 1. 사용 목적과 변화 방향이 다를 때

처음엔 비슷해 보여도, 실제 운영에서는 다르게 진화할 가능성이 있다.\
요구사항이 다르면, 코드도 분리해야 한다.

#### 2. 복잡한 조건을 강제로 끼워 맞출 때

if, switch가 지나치게 많아진다면, 오히려 분리하는 게 훨씬 명확하다.

#### 3. 리스크 관리가 중요할 때

공통 모듈 하나가 여러 서비스에 물려있을 때,\
작은 수정도 여러 서비스에 리스크를 주기 쉽다.\
이럴 땐 분리하는 게 안전하다.

#### 4. 최적화 포인트가 다를 때

배치 처리와 실시간 처리는 요구하는 성능과 로직이 다르다.\
공통화하면 둘 다 망칠 수 있다.

***

## Real Example

```java
public class PaymentProcessor {
    public void process(Payment payment) {
        if (payment.type() == Type.CREDIT_CARD) {
            // 신용카드 처리
        } else if (payment.type() == Type.BANK_TRANSFER) {
            // 계좌이체 처리
        } else if (payment.type() == Type.BITCOIN) {
            // 비트코인 처리
        }
        // 추가 타입들...
    }
}
```

**문제점**: 새로운 타입 추가, 정책 변경이 반복될수록 if-else 지옥.

개선 방법

```java
public interface PaymentHandler {
    void handle(Payment payment);
}

public class CreditCardPaymentHandler implements PaymentHandler { ... }

public class BankTransferPaymentHandler implements PaymentHandler { ... }

public class BitcoinPaymentHandler implements PaymentHandler { ... }
```

**타입별로 책임을 분리**해서 관리하면 유연하고 견고한 구조가 된다.

***

## How to Decide: 공통화 vs 분리 기준

| 질문                            | YES라면 공통화, NO라면 분리 |
| ----------------------------- | ------------------ |
| 두 기능이 본질적으로 같은가?              | 공통화 가능             |
| 앞으로도 항상 비슷한 방향으로 진화할 것인가?     | 공통화 가능             |
| 수정 시 양쪽 모두 문제 없이 적용될 자신이 있는가? | 공통화 가능             |
| 아니라면?                         | **과감히 분리하자**       |

***

## Conclusion

* 공통화는 좋지만 만능이 아니다.
* 이른 공통화는 기술 부채가 된다.
* 진짜 필요할 때 합치는 게 더 좋다.
* "처음부터 완벽한 공통화" 같은 건 환상이다.. **처음엔 단순하게, 나중에 필요하면 통합!**\
  괜히 '비슷해 보인다'는 이유로 묶다가 나중에 **한 번에 터지는 경험**… 내가 지금 하고있다..
