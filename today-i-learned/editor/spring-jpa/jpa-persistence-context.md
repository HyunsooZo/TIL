---
description: >-
  영속성 컨텍스트는 JPA의 핵심 개념 중 하나이다. Spring Data JPA를 사용할 때 자동으로 동작하지만, 내부적으로 어떤 일이
  일어나는지 이해하면 JPA를 더욱 깊이 있게 사용할 수 있다.
---

# ⛳ JPA : Persistence Context

## Persistence Context?

영속성 컨텍스트(Persistence Context)란 엔티티(Entity) 객체를 \
**저장, 조회, 수정, 삭제** 등의 상태로 관리하는 일종의 **메모리 상의 캐시**이다.\
정확히는, **`EntityManager`가 관리하는 엔티티들의 집합**이다.

Spring에서는 `@Transactional` 어노테이션이 붙은 메서드 안에서 \
이 Persistence Context가 생성되고, 해당 트랜잭션이 끝날 때까지 유지된다.

***

## Why Persistence Context?

JPA는 단순히 SQL을 자동으로 생성해서 실행하는 기술이 아니다.\
DB와 메모리 사이의 객체 상태를 관리하는 ORM(Object Relational Mapping) 기술이다.\
그런데 객체는 메모리에 있고, DB는 디스크에 있다.\
이 둘의 **상태를 동기화**하려면 **중간에 상태를 관리해주는 뭔가**가 필요하다.\
바로 그 역할을 하는 것이 **영속성 컨텍스트**이다.

***

### Lifecycle of Entities

JPA에서 엔티티는 아래와 같은 상태를 가진다:

<figure><img src="../../../.gitbook/assets/스크린샷 2025-03-22 오후 10.06.04 (1).png" alt=""><figcaption></figcaption></figure>

* **Transient**: 아무 컨텍스트에도 관리되지 않는 상태. `new`로 만든 객체.
* **Persistent**: 영속성 컨텍스트에 의해 관리되는 상태.
* **Detached**: 영속성 컨텍스트에서 분리된 상태.
* **Removed**: 삭제 예정 상태.

***

## Core Features

### First-Level Cache

Persistence Context는 엔티티를 메모리에 **1차 캐시** 형태로 보관한다.\
`em.find()`나 `em.getReference()`로 조회한 엔티티는 Persistence Context에 저장되고, \
**같은 ID로 다시 조회하면 DB를 보지 않고 캐시에서 가져온다.**

```java
Member member1 = em.find(Member.class, 1L); // DB에서 조회
Member member2 = em.find(Member.class, 1L); // 캐시에서 조회
System.out.println(member1 == member2); // true
```

### Dirty Checking

Persistence Context는 트랜잭션이 끝나기 전에 **모든 엔티티의 상태를 스냅샷**으로 저장한다.\
트랜잭션이 커밋될 때 **스냅샷과 현재 상태를 비교하여 변경된 내용을 자동으로 UPDATE** 한다.

```java
member.setName("현수"); // setter로 변경
// 별도의 save 호출 없이 트랜잭션 커밋 시 update 자동 실행
```

```java
@Transactional
public void updateMember(Long id, String newName) {
    Member member = memberRepository.findById(id).orElseThrow();
    member.setName(newName); // 여기까지만 작성해도
    // 트랜잭션 종료 시점에 자동으로 update 쿼리 나감
}
```

이 방식이 가능한 이유는, `@Transactional`에 의해 Persistence Context가 살아있고,\
이 context가 `member`의 변경을 감지하여 자동으로 SQL을 만들어 실행하기 때문이다.

### Write-Behind

`persist()`를 호출한다고 바로 insert SQL이 나가지 않는다.\
영속성 컨텍스트는 INSERT/UPDATE/DELETE를 모아두고, **트랜잭션 커밋 시 한 번에 flush**한다.

```java
em.persist(member); // insert SQL 안 나감
em.flush(); // 이 시점에서 insert SQL 실행
```

### Identity Guarantee

같은 트랜잭션 내에서 같은 ID를 가진 엔티티는 항상 같은 객체이다.\
이것은 JPA가 객체의 **동일성(identity)** 을 보장해주기 때문에, 편리하게 로직을 작성할 수 있다.

***

## Flush and Clear

* `em.flush()` : 변경 내용을 DB에 반영하지만, 컨텍스트는 그대로 유지.
* `em.clear()` : 모든 엔티티를 준영속(detach) 상태로 만듦.
* `em.flush(); em.clear();` : DB 반영 + 컨텍스트 초기화.

이는 대용량 배치 처리에서 메모리 절약용으로 자주 사용된다.

***

## Transaction and Persistence Context

Spring에서 `@Transactional` 어노테이션을 사용하면,\
트랜잭션이 시작될 때 Persistence Context가 생성되고,\
트랜잭션이 종료될 때 flush → commit 순으로 DB에 반영된다.\
트랜잭션이 끝나면 Persistence Context는 사라지고, 모든 엔티티는 detach 된다.

***

## Summary

| Concept                   | Description                |
| ------------------------- | -------------------------- |
| Persistence Context       | 엔티티를 영속 상태로 관리하는 메모리 상의 공간 |
| First-Level Cache         | 같은 ID 조회 시 DB 재접근 방지       |
| Dirty Checking            | setter 호출만으로 update 가능     |
| Write-Behind              | SQL을 모아뒀다 flush 시 실행       |
| Identity Guarantee        | 같은 트랜잭션 내 동일 엔티티는 동일 객체    |
| flush                     | DB 반영 (커밋 아님)              |
| clear                     | 영속성 컨텍스트 초기화 (detach)      |
| Relation with Transaction | 트랜잭션 단위로 생성되고 사라짐          |
