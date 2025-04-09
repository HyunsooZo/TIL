---
description: Set에는 어떤 요소를 담을 수 있을까?
---

# Java : What Can You Put in a Set?

## &#x20;All Objects Are Allowed?

Java의 Set은 기본적으로 어떤 객체든 담을 수 있다. 참조형이면 뭐든 OK임. 심지어 `null`도 가능하다.

```java
Set<Object> set = new HashSet<>();
set.add("hello");
set.add(123);
set.add(new Object());
set.add(null);
```

하지만 이 말에는 숨겨진 조건이 있다.

## The Real Trick: equals() and hashCode()

Set의 본질은 중복이 없다는 것이다. \
그리고 Java에서 이 중복 여부를 판단하는 건 바로 `equals()`와 `hashCode()` 메서드다.

* `hashCode()`로 같은 버킷인지 확인하고,
* `equals()`로 실제 동일 객체인지 판단한다.

이 두 메서드가 제대로 구현되어 있지 않으면, **동일 객체라도 서로 다른 값으로 처리**될 수 있다.

## Missing equals/hashCode

```java
class User {
    String name;
    User(String name) { this.name = name; }
}

Set<User> users = new HashSet<>();
users.add(new User("현수"));
users.add(new User("현수"));

System.out.println(users.size()); // 결과는 2
```

이유는? `equals()`와 `hashCode()`를 구현하지 않았기 때문에, \
Java는 이 둘을 완전히 다른 객체로 본다.

반면, 아래는 `record` 사용 예시:

```java
record User(String name) {}
```

이 경우엔 `equals()`와 `hashCode()`가 자동 생성되므로 중복 제거가 잘 작동한다.

## Final Answer

### **Q: Set에는 어떤 요소를 담을 수 있을까?**

> 모든 객체를 담을 수 있다. 하지만 **중복 제거를 기대한다면 equals()와 hashCode()는 반드시 필요하다.** \
> 이 두 개 없으면, Set은 중복을 구분하지 못하고 그냥 List처럼 작동할 수도 있다.

## Tip for Real Use

* VO(Value Object)는 반드시 `equals()`와 `hashCode()`를 구현할 것
* `record`는 자동 생성되므로 VO로 사용 시 유리함
* Set에 custom 객체를 넣을 땐 꼭 이걸 고려하자
