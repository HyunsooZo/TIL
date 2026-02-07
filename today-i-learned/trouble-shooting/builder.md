# 🤔 @Builder를 커스텀 생성자 위에 붙여야만 하는이유

Lombok은 사실상 Java/Spring 개발자에게는 필수 라이브러리입니다. 반복적인 보일러플레이트 코드를 제거하고, 코드 가독성을 크게 향상시켜주기 때문입니다.

특히 `@Builder`는 필드가 많은 객체를 만들 때 가독성과 사용성을 크게 높여줍니다.

하지만 **클래스 레벨에 무심코 붙여 모든 필드를 노출하면 도메인 규칙이 깨지고**, **생성 로직의 제어권이 외부로** 넘어갈 수 있습니다.

실무에서는 **도메인 규칙과 유효성 검사를 담은 커스텀 생성자에 `@Builder`를 붙여, 안전하고 명시적인 객체 생성을 보장**해야 합니다.

이번 포스팅에서는 왜 생성자에 붙여야 하는지, 그리고 검증 로직을 어디에 어떻게 담아야 하는지 코드 예시와 함께 적어보려합니다.

### 클래스 레벨 `@Builder`의 문제점

클래스에 바로 `@Builder`를 붙이면 모든 필드가 빌더를 통해 외부에 노출됩니다. 이때 시스템 필드나 내부 정책으로만 세팅되어야 할 값까지 외부에서 주입될 수 있습니다.

```java
@Getter
@Builder // 모든 필드가 외부에 노출되어 버림 (안티패턴)
public class PaymentTransaction {
    private TransactionId transactionId;        // 시스템 발급
    private PartnerId partnerId;                // 필수
    private PartnerTransactionId partnerTransactionId; // 필수
    private String itemName;                    // 필수
    private Amount totalAmount;                 // 필수, > 0
    private TransactionStatus status;           // 파생/상태 전이 규칙 존재
    private LocalDateTime createdAt;            // 시스템 시간
    private String redirectUrl;                 // 화이트리스트 검증 필요
    private PaymentStatus paymentStatus;        // 파생/상태 전이 규칙 존재
}

// 사용 예 (문제)
PaymentTransaction tx = PaymentTransaction.builder()
        .transactionId(new TransactionId("ext-override"))     // 외부 임의 주입 (시스템값 오염)
        .partnerId(new PartnerId("P-1"))
        .partnerTransactionId(new PartnerTransactionId("PP-1"))
        .itemName(null)                                       // 필수 누락
        .totalAmount(new Amount(0))                           // 규칙 위반 값
        .status(TransactionStatus.PAYMENT_APPROVED)           // 전이 규칙 무시
        .createdAt(null)                                      // 시스템 값에 null 주입
        .redirectUrl("<http://phishing.example>")               // 검증 누락
        .paymentStatus(PaymentStatus.COMPLETED)               // 무단 상태 변경
        .build();
```

#### 문제

* 내부 전용 필드(transactionId, createdAt)에 외부 값이 주입되거나 null이 들어갈 수 있음
* 도메인 무결성 해침, 생성 시점 규칙이 코드 전반에 분산됨

### 생성자 레벨 `@Builder` + 검증 & 의도 중심의 안전한 생성

도메인 규칙을 담은 커스텀 생성자를 만들고, 그 생성자 위에 `@Builder`를 붙입니다. 생성자 내부에 검증 로직(null/형식/범위/상호관계 제약 등)을 배치해 객체가 항상 유효한 상태로만 생성되도록 합니다.

```java
@Getter
public class PaymentTransaction {
    private final TransactionId transactionId;
    private final PartnerId partnerId;
    private final PartnerTransactionId partnerTransactionId;
    private final String itemName;
    private final Amount totalAmount;
    private final TransactionStatus status;
    private final LocalDateTime createdAt;
    private final String redirectUrl;
    private final PaymentStatus paymentStatus;

    @Builder(access = AccessLevel.PRIVATE)
    private PaymentTransaction(
            PartnerId partnerId,
            PartnerTransactionId partnerTransactionId,
            String itemName,
            Amount totalAmount,
            String redirectUrl
    ) {
        // 1. 필수값 검증
        Objects.requireNonNull(partnerId, "partnerId is required");
        Objects.requireNonNull(partnerTransactionId, "partnerTransactionId is required");
        if (itemName == null || itemName.isBlank()) {
            throw new IllegalArgumentException("itemName must not be blank");
        }
        if (totalAmount == null || totalAmount.isZeroOrNegative()) {
            throw new IllegalArgumentException("totalAmount must be positive");
        }

        // 2. 화이트리스트 검증 (간략 버전)
        if (redirectUrl == null || !redirectUrl.startsWith("<https://pay.hanpass.com>")) {
            throw new IllegalArgumentException("redirectUrl not allowed: " + redirectUrl);
        }

        // 3. 시스템/파생 값 내부 설정
        this.transactionId = TransactionId.newId();     // 내부에서 발급
        this.partnerId = partnerId;
        this.partnerTransactionId = partnerTransactionId;
        this.itemName = itemName.trim();
        this.totalAmount = totalAmount;
        this.status = TransactionStatus.PAYMENT_READY;  // 기본 상태
        this.paymentStatus = PaymentStatus.PENDING;     // 파생 상태
        this.createdAt = LocalDateTime.now();
        this.redirectUrl = redirectUrl;
    }

    public static PaymentTransaction createReady(
            PartnerId partnerId,
            PartnerTransactionId partnerTransactionId,
            String itemName,
            Amount totalAmount,
            String redirectUrl
    ) {
        return PaymentTransaction.builder()
                .partnerId(partnerId)
                .partnerTransactionId(partnerTransactionId)
                .itemName(itemName)
                .totalAmount(totalAmount)
                .redirectUrl(redirectUrl)
                .build();
    }
}

```

#### 보완점

* 외부에서 건드릴 수 있는 필드를 최소화 (transactionId, status, createdAt 등은 내부 생성)
* 생성자 내부에서 필수값 검증 및 URL 화이트리스트 체크
* 상태/시스템 필드 자동 초기화로 항상 유효한 객체 보장
* 외부는 createReady()를 통해 의도된 생성 플로우만 사용

### 실무에선 반드시..

* `@Builder`는 생성자/팩토리의 시그니처를 외부로 노출하는 API라고 생각해야합니다. 불필요한 필드는 시그니처에 포함시키지 않아야 합니다.
* 시스템 값/파생 값은 외부에서 넣지 못하게 하고, 생성자 내부에서 계산/설정해야합니다.
* 빌더 기본값이 필요하다면 `@Builder.Default` 대신 생성자 내부 기본값을 우선 검토합니다. (전자는 컬렉션 초기화 등 일부 케이스에서만 신중히 사용)
* 도메인 상태 전이 규칙은 팩토리/정적 메서드로 드러내고, 검증과 함께 묶습니다.

### 결론

클래스 레벨 `@Builder`는 도메인 무결성을 깨뜨릴 수도 있으니 지양해야 합니다.

**커스텀 생성자에 `@Builder`를 붙이고**, **생성자 내부에 검증 로직을 배치**하면 **항상 유효한 객체만 생성됩니다.**

정적 팩토리와 병행하면 의도 중심의 생성 API를 제공하면서도, 검증과 규칙을 한 곳에 모아 유지보수성을 높일 수 있습니다.
