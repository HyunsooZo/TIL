---
description: 자바에서 스레드 관련 클래스 및 메서드, 스레드의 동기화 문제와 해결 방법 s
---

# 🧩 Java : Thread

## Thread?&#x20;

**스레드**는 **프로세스 내에서 실행되는 작은 실행 단위**이다. \
**프로세스**는 **운영 체제에서 실행 중인 프로그램의 인스턴스**이며, \
각 프로세스는 최소 하나의 메인 스레드를 포함한다.&#x20;

**스레드는 프로세스 내의 코드 실행 흐름**을 나타내며, \
각 스레드는 고유의 실행 스택과 레지스터를 가지지만, \
**같은 프로세스 내의 다른 스레드들과 메모리를 공유**한다.

<figure><img src="../../../.gitbook/assets/스크린샷 2024-11-09 오후 10.14.40.png" alt=""><figcaption></figcaption></figure>

## Multi-Threading?&#x20;

멀티스레딩은 **하나의 프로세스가 동시에 여러 작업을 수행할 수 있도록 하는 기법**이다. \
예를 들어, 사용자 인터페이스 스레드가 애플리케이션의 반응성을 유지하는 동안 \
백그라운드에서 데이터 처리가 이루어질 수 있다. \
이러한 구조는 자원의 효율적인 사용과 프로그램의 성능 향상에 기여한다.

자바는 `Thread` 클래스와 `Runnable` 인터페이스를 사용하여 멀티스레딩을 구현할 수 있다. \
스레드는 독립적인 실행 흐름을 가지므로, 여러 스레드가 병렬로 실행될 때 각 스레드는 다른 스레드의 실행에 \
영향을 주지 않고 작업을 진행할 수 있다. \
그러나 **이로 인해 동기화 문제나 데드락과 같은 복잡한 상황이 발생**할 수 있다.

## Difference Between Thread and Process

### **Process**

운영 체제에 의해 실행되는 **독립적인 실행 단위**이며, 각 프로세스는 자체 메모리 공간을 가진다. \
**프로세스는 하나 이상의 스레드를 포함할 수** 있으며, 각 프로세스는 운영 체제에서 \
**별도의 메모리 공간을 할당받아 독립적으로 실행**된다.

### Thread

같은 프로세스 내에서 실행되는 작은 단위로, 다른 스레드와 메모리 공간을 공유하여 더 가벼운 실행을 할 수 있다. \
스레드는 **같은 프로세스 내에서 자원을 공유**하므로 프로세스보다 **생성 및 관리 비용이 적고, 빠르게 상호작용**할 수 있다.

## Thread In Java

자바에서 스레드를 생성하는 방법은 크게 두 가지로 나뉜다. \
첫 번째는 `Thread` 클래스를 상속받는 방법이고, \
두 번째는 `Runnable` 인터페이스를 구현하는 방법이다.

참고로 `Runnable` 인터페이스를 구현하는 방법은 다중 상속 문제를 피할 수 있어 더 유연한 설계를 가능하게 한다.

```java
// Thread 상속 받기
class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Thread is running");
    }
}

public class Main {
    public static void main(String[] args) {
        MyThread thread = new MyThread();
        thread.start(); // 스레드 시작
    }
}

// Runnable 구현하기
class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println("Runnable is running");
    }
}

public class Main {
    public static void main(String[] args) {
        Thread thread = new Thread(new MyRunnable());
        thread.start(); // 스레드 시작
    }
}
```

## Lifecycle&#x20;

자바의 스레드는 다음과 같은 생명 주기를 가진다:

<figure><img src="../../../.gitbook/assets/스크린샷 2024-11-09 오후 10.20.01.png" alt=""><figcaption></figcaption></figure>

1. **NEW**: 스레드가 생성되었지만 `start()` 메서드가 호출되지 않은 상태이다.
2. **RUNNABLE**: `start()` 메서드가 호출되어 실행 가능한 상태로 전환되었지만, 실제 CPU에 의해 실행 중인지 여부는 확정되지 않았다.
3. **BLOCKED**: 스레드가 잠금이 해제되기를 기다리고 있는 상태이다.
4. **WAITING**: 스레드가 특정 조건이 충족될 때까지 대기하고 있는 상태이다.
5. **TIMED\_WAITING**: 일정 시간 동안 대기하는 상태이다.
6. **TERMINATED**: 스레드의 실행이 종료된 상태이다.



## Synchronization

멀티스레드 환경에서는 두 개 이상의 스레드가 동일한 자원을 동시에 접근할 때 동기화 문제가 발생할 수 있다. \
이를 해결하기 위해 `synchronized` 키워드를 사용하여 임계 구역을 설정할 수 있다.

```java
public class SynchronizedExample {
    private int count = 0;

    public void increment() {
        synchronized (this) {
            count++;
        }
    }

    public int getCount() {
        return count;
    }

    public static void main(String[] args) {
        SynchronizedExample example = new SynchronizedExample();
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                example.increment();
            }
        });
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                example.increment();
            }
        });
        t1.start();
        t2.start();

        try {
            t1.join();
            t2.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        System.out.println("Final count: " + example.getCount());
    }
}
```

## Deadlock

데드락은 두 개 이상의 스레드가 서로 자원을 점유한 채로 영원히 대기 상태에 빠지는 현상이다. \
데드락을 방지하려면 다음과 같은 전략을 사용할 수 있다:

* 락을 획득하는 순서를 일관되게 유지한다.
* 타임아웃을 사용하여 락을 기다리는 시간을 제한한다.
* 데드락 탐지 알고리즘을 구현한다.

<figure><img src="../../../.gitbook/assets/스크린샷 2024-11-09 오후 10.23.23.png" alt=""><figcaption></figcaption></figure>
