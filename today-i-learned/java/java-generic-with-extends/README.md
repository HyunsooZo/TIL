# 🐸 Java : Generic With Extends

## Why Use `extends` in Generics

Java에서 제네릭은 코드의 **타입 안정성**과 **재사용성**을 높이는 데 중요한 역할을 한다. \
그중에서도 `extends`를 활용한 제네릭은 타입에 제약을 걸어 더욱 안전하고 \
효율적인 코드를 작성할 수 있게 한다. \
이 글에서는 일반 제네릭과 비교하여 `extends` 제네릭의 필요성과 이점에 대해 알아본다.

***

### Bounded Type Parameters

`extends`는 제네릭 타입에 특정 클래스나 인터페이스를 상속 또는 구현하도록 제한을 설정할 수 있다. \
이를 통해 해당 타입의 메서드나 속성을 컴파일 타임에 안전하게 보장할 수 있다.

#### 예제:

```java
class Printer<T extends Number> {
    private T value;

    public Printer(T value) {
        this.value = value;
    }

    public void printDouble() {
        System.out.println(value.doubleValue());
    }
}

public class Main {
    public static void main(String[] args) {
        Printer<Integer> intPrinter = new Printer<>(10);
        intPrinter.printDouble(); // 출력: 10.0

        // Printer<String> strPrinter = new Printer<>("Hello"); // 컴파일 오류
    }
}
```

* `T extends Number`를 통해 `Number` 클래스의 서브타입만 사용할 수 있다.
* 이를 통해 `Number` 클래스의 메서드 (`doubleValue()` 등)를 안전하게 호출할 수 있다.
* `String`과 같이 `Number`를 상속하지 않은 타입은 컴파일 단계에서 바로 차단된다.

***

### Ensuring Code Safety

제네릭 타입을 제한하지 않으면, 예상치 못한 타입이 들어와 런타임 오류가 발생할 가능성이 있다. \
하지만 `extends`로 타입을 제한하면 이러한 오류를 사전에 방지할 수 있다.

#### 제한 없는 제네릭의 문제:

```java
class Box<T> {
    public void doSomething(T item) {
        // T의 타입에 따라 여기에 오류 발생 가능
        // 예: item.length(); // String 타입에서만 가능
    }
}
```

#### `extends` 사용

```java
class Box<T extends CharSequence> {
    public void printLength(T item) {
        System.out.println(item.length()); // CharSequence의 메서드 사용 가능
    }
}

Box<String> box = new Box<>();
box.printLength("Hello"); // 출력: 5
```

* `T extends CharSequence`를 통해 `CharSequence`를 상속한 타입만 허용.
* 이를 통해 `length()`와 같은 메서드를 타입 안정적으로 호출 가능.

***

### Compatibility with Wildcards

제네릭 메서드나 컬렉션을 사용할 때, \
`? extends`를 사용하면 상위 클래스에 대해 읽기 전용으로 데이터를 처리할 수 있다.

```java
public void printList(List<? extends Number> list) {
    for (Number num : list) {
        System.out.println(num);
    }
}
```

* `List<? extends Number>`는 `List<Integer>`, `List<Double>` 등 다양한 `Number`의 하위 타입을 처리할 수 있다.
* 읽기 전용으로 데이터 접근을 허용하며, 컬렉션 수정은 불가능하다. 이를 통해 타입 안정성을 추가로 보장한다.

***

### Utilizing Common Methods

`extends`를 사용하면 특정 타입이 공통적으로 가지고 있는 메서드나 속성에 안전하게 접근할 수 있다.

```java
class Shape {
    public void draw() {
        System.out.println("Drawing shape");
    }
}

class Circle extends Shape {
    @Override
    public void draw() {
        System.out.println("Drawing circle");
    }
}

public class Main {
    public static void drawShapes(List<? extends Shape> shapes) {
        for (Shape shape : shapes) {
            shape.draw();
        }
    }

    public static void main(String[] args) {
        List<Circle> circles = List.of(new Circle());
        drawShapes(circles); // 출력: Drawing circle
    }
}
```

* `List<? extends Shape>`는 `Shape`의 모든 하위 타입을 처리할 수 있다.
* `Shape` 클래스의 `draw()` 메서드를 안전하게 호출할 수 있다.

***

### Code Reusability and Flexibility

`extends`를 사용하면 다양한 하위 타입을 유연하게 처리할 수 있으면서도, 필요 시 공통 메서드나 속성을 활용하여 중복 코드를 줄일 수 있다.

***

### Conclusion

`extends`를 활용한 제네릭은 아래와 같은 장점을 제공한다:

1. **타입 안정성**: 잘못된 타입이 전달되는 것을 방지한다.
2. **코드 재사용성**: 다양한 타입을 처리하면서 공통된 로직을 재사용할 수 있다.
3. **유연성**: 와일드카드(`? extends`)와 함께 사용하여 다양한 데이터 타입을 다룰 수 있다.

따라서 특정 타입의 제약이 필요한 경우, 일반적인 제네릭 대신 `extends`를 활용하는 것이 더욱 안전하고 효율적이다.
