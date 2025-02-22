# 🦋 Java : Synchronization

## Synchronization in Java

동기화는 여러 쓰레드가 동시에 동일한 리소스에 접근할 때 발생할 수 있는 문제를 방지하기 위한 메커니즘이다. \
동기화는 멀티쓰레딩 환경에서 데이터의 일관성을 보장하기 위해 필수적이다. \
자바에서는 다양한 방식으로 동기화를 처리할 수 있는데 전통적인 방식부터 요즘 많이 쓰이는 순으로 작성해보았다.

***

## @Synchronized

`@synchronized` 키워드는 메서드나 특정 블록을 임계 구역으로 설정하여 \
하나의 쓰레드만 해당 코드에 접근할 수 있도록 한다. \
하지만 최근에는 더 나은 동기화 기법들이 등장하면서 `@synchronized`는 자주 사용되지 않게 되었다.

### Cons

* **성능 저하**: `@synchronized`는 자바의 기본적인 동기화 방식으로, 성능이 다소 떨어질 수 있다.
* **유연성 부족**: 세밀한 동기화 제어를 제공하지 않는다.

### Example

```java
public class SynchronizedExample {
    private int count = 0;

    public synchronized void increment() {
        count++;
    }

    public synchronized int getCount() {
        return count;
    }
}
```

### Flow

<figure><img src="../../../.gitbook/assets/스크린샷 2025-01-16 오후 10.20.59.png" alt=""><figcaption></figcaption></figure>

***

## ReentrantLock

`ReentrantLock`은 자바의 `java.util.concurrent.locks` 패키지에 포함된 클래스이다. \
기존의 `@synchronized` 키워드보다 세밀한 제어와 향상된 성능을 제공한다.

### **Pros**

* **공정성 옵션**: 락을 획득하려는 쓰레드의 순서를 제어할 수 있다.
* **시간 초과**: 락을 대기하는 시간 제한을 설정할 수 있다.
* **락 해제 제어**: 명시적으로 락 해제를 호출해야 하므로 코드가 더 명확해진다.

### **Excample**

```java
import java.util.concurrent.locks.ReentrantLock;

public class ReentrantLockExample {
    private final ReentrantLock lock = new ReentrantLock();
    private int count = 0;

    public void increment() {
        lock.lock();
        try {
            count++;
        } finally {
            lock.unlock();
        }
    }

    public int getCount() {
        lock.lock();
        try {
            return count;
        } finally {
            lock.unlock();
        }
    }
}
```

### Flow

<figure><img src="../../../.gitbook/assets/스크린샷 2025-01-16 오후 10.22.39.png" alt=""><figcaption></figcaption></figure>

***

## StampedLock

`StampedLock`은 자바 8에 도입된 동기화 메커니즘으로, 읽기와 쓰기를 구분하는 락을 제공한다. \
이는 읽기 작업이 많은 환경에서 특히 유용하다.

### **Pros**

* **낮은 락 경쟁**: 읽기 락과 쓰기 락을 분리하여 락 경쟁을 줄인다.
* **낙관적 잠금**: 읽기 작업에서 잠금을 사용하지 않고도 데이터의 일관성을 유지할 수 있다.

### **Example**

```java
import java.util.concurrent.locks.StampedLock;

public class StampedLockExample {
    private final StampedLock lock = new StampedLock();
    private int count = 0;

    public void increment() {
        long stamp = lock.writeLock();
        try {
            count++;
        } finally {
            lock.unlockWrite(stamp);
        }
    }

    public int getCount() {
        long stamp = lock.tryOptimisticRead();
        int currentCount = count;
        if (!lock.validate(stamp)) {
            stamp = lock.readLock();
            try {
                currentCount = count;
            } finally {
                lock.unlockRead(stamp);
            }
        }
        return currentCount;
    }
}
```

### Flow

<figure><img src="../../../.gitbook/assets/스크린샷 2025-01-16 오후 10.24.00.png" alt=""><figcaption></figcaption></figure>

***

## Atomic Variables

`Atomic` 클래스를 사용하면 동기화 없이도 쓰레드 간 안전한 연산을 수행할 수 있다. \
이는 주로 간단한 값 업데이트 작업에 적합하다.

### **Pros**

* **성능**: 내부적으로 CAS(Compare-And-Swap) 알고리즘을 사용하여 락 없이 동기화를 구현한다.
* **간결성**: 복잡한 락 제어가 필요하지 않다.

### **Example**

```java
import java.util.concurrent.atomic.AtomicInteger;

public class AtomicExample {
    private final AtomicInteger count = new AtomicInteger(0);

    public void increment() {
        count.incrementAndGet();
    }

    public int getCount() {
        return count.get();
    }
}
```

### Flow

<figure><img src="../../../.gitbook/assets/스크린샷 2025-01-16 오후 10.25.30.png" alt=""><figcaption></figcaption></figure>

***

## CompletableFuture (Recommended!)

`CompletableFuture`는 비동기 프로그래밍을 위한 고급 도구로, 동기화 문제를 효율적으로 처리할 수 있다.

요즘 가장 선호되는 방식 중 하나인데, 비동기 작업을 쉽게 처리할 수 있게 해주고, \
병렬 처리를 간결하게 구현할 수 있으며, 동기화 문제를 완전히 다른 차원에서 접근하게 만들어서 \
더 자연스럽고 효율적인 비동기 프로그래밍을 가능하게 한다.

특히 `thenApply`, `thenAccept` 같은 체이닝 메서드를 사용하면 코드 가독성도 좋아지고 \
복잡한 동기화 문제를 최소화할 수 있다. \
현대적인 Java 애플리케이션에서는 비동기 프로그래밍이 중요해지면서 더 많이 사용되고 있다고 한다.

### Pros

* **비동기 작업**: 동기화의 필요성을 최소화한다.
* **병렬 처리**: 복잡한 작업을 병렬로 처리하고 결과를 결합할 수 있다.

**사용 예제**

```java
import java.util.concurrent.CompletableFuture;

public class CompletableFutureExample {
    public static void main(String[] args) {
        CompletableFuture.supplyAsync(() -> {
            // 비동기 작업
            return 42;
        }).thenApply(result -> {
            // 결과 처리
            return result * 2;
        }).thenAccept(System.out::println);
    }
}
```

### Flow

<figure><img src="../../../.gitbook/assets/스크린샷 2025-01-16 오후 10.28.14.png" alt=""><figcaption></figcaption></figure>

***

## Conclusion

현대의 자바 동기화 방식은 다양한 요구사항에 맞춰 진화해왔다. \
단순 동기화 작업에는 `Atomic` 클래스를, \
복잡한 제어가 필요한 경우에는 `ReentrantLock/StampedLock` 를,\
비동기 작업에는 `CompletableFuture`를 활용하는 것이 좋다.&#x20;

`@synchronized`는 여전히 전통적인 방식으로 많이 사용되지만, \
최신 동기화 메커니즘을 사용하는 것이 일반적으로 더 효율적이고 적합하다.
