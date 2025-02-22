---
description: >-
  현대 컴퓨터 시스템에서는 여러 작업을 동시에 수행할 수 있는 기능이 중요해졌다. 이를 가능하게 해주는 대표적인 기술이 멀티태스킹과
  멀티프로세싱이다. 멀티태스킹은 단일 CPU 코어에서 여러 프로그램이 동시에 실행되는 것처럼 보이도록 하는 기술이고, 멀티프로세싱은 여러
  CPU 코어(또는 여러 프로세서)를 이용해 물리적으로 여러 작업을 병렬로 처리하는 기술이다.
---

# 🤹 Thread : Multi- tasking & processing

## Multitasking?

멀티태스킹이란 단일 CPU 코어가 여러 프로그램을 **매우 빠른 속도로 번갈아**가며 실행하여 \
마치 **동시에 실행되는 것처럼 보이게** 하는 기법이다. (사람이 보기에만 그런게 보이고 물리적으로는 아님)\
이를 위해 운영체제는 시간 분할(Time Sharing) 기법과 스케줄링(Scheduling) 기법을 사용한다.

* **시간 분할(Time Sharing)**: \
  CPU 시간을 짧게 쪼개어 여러 프로그램에 돌아가면서 할당하는 방식이다.
* **스케줄링(Scheduling)**: \
  어떤 프로그램에 얼마만큼 CPU 시간을 줄 것인지, 어떤 우선순위로 실행할 것인지를 결정하는 과정이다.

### Multitasking Diagram

<figure><img src="../../../.gitbook/assets/스크린샷 2025-02-22 오후 8.21.42.png" alt=""><figcaption></figcaption></figure>

## Multiprocessing?

멀티프로세싱이란 **CPU 코어가 둘 이상인 환경에서 여러 프로그램을 물리적으로 동시에 처리**하는 기술이다. \
각 코어가 독립적으로 연산을 수행하므로 멀티태스킹보다 더 높은 처리량(Throughput)을 기대할 수 있다. \
현대의 CPU는 여러 개의 코어를 탑재하고 있으므로, 일반적으로 멀티프로세싱 환경에서 동작하게 된다.

### Multiprocessing Diagram

<figure><img src="../../../.gitbook/assets/스크린샷 2025-02-22 오후 8.22.43.png" alt=""><figcaption></figcaption></figure>

## Differences

### Hardware Perspective

* 멀티태스킹: 단일 CPU(단일 코어)에서 시간 분할로 여러 작업을 동시에 처리하는 것처럼 보이게 한다.
* 멀티프로세싱: 실제로 여러 CPU 코어를 사용해 병렬로 작업을 처리한다.

### Performance Improvement

* 멀티태스킹: 소프트웨어적인 방식으로 CPU 시간을 분할하여 여러 작업을 처리한다.
* 멀티프로세싱: 하드웨어적으로 여러 코어를 활용하여 성능을 높인다.

### Excample

* 멀티태스킹: 초창기에는 단일 코어 환경에서 여러 프로그램을 번갈아 실행하는 방식으로 사용되었다.
* 멀티프로세싱: 현대의 다중 코어 프로세서를 탑재한 컴퓨터가 대표적인 예시이다.

### Comparison Diagram

아래는 단일 CPU 코어(멀티태스킹)와 다중 코어(멀티프로세싱)의 개념 비교를 간단히 나타낸 예시이다.

<figure><img src="../../../.gitbook/assets/스크린샷 2025-02-22 오후 8.17.53.png" alt=""><figcaption></figcaption></figure>

