# 5️⃣ Chapter 5 : Execution Engine

#### Execution Engine

**Execution Engine**은 Class Loader를 통해 JVM으로 배치된 Class를 실행. Bytecode를 해석하고 실행하기 위한 Instruction Set을 사용.

#### Execution Engine의 역할

* **Load** 및 **Link**: Bytecode를 실행 가능한 Runtime Module로 변환.
* **Instruction Set**: Bytecode를 해석하여 실행.
* **주요 기능**:
  * **Instruction**: 1 Byte의 Opcode와 Operand로 구성.
  * **Interpreter 방식**: Bytecode를 하나씩 읽어 해석.
  * **JIT Compiler 방식**: Bytecode를 Native Code로 컴파일.

#### Execution Engine의 방식

* **Interpreter** 방식: Bytecode를 해석하여 실행.
* **JIT Compiler** 방식: Bytecode를 Native Code로 변환하여 실행.
* **성능 차이:**
  * Interpreter는 실행 속도가 느리지만 유연함.
  * JIT Compiler는 빠른 실행 속도.

#### Execution Engine의 성능 이슈

**Execution Engine의 성능 이슈**는 주로 Interpreter 방식과 JIT Compiler를 혼합하여 사용하는 과정에서 발생하는 성능 저하에 관한 것. Sun사의 자료에 따르면, Server Application의 경우 전체 수행 시간 중 절반 이상을 Execution Engine이 차지하고, Client Application에서도 30% 이상의 시간을 사용

#### 주요 이슈

1. **Java Application의 성능 결정 요소**:
   * 대규모 WAS 환경에서의 Java Application은 Execution Engine의 성능이 중요한 요소.
   * Java가 탄생한 이후 지난 10년 동안 Execution Engine은 비약적으로 발전.
2. **Bytecode 반복 실행**:
   * 특히 Loop 문에서 Bytecode를 반복 실행하는 것이 성능 저하의 주요 원인.
   * Java는 다차원 배열을 직접 핸들링할 수 없어 배열 안 원소의 반복 수행이 성능에 부정적인 영향.

***

#### JIT Compiler

**JIT Compiler**는 Method Tree, 즉 IR을 가지고 분석과 최적화(Optimization)를 수행. 최적화가 완료되면 IR은 다시 Native Code로 재생성.

#### JIT Compiler의 동작 방식

**JIT Compiler**는 Lazy Fashion으로 작동하여 필요할 때만 컴파일을 수행한다. 주요 작업은 Method 단위로 컴파일하여 실행 속도를 개선.

* Method f1()과 f3()를 반복 호출 시 JIT Compiler가 최적화하여 Native Code로 변환.

#### JIT Compiler의 과정:

1. **Execution**: 가장 중요한 작업.
2. **Optimization**: 생성된 Code의 실행 성능을 최적화.
   * **JIT Compiler의 최적화 단계:**
     1. **Bytecode Optimization**:
        * Flow Analysis
        * Static Method Inlining
        * Virtual Method Inlining
        * Idiomatic Translation
        * Field Privatization
        * Stack and Register Analysis
     2. **Quad Optimization**:
        * Control Flow Optimization
        * Data Flow Optimization
        * Escape Analysis
        * Loop Optimization
        * Data Flow Analysis
     3. **DAG Optimization**:
        * Direct Acyclic Graph 생성.
     4. **Native Code Generation**:
        * 최종 단계, Native Code로 변환.

***

#### IBM JIT Compiler

**IBM JIT Compiler**는 성능을 개선하기 위해 Bytecode를 최적화하여 Native Code로 변환. JVM과의 통합을 통해 효율적으로 작동.

**주요 특징**:

* Method 단위로 최적화.
* Cache를 활용한 성능 개선.

1.  **Mixed Mode Interpreter**

    **- Mixed Mode Interpreter**는 Interpreter와 JIT Compiler를 결합해 초기성능/ 런타임 성능 동시개선.
2. **JIT Compiler Optimization**
   * **JIT Compiler Optimization**은 Bytecode를 정밀하게 분석하여 최적화된 Native Code로 변환.
     1. **Intermediate Representation** 생성.
     2. **Optimizer**: 최적화 수행.
     3. **Native Code Generator**: 최적화된 Native Code 생성.
     4. **Profiler**: 성능 분석.
   * **Java 5 이상의 Optimization**
     1. **Inlining**: 효율적 Method 호출을 위해 Caller에 삽입.
     2. **Local Optimization**: Method 내부의 작은 부분을 반복적으로 최적화.
     3. **Control Flow Optimization**: Method의 Control Flow를 최적화.
     4. **Global Optimization**: 전체 Method를 대상으로 최적화.
     5. **Native Code Generation**: 최종 단계, Machine Code로 변환.
3. **Inlining 세부 기술**
   * Trivial Inlining
   * Call Graph Inlining
   * Tail Recursion Elimination
4. **Local Optimization 작업**
   * Local Data Flow Analysis and Optimizations
   * Register Usage Optimization
   * Simplification of Java Idioms
5. **Control Flow Optimization 작업**
   * Code Reordering, Splitting and Removal
   * Loop Reduction and Inversion
   * Loop Striding and Loop-Invariant Code Motion
   * Loop Unrolling and Peeling
   * Loop Versioning and Specialization
   * Exception-Directed Optimization
   * Switch Analysis
6. **Global Optimization 작업**
   * Global Data Flow Analysis and Optimizations
   * Partial Redundancy Elimination
   * Escape Analysis
   * GC and Memory Allocation Optimizations
   * Synchronization Optimization

***

#### **Optimization Level**

* **JIT Compiler는 다양한 Optimization Level을 제공.**
  * noOpt
  * cold
  * warm
  * hot
  * veryHot
  * scorching
* **Adaptive Compilation**
* **Method는 처음에 Interpreter로 해석.**
* **'warm' 레벨로 진입 후 'hot' 또는 'veryHot' 수준으로 Recompile.**
* **Temporary Profiling Step을 거치며 'scorching' 수준으로 실행.**
* **JIT Compiler's Options:**
  * IBM\_MIXED\_MODE\_THRESHOLD=\<x>\[\<y>]
  * Compilation Threshold 설정
  * forceAOT: 강제 컴파일
  * Xshareclasses: Class 공유 설정
  * Xquickstart: 빠른 컴파일
  * Xint: Interpreter 사용
  * Xjit: 다양한 JIT 옵션 설정

***

#### Hotspot Compiler

* Hotspot JVM의 Hotspot Compiler는 Bytecode를 해석하여 Dynamic Compile 수행. Profiling을 통해 Hot Code를 식별하고 최적화 수행.
* Method Call 시 Compile을 수행하지 않고 Interpreter로 실행.
* JIT Recompile Threshold를 넘으면 Compile 수행.
* **Hotspot Compiler의 구성**
  * Client VM (C1 Compiler)
  * Server VM (C2 Compiler)
* **C1 Compiler**
  * Local Optimization
  * Inlining
  * Class Analysis
  * Profiling Mechanism 사용
* **C2 Compiler**
  * Global Optimization
  * Profiling Mechanism 사용
  * 다양한 최적화 방식 적용

***
