---
description: >-
  회사에서 현재 맡고 있는 프로젝트의 Back Office는 Jsp로 구현되어있다. Jsp 파일에서 특정 객체접근을 시도할 때 마다 알수 없는
  예외가 발생하였고, 그 답을 찾았다. 바로 자바 레코드를 사용했기 떄문이었는데 왜 JSP에서는 레코드 사용이 불가한지 알아보았다.
---

# 📽️ Java Record in JSP

## Can JSP use Record?

Java 14부터 도입된 **record**는 불변 데이터 객체(VO or DTO)를 간결하게 정의하기 위한 문법이다. \
그러나 JSP는 record를 자유롭게 사용하기에 여러 가지 제약이 존재한다.

## Why can't we use Record in JSP?

JSP는 **EL**기반으로 데이터를 표현하거나 JSTL을 통해 접근하는 경우가 많은데, \
이 때 `record` 객체의 특성상 문제가 발생할 수 있다.

## Characteristics of Record

record는 다음과 같은 특징을 가진다:

* 모든 필드는 `private final`
* 생성자, getter, equals, hashCode, toString 자동 생성
* 별도의 `getX()` 형태의 getter가 아닌 **component 이름 그대로** 메서드가 존재함

```java
public record User(String name, int age) {}
```

`User` 객체는 다음과 같은 메서드를 가진다:

* `name()`
* `age()`

즉, JSP에서 보통 사용되는 `${user.name}` 형식이 아니라 `${user.name()}` 식으로 접근해야 하며, \
EL에서는 `()`가 붙은 메서드 호출이 불가능하다.

## Problem in JSP EL

JSP EL은 **getter 메서드 호출만 가능**하고, \
그 형식은 반드시 `getName()` 또는 `isEnabled()` 같은 형태여야 한다. \
그러나 record는 `getName()`이 아닌 `name()`이라는 메서드만 제공하므로, EL에서는 접근 불가능하다.

## Workaround

다음 중 한 가지 방식으로 해결 가능하다:

1. **DTO 클래스를 별도로 만들어서 record 객체를 변환해서 넘긴다.**
2. **JSP에서 EL 대신 scriptlet이나 JSTL 함수로 직접 호출한다.** _(권장하지 않음)_
3. **JSP를 사용하지 않고 Thymeleaf나 React 등 다른 템플릿 엔진 사용한다.**

## In Practice

```java
// Record
public record User(String name, int age) {}

// Controller
request.setAttribute("user", new User("현수", 30));

// JSP (비정상)
${user.name}    // 작동안함 (getter가 아님)
${user.name()}  // 작동안함 (EL에서 () 호출 불가)

// 해결법 (DTO로 변환)
public class UserDto {
    private String name;
    private int age;
    // getter, setter 구현
}

// JSP
${user.name} // 정상 동작
```

## Conclusion

Java의 record는 간결하고 불변 객체를 정의할 수 있는 강력한 문법이다. \
그러나 JSP는 오래된 기술로, record의 메서드 구조와 호환되지 않는다. \
JSP에서 record를 사용하려면 별도의 DTO 변환이 필요하며, \
근본적으로는 높은 버전의 자바를 지원하지 않는 JSP 사용을 지양해야하지 않을까..?
