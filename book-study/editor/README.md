# 🏝️ Java Performance Fundamental

### 짧은 리뷰 (Overall , 챕터 별 간단 정리)

#### Overall

사내 북 스터디 도서로 읽기 시작한 책이다. \
개발공부를 하며 흐릿하게 알고있던 내용을 보다 선명하게 이해할 수 있게 설명해주는 책 인것 같다. \
Java의 핵심 개념과 JVM의 내부 구조에 대한 깊이 있는 이해를 제공하기때문에, \
Java 백엔드 엔지니어로서의 역량향상에 충분히 도움이 될 만 한 책이라고 생각된다.&#x20;

아쉽게도 스터디는 5장까지만 진행되었으며 이후 다른 책을 통해 스터디를 진행 할 예정이기 때문에 본 포스팅에서는 \
5장까지의 리뷰만을 작성할 예정이다. 나머지 장은 시간이 된다면 틈틈히 읽어 볼 생각이다.

현재 내 수준에서 가장 인상깊었다고 느낀 부분은 2장(Runtime Data Areas),3장(Garbage Collection(GC))장 \
이었는데, Runtime Data Areas에 대한 상세한 설명을 통해 메모리 관리와 성능 최적화의 중요성을 다시 한 번 복기 할 수 있었다. 특히, 스레드별 메모리 구조와 공유 메모리 영역에 대한 이해는 추후 실무에서 멀티스레드 환경에서의 성능 최적화와 안정성을 확보하는 데 직접적으로 적용할 수 있을 것 같다. 예를 들어, 스레드 풀 설정이나 메모리 할당 전략을 세울 때 이 지식을 활용할 수 있을 거라고 생각된다.

#### 1장&#x20;

**Java**의 "**Write Once, Run Everywhere**" 철학을 중심으로, \
자바 프로그래밍 언어의 특징부터 시작해, **동적 링크(Dynamic Linking)를 통한 효율적인 클래스 파일 관리**, \
**클래스 파일 포맷의 구조적 특징**, **Java API의 역할과 중요성**까지 다룬다. \
또한, JVM이 자바 애플리케이션을 어떻게 실행하는지에 대한 과정과 메커니즘을 명확히 설명하며, \
클래스 로딩, 실행 엔진, 런타임 데이터 영역의 역할을 통해 자바의 강력한 성능을 이해할 수 있는 장이었다.

#### 2장

JVM의 **Runtime Data Areas**를 상세히 설명하며, 성능 최적화의 핵심 영역을 다루고 있다. \
Runtime Data Areas는 JVM이 OS로부터 할당받은 메모리 영역으로, \
PC Register, Java Virtual Machine Stacks, Native Method Stacks, Method Area, Heap으로 구성된다. \
각 스레드에 독립적으로 할당되는 PC Register와 Stack 영역, 그리고 모든 스레드가 공유하는 Method Area와 Heap은 자바 애플리케이션의 성능에 중요한 영향을 미친다.\
특히, 스택 기반 구조에서의 메서드 실행과 메모리 관리 방식은 JVM의 효율성을 높이는 중요한 요소라고 한다.&#x20;

#### 3장

**Garbage Collection(GC) 메커니즘**을 상세히 설명하며, 메모리 관리와 성능 최적화에 중요한 개념들을 다루고 있다. GC가 메모리 해제를 자동으로 처리하여 메모리 누수를 방지하는 원리를 시작으로, \
다양한 **GC 알고리즘**(Reference Counting, Mark-and-Sweep 등)의 장단점을 분석하며\
&#x20;**IBM JVM과 CMS GC의 특징을 비교**하며, 각 JVM의 성능과 지연 시간을 최적화하는 방법을 설명하는 장이다.

#### 4장

**Java Class Loader**의 **개념**과 **동작 원리**를 체계적으로 설명하며, 클래스 로딩 과정의 세부 단계를 다루고 있다.\
Class Loader는 **클래스 파일을 JVM으로 로드**하고, **링크**하며 **초기화**하는 역할을 하며, 이를 통해 애플리케이션이 실행된다. **동적 로딩**과 **클래스 로더의 위임 모델**, **WAS에서의 클래스 로더 구조** 등 실무에서 중요한 개념을 깊이 있게 설명하고 있다. 또한, 클래스 공유와 JVM 옵션을 통해 메모리 효율성을 높이는 방법도 다룬다.

#### 5장

JVM의 **Execution Engine**과 **JIT Compiler**의 **작동 원리와 성능 최적화**에 대해 깊이 있는 설명을 제공해. \
Execution Engine은 **Bytecode를 해석**하거나 **JIT Compiler를 통해 Native Code로 변환**하여 **실행**하며, \
이 과정에서 성능 이슈가 발생할 수 있다고 설명한다.\
&#x20;JIT Compiler는 **Lazy 방식**으로 필요할 때만 컴파일을 수행하며, 다양한 최적화 기법을 통해 실행 속도를 크게 개선한다. 또한 **IBM JIT Compiler와 Hotspot Compiler의 최적화 방법을 비교**하며, 각 컴파일러의 특징과 최적화 단계에 대한 이해를 높여주는 장이었다.

### 책 정리

{% content-ref url="chapter-1-jvm.md" %}
[chapter-1-jvm.md](chapter-1-jvm.md)
{% endcontent-ref %}

{% content-ref url="chapter-2-runtime-data-areas.md" %}
[chapter-2-runtime-data-areas.md](chapter-2-runtime-data-areas.md)
{% endcontent-ref %}

{% content-ref url="chapter-3-gc.md" %}
[chapter-3-gc.md](chapter-3-gc.md)
{% endcontent-ref %}

{% content-ref url="chapter-4-class-loader.md" %}
[chapter-4-class-loader.md](chapter-4-class-loader.md)
{% endcontent-ref %}

{% content-ref url="chapter-5-execution-engine.md" %}
[chapter-5-execution-engine.md](chapter-5-execution-engine.md)
{% endcontent-ref %}
