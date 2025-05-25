---
description: >-
  대표적인 자바 데이터 엑세스 기술 세 가지를 모두 써본 입장으로써 느낀 세 개의 특징 및 개인적으로 가장 추천하는 기술에 대해 포스팅
  해보았다.
---

# Java : Data Access Tools

## Brief Comparison

### **jOOQ**

* SQL을 코드로 표현하는 DSL (Domain Specific Language)
* SQL 자체를 중심에 두고 타입 세이프하게 작성 가능
* 복잡한 쿼리도 SQL 그대로 표현할 수 있음
* **활발한 유지보수 진행중**

### **QueryDSL**

* JPQL 또는 SQL을 Java 코드로 표현하는 도구
* JPA와 함께 사용하는 경우가 많지만, SQL 기반도 존재
* Entity 중심의 Query 작성에 적합
* **유지보수가 거의 이뤄지고 있지 않음**

### **JDBC Client (Spring)**

* JDBC API 기반의 가장 로우레벨 접근 방식
* SQL 문자열을 직접 작성
* 가장 유연하지만 타입 안정성과 생산성은 낮음

***

## Developer Experience

<table><thead><tr><th width="135.640625">항목</th><th width="139.8125">jOOQ</th><th width="183.875">QueryDSL</th><th>JDBC Client</th></tr></thead><tbody><tr><td>타입 안전성</td><td>매우 높음</td><td>높음</td><td>낮음</td></tr><tr><td>러닝 커브</td><td>중간</td><td>낮음 (JPA 경험 있다면)</td><td>낮음</td></tr><tr><td>IDE 지원</td><td>매우 좋음</td><td>좋음</td><td>나쁨</td></tr><tr><td>복잡한 쿼리 표현</td><td>매우 유리</td><td>상대적으로 불리</td><td>자유롭게 작성 가능하나 관리 어려움</td></tr></tbody></table>

***

## In Practice

### **jOOQ**

```java
var result = ctx.select(USER.FIRST_NAME, USER.LAST_NAME)
                .from(USER)
                .where(USER.ID.eq(1L))
                .fetchOne();
```

### **QueryDSL**

```java
var user = QUser.user;
var result = queryFactory.selectFrom(user)
                          .where(user.id.eq(1L))
                          .fetchOne();
```

### **JDBC Client**

```java
var sql = """
                SELECT n.*
                FROM notice n
                LEFT JOIN notice_entity_target_country ntc ON n.seq = ntc.notice_entity_seq
                LEFT JOIN user u ON u.country = ntc.target_country 
                LEFT JOIN notice_entity_read_users nru ON n.seq = nru.notice_entity_seq
                WHERE (u.country = ntc.target_country
                   OR ntc.target_country = 'ALL')
                   AND n.status = 'NORMAL'
                   AND (nru.read_users IS NULL OR nru.read_users != ?)
                ORDER BY n.created_at DESC
                LIMIT ? OFFSET ?
                """;

        return jdbcClient.sql(sql)
                .param(userSeq)
                .param(pageSize)
                .param(offset)
                .query((rs, rowNum) -> NoticeResult.from(rs))
                .list();
```

***

## Pros & Cons

### **jOOQ**

* _장점: 타입 안정성, 복잡한 SQL 처리, 가독성_
* _단점: 러닝커브, 설정 비용, 상용 라이선스 (오픈소스 한계)_

### _**QueryDSL**_

* _장점: JPA와 통합성, 코드 자동 생성, 유지보수성_
* _단점: 복잡한 조인, 서브쿼리에서 한계 있음_

### **JDBC Client**

* 장점: 완전한 유연성, 빠름, 프레임워크 독립성
* 단점: SQL 문자열 처리, 타입 안정성 낮음, 반복 코드 많음

***

## When to Use?

* **jOOQ**: SQL 중심, 복잡한 쿼리가 많은 시스템 (예: 데이터웨어하우스, BI 시스템)
* **QueryDSL**: JPA 기반 CRUD 및 단순 조회 위주의 서비스 계층 (예: 일반 웹서비스)
* **JDBC Client**: 경량 서비스, 빠른 프로토타이핑, 단순한 쿼리 위주 프로젝트

***

## Conclusion

jOOQ, QueryDSL, JDBC Client는 각각의 철학과 목적이 명확한 도구이다.  \
ORM이 아닌 복잡한 SQL이 중요한 시스템에서는 jOOQ가 강력하며, \
단순성과 유지보수를 우선하는 경우 QueryDSL, \
완전한 유연성을 추구하거나 단순한 쿼리만 다룰 경우 JDBC Client가 적합하다.\
도구의 선택은 프로젝트의 성격과 팀의 기술 역량에 따라 결정해야하지만, \
단순히 개인적 경험에 의해 기술을 선택하고자 한다면, \
JOOQ를 선호한다. 쿼리자유도가 높은편이며 DB에 직접 엑세스 하여 파일을 생성하므로 JPA Entity와\
함께 사용하는 경우 추가적인 JPA코드 수정없이 사용 가능하다. \
또한 가장 활발히 유지보수 되고 있어 높은 자바버전에서도 무리 없이 사용이 가능하다.\
개인적인 아쉬움은 폐쇄망 사용 시 생성된 모든 파일을 깃에 올려야 한다는 점? \
다만 이 점은 쿼리디에스엘도 크게 다르지 않으므로 논외로 했다.
