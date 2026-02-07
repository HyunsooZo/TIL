# 🤔 메시지 큐 없이 외부 통신 안정성 확보하기

### 머리말

서비스를 개발하다 보면 데이터베이스 저장과 외부 시스템 호출을 동시에 처리해야 하는 상황을 마주합니다. \
예를 들어, 사용자의 결제가 완료되면 데이터베이스에 결제 내역을 저장하고, 동시에 다른 서비스에게 "결제 완료" 이벤트를 발행해야 한다고 가정해 봅시다.

만약 데이터베이스 저장은 성공했는데, 네트워크 문제로 이벤트 발행은 실패한다면 어떻게 될까요? \
시스템 간 데이터 불일치가 발생하고, 이는 곧 비즈니스 로직의 오류로 이어질 수 있습니다.

**제가 다니는 회사에서는 이벤트기반설계 / 메시지 큐를 사용하지 않으므** 이 글에서는 **메시지 큐를 사용할 수 없는 환경**에서 \
이 패턴을 어느 상황에 어떻게 적용했는지 포스팅하려 합니다.

### **Transactional Outbox Pattern?**

머릿말에서 말한 것 처럼 **두 개 이상의 시스템에 데이터를 안정적으로 써야 하는 문제**를 해결할 수 있는 패턴중 하나가**Transactional Outbox** 패턴입니다. \
**Transactional Outbox**의 핵심은 "외부 시스템은 언제든 실패할 수 있다"는 사실을 인정하는 것에서 출발합니다. \
API 호출, 메시지 발행 등 내 서비스의 **트랜잭션 범위 밖에서 일어나는 일은 성공을 보장할 수 없습니다.**

Transactional Outbox 패턴은 이 문제를 아주 간단한 아이디어로 해결합니다.

> **"외부에 메시지를 바로 보내지 말고, 일단 우리 데이터베이스 '보낼 편지함(Outbox)' 테이블에 저장해두자. 그리고 완전히 다른 프로세스가 그 편지함을 보고 메시지를 대신 보내게 하자."**

이렇게 하면 비즈니스 로직(DB 저장)과 메시지 발행(외부 통신)을 분리되며 아래와 같은 이점을 얻습니다.

**데이터 정합성 보장** 비즈니스 데이터 저장과 'Outbox' 테이블 저장이 하나의 원자적인 트랜잭션으로 묶여 \
둘 중 하나만 성공하는 상황이 발생하지 않습니다.

**장애 격리** 메시지 발행 시스템이 일시적으로 장애가 나더라도, \
핵심 비즈니스 로직은 정상적으로 처리됩니다.\
메시지는 Outbox에 안전하게 보관되어 있다가 나중에 재처리될 수 있습니다.

### 실무 적용 이유

#### 슬랙 알림 발송의 데이터 정합성 문제

제가 개발 중인 결제 시스템에는 오류 발생 시 슬랙으로 즉시 알림을 보내는 기능이 있었습니다. 문제는 여기서 발생합니다.

```java
@Transactional
public void processPayment(PaymentCommand command) {
    // 1. 결제 처리
    var payment = paymentRepository.save(new Payment(command));

    // 2. 슬랙 알림 발송
    slackClient.sendNotification("결제 완료: " + payment.getId());
    // 네트워크 오류 발생!
}
```

만약 슬랙 API 호출이 실패하면?

* **옵션 1**: 전체 트랜잭션 롤백 → 결제가 취소됨 (비즈니스 손실)
* **옵션 2**: 알림만 실패하고 진행 → 알림이 영원히 안 감 (운영 혼란)

결국 **외부 시스템 장애가 핵심 비즈니스 로직을 방해**하는 구조입니다.

아래 순환 과정 그래프와 코드를 참고하면 더 이해가 잘되실겁니다.

<figure><img src="../../.gitbook/assets/스크린샷 2026-02-07 22.12.22.png" alt=""><figcaption></figcaption></figure>

### 기존 해결 방식의 문제점

가장 빠른 해결책은 예외를 try-catch로 감싸서 무시하는 것입니다.

```java
@Transactional
public void processPayment(PaymentCommand command) {
    var payment = paymentRepository.save(new Payment(command));

    try {
        slackClient.sendNotification("결제 완료: " + payment.getId());
    } catch (Exception e) {
        log.error("슬랙 알림 실패", e);
        // 그냥 무시하고 진행...
    }
}
```

하지만 이 방식은:

* 알림이 실패하면 **영원히 복구 불가능**합니다.
* 재시도 로직을 직접 구현해야 합니다.
* 실패한 알림을 추적/관리할 방법이 없습니다.

### Transactional Outbox 패턴으로 리팩토링

저는 이 문제를 해결하기 위해 **Transactional Outbox 패턴**을 도입했습니다.

비즈니스 로직과 외부 통신을 완전히 분리하고, 별도의 프로세스가 안정적으로 재시도하도록 하는 것입니다.

#### 1단계: Outbox 테이블에 알림 정보 저장

외부 API를 바로 호출하지 않고, "발송해야 할 알림"을 Outbox 테이블에 저장합니다.

