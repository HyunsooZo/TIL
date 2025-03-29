---
description: >-
  Java에서 Stream은 데이터의 흐름을 추상화한 개념이다. 파일, 네트워크, 메모리 등 다양한 소스로부터 데이터를 읽고 쓸 수 있게
  해준다. Java I/O는 크게 바이트 스트림과 문자 스트림으로 나뉘며, 각각 Input/OutputStream, Reader/Writer를
  통해 처리한다.
---

# ⌨️ Java : I/O Stream

## Byte Stream

바이트 스트림은 **1바이트 단위로 입출력**을 처리한다.\
주로 **이미지, 영상, 실행파일 등 바이너리 데이터**를 다룰 때 사용한다.

### Class

* `InputStream` (추상 클래스)
  * 데이터를 읽는 쪽
  * ex) `FileInputStream`, `BufferedInputStream`, `DataInputStream`
* `OutputStream` (추상 클래스)
  * 데이터를 쓰는 쪽
  * ex) `FileOutputStream`, `BufferedOutputStream`, `DataOutputStream`

### Practice

```java
import java.io.FileInputStream;
import java.io.FileOutputStream;
import java.io.IOException;

public class ByteStreamExample {
    public static void main(String[] args) {
        try (FileInputStream fis = new FileInputStream("source.jpg");
             FileOutputStream fos = new FileOutputStream("copy.jpg")) {

            byte[] buffer = new byte[1024];
            int length;

            while ((length = fis.read(buffer)) != -1) {
                fos.write(buffer, 0, length);
            }

            System.out.println("파일 복사 완료");

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

***

## Character Stream

문자 스트림은 **2바이트 유니코드 기반으로 문자 단위 입출력**을 처리한다.\
텍스트 파일 읽기/쓰기 등 **문자 데이터**를 다룰 때 유리하다.

### Class

* `Reader` (추상 클래스)
  * 문자를 읽는 쪽
  * ex) `FileReader`, `BufferedReader`, `InputStreamReader`
* `Writer` (추상 클래스)
  * 문자를 쓰는 쪽
  * ex) `FileWriter`, `BufferedWriter`, `OutputStreamWriter`

### Practice

```java
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;

public class CharacterStreamExample {
    public static void main(String[] args) {
        try (BufferedReader reader = new BufferedReader(new FileReader("sample.txt"))) {
            String line;

            while ((line = reader.readLine()) != null) {
                System.out.println(line);
            }

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

***

## Buffered Stream

버퍼를 사용하여 **입출력 성능을 향상**시키는 스트림이다.\
버퍼란 데이터를 모아두는 임시 저장소로, **여러 번의 IO를 줄이고 속도를 높여준다.**

### Practice

```java
import java.io.BufferedWriter;
import java.io.FileWriter;
import java.io.IOException;

public class BufferedWriterExample {
    public static void main(String[] args) {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter("output.txt"))) {
            writer.write("안녕하세요, 현수입니다.");
            writer.newLine();
            writer.write("스트림은 어렵지 않아요!");

        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

***

## Stream Structure

<figure><img src="../../../.gitbook/assets/스크린샷 2025-03-29 오후 11.29.03.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../../.gitbook/assets/스크린샷 2025-03-29 오후 11.29.11 (1).png" alt=""><figcaption></figcaption></figure>

***

## Summary

Java의 Stream은 다양한 데이터 소스와 목적에 맞게 **입출력을 유연하게 처리**할 수 있게 해준다.\
**바이트 스트림은 바이너리 데이터**, **문자 스트림은 텍스트 데이터**에 적합하며,\
**버퍼 스트림은 성능 최적화**에 효과적이다.

예제와 함께 직접 테스트해보면 개념이 훨씬 쉽게 잡힐 것이다.\
추후 NIO나 Stream API와의 차이도 나중에 정리해보겠다.
