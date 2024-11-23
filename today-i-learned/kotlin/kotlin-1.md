---
description: >-
  함수형 프로그래밍은 프로그램을 수학적 함수처럼 구성하여 상태 변화를 최소화하고 사이드이펙트를 피하는 프로그래밍 패러다임이다. 객체지향
  프로그래밍과는 달리, 함수형 프로그래밍에서는 데이터를 변경하지 않고, 데이터의 변환과 조합을 통해 작업을 수행한다.
---

# ⛹️‍♂️ Kotlin : 함수형 프로그래밍

## 함수형 프로그래밍과 코틀린

코틀린은 객체지향 프로그래밍뿐 아니라 함수형 프로그래밍 스타일도 지원하는 언어이다. \
함수형 프로그래밍은 상태 변화와 부수 효과를 최소화하고, \
코드를 함수로 추상화하여 간결하고 직관적으로 작성할 수 있게 한다. \
이번 포스팅에서는 코틀린의 함수형 프로그래밍 요소들, \
특히 **람다 표현식, 고차 함수, 확장 함수, 컬렉션 처리**, 그리고 **스코프 함수**에 대해 자세히 살펴보겠다.

## Lambda Expressions

람다 표현식은 익명 함수로, 함수의 정의를 간결하게 작성할 수 있게 한다. \
코틀린에서는 `{ 매개변수 -> 함수 본문 }` 형태로 람다를 정의하며, \
람다를 변수에 저장하거나 다른 함수의 인자로 넘길 수 있다.

### 함수형이 아닌 방식

```kotlin
fun sum(a: Int, b: Int): Int {
    return a + b
}

val result = sum(3, 4)
println(result)  // Output: 7
```

위 코드에서는 `sum` 함수를 별도로 선언하여 사용한다. \
함수 선언과 호출이 명확하게 분리되어 있지만 코드가 다소 길어진다.

### 함수형 방식

```kotlin
val sum = { a: Int, b: Int -> a + b }
val result = sum(3, 4)
println(result)  // Output: 7
```

람다 표현식을 사용하여 `sum`을 간단히 정의할 수 있다. \
람다 표현식은 특히 짧은 함수에서 매우 유용하며, 변수처럼 다룰 수 있어 가독성을 높인다.

## Higher Order Functions

고차 함수는 함수를 매개변수로 받거나 결과로 반환할 수 있는 함수이다. \
이를 통해 동작을 추상화하고 재사용성을 높일 수 있다.

### 함수형이 아닌 방식

```kotlin
fun add(a: Int, b: Int): Int {
    return a + b
}

fun multiply(a: Int, b: Int): Int {
    return a * b
}

fun calculate(a: Int, b: Int, operation: String): Int {
    return when (operation) {
        "add" -> add(a, b)
        "multiply" -> multiply(a, b)
        else -> 0
    }
}

println(calculate(5, 3, "add"))       // Output: 8
println(calculate(5, 3, "multiply"))  // Output: 15
```

이 방식에서는 `operation`을 문자열로 전달하고 `when` 문을 사용하여 특정 함수를 호출한다. \
그러나 문자열을 비교하는 과정이 번거롭고, 실수로 문자열을 잘못 입력할 위험이 있다.

### 함수형 방식

```kotlin
fun calculate(a: Int, b: Int, operation: (Int, Int) -> Int): Int {
    return operation(a, b)
}

println(calculate(5, 3) { x, y -> x + y })  // Output: 8
println(calculate(5, 3) { x, y -> x * y })  // Output: 15
```

함수형 방식에서는 `operation`을 함수로 전달하여 호출할 수 있다. \
이 접근법은 `when` 문을 제거하고, 연산 로직을 함수로 캡슐화하여 재사용성을 높여준다.

## Extension Functions

확장 함수는 기존 클래스에 새로운 메서드를 추가할 수 있도록 해준다. \
이를 통해 기존 클래스의 코드에 접근하지 않고도 새로운 기능을 추가할 수 있다.

### 함수형이 아닌 방식

```kotlin
fun addExclamation(text: String): String {
    return text + "!"
}

println(addExclamation("Hello"))  // Output: Hello!
```

이 방법에서는 문자열에 느낌표를 추가하기 위해 별도의 함수를 작성한다. \
이 방식도 가능하지만 `String` 클래스에 대한 메서드처럼 보이지 않는다.

### 함수형 방식&#x20;

```kotlin
fun String.addExclamation(): String {
    return this + "!"
}

println("Hello".addExclamation())  // Output: Hello!
```

확장 함수를 사용하면 `String` 클래스에 메서드를 추가하는 것처럼 보이게 한다. \
이는 코드의 가독성을 높이고, 메서드 체이닝을 쉽게 할 수 있도록 해준다.

## 함수형 컬렉션 처리

코틀린은 `map`, `filter`, `reduce` 같은 함수형 컬렉션 처리 함수를 제공하여 \
코드를 더욱 간결하게 작성할 수 있게 해준다. \
이를 통해 불변성을 유지하면서도 다양한 연산을 수행할 수 있다. \
\
&#xNAN;_+ Java 의 `stream api` 와 다소 유사한 메서드들이 존재한다._\
&#x20;  _다만 `stream api`처럼 별도의 `stream`을 생성하지 않아도 된다!_

