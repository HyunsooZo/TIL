---
description: tailrec은 Kotlin에서 꼬리 재귀(Tail Recursion)를 최적화하기 위해 제공하는 키워드이다.
---

# 🦎 Kotlin : tailrec

## What is `tailrec` in Kotlin?

`tailrec`은 Kotlin에서 꼬리 재귀(Tail Recursion)를 최적화하기 위해 제공하는 키워드이다.\
꼬리 재귀는 함수의 마지막 작업이 **자기 자신을 호출**하는 경우를 말한다. \
**Kotlin 컴파일러는 이러한 꼬리 재귀를 감지하여 반복문으로 변환**하므로 \
**스택 오버플로우를 방지**하고 성능을 최적화한다.

## Why Use `tailrec`?

**가독성**: 재귀 함수는 반복문보다 코드를 더 간결하고 이해하기 쉽게 만들어준다.

**스택 오버플로우 방지**: Kotlin 컴파일러가 재귀 호출을 반복문으로 변환하여 스택 메모리 사용을 최소화한다.

## **How to Use `tailrec`**

### **Precondition Of tailrec**&#x20;

함수의 **마지막 작업이 자기 자신을 호출**해야 한다.

호출 뒤에 추가 연산이 있으면 꼬리 재귀로 간주되지 않으며, `tailrec` 키워드도 사용할 수 없다.

### **Example**

`tailrec` 키워드를 사용하여 1부터 n까지의 합을 계산하는 함수

```kotlin
tailrec fun calculateSum(n: Int, currentSum: Int = 0): Int {
    return if (n == 0) currentSum
    else calculateSum(n - 1, currentSum + n)
}
```

**동일한 반복문 구현**

위 함수는 컴파일 후 아래와 같은 반복문으로 변환된다

```kotlin
fun calculateSumUseWhile(n: Int): Int {
    var num = n
    var sum = 0
    while (num > 0) {
        sum += num
        num--
    }
    return sum
}
```

**피보나치 수열 예시**

```kotlin
tailrec fun factorial(n: Int, acc: Int = 1): Int {
    return if (n <= 1) acc
    else factorial(n - 1, acc * n)
}
```

```kotlin
tailrec fun fibonacci(n: Int, prev: Long = 0, current: Long = 1): Long {
    return if (n == 0) prev
    else fibonacci(n - 1, current, prev + current)
}
```

## **Limitations of `tailrec`**

**마지막 작업이 아닐 경우**

`tailrec` 함수는 마지막 작업이 자기 자신을 호출해야만 동작한다. \
예를들어 아래코드는 꼬리재귀를 만족하지 못한다.

```kotlin
fun fibo(n: Int): Int {
    if (n <= 1) return n
    return fibo(n - 1) + fibo(n - 2) // 마지막 작업이 자기 자신 호출이 아님
}
```

**불필요한 변환**

모든 재귀 함수를 `tailrec`으로 바꾸는 것이 항상 최선은 아니다.

예를 들어, 계산 범위가 작거나 스택 오버플로우 가능성이 낮은 경우 \
기존의 재귀 방식이 더 간결하고 이해하기 쉬울 수 있다.

## **Best Practices for Using `tailrec`**

**조건 확인**: 재귀 호출이 함수의 마지막 작업인지 확인한다.

**복잡한 로직 피하기**: `tailrec` 함수 내에서 너무 많은 조건문과 계산이 포함되면 가독성이 떨어질 수 있다.

**필요한 경우에만 사용**: 스택 오버플로우 위험이 있는 재귀 함수에 적합하며, 단순한 반복 작업은 일반적인 \
`while`문으로 처리하는 것이 더 적합할 수 있다

## **Conclusion**

`tailrec`은 Kotlin의 강력한 기능으로, 재귀 함수의 가독성을 유지하면서도 성능 문제를 해결할 수 있다.\
하지만 모든 재귀 함수를 꼬리 재귀로 바꾸려는 무리한 시도는 오히려 코드의 복잡성을 증가시킬 수 있으므로, \
적절한 상황에서만 사용하는 것이 중요하다.
