---
description: 비동기적 웹 애플리케이션 구축하기
---

# 📨 WebFlux

### WebFlux

Spring WebFlux는 비동기 및 논블로킹 모델을 기반으로 한 리액티브 웹 프레임워크이다. \
기존의 동기적인 Spring과 달리, WebFlux는 높은 확장성(scalability)을 요구하는 애플리케이션에 최적화되어 있다. \
개인적으로 진행중인 재외국인 커뮤니티 프로젝트 Immilog의 채팅서버를 구축하며\
WebFlux의 기본 개념, 장점, 코드를 통해 WebFlux를 어떻게 활용할 수 있는지 알아보았다.

***

### 1. WebFlux란 무엇인가?

Spring WebFlux는 Java 8의 리액티브 스트림(Reactive Streams) 표준을 기반으로 하여, \
이벤트 기반 비동기 처리 및 논블로킹 웹 애플리케이션을 개발할 수 있도록 지원하는 프레임워크이다. \
전통적인 동기 방식에서는 하나의 요청이 처리될 때까지 스레드가 대기하지만, \
WebFlux에서는 논블로킹 방식으로 동작하여 많은 요청을 처리할 수 있다. \
따라서 채팅과 같은 비동기적인 다수의 I/O을 받는 서버에 적합하다.

**주요 특징:**

* **논블로킹**: 요청과 응답이 대기 중인 동안 스레드가 차단되지 않는다.
* **리액티브 스트림 API**: `Mono`, `Flux`와 같은 리액티브 타입을 통해 데이터를 스트리밍 방식으로 처리한다.
* **높은 확장성**: 비동기적으로 많은 요청을 효율적으로 처리할 수 있기 때문에 I/O 작업이 많은 시스템에 적합하다.

***

### 2. WebFlux와 Spring MVC의 차이점

| 특징       | Spring MVC                 | Spring WebFlux |
| -------- | -------------------------- | -------------- |
| 요청 처리 방식 | 동기(블로킹)                    | 비동기(논블로킹)      |
| 스레드 모델   | 하나의 요청당 하나의 스레드            | 이벤트 루프 기반      |
| 리액티브 지원  | 제한적(프로젝트 리액터 미포함)          | 완전한 리액티브 지원    |
| 리턴 타입    | `ModelAndView`, `String` 등 | `Mono`, `Flux` |

***

### 3. WebFlux 설정하기

Spring WebFlux를 사용하려면, 먼저 `spring-boot-starter-webflux` 의존성을 프로젝트에 추가해야 한다.

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-webflux'
}
```

***

### 4. WebFlux의 핵심 타입: `Mono`와 `Flux`

* **Mono**: 0 또는 1개의 결과를 처리하는 리액티브 타입
* **Flux**: 0부터 N개의 결과를 처리하는 리액티브 타입

```java
@RestController
@RequestMapping("/api")
public class ReactiveController {

    @GetMapping("/mono")
    public Mono<String> getMono() {
        return Mono.just("Hello, Mono!");
    }

    @GetMapping("/flux")
    public Flux<String> getFlux() {
        return Flux.just("Hello", "World", "from", "Flux");
    }
}
```

이 코드를 통해 `/api/mono`로 요청을 보내면 단일 문자열을 비동기로 반환하고, \
`/api/flux`로 요청을 보내면 여러 문자열을 스트리밍 방식으로 반환한다.

***

### 5. 비동기 데이터베이스 처리

Spring WebFlux는 리액티브 데이터베이스 라이브러리와도 잘 통합된다고 한다. \
예를 들어, **Reactive MongoDB**와 같은 리액티브 DB를 사용할 수 있다.&#x20;

**MongoDB 의존성 추가**

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-mongodb-reactive'
}
```

```kotlin
@Repository
interface ChatRoomMongoDBRepository : ReactiveMongoRepository<ChatRoomCollection, String> {
    fun findBySenderOrRecipient(sender: User, recipient: User): Flux<ChatRoomCollection>
}
```

```kotlin
@Service
class ChatRoomService(
    private val chatRoomMongoDBRepository: ChatRoomMongoDBRepository
) {
    fun getChatRoomsByUser(user: User): Flux<ChatRoom> {
        return chatRoomMongoDBRepository.findBySenderOrRecipient(user, user)
            .map { ChatRoom.from(it) }
    }
}
```

***

### 6. 예외 처리

WebFlux에서도 전통적인 Spring MVC처럼 예외 처리를 할 수 있지만, \
리액티브 환경에 맞게 `@ExceptionHandler`를 사용하는 방법이 약간 다르다.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(RuntimeException.class)
    public Mono<ResponseEntity<String>> handleRuntimeException(RuntimeException ex) {
        return Mono.just(ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
                                      .body("An error occurred: " + ex.getMessage()));
    }
}
```

***

### 7. 테스트

리액티브 코드를 테스트할 때도 일반적인 테스트 프레임워크와 함께 사용할 수 있다. \
하지만 비동기적으로 동작하기 때문에 `StepVerifier`와 같은 리액티브 스트림 전용 테스트 도구를 \
활용하는 것이 좋다고 한다.

```java
@SpringBootTest
public class ReactiveControllerTest {

    @Autowired
    private WebTestClient webTestClient;

    @Test
    public void testMonoEndpoint() {
        webTestClient.get().uri("/api/mono")
                .exchange()
                .expectStatus().isOk()
                .expectBody(String.class)
                .isEqualTo("Hello, Mono!");
    }

    @Test
    public void testFluxEndpoint() {
        webTestClient.get().uri("/api/flux")
                .exchange()
                .expectStatus().isOk()
                .expectBodyList(String.class)
                .hasSize(4)
                .contains("Hello", "World", "from", "Flux");
    }
}
```

***

### 8. 마무리

Spring WebFlux는 비동기적이고 논블로킹 방식으로 웹 애플리케이션을 구축할 수 있게 해주며, \
높은 확장성을 제공한다. 특히, 리액티브 스트림 API를 활용하여 I/O 작업이 많은 서비스에서 \
효율적인 성능을 보장한다고 한다.&#x20;