```java
@Slf4j
@Service
@RequiredArgsConstructor
public class NotificationApplicationService {

    private final NotificationRepository notificationRepository;

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void recordCriticalError(SendNotificationCommand command) {
        try {
            // 1. 도메인 모델 생성 (초기 상태: PENDING)
            var notification = Notification.createCriticalError(
                    command.serviceName(),
                    command.message(),
                    command.exception(),
                    command.transactionId(),
                    command.partnerId()
            );

            // 2. 알림 내역 저장 (Outbox 테이블에 저장)
            notificationRepository.save(notification);

        } catch (Exception e) {
            log.error("[NOTIFICATION-SERVICE] Failed to save notification history.", e);
        }
    }
}

```

**핵심 포인트:**

* `PENDING` 상태로 알림 정보만 저장
* `@Transactional(propagation = Propagation.REQUIRES_NEW)`로 독립적인 트랜잭션 생성
* 이 저장이 실패해도 원래 비즈니스 로직에는 영향 없음

#### 2단계: 스케줄러가 Outbox를 폴링하여 발송

이제 저장된 알림을 실제로 발송할 별도의 프로세스가 필요합니다.

```java
@Slf4j
@Component
@Profile("prd")
@RequiredArgsConstructor
public class NotificationScheduler {

    private static final int MAX_RETRY_COUNT = 3;
    private final NotificationRepository notificationRepository;
    private final NotificationSender notificationSender;

    @Scheduled(fixedDelay = 300000) // 5분마다 실행
    @Transactional
    public void processPendingNotifications() {
        // 1. 발송 대기중인 알림 조회 (비관적 락 사용 - 서버 이중화되어있어서..)
        var pendingNotifications = notificationRepository.findPendingNotificationsWithLock(10);
        if (pendingNotifications.isEmpty()) {
            return;
        }

        log.info("[NOTIFICATION-SCHEDULER] Found {} pending notifications...", pendingNotifications.size());

        for (var notification : pendingNotifications) {
            try {
                // 2. 실제 알림 발송 (외부 API 호출)
                notificationSender.send(notification);

                // 3. 발송 성공 시 상태를 SENT로 업데이트
                notification.markAsSent();
                notificationRepository.save(notification);

            } catch (Exception e) {
                // 4. 발송 실패 시 재시도 횟수 확인
                if (notification.getRetryCount() >= MAX_RETRY_COUNT) {
                    notification.markAsFailed();
                    notificationRepository.save(notification);
                }
            }
        }
    }
}

```

**핵심 포인트:**

* 5분마다 `PENDING` 상태 알림을 조회
* **비관적 락**으로 여러 서버가 동시에 처리하는 것 방지
* 최대 3회 재시도 후 `FAILED` 상태로 전환

<figure><img src="../../.gitbook/assets/스크린샷 2026-02-07 22.13.05.png" alt=""><figcaption></figcaption></figure>

### 리팩토링 후 얻은 이점

Transactional Outbox 패턴을 도입함으로써 여러 가지 이점을 얻을 수 있었습니다.

* **데이터 정합성 보장**: 비즈니스 데이터 저장과 알림 저장이 하나의 트랜잭션으로 묶임
* **장애 격리**: 슬랙 API 장애가 결제 처리를 방해하지 않음
* **자동 재시도**: 실패한 알림을 최대 3회까지 자동으로 재시도
* **추적 가능성**: Outbox 테이블을 통해 모든 알림 이력 추적 가능
* **확장성**: 슬랙 외에 이메일, SMS 등 다른 알림도 같은 방식으로 처리 가능

### 결론

외부 시스템 호출은 언제든 실패할 수 있습니다. `try-catch`로 무시하거나 **동기적으로 재시도하는 방식은 임시방편**일 뿐입니다. 대신 **Transactional Outbox 패턴**을 활용하면, 메시지 큐 같은 별도의 인프라 없이도 안정적이고 추적 가능한 외부 통신을 구현할 수 있습니다. 특히 슬랙 알림, 이메일 발송, Webhook 호출 등 **비즈니스 크리티컬하지 않지만 놓쳐서는 안 되는 작업**에 매우 유용한 패턴입니다.

### 추가

포스팅을 작성하면서 `@Transactional(propagation = REQUIRES_NEW)`를 사용했는데, 이 부분에 대해 보충 설명이 필요할 것 같습니다. (김완주 대리 피드백) 예시의 결제 실패 → 슬랙 처럼 알림이 없어도 괜찮은 상황이라면 `REQUIRES_NEW`를 사용해도 됩니다. 이 경우 알림 저장 실패가 핵심 비즈니스 로직을 방해하지 않습니다. (다만 이경우 아웃박스패턴의 본질인 데이터정합성 이 흐렺집니다.) 대부분의 Outbox 패턴 사용 케이스라면, 즉 "결제 후 배송", "송금 후 입금 완료" 처럼 **후속 작업이 반드시 발생해야 하는 경우라면, 하나의 트랜잭션으로 관리하는 것이 맞습니다.** 이부분 꼭 참고부탁드립니다.
