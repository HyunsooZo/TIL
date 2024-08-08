# 4️⃣ Chapter 4 : Class Loader

#### Class Loader

* **Java Class Loader**: Java 프로그램이 실행될 때 클래스 파일을 JVM으로 로드하는 메커니즘.
* **동적 로딩**: 실행 시간에 필요한 클래스를 로드하는 방식.
  * **Load Time Dynamic Loading**: 프로그램이 시작될 때 모든 필요한 클래스를 로드.
  * **Runtime Dynamic Loading**: 프로그램 실행 중에 필요한 클래스를 로드.

#### Class Loader의 동작 방식

* **JVM 내의 Class Loader**: JVM은 여러 개의 클래스 로더를 가지고 있으며, 각 클래스 로더는 특정 네임스페이스에서 클래스를 로드.

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>



* **Namespace**: 클래스 로더는 각기 다른 네임스페이스를 가지고 있으며, 동일한 이름의 클래스를 로드해도 다른 클래스로 인식.
* **Fully Qualified Name**: 클래스의 완전한 이름으로 로더 이름 + 패키지 이름 + 클래스 이름으로 구성.

#### Dynamic Loading

* **Runtime Dynamic Loading**: 소스 코드에서 특정 클래스를 호출할 때 동적으로 로드.
  * `Class.forName(args[0])`과 같은 메서드를 사용해 동적으로 클래스를 로드하고 인스턴스를 생성.

#### Class Loader Delegation Model

* **계층 구조**: JVM에는 여러 계층의 클래스 로더가 있으며, 상위 로더가 먼저 클래스를 로드 시도하고 없으면 하위 로더가 로드.

<figure><img src="../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

* **Bootstrap Class Loader**: 최상위 클래스 로더, 기본 Java API를 로드.
* **Extension Class Loader**: JVM 확장 기능을 로드.
* **System Class Loader**: 애플리케이션 클래스 패스를 로드.
* **User Defined Class Loader**: 사용자 정의 클래스 로더.

#### WAS(Web Application Server)의 Class Loader 구조

* **WAS Class Loader Tree**: 웹 애플리케이션 서버는 여러 계층의 클래스 로더 트리를 구성하여 효율적으로 클래스를 로드.
  * **Shared Class Loader**: 여러 모듈이 공유하는 클래스 로더.
  * **Isolated Class Loader**: 각 애플리케이션이 독립적으로 사용하는 클래스 로더.

#### Class Sharing

<figure><img src="../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

* **Java 5에서 추가**: 여러 JVM 프로세스 사이에서 클래스를 공유하여 메모리 사용 효율을 높임.
* **Class Cache**: JVM이 클래스를 로드한 후 이를 캐시에 저장하여 다음 사용 시 빠르게 접근.
* **Shared Class**: JVM 간에 클래스를 공유하여 중복 로딩을 방지.

#### Class Loader 관련 Options

* JVM 옵션을 통해 클래스 로더의 동작을 설정할 수 있음:
  * `Xbootclasspath`: Bootstrap Class Loader 경로 설정.
  * `Djava.ext.dirs`: Extension Class Loader 경로 설정.
  * `cp`: System Class Loader 경로 설정.
  * `XX:+TraceClassLoading`: 클래스 로딩 과정을 추적하여 디버깅.
  * `XX:+ClassUnloading`: 사용되지 않는 클래스를 언로드.
  * `Xshare:off|auto|on`: 클래스 공유 기능 설정.

#### IBM JVM과 Hotspot JVM의 Class Sharing 옵션

* IBM JVM과 Hotspot JVM 모두 클래스 공유 기능을 제공하지만, 설정 방식과 세부 옵션이 다름:
  * **Hotspot JVM**:
    * `Xshare`: 클래스 공유 기능 설정 (off, auto, on).
    * `XX:+TraceClassLoading`: 클래스 로딩 과정 추적.
  * **IBM JVM**:
    * `Xscmx`: 공유 캐시 크기 설정.
    * `Xshareclasses`: 클래스 공유 및 캐시 설정.

***

#### Class Loader의 역할

* **Class Loader**는 JVM으로 클래스 파일을 로드하여 애플리케이션이 사용할 수 있도록 한다.

<figure><img src="../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

* 작업은 크게 세 가지로 나뉜다: **Loading, Linking, Initialization**.

#### Loading

* **Loading**은 바이너리 형태의 클래스를 JVM으로 가져오는 과정이다.
* 이 과정은 세 가지로 구성된다:
  * **Acquisition**: 네트워크를 통해 클래스 파일을 얻어온다.
  * **Parse**: 클래스와 메서드 영역에 데이터를 적절히 배치.
  * **Verification**: 클래스의 형식을 확인하여 JVM에서 사용할 수 있는지 검사.

#### Linking

* **Linking**은 로드된 클래스를 JVM의 런타임 환경에 맞게 연결하는 작업이다.
* Linking은 세 단계로 나뉜다:
  * **Verification**: 클래스 파일의 정확성과 일관성을 검사.
  * **Preparation**: 클래스의 정적 필드와 기본값을 할당.
  * **Resolution**: 심볼릭 레퍼런스를 실제 메모리 주소로 변환.

#### Initialization

* **Initialization**은 준비된 클래스를 초기화하는 단계로, 정적 필드를 설정하고, 필요한 경우 수퍼클래스 초기화를 포함한다.
* 클래스가 최초로 사용될 때 반드시 초기화가 완료되어야 한다.
* 이 과정에서는 클래스의 static initializer 및 생성자 호출이 포함된다.

#### Loading과 Linking 방식

* **Eager 방식**: JVM이 시작될 때 모든 관련 클래스를 즉시 로드하고 링크.
* **Lazy 방식**: 클래스가 실제로 필요할 때 로드하고 링크.

#### First Actual Use와 Implicit Loading

* **First Actual Use**: 클래스가 처음 실제로 참조될 때 로드 및 링크.
* **Implicit Loading**: 컴파일 타임에 클래스가 참조되는 경우, 자동으로 로드 및 링크.

#### Verification 세부사항

* 슈퍼클래스 존재 확인, 추상 클래스 구현 확인, 모든 메서드의 정의 확인 등.

#### Preparation 세부사항

* 정적 필드와 변수에 기본값 할당.

#### Resolution 세부사항

* Constant Pool 내의 심볼릭 레퍼런스를 실제 메모리 주소로 변환.

#### Initialization 세부사항

* 수퍼클래스 초기화 후 서브클래스 초기화.
* 동기화된 다수의 스레드가 있을 경우, 하나의 스레드만 초기화 작업을 수행.
