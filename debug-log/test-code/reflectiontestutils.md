---
description: >-
  Spring Framework에서 제공하는 유틸리티 클래스로, 주로 테스트 코드에서 사용되며, 리플렉션(Reflection)을 이용해 객체의
  필드나 메서드에 접근하거나 값을 설정하는 데 사용됨
---

# ❕ ReflectionTestUtils

개발을 하다보면 보안 등의 이유로 민감한 데이터를 `@Value` 를 통해 \
application.yml 에 등록한 property 들을 가져와 사용하는 경우가 많다.&#x20;

`@SpringBootTest` 사용 시 해당 데이터는 \
`@SpringBootTest(properties = "private.test.value=value")` \
properties 설정을 통해 손쉽게 바인딩 할 수 있지만 느린 테스트 속도와 자원비용이 높아 \
`@SpringBootTest`를 사용하지 않는 경우 `ReflectionTestUtils` 를 통해 해결이 가능하다.\
아래 예시 코드를 참고해보자.&#x20;

```java
@BeforeEach
    void setUp() {
        //TargetService 클래스의 secretKey 필드 값을 "nobodyisgonnaknow"로 설정
        ReflectionTestUtils.setField(TargetService, "sceretKey", "nobodyisgonnaknow");
    }

 @Test
    void testGetSecretKey() {
        String secretKey = TargetService.getSecretKey();
        assertThat(secretKey).isEqualsTo("nobodyisgonnaknow");
    }
```

이제 테스트 코드 내에서 해당 값을 사용하는 로직이 포함된 경우에도 관련된 예외 없이 내가 셋팅해둔 데이터로\
처리가 가능하다.&#x20;
