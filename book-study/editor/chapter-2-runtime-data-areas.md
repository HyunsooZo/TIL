# 2️⃣ Chapter 2 : Runtime Data Areas

* Runtime Data Areas의 구조
  * Runtime Data Areas는 Process로서 JVM이 OS로 부터 할당 받는 메모리 영역
  * WAS 사용시 빈번한 성능문제가 발생하는 영역이기도 함
  * Runtime Data Area는 목적에 따라 5개 영역으로 나뉨.

<figure><img src="../../.gitbook/assets/image (2) (1).png" alt=""><figcaption></figcaption></figure>

* 이 중에서 PC Register와 두개의 Stack 영역은 각 Thread 별로 생성되고 Method Area와 Heap은 모든 Thread에 공유됨.
*   **PC Register (a.k.a Program Counter)**

    * Java는 Register-base 가 아닌 Stack-Base로 작동함
    * JVM은 CPU에 직접 지시하지 않고 Stack-Operand를 뽑아 이를 별도의 메모리 공간에 저장하는 방식을 취함. 이러한 메모리 공간을 PC Register 라고 함.

    <figure><img src="../../.gitbook/assets/image (1) (1) (1) (1) (1).png" alt=""><figcaption></figcaption></figure>
* PC Register는 각 스레드 마다 하나씩 존재하고 생성 시점도 같음
* Native Pointer 와 Return Address를 가지고 있음.
* Java Method를 수행할때 해당 수행 Instruction 주소를 포함함
*   Java Virtual Machine Stacks

    * Thread의 수행정보를 기록하는 Frame을 저장하는 메모리 영역.
    * 스레드 별로 하나씩 존재하며 스레드 시작시 생성
    * 모든 데이터는 각 스레드가 소유하며 다른 스레드에서 접근 불가
    * JVM은 Stack Frame을 JVM Stacks에 단지 넣고 뺴는 작업만 수행
    * Thread가 Java Method를 하나 수행하면
      1. JVM은 Stack Frame을 하나 생성해 JVM stacks에 push
      2. 해당 frame은 current frame이 되며
      3. 해당 메서드가 수행을 마치면 pop되고
      4. 바로 이전의 frame(stack frame)이 current frame이 됨
    * Stack Frame 란?
      * 스레드가 수행하는 애플리케이션을 메서드 단위로 기록하는 곳.
      * 메서드를 실행하면 클래스의 메타정보를 이용하여 생성
      * 가변이 아니며 컴파일시점에 이미 결정
    * Local Variable Section
      * Method의 파라미터 변수와 로컬 변수들을 저장.
      * Array로 구성되어있고 인덱스를 통해 데이터 접근
      * 선언된 순서로 인덱스가 할당되며 compliler 할당
    * Operand Stack



    <figure><img src="../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>

    * JVM의 작업공간과도 같은 곳.
      * JVM이 프로그램을 실행하며 연산을 위해 사용되는 데이터 및 그 결과를 Operand Stack에 넣고 처리함.
        1. 하나의 Instruction이 Operand Stack에 값을 push
        2. 다음 instruction에서는 값을 pop하여 사용
        3. 해당 값들로 연산이 이루어진다면 위 결과를 다시 push
      * Array로 구성되어있으나 인덱스를 사용해 처리하지는 않음.
      * Frame Data
        * Constant Pool Resolution 데이터 Method 정상/비정상 데이터 저장
        * Class의 모든 Symbolic Reference는 Method Are의 Constant Pool에 저장됨
        * Constant Pool Resolution 은 곧 Constant Pool의 Pointer 정보이고 이 Point를 이용해 필요할 때마다 Constant Pool 조회
        * Java의 Reference는 Symbolic Reference 이므로 Class나 Method, 변수, 상수 접근 시 이러한 Resolution이 수행됨. 또 특정 Object가 특정 Class/Interface에 의존관계가 있는지 확인하기 위해서도 참조
        * Frame Data에는 자신을 호출한 Stack Frame의 Instruction Pointer가 들어있음. Method가 종료되면 JVM은 이 정보를 PD Register에 설정하고 Stack Frame을 빠져나감 만약 해당 Method의 반환값이 있다면 해당 값을 다음 Current Frame(자신을 호출한 Method의 Stack Frame의 Operand Stack에 푸쉬)
        * 메서드가 Exception을 던진경우에는 Exception 핸들링 필요
        * 따라서 이러한 Exception데이터도 Frame Data에 저장 (Exception Table의 Reference)
        * 모든 Class File은 Exception Table을 가지고 있는데, Exception발생 시 JVM은 이를 참조해 catch절에 해당하는 Bytecode로 점프
      * Native Method Stacks

    <figure><img src="../../.gitbook/assets/image (4) (1).png" alt=""><figcaption></figcaption></figure>

    ![](https://prod-files-secure.s3.us-west-2.amazonaws.com/27be91b2-add3-4771-858a-64a6e8561d41/add41cd9-33d9-4fae-a1e5-c1c23b629b0d/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2024-07-21_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_2.46.45.png)

    * Java는 타 언어로 작성된 프로그램 /API 툴킷 등과의 통합을 위해 JNI(Java Native Interface) 표준규약을 제공한다.
    * JNI는 Native Code로 되어있는 Function의 호출을 Java 프로그램 내에서도 직접 수행 할 수도, 결과값을 받을 수도 있음.
      1. Application에서 Native Method를 호출
      2. 이를 호출한 Stack Frame이 있는 Java Virtual Machine Stacks는 그냥 남겨두고 Native Function을 계속 수행
      3. Native Function의 수행이 끝나면 다시 Natice Method Stacks에서 나와 해당 스레드의 JVM Stacks로 돌아온다
      4. 새로운 Stack Frame을 하나 생성에 여기서 다시 작업을 수행
