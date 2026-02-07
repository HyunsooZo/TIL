# 🤔 @Lazy 대신 이벤트로 순환 참조 없애기

### 머리말

서비스를 개발하다 보면 불가피한 서비스 간 순환 참조가 발생 할때가 있습니다. 가장 손쉬운 해결책으로 Spring의 `@Lazy` 어노테이션을 떠올릴 수 있지만, 이는 **근본적인 해결책이 아니며 잠재적인 문제를 감출 수도** 있습니다. \
이번 포스팅에서는 실제 프로젝트에서 `@Lazy`를 사용해 해결했던 컴포넌트 간의 순환 참조를 스프링 이벤트를 통해 어떻게 리팩토링했는지, 그리고 그로 인해 얻은 이점은 무엇인지 공유하고자 합니다.

### 실무 적용 이유

결제 사가와 취소 사가의 순환 참조제가 개발 중인 결제 시스템에는 결제 사가와 취소 사가가 존재합니다. 두 사가는 각각의 흐름을 관리하는 `PaymentSagaManager`와 `SagaStepExecutor`를 통해 실행됩니다. 문제는 여기서 발생합니다.

`PaymentCompleteStepExecutor`는 보상 로직에서 결제취소를 위해 `CancelSagaManager`를 주입받습니다. `CancelSagaManagerImpl`은 `CancelSagaManager`의 구현체입니다. `CancelSagaManagerImpl`은 모든 `SagaStepExecutor` 구현체들을 리스트로 주입받습니다.

근데 `PaymentCompleteStepExecutor`가 `SagaStepExecutor`의 구현체 중 하나라 리스트에 포함됩니다.

결과적으로 `PaymentCompleteStepExecutor`를 생성하기 위해 `CancelSagaManagerImpl`이 필요하고, `CancelSagaManagerImpl`을 생성하기 위해 다시 `PaymentCompleteStepExecutor`가 필요한 순환 구조가 발생합니다.

아래 순환 과정 그래프와 코드를 참고하면 더 이해가 잘되실겁니다.

<figure><img src="../../.gitbook/assets/스크린샷 2026-02-07 22.14.54.png" alt=""><figcaption></figcaption></figure>

```java
@Slf4j
@Component
public class PaymentCompleteStepExecutor implements SagaStepExecutor {
    private final CancelSagaManager cancelSagaManager;
    private final PaymentStateUpdateService completeStateUpdateService;
    private final PaymentTransactionRepository transactionRepository;
    private final ObjectMapper objectMapper;

    public PaymentCompleteStepExecutor(
            @Lazy CancelSagaManager cancelSagaManager, // << 여기
            @Qualifier("paymentCompleteProcessor") PaymentStateUpdateService completeStateUpdateService,
            PaymentTransactionRepository transactionRepository,
            ObjectMapper objectMapper
    ) {
        this.cancelSagaManager = cancelSagaManager;
        this.completeStateUpdateService = completeStateUpdateService;
        this.transactionRepository = transactionRepository;
        this.objectMapper = objectMapper;
    }
```

```java
@Slf4j
@Service
@RequiredArgsConstructor
public class CancelSagaManagerImpl implements CancelSagaManager {
    private final PaymentSagaRepository sagaRepository;
    private final PaymentSagaStepRepository stepRepository;
    private final List<SagaStepExecutor> stepExecutors;
    private final SagaPersistence sagaPersistenceService;

}
```

### 기존 해결 방식의 문제점

가장 빠른 해결책은 예시코드와 같이 순환 참조가 발생하는 의존성 주입 지점에 `@Lazy` 어노테이션을 붙여주는 것입니다.

`@Lazy`는 스프링 컨테이너가 빈을 생성할 때 의존성 주입을 즉시 수행하지 않고, 실제 해당 빈이 사용되는 시점에 주입하도록 지연시킵니다. 이로써 순환 참조 오류를 회피할 수 있습니다.

하지만 `@Lazy`는

* 문제를 해결하는 것이 아니라 단순히 가리는 것에 불과합니다.
* 코드의 의존성 관계를 파악하기 어려워지고, 빈 생성 시점을 예측/디버깅이 어렵습니다.
* 애플리케이션 로딩 시점이 아닌 런타임에 빈 생성 관련 오류가 발생할 수 있습니다.

### 이벤트 기반 아키텍처로 리팩토링

저는 이 문제를 해결하기 위해 스프링 이벤트를 도입했습니다.

두 `SagaManager` 간의 직접적인 의존성을 제거하고, 이벤트를 통해 느슨하게 결합하는 것입니다.

#### 1단계: 보상 요구 이벤트(CompensationRequiredEvent) 정의

보상 로직이 필요할 때 발행할 이벤트를 정의합니다.

이 이벤트는 보상 처리에 필요한 정보(어떤 사가의 어떤 단계에서 문제가 발생했는지 등)를 담고 있습니다.

