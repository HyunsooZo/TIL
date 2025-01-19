---
description: >-
  데이터베이스에서 대량의 데이터를 효율적으로 관리하기 위해 인덱스는 필수적인 도구이다. 오늘은 인덱스의 개념과 장단점, 그리고 스프링 부트
  환경에서 JPA와 JdbcClient를 활용해 인덱스를 설정하는 방법에 대해 알아보았다
---

# 🔍 DB : Index

## What's Index ?

\
**인덱스**는 데이터베이스 테이블에서 특정 컬럼에 대한 검색 성능을 최적화하기 위해 사용하는 자료구조이다.\
인덱스는 데이터를 특정 컬럼을 기준으로 **정렬된 구조**로 유지하며, 이를 통해 데이터 검색 속도를 크게 높일 수 있다.

### Full Table Scan과 Index Scan의 차이

* **Full Table Scan**: 인덱스가 없을 경우, 테이블의 모든 데이터를 **처음부터 끝까지** 읽어야 한다.
* **Index Scan**: 인덱스를 사용하면 **필요한 레코드의 위치를 빠르게** 찾아 데이터 검색 성능이 대폭 향상된다.

쉽게 말해, **인덱스는 책의 목차처럼 원하는 내용을 빠르게 찾을 수 있는 방법**을 제공한다.

## The Way Index Works

<figure><img src="../../.gitbook/assets/스크린샷 2024-11-04 오후 8.21.27.png" alt=""><figcaption></figcaption></figure>

1. 클라이언트가 특정 컬럼 값을 검색 요청 &#x20;
2. 인덱스는 해당 컬럼의 정렬된 구조를 활용해 빠르게 데이터 위치를 찾아감&#x20;
3. 그 위치를 기반으로 데이터베이스 테이블에서 필요한 데이터를 조회
4. 반환

만약 위 과정에서 인덱스를 사용하지 않는다면 데이터베이스는 테이블의 모든 데이터를 읽어야 하기 때문에 \
성능이 크게 저하될 수 있다.

### &#x20;Example&#x20;

데이터베이스에 나이(age) 컬럼의 값이 20인 레코드를 찾는다고 가정하자.

인덱스 없음: 모든 데이터를 처음부터 끝까지 확인해야 하므로 성능이 저하된다. \
인덱스 있음: \
B+트리 구조(다른 자료구조일 수도 있다) 를 기반으로 데이터를 탐색해, 적은 탐색만으로 데이터를 찾을 수 있다.

<figure><img src="../../.gitbook/assets/스크린샷 2025-01-19 오후 3.58.48.png" alt=""><figcaption></figcaption></figure>

## Pros & Cons Of Index

### **Pros**

1. **검색 속도 향상**: \
   인덱스는 특정 컬럼을 기준으로 정렬되어 있으므로, 조회 성능을 크게 향상시킨다. \
   특히 대규모 테이블에서는 검색 성능의 차이가 더욱 크다.
2. **빠른 정렬 및 필터링**: \
   정렬된 데이터 구조를 통해 원하는 데이터를 빠르게 필터링하고, \
   특정 조건에 맞는 데이터를 효과적으로 조회할 수 있다.
3. **중복 방지**: \
   유니크 인덱스(Unique Index)를 설정하면 해당 컬럼에 중복된 값을 허용하지 않도록 관리할 수 있다. \
   이는 데이터 무결성 유지에도 중요한 역할을 한다.

### Cons

1. **쓰기 성능 저하**: \
   인덱스는 데이터가 추가되거나 수정될 때마다 갱신되어야 하므로, \
   삽입(INSERT), 수정(UPDATE), 삭제(DELETE) 작업의 성능이 저하될 수 있다.
2. **저장 공간 증가**: \
   인덱스를 생성하면 데이터베이스가 별도의 자료구조를 관리해야 하므로, 저장 공간을 추가로 소모하게 된다.
3. **과도한 인덱스 사용 시 성능 저하**: \
   모든 컬럼에 인덱스를 생성하면 성능이 오히려 저하될 수 있다. \
   데이터 삽입/수정/삭제 작업이 많을수록 인덱스 유지에 부담이 가중되기 때문이다. \
   따라서 필요한 컬럼에만 선택적으로 인덱스를 생성하는 것이 좋다.

## Cautionary notes

1. **주요 검색 컬럼에만 인덱스를 설정**: \
   인덱스는 검색에 사용되는 컬럼에만 설정하는 것이 좋다. \
   자주 필터링하거나 정렬에 사용되는 컬럼에 인덱스를 설정함으로써 검색 속도를 극대화할 수 있다.
2. **작은 테이블에는 인덱스가 불필요할 수 있음**: 테\
   이블 크기가 작다면 인덱스를 생성해도 성능 향상이 미미할 수 있다. \
   테이블을 전체 스캔하는 비용이 인덱스를 사용하는 비용보다 낮다면, \
   오히려 인덱스를 유지하는 비용이 더 클 수 있다.
