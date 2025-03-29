---
description: thread > developers 에 올라온 재밌는 문제
---

# 🛫 Java : Multiple Returns

## What happens when `try` and `finally` both return?

자바에서 `try` 블록과 `finally` 블록에 각각 `return` 문이 있다면 어떤 값이 실제로 리턴될까?

다음 코드를 보자.

```java
public String test() {
    try {
        return "try";
    } finally {
        return "retry";
    }
}
```

이 코드를 실행하면 리턴값은 `"retry"`이다.

***

## Why?

자바에서 `finally` 블록은 **무조건 실행**된다.\
심지어 `try` 블록에서 `return`을 하더라도, 그 **리턴 전에** `finally` 블록이 실행된다.

그리고 중요한 포인트는,\
&#xNAN;**`finally` 안에 `return`이 있을 경우,&#x20;**<mark style="color:blue;">**그것이 최종적으로 호출 결과**</mark>**를 덮어쓴다.**

즉, `try` 블록에서 먼저 `"try"`를 리턴하려 했지만,\
`finally` 블록에서 `"retry"`를 리턴하면서\
최종적으로 메서드는 `"retry"`를 리턴하게 된다.

***

## Recommendation

자바 공식적으로도 권장하는 방식은,

> **`finally` 블록 안에 `return` 문을 작성하지 말 것.**

이유는 단순하다.\
`finally` 블록에서 `return`이나 `throw`를 하면 예외 흐름이나 결과 값을 예상할 수 없게 되기 때문이다.

***

### Summary

* `finally`는 항상 실행된다.
* `finally`에 있는 `return`은 `try`의 `return`을 덮어쓴다.
* 따라서 위 예제의 결과는 `"retry"`이다.
* **`finally` 안에서는 로직만 수행하고 리턴은 피하자.**
