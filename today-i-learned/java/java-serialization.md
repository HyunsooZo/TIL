---
description: 직렬화와 역직렬화, 그리고 현대 자바 프로그래밍에서는 기본 직렬화를 잘 사용하지 않는 이유에 대해 알아보았다.
---

# 🤹‍♂️ Java : Serialization

## **Serialization?**

직렬화란 객체 데이터를 바이트 형태로 변환하여 저장하거나 네트워크를 통해 전송할 수 있도록 하는 과정이다. \
이를 통해 Java 객체를 파일이나 소켓에 저장하거나, 다른 시스템으로 전송할 수 있다. \
직렬화된 데이터는 동일한 구조를 유지하며, Java가 아닌 시스템에서도 활용될 수 있다.

***

## **Deserialization?**

역직렬화란 직렬화된 바이트 데이터를 다시 객체로 복원하는 과정이다. \
역직렬화를 통해 저장된 데이터를 객체 형태로 불러와 프로그램에서 활용할 수 있다. \
이 과정은 직렬화와 반대 방향으로 동작하며, 데이터의 상태를 유지한다.

***

## **Flow**

<figure><img src="../../.gitbook/assets/스크린샷 2025-01-11 오후 10.56.06.png" alt=""><figcaption></figcaption></figure>

***

## **Serializable vs Externalizable**

Java는 직렬화를 위해 두 가지 주요 인터페이스를 제공한다(`Serializable`/ `Externalizable)` \
두 인터페이스는 객체를 바이트 형태로 변환하는 기본 구조를 제공하지만, 동작 방식과 유연성에서 차이가 있다.

* **Serializable**: 자동 직렬화를 지원하며, 개발자가 직접 제어할 수 있는 범위가 제한적이다.
* **Externalizable**: 개발자가 직렬화/역직렬화 과정을 직접 구현할 수 있어 더 유연하다.

***

## **Example**

### **Serializable**

```java
import java.io.*;

// Serializable 구현 클래스
class Person implements Serializable {
    private static final long serialVersionUID = 1L; // serialVersionUID 설정
    private String name;
    private int age;
    private transient String password; // 직렬화 제외

    public Person(
        String name, 
        int age, 
        String password
    ) {
        this.name = name;
        this.age = age;
        this.password = password;
    }

    @Override
    public String toString() {
        return "Person{" +
                    name='" + name + "', 
                    age=" + age + ", 
                    password='" + password + 
                "'}";
    }
}

public class SerializationExample {
    public static void main(String[] args) {
        Person person = new Person("Alice", 30, "mypassword");

        // 직렬화
        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("person.ser"))){
            oos.writeObject(person);
        } catch (IOException e) {
            e.printStackTrace();
        }

        // 역직렬화
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream("person.ser"))) {
            Person deserializedPerson = (Person) ois.readObject();
            System.out.println("Deserialized: " + deserializedPerson);
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

**Result:**

```arduino
Deserialized: Person{name='Alice', age=30, password='null'}
```

***

### **Externalizable**&#x20;

```java
import java.io.*;

// Externalizable 구현 클래스
class CustomPerson implements Externalizable {
    private String name;
    private int age;

    public CustomPerson() {}

    public CustomPerson(
        String name, 
        int age
    ) {
        this.name = name;
        this.age = age;
    }

    @Override
    public void writeExternal(ObjectOutput out) throws IOException {
        out.writeObject(name);
        out.writeInt(age);
    }

    @Override
    public void readExternal(ObjectInput in) throws IOException, ClassNotFoundException {
        name = (String) in.readObject();
        age = in.readInt();
    }

    @Override
    public String toString() {
        return "CustomPerson{name='" + name + "', age=" + age + "}";
    }
}

public class ExternalizableExample {
    public static void main(String[] args) {
        CustomPerson person = new CustomPerson("Bob", 40);

        // 직렬화
        try (ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("custom_person.ser"))) {
            oos.writeObject(person);
        } catch (IOException e) {
            e.printStackTrace();
        }

        // 역직렬화
        try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream("custom_person.ser"))) {
            CustomPerson deserializedPerson = (CustomPerson) ois.readObject();
            System.out.println("Deserialized: " + deserializedPerson);
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

**Result**

```vbnet
Deserialized: CustomPerson{name='Bob', age=40}
```

***

## **Why Serialization is Rarely Used Today**

현대 Java 개발에서는 기본 직렬화를 잘 사용하지 않는다. 이는 다음과 같은 문제점 때문이다.

* 기본 직렬화는 **데이터 크기가 크고 느리다**. 특히 **대규모 데이터를 처리하는 데 적합하지 않다**.
* 클래스 **구조가 변경**되면 **이전 직렬화 데이터와 호환되지 않아** `serialVersionUID`를 관리해야 한다.
* 역직렬화 과정에서 **악의적인 데이터를 주입할 수 있는 취약점**이 존재한다.
* 기본 직렬화는 모든 필드를 자동으로 처리하므로 **커스텀 제어가 어렵다.**

***

## **Alternatives to Java Serialization**

현대 개발에서는 JSON, XML, Protobuf와 같은 대체 기술들이 선호된다. \
이들은 플랫폼 독립적이며, 더 효율적이고 안전하다.\
그 중에서도 **JSON** 은 그 **범용성**, **가독성**, **유지보수성** 덕분에 현대 개발에서 \
**직렬화/역직렬화의 "표준"으로 자리 잡았다.**\
**Protobuf**, **Kryo**, **Custom Serialization** 같은 기술들은 여전히 특수한 도메인에서 사용된다고는 하는데\
대부분의 애플리케이션에서는 **JSON**이 가장 적합한 선택이다.

* **JSON**: 사람이 읽기 쉬운 데이터 포맷. **Jackson**이나 **Gson**과 같은 라이브러리를 통해 쉽게 활용할 수 있다.
* **Protobuf**: Google에서 개발한 이진 직렬화 포맷으로, 빠르고 데이터 크기가 작다.
* **Kryo**: Java 객체를 효율적으로 직렬화하며, 성능 면에서 뛰어나다.
* **Custom Serialization**: 필요에 따라 특정 필드만 직렬화하거나 커스텀 로직을 구현하는 방식이다.