3. **복합 인덱스의 순서에 주의**: \
   여러 컬럼에 인덱스를 설정할 경우, 컬럼의 순서가 검색 성능에 영향을 미칠 수 있다. \
   예를 들어 `(firstName, lastName)` 순서로 복합 인덱스를 생성했다면, \
   `firstName`을 기준으로 먼저 검색할 때 성능이 좋아진다. \
   단, `lastName`만으로 검색할 때는 인덱스가 잘 활용되지 않을 수 있다.
4. **데이터의 분포에 따라 인덱스 성능 차이**: \
   인덱스는 값의 분포가 균일하지 않은 컬럼에는 효과가 떨어질 수 있다. \
   예를 들어, `성별`과 같이 값의 종류가 적은 컬럼에 인덱스를 생성하면 실제 성능 향상이 미미할 수 있다.

## Indexes in Spring Boot

스프링 부트에서 인덱스를 적용하는 방법은 여러가지가 있다. \
오늘 알아볼 방법은 JPA를 사용하는 방법과 `JdbcClient`를 이용해 직접 SQL로 생성하는 방법이다.

### **Indexing in JPA**

JPA에서는 `@Table` 어노테이션의 `indexes` 속성을 사용하여 테이블 수준에서 인덱스를 설정할 수 있다. \
또 `@Column`의 `unique` 속성을 통해 유니크 인덱스를 적용할 수 있다.

```java
import jakarta.persistence.*;

@Entity
@Getter
@Table(name = "person", indexes = {
    @Index(name = "idx_first_name", columnList = "firstName"),
    @Index(name = "idx_last_name_created_at", columnList = "lastName, createdAt")
})
public class Person {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "first_name")
    private String firstName;

    @Column(name = "last_name")
    private String lastName;

    @Column(name = "created_at")
    private LocalDateTime createdAt;
}
```

### **Indexing In JdbcClient**

`JdbcClient`를 사용하여 인덱스를 설정할 경우,\
&#x20;데이터베이스에 직접 SQL을 실행해 인덱스를 생성해야 한다. \
주로 DDL을 사용해 인덱스를 생성하게 된다.

```java
import org.springframework.jdbc.core.JdbcClient;
import org.springframework.stereotype.Repository;

@Repository
public class PersonRepository {
    private final JdbcClient jdbcClient;

    public PersonRepository(JdbcClient jdbcClient) {
        this.jdbcClient = jdbcClient;
    }

    public void createIndex() {
        String sql = "CREATE INDEX idx_first_name ON person(first_name)";
        jdbcClient.sql(sql).execute();
    }
}

```

## Configuring Index in MySQL

### Create Basic Index

특정 컬럼에 대해 기본 인덱스를 생성하려면 아래 명령어를 사용한다.

```sql
CREATE INDEX index_name ON table_name(column_name);
```

**예시**\
`users` 테이블의 `age` 컬럼에 인덱스를 생성한다.

```sql
CREATE INDEX idx_age ON users(age);
```

***

### Create Unique Index

유니크 인덱스는 특정 컬럼의 값이 중복되지 않도록 설정할 때 사용한다.

```sql
CREATE UNIQUE INDEX index_name ON table_name(column_name);
```

**예시**\
`email` 컬럼에 유니크 인덱스를 생성한다.

```sql
CREATE UNIQUE INDEX idx_email ON users(email);
```

***

### Create Composite Index

복합 인덱스는 두 개 이상의 컬럼에 대해 생성하여 특정 쿼리의 성능을 향상시킨다.

```sql
CREATE INDEX index_name ON table_name(column1, column2);
```

**예시**\
`first_name`과 `last_name` 컬럼에 복합 인덱스를 생성한다.

```sql
CREATE INDEX idx_full_name ON users(first_name, last_name);
```

***

### Set Index While Creating Table

테이블 생성 시 `CREATE TABLE` 명령어를 사용하여 인덱스를 설정할 수 있다.

```sql
CREATE TABLE table_name (
    column1 data_type,
    column2 data_type,
    ...
    INDEX index_name (column_name)
);
```

**예시**\
테이블 생성 시 `age` 컬럼에 인덱스를 추가한다.

```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255),
    age INT,
    INDEX idx_age (age)
);
```

***

### Delete Index

인덱스를 삭제하려면 `DROP INDEX` 명령어를 사용한다.

```sql
DROP INDEX index_name ON table_name;
```

**예시**\
`users` 테이블에서 `idx_age` 인덱스를 삭제한다.

```sql
DROP INDEX idx_age ON users;
```

***

### Clustered Index

MySQL에서는 기본 키(Primary Key)가 자동으로 클러스터드 인덱스를 생성하므로 별도로 설정할 필요는 없다.\
아래 예시에서 기본 키 설정은 클러스터드 인덱스를 자동으로 생성한다.

```sql
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255),
    age INT
);
```
