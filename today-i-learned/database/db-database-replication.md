---
description: >-
  단순한 백업? 아니다. 읽기 부하 분산, 장애 대응, 실시간 분석까지가능한 MySQL의 Replication 구조와 함께 레플리카의 실전
  활용사례를 알아보았다.
---

# 👯‍♀️ DB : Database Replication

## What is DB Replication?

대규모 트래픽을 감당해야 하는 백엔드 서비스에서는 DB의 **안정성과 가용성**이 가장 기본적인 요구사항이다.\
이때 필수적으로 도입되는 기술이 바로 **DB Replication(데이터베이스 복제)** 이다.

Replication은 한 대의 **원본 DB(Source)** 에서 발생한 변경사항을 \
하나 이상의 **복제 DB(Replica)** 로 실시간 전파하여,\
동일한 데이터를 유지하도록 동기화하는 구조이다.\
이를 통해 장애 발생 시 **페일오버(failover)** 도 가능하고, **읽기 부하 분산(read scale-out)** 도 이뤄진다.

***

## How Replication Works

MySQL의 Replication은 내부적으로 `Binary Log` 라는 로그 시스템을 기반으로 작동한다.\
이 로그는 **데이터를 어떻게 변경했는지**를 기록해두는 일종의 트랜잭션 로그다.

복제의 전체 과정은 다음과 같다

<figure><img src="../../.gitbook/assets/스크린샷 2025-04-30 19.49.59.png" alt=""><figcaption></figcaption></figure>

* **Source DB**: 데이터 변경 발생 → Binary log 기록
* **Replica DB**: \
  IO Thread가 Binary log를 읽어와 → \
  Relay log에 저장 → \
  SQL Thread가 실행하여 실제 데이터 반영

이렇게 구성된 복제 시스템은 **보통 수백 밀리초 단위**로 싱크가 맞춰져,\
거의 **실시간 동기화 수준**을 보장할 수 있다.

***

## Binary Log Format

MySQL은 Binary Log를 기록하는 방식으로 **세 가지 모드**를 제공한다:

#### 1. Row-based

* **특징**: 실제로 변경된 **데이터 행(row)의 값**을 그대로 기록
* **장점**: 복제 시 정확히 같은 결과를 보장 → 데이터 일관성이 높음
* **단점**: 모든 데이터 변경 값을 저장하기 때문에 Binary log가 **매우 커질 수 있음**

```sql
UPDATE users SET balance = balance + 100 WHERE id = 1;
-- Binary log: row data 변경값 자체를 저장
```

#### 2. Statement-based

* **특징**: 변경을 유발한 **SQL 문장 자체**를 기록
* **장점**: 로그 크기가 작음 → 저장 효율 좋음
* **단점**: `NOW()`나 `RAND()` 같이 실행마다 결과가 달라지는 **비결정적 함수** 사용 시,\
  복제 서버의 실행 결과가 달라질 수 있음

```sql
UPDATE users SET last_login = NOW() WHERE id = 1;
-- Binary log: SQL 문장만 저장됨 → 다른 시간으로 기록될 수도 있음
```

#### 3. Mixed-based

* **특징**: 상황에 따라 Row 또는 Statement를 **자동으로 선택**
* **장점**: 저장 효율성과 데이터 일관성의 **균형점**
* **단점**: 내부 동작이 복잡하여 디버깅이 어려울 수 있음

실제로 많은 서비스에서 Mixed 모드를 기본 설정으로 사용한다.

***

## Practical Usage&#x20;

Replication은 단순한 백업 목적 외에도 **아키텍처 설계에서 핵심 역할**을 수행한다.

### Read/Write Separation

가장 흔한 활용 방식은 **쓰기(write)는 Source DB**에만,\
**읽기(read)는 Replica DB**를 통해 처리하는 **Read/Write 분리 전략**이다.

<figure><img src="../../.gitbook/assets/스크린샷 2025-04-30 19.51.58.png" alt=""><figcaption></figcaption></figure>

이렇게 하면

* **Source DB**는 변경 작업만 집중 → **성능 부하 감소**
* **Replica DB**는 조회 요청만 담당 → **트래픽 분산 효과**

💡 예를 들어 상품 조회, 검색, 유저 정보 조회 등은 **Replica** 를 활용\
주문 등록, 결제, 수정 같은 작업은 반드시 **Source** 를 사용

***

### Failover Structure

Replication 구조는 **장애 대응**에도 유용하다.

* 만약 Source 서버에 장애가 발생하면 → **Replica 중 하나를 승격(Promotion)** 시켜 Primary로 전환
* 이 과정은 자동화 시스템(Pacemaker, Orchestrator 등)으로 운영할 수 있음

***

### Data Backup & Analysis

* 실시간 운영 DB에서 직접 **대용량 분석 쿼리**를 실행하면 성능에 영향을 줄 수 있다.
* 그래서 **Replica DB를 분석 전용 DB**로 설정해서\
  BI 도구나 배치 프로세스를 분리된 DB에서 돌리는 방식도 흔하다.

***

## Summary

<table><thead><tr><th width="164.8359375">항목</th><th>내용</th></tr></thead><tbody><tr><td>목적</td><td>고가용성, 성능 분산, 장애 대응, 실시간 백업</td></tr><tr><td>핵심 구성</td><td>Source(DB), Replica(DB), Binary Log, IO/SQL Thread</td></tr><tr><td>Binary Log 방식</td><td>Row, Statement, Mixed</td></tr><tr><td>주요 활용</td><td>Read/Write 분리, 장애 복구, 데이터 분석 전용</td></tr></tbody></table>

***

## Personal Memo

* MySQL Replication은 **Binary log 기반으로 Source → Replica 데이터 복제**를 실시간에 가깝게 수행한다.
* Binary log 기록 방식에 따라 **일관성과 성능의 trade-off 가 존재한다.**
* Replica는 단순한 백업 그 이상으로, **Read 부하 분산**, **장애 복구**, **데이터 분석** 등에서 필수적인 존재다.
