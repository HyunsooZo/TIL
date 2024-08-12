---
description: >-
  Spring Framework에서 제공하는 유틸리티 클래스로, 주로 테스트 코드에서 사용되며, 리플렉션(Reflection)을 이용해 객체의
  필드나 메서드에 접근하거나 값을 설정하는 데 사용됨
---

# ❕ ReflectionTestUtils

경력있는 다른 개발자 분들께 문의해 본 결과 `ReflectionTestUtils`는 특정 상황에서 유용할 수 있지만, 일반적으로는 피하는 것이 좋다는 의견이 많은 것 같다.\
그래서 해당부분을 걷어내고 다시 코드를 짜려고 한다.

*   **대안**

    * \`@UtilityClass\`의 경우 인스턴스화 하여 주입받아 Testable 하게 만들기\
      &#x20;(본인의 경우 회사코드이기도 하고 너무 광범위 하게 사용되어 사실 길게잡고 해당부분을 리팩토링 해야할 것 같다.)
    * `@Value`와 같은 케이스는 `@TestPropertySource`나 `@DynamicPropertySource`를 사용해서 테스트 환경에서 주입되는 프로퍼티 값을 설정하는 방법이 낫다고 생각된다.
    * **유틸리티 클래스의 범용적 사용 제한**: \
      모든 코드에서 유틸리티 클래스를 범용적으로 사용하는 것보다는, 특정 모듈이나 기능 내에서만 사용되도록 경계를 정하는 게 유지보수에 좋다는 의견이 있었다.
    * **컴포지트 패턴 적용**: \
      유틸리티 메서드들을 단순히 나열하지 말고, \
      서로 관련 있는 메서드들을 묶어서 사용하는 구조(컴포지트)를 만드는 게 좋다는 의견이 있었다.


* **결론**
  * 단순 해피케이스 작성목적으로는 정말 어쩔 수 없을땐 사용하되 최대한 사용을 지양하자

\
~~개발을 하다보면 보안 등의 이유로 민감한 데이터를 `@Value` 를 통해~~ \
~~application.yml 에 등록한 property 들을 가져와 사용하는 경우가 많다.~~&#x20;

~~`@SpringBootTest` 사용 시 해당 데이터는~~ \
~~`@SpringBootTest(properties = "private.test.value=value")`~~ \
~~properties 설정을 통해 손쉽게 바인딩 할 수 있지만 느린 테스트 속도와 자원비용이 높아~~ \
~~`@SpringBootTest`를 사용하지 않는 경우 `ReflectionTestUtils` 를 통해 해결이 가능하다.~~\
~~아래 예시 코드를 참고해보자.~~&#x20;

```java
@BeforeEach
    void setUp() {
        //TargetService 클래스의 secretKey 필드 값을 "nobodyisgonnaknow"로 설정
        ReflectionTestUtils.setField(TargetService, "sceretKey", "nobodyisgonnaknow");
    }

 @Test
    void testGetSecretKey() {
        String secretKey = TargetService.getSecretKey();
        assertThat(secretKey).isEqualsTo("nobodyisgonnaknow");
    }
```

~~이제 테스트 코드 내에서 해당 값을 사용하는 로직이 포함된 경우에도 관련된 예외 없이 내가 셋팅해둔 데이터로~~\
~~처리가 가능하다.~~&#x20;
