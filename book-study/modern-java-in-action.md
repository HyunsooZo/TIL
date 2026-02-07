---
description: written by Raoul-Gabriel Urma, Mario Fusco, Alan Mycroft.
---

# 👲 Modern Java In Action

### **짧은 리뷰**

모던 자바 인 액션은 자바 8 이후에 도입된 람다, 스트림, 함수형 프로그래밍 등 주요 기능을 깊이 있게 설명하는 책이다.&#x20;

책은 자바의 과거와 현재, 그리고 미래를 연결하는 구조로 구성되어 있는데 \
자바 8부터 10까지의 변화가 어떻게 자바 생태계를 바꾸었는지 명확히 보여준다.&#x20;

특히 자바 8 부터 스트림 API를 통해 데이터 처리를 선언적으로 할 수 있는 방식은 \
코드 가독성과 유지보수성에서 큰 이점을 제공함을 강조한다.\
또한 자바의 함수형 프로그래밍 패러다임을 설명하면서도, \
실무에서 어떻게 적용할 수 있는지 구체적인 예제와 함께 잘 설명하고 있다.&#x20;

스트림 처리나 병렬 프로그래밍, 그리고 CompletableFuture 같은 비동기 프로그래밍 주제도 다루고 있어 \
자바의 현대적인 기술에 대한 이해를 넓히고 성능 향상을 위한 기술도 배울 수 있었다.

다만, 초보자보다는 이미 자바에 대한 기본 지식이 있는 사람들에게 더 적합할 것으로 보인다 \
(내용이 조금 깊고 실무 적용 팁들이 많다.)

회사에서는 대부분의 프로젝트가 자바 17 이상 을 사용하고 있지만, \
잘 알지 못하고 사용하는 기분이 들었는데 책을 읽으며 조금 더 이해가 깊어졌다고 생각된다.

또한 경력이 많은 직장동료와 함수형 프로그래밍에 관해 이야기 한적이 있었는데 \
당시에 이해가지 않던 일부 개념들도 조금 더 이해되었다.

19장의 경우 시간이 조금 지난 후 다시 한번 꼭 읽어봐야겠다.

(아 그리고 20장의 경우 스칼라에 관한 이야기가 많아 따로 책정리 섹션에서 다루진 않았다..)<br>

### **책 정리**

<details>

<summary>1장 - 자바 8, 9, 10, 11 : 무슨 변화가 있는가?</summary>

자바 8은 람다 표현식과 스트림 API로 대표되는 큰 변화가 있었고, \
그 이후로도 여러 버전이 출시되면서 많은 기능들이 추가되었다.\
&#x20;특히 자바 9에서는 모듈 시스템이 도입되었고, \
자바 10에서는 `var` 키워드로 로컬 변수의 타입을 추론할 수 있게 되었다. \
자바 11에서는 새로운 표준 API들이 개선되었다(성능 향상과 간결한 코드 작성)

```java
// 자바 8 람다 표현식 예시
List<String> names = Arrays.asList("John", "Jane", "Doe");
List<String> upperCaseNames = names.stream()
                                    .map(String::toUpperCase)
                                    .collect(Collectors.toList());

// 자바 10 var 예시
var numbers = List.of(1, 2, 3, 4, 5);
```

</details>

<details>

<summary>2장 - 동작 파라미터화 코드 전달하기</summary>

동작 파라미터화는 메서드나 함수에 '동작'을 전달하는 방식으로, 코드를 더욱 유연하게 만든다. \
예를 들어 필터링, 정렬, 변환 등을 다양한 방식으로 처리할 수 있다.  (**람다 표현식,** **익명 클래스**)\
예를 들어, 객체를 필터링할 때 조건을 외부에서 쉽게 변경할 수 있다.

