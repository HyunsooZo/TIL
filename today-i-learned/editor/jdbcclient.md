---
description: JdbcClient에 대한 간단한 소개 및 사용법을 정리해 보았다.
---

# 📑 JdbcClient

### Spring 6.1: `JdbcClient` 소개 및 활용

Spring 6.1에서는 새로운 데이터베이스 접근 방식을 제공하는 `JdbcClient`가 도입되었다. \
기존의 `JdbcTemplate`와 비슷한 방식으로 작동하지만, 더 간결한 API와 Java 8 이후 추가된 기능들을 \
자연스럽게 활용할 수 있도록 설계되었다. \
특히, 리액티브하지 않으면서도 효율적이고 간단하게 데이터베이스 작업을 처리할 때 효과적이다.

**1. `JdbcClient`란?**

`JdbcClient`는 `JdbcTemplate`을 개선한 형태로, SQL 문을 실행하고, 결과를 매핑하는 과정을 더 직관적이고 쉽게 수행할 수 있게 해준다. 또한, 메서드 체이닝을 통해 SQL 실행 흐름을 보다 간결하게 표현할 수 있으며, 결과 매핑을 위한 기능도 강화되었다.

**2. 설정 및 의존성 추가**

`JdbcClient`를 사용하려면 Spring 6.1 이상의 버전에서 사용할 수 있으며, Spring Boot 프로젝트에서는 다음과 같은 의존성을 추가하면 된다.

```groovy
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-data-jdbc'
}
```

**3. `JdbcClient` 기본 사용법**

* `sql(String)` : SQL 문을 정의한다.
* `param(String, Object)` : SQL에 사용할 파라미터를 정의한다.
* `fetch()` : 데이터를 조회한다.
* `update()` : 데이터를 수정하거나 삽입할 때 사용한다.

```java
@Autowired
private JdbcClient jdbcClient;

public List<User> findAllUsers() {
    return jdbcClient.sql("SELECT * FROM users")
                     .fetch()
                     .list((rs, rowNum) -> new User(
                         rs.getLong("id"),
                         rs.getString("name"),
                         rs.getString("email")
                     ));
}
```

위 코드는 SQL 문을 작성하고 결과를 `User` 객체로 매핑하는 간단한 과정이다. \
`JdbcTemplate`과 비교하면코드가 훨씬 간결해진 것을 볼 수 있다.

**4. 파라미터 바인딩**

SQL 쿼리에서 파라미터를 동적으로 전달할 때는 `param` 메서드를 사용할 수 있다.

```java
public User findUserById(Long id) {
    return jdbcClient.sql("SELECT * FROM users WHERE id = :id")
                     .param("id", id)
                     .fetch()
                     .one((rs, rowNum) -> new User(
                         rs.getLong("id"),
                         rs.getString("name"),
                         rs.getString("email")
                     ));
}
```

**5. 데이터 삽입 및 수정**

데이터 삽입이나 수정을 위한 쿼리도 매우 직관적이다.&#x20;

```java
java코드 복사public int insertUser(User user) {
    return jdbcClient.sql("INSERT INTO users (name, email) VALUES (:name, :email)")
                     .param("name", user.getName())
                     .param("email", user.getEmail())
                     .update();
}
```

`update()`는 데이터베이스에서 수정된 행의 수를 반환한다.

**6. 트랜잭션 관리**

`JdbcClient`는 트랜잭션과 함께 사용할 수 있다. \
Spring의 `@Transactional` 애너테이션을 활용하면 쉽게 트랜잭션을 적용할 수 있다.

```java
java코드 복사@Transactional
public void updateUserEmail(Long id, String newEmail) {
    jdbcClient.sql("UPDATE users SET email = :email WHERE id = :id")
              .param("email", newEmail)
              .param("id", id)
              .update();
}
```

**7. 요약**

Spring 6.1의 `JdbcClient`는 `JdbcTemplate`보다 더 간결하고 직관적인 API를 제공한다. SQL 쿼리 작성 및 파라미터 바인딩, 결과 매핑 과정이 모두 간소화되어 개발자들이 더 쉽게 데이터베이스 작업을 처리할 수 있도록 도와준다.

### `JdbcClient`와 `QueryDSL` 비교

나는 주로 JPA와 `QueryDSL`을 사용하는 편이었는데, \
실제로 사용해보니 `JdbcClient`가 훨씬 사용하기 편하고 직관적으로 다가왔다. \
또한, 외국 개발자 커뮤니티에서는 `QueryDSL`이 "죽은 프로젝트"라는 글이 돌고 있다.

실제로 `QueryDSL`은 2024년 기준으로 버그 수정을 제외하면 \
이렇다 할 수정이나 개선 사항이 없는 상태다. \
대체제로 `JOOQ`가 거론되기도 하지만, 실제로 `JOOQ`를 사용해본 입장에서는 \
`QueryDSL`보다 이렇다 할 나은 점을 느끼진 못했다.

