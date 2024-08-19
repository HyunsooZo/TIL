---
description: Mockito 프레임워크에서 사용되는 클래스로, 정적 메서드를 모킹하는 데 사용
---

# ❕ MockedStatic

#### **Static 메서드 모킹**&#x20;

테스트 코드를 작성하다 보면 정적(static) 메서드를 사용해야 하는 경우가 종종 발생한다. \
예를 들어, 공통 유틸리티 클래스를 사용하는 경우가 이에 해당한다. \
회사에서 테스트 코드를 작성하는 중 이러한 케이스를 마주쳤고, \
해당 글은 이러한 static method 를 모킹(mocking)하는 방법을 정리해본다.

#### **Mockito와 MockedStatic 사용**

Mockito 프레임워크를 사용하면 인스턴스 메서드뿐만 아니라 정적 메서드도 모킹할 수 있다. \
이를 위해 `MockedStatic` 클래스를 사용한다.&#x20;

#### **예시**&#x20;

다음은 그 방법에 대한 간단한 예시이다.

```java
@Test
    public void testStaticMethod() {
        // static method 모킹
        try (MockedStatic<UtilityClass> mockedStatic = mockStatic(UtilityClass.class)) {
            // 목 메서드 동작 정의
            mockedStatic.when(UtilityClass::staticMethod).thenReturn("ta-da~~");

            // 호출 하여 위에서 정의한 값 받아오기
            String result = UtilityClass.staticMethod();
            assertThat(result).isEqaulsTo("ta-da~~");

            // 호출 여부 체크
            mockedStatic.verify(UtilityClass::staticMethod);
        }
    }
```
