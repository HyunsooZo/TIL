# 🗞️ AOP : @Transactional

## @Transactional

`@Transactional`은 트랜잭션을 관리하기 위한 Spring의 어노테이션이다. \
주어진 메서드를 트랜잭션 범위 내에서 실행하도록 보장하며, 실행 도중 예외가 발생하면 롤백을 수행한다.&#x20;

## AOP & @Transactional

`@Transactional`의 핵심은 **AOP(Aspect-Oriented Programming)**&#xC744; 이용하여 동작한다는 점이다.

`@Transactional`이 적용된 메서드를 호출할 때 **프록시 객체**를 생성하고, 이 프록시를 통해 트랜잭션을 관리한다. \
AOP가 적용된 방식에 따라 프록시는 트랜잭션을 시작하고 메서드 실행 후 커밋 또는 롤백을 수행한다.

<figure><img src="../../../../.gitbook/assets/스크린샷 2025-01-31 오후 7.49.04.png" alt=""><figcaption></figcaption></figure>

## Transaction Management Of Spring

### Proxy-based Transaction Management

Spring은 AOP를 활용하여 **프록시 객체**를 생성하고, 트랜잭션을 관리한다. \
이때 **JDK 동적 프록시**와 **CGLIB 프록시** 두 가지 방식을 사용한다.

* **JDK Dynamic Proxy**: 인터페이스가 존재하면 JDK의 동적 프록시를 사용하여 트랜잭션을 적용한다.
  * 인터페이스가 존재할 경우 `java.lang.reflect.Proxy`를 사용하여 \
    인터페이스 기반의 프록시 객체를 생성한다. \
    메서드 호출 시 트랜잭션 로직이 먼저 실행된 후 실제 대상 객체의 메서드가 실행된다.
* **CGLIB Proxy**: 인터페이스가 없는 경우 CGLIB을 이용해 클래스 기반의 프록시를 생성한다.
  * 클래스 기반의 프록시를 생성할 때 사용되며, 직접 클래스를 상속하여 프록시를 만든다. \
    인터페이스가 없는 경우 기본적으로 이 방식이 적용된다.

### Transaction Propagation Levels

Spring은 중첩된 트랜잭션을 제어하기 위해 여러 가지 전파 레벨(Propagation Level)을 제공한다.

| 전파 레벨           | 설명                                    |
| --------------- | ------------------------------------- |
| `REQUIRED`      | 기본값. 기존 트랜잭션이 존재하면 참여, 없으면 새 트랜잭션 시작  |
| `REQUIRES_NEW`  | 기존 트랜잭션과 별개로 새로운 트랜잭션을 생성             |
| `NESTED`        | 부모 트랜잭션 내에서 중첩 트랜잭션 생성 (savepoint 활용) |
| `SUPPORTS`      | 트랜잭션이 있으면 참여하고, 없으면 트랜잭션 없이 실행        |
| `NOT_SUPPORTED` | 트랜잭션 없이 실행                            |
| `NEVER`         | 트랜잭션이 있으면 예외 발생                       |
| `MANDATORY`     | 기존 트랜잭션이 없으면 예외 발생                    |

### Nested Transaction Handling

**(1) `REQUIRES_NEW` vs. `NESTED`**

* `REQUIRES_NEW`: 기존 트랜잭션과 독립적으로 실행되며, 기존 트랜잭션이 롤백되어도 영향을 받지 않는다.
* `NESTED`: 기존 트랜잭션 안에서 하위 트랜잭션을 만들고, 부모 트랜잭션의 롤백 시 같이 롤백된다.

**(2) `REQUIRED`와 중첩 트랜잭션**

* `REQUIRED`를 사용하면 기존 트랜잭션이 있으면 참여하고, 없으면 새 트랜잭션을 만든다.
* 부모 트랜잭션이 롤백되면 함께 롤백된다.

```java
@Service
public class TransactionService {

    @Transactional(propagation = Propagation.REQUIRED)
    public void outerMethod() {
        innerMethod(); // 같은 트랜잭션 내부에서 실행됨
    }

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void innerMethod() {
        // 새로운 트랜잭션이 시작됨
    }
}
```

## **AOP Dynamic Proxy and @Transactional**

### **Understanding the Relationship**&#x20;

Spring의 `@Transactional`은 AOP(Aspect-Oriented Programming)를 기반으로 트랜잭션을 관리한다. \
이 과정에서 Dynamic Proxy 또는 CGLIB Proxy를 이용하여 트랜잭션을 적용하는 것이 핵심이다.

### **How AOP Dynamic Proxy Works in @Transactional**&#x20;

Spring에서 `@Transactional`을 선언하면 \
해당 메서드의 실행을 인터셉트해서 트랜잭션을 시작하고, 예외 발생 여부에 따라 커밋 또는 롤백을 수행한다. \
이를 가능하게 하는 것이 AOP Dynamic Proxy이다.

### **Why Proxies Are Essential for @Transactional**&#x20;

Spring이 프록시를 사용하는 이유는 **트랜잭션 관리 코드가 대상 객체와 분리**되어야 하기 때문이다. \
직접 객체 내부에서 트랜잭션을 처리하면 결합도가 증가하고 관리가 어려워진다. \
프록시 패턴을 활용하면 트랜잭션 로직을 분리하면서도 기존 코드에 영향을 주지 않고 적용할 수 있다.

## Cases Where `@Transactional` Does Not Apply

### Self-invocation **(`this.method()`)**

Spring의 AOP 프록시는 **프록시 객체를 통해 호출될 때만 적용**되기 때문에, \
같은 클래스 내부에서 `this.method()`로 호출하면 트랜잭션이 적용되지 않는다.

**해결 방법**

1. `ApplicationContext`에서 직접 빈을 가져와서 호출
2. 트랜잭션을 별도의 빈(Service)으로 분리

### **Final Method**

CGLIB 프록시는 클래스 상속을 기반으로 동작하기 때문에 `final` 메서드에서는 트랜잭션이 적용되지 않는다.

### Rollback Restrictions Based on Exception Types

Spring은 기본적으로 **`RuntimeException`과 `Error`가 발생했을 때만 롤백**한다. \
`CheckedException`(예: `IOException`)은 기본적으로 롤백되지 않는다.

**해결 방법**

`rollbackFor` 옵션을 사용하여 특정 예외에 대해 롤백 설정

```java
@Transactional(rollbackFor = Exception.class)
public void someMethod() throws Exception {
    throw new IOException(); // 기본적으로 롤백되지 않지만 rollbackFor 설정으로 롤백됨
}
```

## Conclusion

* `@Transactional`은 **AOP 기반의 프록시 객체**를 활용하여 트랜잭션을 관리한다.
* 프록시는 트랜잭션을 시작하고, 예외 발생 시 롤백, 정상 실행 시 커밋을 수행한다.
* 트랜잭션 전파 레벨을 활용하면 중첩된 트랜잭션을 효율적으로 관리할 수 있다.
* **자기 자신 호출, final 메서드, Checked Exception 처리**와 같은 트랜잭션이 적용되지 않는 경우를 주의해야 한다.
