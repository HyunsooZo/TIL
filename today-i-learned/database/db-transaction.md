---
description: 데이터베이스 트랜잭션은 ACID 원칙을 준수하여 데이터의 일관성, 신뢰성, 무결성을 보장하는 논리적 작업 단위
---

# 🤝 DB : Transaction

## Transaction ?

트랜잭션(Transaction)은 데이터베이스에서 논리적인 작업 단위를 의미하며, \
**여러 작업을 하나의 작업처럼 처리**하여 데이터의 일관성을 보장하는 메커니즘이다.

예를 들자면 은행 계좌 이체에서 "A 계좌에서 돈을 출금"하고 "B 계좌에 돈을 입금"하는 과정은 \
**하나의 트랜잭션**으로 처리되어야 한다. 중간에 문제가 생기면 작업 전체가 취소되어야 한다.

## Features (ACID)

### **원자성 (Atomicity)**

트랜잭션의 **모든 작업이 성공**하거나, **전혀 수행되지 않은 상태로 복구**되어야 한다.

"All or Nothing" 원칙.

### **일관성 (Consistency)**:

트랜잭션 전후 데이터베이스의 상태는 항상 일관성을 유지해야 한다.

### **고립성 (Isolation)**:

동시에 실행되는 트랜잭션들이 서로 간섭하지 않도록 보장.

### **지속성 (Durability)**:

트랜잭션이 성공적으로 완료되면 그 결과는 영구적으로 저장된다.

## **How Transactions Work**

트랜잭션의 실행 과정은 아래와 같다:

1. **Transaction Start**: `BEGIN TRANSACTION` 명령으로 트랜잭션 시작.
2. **Execute Operations**: 데이터 읽기, 쓰기 등의 작업 수행.
3. **Commit**: 모든 작업이 성공적으로 완료되면 변경 사항을 저장.
4. **Rollback**: 작업 도중 오류가 발생하면 트랜잭션을 취소하고 원래 상태로 복구.

## **Isolation Levels**

트랜잭션 간 간섭을 얼마나 허용할지를 결정하는 설정이다.&#x20;

<table><thead><tr><th width="202">Level</th><th width="155">Issues Allowed</th><th>Description</th></tr></thead><tbody><tr><td><strong>Read Uncommitted</strong></td><td>Dirty Read</td><td>커밋되지 않은 데이터를 읽을 수 있음.</td></tr><tr><td><strong>Read Committed</strong></td><td>-</td><td>커밋된 데이터만 읽을 수 있음.</td></tr><tr><td><strong>Repeatable Read</strong></td><td>Phantom Read</td><td>동일 트랜잭션 내 데이터 일관성 유지.</td></tr><tr><td><strong>Serializable</strong></td><td>-</td><td>완전한 고립성 보장, 성능 부담 큼.</td></tr></tbody></table>

## **Key Concepts in Transactions**

* **Dirty Read**: 커밋되지 않은 데이터를 읽는 현상.
* **Non-Repeatable Read**: 같은 데이터를 반복 조회할 때 값이 달라지는 현상.
* **Phantom Read**: 트랜잭션 도중 데이터가 삽입되거나 삭제되어 결과가 달라지는 현상.

## **Managing Transactions**

### **In SQL**&#x20;

```sql
BEGIN TRANSACTION;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

## **In Spring**&#x20;

```kotlin
@Transactional
fun transferMoney(
    fromAccountId: Long, 
    toAccountId: Long, 
    amount: Double
) {
    accountRepository.decreaseBalance(fromAccountId, amount)
    accountRepository.increaseBalance(toAccountId, amount)
}
```

## **Best Practices for Transactions**

### **Avoid Long Transactions**

내가 많이 하던 실수인데, 락을 지나치게 오래 유지하는것이다. \
나의 경우에는 주로 Application 레이어에서 지나치게 많은 역할을 가지고 있는 메서드에\
`@Transactional`을 붙이며 발생했었다.\
이렇게 긴 트랜잭션은 락을 오래 유지하여 성능 저하와 충돌을 유발할 수 있다.

### **Define Clear Rollback Conditions**

특정 예외 상황에서 롤백이 필요한 경우 명확히 정의해야 한다.

### **Set Appropriate Isolation Levels**

애플리케이션의 성능과 데이터 일관성 요구에 맞는 격리 수준을 선택한다.

## In An Addition

사실 개인적으로 트랜잭션은 어느 서비스에든 중요하다고 생각하지만, \
특히 나 동시성 및 금전거래 등과 같은 도메인에서 특히 더 중요시된다!\
예 : 계좌 이체 작업. 재고 관리와 결제 처리 항공권, 호텔 예약 시 동시성 처리 등
