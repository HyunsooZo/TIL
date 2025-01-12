---
description: >-
  무서울 정도로 테스트 코드 커버리지에 집착하는동료의 레포지토리에 작업할 일이 있었는데 JaCoCo 커버리지 임계값이 0.9 이어서 상당히
  애먹었던 기억이 있다. 지금와서는 그때 그 동료가 왜 그렇게 까지 테스트 코드에 집착했는지 이해가 간다. 높은 코드 품질 및 유지보수 /
  요구사항 변경을 위한 테스트 코드 커버리지 및 실전 예시를 포스팅 해본다.
---

# ❕ Test : Coverage & Practice

## Why Test Coverage Matters?

테스트 커버리지는 코드가 얼마나 테스트되었는지를 보여주는 지표로, \
코드의 품질과 안정성을 높이는 데 중요한 역할을 한다. \
테스트 커버리지를 효과적으로 관리하면 버그를 예방하고 유지보수를 쉽게 할 수 있다.

## How to Increase Test Coverage

### 1. Prioritize Critical Paths

애플리케이션에서 중요한 **핵심 로직**이나 크리티컬한 기능에 우선적으로 테스트를 작성해야 한다.

* 주요 서비스 메서드
* 중요한 데이터 처리 로직
* 외부 API와의 통신 부분

### 2. Use Parameterized Tests

다양한 입력값에 대해 테스트를 작성하여 커버리지를 높일 수 있다. \
JUnit의 `@ParameterizedTest`를 활용하면 효율적으로 작성 가능하다.&#x20;

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;

class CalculatorTest {

    @ParameterizedTest
    @CsvSource({"1, 1, 2", "2, 3, 5", "5, 5, 10"})
    void addNumbers(int a, int b, int result) {
        Calculator calculator = new Calculator();
        assertEquals(result, calculator.add(a, b));
    }
}
```

### 3. Write Tests for Edge Cases

경계값, null 값, 빈 값 등 예상치 못한 입력에 대한 테스트를 추가하면 \
숨겨진 결함을 발견할 가능성이 높아진다.

* 예: 빈 리스트, 음수 입력, 최대값/최소값

<pre class="language-java"><code class="lang-java"><strong>@Test
</strong>void shouldThrowExceptionForNegativeInput() {
    assertThrows(IllegalArgumentException.class, () -> someService.process(-1));
}
</code></pre>

### 4. Leverage Mocking Frameworks

외부 의존성을 모킹하여 독립적인 단위 테스트를 작성할 수 있다. \
Mockito를 활용하면 복잡한 의존성을 쉽게 모킹 가능하다.

```java
@Test
void testWithMocking() {
    SomeService mockService = mock(SomeService.class);
    when(mockService.getData()).thenReturn("Mocked Data");

    MyService myService = new MyService(mockService);
    String result = myService.performAction();

    assertEquals("Processed: Mocked Data", result);
}
```

### 5. Use Code Coverage Tools

코드 커버리지 도구를 활용하여 누락된 테스트 영역을 쉽게 확인하고 보완할 수 있다.

* **JaCoCo**: Java 프로젝트에 가장 널리 사용되는 커버리지 도구
* **SonarQube**: 코드 품질 분석 도구로 커버리지 지표를 시각화

### 6. Adopt TDD (Test-Driven Development)

TDD를 도입하면 기능 구현 전에 테스트를 먼저 작성하여 \
자연스럽게 테스트 커버리지가 높아진다고 한다.\
머리로는 알고있지만 아직 시도해보지 못한 방법론이다. \
TDD 까진 아니어도 높은 수준의 테스트커버리지는 \
코드 품질향상에 분명히 도움된다는 정도까지만 기억하자.

1. 실패하는 테스트 작성
2. 코드를 작성하여 테스트 통과
3. 리팩토링

```java
@Test
void shouldReturnTrueForValidInput() {
    MyService service = new MyService();
    assertTrue(service.isValid("input"));
}
```

### 7. Cover Integration Points

서비스 간의 통합 지점이나 외부 API 호출, 데이터베이스 연동 부분에 대한 통합 테스트\
이 방법또한 폐쇄망을 사용하는 현업에선 조금 어려울 수 있다. \
나는 MockServer 활용, 로컬 테스트용 DB 구축 등을 사용하여 커버하는 방법을 사용하고 있다.&#x20;

* **Integration Test**

```java
@SpringBootTest
class IntegrationTest {

