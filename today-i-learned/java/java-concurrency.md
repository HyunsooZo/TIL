---
description: '부제 : CompletableFuture vs ForkJoinPool & Structured Concurrency'
---

# 🟰 Java : Concurrency

## CompletableFuture

### CompletableFuture?

`CompletableFuture`는 Java 8에서 도입된 비동기 작업 처리 API이다. \
기존의 `Future`와 달리 비동기 작업의 결과를 기다리지 않고, \
작업 완료 후의 동작을 지정할 수 있다. \
또한, 여러 작업을 연결하거나 조합하여 복잡한 비동기 로직을 간결하게 표현할 수 있다.

*   **비동기 작업의 흐름**

    ```java
    CompletableFuture.supplyAsync(() -> "Fetch Data")
                     .thenApply(data -> data + " Processed")
                     .thenAccept(System.out::println);
    ```

    위 코드는 다음 순서로 동작한다

    1. 데이터를 비동기적으로 가져옴
    2. 데이터를 처리
    3. 결과를 출력
*   **에러 처리** CompletableFuture는 예외를 처리하기 위한 다양한 메서드를 제공한다.

    ```java
    CompletableFuture.supplyAsync(() -> { throw new RuntimeException("Error"); })
                     .exceptionally(ex -> "Recovered from " + ex.getMessage())
                     .thenAccept(System.out::println);
    ```

    위 코드는 예외 발생 시 이를 처리하고, 복구된 결과를 출력한다.

### **Flow**

<figure><img src="../../.gitbook/assets/스크린샷 2025-01-09 오후 8.23.02.png" alt=""><figcaption></figcaption></figure>

***

## ForkJoinPool

### ForkJoinPool?

`ForkJoinPool`은 Java 7에서 도입된 프레임워크로, \
병렬 작업을 처리하기 위해 설계되었다. \
작업을 작은 단위로 분할(fork)하고 병합(join)하여 높은 처리 성능을 제공한다.

*   **RecursiveTask 사용**

    ```java
    public class SumTask extends RecursiveTask<Integer> {
        private final int[] array;
        private final int start, end;

        public SumTask(int[] array, int start, int end) {
            this.array = array;
            this.start = start;
            this.end = end;
        }

        @Override
        protected Integer compute() {
            if (end - start <= 10) {
                return Arrays.stream(array, start, end).sum();
            }
            int mid = (start + end) / 2;
            SumTask left = new SumTask(array, start, mid);
            SumTask right = new SumTask(array, mid, end);
            left.fork();
            return right.compute() + left.join();
        }
    }
    ```
*   **ForkJoinPool 사용 예**

    ```java
    ForkJoinPool pool = new ForkJoinPool();
    int[] array = IntStream.range(0, 100).toArray();
    int sum = pool.invoke(new SumTask(array, 0, array.length));
    System.out.println("Sum: " + sum);
    ```

### **Flow**

<figure><img src="../../.gitbook/assets/스크린샷 2025-01-09 오후 8.25.44.png" alt=""><figcaption></figcaption></figure>

***

## Comparison

| **Feature** | **CompletableFuture** | **ForkJoinPool**   |
| ----------- | --------------------- | ------------------ |
| 사용 목적       | 비동기 작업                | 병렬 작업 분할-정복        |
| 코드 가독성      | 높음                    | 중간                 |
| 유연성         | 높음                    | 낮음 (주로 재귀적 작업에 적합) |
| 적합한 작업      | HTTP 요청 등 IO 작업       | CPU 집중형 작업         |

***

## Structured Concurrency in Java 21

## Structured Concurrency?

Structured Concurrency는 Java 21에서 도입된 개념으로, \
**비동기 작업을 계층적으로 관리**하여 작업의 수명 주기를 명확히 한다. \
이는 복잡한 비동기 작업에서도 코드의 안정성과 유지보수성을 보장한다.

**주요 특징:**

* 작업이 부모 작업의 컨텍스트에 묶여 있다.
* 비동기 작업이 실패하면 전체 작업 트리를 안전하게 중단한다.
* 리소스 누수 방지.

***

### How to Use Structured Concurrency

Structured Concurrency를 사용하려면 `StructuredTaskScope`를 활용하면 된다.

```java
try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
    Future<String> task1 = scope.fork(() -> fetchDataFromAPI());
    Future<Integer> task2 = scope.fork(() -> calculateResult());

    scope.join(); // 모든 작업 완료 대기
    scope.throwIfFailed();

    String result1 = task1.resultNow();
    int result2 = task2.resultNow();

    System.out.println(result1 + ", " + result2);
}
```

* **ShutdownOnFailure**: 작업 중 하나라도 실패하면 전체 작업 중단.
* **ThrowIfFailed**: 예외를 처리하고 필요한 경우 호출.

***

### **Flow**

<figure><img src="../../.gitbook/assets/스크린샷 2025-01-09 오후 8.24.38.png" alt=""><figcaption></figcaption></figure>

***

### Benefits

* **가독성**: 비동기 작업이 계층 구조로 명확히 관리된다.
* **디버깅 용이**: 작업 트리가 명확하여 문제를 추적하기 쉽다.
* **안정성**: 작업 실패 시 전체 작업 중단으로 안정성 보장.
