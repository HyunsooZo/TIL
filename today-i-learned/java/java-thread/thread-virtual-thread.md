---
description: Java 21 의 가상 스레드에 관해
---

# 🕶️ Java : Virtual Thread

Java 21 에서 도입된 가상 스레드는 Java의 스레드 모델을 개선해 동시성 처리 성능을 크게 향상 시켰다.

## **기존 스레드 모델의 문제점**

기존 스레드 모델의 경우 커널 스레드 기반으로 운영체제에 의해 관리되는 실제 스레드에 매핑되고,\
성능이 좋고 안정적이다.

하지만&#x20;

**비용이 비싸다** :\
&#x20;커널 스레드는 생성, 스위칭, 관리를 운영체제가 처리하기 때문에 무겁다. \
따라서 많은 스레드를 동시에 생성하면 성능 문제나 메모리 오버헤드가 발생할 수 있다.

**블로킹 I/O :** \
스레드가 블로킹 I/O 작업을 할 때, 다른작업들이 기다려야 하는 상황이 발생하고 이를 위해 \
비동기 I/O 처리를 하기도 하지만 이는 코드복잡도를 향상시키는 부작용이 있다.&#x20;

## **가상 스레드**

Java 21의 가상 스레드는 **커널 스레드**와는 달리, **JVM**이 관리하는 매우 가벼운 스레드다.\
기존의 스레드처럼 동작하지만, 운영체제의 커널 스레드와 직접적으로 매핑되지 않아 \
대규모의 스레드를 쉽게 생성하고 관리할 수 있다.

**주요 특징:**

**가벼움**: \
가상 스레드는 커널 스레드보다 훨씬 가볍다. \
수백만 개의 스레드도 생성할 수 있을 정도로 메모리와 자원을 적게 사용한다.

**동시성 처리 개선**: \
많은 수의 스레드를 효율적으로 처리할 수 있기 때문에, \
대규모 동시성을 요구하는 작업(예: 서버 애플리케이션)에 유리함.

**기존 코드와의 호환성**: \
기존 스레드와 가상 스레드는 서로 교체 가능하며, \
큰 코드 수정 없이 가상 스레드를 적용할 수 있다 .&#x20;

**블로킹 I/O 문제 해결**: \
가상 스레드는 블로킹 I/O 작업 중에 스레드를 자동으로 중단(suspend)하고,\
&#x20;I/O가 완료되면 다시 실행할 수 있다. \
이는 비동기 I/O를 사용하지 않고도, 동기식 코드 작성이 가능하다는 의미이다.

## **예시 코드**&#x20;

1\. `Thread.ofVirtual()`

```java
public class VirtualThreadExample {
    public static void main(String[] args) {
        // 가상 스레드 생성 및 실행
        Thread virtualThread = Thread.ofVirtual().start(() -> {
            System.out.println("Hello from a virtual thread!");
        });

        // 가상 스레드가 끝날 때까지 대기
        try {
            virtualThread.join();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}
```

2. 가상 스레드 많이 생성 해보기 (병렬스트림 사용 시 더 많은 병렬처리도 가능)

```java
import java.util.stream.IntStream;

public class ManyVirtualThreadsExample {
    public static void main(String[] args) {
        // 병렬로 가상 스레드를 실행
        IntStream.range(0, 100000)
                 .parallel()
                 .forEach(i -> Thread.ofVirtual().start(() -> {
                     System.out.println("Virtual thread: " + Thread.currentThread());
                 }));
    }
}
```

3. 가상 스레드 풀 사용 (개인 프로젝트 시 비동기 기능과 함께 사용)

```java
import com.backend.immilog.global.exception.AsyncUncaughtExceptionHandlerCustom;
import org.springframework.aop.interceptor.AsyncUncaughtExceptionHandler;
import org.springframework.context.annotation.Configuration;
import org.springframework.scheduling.annotation.AsyncConfigurerSupport;
import org.springframework.scheduling.annotation.EnableAsync;

import java.util.concurrent.Executor;
import java.util.concurrent.Executors;


@Configuration
@EnableAsync
public class AsyncConfig extends AsyncConfigurerSupport {

    @Override
    public Executor getAsyncExecutor() {
        return Executors.newVirtualThreadPerTaskExecutor();
    }

    @Override
    public AsyncUncaughtExceptionHandler getAsyncUncaughtExceptionHandler() {
        return new AsyncUncaughtExceptionHandlerCustom();
    }
}
```

## 정리

핵심 내용 자바는 이제 수백만 개의 가상 스레드를 동시에 실행할 수 있게 됐다. \
OS 스레드를 직접 사용하지 않고 JDK에서 사용자 모드로 구현.\
\
왜 필요했나? \
서버는 많은 동시 요청을 처리해야 하는데, OS 스레드는 너무 무거워서 한계가 있었다.\
\
구현 \
기존 Thread 클래스를 추상화해서 가상 스레드를 구현했다. \
컨티뉴에이션과 스케줄러를 결합한 형태로, 기존 코드와 호환성을 유지하면서도 가볍게 만들었다. \
\
장점과 특징 \
\- 초당 300만개 이상의 새로운 스레드 생성 가능 \
\- 기존 자바 코드와 완벽한 호환 \
\- 스레드 풀이 필요 없어짐 \
\- 디버깅과 프로파일링 도구들도 그대로 사용 가능\
\
이제 개발자들이 스레드를 "관리해야 할 리소스"로 보는 관점을 바꾸는 게 중요하다. \
가상 스레드는 비즈니스 로직을 표현하는 객체로 봐야 하기때문!\![\
](https://www.threads.net/@greatherakles)
