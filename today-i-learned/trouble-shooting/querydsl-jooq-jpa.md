# 🤔 QueryDSL/JOOQ 없이 JPA만으로 동적 검색 구현하기

### 머릿말

개발을하다보면 검색 조건이 매번 달라지는 리스트 조회를 구현할 때가 있습니다. \
파라미터가 비어있을 수도 있고, 일부만 들어올 수도 있고. 더 복잡한 조건이 있을 수도 있습니다. \
이럴때 흔히 `QueryDSL`이나 `JOOQ`를 이용한 동적쿼리를 활용하는 것을 떠올리지만, \
**Spring Data JPA의 `Specification`만으로도** 깔끔하게 **동적 쿼리**를 구성할 수 있습니다.

이번 포스팅은 간편결제 BO 개발 당시 선임 개발자가 알려준 별도 DSL 없이 \
**JPA + Specification**으로 조건을 조합하여 간단한 동적쿼리를 만드는 법에 적어보겠습니다.

### JPA Specification이란?

`Specification<T>`는 JPA Criteria 기반으로 **where 절을 동적으로 조립**할 수 있는 인터페이스입니다.

각 조건을 `Predicate`로 만들고, 상황에 따라 and/or로 묶어 **필요한 조건만 활성화**할 수 있습니다. \
추가 라이브러리 없이 **Spring Data JPA만으로** 활용 가능합니다.

#### 동작 원리

1.  **Specification 정의**

    엔티티별로 `withCriteria(...)` 정적 메서드를 두고 (또는 따로 팩토리를 구현하던), 들어온 검색 DTO를 기준으로 `Predicate` 목록을 생성합니다.
2.  **Repository에서 위임**

    `JpaSpecificationExecutor`를 상속하면 `findAll(Specification, Pageable)`을 바로 사용할 수 있습니다.

### 실제 코드 예시

#### 1) 엔티티

* 저는 그냥 Entity 안에 넣었지만 별도로 외부 Specification 클래스로 빼셔도 되고 지금생각해보니 그게 더 낫다고 생각도듭니다.

```java
@Getter 
@Entity
@Table(name = "payment_transaction")
@NoArgsConstructor 
public class PaymentTransactionJpaEntity {

    @Id
    @Column(name = "transaction_id", length = 26)
    private String transactionId;

    @Column(name = "member_id", length = 10)
    private String hanpassMemberId;

    @Column(name = "partner_id", length = 10, nullable = false)
    private String partnerId;

    @Column(name = "partner_order_id", length = 100, nullable = false)
    private String partnerTransactionId;

    @Column(name = "item_name", length = 50, nullable = false)
    private String itemName;

    @Column(name = "item_code", length = 10)
    private String itemCode;

    @Column(name = "total_amount", length = 12, nullable = false)
    private String totalAmount;

    @Enumerated(EnumType.STRING)
    @Column(name = "status", nullable = false)
    private TransactionStatus status;

    @Column(name = "approve_id", length = 100)
    private String approveId;

    @Column(name = "created_at", nullable = false)
    private LocalDateTime createdAt;

    @Column(name = "approved_at")
    private LocalDateTime approvedAt;

    @Column(name = "cancelled_at")
    private LocalDateTime cancelledAt;

    @Column(name = "redirect_url", length = 100)
    private String redirectUrl;

    @Column(name = "bridge_url", length = 100)
    private String bridgeUrl;

    @Column(name = "transaction_token", length = 100)
    private String transactionToken;

    @Column(name = "failure_details", length = 500)
    private String failureDetails;

    @Enumerated(EnumType.STRING)
    @Column(name = "payment_status", length = 50)
    private PaymentStatus paymentStatus;

    @Override
    public final boolean equals(Object o) {
        //..
    }

    @Override
    public final int hashCode() {
        //..
    }

    // 핵심: 동적 조건 조립
    public static Specification<PaymentTransactionJpaEntity> withCriteria(TransactionCriteria criteria) {
        return (root, query, cb) -> {
            var predicates = new ArrayList<Predicate>();

            if (hasText(criteria.getTransactionId())) {
                predicates.add(cb.equal(root.get("transactionId"), criteria.getTransactionId()));
            }
            if (hasText(criteria.getPartnerTransactionId())) {
                predicates.add(cb.equal(root.get("partnerTransactionId"), criteria.getPartnerTransactionId()));
            }
            if (hasText(criteria.getMemberSeq())) {
                predicates.add(cb.equal(root.get("hanpassMemberId"), criteria.getMemberSeq()));
            }
            if (hasText(criteria.getStatus())) {
                try {
                    var st = TransactionStatus.valueOf(criteria.getStatus());
                    predicates.add(cb.equal(root.get("status"), st));
                } catch (IllegalArgumentException ignored) {}
            } else {
                // 기본 상태 필터 (없으면 승인된 건만)
                predicates.add(cb.equal(root.get("status"), TransactionStatus.PAYMENT_APPROVED));
            }
            if (hasText(criteria.getPaymentStatus())) {
                try {
                    var ps = PaymentStatus.valueOf(criteria.getPaymentStatus());
                    predicates.add(cb.equal(root.get("paymentStatus"), ps));
                } catch (IllegalArgumentException ignored) {}
            }
            if (hasText(criteria.getPartnerId())) {
                predicates.add(cb.equal(root.get("partnerId"), criteria.getPartnerId()));
            }
            if (criteria.getStartDate() != null) {
                predicates.add(cb.greaterThanOrEqualTo(root.get("createdAt"), criteria.getStartDate()));
            }
            if (criteria.getEndDate() != null) {
                predicates.add(cb.lessThanOrEqualTo(root.get("createdAt"), criteria.getEndDate()));
            }

            return cb.and(predicates.toArray(new Predicate[0]));
        };
    }

    private static boolean hasText(String s) {
        return s != null && !s.isBlank();
    }
}
```

