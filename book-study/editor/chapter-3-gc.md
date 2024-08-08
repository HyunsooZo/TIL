# 3️⃣ Chapter 3 : GC

Garbage Collection이란?

* **메모리 할당과 해제**:
  * Java는 다른 프로그래밍 언어와 달리 메모리의 할당은 개발자가 할 수 있지만, 메모리의 해제는 Garbage Collection이 자동으로 처리한다.
  * 개발자가 명시적으로 메모리를 해제할 수 없으며, 이는 Garbage Collection의 역할이다.
* **객체의 메모리 할당**:
  * Java에서는 객체를 new, newarray, anewarray, multianewarray와 같은 명령어를 통해 메모리를 할당받는다.
  * 하지만 이 할당받은 메모리를 직접 해제하는 방법은 제공되지 않으며, system.gc()와 같이 힌트를 줄 수는 있지만, 실제로 언제 해제될지는 알 수 없다.
* **Garbage Collection의 필요성**:
  * 힙(Heap)이나 메서드 영역(Method Area)에 있는 객체가 메모리에서 삭제되는 과정을 자동으로 처리하여, 메모리 누수와 같은 문제를 방지한다.
  * 객체가 더 이상 사용되지 않으면 Garbage Collection이 이를 감지하고 메모리에서 해제한다.

***

#### Garbage Collection의 대상

* **Garbage의 정의**:
  * 힙(Heap)과 메서드 영역(Method Area)에서 사용되지 않는 객체를 감지하여 제거한다.
  * Root Set을 통해 참조되지 않는 객체를 Garbage로 간주한다.
* **Root Set**:
  * Root Set은 Stack의 참조 정보, Local Variable Section, Operand Stack, Native Method 등이 포함된다.
  * Root Set에 의해 참조되지 않는 객체는 Garbage로 간주된다.
* **Reachable Object와 Unreachable Object**:
  * Reachable Object: Root Set에 의해 참조되는 객체.
  * Unreachable Object: Root Set에 의해 참조되지 않는 객체로, Garbage로 수집된다.

***

#### Garbage Collection 과정

* **객체의 참조 여부**:
  * 객체가 더 이상 참조되지 않으면 Garbage Collection의 대상이 된다.
  * Reference가 null로 설정되지 않으면 객체는 여전히 메모리에 남아 있을 수 있다.
* **메모리 누수 방지**:
  * 적절한 메모리 관리와 Garbage Collection을 통해 메모리 누수를 방지하고, 시스템의 안정성을 유지한다.
* **Compaction**:
  * 메모리 단편화(Fragmentation)를 줄이기 위해 Garbage Collection 후 남는 메모리 공간을 효율적으로 사용하도록 Compaction을 수행한다.

***

#### Garbage Collector의 기본 알고리즘

* **Reference Counting Algorithm**
  * **방식**: 객체의 참조 횟수를 세어 0이 되면 메모리에서 해제하는 방식.
  * **장점**: 간단하고 구현이 쉬움.
  * **단점**: 순환 참조 문제를 해결하지 못함.
  * 즉 객체의 참조 횟수로 메모리 해제를 결정하지만 순환 참조 문제를 해결하지 못한다.
* **Mark-and-Sweep Algorithm**
  * **방식**: 객체를 마크하고, 마크되지 않은 객체를 스윕하여 메모리에서 해제하는 방식.
  * **장점**: 비교적 간단한 구현.
  * **단점**: Fragmentation 문제를 일으킬 수 있음.
  * 즉 객체를 마크하고, 마크되지 않은 객체를 스윕하여 해제하지만 Fragmentation이 발생할 수 있다.
* **Mark-and-Compacting Algorithm**
  * **방식**: Mark-and-Sweep의 Fragmentation 문제를 해결하기 위해 객체를 압축하여 메모리를 재배치하는 방식.
  * **장점**: 메모리의 효율적인 사용.
  * **단점**: 추가적인 연산 오버헤드가 발생할 수 있음.
  * 즉 Fragmentation 문제를 해결하기 위해 객체를 압축하여 메모리를 재배치한다.
