# 🛡️ Java : Defensive Copy

## Defensive Copy?

방어적 복사(defensive copy)는 객체의 불변성을 보장하거나 외부로부터의 예상치 못한 변경을 막기 위해, \
객체나 컬렉션을 복사하여 사용하는 프로그래밍 기법이다. \
특히 Java에서는 컬렉션, 배열, Date 같은 mutable 객체를 리턴하거나 파라미터로 받을 때 자주 사용된다.

## Why is Defensive Copy Important?

방어적 복사를 사용하지 않으면 외부에서 객체의 내부 상태를 변경할 수 있다. \
이는 의도하지 않은 부작용을 초래할 수 있으며, 시스템의 안정성과 예측 가능성을 해친다.

## Common Use Cases

1. **Getter 메서드에서 방어적 복사**
2. **생성자나 setter에서 외부 입력에 대해 방어적 복사**

### &#x20;Without Defensive Copy

```java
public class Person {
    private Date birthDate;

    public Person(Date birthDate) {
        this.birthDate = birthDate; // 위험함!
    }

    public Date getBirthDate() {
        return birthDate; // 위험함!
    }
}
```

외부 코드에서 `getBirthDate()`로 받은 객체를 수정하면 `Person` 인스턴스 내부의 상태도 변한다.

### With Defensive Copy

```java
public class Person {
    private Date birthDate;

    public Person(Date birthDate) {
        this.birthDate = new Date(birthDate.getTime()); // 방어적 복사
    }

    public Date getBirthDate() {
        return new Date(birthDate.getTime()); // 방어적 복사
    }
}
```

이렇게 하면 외부에서 원본 객체에 접근하거나 수정할 수 없다.

## When to Use

* 클래스 내부 필드가 mutable한 객체인 경우 (예: 배열, `Date`, `List`, 커스텀 mutable 객체)
* 외부에서 객체를 전달받거나 반환할 때, 그 객체의 내부 상태 보호가 중요한 경우

## Conclusion

Java에서 방어적 복사는 **불변성 보장**, **사이드 이펙트 방지**, **객체 캡슐화 유지**를 위해 필수적인 패턴이다.\
특히 객체가 mutable하다면 방어적 복사를 통해 데이터 일관성을 유지할 수 있다.