#### 2) Repository (Specification + Pageable)

```java
public interface PaymentTransactionJpaRepository
        extends JpaRepository<PaymentTransactionJpaEntity, String>,
                JpaSpecificationExecutor<PaymentTransactionJpaEntity> {

    default Page<PaymentTransactionJpaEntity> findByCriteria(
            TransactionCriteria criteria,
            Pageable pageable
    ) {
        return findAll(PaymentTransactionJpaEntity.withCriteria(criteria), pageable);
    }
}
```

### 주의사항

* **기본 필터 전략** 예 ) 상태값이 없을 때 `PAYMENT_APPROVED`만 보이도록 한 것처럼, **안전한 기본값**을 명확히 두면 운영 실수를 줄입니다.
* **Enum 파싱 예외 무시** 예) 외부 파라미터에서 들어오는 문자열이 enum에 없을 수 있으므로, `IllegalArgumentException` 방어 코드를 꼭 두세요.
* **Index 설계** 예) \*\*\*\*where절에 자주 쓰는 칼럼(`partnerId`, `status`, `createdAt`)에 **복합 인덱스**를 고려하세요. (예: `(partner_id, status, created_at)`)
* **N+1 주의** 예) \*\*\*\*리스트에 연관 엔티티 접근이 필요한 경우 `fetch join`이 유리하지만, **`Specification + fetch join`은 count 쿼리와 충돌**할 수 있습니다.
  * 해결책: 조회용은 fetch join, 카운트용은 fetch 없이 분리하거나, 화면에서 상세는 개별 조회로 분리.
* **대량조건 최적화** 예) \*\*\*\*`IN` 목록이 너무 크면 성능 저하가 큽니다. 적절한 최대 개수를 제한하거나 임시 테이블 전략 고려.
* **재사용성** 예) \*\*\*\*조건별 `Specification` 조각을 메서드로 쪼개 `where(spec1.and(spec2).or(spec3))` 형태로 합성 가능하지만, 복잡성이 커지면 명시적 빌더 스타일(지금 예시처럼)이 더 읽기 편합니다.

### 결론

* **라이브러리 추가 없이** Spring Data JPA만으로 동적 쿼리 구현 가능
* 조건이 늘어나도 **Predicate 리스트 조립**으로 깔끔하게 관리
* **기본값/예외/페이징/정렬**까지 한 번에 처리
* 복잡도, 성능 이슈만 주의하면 **`QueryDSL`/ `JOOQ` 없이도 충분히 커버 가능**
* 단, **더** **복잡한 조인 조건**이나 **서브쿼리 기반 통계형 조회**, **select 절 가공이 필요한 경우**엔 **`QueryDSL`** / \*\*`JOOQ`\*\*가 더 적합할 수 있습니다.
