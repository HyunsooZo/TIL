---
description: >-
  백엔드 개발자로서 스프링을 제대로 활용하려면 스프링의 핵심 특징과 최적화된 사용 방법을 알아 둬야 한다. 프레임워크는 미리 정의된 틀과 규칙
  안에서 동작하도록 설계되어있기 때문에 그 틀을 제대로 이해하고 활용해야 가장 효율적인 코드를 작성할 수 있다. 이 섹션 에서는 스프링의 주요
  특징과 효율적으로 사용하는 방법을 포스팅 해본다.
---

# 🍃 Spring

## Key Features of the Spring&#x20;

### **IoC (Inversion of Control) & DI (Dependency Injection)**

* **IoC란?**
  * 객체의 생성과 의존성 관리를 개발자가 아닌 컨테이너(스프링)에 맡기는 디자인 패턴.
  * 개발자는 객체의 생명 주기와 의존성 연결을 직접 처리하지 않아도 됨.
  * 객체 간 결합도를 낮추고 코드 재사용성과 유지보수성을 높이는 데 핵심 역할.
*   **DI란?**

    * 의존성 주입(DI)은 객체가 필요한 의존성을 스스로 생성하지 않고 외부에서 주입받도록 하는 기법.
    * 스프링에서는 **`@Autowired`, `@Qualifier`** 또는 생성자 주입 방식으로 DI를 구현

    <figure><img src=".gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>
* **활용 팁**
  * **생성자 주입**을 기본으로 사용하자: 더 안전하고 테스트 작성이 쉬움.
  * **@ComponentScan** 으로 자동 등록이 되는 범위를 명확히 지정해 불필요한 빈 로드를 방지

***

### **AOP (Aspect-Oriented Programming)**

* **AOP란?**
  * 애플리케이션의 여러 모듈에서 공통적으로 필요한 횡단 관심사(로깅, 보안, 트랜잭션 관리 등)를 별도로 분리하는 프로그래밍 기법.
  * 핵심 비즈니스 로직과 부가 로직을 분리하여 가독성과 유지보수를 높임.
*   **구성 요소**

    * **Advice**: 실제 실행할 부가 로직 (e.g., 메서드 호출 전/후 로직).
    * **Pointcut**: Advice가 적용될 지점을 정의.
    * **Aspect**: Pointcut과 Advice를 묶은 모듈.

    <figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption></figcaption></figure>
* **활용 팁**
  * 로깅, 트랜잭션, 보안 검증 같은 로직은 AOP를 활용해 코드를 깔끔하게 분리.
  * 성능 문제를 방지하기 위해 복잡한 Pointcut 표현식을 남용하지 말 것.

***

### **MVC (Model-View-Controller)**

* **MVC란?**
  * 스프링은 웹 애플리케이션의 구조를 Model, View, Controller로 나누어 개발하는 패턴을 지원.
  * 각 역할이 명확히 분리되어 유지보수가 쉬움.
*   **구조**

    * **Model**: 데이터와 비즈니스 로직 담당.
    * **View**: 사용자에게 보여질 화면 (e.g., Thymeleaf, HTML, JSON).
    * **Controller**: 사용자의 요청을 받고, Model과 View를 연결.

    <figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>
* **활용 팁**
  * Controller에서는 비즈니스 로직을 최대한 제거하고, Service 계층에 위임.
  * DTO 를 사용해 Presentation 계층과 비즈니스 계층 간 데이터 전달 명확화.
  * RESTful API를 개발할 때는 **ResponseEntity**를 활용해 응답 데이터를 일관되게 전달.

***

### **Transaction Management**

* **트랜잭션 관리란?**
  * 데이터베이스 작업에서 여러 단계를 하나의 작업으로 묶어주는 기능.
  * 스프링에서는 `@Transactional`을 통해 트랜잭션 경계를 선언적으로 설정 가능.
*   **트랜잭션 옵션**

    * **Propagation**: 트랜잭션 전파 방식 설정 (e.g., REQUIRED, REQUIRES\_NEW).
    * **Isolation**: 트랜잭션 간 데이터 격리 수준 설정.
    * **Rollback**: 특정 예외 발생 시 롤백 여부 설정 가능.

    <figure><img src=".gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>
* **활용 팁**
  * Service 계층에서만 트랜잭션을 관리하고, 하위 계층에서는 트랜잭션 처리 로직을 제거.
  * 읽기 전용 작업에는 `@Transactional(readOnly = true)`를 활용해 성능 최적화.

***

### **Flexible Configuration**

* **XML vs Java Config vs Annotation**
  * 스프링 초기에는 XML로 빈을 설정했지만, 요즘은 Java Config와 어노테이션 기반 설정을 선호.
  * Java Config는 컴파일 시점에 오류를 확인할 수 있어 안정성이 높음.
* **활용 팁**
  * Java Config를 기본으로 사용하고, 환경별로 다른 설정이 필요하면 **Profile**을 함께 활용.
  * **application.yml**로 환경별 설정을 정리하고, `@Value`로 값을 주입.

***

### **Testing Support**

* **Spring Test Framework**: 스프링 컨텍스트를 테스트 환경에서도 로드 가능.
* **MockMvc**: Controller 테스트를 위한 강력한 도구.
* **DataJpaTest**: JPA 테스트를 위한 전용 어노테이션으로 데이터베이스 동작 테스트.
* **활용 팁**
  * 테스트 코드에서 Spring Context를 필요 이상으로 로드하지 않도록 주의.
  * Mock 객체를 활용해 의존성을 분리하면 테스트 속도가 더 빨라짐.

***

## **Efficient Use of Spring Framework**

### **Understanding Spring Context and Bean Lifecycle**

* Bean의 생성, 초기화, 소멸 과정을 알아두면 더 최적화된 코드를 작성 가능.
* `@PostConstruct`, `@PreDestroy`로 초기화와 종료 시 로직 추가 가능.

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

### Adhering to Layered Architecture

* Controller와 Service, Repository 간의 책임을 명확히 분리.
* **CQRS (Command Query Responsibility Segregation)** 패턴도 고려해볼 만함.

### Efficient Transaction Management

* 비즈니스 로직의 경계를 명확히 하고, 필요한 곳에만 트랜잭션을 적용.
* 데이터 일관성을 유지하기 위해 트랜잭션 내에서만 데이터 상태를 변경.

### Avoid Overusing AOP

* AOP는 필요한 곳에만 적용하되, 과도하게 Pointcut 표현식을 사용하면 성능 저하 발생.

### Focusing Business Logic in the Service Layer

* Controller는 최대한 가볍게 유지하고, 모든 비즈니스 로직은 Service 계층으로 이동.

### Performance Optimization

* 데이터베이스 쿼리는 최소화하고, 필요한 경우 **캐싱** 활용 (e.g., Redis).
* 대량 데이터 처리 시에는 **Spring Batch**를 도입해 효율적으로 처리.

### Leveraging Spring Boot and Actuator

* Actuator를 통해 애플리케이션 상태를 모니터링하고, 프로덕션 환경에서 문제를 빠르게 파악.
