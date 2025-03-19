---
description: >-
  오늘은 자바에서 체크드 익셉션과 언체크드 익셉션, 그리고 커스텀 익셉션에 대해 이야기해보려고 한다. 또한 "커스텀 익셉션은 항상
  옳은가?"라는 물음에 대해 나름의 결론을 내려봤다.
---

# ⚠️ Java : Exception

## Checked Exception

체크드 익셉션은 컴파일 시점에서 예외 처리를 강제하는 익셉션이다. \
예를 들어 `IOException`이나 `SQLException` 등이 있다. \
이들은 호출되는 메서드가 예외를 던질 수 있음을 선언해야 하며(throws), \
이를 사용하는 쪽에서 반드시 예외를 처리(try-catch)하거나, 다시 throws로 던져야 한다.\
주로 파일 입출력, DB 연동 등 외부 환경이나 자원과 상호작용하는 과정에서 발생할 수 있는 예외 상황에서 사용한다. \
왜냐하면 이런 상황들은 충분히 예측 가능하고, \
호출하는 쪽에서 적절한 예외 처리를 통해 복구할 수 있는 가능성이 있기 때문이다.

## Unchecked Exception

언체크드 익셉션은 런타임 시점에 발생하며, 컴파일러가 예외 처리를 강제하지 않는 익셉션이다. `RuntimeException`을 상속하는 익셉션들이 대표적으로 해당된다. \
예를 들어 `NullPointerException`, `IndexOutOfBoundsException` 등이 있다.\
이들은 주로 프로그래머의 실수나 로직 에러로 인해 발생하며, 반드시 복구하거나 처리할 필요가 없을 수도 있다. \
따라서 런타임 익셉션들은 호출자가 따로 예외 처리를 하지 않아도 컴파일 단계에서 오류를 내지 않는다. \
하지만 적절하게 잡아내지 않으면 프로그램이 예기치 못하게 종료될 수 있으므로, 상황에 따라 처리가 필요하다.

## Error vs Exception

자바에서 `Error`는 시스템 레벨에서 발생하는 심각한 문제를 의미한다. \
예를 들어 `OutOfMemoryError`, `StackOverflowError` 등이 있다. \
이는 보통 애플리케이션 코드에서 복구하기가 사실상 불가능한 수준의 오류이므로, 직접 처리하지 않는 경우가 많다.\
반면 `Exception` 계열은 프로그램 로직 안에서 발생하는 대부분의 오류 상황을 나타낸다. \
적절한 예외 처리(try-catch, throws 등)를 통해 복구하거나 상황을 알려줄 수 있다.

## Custom Exception

자바는 이미 많은 표준 예외 클래스를 제공하지만, 때로는 표준 예외만으로는 설명이 부족하거나 \
처리 로직을 일괄적으로 관리하기 어려울 수 있다. 이럴 때 ‘커스텀 익셉션’을 만들어 사용할 수 있다.\
커스텀 익셉션을 사용하면 클래스 이름과 메시지만 보더라도 어떤 상황에서 발생한 예외인지 \
더 구체적으로 파악할 수 있다. \
또한 예외가 발생할 때 추가로 담을 정보(필드)를 선언할 수도 있어, \
로깅이나 전역 예외 처리에서 가독성과 유지보수성이 높아진다.

### Practice

```java
package com.example.demo;

import java.util.Arrays;
import java.util.List;

class PostNotFoundException extends RuntimeException {
    public PostNotFoundException() {
        super("포스트를 찾을 수 없습니다.");
    }

    public PostNotFoundException(String message) {
        super(message);
    }
}

public class PostService {

    private static final List<String> posts = Arrays.asList("개발", "잼따");

    public String getPost(int index) {
        if (index < 0 || index >= posts.size()) {
            throw new PostNotFoundException("해당 인덱스의 포스트가 존재하지 않습니다. index: " + index);
        }
        return posts.get(index);
    }

    public static void main(String[] args) {
        PostService service = new PostService();

        System.out.println(service.getPost(0));  
        System.out.println(service.getPost(5));  
}

```

<figure><img src="../../.gitbook/assets/스크린샷 2025-03-19 오후 10.10.29.png" alt=""><figcaption></figcaption></figure>

## Are Custom Exceptions Always Right?

커스텀 익셉션이 항상 정답은 아니다. \
불필요하게 너무 많은 커스텀 익셉션을 만들면 오히려 예외 클래스가 난립하여 유지보수가 어려워지거나, \
의미가 애매모호한 예외가 늘어날 수 있다. \
또한 이미 자바에서 제공하는 표준 예외로 충분히 의도를 전달할 수 있는데도 \
굳이 새 클래스를 만들면 가독성에 혼동을 줄 수 있다.\
그러나 여러 모듈에서 동일한 예외 상황을 일관성 있게 처리해야 하거나, \
특정 상황을 명확히 표현하고 싶다면 커스텀 익셉션이 큰 도움이 된다. \
또한 예외가 발생했을 때 전역적으로 특정 정보를 추가하거나, \
stack trace 생성 비용을 줄이는 등 세밀한 제어가 가능하다.

## Conclusion

결론적으로 체크드 익셉션은 복구 가능성이 있고 외부 자원과 상호작용하는 경우에 유용하며, \
언체크드 익셉션은 주로 프로그래머 실수나 로직 문제에 대응하기 위해 사용된다.\
에러는 시스템 레벨에서 발생하는 치명적인 상황이므로 애플리케이션 코드에서 직접 처리하기 어렵다.\
커스텀 익셉션은 특정 상황을 더욱 명확히 드러내고, 예외 처리 로직을 일관성 있게 가져가며, \
사용자나 개발자에게 풍부한 정보를 전달하기에 좋다. \
하지만 커스텀 익셉션이 너무 많아지면 관리가 힘들어질 수 있으므로, 꼭 필요한 경우에만 적절히 활용하는 것이 좋다.\
즉, “커스텀 익셉션은 항상 옳은가?”에 대한 대답은 당연하게도 ‘상황에 따라 다르다’\
(사실 나는 거의 무조건 커스텀 익셉션을 만들어 사용하곤한다...ㅜ)\
적절한 상황에 현명하게 선택해 사용한다면, 코드의 가독성과 유지보수성을 높일 수 있을 것이다
