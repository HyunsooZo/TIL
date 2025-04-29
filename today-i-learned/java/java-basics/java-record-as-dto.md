# 🥋 Java : Record as DTO

## Record?

Record는 **Java 16**에서 정식 도입된 특별한 클래스 유형이다. \
기본적으로 **불변성(Immutable)** 을 제공한다.

Record를 사용하면:

* 모든 필드가 자동으로 `final`로 선언된다.
* 필드만 선언하면 Java가 자동으로 다음을 생성해준다:
  * 생성자
  * Getter
  * `equals()`
  * `hashCode()`
  * `toString()`

**즉,  DTO나 VO 작성 시 반복되는 코드를 크게 줄일 수 있다.**

***

## DTO vs Record

#### 기존 DTO

```java
public class MemberDto {
    private final String name;
    private final String email;
    private final int age;

    public MemberDto(String name, String email, int age) {
        this.name = name;
        this.email = email;
        this.age = age;
    }

    public String getName() { return name; }
    public String getEmail() { return email; }
    public int getAge() { return age; }
}
```

```java
public record MemberDto(String name, String email, int age) {}
```

* 생성자, getter, equals, hashCode, toString 자동 생성
* 객체 생성 이후 값 변경 불가

***

## Why use Record as DTO?

### Pros

| 항목                 | 설명                          |
| ------------------ | --------------------------- |
| **불변성(Immutable)** | 생성 이후 값 변경 불가, 스레드 세이프      |
| **보일러플레이트 제거**     | 생성자, getter 등을 직접 작성할 필요 없음 |
| **간결성**            | 코드가 짧아지고 가독성 향상             |
| **타입 안전성**         | 필드가 final이므로 실수 방지          |
| **데이터 전송에 최적화**    | 계층 간 데이터 전달에 적합             |

특히 다음과 같은 계층 간 데이터 전송에 이상적이다:

* 컨트롤러 → 서비스 → 레포지토리
* JSON 직렬화/역직렬화 시 (Jackson과 호환성 좋음)

***

## **Should all Records be used as DTOs?**

**아니다.**

* Record는 단순한 **데이터 캡슐화 도구**일 뿐이다.
* 사용 목적에 따라 다음과 같이 다양하게 활용된다:
  * DTO (데이터 전송 객체)
  * VO (값 객체)
  * Event 객체
  * Command 객체

```java
// DTO로 사용
public record MemberDto(String name, String email, int age) {}

// VO로 사용
public record Coordinates(double x, double y) {}
```

\
Record는 어떻게 사용하느냐에 따라 DTO가 될 수도, VO가 될 수도 있다. \
나는 심지어 도메인 모델로도 사용한다

***

## Record vs Value Object (VO)&#x20;

| 항목       | Record       | Value Object (VO) |
| -------- | ------------ | ----------------- |
| 불변성      | 기본 제공        | VO도 불변성 요구        |
| 값 기반 동등성 | 필드값 기반 비교    | 필드값 기반 비교         |
| 목적       | 데이터 캡슐화 및 전달 | 도메인 내 특정 개념 표현    |
| 비즈니스 로직  | 거의 없음        | 간단한 도메인 로직 포함 가능  |
| 설계 관점    | 간단한 구현 중심    | 도메인 주도 설계(DDD) 중심 |

> **VO**는 단순 값 보관을 넘어, 도메인 개념을 표현하고 의미 있는 행위를 포함할 수 있다.\
> 반면 **Record**는 기본적으로 데이터 통(Data Holder) 역할에 집중한다.

***

## Limit Of Record

| 항목                  | 설명                              |
| ------------------- | ------------------------------- |
| **상속 불가**           | 다른 클래스를 상속할 수 없다 (`extends` 불가) |
| **필드 변경 불가**        | 생성 이후 필드 값을 수정할 수 없다            |
| **구조 제약**           | 모든 필드는 생성자에서 초기화해야 한다           |
| **복잡한 비즈니스 로직 부적합** | 복잡한 상태 관리나 로직에 적합하지 않다          |
| **버전 제한**           | Java 16 이상 버전 필요                |
| **복잡한 초기화 시 불편**    | 복잡한 생성 로직이 필요한 경우 커스텀 생성자 작성 필요 |

특히,

* **확장성(상속, 다형성)** 이 필요한 상황에서는 Record 사용이 제한적이다.
* 복잡한 도메인 모델에는 일반 클래스를 사용하는 것이 더 적합할 수 있다.

***

## Practice (CQRS)

Record는 **Command-Query Responsibility Segregation (CQRS)** 패턴에서도 매우 유용하다.

* **Command Record**: 변경 요청에 대한 입력 데이터 캡슐화
* **Query Record**: 조회 결과를 캡슐화하여 반환

#### 예시

```java
// Command 용 Record
public record CreateUserCommand(String name, String email) {}

// Query 용 Record
public record UserDetailResponse(String name, String email, int age) {}
```

이렇게 사용하면 역할이 명확하게 분리되어 코드 가독성 및 유지보수성이 높아진다.

***

## Record Serializing/Desrializing

Record는 기본적으로 **Jackson** 같은 JSON 라이브러리와 호환된다.\
그러나 몇 가지 주의할 점이 있다:

1. **기본 생성자가 없다**
   * Jackson은 기본적으로 빈 생성자(default constructor)와 setter를 이용해 객체를 생성한다.
   * Record는 생성자가 하나뿐이고 모든 필드를 반드시 받아야 하므로, Jackson이 자동으로 생성자 기반 바인딩을 해야 한다.
2. **필드 이름이 JSON key와 정확히 일치해야 한다**
   * JSON의 키 값과 Record의 필드명이 다르면 매핑에 실패할 수 있다.
3. **라이브러리 버전 호환성**
   * Jackson 2.12 이상부터 Record를 공식 지원한다.
   * 구버전 Jackson을 사용할 경우 직렬화/역직렬화가 실패할 수 있다.

#### 해결 방법

* gradle 에 다음 의존성을 추가해 최신 Jackson 사용 보장하기:

```gradle
implementation 'com.fasterxml.jackson.core:jackson-databind:2.13.0'
```

* 필요한 경우 `@JsonProperty`를 명시적으로 설정하여 필드명을 매핑할 수 있다:

```java
public record MemberDto(
    @JsonProperty("name") String name,
    @JsonProperty("email") String email,
    @JsonProperty("age") int age
) {}
```

***

## Conclusion

* Record는 **빠르고 안전하게 불변 데이터 객체**를 만들 수 있는 도구다.
* DTO, VO, Command, Query 객체로 매우 유용하게 사용될 수 있다.
* 단순한 데이터 캡슐화가 필요한 경우 최적이지만,
* 복잡한 비즈니스 로직이 필요한 경우에는 일반 클래스를 사용하는 것이 더 적합하다.
