---
description: >-
  Java에서 데이터 타입은 크게 두 가지로 나뉜다. 기본형(Primitive Type) 과 래퍼 클래스(Wrapper Class) 이다. 이
  둘은 단순히 사용법만 다른 것이 아니라, 메모리 저장 위치, 객체 참조 여부, null 처리, 컬렉션 사용 가능성 등에서 본질적인 차이가
  존재한다.
---

# 🌯 Java : Primitive vs Wrapper

## Primitive Type

Primitive Type은 **Java에서 가장 기본적인 데이터 타입**이다. \
총 8가지 타입이 존재하며, 이는 **값 자체를 스택(Stack)에 저장**한다. \
객체가 아니므로, 메서드를 가질 수 없고, 참조 주소도 존재하지 않는다.

| 타입      | 크기     | 기본값      |
| ------- | ------ | -------- |
| byte    | 1 byte | 0        |
| short   | 2 byte | 0        |
| int     | 4 byte | 0        |
| long    | 8 byte | 0L       |
| float   | 4 byte | 0.0f     |
| double  | 8 byte | 0.0      |
| char    | 2 byte | '\u0000' |
| boolean | 1 bit  | false    |

### Example

```java
int a = 10;
int b = a; // 값 복사
b = 20;
System.out.println(a); // 10 (값 자체를 복사했기 때문에 a는 변하지 않음)
```

### Memory Representation

<div align="left"><figure><img src="../../../.gitbook/assets/스크린샷 2025-03-26 오후 8.58.55.png" alt="" width="375"><figcaption></figcaption></figure></div>

***

## Wrapper Class

Wrapper 클래스는 Primitive 값을 감싸는 **객체형 타입**이다. \
자바에서는 각 기본형에 대해 대응되는 래퍼 클래스가 존재한다.

| Primitive | Wrapper Class |
| --------- | ------------- |
| byte      | Byte          |
| short     | Short         |
| int       | Integer       |
| long      | Long          |
| float     | Float         |
| double    | Double        |
| char      | Character     |
| boolean   | Boolean       |

Wrapper는 **Heap 영역에 생성되며 참조 변수로 관리**된다. \
또한 null 저장이 가능하고, 제네릭 컬렉션과 함께 사용할 수 있다.

### Example

```java
Integer x = new Integer(10);
Integer y = x; // 주소 복사
y = 20;
System.out.println(x); // 10 (y는 새로운 Integer 객체를 가리키게 됨)
```

### Memory Representation

<div align="left"><figure><img src="../../../.gitbook/assets/스크린샷 2025-03-26 오후 8.59.52 (4).png" alt="" width="434"><figcaption></figcaption></figure></div>

<div align="left"><figure><img src="../../../.gitbook/assets/스크린샷 2025-03-26 오후 8.59.57 (2).png" alt="" width="459"><figcaption></figcaption></figure></div>

***

## Autoboxing & Unboxing

자바는 편의를 위해 Primitive와 Wrapper 간의 자동 변환 기능을 제공한다.

* **Autoboxing**: Primitive → Wrapper 로 자동 변환
* **Unboxing**: Wrapper → Primitive 로 자동 변환

```java
int num = 5;
Integer obj = num; // Autoboxing
int again = obj;   // Unboxing
```

이러한 기능은 컬렉션(List, Set, Map 등)과 함께 사용할 때 매우 유용하다.

***

## Equality: == vs equals()

자바에서 두 값을 비교할 때 `==`와 `equals()`는 동작 방식이 다르다. \
Primitive와 Wrapper에 따라 다음과 같은 차이가 있다.

### Primitive Type

```java
int a = 100;
int b = 100;
System.out.println(a == b); // true (값 자체 비교)
```

Primitive는 값을 직접 비교하므로 `==`만 사용해도 정확한 비교가 된다. \
`equals()`는 사용 불가 (primitive 타입은 객체가 아니기 때문)

### Wrapper Type

```java
Integer a = new Integer(100);
Integer b = new Integer(100);
System.out.println(a == b);       // false (주소 비교)
System.out.println(a.equals(b));  // true  (값 비교)
```

`==`은 **객체의 주소값**을 비교하고, `equals()`는 **내부 값**을 비교한다. \
따라서 Wrapper 클래스를 비교할 때는 항상 `equals()`를 사용하는 것이 안전하다.

### Integer Caching

```java
Integer a = 100;
Integer b = 100;
System.out.println(a == b); // true

Integer c = 200;
Integer d = 200;
System.out.println(c == d); // false
```

* `-128 ~ 127` 사이의 값은 JVM이 **캐싱**을 하기 때문에 `==` 비교도 true가 나올 수 있다.
* 그 외의 값은 새로운 객체가 생성되므로 `==` 비교는 false가 된다.

***

## hashCode()

Java에서 `equals()`와 `hashCode()`는 항상 함께 고려되어야 한다.\
&#x20;특히 Wrapper 클래스는 내부적으로 `equals()`와 `hashCode()`가 값 기반으로 오버라이드 되어 있다.

### hashCode() in WrapperClass

```java
Integer a = new Integer(42);
Integer b = new Integer(42);
System.out.println(a.equals(b));     // true
System.out.println(a.hashCode());    // 42
System.out.println(b.hashCode());    // 42
```

두 객체는 다른 주소를 가지지만, 값이 같기 때문에 `equals()`는 true이고 `hashCode()`도 동일하다. \
이 덕분에 Wrapper 객체는 HashMap, HashSet 등에 안전하게 사용할 수 있다.

### equals() & hashCode()

* `a.equals(b) == true` 라면 반드시 `a.hashCode() == b.hashCode()` 여야 한다.
* 반대로 `hashCode()`가 같다고 해서 반드시 `equals()`가 true는 아니다.

### in Primitive

Primitive는 객체가 아니기 때문에 `hashCode()`를 호출할 수 없다. \
다만 오토박싱을 통해 Wrapper로 변환되면 `hashCode()`가 적용된다.

***

## Comparison&#x20;

| 항목       | Primitive Type | Wrapper Class                 |
| -------- | -------------- | ----------------------------- |
| 메모리 위치   | Stack          | Heap + Stack (참조)             |
| null 허용  | 불가             | 가능                            |
| 기본값      | 0 등 명확         | null                          |
| 메서드 보유   | 불가             | 가능 (ex: `Integer.parseInt()`) |
| 성능       | 빠름             | 느릴 수 있음                       |
| 컬렉션 사용   | 불가             | 가능                            |
| == 비교    | 값 자체 비교        | 주소 비교 (주의 필요)                 |
| equals() | 사용 불가          | 값 비교에 적합                      |

***

## Summary

Primitive와 Wrapper는 단순한 형변환 이상의 차이를 가진다.

* Primitive는 값 그 자체를 저장하고, 빠르고 가볍다.
* Wrapper는 객체로 동작하며 컬렉션, null 처리가 필요한 상황에서 쓰인다.
* Autoboxing/Unboxing 덕분에 두 타입을 유연하게 넘나들 수 있지만, \
  성능에 민감한 애플리케이션에서는 의도하지 않은 객체 생성 비용이 발생할 수 있다.
* Wrapper는 `==`과 `equals()` 비교 결과가 다를 수 있으므로, \
  항상 상황에 맞는 비교 연산자를 사용하는 것이 중요하다.

개발 시 상황에 따라 정확한 타입을 선택하는 것이 중요하다.
