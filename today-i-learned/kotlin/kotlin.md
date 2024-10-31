---
description: 코틀린 프로젝트를 시작하며 알아본 생 기초 문법
---

# Kotlin 기초 문법

## Basic Syntax

### Variables and Constants

코틀린에서는 `var`와 `val` 키워드를 사용하여 변수를 선언한다.

* `var`: mutable
* `val`: immutable

```kotlin
var mutableVar = 10  // 변경 가능
val immutableVal = 20  // 변경 불가능

mutableVar = 15  // 가능
// immutableVal = 25  // 오류 발생
```

### Data type

코틀린은 기본 데이터 타입을 아래와 같이 제공한다.

* 숫자: `Int`, `Long`, `Float`, `Double`
* 문자: `Char`
* 문자열: `String`
* 불린: `Boolean`

```kotlin
val age: Int = 33
val name: String = "조현수"
val isSmart: Boolean = true
```

### Null Safety

코틀린은 NullPointerException을 방지하기 위해 Null 안전성을 지원한다.

* 타입 뒤에 `?`를 붙여서 nullable 타입을 표시한다&#x20;

```kotlin
var nullableString: String? = null
val length = nullableString?.length  // 안전한 호출
```

***

## Control Statements

### If

```kotlin
val score = 85
if (score >= 90) {
    println("A 학점")
} else if (score >= 80) {
    println("B 학점")
} else {
    println("C 학점")
}
```

### When

```kotlin
val grade = 'B'
when (grade) {
    'A' -> println("Excellent")
    'B' -> println("Good")
    'C' -> println("Fair")
    else -> println("Fail")
}
```

### For

```kotlin
for (i in 1..5) {
    println(i)
}

val fruits = listOf("한패스", "조현수", "월렛")
for (fruit in fruits) {
    println(fruit)
}
```

### While

조건이 참인 동안 코드 실행하기

```kotlin
var count = 0
while (count < 5) {
    println("Count: $count")
    count++
}
```

***

## Function

### Function Declaration and Invocation

코틀린에서 함수는 `fun` 키워드로 선언한다.

```kotlin
fun add(a: Int, b: Int): Int {
    return a + b
}

val result = add(5, 3)
println("Result: $result")  // Result: 8
```

### Default Parameters and Named Arguments

```kotlin
fun greet(name: String = "Guest") {
    println("Hello, $name!")
}

greet()  // Hello, Guest!
greet("조현수")  // Hello, 조현수!

fun formatMessage(msg: String, prefix: String = "", suffix: String = "") {
    println("$prefix$msg$suffix")
}

formatMessage("Welcome", suffix = "!")  // Welcome!
```

***

## Classes and Objects

### Class Declaration

```kotlin
class Person {
    var name: String = ""
    var age: Int = 0
}
```

### Constructor

```kotlin
class Animal(val name: String, var age: Int)

val dog = Animal("강아지", 3)
println("Name: ${dog.name}, Age: ${dog.age}")
```

### Property & Method

```kotlin
class Rectangle(var width: Double, var height: Double) {
    val area: Double
        get() = width * height

    fun printArea() {
        println("Area: $area")
    }
}

val rect = Rectangle(5.0, 3.0)
rect.printArea()  // Area: 15.0
```

***

## Collection

### List

* `List`: 읽기 전용 리스트
* `MutableList`: 수정 가능한 리스트

```kotlin
val readOnlyList = listOf(1, 2, 3)
val mutableList = mutableListOf("A", "B", "C")

mutableList.add("D")
println(mutableList)  // [A, B, C, D]
```

### Map

* `Map`: 읽기 전용 맵
* `MutableMap`: 수정 가능한 맵

```kotlin
val readOnlyMap = mapOf("key1" to "value1", "key2" to "value2")
val mutableMap = mutableMapOf("name" to "조현수", "age" to 30)

mutableMap["age"] = 33
println(mutableMap)  // {name=조현수, age=33}
```

### Set

* `Set`: 중복 없는 요소들의 집합

```kotlin
val numberSet = setOf(1, 2, 3, 3)
println(numberSet)  // [1, 2, 3]
```
