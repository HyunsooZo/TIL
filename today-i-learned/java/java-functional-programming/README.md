---
description: 자바는 객체지향 프로그래밍을 중심으로 설계되었지만, 자바 8부터 함수형 프로그래밍의 요소들을 지원한다.
---

# ⛹️‍♂️ Java : 함수형 프로그래밍

## Java의 함수형 프로그래밍

함수형 프로그래밍은 불변성, 순수 함수, 고차 함수, 지연 평가 등을 통해 상태 변화와 부수 효과를 최소화하고, \
코드를 더욱 안전하고 간결하게 작성할 수 있는 패러다임이다. \
이 포스팅에서는 자바에서 함수형 프로그래밍을 활용하는 방법을 다루며, \
특히 **Lamda**, **함수형 인터페이스**, **스트림 API**, **메서드 참조**, **Optional**, **고차 함수** 등을 알아봤다.

## Lambda Expressions

람다 표현식은 익명 함수를 간단하게 표현할 수 있는 구문이다. \
자바에서는 `->` 문법을 사용하여 람다 표현식을 정의할 수 있으며, \
함수형 프로그래밍에서 가장 기본적인 도구로 사용된다.

```java
// 기존 방식
Runnable oldRunnable = new Runnable() {
    @Override
    public void run() {
        System.out.println("Hello, Hyunsoo!");
    }
};

// 람다 표현식
Runnable lambdaRunnable = () -> System.out.println("Hello, Hyunsoo!");
lambdaRunnable.run();
```

## Functional Interface

자바의 함수형 프로그래밍에서는 **함수형 인터페이스**가 중요한 역할을 한다. \
함수형 인터페이스는 하나의 추상 메서드만 가지는 인터페이스로, \
자바 표준 라이브러리에는 다양한 함수형 인터페이스가 제공된다. \
대표적인 함수형 인터페이스로는 **`Function`**, **`Consumer`**, **`Supplier`**, **`Predicate`** 등이 있다.

```java
import java.util.function.Function;

Function<Integer, Integer> square = x -> x * x;
System.out.println(square.apply(5));  // Output: 25
```

## Streams API

자바 8에서 추가된 스트림 API는 컬렉션 데이터를 함수형 스타일로 처리할 수 있게 해준다. \
스트림 API는 데이터를 필터링, 변환, 집계할 때 유용하며, \
불변성과 지연 평가(lazy evaluation)로 인해 메모리 효율성을 높일 수 있다.

```java
import java.util.Arrays;
import java.util.List;
import java.util.stream.Collectors;

List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6);

List<Integer> result = numbers.stream()
    .filter(n -> n % 2 == 0)  // 짝수만 필터링
    .map(n -> n * n)          // 각 요소 제곱
    .toList();                // 불변 리스트 반환 

System.out.println(result);  // Output: [4, 16, 36]
```

## Method References

메서드 참조는 람다 표현식을 간결하게 작성할 수 있는 구문으로, 특정 메서드를 바로 참조하도록 해준다. `ClassName::methodName` 형식으로 작성되며, 간단한 람다 표현식 대신 메서드 참조를 사용할 수 있다.

#### 예시: 메서드 참조를 사용하여 출력하기

```java
import java.util.Arrays;
import java.util.List;

List<String> names = Arrays.asList("Hyunsoo", "Eunmi", "Hana");

names.forEach(System.out::println);
```

## Optional

`Optional` 클래스는 자바 8에 추가된 null-safe 컨테이너로, \
값이 있을 수도 있고 없을 수도 있는 상황에서 안전하게 데이터를 다룰 수 있게 해준다. \
`Optional`은 함수형 메서드(`map`, `filter`, `ifPresent` 등)를 제공하여 \
null 여부를 쉽게 체크하고 처리할 수 있다.

```java
import java.util.Optional;

Optional<String> name = Optional.of("HANA");

name.filter(n -> n.equals("HANA"))
    .map(String::toUpperCase)
    .ifPresent(System.out::println);  // Output: HANA
```

## Higher-Order Functions & Currying

자바에서 고차 함수는 함수형 인터페이스를 사용하여 구현할 수 있다. \
자바는 함수 자체를 매개변수로 받을 수 없지만, \
`Function` 인터페이스를 이용하면 유사하게 구현이 가능하다. \
커링은 여러 매개변수를 받는 함수를 연속적인 단일 인자로 처리할 수 있게 한다.

```java
import java.util.function.Function;

Function<Integer, Function<Integer, Integer>> add = x -> y -> x + y;
Function<Integer, Integer> add5 = add.apply(5);

System.out.println(add5.apply(3));  // Output: 8
```
