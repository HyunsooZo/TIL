---
description: >-
  프로그래밍을 처음 배울 때 SOLID 원칙을 접했다. 하지만 1년 넘게 실무를 경험하고 나니, 그때 배운 SOLID와 지금 내가 이해하는
  SOLID는 전혀 다르게 느껴진다. 오늘은 다시 한번 SOLID 원칙을 정리하며, 실무에서 체득한 관점과 함께 그 의미를 돌아보려 한다.
---

# ◻️ Arch : SOLID Principles

## What is SOLID?

SOLID는 객체지향 설계에서 매우 중요한 다섯 가지 원칙의 앞 글자를 딴 용어로, \
소프트웨어 설계의 유연성과 유지보수성을 높이기 위한 핵심 개념이다. \
특히 백엔드 시스템에서는 다양한 도메인 요구사항과 외부 시스템과의 연동, \
빈번한 기능 변경과 확장이 발생하는데, \
이러한 상황에 효과적으로 대응하기 위해 SOLID 원칙의 이해와 적용은 필수적이다.

SOLID는 다음 다섯 가지 원칙으로 구성된다:

* **S**: Single Responsibility Principle (단일 책임 원칙)
* **O**: Open/Closed Principle (개방/폐쇄 원칙)
* **L**: Liskov Substitution Principle (리스코브 치환 원칙)
* **I**: Interface Segregation Principle (인터페이스 분리 원칙)
* **D**: Dependency Inversion Principle (의존 역전 원칙)

***

## Single Responsibility Principle (SRP)

> 클래스는 변경의 이유가 오직 하나뿐이어야 한다.

단일 책임 원칙은 클래스나 모듈이 오직 하나의 역할만을 가져야 하며, \
그 역할이 바뀌는 이유도 하나만 존재해야 한다는 원칙이다. \
"책임"이라는 단어는 단순히 메서드의 수나 기능의 수를 의미하는 것이 아니라, \
해당 모듈이 변경되어야 할 이유, 즉 비즈니스 요구사항 변경의 이유를 말한다.

### Common Mistake

* 하나의 서비스 클래스에 등록/조회/삭제/알림 전송까지 다 들어있던 코드에서, \
  변경할 때마다 의도치 않은 사이드 이펙트가 발생할 수 있다.\
  이후 알림 전송 로직을 별도 NotificationService로 분리하고 나면 테스트와 유지보수가 수월해 진다.

### Code

```java
// Bad Example
public class UserService {
    public void registerUser(User user) {
        saveToDB(user);
        sendEmail(user);
    }
}

// Better Separation
public class UserRegistrationService {
    private final UserRepository userRepository;
    private final EmailSender emailSender;

    public void register(User user) {
        userRepository.save(user);
        emailSender.send(user);
    }
}
```

***

### Open/Closed Principle (OCP)

> 소프트웨어 구성요소는 확장에는 열려 있어야 하고, 변경에는 닫혀 있어야 한다.

확장은 가능하되 기존 코드는 수정하지 않도록 설계하는 것이 핵심이다. \
즉, 새로운 기능을 추가할 수 있도록 구조화하되 기존 클래스의 코드를 변경하지 않아야 한다. \
이를 위해 보통 **다형성(polymorphism)** 과 **추상화(abstraction)** 을 적극 활용한다.

### Common Mistake

* 할인 정책이 바뀔 때마다 기존 Service 클래스를 직접 수정하다 보면,\
  QA에서 기존 로직이 깨지는 문제가 반복될 수 있다.\
  &#x20;인터페이스 기반으로 분리하고 DI 구조로 바꾸면서 \
  새로운 정책을 추가하더라도 기존 코드는 손대지 않게 할 수 있다.

### Code

```java
// Interface
public interface DiscountPolicy {
    int discount(User user, int price);
}

public class FixDiscountPolicy implements DiscountPolicy {
    public int discount(User user, int price) {
        return 1000;
    }
}

public class RateDiscountPolicy implements DiscountPolicy {
    public int discount(User user, int price) {
        return price * 10 / 100;
    }
}

// Service - 확장에는 열려있고, 변경에는 닫힘
public class OrderService {
    private final DiscountPolicy discountPolicy;

    public OrderService(DiscountPolicy discountPolicy) {
        this.discountPolicy = discountPolicy;
    }
}
```

***

## Liskov Substitution Principle (LSP)

> 프로그램의 객체는 부모 타입의 객체로 바꿔치기해도 실행에 문제가 없어야 한다.

서브 클래스는 반드시 부모 클래스의 계약(규약)을 준수해야 하며, \
부모 객체가 사용되는 모든 곳에 서브 객체를 대신 사용해도 동작에 문제가 없어야 한다는 원칙이다. \
이 원칙이 깨지면 상속을 통한 확장이 오히려 위험해진다.

### Common Mistake

* 어떤 특정 구현체가 예외 케이스에서 부모와 다른 방식으로 동작하도록 오버라이딩하면서, \
  부모 타입으로 사용하던 코드에서 버그가 발생 할 수 있다.\
  결국 타입 체크로 흐름을 제어하는 코드가 생겨나면서 OCP도 깨지는 결과가 된다.

