---
description: >-
  애플리케이션에서 외부 API 호출이나 데이터베이스와 같은 시스템 간 통신은 네트워크 문제나 일시적인 장애로 인해 실패할 수 있다. 이러한
  실패를 관리하기 위해 재시도 로직이 필요하다. Spring에서는 이를 간단히 처리할 수 있도록 RetryTemplate을 제공한다.
---

# ⛳ Spring : RetryTemplate

## RetryTemplate?

**RetryTemplate**은 Spring Retry 라이브러리에서 제공하는 유틸리티 클래스이다. \
이를 통해 재시도 로직을 명시적으로 작성하지 않고, 간단히 구현할 수 있다. \
주로 다음과 같은 상황에서 사용된다

* 외부 API 호출 실패 시 재시도
* 데이터베이스 연결 문제 복구
* 일시적인 장애로 인한 예외 처리

***

## Core Concepts of RetryTemplate

### Retry Policy

재시도 정책을 정의하는 역할을 한다. 대표적으로 사용되는 정책은 다음과 같다

* **SimpleRetryPolicy**: 고정된 횟수만큼 재시도.
* **TimeoutRetryPolicy**: 정해진 시간 동안 재시도.
* **ExceptionClassifierRetryPolicy**: 예외 유형에 따라 다른 재시도 정책을 적용.

### BackOff Policy

재시도 간의 대기 시간을 설정한다. 다음과 같은 정책이 있다

* **FixedBackOffPolicy**: 고정된 대기 시간.
* **ExponentialBackOffPolicy**: 재시도할 때마다 대기 시간이 점점 증가.
* **NoBackOffPolicy**: 대기 시간 없음.

### RetryCallback

실제 재시도 로직을 구현하는 부분이다.

### RecoveryCallback

모든 재시도가 실패했을 때 실행되는 콜백이다.

***

## How to Use&#x20;

Spring에서 `RetryTemplate`을 설정하고 사용하는 방법을 단계별로 정리해봤다.

### Step 1: Add Dependency&#x20;

```yaml
implementation 'org.springframework.retry:spring-retry:1.3.4'
```

***

### Step 2: Bean Configuration

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.retry.backoff.ExponentialBackOffPolicy;
import org.springframework.retry.policy.SimpleRetryPolicy;
import org.springframework.retry.support.RetryTemplate;

@Configuration
public class RetryConfig {

    @Bean
    public RetryTemplate retryTemplate() {
        RetryTemplate retryTemplate = new RetryTemplate();

        // Retry Policy 설정: 최대 3회 재시도
        SimpleRetryPolicy retryPolicy = new SimpleRetryPolicy();
        retryPolicy.setMaxAttempts(3);
        retryTemplate.setRetryPolicy(retryPolicy);

        // BackOff Policy 설정: 재시도 간 대기 시간
        ExponentialBackOffPolicy backOffPolicy = new ExponentialBackOffPolicy();
        backOffPolicy.setInitialInterval(1000); // 1초
        backOffPolicy.setMultiplier(2.0);      // 2배씩 증가
        backOffPolicy.setMaxInterval(5000);   // 최대 5초
        retryTemplate.setBackOffPolicy(backOffPolicy);

        return retryTemplate;
    }
}
```

***

### Step 3: Use

#### RetryTemplate을 직접 호출하여 재시도 로직 구현

```java
import org.springframework.retry.support.RetryTemplate;
import org.springframework.stereotype.Service;

@Service
public class ApiService {

    private final RetryTemplate retryTemplate;

    public ApiService(RetryTemplate retryTemplate) {
        this.retryTemplate = retryTemplate;
    }

    public String callExternalApi() {
        return retryTemplate.execute(
            retryContext -> {
                // 재시도할 로직
                System.out.println("Trying to call API...");
                if (Math.random() > 0.5) {
                    throw new RuntimeException("API call failed!");
                }
                return "Success!";
            },
            retryContext -> {
                // 모든 재시도가 실패했을 때 실행
                System.out.println("All retries failed. Recovering...");
                return "Fallback response";
            }
        );
    }
}
```

***

## Flow

<figure><img src="../../.gitbook/assets/스크린샷 2025-01-08 오후 8.08.56.png" alt=""><figcaption></figcaption></figure>

***

## Pros and Cons

#### Pros

* 간단한 설정으로 재시도 로직 구현 가능
* 다양한 재시도 및 BackOff 정책 지원
* 재사용 가능한 구성 가능

#### Cons

* 복잡한 재시도 로직은 구현이 어려울 수 있음
* 적절한 정책 설정이 중요함

***

## Conclusion

`RetryTemplate`은 안정적인 애플리케이션을 구축하기 위한 강력한 도구이다. \
이를 활용하면 예외 상황에서도 애플리케이션의 신뢰성을 유지할 수 있다. \
사용 시 적절한 정책을 설정하고, 필요 이상의 재시도를 방지하기 위한 설계가 뒷받침된다면 정말 좋은 도구인 것 같다.\
일하면서 한 번 사용 해 본 적 (api 배치) 이 있긴 한데 다른 분들 코드에서는 아직 한번도 본적은 없다.
