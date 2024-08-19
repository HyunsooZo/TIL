---
description: >-
  JPA와 같은 ORM(Object-Relational Mapping) 도구를 사용할 때 발생할 수 있는 성능 문제로, 하나의 쿼리를 통해
  특정 엔티티를 조회한 후, 그 엔티티와 연관된 다른 엔티티들을 조회 시 추가로 쿼리를 발생시키는 이슈를 말한다.
---

# ☝️ N + 1 Issue

### **원인**

* ~~**Fetch Type**~~ \
  ~~ORM 에서는 기본적으로 지연 로딩을 Default로 가진다.~~ \
  ~~이는 실제로 필요한 시점에서 데이터를 로딩하는 방식인데, 이 과정에서 추가 쿼리가 발생  할 수 있다.~~  \
  하지만 `FetchType`은 LAZY, EAGER 를  통해 추가쿼리가 메인 엔티티를 호출할때 호출될지, \
  아니면 해당 데이터에 접근 할때 호출 할 지의 정도의 컨트롤만 가능하다
* **JPA 의  특성**\
  JPA는 JPQL을 사용해 엔티티 객체와 필드 이름을 기반으로 쿼리를 생성하며, \
  특정 SQL에 종속되지 않는다. `findAll()`은 `SELECT e FROM Entity e`와 같은 \
  쿼리를 실행하지만, 연관관계는 Fetch 전략에 따라 처리된다.\
  즉 JPA는 설정된 로딩 전략에 따라 연관 엔티티에 대한 쿼리를 추가로 실행할 수 있다.
* **비효율적 설계 / 데이터 접근**\
  때로는 ORM 설계 / 데이터 접근 방식이 비효율적이어서\
  DB와의 상호작용 이슈로 N+1 문제가 발생하기도 한다.

### **해결 방법**

* <mark style="background-color:green;">**Fetch Join 사용**</mark>\
  \
  JPQL 또는  QueryBuilder(QueryDSL , JOOQ) 등을 통해 `fetchJoin()`을 사용할 수 있다.\
  `fetchJoin()`은 일반적으로 연관된 엔티티를 한 번의 쿼리로 함께 가져오기 위해 사용된다. \
  단 이 경우에도 주의해야 할 점들이 존재한다

1. **`fetchJoin()`**사용 시 페이징이 어렵다.\
   **`fetchJoin()`**을 사용하는 경우 페이징이 제한될 수 있는 문제가 있다. (어떻게 보면   치명적)\
   특히 JPA에서는 **`fetchJoin()`**과 페이징을 함께 사용하면 쿼리 결과에 대해 메모리에서 \
   페이징 처리를 해야 하므로 성능에 문제가 생길 수 있다.
2. Cartesian Product 발생 가능성\
   만약 `fetchJoin()` 사용 시 조인 조건이 적절히 설정되지 않으면 Cartersian Product 가 발생 할 수 있고 이는 곳 모든 조합의 레코드가 생성되어 불필요한 중복 결과가 발생 할 수 있다.&#x20;

```java
    @Override
    public List<Parent> findAllWithChildren() {
        QParent parent = QParent.parent;
        QChild child = QChild.child;

        return queryFactory.selectFrom(parent)
                .leftJoin(parent.children, child).fetchJoin()  // fetchJoin 사용
                .fetch(); 
    }
```

* <mark style="background-color:green;">**BatchSize 설정**</mark>\
  \
  **아래와 같이 BatchSize를 필드에 설정해주면** `Parent` 엔티티를 조회하면서 `Child` 엔티티를 접근하게 되면, **`@BatchSize(size = 10)`** 설정에 따라 한 번에 10개의 `Child` 엔티티를 로딩하게 되고,\
  이로 인해 개별적으로 쿼리가 실행되는 것을 방지할 수 있다.\
  \
  다만 이 또한 여러개의 쿼리를 반환하는 것은 변함이 없으므로 완전한 해결책이라 보기는 어렵다.

```java
@Entity
public class Parent {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    
    private String name;

    @OneToMany(mappedBy = "parent", fetch = FetchType.LAZY)
    @BatchSize(size = 10)  // 한 번에 10개의 Child 엔티티를 로딩
    private List<Child> children = new ArrayList<>();
}
```

* <mark style="background-color:green;">**JPQL 을 통한 직접 쿼리작성**</mark>\
  \
  가장 간단할 수 있는 방법이나 이는 곧 JPA의 이점을 최대한 활용하지 못한다는 단점이 존재한다.

```java
@PersistenceContext
private EntityManager entityManager;

public List<Parent> findAllWithChildren() {
    String jpql = "SELECT p FROM Parent p JOIN FETCH p.children";
    TypedQuery<Parent> query = entityManager.createQuery(jpql, Parent.class);
    return query.getResultList();
}
```



* <mark style="background-color:green;">**물리적 연관관계 설정 최대한 피하기   (개인적으로는 그나마 해결방안에 적합하다고 생각한다)**</mark>\
  \
  개인적으로는 JPA의 이점을 가져갈 수 있음과 동시에 \
  QueryBuilder를 사용한 일반 join으로 N+1 을 방지 할 수 있는 가장 적합한 방법이 아닐까 생각된다. \
  \
  데이터 무결성 문제나, 장기적인 유지보수에서의 문제점 등이 거론되나 \
  이 경우 적절한 DB 락 전략 및 팀 내 합의가 있다면 해결 가능하다고 생각된다. \
  \
  실제로 몇몇 IT기업에서는 성능 최적화 측면에서의 이점이나, \
  개발 속도 및 테스트 편의성 등을 이유로 복잡한 연관관계를 금하고 \
  더 나아가 연관관계를 아예 설정하지 않는다고 들었다.



### **결론**

부족한 경력과 경험으로 이러한 방안이 제일 적합하다고 생각할 뿐 정답은 찾지 못했다.\
프로젝트의 규모와 팀의 규칙에 따라 가능한 한 성능에 피해가 가지 않는 방향으로 코딩하면 될 것같다. \
지금은 물리적 연관관계를 맺지 않는 것이 제일 나은방법이라고 생각한다고 했지만, \
한달 뒤, 1년 뒤, 5년 뒤의 나는 다르게 생각 할 수 있을 것 같다.\
어쩌면 그땐 더 나은 대안이 나와있을 지도 모르겠다.
