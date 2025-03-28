---
description: >-
  Java에서 equals와 hashCode는 객체의 동등성 비교와 해시 기반 컬렉션 동작에 핵심적인 역할을 담당한다. 이 둘은 반드시 함께
  재정의되어야 하며, 그렇지 않을 경우 매우 미묘한 버그가 발생할 수 있다.
---

# 🟰 Java : equals & hashCode

## Equality and Hashing Contract

Java에서는 `Object` 클래스가 `equals()`와 `hashCode()`를 기본적으로 제공한다. \
이를 재정의할 때 반드시 지켜야 할 계약(Contract)이 존재한다.

### equals와 hashCode의 관계

> “**equals가 true인 두 객체는 반드시 동일한 hashCode를 가져야 한다.**”

즉, 다음 조건이 반드시 성립해야 한다:

```java
if (a.equals(b)) {
    a.hashCode() == b.hashCode(); // 반드시 true여야 함
}
```

반면, `hashCode`가 같다고 해서 `equals`가 반드시 true일 필요는 없다. 이 점이 핵심이다.

***

## What happens if only `equals` is overridden?

`equals()`만 재정의하고 `hashCode()`를 재정의하지 않으면, 다음과 같은 문제가 발생한다.

#### 예시 코드:

```java
class Person {
    String name;

    public Person(String name) {
        this.name = name;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (!(o instanceof Person)) return false;
        Person person = (Person) o;
        return Objects.equals(name, person.name);
    }
    // hashCode는 재정의하지 않음
}
```

이 경우, `equals()`는 이름이 같은 두 객체를 동등하게 판단하지만, \
`hashCode()`는 여전히 `Object`의 기본 구현을 사용하게 되어 **서로 다른 해시코드**를 생성한다.

#### 문제 상황:

```java
Set<Person> set = new HashSet<>();
set.add(new Person("현수"));

System.out.println(set.contains(new Person("현수"))); // false!
```

같은 이름의 Person인데도 `HashSet`에서 찾지 못한다. \
이유는 내부적으로 `hashCode()`로 먼저 버킷을 찾고, 그 안에서 `equals()`를 사용하기 때문이다.

***

## What happens if only `hashCode` is overridden?

이번엔 반대로 `hashCode()`만 재정의하고, `equals()`는 기본 구현을 사용한다면?

두 객체가 `같은 hashCode`를 가졌더라도, `equals()`는 여전히 객체 주소값을 비교하게 된다. \
즉, **동등 비교 자체가 성립하지 않는다.**

```
// 위 예제에서 equals를 제거하고 hashCode만 오버라이드한 경우에도 문제가 생긴다
```

***

## When should I override both?

* 커스텀 클래스를 `HashMap`, `HashSet`, `Hashtable` 등에 키/값으로 사용해야 할 경우
* 두 객체의 **논리적 동등성(logical equality)** 을 정의해야 할 경우

***

## Summary

<figure><img src="../../../.gitbook/assets/스크린샷 2025-03-28 오후 9.49.54.png" alt=""><figcaption></figcaption></figure>

***

## Conclusion

Java에서는 `equals()`와 `hashCode()`를 **쌍으로 재정의해야 한다.**\
이 둘은 단순한 메소드 이상의 의미를 가지며, 컬렉션의 핵심 동작 원리를 지탱하는 기반이 된다.\
&#x20;재정의 시 반드시 두 메소드의 계약을 충실히 따르는 것이 중요하다.
