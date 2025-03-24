# ✴️ Stream : Intermediate vs Terminal Operations

## Intermediate Operations?

중간 연산은 `Stream`의 **변형 또는 필터링**을 위한 연산이다. \
이 연산들은 실제 데이터를 소비하지 않으며, \`**Stream \`을 반환**함으로써 체이닝을 가능하게 한다.

### Characteristics

* **Lazy Evaluation(지연 평가)**: 종료 연산이 호출되기 전까지 실행되지 않는다.
* **연속 호출 가능**: `map`, `filter` 등을 연달아 호출할 수 있다.
* **Stream 반환**

### Practice

```java
IntStream.of(1, 2, 3, 4, 5)
         .filter(n -> n % 2 == 0)
         .map(n -> n * 10);
// 실행되지 않음 (종료 연산 없음)
```

### Common Intermediate Operations

* `filter(Predicate)`
* `map(Function)` / `flatMap(Function)`
* `sorted()`
* `distinct()`
* `limit(long)` / `skip(long)`
* `peek(Consumer)`

***

## Terminal Operations?

종료 연산은 Stream의 연산을 **최종 실행**하여 결과를 반환하거나, 부수 효과를 발생시키는 연산이다. \
한 번 실행되면 해당 `Stream`은 더 이상 사용할 수 없다.

### Characteristics

* **Stream을 소비**함
* **값 반환 혹은 출력 처리**
* **하나의 Stream에 하나의 종료 연산만 가능**

### Practice

```java
IntStream.of(1, 2, 3, 4, 5)
         .filter(n -> n % 2 == 0)
         .map(n -> n * 10)
         .forEach(System.out::println); // 실제 실행
```

### Common Terminal Operations

* `forEach(Consumer)`
* `collect(Collector)`
* `count()`
* `sum()`, `average()`, `min()`, `max()`
* `findFirst()`, `findAny()`
* `allMatch()`, `anyMatch()`, `noneMatch()`

***

## Comparison&#x20;

<table><thead><tr><th width="131.4921875">종류</th><th>즉시 실행 여부</th><th>Stream 반환 여부</th><th>목적</th></tr></thead><tbody><tr><td>중간 연산</td><td>아니오</td><td>예</td><td>데이터 변환 또는 필터링</td></tr><tr><td>종료 연산</td><td>예</td><td>아니오</td><td>최종 실행 및 결과 반환</td></tr></tbody></table>

***

## Conclusion

Java Stream에서 중간 연산과 종료 연산은 Stream 처리 파이프라인의 핵심 개념이다. \
중간 연산은 **데이터 흐름을 구성**하고, 종료 연산이 호출될 때까지 **실행을 유보**한다.

종료 연산이 호출되어야 전체 연산이 실제로 실행되며, 그 결과를 얻을 수 있다. \
이 개념을 이해하면 Stream API를 더욱 효율적이고 명확하게 사용할 수 있다.