* **Copying Algorithm**
  * **방식**: 메모리를 두 개의 영역으로 나누어 사용되지 않는 객체를 새로운 영역으로 복사하여 해제하는 방식.
  * **장점**: Fragmentation 문제를 방지.
  * **단점**: 메모리 사용이 비효율적일 수 있음.
  * 즉 \*\*\*\*메모리를 두 개의 영역으로 나누어 사용되지 않는 객체를 새로운 영역으로 복사하여 해제한다.
* **Generational Algorithm**
  * **방식**: 객체를 생애 주기별로 나누어 관리하는 방식으로, Young Generation과 Old Generation으로 구분.
  * **장점**: 객체의 생애 주기에 따른 최적화 가능.
  * **단점**: 구현이 복잡할 수 있음.
  * 즉 객체를 생애 주기별로 나누어 관리하고, 각 세대별로 다른 알고리즘을 적용한다.
* **Train Algorithm**
  * **방식**: 힙을 여러 블록으로 나누어 각 블록 단위로 Garbage Collection을 수행하는 방식.
  * **장점**: Fragmentation 문제를 해결하고, 전체 메모리의 중단 시간을 줄임.
  * **단점**: 구현 복잡성.
  * 즉 메모리를 여러 블록으로 나누어 각 블록 단위로 Garbage Collection을 수행하여 Fragmentation 문제를 해결한다.
* **Adaptive Algorithm**
  * **방식**: 힙의 현재 상황을 모니터링하여 적절한 알고리즘을 선택하고 적용하는 방식.
  * **장점**: 상황에 맞게 적절한 알고리즘을 선택하여 효율적인 메모리 관리 가능.
  * **단점**: 다양한 알고리즘의 Trade-off를 고려해야 함.
  * 즉 힙의 상황을 실시간으로 모니터링하여 적절한 GC 알고리즘을 적용하는 방식.

***

#### Hotspot JVM의 Garbage Collection

#### 1. **Generational Algorithm**

* 객체마다 나이를 기록하여 생존도를 판단.
* Minor GC는 객체가 사라지지 않고 Survivor Area로 이동한 횟수를 의미.
* 객체마다 나이를 기록하여 생존도를 판단.
* Hotspot JVM에서는 이 Age를 Object Header에 기록.
* Minor GC 때 Promotion 여부를 결정.

#### 2. **Age와 Promotion**

* Age가 같거나 다르면 Old Generation으로 Promotion될 가능성이 있음.
* `XX:MaxTenuringThreshold` 옵션으로 Promotion을 위한 임계값 설정. 기본값은 31.
* 객체는 최대 31번의 Young Generation GC를 거쳐야 Promotion 됨.
* Age는 Object Layout에서 세 번째 Word에 기록됨.

#### 3. **Minor GC 과정**

* Young Generation의 Garbage Collection을 의미.
* Eden, Survivor1, Survivor2로 나뉨.
* Eden에서 객체가 할당되고, 살아남은 객체는 Survivor1으로 이동.
* Minor GC 때 From 영역과 To 영역을 번갈아가며 사용.
* Mark Phase 후 Copy Phase를 통해 객체를 이동.
* Copy가 완료되면 Free Space 확보를 위해 Scavenge 실행.

#### 4. **Garbage Collection 대상 영역**

* **Young Generation**:
  * Eden: 객체가 최초로 할당되는 영역.
  * Survivor1: 첫 번째 Survivor 영역.
  * Survivor2: 두 번째 Survivor 영역.
* **Old Generation**:
  * Tenured Area: 오래된 객체가 저장되는 영역.
* **Permanent Area**:
  * 메타데이터와 클래스 정보를 저장.

#### 5. **Garbage Collector 종류**

* **Serial Collector**:
  * 단일 스레드로 동작하며 기본 Collector.
  * Young Generation과 Old Generation 모두 처리.
* **Parallel Collector**:
  * 다중 스레드를 사용해 병렬로 Garbage Collection 수행.
  * Young Generation과 Old Generation 모두 처리.
* **Concurrent Mark-Sweep (CMS) Collector**:
  * Low Pause Collector, Young Generation을 Mark-and-Sweep 알고리즘으로 처리.
  * 애플리케이션의 중단 시간을 최소화.
* **Garbage-First (G1) Collector**:
  * 전체 Heap을 작은 Region으로 나누어 관리.
  * 실시간 Garbage Collection을 목표로 함.

#### 6. **Heap Sizing 관련 옵션**

