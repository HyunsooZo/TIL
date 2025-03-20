---
description: 프로그래밍에서의 일급객체 / 일급 클래스에 대해 알아보았다.
---

# 🥇 Java : First-Class Citizen & Collection

## **First-Class Citizen?**

일급 객체란 프로그래밍 언어에서 **다음 조건을 만족하는 객체**를 의미한다.

1. 변수나 데이터 구조(컬렉션)에 저장할 수 있다.
2. 함수의 **인자로 전달할 수 있다.**
3. 함수의 **반환값으로 사용할 수 있다.**
4. 실행 중에 동적으로 생성할 수 있다.

자바에서는 기본적으로 객체(클래스의 인스턴스)들이 일급 객체에 해당하며,\
특히 **람다 표현식**을 통해 함수도 일급 객체로 다룰 수 있다.

### Practice

```java
import java.util.function.Function;

public class FirstClassFunction {
    public static void main(String[] args) {
        // 1. 함수를 변수에 저장할 수 있음
        Function<Integer, Integer> square = x -> x * x;

        // 2. 함수를 다른 함수의 인자로 전달 가능
        printResult(5, square);

        // 3. 함수를 반환할 수 있음
        Function<Integer, Integer> increment = getIncrementFunction();
        System.out.println(increment.apply(10)); // 11
    }

    public static void printResult(int x, Function<Integer, Integer> func) {
        System.out.println(func.apply(x));
    }

    public static Function<Integer, Integer> getIncrementFunction() {
        return x -> x + 1;
    }
}
```

### Pros & Cons

#### **Pros**

1. **코드 재사용성 증가**
   * 함수를 변수처럼 다룰 수 있어 **중복 코드를 줄이고 유지보수성을 높일 수 있다.**
   * 예를 들어, `Function<T, R>` 인터페이스를 활용하면, 함수 로직을 쉽게 재사용할 수 있다.
2. **유연한 설계 가능**
   * 함수형 프로그래밍 스타일을 적용할 수 있어 **전략 패턴(Strategy Pattern) 같은** \
     **디자인 패턴을 더 쉽게 구현 가능**하다.
   * `Comparator` 같은 인터페이스를 활용하면, 정렬 로직을 동적으로 변경할 수 있다.
3. **코드 가독성 향상**
   * 메서드 참조나 람다 표현식을 활용하면 가독성이 높아진다.
   * `list.forEach(System.out::println);` 같은 코드가 가능하다.

#### **Cons**

1. **디버깅이 어려울 수 있음**
   * 람다 표현식이나 함수형 인터페이스를 사용하면 \
     **스택 트레이스(Stack Trace)가 단순화되어 디버깅이 어려울 수 있다.**
   * 익명 클래스보다 직관적인 오류 메시지가 부족할 수도 있다.
2. **메모리 관리 부담 증가**
   * 불필요한 람다 객체가 생성될 경우 **GC(Garbage Collector)의 부담이 커질 수 있다.**
   * 불필요한 클로저(Closure)로 인해 참조가 유지될 가능성이 있다.
3. **객체지향 패러다임과 혼합 시 혼란 발생 가능**
   * 객체지향 프로그래밍(OOP)과 함수형 프로그래밍(FP)을 혼용할 경우 **일관성이 떨어질 수 있다.**
   * 특히, 객체 상태를 변경하는 로직과 함수형 스타일을 섞으면 유지보수가 어려워질 수 있다.

***

## **First-Class Collection?**

일급 컬렉션이란 **컬렉션을 Wrapping하여 단일 클래스로 관리하는 패턴**을 말한다.\
즉, 컬렉션(List, Set, Map 등)을 직접 노출하지 않고 **객체로 감싸서 관리하는 것**이 핵심이다.

**왜 First-Class Collection을 사용할까?**

* **비즈니스 로직이 컬렉션 외부에 흩어지는 문제 방지**
* **컬렉션과 관련된 검증 로직을 한 곳에서 관리 가능**
* **객체 지향적인 설계를 유지**

### **Practice**

```java
import java.util.Collections;
import java.util.List;
import java.util.Objects;

class Names {
    private final List<String> names;

    public Names(List<String> names) {
        validate(names);
        this.names = Collections.unmodifiableList(names);
    }

    private void validate(List<String> names) {
        Objects.requireNonNull(names, "이름 리스트는 null일 수 없습니다.");
        if (names.isEmpty()) {
            throw new IllegalArgumentException("이름 리스트는 비어 있을 수 없습니다.");
        }
    }

    public List<String> getNames() {
        return names;
    }

    public int size() {
        return names.size();
    }
}

public class FirstClassCollectionExample {
    public static void main(String[] args) {
        Names names = new Names(List.of("Alice", "Bob", "Charlie"));
        System.out.println(names.getNames()); // [Alice, Bob, Charlie]
        System.out.println("Size: " + names.size()); // Size: 3
    }
}
```

### **Pros & Cons**

**Pros**

1. **컬렉션 관련 비즈니스 로직을 한 곳에서 관리 가능**
   * 검증 로직, 필터링, 정렬 등의 로직을 **객체 내부에서 캡슐화**할 수 있어 코드가 깔끔해진다.
   * 예를 들어, `Names` 클래스 내부에서 `validate()`를 수행하여 유효성을 보장할 수 있다.
2. **불변성 유지 가능**
   * 컬렉션을 `Collections.unmodifiableList()`로 감싸면 **외부에서 변경할 수 없도록 보호**할 수 있다.
   * 이는 멀티스레드 환경에서 **데이터 일관성을 유지하는 데 도움을 준다.**
3. **응집도가 높은 객체 설계 가능**
   * 컬렉션을 단순한 자료구조가 아니라 **객체로 다루므로, 데이터와 로직을 함께 관리**할 수 있다.
   * 결과적으로 **객체지향적인 설계를 유지할 수 있다.**

**Cons**

1. **코드가 다소 복잡해질 수 있음**
   * 단순한 `List<String>`을 사용하는 것보다 클래스를 따로 정의해야 하므로 **코드가 길어질 수 있다.**
   * 작은 프로젝트에서는 오버 엔지니어링(over-engineering)이 될 가능성이 있다.
2. **기본 컬렉션 API를 직접 사용하기 어려움**
   * `List<String>`을 바로 사용할 수 없고, 감싸진 객체의 메서드를 통해 접근해야 하므로,\
     기존 컬렉션 API를 바로 활용하기 어려울 수 있다.
3. **필요한 메서드를 추가해야 하는 부담**
   * `List.size()`, `List.contains()` 같은 기본 기능도 별도로 메서드를 추가해야 한다.
   * 컬렉션 조작이 많아질수록 해당 클래스를 계속 확장해야 하는 부담이 생긴다.

***

## **Summary**

<table><thead><tr><th width="123.10546875">개념</th><th width="208.25">설명</th><th width="194.3125">장점</th><th>단점</th></tr></thead><tbody><tr><td><strong>First-Class Citizen</strong></td><td>객체를 변수에 저장, 인자로 전달, 반환 가능</td><td>코드 재사용성 증가<br>유연한 설계 가능<br>코드 가독성 향상</td><td>디버깅 어려움<br>메모리 부담 증가<br>OOP와 혼합 시 혼란</td></tr><tr><td><strong>First-Class Collection</strong></td><td>컬렉션을 직접 노출하지 않고 객체로 관리</td><td>비즈니스 로직 응집 가능<br>불변성 유지 가능<br>객체지향 설계 강화</td><td>코드 길어짐<br>기본 컬렉션 API 활용 어려움<br>필요한 메서드 추가 부담</td></tr></tbody></table>