```java
@Getter
public class CompensationRequiredEvent extends ApplicationEvent {
	private final PaymentSagaStep step;
	private final String reason;
	public CompensationRequiredEvent(
	        Object source,
	        PaymentSagaStep step,
	        String reason
	) {
	    super(source);
	    this.step = step;
	    this.reason = reason;
	}
}
```

#### 2단계: 보상 로직이 필요한 부분에서 CompensationRequiredEvent를 발행

```java
@Slf4j
@Component
public class PaymentCompleteStepExecutor implements SagaStepExecutor {
    private final PaymentStateUpdateService completeStateUpdateService;
    private final PaymentTransactionRepository transactionRepository;
    private final ApplicationEventPublisher eventPublisher;
    private final ObjectMapper objectMapper;

    public PaymentCompleteStepExecutor(
            @Qualifier("paymentCompleteProcessor") PaymentStateUpdateService completeStateUpdateService,
            PaymentTransactionRepository transactionRepository,
            ApplicationEventPublisher eventPublisher,
            ObjectMapper objectMapper
    ) {
        this.completeStateUpdateService = completeStateUpdateService;
        this.transactionRepository = transactionRepository;
        this.eventPublisher = eventPublisher;
        this.objectMapper = objectMapper;
    }
    
    @Override
    public void compensate(
            PaymentSagaStep step,
            String reason
    ) {
        var transactionId = step.getSaga().getTransactionId();
        log.info("[PAYMENT-COMPLETE-STEP] Publishing event to compensate PAYMENT_COMPLETE step for txId: {}", transactionId);
        try {
        // 여기서 호출!
            eventPublisher.publishEvent(new CompensationRequiredEvent(this, step, reason));
            log.info("[PAYMENT-COMPLETE-STEP] Event for txId: {} has been successfully published.", transactionId);
        } catch (Exception e) {
            log.error("[PAYMENT-COMPLETE-STEP] Failed to publish compensation event for txId: {}. This compensation will be retried.", transactionId, e);
            throw new RuntimeException("Failed to publish compensation event", e);
        }
    }
 
}
```

`StepExecutor`는 그저 "보상이 필요하다"는 이벤트만 시스템에 알립니다

#### 3단계: 이벤트 리스너 구현

```java
@Slf4j
@Component
@RequiredArgsConstructor
public class SagaEventListener {
	private final CancelSagaManager cancelSagaManager;
	private final PaymentNotificationPort paymentNotificationPort;
	
	@EventListener
	public void handleCompensation(CompensationRequiredEvent event) {
	    var step = event.getStep();
	    var transactionId = step.getSaga().getTransactionId();
	    var reason = event.getReason();
	
	    log.info("[SAGA-EVENT-LISTENER] Received event to compensate PAYMENT_APPROVAL step. Handing over to CancellationSaga for txId: {}", transactionId);
	
	    try {
	        // 이벤트를 수신하여 CancelSagaManager를 호출
	        cancelSagaManager.startSaga(transactionId, reason);
	        log.info("[SAGA-EVENT-LISTENER] CancellationSaga has been successfully started for txId: {}", transactionId);
	    } catch (Exception e) {
	        log.error("[SAGA-EVENT-LISTENER] Failed to start CancellationSaga for txId: {}. This compensation will be retried.", transactionId, e);
	        paymentNotificationPort.recordCriticalError(
	                this.getClass().getSimpleName(),
	                "Failed to start CancellationSaga for transactionId: " + transactionId,
	                e,
	                transactionId,
	                step.getSaga().getPartnerId()
	        );
	        throw new RuntimeException("[SAGA-EVENT-LISTENER] Compensation handling failed");
	    }
	}
}

```

마지막으로, 발행된 `CompensationRequiredEvent`를 수신하여 취소 사가를 시작하는 리스너를 작성합니다.

### 리팩토링 후 앋은 이점

이벤트 기반으로 아키텍처를 변경함으로써 여러 가지 이점을 얻을 수 있었습니다.

* 순환 참조라는 잠재적 이슈를 제거하고, 역할과 책임에 따라 코드를 분리함으로써 더 깔끔하고 이해하기 쉬운 구조가 되었습니다.
* 만약 결제 취소 외에 다른 보상 로직(슬랙 알림, 에러 로깅)이 필요하다면, 새로운 이벤트 리스너를 추가하기만 하면 됩니다. 기존의 이벤트 발행 코드는 전혀 수정할 필요가 없습니다.
*   각 컴포넌트를 독립적으로 테스트하기가 훨씬 수월해졌습니다.결론순환 참조는 복잡한 애플리케이션에서 언제든 발생할 수 있는 문제입니다.

    <figure><img src="../../.gitbook/assets/스크린샷 2026-02-07 22.15.27.png" alt=""><figcaption></figcaption></figure>

### 결론

`@Lazy` 어노테이션은 개발이 급할때는 유용할 수 있지만, 장기적으로는 아키텍처를 해치는 기술 부채가 될 수 있습니다. 대신 스프링 이벤트와 같은 패턴을 활용해 컴포넌트 간의 결합도를 낮추면, 더 유연하고 유지보수하기 쉬우며 확장 가능한 시스템을 만들 수 있습니다.