아래는 `QueryDSL`과 `JdbcClient`의 특징및 성능을 비교한 표다.\
개인적으로는 프로젝트 버전만 받쳐준다면 `JdbcClient` 사용이 생산성/유지보수 \
측면에서 훨씬 유리한 선택이 될 것이라 생각한다.

#### 특징 비교

<table><thead><tr><th width="207">항목</th><th>JdbcClient</th><th>QueryDSL</th></tr></thead><tbody><tr><td><strong>주요 목적</strong></td><td>SQL 기반 데이터베이스 작업을 간단하고 직관적으로 처리</td><td>타입 안전한 JPQL/HQL 쿼리 생성 및 동적 쿼리 지원</td></tr><tr><td><strong>SQL 작성 방식</strong></td><td>직접 SQL 작성 (문자열 기반)</td><td>Java 코드로 쿼리 작성 (타입 안전성 보장)</td></tr><tr><td><strong>동적 쿼리</strong></td><td>제한적 (파라미터 바인딩으로 처리)</td><td>매우 강력함. Java 문법을 활용하여 동적으로 쿼리 작성 가능</td></tr><tr><td><strong>타입 안전성</strong></td><td>SQL 문자열에 의존하여 컴파일 시 타입 체크가 되지 않음</td><td>Java 객체와 메서드 기반으로 컴파일 시 타입 안전성 보장</td></tr><tr><td><strong>쿼리 파라미터 바인딩</strong></td><td><code>param()</code> 메서드를 사용해 파라미터를 바인딩</td><td><code>BooleanExpression</code> 등을 사용해 파라미터를 안전하게 바인딩</td></tr><tr><td><strong>트랜잭션 관리</strong></td><td>Spring의 <code>@Transactional</code>로 간단하게 관리 가능</td><td>JPA의 트랜잭션 관리를 통해 기본적으로 제공됨</td></tr><tr><td><strong>결과 매핑</strong></td><td>직접 매핑 (<code>fetch().list()</code>, <code>fetch().one()</code>)</td><td>JPA 엔티티 자동 매핑 및 별도 DTO로 매핑 가능</td></tr><tr><td><strong>러닝 커브</strong></td><td>상대적으로 낮음, SQL에 익숙하면 바로 사용 가능</td><td>상대적으로 높음, <code>QueryDSL</code>의 DSL(도메인 특화 언어)에 익숙해져야 함</td></tr><tr><td><strong>유연성</strong></td><td>SQL의 모든 기능을 활용할 수 있음</td><td>동적 쿼리 및 조건 추가에 매우 유연함</td></tr><tr><td><strong>지원되는 DB</strong></td><td>SQL을 지원하는 모든 데이터베이스</td><td>JPA 또는 Hibernate와 연동되는 모든 데이터베이스</td></tr><tr><td><strong>복잡한 쿼리 작성</strong></td><td>복잡한 SQL 쿼리도 자유롭게 작성 가능</td><td>복잡한 동적 쿼리를 타입 안전하게 작성 가능</td></tr><tr><td><strong>라이브러리 의존성</strong></td><td><code>spring-boot-starter-data-jdbc</code></td><td><code>querydsl-jpa</code>, <code>querydsl-apt</code>, JPA 의존성 필요</td></tr><tr><td><strong>성능</strong></td><td>SQL을 직접 실행하여 성능 최적화 가능</td><td>Hibernate나 JPA를 거쳐 실행되므로 약간의 오버헤드 발생 가능</td></tr></tbody></table>

#### 성능  비교 

| 항목            | JdbcClient                  | QueryDSL                        |
| ------------- | --------------------------- | ------------------------------- |
| **실행 속도**     | 빠름. SQL을 직접 실행하므로 성능 최적화 가능 | ORM 계층을 통해 실행되므로 오버헤드 발생        |
| **쿼리 생성 방식**  | SQL 직접 작성, 빠르지만 유지보수 어려움    | 객체지향적으로 쿼리 생성, 유지보수성 뛰어남, 다소 느림 |
| **캐시 활용**     | 기본적으로 제공하지 않음               | JPA의 1차 및 2차 캐시를 활용 가능          |
| **복잡한 쿼리 성능** | 쿼리 최적화 가능, 빠르지만 수동 작업 필요    | 복잡한 매핑이 자동화되지만 성능 저하 가능         |
| **동적 쿼리**     | 구현 어려움, 동적 쿼리 생성 시 성능 저하 가능 | 동적 쿼리 작성이 용이하고 유지보수에 유리         |
| **타입 안전성**    | 없음. SQL을 문자열로 작성            | 타입 안전성을 보장, 컴파일 시점에 오류 발견 가능    |

