---
description: >-
  데이터베이스 시스템에서 동시성 제어와 데이터 무결성을 보장하기 위해 락(Lock)을 사용하는 것은 매우 중요하다. DB 락은 다중 사용자
  환경에서 데이터 일관성을 유지하고 충돌을 방지하기 위해 사용된다.
---

# 🔓 DB : Lock

## DB Lock의 필요성

데이터베이스는 다수의 클라이언트가 동시에 데이터를 읽거나 수정할 수 있는 다중 사용자 환경에서 동작한다. \
이로 인해 다음과 같은 문제가 발생할 수 있다

### **Dirty Read**

하나의 트랜잭션이 커밋되지 않은 데이터를 읽어 데이터 불일치 발생

### **Non-repeatable Read**

동일한 트랜잭션 내에서 같은 쿼리가 다른 결과를 반환할 수 있다.

### **Phantom Read**

다른 트랜잭션에 의해 삽입된 새로운 행이 결과에 나타날 수 있다.\


## Lock 사용 시 고려사항

### **성능 문제**

과도한 락 사용은 데드락(교착 상태)을 유발하거나 시스템 성능을 저하시킬 수 있다.

### **적절한 트랜잭션 범위 설정**

트랜잭션 범위를 명확히 설정하여 불필요한 락 유지 시간을 줄여야 한다.

### **락 타임아웃 설정**

무기한 대기를 방지하기 위해 타임아웃 설정이 필요할 수 있다.



## Spring (Java) 에서 Lock 걸기

### **JDBC (Template/Client)**

JDBC를 사용하면 SQL 쿼리를 통해 명시적으로 락을 설정할 수 있다. \
예를 들어, 다음과 같은 SQL 명령어를 사용해 테이블이나 특정 레코드에 락을 걸 수 있다

```java
Connection connection = DriverManager.getConnection(DB_URL, USER, PASSWORD);
connection.setAutoCommit(false);

String sql = "SELECT * FROM employees WHERE id = ? FOR UPDATE";
PreparedStatement preparedStatement = connection.prepareStatement(sql);
preparedStatement.setInt(1, 123);
ResultSet resultSet = preparedStatement.executeQuery();

// 데이터 수정 작업
if (resultSet.next()) {
    String currentName = resultSet.getString("조사원");
    String updatedName = currentName + "조대리";

    String updateSql = "UPDATE employees SET name = ? WHERE id = ?";
    PreparedStatement updateStatement = connection.prepareStatement(updateSql);
    updateStatement.setString(1, updatedName);
    updateStatement.setInt(2, 123);
    updateStatement.executeUpdate();
}

// 커밋 / 클로즈
connection.commit();
connection.close();
```

* `FOR UPDATE`: SELECT 쿼리에 추가하여 해당 행에 배타적 락을 설정한다.
* `Connection.setAutoCommit(false)`: 트랜잭션을 수동으로 관리하여 커밋 전까지 락이 유지된다.

### **JPA**&#x20;

JPA는 데이터베이스 락을 더 추상화된 방법으로 제공한다. \
`@Lock` 어노테이션이나 `EntityManager`의 `lock` 메서드를 통해 락을 설정할 수 있다.

#### **Optimistic Locking** (2)

낙관적 락은 충돌이 드물다고 가정하고 트랜잭션 완료 시 충돌을 탐지한다. \
주로 `@Version` 필드를 사용하여 충돌을 감지하고 트랜잭션 충돌 시 예외를 발생시킨다.

```java
@Entity
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Version
    private Integer version;

    private String name;
}
------------------------------------Or---------------------------------------
public interface EmployeeRepository extends JpaRepository<Employee, Long> {
    @Lock(LockModeType.PESSIMISTIC_WRITE)
    @Query("SELECT e FROM Employee e WHERE e.id = :id")
    Employee findEmployeeForUpdate(@Param("id") Long id);
}
```

1. `@Version` 필드는 엔티티가 수정될 때마다 증가하여 충돌을 감지한다. \
   낙관적 락은 성능이 뛰어나지만, **충돌이 발생했을 때 재시도 로직이 필요할 수** 있다.
2. `@Lock(LockModeType.PESSIMISTIC_WRITE)`를 리포지토리 메서드에 적용하면 \
   해당 메서드 실행 시 Pessimistic Lock이 걸린다.

#### **Pessimistic Locking** (2)

비관적 락은 충돌이 자주 발생할 것으로 예상될 때 사용된다. \
트랜잭션이 진행되는 동안 다른 트랜잭션이 해당 데이터를 수정하지 못하도록 즉시 락을 건다.

```java
EntityManager em = entityManagerFactory.createEntityManager();
em.getTransaction().begin();

Employee employee = em.find(Employee.class, 123L, LockModeType.PESSIMISTIC_WRITE);

// 데이터 수정 및 커밋
employee.setName(employee.getName() + "조과장");
em.getTransaction().commit();
em.close();
-----------------------------------------Or-----------------------------------------
public interface EmployeeRepository extends JpaRepository<Employee, Long> {

    @Lock(LockModeType.PESSIMISTIC_WRITE)
    @Query("SELECT e FROM Employee e WHERE e.id = :id")
    Employee findEmployeeForUpdate(@Param("id") Long id);
}
```

1. `LockModeType.PESSIMISTIC_WRITE`는 배타적 쓰기 락을 걸어 \
   다른 트랜잭션이 읽거나 쓰기를 못하게 한다. \
   비관적 락은 데이터 경합이 심한 환경에서 안전하지만, 성능 저하의 위험이 있다.
2. `@Lock(LockModeType.PESSIMISTIC_WRITE)`를 리포지토리 메서드에 적용하면 \
   해당 메서드 실행 시 Pessimistic Lock이 걸린다.