    @Autowired
    private SomeController controller;

    @Test
    void integrationTest() {
        ResponseEntity<String> response = controller.someEndpoint();
        assertEquals(HttpStatus.OK, response.getStatusCode());
    }
}
```

* **MockServer**&#x20;

서버 모킹관련해서는 아래 포스팅 참고 바람!

{% content-ref url="mockito-mockrestserviceserver.md" %}
[mockito-mockrestserviceserver.md](mockito-mockrestserviceserver.md)
{% endcontent-ref %}

* **Local Test DB (H2) Example**

```gradle
dependencies {
    testImplementation 'com.h2database:h2'
}
```

```yaml
spring:
  datasource:
    url: jdbc:h2:mem:testdb
    driver-class-name: org.h2.Driver
    username: sa
    password:
  h2:
    console:
      enabled: true
  jpa:
    hibernate:
      ddl-auto: create-drop
    show-sql: true
```

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.ActiveProfiles;

import java.util.Optional;

import static org.assertj.core.api.Assertions.assertThat;

@SpringBootTest 
@ActiveProfiles("local") // local 프로파일 활성화
class UserRepositoryLocalTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    void saveAndFindUserUsingLocalDB() {
        // Given
        User user = new User("jane_doe", 28);
        userRepository.save(user);

        // When
        Optional<User> foundUser = userRepository.findById(user.getId());

        // Then
        assertThat(foundUser).isPresent();
        assertThat(foundUser.get().getUsername()).isEqualTo("jane_doe");
        assertThat(foundUser.get().getAge()).isEqualTo(28);
    }
}
```

## Best Practices for Writing Tests

### Keep Tests Simple

* 테스트는 **단순하고 명확**해야 한다.
* **복잡한 로직**은 테스트 코드가 아니라 **애플리케이션 코드에서 처리**하도록 한다.

### Use Descriptive Names

* 테스트 메서드 이름은 테스트의 **의도를 명확히** 드러내야 한다.
* `@DisplayName` 어노테이션 사용은 테스트 의도를 보다 분명히 해줄 수 있다.!!

```java
@Test
@DisplayName("사용자 못찾으면 예외를 반환함")
void shouldReturnErrorWhenUserIsNotFound() {
    // 명확한 이름으로 의도 전달
}
```

### Avoid Over-Mocking

* 과도한 모킹은 테스트 유지보수를 어렵게 만든다.
* 필요한 부분만 모킹하고 나머지는 실제 객체를 사용.

### Regularly Review Test Cases

* 테스트가 오래되면 코드와 동기화되지 않을 가능성이 있다.
* 정기적으로 테스트 코드를 점검하고 필요시 업데이트.

### Conclusion

테스트 커버리지를 높이는 것은 단순히 수치를 올리는 것이 아니라, \
코드의 품질과 안정성을 확보하는 데 있다. \
물론 개인프로젝트가 아닌 현업에 적용 시 폐쇄망 및 기타 라이브러리 사용으로 인해 \
완벽한 테스트코드 작성이 어려울 수 있다. \
그렇다 하더라도 테스트 코드는 높은 코드품질 유지에 있어 옵션이 아닌 필수사항이라고 생각한다.\
작성이 가능한 범위 내에서 위의 방법과 실전활용 예시를 참고하여 \
효율적이고 유지보수 가능한 테스트 코드를 작성해 보자.