* `Xms<size>`: 초기 Heap 크기 설정.
* `Xmx<size>`: 최대 Heap 크기 설정.
* `XX:NewSize=<size>`: Young Generation의 초기 크기 설정.
* `XX:MaxNewSize=<size>`: Young Generation의 최대 크기 설정.
* `XX:SurvivorRatio=<value>`: Eden과 Survivor 영역 비율 설정.
* `XX:TargetSurvivorRatio=<value>`: Survivor 영역의 사용 비율 목표 설정.
* `XX:MaxTenuringThreshold=<value>`: 객체의 Promotion을 위한 최대 나이 설정.

#### 7. **Promotion과 Age**

* Promotion은 Young Generation에서 Old Generation으로 객체를 이동시키는 과정.
* 성숙된 객체만 Promotion의 대상이 됨.
* Promotion은 객체의 Age에 따라 결정됨.

#### 8. **Mark-and-Sweep 과정**

* **Mark Phase**:
  * GC 루트에서 시작해 모든 객체를 마킹.
* **Sweep Phase**:
  * 마킹되지 않은 객체를 청소.

#### 9. **Card Table과 Write Barrier**

* **Card Table**:
  * Old Generation의 메모리 구조를 대표하는 밴드.
  * 각 카드의 크기는 512 Bytes.
* **Write Barrier**:
  * Old Object가 Young Object를 참조할 때 사용.
  * Card Table을 통해 참조 여부를 체크.

#### 10. **Scavenge**

* Free Space 확보를 위해 사용.
* Eden 영역과 Survivor 영역의 객체를 청소.
* HP 머신에서는 Scavenge 과정을 Minor GC로 기록.

#### 11. **JVM 옵션**

* 다양한 GC 및 Heap 옵션을 제공.
* `XX:MaxHeapFreeRatio=<percent>`: 전체 Heap 대비 Free Space의 최대 비율.
* `XX:NewRatio=<value>`: Young/Old Generation 크기 비율.
* `XX:SurvivorRatio=<value>`: Eden/Survivor 영역 비율.
* `XX:TargetSurvivorRatio=<value>`: Survivor 영역의 사용 비율 목표.

#### 12. **Generational Hypothesis**

* 대부분의 객체는 금방 사라진다는 가정(유아사망률 가설).
* Young Generation은 주로 금방 사라지는 객체가 할당됨.
* Old Generation은 오랫동안 살아남는 객체가 할당됨

***

#### CMS Collector

1. **CMS Collector 단계**:
   * **Initial Mark Phase**: Serial Phase로 Heap이 Suspend 상태가 되고 Root Set에서 직접 Reference 되는 Object를 대상으로 함.
   * **Concurrent Mark Phase**: Single Thread가 수행하며 Application 동작 중 수행. Initial Mark Phase에서 선택된 Live Object를 따라가며 Marking.
   * **Remark Phase**: Parallel Phase로 Garbage Collection 중 가장 Pause Time이 길지만 최대한 줄이기 위해 Concurrent Mark 중 변경된 Reference를 추적.
   * **Concurrent Sweep Phase**: Application과 병행하여 Dead Object를 제거.
2. **Incremental Mode**:
   * **XX:+CMSIncrementalMode**: CMS Collector의 Incremental Mode 사용.
   * **XX:+CMSIncrementalPacing**: Duty Cycle을 자동으로 설정.
   * **XX:+CMSIncrementalDutyCycle=\<value>**: Duty Cycle을 사용자가 직접 설정. Minor GC와 Full GC 사이의 비율을 조절.
3. **Scheduling 및 Pause Time 조정**:
   * **Initial Mark Phase**: Remark Phase와 Minor GC가 충돌하지 않도록 Scheduling.
   * **Scheduling의 목적**: Old Generation이 Full이 되기 전에 Garbage Collection 수행하여 OutOfMemoryException 방지.
4. **Garbage Collection의 최적화**:
   * **Fragmentation 및 Floating Garbage 문제 해결**: Fragmentation과 Floating Garbage를 줄이기 위해 Compact 작업 필요성 언급.
   * **Promotion Buffer**: 두 Thread가 동시에 Promotion 영역에 접근할 때 Memory Corruption 방지를 위해 사용.
