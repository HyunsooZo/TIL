# 🤥 Java : new String("\*") == "\*"?

## Concept

자바에서 문자열 리터럴(`"*"`)은 JVM의 **String Pool**에 저장된다. \
이는 **같은 문자열을 여러 번 생성하더라도 메모리를 절약하고 비교 연산 속도를 빠르게 하기 위함**이다.

하지만 `new String("*")`처럼 생성자를 이용하면 **무조건 새로운 객체**가 Heap 메모리에 생성된다. \
따라서 문자열을 다룰 때는 이런 동작 방식을 반드시 이해하고 있어야 한다.

***

### Code

```java
String a = "*";
String b = new String("*");

System.out.println(a == b);         // false
System.out.println(a.equals(b));    // true
```

### Explanation

* `a == b`: false
  * `a`는 String Pool 안의 리터럴 "\*"를 가리킴
  * `b`는 Heap에 새로 만들어진 "\*" 객체를 가리킴
  * **참조(reference)가 다르기 때문에 false**
* `a.equals(b)`: true
  * 값 자체는 같기 때문에 true

***

### intern()&#x20;

```java
String a = "*";
String b = new String("*");
String c = b.intern();

System.out.println(a == c); // true
```

* `intern()`은 해당 문자열을 String Pool에 등록하고, Pool에 있는 참조를 반환한다.
* 이미 존재하는 경우엔 기존 참조만 반환한다.

#### 요약

* `new String(...)`: 항상 새로운 객체 생성 (Heap)
* `intern()`: String Pool에서 동일한 문자열의 참조 반환

***

### Compile Time Constant Pooling

```java
String a = "ab" + "cd";   // 컴파일 타임에 "abcd"로 합쳐짐
String b = "abcd";
System.out.println(a == b); // true
```

* 상수 문자열은 컴파일 시점에 미리 합쳐져 String Pool에 저장된다.
* 따라서 `a == b`는 true가 된다.

#### 하지만!

```java
String x = "ab";
String y = x + "cd";   // 런타임에서 문자열이 결합됨
String z = "abcd";
System.out.println(y == z); // false
```

* 변수로 문자열을 조합하면 **런타임에 새로운 String 객체가 만들어짐**
* 그래서 `==`은 false, `equals()`는 true

***

## &#x20;StringBuilder / StringBuffer

문자열을 자주 결합해야 할 때는 `+` 연산보다 `StringBuilder`를 사용하는 것이 효율적이다.

```java
StringBuilder sb = new StringBuilder();
sb.append("hello").append(" ").append("world");
String result = sb.toString();
```

* `+` 연산은 내부적으로 `StringBuilder`로 변환되지만,
* **루프 안에서 문자열 결합 시엔 명시적으로 사용하는 게 성능 면에서 낫다.**

***

## String is Immutable!

```java
String a = "*";
a.concat(" ^");
System.out.println(a); 
```

* `String`은 변경 불가능한 객체이다.
* `concat()`이나 `replace()` 같은 메서드를 사용하면 **새로운 문자열 객체가 생성된다.**
* 원본 문자열은 **절대 변하지 않는다.**

***

## equals vs ==&#x20;

| 비교 방식      | 의미    | 사용 대상      |
| ---------- | ----- | ---------- |
| `==`       | 참조 비교 | 주소가 같은지 비교 |
| `equals()` | 값 비교  | 문자열 내용 비교  |

#### 결론

* **문자열 값 비교는 무조건 equals() 사용하자.**
* `==`은 같은 Pool 참조일 때만 true

***

## Tips For Practice

* 문자열을 비교할 땐 항상 `equals()` 사용 (혹은 `Objects.equals(a, b)`)
* 다량의 문자열을 다룰 땐 `intern()`이나 `String.intern()` 활용을 고려 (주의해서)
* 반복적인 문자열 결합에는 `StringBuilder` 또는 `StringBuffer` 사용
* 문자열 리터럴은 자동으로 String Pool에 저장되며, `new`로 만들면 안 됨

***

## Conclusion

* 문자열 리터럴은 String Pool에 저장되어 메모리 최적화와 빠른 비교가 가능하다.
* `new String()`은 Heap에 별도 객체를 생성하므로 피하는 것이 좋다.
* `intern()`을 이용하면 Heap 객체를 Pool 객체로 변환할 수 있다.
* 문자열 비교는 `equals()`를 사용하고, 성능 최적화를 위해선 StringBuilder, String Pool 활용 방법을 익혀두자.
