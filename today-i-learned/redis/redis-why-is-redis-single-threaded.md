---
description: >-
  Redis는 빠르기로 유명한 인메모리 데이터 저장소이다. 많은 사람들이 처음 접할 때 의문을 갖는다.  "왜 Redis는 멀티스레드가 아닌
  싱글스레드인가?" 이 포스팅에서는 그 이유와 싱글스레드의 이점을 설명한다.
---

# Redis : Why is Redis Single-Threaded?

## Core Design Philosophy

Redis는 싱글스레드 기반의 이벤트 루프(event loop) 구조로 동작한다. \
이는 select(), epoll(), kqueue 등 OS의 비동기 IO 메커니즘을 활용하여 모든 요청을 순차적으로 처리하는 구조이다. \
이러한 구조는 다음과 같은 철학에서 출발한다.

### **Simple is Fast**

복잡한 락(lock) 관리 없이 단일 쓰레드로 처리하면 코드가 단순해진다. \
이는 디버깅, 테스트, 유지보수 측면에서 유리하다.

### **Memory Access is King**

Redis의 모든 데이터는 메모리에 상주하므로, 네트워크 요청만 잘 처리하면 성능 병목은 거의 없다. \
CPU 연산보다 메모리 접근과 네트워크가 병목이 되기 쉬운 구조에서는 멀티스레드로 인한 이득이 크지 않다.

***

## Advantages of a Single-Threaded Model

### **No Lock Contention**

모든 작업이 하나의 쓰레드에서 순차적으로 처리되므로 락을 사용할 필요가 없다. \
이는 락 경합으로 인한 성능 저하를 방지한다.

### **Predictable Performance**

요청이 병렬로 처리되지 않기 때문에, 처리 순서와 타이밍이 예측 가능하다. \
이는 시스템 안정성과 디버깅에 큰 이점을 준다.

### **Minimal Context Switching**

멀티스레드 환경에서는 쓰레드 간 전환(context switch) 오버헤드가 존재한다. \
Redis는 이를 회피하고 CPU 캐시 효율성을 높인다.

***

## What About Multi-Core CPUs?

Redis는 단일 쓰레드로 클라이언트 명령을 처리하지만, \
백업(snapshotting), AOF 저장, 복제(replication) 등 일부 작업은 **백그라운드 쓰레드**로 수행한다. \
또한 Redis Cluster 또는 여러 인스턴스를 사용하여 멀티코어 환경을 적극적으로 활용할 수 있다.

***

## Multithreading in Recent Versions

Redis 6부터는 **I/O 멀티스레딩**을 선택적으로 지원한다. \
클라이언트와의 read/write 처리에 멀티스레드를 활용하여 네트워크 병목을 해소할 수 있도록 설계되었다. \
하지만 여전히 명령 처리 자체는 싱글스레드로 유지된다.

***

## Conclusion

Redis는 단순한 구조, 예측 가능한 성능, 락 경합 없는 설계 등을 이유로 싱글스레드 방식을 채택하였다. \
이는 Redis의 주요 사용 목적(캐싱, 빠른 조회)에 매우 잘 들어맞는 설계이며, \
실제로 이 구조 덕분에 Redis는 놀라운 성능을 제공하고 있다.
