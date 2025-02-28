---
description: >-
  Kotlin과 JPA를 함께 사용할 때에는 여러 가지 이유로 충돌이 발생한다. 오늘은 그 이유와 함께 해결 방안을 살펴보고, 실제
  프로젝트에서 어떻게 적용할 수 있는지 포스팅 해본다.
---

# 😖 Kotlin : Conflict With JPA

## Why They Conflict

Kotlin은 기본적으로 클래스와 메서드가 `final`이며, 불변성을 강조하기 위해 `val`을 많이 사용한다. \
반면에 JPA는 빈 객체를 생성한 후 필드를 채워넣는 방법으로 동작하며, (기본생성자 필수!)\
&#x20;Lazy Loading 시 엔티티를 상속받아 프록시 객체를 생성해야 하므로,\
클래스나 메서드가 `final`이거나 필드가 `val`이면 문제가 생길 수 있다. \
또한 JPA 스펙에서 요구하는 기본 생성자를 Kotlin이 자동으로 제공하지 않는 점도 충돌을 야기한다.

## Common Issues

### **`final` Class and Method**

Kotlin 클래스와 메서드는 기본적으로 `final`이므로, JPA의 프록시 객체 생성 과정에서 상속이 불가능하다.

### **`val` vs `var`**

`val`로 선언된 필드는 변경이 불가능하므로, 지연 로딩 시점에 값이 설정되어야 하는 경우 문제를 일으킨다. \
따라서 엔티티 필드는 `var`나 `lateinit var`로 두는 것이 일반적이다.

### **No-Arg Constructor**

JPA는 엔티티에 파라미터 없는 기본 생성자를 요구하지만, \
Kotlin에서는 이를 명시적으로 구현하거나 플러그인을 사용하지 않으면 제공되지 않는다.

### **Initialization Block(`init`) 사용**

Kotlin의 `init` 블록은 객체 생성 시점에 실행된다. \
그러나 JPA가 엔티티를 로딩하거나 프록시로 다룰 때는 다른 시점에 필드 초기화가 이뤄질 수 있으므로 \
충돌이 발생할 수 있다.

## Solutions

### **Kotlin Plugins (all-open, no-arg)**

* `all-open` 플러그인은 @Entity, @MappedSuperclass, @Embeddable 같은 어노테이션을 붙인 \
  클래스를 자동으로 `open` 처리하여 JPA의 프록시 생성이 가능하도록 돕는다.
* `no-arg` 플러그인은 동일한 어노테이션이 붙은 클래스에 대해 기본 생성자를 자동 생성해준다.

```kotlin
plugins {
    kotlin("jvm") version "1.8.0"
    id("org.jetbrains.kotlin.plugin.allopen") version "1.8.0"
    id("org.jetbrains.kotlin.plugin.noarg") version "1.8.0"
}

allOpen {
    annotation("javax.persistence.Entity")
    annotation("javax.persistence.MappedSuperclass")
    annotation("javax.persistence.Embeddable")
}

noArg {
    annotation("javax.persistence.Entity")
    annotation("javax.persistence.MappedSuperclass")
    annotation("javax.persistence.Embeddable")
}
```

JPA(Hibernate/Spring)가 엔티티를 다룰 때, 동적 프록시를 생성한다. \
이 프록시는 엔티티 클래스를 상속하거나 필드에 접근해서 동작하는데, \
Kotlin의 클래스 구조나 컴파일 방식 때문에 다음과 같은 충돌이 발생할 수 있다\
하지만 위와 같이 설정하면 Kotlin이 만들어내는 바이트코드가 JPA가 기대하는 형태에 부합하게 되어, \
충돌 없이 엔티티를 사용할 수 있다.

### **Declare Entity Fields Properly**

* 엔티티의 필드를 `var`로 두면, JPA가 값 주입이나 변경을 수행하기 용이하다.
* 불변성을 선호한다면, 생성자에서만 필드를 할당하고 `private set`을 붙이거나, \
  생성자 파라미터 활용 후 값을 수정하지 않는 방식을 적용할 수 있다.

```java
@Entity
data class ImageEntity(
    @Id
    var id: Long? = null,
    var name: String? = null
)
```

### **Entity in Java, Others in Kotlin**

* 엔티티 클래스만 자바로 작성하고, 나머지(서비스, DTO)는 Kotlin으로 구현하는 방식이다.
* 자바는 기본적으로 클래스와 메서드가 `open` 상태이며, Lombok을 이용해 편리하게 엔티티를 작성할 수 있다.
* 다만 한 프로젝트 내에 자바와 코틀린이 혼재한다는 점에서 코드 스타일 관리가 까다로워질 수 있다.

## Conclusion

`all-open`과 `no-arg` 같은 플러그인을 적절히 적용하고, \
엔티티 필드를 `var`로 선언하여 동적으로 관리할 수 있도록 준비하면 문제 없이 운영이 가능하다. \
또한 필요에 따라 엔티티를 자바로만 작성하는 방식도 고려할 수 있다. \
이러한 접근 방법을 통해 Kotlin 특유의 간결함과 JPA의 기능을 함꼐 누릴 수 있다.\
이런 간단한(?) 대안들을 사용해 열심히 코틀린 프로젝트를 만들어 봐야겠다.
