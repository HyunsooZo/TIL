---
description: 스프링 AOP는 Aspect-Oriented Programming(관점 지향 프로그래밍)의 개념을 스프링 프레임워크에 적용한 기능이다.
---

# 🥥 Spring : AOP

### Why AOP?

\
AOP는 비즈니스 로직과 공통 관심 사항을 분리함으로써 코드의 응집도를 높이고 유지 보수성을 향상시킨다. \
주요 예로 로깅, 트랜잭션 관리, 보안 검증 등이 있다.

***

## Core Concepts of AOP

### Aspect

Aspect는 공통 관심 사항을 모아둔 모듈이다. 예를 들어, 로깅 로직이 Aspect로 정의될 수 있다.

### Join Point

Join Point는 어드바이스가 적용될 수 있는 지점을 의미한다. 메서드 호출, 객체 생성 등이 Join Point가 될 수 있다.

### Advice

Advice는 Join Point에서 수행될 실제 작업이다. Advice는 다음과 같은 유형으로 나뉜다:

* Before: 메서드 실행 전에 실행.
* After: 메서드 실행 후에 실행.
* Around: 메서드 실행 전후에 실행.

### Pointcut

Pointcut은 Advice를 적용할 Join Point를 결정하는 표현식이다. \
스프링 AOP는 AspectJ 포인트컷 표현식을 지원한다.

### Weaving

Weaving은 Aspect를 실제 코드에 적용하는 과정이다. 스프링 AOP는 런타임 Weaving 방식을 사용한다.

***

## Benefits of Using Spring AOP

1. **코드 분리**: 공통 관심 사항을 분리하여 비즈니스 로직을 깔끔하게 유지할 수 있다.
2. **재사용성**: 공통 로직을 여러 모듈에서 재사용 가능하다.
3. **유지보수성 향상**: 비즈니스 로직과 공통 관심 사항이 분리되어 수정 및 확장이 용이하다.

***

## How Spring AOP Works

Spring AOP는 프록시 패턴을 기반으로 동작한다. \
스프링 컨테이너는 특정 Bean에 대해 프록시 객체를 생성하고, \
이 프록시를 통해 메서드 호출을 가로채어 Advice를 실행한다.

<figure><img src="../../../.gitbook/assets/스크린샷 2025-01-04 오후 6.08.48.png" alt=""><figcaption></figcaption></figure>

***

## Setting Up Spring AOP

### Gradle Dependency

스프링 AOP를 사용하려면 `spring-aspects` 의존성을 추가해야 한다.

```xml
dependencies {
    implementation 'org.springframework:spring-aspects:3.2.0'
}
```

***

## Creating an Aspect

### Example

#### 다음은 메서드 실행 전에 로그를 출력하는 Aspect의 예이다.

```java
@Aspect
@Component
public class LoggingAspect {

    private static final Logger log = LoggerFactory.getLogger(LoggingAspect.class);

    @Before("execution(* com.example.service.*.*(..))")
    public void logBefore(JoinPoint joinPoint) {
        log.info("메서드 호출: {}", joinPoint.getSignature().getName());
    }
}
```

***

## Applying AOP with Annotations

스프링에서 AOP를 적용하려면 `@EnableAspectJAutoProxy`를 설정해야 한다.

```java
@Configuration
@EnableAspectJAutoProxy
public class AppConfig {
    // 기타 Bean 
}
```

***

## Common Use Cases of AOP

1. **로깅**: 모든 메서드 호출 또는 특정 서비스 호출 시 로그를 남긴다.
2. **트랜잭션 관리**: 메서드 실행 전후로 트랜잭션을 시작하고 종료한다.
3. **보안**: 특정 조건에 따라 접근 권한을 확인한다.

***

## Limitations of Spring AOP

1. **메서드 기반 Join Point**: Spring AOP는 메서드 호출에만 적용된다.
2. **프록시 기반 제한**: 프록시 객체만 AOP의 대상이 된다.

***

## Conclusion

스프링 AOP는 공통 관심 사항을 쉽게 분리할 수 있는 강력한 도구이다. \
적절히 사용하면 코드 품질을 크게 향상시킬 수 있지만, \
과도한 사용은 코드 복잡도를 높일 수 있으므로 주의해야 한다.\
그리고 실수로라도 비즈니스 로직을 포함하지 않도록 주의하자.
