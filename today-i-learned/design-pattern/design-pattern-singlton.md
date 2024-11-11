# 🔂 Design Pattern : Singlton

## **Singleton Pattern?**&#x20;

싱글톤 패턴은 소프트웨어 개발에서 가장 널리 사용되는 생성 패턴 중 하나이다. \
이 패턴의 주요 목적은 특정 클래스가 애플리케이션의 전체 수명 동안 오직 하나의 인스턴스만을 가지도록 보장하고, \
그 인스턴스에 대한 전역적인 접근점을 제공하는 것이다. \
시스템 전반에서 하나의 객체만 필요할 때 특히 유용하다.

## **Key Characteristics of the Singleton Pattern**

### **Single Instance**

클래스의 **인스턴스가 단 하나만 생성**되도록 보장한다.

### **Global Access**

인스턴스에 대한 **전역 접근점을 제공**한다.

### **Lazy Initialization**

**필요할 때만** 인스턴스를 생성한다 (선택적이지만 일반적이다).

## **Why Use the Singleton Pattern?**&#x20;

싱글톤 패턴은 다음과 같은 상황에서 자주 사용된다:

* **공유 자원 관리**: 데이터베이스 연결, 설정 파일, 로깅 메커니즘과 같은 자원에 대한 접근을 제어해야 할 때.
* **동작 조율**: 스레드 풀이나 캐싱 서비스와 같이 중앙 집중식 제어가 필요할 때.
* **일관된 상태 보장**: 객체의 상태 일관성이 중요하고 중복 생성이 되지 않아야 할 때.

## **Common Use Cases of Singleton Pattern**

* **로깅 프레임워크**: 모든 로깅 작업이 하나의 인스턴스를 통해 처리되도록 보장한다.
* **설정 관리자**: 설정 속성의 관리를 중앙 집중화한다.
* **커넥션 풀**: 데이터베이스 연결의 공유 풀을 효율적으로 관리한다.

## Singlton In Spring&#x20;

스프링은 기본적으로 싱글톤 패턴을 사용해 애플리케이션 컨텍스트 내에서 빈(Bean)을 관리한다. \
이는 메모리 효율성과 성능 면에서 이점을 제공하나 이 싱글톤 특성 때문에 몇 가지 주의할 점도 있다.

특히 과도한 `Utility Class` 사용은 싱글톤 패턴의 장점과 상충될 수 있다. \
`Utility Class`는 보통 static 메서드들로 구성되는데, 스프링 컨텍스트의 관리 밖에서 동작하므로 \
스프링의 의존성 주입이나 라이프사이클 관리를 받지 못한다.&#x20;

만약 `Utility Class`에서 상태를 가지는(static 변수 등) 경우, 스레드 세이프하지 않아서 \
동시성 문제를 일으킬 수 있고 이는 싱글톤 패턴의 안전성과 스프링 빈의 의존성 주입 개념에 부정인 영향을 미친다.

따라서, 싱글톤 기반의 스프링 빈에서 `Utility Class` 사용을 고려할 때는 상태를 가지지 않게 설계하거나, \
스프링 빈으로 구현해 스프링의 라이프사이클 관리와 의존성 주입을 활용하는 것이 낫다.

스프링에서의 싱글톤이란 아래와 같다.

**싱글톤 빈**

기본적으로, 스프링은 각 클래스에 대해 단 하나의 빈 인스턴스만 생성하고, \
이를 모든 요청에 재사용한다. \
예를 들어, `@Service`, `@Repository`, `@Component`, `@Controller` 등의 \
어노테이션으로 등록된 클래스는 기본적으로 싱글톤 스코프이다.

**싱글톤 스코프**

이 스코프는 스프링 컨테이너에 의해 빈이 한 번만 생성되고, \
이후 요청 시마다 동일한 인스턴스를 반환한다는 것을 의미한다.

## **Pitfalls to Avoid**

**과도한 사용**

싱글톤은 필요할 때만 사용해야 한다. 과도한 사용은 코드의 결합도를 높이고 테스트가 힘들 수 있다.\
실제로 내가 회사에서 맡고 있는 프로젝트도 과한 결합도로 인해 테스트코드 작성이 어렵다.

