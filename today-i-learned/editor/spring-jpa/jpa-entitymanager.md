---
description: >-
  엔티티 매니저(Entity Manager)는 JPA(Java Persistence API)에서 영속성 컨텍스트(Persistence
  Context)와 상호작용하여 엔티티의 상태를 관리하는 핵심 객체이다. 이를 이해하기 위해 먼저 영속성 컨텍스트에 대해 알아봤다.
---

# 👨‍💼 JPA : EntityManager

## Persistence Context

**영속성 컨텍스트는 엔티티를 영구 저장하는 환경**이다. \
이를 통해 JPA는 **1차 캐싱, 쓰기 지연, 변경 감지** 기능을 제공하여 데이터베이스와의 상호작용을 최적화한다.

엔티티 매니저는 이러한 영속성 컨텍스트와 상호작용하며 엔티티의 상태를 관리하는 역할을 수행한다.

***

## Responsibilities of Entity Manager

엔티티 매니저는 엔티티의 상태를 변경하고, 영속성 컨텍스트와 상호작용하며 다음과 같은 주요 기능을 수행한다.

### **엔티티 상태 관리**

* `persist(entity)`: 엔티티를 영속 상태로 변경
* `merge(entity)`: 준영속 상태의 엔티티를 다시 영속 상태로 변경
* `remove(entity)`: 엔티티를 삭제 상태로 변경
* `detach(entity)`: 특정 엔티티를 영속성 컨텍스트에서 분리
* `clear()`: 영속성 컨텍스트 초기화
* `close()`: 영속성 컨텍스트 종료

### **쿼리 실행 및 데이터 조회**

* `find(entityClass, primaryKey)`: 1차 캐시 또는 DB에서 엔티티 조회
* `createQuery(jpql)`: JPQL을 사용한 쿼리 실행
* `createNativeQuery(sql)`: 네이티브 SQL 실행

### **데이터베이스 동기화**

* `flush()`: 쓰기 지연 저장소의 SQL을 즉시 실행하여 DB에 반영

***

## Entity States

JPA의 엔티티는 **비영속, 영속, 준영속, 삭제**의 네 가지 상태를 가질 수 있다.

### **Transient (비영속)**

영속성 컨텍스트와 연관되지 않은 상태. 메모리에만 존재하며 데이터베이스와 관계없음.

```java
Member member = new Member("산초"); // 비영속 상태
```

### **Persistent (영속)**

엔티티가 영속성 컨텍스트에 의해 관리되는 상태. 변경 감지가 가능하며, 자동으로 데이터베이스에 반영됨.

```java
em.persist(member); // 영속 상태
```

### **Detached (준영속)**

한 번 영속되었던 엔티티가 영속성 컨텍스트에서 분리된 상태. 변경 사항이 데이터베이스에 반영되지 않음.

```java
em.detach(member);
em.clear();
em.close();
```

### **Removed (삭제)**

엔티티가 영속성 컨텍스트에서 제거된 상태. 데이터베이스에서도 삭제됨.

```java
em.remove(member);
```

***

## Practical Usage of Entity Manager

### Basic CRUD

```java
@PersistenceContext
private EntityManager entityManager;

@Transactional
public void saveMember() {
    Member member = new Member("산초");
    entityManager.persist(member); // INSERT 실행 전 영속성 컨텍스트에 저장
}

@Transactional
public Member findMember(Long id) {
    return entityManager.find(Member.class, id); // 1차 캐시 확인 후 DB 조회
}

@Transactional
public void updateMember(Long id, String newName) {
    Member member = entityManager.find(Member.class, id);
    member.setName(newName); // 변경 감지 기능으로 자동 반영 (Flush 시점)
}

@Transactional
public void deleteMember(Long id) {
    Member member = entityManager.find(Member.class, id);
    entityManager.remove(member); // 삭제 요청
}
```

### **JPQL**&#x20;

```java
public List<Member> findByName(String name) {
    return entityManager.createQuery("SELECT m FROM Member m WHERE m.name = :name", Member.class)
            .setParameter("name", name)
            .getResultList();
}
```

### **Native Query**

```java
public List<Object[]> findNativeQuery() {
    return entityManager.createNativeQuery("SELECT * FROM members WHERE age > ?")
            .setParameter(1, 20)
            .getResultList();
}
```

***

## Importance of Entity Manager

엔티티 매니저는 JPA의 핵심 객체로, 엔티티의 **생명주기를 관리하고, 데이터베이스와의 상호작용을 최적화**한다.

* **1차 캐시**를 통해 불필요한 데이터베이스 조회를 방지
* 변경 감지(Dirty Checking)로 자동 업데이트 수행
* 쓰기 지연(Flush)을 활용해 성능 최적화

이를 활용하면 **효율적인 영속성 관리**가 가능하며, 애플리케이션의 성능과 유지보수성을 향상시킬 수 있다.
