---
description: 비동기 이벤트 기반 고성능 네트워크 애플리케이션 프레임워크
---

# 🧚 Netty

## 머릿말

회사에서 Protocol Data <--> Json 을 파싱하는 웹플럭스 프로젝트를 맡게 되었다.\
WebFlux + Netty 를 사용하고 있고, 지식이 없는 상태에서 접하기엔 무리가 있어\
동료의 조언과 검색을 통해 미리 알아보았다. &#x20;

## Netty란?

**Netty**는 비동기 이벤트 기반의 고성능 네트워크 애플리케이션 프레임워크이다. \
`Java NIO`를 사용해 `TCP`, `UDP` 등의 프로토콜을 지원하며, \
복잡한 네트워크 프로그래밍을 단순화시켜 쉽고 빠르게 네트워크 애플리케이션을 \
개발할 수 있도록 도와주는 프레임워크다.

## Netty의 특징

* **비동기 논블로킹 I/O**: 높은 성능과 확장성을 제공
* **이벤트 드리븐 아키텍처**: 이벤트 기반으로 동작하여 효율적 리소스 관리
* **유연한 프로토콜 지원**: `HTTP`, `WebSocket` 등 다양한 프로토콜 지원
* **사용 편의성**: 복잡한 네트워크 프로그래밍을 추상화하여 난이도 낮은편

## Netty 구성&#x20;

#### 1. Channel

**`Channel`**은 네트워크 소켓이나 연결을 나타내는 인터페이스 \
주로 데이터 입출력 작업 수행

#### 2. EventLoop

**`EventLoop`**는 하나 이상의 **`Channel`**에 대한 이벤트를 처리하는 스레드\
이벤트의 등록/실행 담당

#### 3. ChannelFuture

비동기 입출력 작업의 결과 나타냄 \
작업이 완료되면 콜백 메서드를 통해 결과를 확인할 수 있음

#### 4. ChannelHandler와 Pipeline

**`ChannelHandler`**는 입출력 데이터의 처리 로직을 구현하는 곳이며, \
**`Pipeline`**은 이러한 **`Handler`**들을 연결한 체인



## Netty 동작 플로우

<figure><img src="../../.gitbook/assets/스크린샷 2024-10-28 오후 9.12.13.png" alt=""><figcaption><p>출처: 본인</p></figcaption></figure>

1. **클라이언트 연결 요청**: 클라이언트가 서버에 연결 요청
2. **BossGroup**: 서버는 `BossGroup`에서 새로운 연결 수락
3. **WorkerGroup**: 수락된 소켓 채널을 `WorkerGroup`에 전달해 입출력 작업 처리
4. **ChannelPipeline을 통한 이벤트 처리**: `WorkerGroup`의 스레드가 `ChannelPipeline`을 통해 데이터의 입출력 이벤트 처리

## 더 쉽게 이해해보자&#x20;

### Netty의 Handler와 Spring Boot의 Controller

* **Netty의 `ChannelHandler`**:&#x20;

`Netty`에서 `ChannelHandler`는 네트워크 이벤트나 데이터를 처리하는 핵심 컴포넌트이다. \
클라이언트로부터 받은 메시지를 읽고, 필요한 로직을 수행한 후 응답을 생성하여 클라이언트에게 보내는데 \
이 부분이 **Spring Boot의 Controller**와 유사하다. \
둘 다 요청을 처리하고 응답을 생성하는 역할을 담당한다!

* **Netty의 `ChannelInitializer`와 Spring Boot의 `Interceptor`**

**Netty의 `ChannelInitializer`**: \
새로운 채널이 생성될 때 호출되며, 해당 채널의 `ChannelPipeline`에 필요한 \
`ChannelHandler`들을 추가하는 역할을 한다. \
이는 채널 설정을 위한 초기화 단계로, 각 요청마다 호출되는 것은 아니다.

**Spring Boot의 Interceptor**: \
요청이 컨트롤러에 도달하기 전후에 공통된 로직을 처리하기 위해 사용된다. \
예를 들어, 인증 검증이나 로깅 등의 작업을 수행한다\


### 역할의 유사성

Netty의 `ChannelHandler`들이 `ChannelPipeline`을 통해 순차적으로 \
연결되어 데이터나 이벤트를 처리하는 방식은 Spring Boot에서 \
`Interceptor`와 `Controller`가 체인 형태로 요청을 처리하는 것과 유사하다.

### 차이점

Netty의 `ChannelInitializer`는 채널이 처음 생성될 때 한 번만 실행되어 \
`ChannelHandler`들을 설정하는 반면 Spring Boot의 `Interceptor`는 각 요청마다 실행된다

Netty에서는 여러 개의 `ChannelHandler`를 `ChannelPipeline`에 추가하여 \
요청/응답 처리 과정에서 다양한 로직을 적용할 수 있다. \
이 중 일부 핸들러는 데이터 디코딩, 인코딩, 로깅, 인증 등 공통된 작업을 처리할 수 있으며, \
이는 Spring Boot의 `Interceptor`의 역할과 유사한 편이다.



\
.