5. **Collector 옵션**:
   * **XX:+UseConcMarkSweepGC**: CMS Collector 사용.
   * **XX:+UseParNewGC**: Young Generation에서 Parallel GC 수행.
   * **XX:+CMSParallelRemarkEnabled**: Remark Phase 동안 Pause Time 줄이기 위해 사용.
   * **XX:+CMSInitiatingOccupancyFraction=\<value>**: Old Generation의 사용 비율 설정.
   * **XX:+CMSExpAvgFactor=\<value>**: CMS Collector의 통계적 평균 계산을 위한 Factor 설정.
6. **Ergonomics 옵션**:
   * **XX=\<value>**: 최대 Pause Time 설정.
   * **XX=\<value>**: Application의 총 실행 시간 대비 GC 시간 비율 설정.
   * **XX:+UseAdaptiveSizePolicy**: 자동으로 Heap 크기 조정.
   * **XX=\<value>**: Young Generation 크기 증분 설정.
   * **XX=\<value>**: Old Generation 크기 증분 설정.
   * **XX=\<value>**: Heap 감소 비율 설정.
   * **XX:+AggressiveHeap**: Physical Memory를 최대한 활용.
7. **Serial Collector 옵션**:
   * **XX:+UseSerialGC**: Serial Collector 사용.
   * **XX=\<value>**: Object의 초기 Age 값 설정.
   * **XX=\<value>**: Promotion 되는 Age 최대값 설정.
   * **XX=\<byte size>**: Young Generation에서 바로 Tenured Area로 Object 이동.

#### Collector의 비교 및 특징

* **Serial Collector**: Single Thread로 모든 작업 수행. 간단하지만 Pause Time이 길다.
* **Parallel Collector**: Multi-Thread를 활용해 빠른 Garbage Collection. 주로 Young Generation에 적용.
* **CMS Collector**: Concurrent 작업을 통해 Pause Time을 줄이는 것을 목표로 함.
* **Incremental Collector**: CMS Collector의 Incremental Mode를 사용해 Low Pause Goal을 달성.

***

#### IBM JVM의 Garbage Collection

#### 1. 기본 개념

* **Garbage Collection (GC)**: 메모리 관리 기법으로, 사용되지 않는 메모리를 자동으로 회수.
* **Mark, Sweep, Compaction 단계**: GC 과정의 세 단계로, Mark는 살아있는 객체 표시, Sweep은 죽은 객체 회수, Compaction은 메모리 단편화 해결.

#### 2. 주요 요소

* **Work Packet**: GC 작업을 분할하여 처리하기 위한 단위.
  * **Output Packet**: 참조를 포함한 객체를 처리.
  * **Non-Empty List**: 객체 참조를 포함하는 패킷.
  * **Empty List**: 처리할 객체 참조가 없는 패킷.

#### 3. Mark Phase

* **Marking Thread**: 객체 참조를 따라가며 살아있는 객체를 표시.
* **Mark Stack**: 현재 추적 중인 객체 참조를 저장.
  * **Mark Stack Overflow(MSO)**: Mark Stack이 가득 찰 때 발생하는 현상.
  * **Parallel Mark**: 여러 스레드가 동시에 마킹 작업 수행.

#### 4. Sweep Phase

* **비트 벡터**: 객체 상태(Alive/Dead)를 기록.
* **Compaction**: 단편화를 줄이기 위해 메모리를 재배치.

#### 5. Heap Management

* **Heap Layout**: JVM의 메모리 영역.
  * **Heap Base**: 기본 힙 영역.
  * **Heap Limit**: 힙의 최대 크기.
  * **TLH(Thread Local Heap)**: 각 스레드가 사용하는 로컬 힙.

#### 6. Java 5 및 이후 버전 변화

* **Generational Concurrent Collector (gencon)**: Java 5에서 도입된 세대별 동시 수집기.
  * **Nursery 영역**: 새로운 객체가 할당되는 영역.
  * **Tenured 영역**: 오래된 객체가 이동되는 영역.
* **Heap Expansion**: 힙 공간이 부족할 때 자동으로 확장.
  * **Free Space의 최소/최대 비율**: 힙 확장의 기준이 되는 비율.

#### 7. IBM JVM의 고유 기능

