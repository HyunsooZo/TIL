---
description: >-
  제네릭 타입 제한과 sealed interface는 목적이 비슷해 보이지만, 사용하는 맥락과 의도에서 차이가 크다.  제네릭은 다양한 타입을
  처리하면서도 타입 안전성을 보장하기 위해 사용되며, 컴파일 시점에서 주로 작동한다. Sealed Interface는 타입 계층을 고정하고,
  런타임 다형성을 명확히 정의하는 데 적합하다.
---

# ✔️ vs Sealed Interface

## Generic?  &#x20;

제네릭 타입 제한은 **컴파일 시점에 타입 안전성을 확보**하고, 특정 타입들만 허용하기 위해 사용되는 메커니즘이다.

***

### **Purpose**

1. **Type Restriction**\
   특정 클래스 계열이나 인터페이스를 구현한 타입만 허용하도록 제한한다.
2. **Code Reusability**\
   여러 타입에 대해 동일한 동작을 처리하면서, 잘못된 타입 사용을 방지한다.

***

### **Example**

```java
class Container<T extends Number> {
    private T value;

    public Container(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }
}
```

* `T extends Number`를 사용해 `Container`는 `Number`를 상속하는 타입만 받을 수 있다.
* 이를 통해 **제네릭 타입의 유연성**과 동시에 **컴파일 시점의 타입 안전성**을 보장한다.

***

## Sealed Interface?

`sealed interface`는 특정 클래스 계열을 고정시키고, \
그 인터페이스를 구현할 수 있는 타입을 **명시적으로 제한**하기 위한 메커니즘이다.\
주로 **런타임 다형성을 명확히 정의**하고, **예측 가능한 타입 계층**을 구성하기 위해 사용된다.

***

### **Purpose**

1. **Fixing Type Hierarchy**\
   인터페이스나 추상 클래스를 상속할 수 있는 타입을 고정시킨다.
2. **Clear Type Model**\
   계층 구조를 명확히 정의하여 **유지보수성과 가독성**을 높인다.
3. **Pattern Matching**\
   `Switch`와 같은 구문에서 **컴파일러가 모든 타입을 알고 완전성 검사를 수행**할 수 있도록 돕는다.

***

### **Example**

```java
복사sealed interface Shape permits Circle, Rectangle {}

final class Circle implements Shape {
    double radius;
}

final class Rectangle implements Shape {
    double width, height;
}
```

* `Shape`를 구현할 수 있는 타입이 `Circle`과 `Rectangle`로 제한된다.
* 이를 통해 `Shape` 계층에서 **예상치 못한 타입 추가** 가능성을 제거한다.
* `Switch` 구문에서 컴파일러는 모든 가능한 타입을 알고 완전성을 검사한다.

***

## Key Differences

<table data-header-hidden><thead><tr><th width="248"></th><th></th><th></th></tr></thead><tbody><tr><td><strong>Feature</strong></td><td><strong>Generic Type Bound</strong></td><td><strong>Sealed Interface</strong></td></tr><tr><td><strong>Target</strong></td><td>컴파일 시점에서 허용할 타입을 제한</td><td>런타임에 특정 인터페이스/클래스 계층 고정</td></tr><tr><td><strong>Purpose</strong></td><td>타입 안전성 확보 및 다양한 타입 지원</td><td>타입 계층 고정 및 명확한 타입 관계 설정</td></tr><tr><td><strong>Extensibility</strong></td><td>제한된 타입에 대해 추가적으로 유연하게 지원 가능</td><td>고정된 타입 계층에서만 확장 가능</td></tr><tr><td><strong>Use Cases</strong></td><td>컬렉션, 유틸리티 클래스 등에서 타입 제한</td><td>명확한 타입 계층이 필요한 상황</td></tr><tr><td><strong>Runtime/Compile Time</strong></td><td>컴파일 시점에 타입 안전성 확보</td><td>런타임 계층 고정 및 다형성 관리</td></tr><tr><td><strong>Pattern Matching</strong></td><td>지원하지 않음</td><td>Switch 구문에서 타입 완전성 검사 가능</td></tr></tbody></table>

***

## When to Use?

### **Generic Type Bound**

* 다양한 타입에 대해 유연한 로직을 처리해야 할 때.
* 특정 상속 관계에 있는 타입만 허용해야 할 때.
* **Examples**: `List<T>`, `Map<K, V>` 같은 컬렉션 API.

### **Sealed Interface**

* 명확한 타입 계층이 필요한 경우.
* 추가적인 타입 확장을 금지하고, 코드 구조를 고정하려는 경우.
* 런타임에서 패턴 매칭을 활용하려는 경우.
* **Examples**: 상태(State), 이벤트(Event) 처리.

***

## Conclusion

제네릭 타입 제한과 `sealed interface`는 **제약을 설정하여 코드의 안정성을 높이는 목적**은 비슷하지만, \
사용하는 **맥락과 의도**에서 큰 차이가 있다.

* **Generic Type Bound**는 다양한 타입을 처리하면서도 **컴파일 시점에서 타입 안전성**을 보장하기 위해 사용된다.
* **Sealed Interface**는 타입 계층을 고정하고, **런타임 다형성을 명확히 정의**하는 데 적합하다.
