# 👬 + Transactional Propagation

## What is Propagation?

스프링 트랜잭션의 **전파(propagation)** 옵션은, 현재 실행 중인 트랜잭션이 있을 때 \
해당 메서드가 트랜잭션을 **어떻게 처리할 것인지**에 대한 전략이다.

즉, 메서드가 호출될 때 기존 트랜잭션을 **이어받을지**, **새로 만들지**, **무시하거나 예외를 던질지** 등을 정의한다.

***

## Propagation Types

### REQUIRED

* **기존 트랜잭션이 있으면 참여**, 없으면 새로 생성
* `@Transactional`의 기본값
* 부모 트랜잭션이 롤백되면 자식도 함께 롤백됨

```java
@Transactional(propagation = Propagation.REQUIRED)// 하위도 REQUIRED면 같은 트랜잭션
```

<figure><img src="../../../../.gitbook/assets/스크린샷 2025-03-24 오후 8.22.48 (2).png" alt=""><figcaption></figcaption></figure>

***

### REQUIRES\_NEW

* 항상 **새로운 트랜잭션 생성**
* 기존 트랜잭션은 **일시 중단**
* 독립적으로 커밋/롤백 가능
* 부분 커밋이 필요한 경우 유용 (예: 로그 저장)

```java
@Transactional(propagation = Propagation.REQUIRES_NEW) // 독립 트랜잭션 실행
```

<figure><img src="../../../../.gitbook/assets/스크린샷 2025-03-24 오후 8.16.49.png" alt=""><figcaption></figcaption></figure>

***

### NESTED

* 기존 트랜잭션이 있다면 **Savepoint를 생성해서 중첩 실행**
* 내부 오류 시 Savepoint로만 롤백 가능 (전체 롤백 아님)
* DB 드라이버가 savepoint를 지원해야 동작함

```java
@Transactional(propagation = Propagation.NESTED)    // 중첩 트랜잭션 실행
```

<figure><img src="../../../../.gitbook/assets/스크린샷 2025-03-24 오후 8.23.38.png" alt=""><figcaption></figcaption></figure>

***

### SUPPORTS

* 트랜잭션이 있으면 참여, 없으면 트랜잭션 없이 실행
* 꼭 트랜잭션이 필요하지 않은 조회성 작업에 사용

```java
@Transactional(propagation = Propagation.SUPPORTS)    // 트랜잭션이 있어도 되고 없어도 됨
```

***

### NOT\_SUPPORTED

* 트랜잭션이 있으면 **중단**시키고, 트랜잭션 없이 실행
* 트랜잭션이 있으면 안 되는 작업(ex: 대용량 batch insert 등)에 사용

```java
@Transactional(propagation = Propagation.NOT_SUPPORTED)    // 트랜잭션 없이 실행됨
```

***

### NEVER

* 트랜잭션이 존재하면 예외 발생시킴
* 절대로 트랜잭션과 함께 실행되면 안 되는 작업에 사용

```java
@Transactional(propagation = Propagation.NEVER)    // 트랜잭션이 존재하면 IllegalTransactionStateException 발생
```

***

### MANDATORY

* 반드시 트랜잭션 안에서만 실행되어야 함
* 트랜잭션이 없으면 예외 발생

```java
@Transactional(propagation = Propagation.MANDATORY)    // 트랜잭션 없으면 예외 발생
```

***

## Comparison Table

| Propagation    | 기존 트랜잭션 존재 시    | 새 트랜잭션 생성 여부 | 롤백 영향     |
| -------------- | --------------- | ------------ | --------- |
| REQUIRED       | 참여              | 있음           | 부모에 의존    |
| REQUIRES\_NEW  | 무시 (일시 중단)      | 항상 생성        | 독립적 처리    |
| NESTED         | Savepoint로 중첩   | 없음           | 내부 롤백만 가능 |
| SUPPORTS       | 있으면 참여 / 없으면 없음 | 생성 안 함       | 부모에 의존    |
| NOT\_SUPPORTED | 중단 후 비트랜잭션 실행   | 없음           | 없음        |
| NEVER          | 트랜잭션 존재 시 예외    | 없음           | 없음        |
| MANDATORY      | 트랜잭션 없으면 예외     | 생성 안 함       | 부모에 의존    |

***

## Caveats

* `REQUIRES_NEW`는 **기존 트랜잭션과 완전히 분리**되므로, 예외가 발생해도 부모 트랜잭션에는 영향 없음
* `NESTED`는 rollback 처리가 **savepoint 수준**에서만 가능하며, DB나 JPA 설정에 따라 동작하지 않을 수도 있음
* `NEVER`, `MANDATORY`는 잘 쓰이지 않지만, **특정 트랜잭션 보안 정책**을 강제할 때 유용함
* 트랜잭션 전파는 메서드 간 호출 구조가 복잡할수록 **예상치 못한 결과**를 만들 수 있으므로 신중하게 사용해야 함

***

## Conclusion

트랜잭션 전파는 단순한 설정이지만, 실제 프로젝트에서는 로깅, 결제, 알림 등 다양한 작업에서 \
**부분 커밋**, **롤백 제어**, **독립 실행**이 필요한 경우가 많기 때문에 제대로 이해하고 있어야 한다.

전파 속성을 잘 활용하면 트랜잭션 흐름을 명확하게 제어할 수 있고, \
잘못 사용하면 의도치 않은 롤백이나 데이터 정합성 오류를 유발할 수 있다.

꼭 직접 테스트 케이스를 만들어 상황별로 돌려보는 걸 추천한다.