* **Dark Matter**: 사용되지 않는 메모리 영역으로, 메모리 단편화 방지를 위한 개념.
* **First Fit 방식**: 적합한 크기의 Free Chunk를 선택하여 메모리를 할당.

#### 옵션들

* **Xnoclassgc**: 클래스 객체에 대한 GC 비활성화.
* **Xenableexcessivegc**: 과도한 GC 발생 시 OutOfMemoryError 방지.
* **Xgcpolicy**: 다양한 GC 정책 선택.
  * **optthruput**: Throughput 향상을 위한 정책.
  * **optavgpause**: Pause Time 감소를 위한 정책.
  * **gencon**: 세대별 수집기.
  * **subpool**: 서브풀을 이용한 관리.

#### 주요 설정 값

* **Xmx**: 최대 힙 크기.
* **Xms**: 초기 힙 크기.
* **Xmn**: Nursery 영역 크기.
* **Xmaxf**: Free Space 최대 비율.
* **Xminf**: Free Space 최소 비율.

#### Heap Allocation과 Sizing

* **Object 할당**: Heap Base에서 시작하여 공간 할당.
* **Freelist**: 사용되지 않은 메모리 블록 목록.
* **Allocation 실패 시**: Garbage Collection 실행하여 공간 확보.

#### **Compaction Phase와 Map2**

* Application 초기화 작업이 완료되면 Compaction Area를 재설정하고, 최소한의 시간으로 작업을 끝내기 위한 Compaction 작업의 최적화된 섹션을 설정한 후 Object를 이동시킨다. Move Step이 완료되면 Fix-Up Step이 시작된다.

#### **Fix-Up Step**

* **Stop-the-World Fix-Up**: Root Set에서 직접 참조하고 있는 Object부터 Fix-Up 작업을 수행한다.
* **Concurrent Fix-Up**: Collection Thread와 Concurrent Fixer가 Fix-Up 작업을 수행하며, Unfixed 상태의 페이지를 수정한다.
* **Heap Page 상태 조사**:
  * Heap Page 상태를 Unfixed에서 Busy로 변경하고, Fix-Up 작업을 진행한다. 이후 상태를 Fixed로 변경하면 Mutator가 해당 페이지에 접근하여 작업을 수행할 수 있다.

#### **Concurrent Sweep**

* Allocation 시 필요한 Free Space를 제공하지 못할 경우, Sweep Analysis 작업을 수행하여 연결된 Section을 찾고, 필요시 Allocation 작업을 요청한다.
* Compaction Phase에서는 Object 이동 중 발생할 수 있는 데이터 유실이나 Memory Corruption을 방지하기 위해 Concurrent Sweep 작업을 수행해야 한다.

#### **Optimize for Pause-Time Collector**

* Java 5부터 Compaction 단계에서 Mostly Concurrent Compaction Algorithm을 적용하여 Mark Phase 동안 Compaction을 수행한다.
* Compaction 단계를 Move Step과 Fix-Up Step으로 나누어 수행하며, 가능한 Concurrent Job으로 수행하여 Pause Time을 줄이는 것을 목표로 한다.

#### **Parallel Compaction**

* Compaction 시에는 Block 단위로 Object를 이동시키며, Move Step 이후에 Fix-Up Step에서 모든 Reference를 수정하고 새로운 위치 정보를 갱신한다.
* Incremental Compaction은 Section 단위로 나누어 Section 별로 Compaction 작업을 수행하며, Object 이동 후 Reference의 위치를 갱신하는 방식으로 진행된다.

#### **Unmovable Object**

* Compaction 작업 시 Unmovable Object의 영향을 분석하며, 이러한 Object들이 전체 Heap에서 차지하는 공간과 관련된 문제를 해결한다.

#### **Throughput Collector의 옵션**

* Helper Thread 수를 설정하여 Parallel 작업에 사용하며, Work Packet의 크기를 설정하여 작업 효율을 높인다.
* Java 5부터는 Parallel Compaction Algorithm을 적용하여 각 Thread가 여러 Area를 담당하여 Compaction 작업을 수행한다.

***

### 비교점 요약

#### IBM 가비지 컬렉터

IBM JVM의 가비지 컬렉터는 특히 고성능과 낮은 지연 시간 (low latency)을 목표로 설계되었다. 주요 특징은 다음과 같다:

