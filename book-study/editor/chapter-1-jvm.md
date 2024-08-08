# 1️⃣ Chapter 1 : JVM

<figure><img src="../../.gitbook/assets/스크린샷 2024-07-21 오후 2.13.0722.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/스크린샷 2024-07-21 오후 2.13.07.png" alt=""><figcaption></figcaption></figure>

* Java는 “Write Once, Run Everywhere” 라는 하나의 철학에 가까움
*   자바는 아래 네 가지 상호 연관된 기술을 엮어 놓았음

    * **The Java Programming Language**
      * **Dinamic Linking**
        * Class 파일은 실행 시 Link 를 위한 Symbolic Reference 만을 가지고있음.
        * Symbolic Reference 가 Runtime 시점에서 메모리 상 물리주소로 대체되는 작업이 Dynamic Linking.
        * 이를 통해 Class 파일 크기를 작게 유지할 수 있음
    * **The Java Class File Format**
      * **Class 파일의 네 가지 특징**
        * Class 파일은 ByteCode 를 Binary 형태로 담아놓은것.
        * Class 파일에는 실제 참조 라이브러리를 포함하지 않고 Symbolic Reference 만을 가지므로 작은 크기 유지 가능.
        * Nerwork Byte Order를 사용해 서로 다른 계열의 CPU끼리 데이터 전송 시의 문제점을 해결.
    * **The Java Application Programming interface(Java API)**



    <figure><img src="../../.gitbook/assets/image (6).png" alt=""><figcaption></figcaption></figure>

    * Java API는 Runtime Library의 집합이라고 볼수 있음. (JRE 등)
    * OS System ↔ Java Program 을 이어주는 역할 그야말로 API 역할
    * 이를 통해 OS 제약없는 Java Interface를 통한 실행 가능
    * **The Java Virtual Machine(JVM)**

    <figure><img src="../../.gitbook/assets/image (5).png" alt=""><figcaption></figcaption></figure>

    * JVM은 소프트웨어로써 존재하며 독자적 작동이 가능한 메커니즘 및 구조 가지고 있음.
    * JVM은 정의된 스펙들을 구현한 하나의 독자적인 Runtime Instance라 볼 수 있음.
      1. Class Loader System을 통해 Class파일들을 JVM으로 로딩
      2. 로딩된 Class 파일들은 Execution Engine을 통해 해석
      3. Runtime Data Areas에 배치해 실직적인 수행
      4. 해당 과정에서 스레드 동기화 / 가비지 컬렉션 등 수행
