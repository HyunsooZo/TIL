---
description: >-
  sealed 클래스는 자기 자신이 추상 클래스이고, 자신을 상속받는 여러 서브 클래스들을 가질 수 있다. 이를 사용하면 enum 클래스와
  달리 상속을 지원하기 때문에, 상속을 활용한 풍부한 동작을 구현할 수 있다.
---

# 🎫 Kotlin : sealed class

## **Sealed Class?**

Kotlin의 **Sealed Class**는 **클래스 계층 구조**를 안전하고 명확하게 정의할 수 있도록 도와주는 특수한 클래스이다.\
`sealed` 키워드를 사용하면 **상속 가능한 클래스의 종류를 제한**할 수 있어, \
컴파일 타임에 더 안전한 코드를 작성할 수 있다.

***

## **Why Use Sealed Class?**

**명확한 클래스 계층 구조**:

* 상속받을 수 있는 클래스가 정해져 있어, 클래스 관계를 명확히 정의할 수 있다.
* 상속받는 모든 클래스는 **동일한 파일**에 정의되어야 한다.

**컴파일 타임 안전성**:

* `when` 문에서 모든 하위 클래스를 처리하도록 강제하여 런타임 예외를 줄인다.

**가독성과 유지보수성 향상**:

* 명확한 설계와 제한된 계층 구조로 코드가 간결해진다.

***

## **How to Use Sealed Class**

### **Sealed Class 선언**

```kotlin
sealed class Shape
data class Circle(val radius: Double) : Shape()
data class Rectangle(val width: Double, val height: Double) : Shape()
object NoShape : Shape() // Singleton object
```

### **`when` 문과 Sealed Class**

`when` 문에서 Sealed Class를 사용할 때, 모든 하위 클래스를 처리해야 한다.

```kotlin
fun describeShape(shape: Shape): String = when (shape) {
    is Circle -> "Circle with radius ${shape.radius}"
    is Rectangle -> "Rectangle with width ${shape.width} and height ${shape.height}"
    NoShape -> "No shape defined"
    // 'else'가 필요 없음. 모든 하위 클래스가 처리되었기 때문.
}
```

***

## **Sealed Class vs Enum**

<table><thead><tr><th width="172">Feature</th><th>Sealed Class</th><th>Enum Class</th></tr></thead><tbody><tr><td><strong>상태 표현</strong></td><td>다양한 상태와 속성을 정의할 수 있음.</td><td>정해진 상태만 정의 가능.</td></tr><tr><td><strong>하위 클래스</strong></td><td>복잡한 데이터 구조 표현 가능.</td><td>단순한 값 기반 상태 표현에 적합.</td></tr><tr><td><strong>데이터 포함</strong></td><td>하위 클래스마다 고유 속성 정의 가능.</td><td>고유 속성 정의 불가.</td></tr><tr><td><strong><code>when</code> 처리 강제</strong></td><td>모든 하위 클래스 처리 강제.</td><td>모든 enum 값을 처리해야 함.</td></tr></tbody></table>

***

## **Sealed Interface**

Kotlin 1.5부터는 **Sealed Interface**도 지원된다.\
Sealed Class와 동일한 특성을 가지며, 인터페이스로도 계층 구조를 제한할 수 있다.

### **Example**

```kotlin
sealed interface Shape
data class Circle(val radius: Double) : Shape
data class Rectangle(val width: Double, val height: Double) : Shape
object NoShape : Shape
```

***

### **When to Use Sealed Class?**

* **다양한 상태를 표현해야 할 때**:\
  예를 들어, 네트워크 요청의 상태를 처리할 때 유용하다.
* **고정된 계층 구조가 필요할 때**:\
  예를 들어, 특정 이벤트를 처리하거나 상태 전환을 관리할 때.

```kotlin
sealed class NetworkResult<out T> {
    data class Success<out T>(val data: T) : NetworkResult<T>()
    data class Error(val message: String) : NetworkResult<Nothing>()
    object Loading : NetworkResult<Nothing>()
}

fun handleResult(result: NetworkResult<String>) {
    when (result) {
        is NetworkResult.Success -> println("Data: ${result.data}")
        is NetworkResult.Error -> println("Error: ${result.message}")
        NetworkResult.Loading -> println("Loading...")
    }
}
```

***

## **Limitations of Sealed Class**

1. **모든 하위 클래스는 동일한 파일에 있어야 함**:
   * 이는 설계 의도를 명확히 하지만, 대규모 프로젝트에서는 제한이 될 수 있다.
2. **계층 구조의 유연성 부족**:
   * 동적으로 하위 클래스를 추가하거나 외부에서 상속을 허용할 수 없다.

***

## **Conclusion**

Sealed Class는 Kotlin에서 안전하고 명확한 클래스 계층 구조를 설계하는 데 매우 유용한 도구이다. \
특히 상태 관리나 이벤트 처리와 같은 작업에서 코드의 안정성, 가독성, 유지보수성을 크게 향상시킨다. \
다만, 최신 Java에서는 Enum과 Switch가 매우 잘 호환되며 별로 불편함을 못느껴서인지, \
아직은 Enum이 더 편하게 느껴진다. \
Sealed Class와 Enum, 그리고 Open Class의 장단점을 비교해가며, \
상황에 따라 적절히 선택하는 것이 중요할 것이다.