1. **Balanced GC**:
   * IBM JVM의 대표적인 가비지 컬렉터로, 힙 메모리를 작은 섹션으로 나누어 효율적으로 관리한다.
   * Marking, Copying, Compaction 단계를 동시에 수행하여 메모리 단편화를 최소화하고 성능을 극대화한다.
   * 멀티코어 프로세서 환경에서 효과적으로 작동한다.
2. **Metronome GC**:
   * 실시간 (real-time) 가비지 컬렉터로, 엄격한 지연 시간 요구사항을 충족시킨다.
   * 애플리케이션의 중단 시간을 매우 짧게 유지하도록 설계되었다.
3. **Generational GC**:
   * 객체의 생존 기간에 따라 Young Generation과 Old Generation으로 나누어 관리한다.
   * 주로 Scavenge(Young Generation)와 Global(Old Generation) 단계를 통해 가비지 컬렉션을 수행한다.

#### CMS (Concurrent Mark-Sweep) 가비지 컬렉터

CMS 가비지 컬렉터는 Oracle JVM (특히 HotSpot JVM)에서 제공하는 가비지 컬렉터로, 낮은 지연 시간을 목표로 한다. 주요 특징은 다음과 같다:

1. **Concurrent Marking**:
   * 애플리케이션 스레드와 동시에 실행되며, 마킹 단계에서 애플리케이션의 일시 중단 시간을 줄인다.
   * 두 단계로 이루어지며, 초기 마킹 (initial marking)과 재마킹 (remarking)을 통해 살아있는 객체를 식별한다.
2. **Concurrent Sweeping**:
   * 마킹 단계 이후, 살아있는 객체를 제외한 나머지 객체들을 청소하는 단계이다.
   * 애플리케이션 스레드와 동시에 수행되며, 긴 중단 시간을 피할 수 있다.
3. **Old Generation 집중**:
   * 주로 Old Generation에서 발생하는 가비지 컬렉션에 초점을 맞춘다.
   * Young Generation은 별도로 관리되어 Minor GC가 주기적으로 발생한다.
4. **Compaction 없음**:
   * CMS는 메모리 단편화를 방지하기 위한 Compaction을 수행하지 않아서, 메모리 단편화 문제가 발생할 수 있다.
   * 단편화가 심할 경우, Full GC를 통해 메모리를 정리한다.

### 3장 핵심정리

#### GC 란

* Java의 Garbage Collection은 메모리 해제를 자동으로 처리하여 메모리 누수를 방지한다.
* 객체는 new 등으로 메모리를 할당받지만, 해제는 시스템에 의해 관리된다.
* Root Set에서 참조되지 않는 객체는 Garbage로 간주되어 제거된다.
* 주요 GC 알고리즘에는 Reference Counting, Mark-and-Sweep, Mark-and-Compacting, Copying, Generational, Train, Adaptive Algorithm이 있다.
* 각 알고리즘은 메모리 단편화 문제와 객체 생존 주기를 다루는 방식이 다르다.

#### IBM GC와 CMS GC 비교

* **IBM GC**는 Balanced GC, Metronome GC, Generational GC를 포함하며 고성능과 낮은 지연 시간을 목표로 한다. Marking, Copying, Compaction 단계를 통해 메모리 단편화를 최소화한다.
* **CMS GC**는 주로 Old Generation을 대상으로 하며 Compaction 없이 동시 마킹과 스위핑을 통해 낮은 지연 시간을 유지한다. 메모리 단편화 문제는 Full GC로 해결한다.

| 특징         | IBM 가비지 컬렉터                                | CMS 가비지 컬렉터                 |
| ---------- | ------------------------------------------ | --------------------------- |
| 수집기 유형     | Balanced GC, Metronome GC, Generational GC | Concurrent Mark-Sweep (CMS) |
| 주요 목표      | 고성능, 낮은 지연 시간                              | 낮은 지연 시간                    |
| 대상 세대      | Young 세대와 Old 세대                           | 주로 Old 세대                   |
| 압축         | 예                                          | 아니오                         |
| 동시 마킹      | 예                                          | 예                           |
| 동시 스위핑     | 예                                          | 예                           |
| 메모리 단편화 처리 | 압축으로 최소화                                   | 가능, Full GC 필요              |