### 함수형이 아닌 방식

```kotlin
val numbers = listOf(1, 2, 3, 4, 5, 6)
val evenNumbers = mutableListOf<Int>()

for (number in numbers) {
    if (number % 2 == 0) {
        evenNumbers.add(number)
    }
}

println(evenNumbers)  // Output: [2, 4, 6]
```

이 방식에서는 `for` 문과 조건문을 사용하여 리스트의 짝수만 필터링한다. 이 경우 임시 리스트를 사용해야 하며, 코드가 다소 길어진다.

### 함수형 방식

```kotlin
val numbers = listOf(1, 2, 3, 4, 5)
val evenNumbers = numbers.filter { it % 2 == 0 }
println(evenNumbers)  // Output: [2, 4]
```

함수형 컬렉션 API를 사용하면 한 줄로 필터링 작업을 수행할 수 있다. 이 방법은 코드가 간결하고 가독성이 높다.



## Scope Functions (`let`, `apply`, `run`, `also` and so forth)

코틀린의 스코프 함수들은 특정 객체의 컨텍스트 내에서 코드를 간결하게 작성하고, \
불필요한 임시 변수를 줄이는 데 유용하다. \
`let`, `apply`, `run`, `also` 등 다양한 스코프 함수가 있다.

* **`let`**: `it`을 통해 객체를 참조하고, 마지막 구문이 반환된다.
* **`apply`**: `this`로 객체에 접근하며, 객체 자체를 반환한다.
* **`run`**: 코드 블록의 마지막 값을 반환한다.
* **`also`**: `it`으로 객체를 참조하며, 객체 자체를 반환한다.

### `let`

`let` 함수는 객체를 `it`으로 참조하며, 블록 내 마지막 구문의 결과를 반환한다. \
주로 특정 객체가 null이 아닌 경우에 수행할 작업을 정의할 때 유용하다.

```kotlin
val message = "Hello, Kotlin!".let {
    println(it)
    it.uppercase()  // 마지막 구문이 반환됨
}
println(message)  // Output: HELLO, KOTLIN!
```

위 코드에서 `let`을 사용하여 `message`를 출력하고 대문자로 변환한 결과를 반환한다. \
`let`은 null 안전성과 함께 사용될 때 특히 유용하다.

***

### `apply`

`apply` 함수는 객체를 `this`로 참조하며, 블록의 마지막 구문이 아닌 **객체 자체를 반환**한다. \
주로 객체를 생성하고 속성을 설정할 때 사용된다.

```kotlin
val person = Person().apply {
    name = "Alice"
    age = 25
}
println(person)  // Output: Person(name=Alice, age=25)
```

`apply`는 객체 초기화 시 필요한 속성을 설정할 때 유용하며, `this`를 생략할 수 있어 코드가 간결해진다.

***

### `run`

`run` 함수는 `this`를 통해 객체에 접근하며, 블록의 마지막 구문의 결과를 반환한다. \
`run`은 객체 초기화 후 작업을 수행하거나, null 검사를 수행한 후 안전하게 객체를 사용할 때 유용하다.

```kotlin
val greeting = "Hello".run {
    println(this)  // this로 접근
    uppercase()  // 마지막 구문 반환
}
println(greeting)  // Output: HELLO
```

`run`은 블록 마지막 구문의 결과를 반환하므로, 작업 수행 후 특정 값을 리턴해야 하는 경우 유용하게 사용할 수 있다.

***

### `also`

`also` 함수는 객체를 `it`으로 참조하며, **객체 자체를 반환**한다. \
객체의 값이나 상태를 로그로 확인하거나 디버깅할 때 유용하다.

```kotlin
val numbers = mutableListOf(1, 2, 3).also {
    println("Original list: $it")
    it.add(4)
}
println("Updated list: $numbers")  // Output: Updated list: [1, 2, 3, 4]
```

`also`는 객체에 대한 부가적인 작업을 수행할 때 사용되며, 객체 자체를 반환하므로 다른 작업에서 계속 사용할 수 있다.

***

### 스코프 함수 요약 비교

<table><thead><tr><th width="121">함수</th><th width="106">참조 방식</th><th width="159">반환 값</th><th>주로 사용되는 용도</th></tr></thead><tbody><tr><td><code>let</code></td><td><code>it</code></td><td>블록의 마지막 구문</td><td>특정 객체의 연산 결과가 필요할 때, null 안전성 체크</td></tr><tr><td><code>apply</code></td><td><code>this</code></td><td>객체 자체</td><td>객체 초기화와 설정 시</td></tr><tr><td><code>run</code></td><td><code>this</code></td><td>블록의 마지막 구문</td><td>블록 내 작업을 수행하고 결과를 반환할 때</td></tr><tr><td><code>also</code></td><td><code>it</code></td><td>객체 자체</td><td>부가적인 작업(로그, 디버깅) 수행 시</td></tr></tbody></table>