```java
public interface Predicate<T> {
    boolean test(T t);
}

public static <T> List<T> filter(List<T> list, Predicate<T> predicate) {
    List<T> result = new ArrayList<>();
    for (T t : list) {
        if (predicate.test(t)) {
            result.add(t);
        }
    }
    return result;
}

// 필터링 예시
List<String> names = Arrays.asList("apple", "banana", "pear");
List<String> filtered = filter(names, (String s) -> s.startsWith("a"));
```

</details>

<details>

<summary>3장 - 람다 표현식</summary>

람다 표현식은 자바 8에서 도입된 익명 함수 표현 방식이다. \
익명 클래스를 간결하게 대체하고, 코드의 가독성을 크게 향상시킨다. \
`() -> {}` 같은 형태로 함수형 인터페이스의 구현을 쉽게 할 수 있고\
메서드를 다른 메서드의 인자로 전달하거나, 즉석에서 구현할 수 있다.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
numbers.forEach(n -> System.out.println(n));  // 람다 표현식 사용
```

</details>

<details>

<summary>4장 - 스트림 소개</summary>

스트림 API는 컬렉션 데이터를 순차적 또는 병렬적으로 처리하기 위한 선언적 방식이다. \
스트림은 중간 연산(filter, map)과 최종 연산(reduce, collect)으로 구성되고, \
이를 통해 간결하게 데이터를 처리할 수 있다. \
스트림은 기본적으로 게으르게(lazy) 처리되고, 중간 연산은 언제나 최종 연산이 호출될 때만 실제로 실행된다

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
List<Integer> evenNumbers = numbers.stream()
                                    .filter(n -> n % 2 == 0)  // 중간 연산
                                    .collect(Collectors.toList());  // 최종 연산
```

</details>

<details>

<summary>5장 - 스트림 활용</summary>

스트림의 중간 연산을 활용하면 복잡한 데이터를 효율적으로 처리할 수 있다. \
매핑(mapping), 리듀싱(reducing), 플랫 매핑(flat mapping) 등 다양한 연산을 통해 \
데이터를 변형하고 결합할 수 있으며 특히 플랫 매핑은 리스트나 배열 등의 중첩된 구조를 \
단일 스트림으로 펼치는 데 유용하다.

```java
List<String> words = Arrays.asList("Modern", "Java", "In", "Action");
List<String> uniqueChars = words.stream()
                                .map(w -> w.split(""))      // 문자열을 문자 배열로 변환
                                .flatMap(Arrays::stream)    // 플랫 매핑
                                .distinct()                 // 중복 제거
                                .collect(Collectors.toList());
```

</details>

<details>

<summary>6장 - 스트림 수집</summary>

스트림의 최종 연산인 수집(collect)은 데이터를 특정 형식으로 변환하고 집계할 수 있게 해준다. `Collectors` 클래스는 데이터 수집을 위한 다양한 메서드를 제공하며, `toList()`, `toSet()`, `groupingBy()`, `partitioningBy()` 같은 메서드로 데이터를 원하는 대로 그룹화하거나 분할할 수 있고 이로써 리스트나 맵 등 다양한 자료구조로 변환할 수 있다.\
(참고로 책에서는 Java 11 까지만 다루지만 Java 16 부터는 \
`.collect(Collectors.toList())` -> `.toList()`로 간소화 할 수도 있다.)<br>

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
Map<Boolean, List<Integer>> partitionedNumbers = 
    numbers.stream().collect(Collectors.partitioningBy(n -> n % 2 == 0));
```

</details>

<details>

<summary>7장 - 병렬 데이터 처리 및 성능</summary>

자바 스트림은 순차 처리뿐만 아니라 병렬 처리도 가능하다. \
병렬 스트림을 사용하면 데이터 처리를 여러 CPU 코어에서 동시처리할 수 있기 때문에 성능이 향상된다.\
하지만 병렬 처리는 적절한 상황에서만 사용해야 하며, \
병렬 처리로 인해 오히려 성능이 저하되거나 동기화 이슈가 발생할 수 있음을 주의해야 한다.

```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
int sum = numbers.parallelStream()    // 병렬 스트림
                 .reduce(0, Integer::sum);  // 병렬로 합계 계산
