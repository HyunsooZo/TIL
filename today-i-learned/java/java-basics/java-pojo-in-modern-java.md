# 🥛 Java : POJO in Modern Java

## POJO?

POJO는 **Plain Old Java Object**의 약자로, \
**특정 프레임워크나 라이브러리에 의존하지 않는 순수한 자바 객체**를 의미한다.

* 특별한 상속이나 어노테이션이 없어도 되고
* getter/setter, 생성자, toString 같은 일반적인 메서드만 갖는 객체

POJO라는 개념은 **EJB 시대처럼 무거운 프레임워크에 의존했던 자바 객체**들과 구분하기 위해 등장했다.

즉, "프레임워크 없이도 도메인을 표현할 수 있다"는 철학에서 출발한 개념이다.

***

## Why POJO Still Matters

현대 자바 생태계에서도 POJO는 여전히 중요하다. \
오히려 **스프링, JPA, Jackson** 등 다양한 프레임워크가 POJO 스타일을 존중하도록 설계되어 있다.

* **Spring**: 빈 정의 없이도 @Component, @Service 등으로 POJO 객체를 스캔
* **JPA**: POJO를 Entity로 간단히 매핑
* **Jackson**: POJO를 JSON 직렬화/역직렬화 대상으로 사용
* **테스트 용이성**: 외부 의존성 없이 독립적으로 테스트 가능

***

## POJO vs JavaBean vs DTO

<table><thead><tr><th width="118.90625">구분</th><th width="339.62890625">특징</th><th>목적</th></tr></thead><tbody><tr><td>POJO</td><td>순수 자바 객체, 아무 의존성 없음</td><td>도메인 모델, 상태 표현</td></tr><tr><td>JavaBean</td><td>기본 생성자 + getter/setter + Serializable</td><td>UI, 데이터 바인딩 용도</td></tr><tr><td>DTO</td><td>계층 간 데이터 전달용, 불변성을 가지기도 함</td><td>컨트롤러 ↔ 서비스 ↔ 외부 전송</td></tr></tbody></table>

***

## In Practice

```java
public class User {
    private String name;
    private int age;

    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    @Override
    public String toString() {
        return "User{name='" + name + "', age=" + age + '}';
    }
}
```

* 프레임워크 어노테이션 없이도 독립적으로 동작 가능
* JPA Entity나 Jackson 직렬화 대상이 될 수도 있음

***

## POJO & DDD

현대 도메인 주도 설계(DDD)에서도 POJO는 핵심이다. \
**Entity, VO, Aggregate Root** 모두 POJO 형태로 설계되며, \
이 객체들은 **비즈니스 불변성과 규칙**을 표현하는 주체다.

* @Entity 등의 JPA 어노테이션은 기술적 관심사
* 비즈니스 로직은 POJO 안에 설계 가능 (e.g. validate, calculate, changeState 등)

***

## POJO in Spring Projects

스프링 프로젝트에서는 POJO가 핵심 구조의 기반이 된다. 아래와 같은 방식으로 자연스럽게 활용된다:

* **@Component, @Service, @Repository** 등으로 빈 등록되지만, 객체 자체는 POJO 스타일 유지
* **Entity 클래스**도 결국 getter/setter만 갖는 POJO 기반의 구조
* **DTO, Request/Response 객체** 역시 POJO 스타일로 설계해 Jackson 등과 연동
* **테스트 코드**에서 POJO는 외부 환경과 무관하게 단독 실행 가능하므로 단위 테스트가 용이함

즉, 스프링은 POJO를 우선으로 하고 여기에 어노테이션 기반 설정을 덧붙이는 \
**비침투적 프로그래밍 모델(non-intrusive programming)**&#xC744; 지향한다.

***

## Conclusion

POJO는 단순한 개념이지만, **유연하고 유지보수 가능한 구조의 기반**이 된다. \
프레임워크에 종속되지 않고 **도메인 중심의 설계와 테스트 가능한 아키텍쳐를 가능하게 한다.**

오늘날의 스프링 기반 개발, 마이크로서비스, 클린 아키텍쳐에서도 여전히 중요한 중심축이다.
