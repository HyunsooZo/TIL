---
description: 객체 생성 로직을 서브클래스에서 결정하도록 하여, 구체적인 클래스에 의존하지 않고 객체를 생성하는 유연한 방법을 제공하는 패턴
---

# 🏭 Design Pattern : Factory Method

## Factory Method Pattern?

팩토리 메서드 패턴은 객체 생성 방식에 대한 결정을 서브클래스에서 내리도록 하여 \
객체 생성의 책임을 분리하는 생성 패턴이다. \
인터페이스나 추상 클래스를 통해 객체를 생성하는 메서드를 정의하고, \
이를 통해 객체 생성을 서브클래스가 담당하도록 유도하는 것이다.

## Purpose of Factory Method Pattern

이 패턴의 주요 목적은 **객체 생성 로직을 캡슐화**하여 코드의 **유연성과 확장성**을 높이는 것이다. \
특히, 어떤 객체를 생성할지에 대한 결정을 런타임 시점에 할 수 있으며, \
새로운 클래스가 추가되더라도 클라이언트 코드의 변경을 최소화할 수 있다.

## When to Use and Applicable Scenarios

### When to Use

* 객체 생성이 복잡하거나 구체적인 클래스와의 의존성을 줄이고 싶을 때&#x20;
* 클래스의 인스턴스가 생성될 때마다 특정한 로직을 적용해야 하는 경우
* 인터페이스/추상 클래스 기반 계층구조에서 특정 구현체를 생성하는 방법을 서브클래스가 결정하도록 하고 싶을 때

### Applicable Examples

**GUI Frameworks**: Windows, MacOS 등의 플랫폼에 따라 버튼을 생성해야 할 때

**Database Connections**: MySQL, Oracle 등 데이터베이스 유형에 따라 커넥션 객체를 생성하는 경우

**Logging Libraries**: 다양한 로깅 방법(Console, File, Database)에 따라 로그 객체를 생성할 때

## Components of the Pattern

팩토리 메서드 패턴은 일반적으로 다음과 같은 구성 요소로 이루어진다\
Product 라는 인터페이스를 기반으로 예시를 들어보자면..

### Product Interface

객체들이 구현해야 할 인터페이스 정의

### ConcreteProduct Class

Product 인터페이스를 구현하는 실제 클래스들

### Creator Class

Product 객체를 반환하는 팩토리 메서드를 포함하며, \
기본적으로 팩토리 메서드를 호출하여 객체를 생성할 수 있도록 함

### ConcreteCreator Class

Creator를 상속받아 실제로 Product를 생성하는 팩토리 메서드를 구현하는 클래스들

## Flow of The Pattern

<figure><img src="../../.gitbook/assets/스크린샷 2024-11-12 오후 7.57.05.png" alt=""><figcaption></figcaption></figure>

## Implementation In Java

`Product` 인터페이스는 구체적인 제품 클래스들이 구현해야 하는 메서드를 정의.

`ConcreteProduct1`과 `ConcreteProduct2`는 `Product` 인터페이스를 구현하는 구체적인 제품.

`Creator`는 `factoryMethod`를 가지고 있으며, 실제 객체 생성은 구체적 서브클래스에서 결정.

`ConcreteCreator1`과 `ConcreteCreator2`는 `Creator` 클래스를 상속받아 실제 제품 객체를 생성

```java
// Product 인터페이스
public interface Product {
    void use();
}

// ConcreteProduct1 클래스
public class ConcreteProduct1 implements Product {
    @Override
    public void use() {
        System.out.println("ConcreteProduct1 사용");
    }
}

// ConcreteProduct2 클래스
public class ConcreteProduct2 implements Product {
    @Override
    public void use() {
        System.out.println("ConcreteProduct2 사용");
    }
}

// Creator 클래스
public abstract class Creator {
    public abstract Product factoryMethod();
    
    public void someOperation() {
        Product product = factoryMethod();
        product.use();
    }
}

// ConcreteCreator1 클래스
public class ConcreteCreator1 extends Creator {
    @Override
    public Product factoryMethod() {
        return new ConcreteProduct1();
    }
}

// ConcreteCreator2 클래스
public class ConcreteCreator2 extends Creator {
    @Override
    public Product factoryMethod() {
        return new ConcreteProduct2();
    }
}

// 클라이언트 코드
public class Client {
    public static void main(String[] args) {
        Creator creator1 = new ConcreteCreator1();
        creator1.someOperation();
        
        Creator creator2 = new ConcreteCreator2();
        creator2.someOperation();
    }
}

```

## Pros And Cons

### Pros

* 클라이언트가 구체적인 클래스에 의존하지 않고 객체를 생성할 수 있어 유연성이 높아진다.
* 제품 추가하는 것이 비교적 쉽다. 새로운 ConcreteProduct와 ConcreteCreator 클래스를 추가하면 된다.
* 객체 생성 코드를 클라이언트에서 분리해 코드의 유지 보수가 용이하다.

### Cons

1. 각 제품에 대해 새로운 Creator와 ConcreteProduct 클래스를 만들어야 하므로 클래스 수가 많아진다.
2. 간단한 객체 생성에는 다소 복잡할 수 있다.

## Conclusion

팩토리 메서드 패턴은 객체 생성에 대한 책임을 분리하여 코드의 유연성과 확장성을 높이는데 유용한 패턴이다. \
특정 로직에 구체적인 객체 생성을 감추고 싶을 때, \
그리고 다양한 구현체를 클라이언트에서 손쉽게 교체할 수 있도록 하고자 할 때 유용하다. \
다만, 간단한 상황에서는 과도한 설계가 될 수 있으니 유의하여 사용해야 한다.
