# 🔌 Arch : Adapter Pattern

## Adapter Pattern?

Adapter 패턴은 **기존의 인터페이스를 클라이언트가 기대하는 형태로 변환해주는 디자인 패턴**이다. \
주로 서로 다른 시스템이나 라이브러리를 통합할 때 유용하게 사용되며, \
**호환되지 않는 인터페이스 간의 연결 고리 역할**을 한다.

Adapter는 GoF(Gang of Four) 디자인 패턴 중 구조(Structural) 패턴에 해당하며, \
"이미 존재하는 코드를 변경하지 않고 재사용할 수 있도록 도와주는 도우미"라고도 볼 수 있다.

즉, **외부 API와의 통신, 3rd-party 라이브러리 사용, 시스템 간 연동** 같은 책임을 가진 모듈을 직접 서비스에서 호출하지 않고, 중간에 어댑터 역할을 하는 계층을 둬서 간접적으로 사용한다는 의미다. 이 방식은 DIP(의존 역전 원칙)과도 연결되며, 아키텍처의 결합도를 낮추는 데 굉장히 효과적이다.

나는 주로 Gateway를 어댑터로 감싸는 편이다.  (타 도메인 application 레이어에서 주입받기 위해..)

***

## Adapter Pattern&#x20;

<figure><img src="../../.gitbook/assets/스크린샷 2025-05-04 15.12.39.png" alt=""><figcaption></figcaption></figure>

* `Client`: 기대하는 인터페이스 (`Target`)만 알고 있음
* `Target`: 클라이언트가 의존하는 추상화된 인터페이스
* `Adaptee`: 실제 기능을 제공하지만 기대와는 다른 인터페이스를 가진 객체
* `Adapter`: `Target`을 구현하고 내부적으로 `Adaptee`를 호출함

***

## In Practice

외부 API를 감싸는 Gateway를 Adapter로 사용하는 경우

```java
public interface PaymentAdapter {
    PaymentResult execute(PaymentRequest request);
}

public class TossPaymentAdapter implements PaymentAdapter {

    private final TossPaymentGateway tossGateway;

    public TossPaymentAdapter(TossPaymentGateway tossGateway) {
        this.tossGateway = tossGateway;
    }

    @Override
    public PaymentResult execute(PaymentRequest request) {
        return tossGateway.requestPayment(request);
    }
}

public class TossPaymentGateway implements PaymentGateway {

    private final TossPaymentClient client;

    public TossPaymentGateway(TossPaymentClient client) {
        this.client = client;
    }

    @Override
    public PaymentResult requestPayment(PaymentRequest request) {
        TossRequest tossReq = mapToTossRequest(request);
        TossResponse tossRes = client.pay(tossReq);
        return mapToPaymentResult(tossRes);
    }

    // ..
}

```

#### 이점

* 외부 API 변경 시 Adapter만 수정하면 됨
* 테스트 시 Mock Gateway를 주입해 테스트 가능
* 서비스 레이어는 외부 구현에 전혀 의존하지 않음

***

## When to Use the Adapter Pattern i

카카오페이, 네이버페이, 토스 등 다양한 결제 수단을 하나의 인터페이스(`PaymentGateway`)로 감싼 다고 치자. \
초기에는 if-else로 분기할 수 있지만 늘어날수록  유지보수 지옥이 펼쳐진다. \
이후 각 구현체를 Adapter로 만들고 DI로 주입하게 되면 코드가 명확해지고 확장도 쉬워진다.

즉,

* _외부 시스템이나 라이브&#xB7EC;_&#xB9AC;와의 통신이 있을 때
* 기존 코드(레거시)를 새로운 코드와 연결해야 할 때
* 테스트 가능한 구조를 만들고 싶을 때
* 클린 아키텍처에서 **Interface Adapter Layer** 를 구현할 때

어댑터 패턴을 쓴다면 유지보수에 도움이 될 수 있다.

***

## Summary

<table data-header-hidden><thead><tr><th width="155.2890625"></th><th></th></tr></thead><tbody><tr><td>항목</td><td>설명</td></tr><tr><td>목적</td><td>호환되지 않는 인터페이스를 연결</td></tr><tr><td>위치</td><td>Gateway, Infra Layer, Legacy Wrapper 등</td></tr><tr><td>이점</td><td>결합도 감소, 테스트 용이성, 유지보수성 증가</td></tr><tr><td>관련 원칙</td><td>DIP, OCP, SRP</td></tr></tbody></table>

Adapter 패턴은 단순한 구조 패턴을 넘어서, 실전 아키텍처 설계에서 \
외부 의존성과 비즈니스 로직을 분리하는 **핵심 전략**이 된다.&#x20;

***

## Conclusion

처음엔 그냥 외부 API 붙이는 데만 집중했는데, 점점 코드가 얽히고 테스트가 어려워지면서\
Adapter가 왜 필요한지 절실히 느끼게 됐다. \
Adapter를 만들고 나니 확장도 쉬워지고 테스트 코드도 깔끔해져서, \
금은 외부 의존성 있는 모듈은 무조건 어댑터로 감싸는 습관이 생겼음. \
단, 초반에 추가 공수가 들어간다는 점에 있어서 호불호가 많이 갈릴 패턴임은 분명하다.