```

</details>

<details>

<summary>8장 - 컬렉션 API 개선</summary>

자바 8 이후, 컬렉션 API는 다양한 개선이 이루어졌다. \
`removeIf`, `forEach`, `replaceAll` 같은 메서드를 통해 컬렉션을 더 쉽게 조작할 수 있게 되었고, \
이러한 메서드들은 람다 표현식과 결합하여 매우 강력하게 사용할 수 있다. \
예를 들어 리스트에서 특정 조건을 만족하는 요소를 쉽게 제거할 수도 있다.

```java
List<String> names = new ArrayList<>(Arrays.asList("apple", "banana", "pear"));
names.removeIf(s -> s.startsWith("a"));  // "apple" 제거
```

</details>

<details>

<summary>9장 - 리팩터링, 테스트, 디버깅</summary>

함수형 프로그래밍 스타일과 스트림을 사용하면 기존 코드의 리팩터링을 더 쉽게 할 수 있다. \
불필요한 반복문을 줄이고, 코드의 가독성을 높일 수 있다는 말이다.  \
또한, 스트림의 중간 연산을 디버깅할 때는 `peek()` 메서드를 사용하면 \
데이터가 스트림을 통과하는 과정을 추적할 수도 있다.

```java
// peek을 사용한 중간 결과 디버깅
List<Integer> result = numbers.stream()
                              .peek(n -> System.out.println("Processing: " + n))  // 중간 결과 출력
                              .filter(n -> n % 2 == 0)
                              .collect(Collectors.toList());

```

</details>

<details>

<summary>10장 - 도메인 전용 언어와 디자인 패턴</summary>

자바의 함수형 프로그래밍 기능을 활용해 도메인 전용 언어(DSL)를 쉽게 만들 수 있고, \
기존의 디자인 패턴을 함수형 스타일로 재구현할 수 있다. \
이를 통해 코드의 유연성과 재사용성을 높일 수 있으며, 복잡한 비즈니스 로직을 간결하게 표현할 수 있다.

```java
// 커맨드 패턴 예시 (람다 표현식으로 처리)
public interface Command {
    void execute();
}

Command cmd = () -> System.out.println("Executing command");
cmd.execute();
```

</details>

<details>

<summary>11장 - null 대신 Optional</summary>

`Optional` 클래스는 `NullPointerException`을 방지하기 위해 도입되었다. \
이를 통해 값이 존재할 수도, 존재하지 않을 수도 있는 상황을 안전하게 처리할 수 있다. \
`Optional`을 사용하면 null 값을 직접 다루지 않고도, \
값이 없을 때의 대체값을 명시적으로 처리하거나 예외를 발생시킬 수 있다. \
값이 존재하는 경우에만 연산을 수행하고, 그렇지 않으면 미리 정의된 행동을 취할 수 있기 때문에 \
코드의 안정성을 높일 수 있다.

```java
// null 대신 Optional 사용 예시
Optional<String> name = Optional.ofNullable(null);  // null이 들어갈 수 있는 Optional
String result = name.orElse("default");  // null이면 "default" 반환
System.out.println(result);  // 출력: "default"

// Optional 값이 존재할 때만 출력
Optional<String> nonEmpty = Optional.of("Java");
nonEmpty.ifPresent(n -> System.out.println("Value: " + n));  // 출력: "Value: Java"
```

</details>

<details>

<summary>12장 - 새로운 날짜와 시간 API</summary>

자바 8에서 도입된 새로운 날짜와 시간 API인 `java.time` 패키지는 기존의 `D`\
`ate`와 `Calendar` 클래스의 문제점을 해결하기 위해 설계되었다. \
이 API는 불변 객체로 구성되어 스레드 안전성을 보장하며, \
날짜와 시간 계산을 쉽게 할 수 있도록 다양한 유틸리티 메서드를 제공한다. \
주요 클래스에는 `LocalDate`, `LocalTime`, `LocalDateTime`, `ZonedDateTime` 등이 있고\
특히 `Duration`과 `Period` 클래스를 통해 시간 간격을 다룰 수 있다.

```java
// 현재 날짜와 시간 구하기
LocalDate today = LocalDate.now();
System.out.println("Today's date: " + today);  // 출력: Today's date: yyyy-MM-dd

