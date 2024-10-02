---
description: 아무 메서드나 필드 없이 어떤 특성을 갖고 있음을 마킹하는 인터페이스
---

# ☝️ Marker Interface

### **Marker Interface**의 개념

**목적**: \
메서드나 필드를 정의하지 않고, 특정 클래스가 어떤 행동을 할 수 있음을 표시.

**용도**: \
런타임에 해당 클래스가 특정 인터페이스를 구현하고 있는지 확인함으로써, \
클래스에 대한 특정 동작을 수행한다.

```java
import java.io.Serializable;

public class BzhsClass implements Serializable {
    private String name;

    public MyClass(String name) {
        this.name = name;
    }
}
```

위 `bzhsClass`를 보게 되면 `Serializable` 인터페이스를 구현했지만, 그 인터페이스에 아무런 메서드도 정의되지 않았으며 이 클래스는 직렬화 할 수 있다는 의미만을 전달한다. \
또한 런타임 검증 (특정 인터페이스 구현하는지 체크) 도 가능하다.

### Marker Interface 예시

* `Serializable` : 객체 직렬화 가능함을 마킹
* `Cloneable` :  객체 복제 가능함을 마킹
* `Remote` :  객체 원격호출 가능함을 마킹&#x20;

### **Marker Interface VS Annotation**

최근에는 Marker Interface 대신 어노테이션을 사용하는 경우가 더 많아졌다고한다 \
어노테이션은 더 유연하고 메타데이터를 포함할 수 있기 때문에, \
더 많이 사용되는 추세이나, Marker Interface는 여전히 특정 상황에서 유용할 수 있다.\
실제로 회사에서 responseDTO 의 역직렬화 문제로 위 내용을 참고하여 사용해 보았다.

