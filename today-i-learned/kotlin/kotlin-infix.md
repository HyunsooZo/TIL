---
description: infix는 Kotlin에서 함수를 중위 표현법으로 호출해 두 객체 간 관계나 동작을 연산자처럼 간결하게 표현할 수 있게 하는 키워드
---

# 🧩 Kotlin : infix

## `infix` in Kotlin?

`infix`는 Kotlin에서 함수 호출을 더 간결하고 직관적으로 작성할 수 있도록 해주는 키워드이다.\
특히 두 객체 간의 관계나 동작을 표현할 때 유용하며, \
중위 표현법을 통해 함수 호출을 연산자처럼 사용할 수 있게 한다.

***

## How to Use `infix`

### Precondition

`infix` 키워드를 함수에 적용하려면 아래 조건을 만족해야 한다:

* 함수는 **멤버 함수** 또는 **확장 함수**여야 한다.
* 함수는 **매개변수를 하나만** 가져야 한다.
* 함수 선언 시 **`infix` 키워드**를 사용해야 한다.

***

## Syntax and Example

### Basic Syntax

```kotlin
infix fun Type.functionName(parameter: Type): ReturnType
```

### Example

```kotlin
class Calculator {
    infix fun add(value: Int): Int {
        return value + 10
    }
}

fun main() {
    val calculator = Calculator()
    // 일반적인 호출
    println(calculator.add(5)) // 15

    // infix 호출
    println(calculator add 5) // 15
}
```

***

## Infix Function in Extension Functions

### Example

```kotlin
infix fun String.concat(other: String): String {
    return this + other
}

fun main() {
    val result = "Hello" concat " World"
    println(result) // Hello World
}
```

***

## Real-World Use Cases

### Pair 생성

`to` 함수는 Kotlin에서 제공하는 기본 `infix` 함수로, 두 값을 `Pair`로 묶을 때 사용된다.

```kotlin
val pair = "key" to "value"
println(pair) // (key, value)
```

### Permission Checking

`infix`를 활용해 코드의 가독성을 높일 수 있다.

```kotlin
class Permission(val name: String)

infix fun Permission.grantTo(user: String): String {
    return "$user has been granted $name permission"
}

fun main() {
    val permission = Permission("Read")
    println(permission grantTo "Alice")
    // Output: Alice has been granted Read permission
}
```

### Custom Operator-Like Behavior

`infix`는 연산자처럼 표현될 수 있어 DSL(Domain-Specific Language) 설계 시 유용하다.

```kotlin
class Point(val x: Int, val y: Int)

infix fun Point.moveTo(other: Point): Point {
    return Point(this.x + other.x, this.y + other.y)
}

fun main() {
    val p1 = Point(2, 3)
    val p2 = Point(4, 5)
    val result = p1 moveTo p2
    println("(${result.x}, ${result.y})") // (6, 8)
}
```

***

## Limitations of `infix`

* **매개변수 하나만 사용 가능**:\
  `infix` 함수는 매개변수를 하나만 받을 수 있다. 추가 매개변수를 사용하려면 `default`나 `data class`로 처리해야 한다.
* **멤버 함수 또는 확장 함수로만 사용 가능**:\
  일반 함수에는 `infix`를 적용할 수 없다.

***

## When to Use `infix`

* **두 객체 간 관계를 표현할 때**:\
  예: 사용자와 권한, 키와 값 등.
* **가독성을 높이고 싶을 때**:\
  수식처럼 읽히는 직관적인 코드가 필요할 때.
* **DSL 설계**:\
  `infix`는 DSL을 구축하는 데 특히 유용하다.

***

## Conclusion

`infix`는 Kotlin의 유연하고 직관적인 코드를 작성할 수 있게 해주는 강력한 도구이다.\
특히, 두 객체 간의 관계를 명확히 표현하거나, 코드 가독성을 높이는 데 적합하다.\
하지만 제한 사항을 이해하고, 적합한 상황에서 활용해야 그 진가를 발휘할 수 있다.&#x20;