// 날짜 연산
LocalDate nextWeek = today.plus(1, ChronoUnit.WEEKS);
System.out.println("Next week's date: " + nextWeek);  // 출력: yyyy-MM-dd

// 시간 연산
LocalTime now = LocalTime.now();
LocalTime inTwoHours = now.plusHours(2);
System.out.println("Time in two hours: " + inTwoHours);  // 출력: HH:mm:ss
```

</details>

<details>

<summary>13장 - 디폴트 메서드</summary>

자바 8에서는 인터페이스에 메서드 구현을 포함할 수 있는 **디폴트 메서드**가 도입되었다. \
이를 통해 기존의 인터페이스를 깨뜨리지 않고 새로운 메서드를 추가할 수 있게 되었다. \
디폴트 메서드는 인터페이스 안에서 `default` 키워드를 사용해 정의되며, \
상속받은 클래스가 이를 재정의할 수도 있다.

```java
interface MyInterface {
    default void defaultMethod() {
        System.out.println("This is a default method");
    }
}

class MyClass implements MyInterface {
    // 디폴트 메서드를 재정의하지 않으면 기본 구현 사용
}

public class Main {
    public static void main(String[] args) {
        MyClass myClass = new MyClass();
        myClass.defaultMethod();  // 출력: This is a default method
    }
}
```

</details>

<details>

<summary>14장 - 자바 모듈 시스템</summary>

자바 9에서는 **모듈 시스템**이 도입되었다.\
모듈 시스템은 큰 규모의 애플리케이션에서 클래스 간의 의존성을 관리하고, \
명확한 모듈화된 아키텍처를 설계할 수 있게 도와줌. \
모듈은 명시적으로 `module-info.java` 파일에서 어떤 패키지를 공개할지 설정하고, \
외부 모듈과의 의존성을 명시함.

```java
module my.module {
    exports com.mycompany;  // 모듈에서 내보낼 패키지
    requires java.sql;      // 의존성 선언
}
```

</details>

<details>

<summary>15장 - 리팩터링, 테스트, 디버깅</summary>

자바 8에서 도입된 `CompletableFuture`는 비동기 프로그래밍을 지원하는 중요한 도구이다. \
이를 통해 여러 비동기 작업을 손쉽게 처리할 수 있고, 작업 간 의존성도 쉽게 관리할 수 있다. \
리액티브 프로그래밍은 비동기 데이터 스트림을 처리하는 방식으로, \
특히 많은 양의 데이터를 효율적으로 처리하는 데 유용하다.

```java
// CompletableFuture 사용 예시
CompletableFuture.supplyAsync(() -> {
    // 비동기 작업 수행
    return "Hello";
})
.thenApply(result -> result + " World")  // 결과를 변환
.thenAccept(System.out::println)         // 최종 결과 출력
.exceptionally(e -> {
    // 예외 발생 시 처리
    System.out.println("Exception: " + e.getMessage());
    return null;
});
```

</details>

<details>

<summary>16장 - CompletableFuture를 사용한 안정적인 비동기 프로그래밍</summary>

`CompletableFuture`를 이용해 안정적이고 효율적인 비동기 프로그래밍을 구현하는 방법을 설명한다.\
&#x20;특히 비동기 작업의 조합과 에러 처리, 병렬 처리 등 비동기 작업을 안정적으로 관리하는 방법을 다룬다. `CompletableFuture`는 비동기적으로 실행되는 작업 간의 의존성을 관리하고, \
예외 발생 시 복구하는 방법까지 포함해, 복잡한 비동기 흐름을 효과적으로 처리할 수 있게 해준다.

```java
// 두 개의 비동기 작업을 결합하고, 예외를 처리하는 예시
CompletableFuture<Integer> future1 = CompletableFuture.supplyAsync(() -> {
    // 첫 번째 비동기 작업 (5를 반환)
    return 5;
});