**리플렉션 취약성**

리플렉션을 통해 싱글톤이 깨질 수 있다.(기 인스턴스가 존재하는 경우 생성자에서 예외 던져 해결가능)

**직렬화 문제**

직렬화 과정에서 싱글톤이 깨질 수 있다.\
( `readResolve()` 메서드를 구현해 직렬화 후 동일한 인스턴스를 반환하면 해결 가능)

## **Pros & Cons of Singleton Pattern**

### **Eager Initialization**

**장점**: 간단하며, 동기화 없이도 스레드 안전성을 제공한다. \
**단점**: 인스턴스가 사용되지 않을 경우에도 미리 생성되므로 자원이 낭비될 수 있다.

```java
public class EagerSingleton {
    private static final EagerSingleton instance = new EagerSingleton();

    private EagerSingleton() {
        // 인스턴스 생성을 방지하기 위한 private 생성자
    }

    public static EagerSingleton getInstance() {
        return instance;
    }
}
```

### **Lazy Initialization**

**장점**: 필요할 때만 인스턴스를 생성하므로 자원을 절약할 수 있다. \
**단점**: 스레드 안전하지 않아 다중 스레드 환경에서 문제를 일으킬 수 있다.

```java
public class LazySingleton {
    private static LazySingleton instance;

    private LazySingleton() {
        // 인스턴스 생성을 방지하기 위한 private 생성자
    }

    public static LazySingleton getInstance() {
        if (instance == null) {
            instance = new LazySingleton();
        }
        return instance;
    }
}
```

### **Thread-Safe Singleton (Synchronized Method)**

**장점**: 스레드 안전하며 하나의 인스턴스만 생성됨을 보장한다. \
**단점**: 동기화로 인해 성능 저하가 발생할 수 있다.

```java
public class ThreadSafeSingleton {
    private static ThreadSafeSingleton instance;

    private ThreadSafeSingleton() {
        // private 생성자
    }

    public static synchronized ThreadSafeSingleton getInstance() {
        if (instance == null) {
            instance = new ThreadSafeSingleton();
        }
        return instance;
    }
}
```

### **Double-Checked Locking**

**장점**: 효율적이며, 동기화된 메서드의 성능 오버헤드를 줄이기 위해 두 번 확인한다. \
**단점**: 복잡하며 `volatile` 에 대한 추가적인 이해가 필요하다.

```java
public class DoubleCheckedLockingSingleton {
    private static volatile DoubleCheckedLockingSingleton instance;

    private DoubleCheckedLockingSingleton() {
        // private 생성자
    }

    public static DoubleCheckedLockingSingleton getInstance() {
        if (instance == null) {
            synchronized (DoubleCheckedLockingSingleton.class) {
                if (instance == null) {
                    instance = new DoubleCheckedLockingSingleton();
                }
            }
        }
        return instance;
    }
}
```

### **Bill Pugh Singleton Implementation**

**장점**: 간단하며, 스레드 안전성을 제공하고 동기화 없이도 지연 초기화를 보장한다. \
**단점**: 큰 단점은 없으며, Java에서 싱글톤 패턴을 구현하는 **가장 좋은 방법으로 여겨진다.**

```java
public class BillPughSingleton {
    private BillPughSingleton() {
        // private 생성자
    }

    private static class SingletonHelper {
        private static final BillPughSingleton INSTANCE = new BillPughSingleton();
    }

    public static BillPughSingleton getInstance() {
        return SingletonHelper.INSTANCE;
    }
}
```

## **Conclusion**&#x20;

싱글톤 패턴은 애플리케이션에서 단일 인스턴스 요구 사항을 관리하기 위한 강력한 도구이다. \
애플리케이션의 요구 사항에 맞는 적절한 구현 방법을 선택함으로써 자원 사용의 효율성을 보장하고 \
시스템 상태를 제어할 수 있다. \
하지만 단점에 유의하여 불필요한 복잡성을 피할 수 있도록 신중하게 사용하는 것이 좋다.
