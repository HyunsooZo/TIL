---
description: "\b일을 하다 보면 가변 객체의 사용이 꽤나 위험하다고 느낄 때가 많다. 오늘은 가변 객체 대신 불변 객체를 활용했을 때 얻을 수 있는 이점에 대해 알아본다. 불변 객체를 사용하면 동시성 문제를 줄이고, 예측 가능한 코드 구조를 유지하며, 성능 최적화에도 기여할 수 있다.  + 심지어 테스트 코드 작성도 쉬워진다!!"
---

# 🪨 Java : Immutable Objects

## Immutable Objects in Backend Development

불변 객체(Immutable Object)는 한 번 생성되면 내부 상태가 변경되지 않는 객체이다. \
Java의 `record` 또는 `final` 프로퍼티를 가지는 클래스가 불변 객체에 해당된다. \
Kotlin에서는 `val` 프로퍼티를 사용하는 `data class`를 활용하여 불변 객체를 쉽게 만들 수 있다. \
백엔드 개발에서 불변 객체를 활용하면 여러 이점이 있다. \
이는 성능, 안정성, 병렬 처리, 유지보수성 등의 측면에서 유리하게 작용한다.

## Thread Safety

멀티스레드 환경에서 동시성 이슈는 주요한 문제이다. \
불변 객체는 상태 변경이 불가능하기 때문에 여러 스레드에서 동시에 접근하더라도 동기화 문제를 일으키지 않는다. \
따라서 `synchronized` 블록이나 락(lock) 없이도 안전하게 공유될 수 있다.

다만, 좋아요(Like)나 조회수(Views)처럼 상태 변경이 필요한 경우, \
불변 객체만으로는 해결할 수 없으며, Redis의 Pub/Sub 또는 Optimistic Locking을 활용하는 것이 좋다.

* **조회수(Views)**: Redis의 `INCR` 연산을 활용하면 높은 성능과 동시성을 보장할 수 있다.
* **좋아요(Like)**: Optimistic Locking을 활용하면 데이터 정합성을 유지하면서 성능을 보장할 수 있다.
* **트랜잭션을 활용한 해결**: 트랜잭션을 길게 유지하는 방식도 고려할 수 있지만, \
  데드락 위험과 성능 저하를 초래할 수 있으므로 Optimistic Locking과의 조합이 더 적절하다.

## Predictability

불변 객체는 상태가 변하지 않기 때문에 예측 가능한 동작을 보장한다. \
이를 통해 디버깅이 쉬워지고, 유지보수가 용이해진다. \
변경이 필요한 경우 새로운 객체를 생성해야 하므로, 원본 객체의 예상치 못한 변경을 방지할 수 있다.

## Caching Optimization

불변 객체는 동일한 값에 대해 동일한 객체를 재사용할 수 있기 때문에 캐싱이 용이하다. \
예를 들어, `String` 객체처럼 캐싱을 활용하면 불필요한 객체 생성을 줄여 성능을 개선할 수 있다. \
또한, 불변 객체는 `HashMap`의 키로 사용하기에 적합하며, \
해시 코드가 변하지 않기 때문에 불필요한 재계산을 방지할 수 있다.

## Functional Programming

불변성을 유지하면 함수형 프로그래밍 스타일을 적용하기 쉬워진다. \
순수 함수(Pure Function)에서는 외부 상태를 변경하지 않으므로, \
불변 객체와 조합하면 부작용(Side Effect) 없는 코드를 작성할 수 있다. \
이는 유지보수성을 높이고, 테스트를 단순하게 만든다.

* **불변 객체는 참조 투명성(Referential Transparency)을 보장**하기 때문에 함수형 스타일과 궁합이 좋다.
* **함수형 프로그래밍에서는 Immutable Collections와 함께 사용하면 더욱 효과적이다.**

## Defensive Copy Reduction

불변 객체를 사용하면 방어적 복사(Defensive Copy)를 할 필요가 줄어든다. \
가변 객체를 외부에서 변경할 가능성이 있다면 이를 복사하여 전달해야 하지만, \
불변 객체는 변경되지 않으므로 복사 없이 안전하게 공유할 수 있다.

## In Kotlin & Java

Kotlin에서는 `data class`, \
Java에서는 `record`를 사용하여 불변 객체를 쉽게 생성할 수 있다.

### **Kotlin Example**

```kotlin
data class User(val name: String, val age: Int)

fun main() {
    val user1 = User("Hyunsoo", 32)
    val user2 = user1.copy(age = 33) // 새로운 객체 생성
    println(user1) // User(name=Alice, age=32)
    println(user2) // User(name=Alice, age=33)
}
```

### **Java Example**

```java
public record User(String name, int age) {}

public class Main {
    public static void main(String[] args) {
        User user1 = new User("Hyunsoo", 32);
        User user2 = new User(user1.name(), 33); // 새로운 객체 생성

        System.out.println(user1); // User[name=Alice, age=32]
        System.out.println(user2); // User[name=Alice, age=33]
    }
}
```

## Use Cases of Immutable Objects in Backend

* **DTO (Data Transfer Object)**: API 응답을 위한 데이터 전송 객체로 활용.
* **Configuration Values**: 설정 값이 변경되지 않는 경우 불변 객체로 유지.
* **Concurrency-Safe Data Structures**: 여러 스레드에서 공유할 데이터 구조.
* **Event Sourcing**: 상태 변경을 기록하고, 기존 데이터를 변경하지 않고 새로운 이벤트를 추가.
* **Domain Model in DDD**: \
  도메인 모델을 불변 객체로 설계하면 불필요한 상태 변화를 방지하고, 명확한 비즈니스 로직을 유지할 수 있다.

## Conclusion

불변 객체는 백엔드 개발에서 안정성을 높이고, 동시성 문제를 줄이며, 유지보수성을 개선하는 데 큰 도움을 준다. \
특히, Java의 `record`, Kotlin의 `data class` 같은 언어적 지원을 활용하면 쉽게 적용할 수 있다.

불변 객체의 장점을 적극적으로 활용하면 **더 안전하고 예측 가능한 시스템을 구축**할 수 있으며, \
캐싱 최적화, 함수형 프로그래밍과의 조합, 방어적 복사 감소 등 다양한 이점을 누릴 수 있다. \
불변 객체를 활용하는 것은 단순한 설계 원칙이 아니라, **백엔드 성능과 안정성을 높이는 필수적인 패턴**이다.