CompletableFuture<Integer> future2 = CompletableFuture.supplyAsync(() -> {
    // 두 번째 비동기 작업 (10을 반환)
    return 10;
});

// 두 작업의 결과를 결합
CompletableFuture<Integer> combinedFuture = future1.thenCombine(
    future2, (result1, result2) -> result1 + result2
);

combinedFuture.thenAccept(result -> System.out.println("Result: " + result))  // 출력: Result: 15
              .exceptionally(e -> {
                  System.out.println("Exception: " + e.getMessage());
                  return null;
              });
```

</details>

<details>

<summary>17장 - 리액티브 프로그래밍 (심화)</summary>

이 장에서는 리액티브 프로그래밍을 더 깊이 다룬다. \
리액티브 프로그래밍은 **Reactive Streams** API를 기반으로 하며, \
비동기 스트림 데이터의 처리와 백프레셔(backpressure) 문제를 해결하는 데 중점을 두고 \
이를 통해 매우 많은 양의 데이터를 효율적으로 처리할 수 있게 해줌.

```java
// Reactor의 Flux 예시
Flux<Integer> numbers = Flux.range(1, 10)
                            .filter(n -> n % 2 == 0)
                            .map(n -> n * n);
numbers.subscribe(System.out::println);

```

</details>

<details>

<summary>18장 - 함수형 관점으로 생각하기</summary>

함수형 프로그래밍의 철학을 배우고, 문제를 함수형 관점에서 해결하는 방법을 소개한다. \
명령형 프로그래밍과 함수형 프로그래밍의 차이를 설명하고, \
상태 변화와 부수효과를 최소화하는 코드 작성 방법을 강조한다. \
또한 순수 함수와 불변성, 고차 함수의 개념을 설명한다.

```java
// 순수 함수 예시
Function<Integer, Integer> square = x -> x * x;
System.out.println(square.apply(5));  // 출력: 25
```

</details>

<details>

<summary>19장 - 함수형 프로그래밍 기법</summary>

함수형 프로그래밍의 기법들을 더 깊이 다루고 있다. \
고차 함수(Higher-Order Functions), 커링(Currying), 함수 합성(Function Composition) 등 \
함수형 프로그래밍의 주요 기법들을 배울 수 있다. \
이를 통해 함수형 프로그래밍이 코드 재사용성, 가독성, 유지보수성을 어떻게 향상시키는지 설명한다.

```java
// 고차 함수 예시: 함수를 인자로 받는 함수
Function<Integer, Function<Integer, Integer>> adder = x -> y -> x + y;
Function<Integer, Integer> addFive = adder.apply(5);
System.out.println(addFive.apply(3));  // 출력: 8
```

</details>

<details>

<summary>20장 - 결론과 자바의 미래</summary>

마지막 장에서는 자바의 발전과 미래에 대해 논의한다.\
함수형 프로그래밍, 리액티브 프로그래밍, 모듈화 시스템 등의 자바 8, 9, 10의 새로운 기능들이 \
어떻게 자바 생태계를 변화시켰는지 설명하고, 앞으로의 자바가 나아가야 할 방향을 전망한다. \
자바가 하위 호환성을 유지하면서도 현대 프로그래밍의 요구사항을 충족시키는 방법을 강조하며, \
앞으로의 기술적 발전에 대한 논의로 마무리된다.

</details>
