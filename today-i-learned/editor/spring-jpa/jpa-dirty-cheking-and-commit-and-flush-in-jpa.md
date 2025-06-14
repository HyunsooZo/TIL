---
description: >-
  JPA에서는 엔티티를 저장하거나 수정한 후, 실제로 DB에 반영되는 시점이 언제인지 명확히 이해하는 것이 중요하다.  이번 포스팅에서는
  flush()와 commit()의 차이점, 그리고 JPA 내부에서 어떻게 처리하는지 정리해보았다.
---

# 🌊 JPA : Dirty-Cheking & Commit & Flush In JPA

What is Dirty Checking?

JPA는 `@Transactional` 안에서 `영속 상태(Persistent State)`의 엔티티에 변경이 생겼는지 감지해서, \
SQL로 바꿔주는 로직을 자동으로 수행한다.\
이걸 **Dirty Checking**이라고 한다.

그런데 JPA는 단순히 객체의 필드 값을 계속 감시하지 않는다.\
→ **스냅샷(Snapshot)** 을 통해 감지한다.

## What is Flush?

Flush란 JPA의 **영속성 컨텍스트(Persistence Context)**&#xC5D0; 저장된 변경 내용을 **데이터베이스에 반영하는 작업**이다.\
단, flush는 **트랜잭션을 커밋하지 않는다.**\
즉, SQL문은 전송되지만 **실제 데이터베이스에는 아직 확정(commit)되지 않는다.**

Flush는 다음 시점에 자동으로 호출된다:

* JPQL 쿼리를 실행할 때
* 트랜잭션 커밋 직전
* `em.flush()` 수동 호출 시

> Flush는 단지 변경 내용을 **DB로 전달**하는 것이며, 트랜잭션을 종료하거나 확정하는 것이 아니다.

***

## What is Commit?

Commit은 **트랜잭션을 종료하고, DB에 영구적으로 반영**하는 단계이다.\
Flush가 완료된 후, 커밋이 수행되면 해당 변경은 **롤백할 수 없는 상태**가 된다.\
이 커밋은 **JPA가 아닌, 데이터베이스 커넥션 수준에서 수행**된다.

즉, JPA는 `TransactionManager`를 통해 트랜잭션을 관리하고, 그 하부에서 JDBC를 통해 커밋을 실행한다.

***

## Internal Flow&#x20;

<figure><img src="../../../.gitbook/assets/스크린샷 2025-06-15 01.24.47.png" alt=""><figcaption></figcaption></figure>

***

## Practice

```java
@Transactional
public void registerUser() {
    User user = new User("현수", "hyunsu@example.com");
    entityManager.persist(user);   // 1. 영속성 컨텍스트에 저장
    entityManager.flush();         // 2. SQL 실행 (insert) → 아직 commit 아님
    // ... 중간에 오류 발생 시 rollback 가능
    // 트랜잭션 종료 시점에 commit 자동 수행됨
}
```

***

## Summary

<table><thead><tr><th width="150.609375">항목</th><th width="259.72265625">flush</th><th>commit</th></tr></thead><tbody><tr><td>대상</td><td>JPA(EntityManager)</td><td>DB(Connection)</td></tr><tr><td>시점</td><td>JPQL 실행 전, 명시적 호출 시</td><td>트랜잭션 종료 시점</td></tr><tr><td>역할</td><td>SQL을 DB에 전송</td><td>DB 트랜잭션 확정</td></tr><tr><td>롤백 가능 여부</td><td>가능</td><td>불가능 (commit 이후는 rollback 불가)</td></tr></tbody></table>

***

## Conclusion

JPA에서 flush와 commit은 분명히 구분되는 작업이다.\
**flush는 변경사항을 DB로 보내는 작업이고, commit은 그것을 확정짓는 작업**이다.\
이 둘의 흐름을 명확히 이해하면 트랜잭션 문제나 지연 쓰기(lazy writing)에 의한 오류를 줄일 수 있다.
