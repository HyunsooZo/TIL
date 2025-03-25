# 🪞 Java : Object Copy

## Object Copy?

Java에서 객체 복사는 메모리에 존재하는 객체의 내용을 다른 객체에 복제하는 개념이다. \
이때, 복사 방식에 따라 **얕은 복사(Shallow Copy)** 와 **깊은 복사(Deep Copy)** 로 나뉜다.

개념은 단순해 보이지만, **참조 타입 필드가 있는 경우** 복사 방식에 따라 결과가 달라질 수 있어 \
의도하지 않은 사이드 이펙트를 만들기도 한다. 따라서 정확히 이해하는 것이 중요하다.

***

## Shallow Copy

얕은 복사는 **객체의 필드 값만 복사**한다.\
&#x20;하지만, 필드가 참조 타입일 경우 **참조 주소만 복사**되기 때문에 \
복사된 객체와 원본 객체가 **같은 객체를 참조**하게 된다.

**즉, 내부 객체는 공유된다.**

```java
class Person implements Cloneable {
    String name;
    Address address; // 참조 타입 필드

    public Person clone() throws CloneNotSupportedException {
        return (Person) super.clone(); // 얕은 복사
    }
}

class Address {
    String city;
}
```

### Behavior

```java
Person p1 = new Person("현수", new Address("Seoul"));
Person p2 = p1.clone();

p2.address.city = "Busan";
System.out.println(p1.address.city); // "Busan" → 같이 바뀜
```

### Diagram

<figure><img src="../../.gitbook/assets/스크린샷 2025-03-25 오후 11.18.30.png" alt=""><figcaption></figcaption></figure>

두 객체(p1, p2)는 서로 다른 객체지만 **같은 Address 인스턴스를 공유**하고 있다.

***

## Deep Copy

깊은 복사는 객체 자체뿐만 아니라, **그 안에 포함된 참조 타입 필드들도 모두 복제**하는 방식이다.

즉, 완전히 새로운 객체 트리를 생성하기 때문에 원본과 복사본은 서로 전혀 영향을 주지 않는다.

```java
class Person implements Cloneable {
    String name;
    Address address;

    public Person clone() throws CloneNotSupportedException {
        Person cloned = (Person) super.clone();
        cloned.address = new Address(this.address.city); // 깊은 복사
        return cloned;
    }
}

class Address {
    String city;
    public Address(String city) {
        this.city = city;
    }
}
```

### Behavior

```java
Person p1 = new Person("현수", new Address("Seoul"));
Person p2 = p1.clone();

p2.address.city = "Busan";
System.out.println(p1.address.city); // "Seoul" → 영향을 안 받음
```

### Diagram

<figure><img src="../../.gitbook/assets/스크린샷 2025-03-25 오후 11.20.24.png" alt=""><figcaption></figcaption></figure>

두 객체(p1, p2)는 Address까지 **서로 완전히 분리된 인스턴스**를 가진다.

***

### Comparison Table

| 항목         | 얕은 복사 (Shallow Copy) | 깊은 복사 (Deep Copy) |
| ---------- | -------------------- | ----------------- |
| 기본 방식      | 참조 주소만 복사            | 내부 객체까지 재귀적 복사    |
| 성능         | 빠름                   | 느릴 수 있음           |
| 복잡도        | 간단                   | 구현 복잡             |
| 참조 객체 변경 시 | 원본에도 영향              | 원본과 무관            |
| 용도         | 간단한 구조나 불변 객체        | 중첩 객체가 많은 구조      |

***

## Summary

Java에서 객체 복사를 할 때는 **내부에 참조 타입이 포함되어 있는지**, \
**불변 객체인지**, **복사된 객체가 원본에 영향을 주면 안 되는지**를 기준으로 \
깊은 복사와 얕은 복사를 선택해야 한다.

실제로는 `Object.clone()`, 생성자 복사, \
또는 `copy constructor`, `serialization`, `BeanUtils`, `ModelMapper` 등 다양한 방식이 존재하며, \
구조의 복잡성과 퍼포먼스 요건에 따라 적절한 방법을 선택하는 것이 중요하다.