### Code

```java
class Rectangle {
    int width, height;
    void setWidth(int w) { width = w; }
    void setHeight(int h) { height = h; }
    int area() { return width * height; }
}

class Square extends Rectangle {
    void setWidth(int w) {
        width = height = w;
    }
    void setHeight(int h) {
        width = height = h;
    }
}
```

***

## Interface Segregation Principle (ISP)

> 클라이언트는 자신이 사용하지 않는 인터페이스에 의존해서는 안 된다.

하나의 인터페이스가 너무 많은 책임을 가지고 있다면, \
이를 사용하는 클라이언트는 자신이 사용하지 않는 메서드까지 구현하거나 참조해야 한다. \
이는 변경에 취약한 구조를 만들며, 결합도를 높인다. \
ISP는 인터페이스를 작고 명확하게 쪼개서, 클라이언트에게 꼭 필요한 기능만 제공하도록 유도한다.

### Common Mistake

* 페이먼트 모듈에서 `PaymentProcessor`라는 하나의 인터페이스가 너무 많은 기능을 갖고 있어서, 간단한 조회 API도 결제 기능까지 의존해야 했다. 이후 기능별로 인터페이스를 나눠서 분리 개발이 훨씬 쉬워졌다.

### Code

```java
// Bad
interface Worker {
    void work();
    void eat();
}

class Robot implements Worker {
    public void work() {}
    public void eat() {} // 의미 없음
}

// Good - 분리
interface Workable { void work(); }
interface Eatable { void eat(); }

class Robot implements Workable {
    public void work() {}
}
```

***

## Dependency Inversion Principle (DIP)

> 고수준 모듈은 저수준 모듈에 의존하면 안 되며, 둘 다 추상화에 의존해야 한다.

비즈니스 로직이 담긴 고수준 모듈이 구체적인 구현(저수준 모듈)에 의존하게 되면, \
구현 변경에 따라 전체 시스템이 흔들릴 수 있다. \
DIP는 이 의존성을 추상화로 대체하여 유연한 구조를 만든다.

### Common Mistake

* NotificationService가 EmailSender에 직접 의존하고 있어서, \
  카카오 알림톡이나 SMS로 확장할 수 없었다. \
  인터페이스 추출 후 구현체를 분리하고 DI 구조로 변경하면서 확장성이 크게 올라갔다.

### Code

```java
interface NotificationSender {
    void send(String message);
}

class EmailSender implements NotificationSender {
    public void send(String message) {
        // 이메일 발송 로직
    }
}

class NotificationService {
    private final NotificationSender sender;

    public NotificationService(NotificationSender sender) {
        this.sender = sender;
    }

    public void notifyUser(String message) {
        sender.send(message);
    }
}
```

***

## SOLID Application in Practice

OCP와 DIP 같은 원칙은 지나치게 이른 최적화나 추상화로 오용될 수 있다. \
"모든 변경을 예측해서 대비한다"는 건 현실적으로 불가능하다. \
실제로는 다음과 같은 방식이 더 유용하다:

* **실제 변화가 일어난 후에 구조를 개선**하자.
* **유사한 변경이 반복될 경우**에만 추상화하자.
* 고객이 요청한 기능을 빠르게 제공하고, 그 변화의 패턴을 관찰하자.

즉, **변화를 경험한 후 그에 대비하는 구조를 만드는 것**이 더 현실적이고 비용 효율적이다.&#x20;

***

## Summary

<table data-header-hidden><thead><tr><th width="111.26953125"></th><th width="288.57421875"></th><th></th></tr></thead><tbody><tr><td>원칙</td><td>설명</td><td>목적</td></tr><tr><td>SRP</td><td>단일 책임</td><td>변경 이유 하나로 명확히 유지</td></tr><tr><td>OCP</td><td>확장에는 열려 있고, 변경에는 닫힘</td><td>기존 코드 안정화, 기능 추가 용이</td></tr><tr><td>LSP</td><td>하위 타입은 상위 타입으로 대체 가능</td><td>상속 구조 안정화</td></tr><tr><td>ISP</td><td>필요한 인터페이스만 제공</td><td>불필요한 의존성 제거</td></tr><tr><td>DIP</td><td>추상화에 의존</td><td>유연하고 테스트 가능한 구조</td></tr></tbody></table>

SOLID 원칙은 단순히 이론이 아니라, 실제 현업에서 점점 커져가는 복잡한 코드와 \
끊임없이 발생하는 요구사항 변경 속에서도 견고하고 확장 가능한 시스템을 구축하기 위한 기반이다.

***

## Conclusion

예전엔 기능을 만들 때 미리 확장 고려해서 인터페이스도 만들고 추상화도 무조건 했었는데, \
지금은 실제 변경이 일어난 뒤에 코드를 정리해도 충분하다는 걸 몸으로 깨달았다. \
예측이 아니라 경험 기반으로 구조를 개선하는 게 더 실용적인 접근이라고 본다. \
SOLID는 지켜야 할 법칙이라기보단, 상황에 따라 활용할 수 있는 무기라는 게 내 생각이다.
