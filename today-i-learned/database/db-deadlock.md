---
description: >-
  데드락은 두 개 이상의 트랜잭션이 서로가 필요로 하는 리소스를 점유하여 교착 상태에 빠지는 현상이다. 이 상태에서는 어떠한 트랜잭션도 진행될
  수 없으며, 결국 시스템이 정상적으로 작동하지 않게 된다.
---

# ☠️ DB : Deadlock

## How Deadlocks Occur

데드락은 주로 다음과 같은 상황에서 발생한다:

1. **Mutual Exclusion:** 하나의 리소스가 단 한 개의 트랜잭션에 의해서만 점유될 수 있다.
2. **Hold and Wait:** 트랜잭션이 이미 보유 중인 리소스를 해제하지 않고 다른 리소스를 요청한다.
3. **No Preemption:** 점유된 리소스는 강제로 해제될 수 없다.
4. **Circular Wait:** 트랜잭션들이 원형 대기 상태를 형성한다.

***

## Deadlock Detection

DBMS는 일반적으로 데드락을 탐지하기 위해 **Wait-for Graph**를 활용한다.

<figure><img src="../../.gitbook/assets/스크린샷 2025-01-07 오후 9.54.18.png" alt=""><figcaption></figcaption></figure>

위 그림처럼 트랜잭션들이 원형으로 대기 상태를 형성하면 데드락이 발생한다.

***

## Deadlock Simulation Code

아래는 MySQL에서 데드락을 유발하는 간단한 시뮬레이션 코드이다.

```sql
-- Transaction 1
START TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;

-- Transaction 2
START TRANSACTION;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;

-- Deadlock 발생
UPDATE accounts SET balance = balance - 100 WHERE id = 2; -- Transaction 1
UPDATE accounts SET balance = balance + 100 WHERE id = 1; -- Transaction 2
```

***

## Deadlock Prevention Strategies

1. **Avoid Circular Wait:** 트랜잭션이 항상 동일한 순서로 리소스를 요청하도록 설계한다.
2. **Timeout:** 특정 시간 내에 트랜잭션이 완료되지 않으면 강제로 롤백한다.
3. **Lock Granularity:** 락의 범위를 좁게 설정하여 충돌 가능성을 줄인다.

***

## DBMS-Specific Deadlock Handling

* **MySQL:**
  * `innodb_lock_wait_timeout` 설정을 통해 데드락 발생 시 대기 시간을 조정할 수 있다.
* **PostgreSQL:**
  * 데드락 탐지기를 주기적으로 실행하며, 데드락이 발견되면 트랜잭션 중 하나를 강제로 종료한다.

***

## Conclusion

데드락은 데이터베이스의 **동시성 문제에서 발생할 수 있는 대표적인 교착 상태**로, \
시스템 성능과 안정성에 큰 영향을 미칠 수 있다. \
이를 예방하거나 효과적으로 해결하기 위해서는 데드락의 발생 원인과 탐지 방법을 명확히 이해하고, \
시스템 요구사항에 맞는 예방 전략을 설계하는 것이 중요하다.

DBMS의 설정값과 특성을 잘 활용하면 데드락 발생 빈도를 줄이고 안정적인 시스템을 운영할 수 있다. \
특히, **애플리케이션 레벨에서의 리소스 접근 순서나 트랜잭션 설계**는 **데드락 예방에 중요한 역할**을 한다.

**효율적인 설계와 관리**로 데드락 문제를 최소화하여 데이터베이스의 안정성과 성능을 극대화하는 것이 핵심이다.

***
