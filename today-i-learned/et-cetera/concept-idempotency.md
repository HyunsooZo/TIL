---
description: >-
  멱등성(Idempotency)이란 같은 연산을 여러 번 수행해도 결과가 변하지 않는 성질을 의미한다.  즉, 동일한 요청을 여러 번
  보내더라도 시스템의 상태나 응답이 변하지 않는 특성을 가진다.
---

# 👆 Concept : Idempotency

## Why Idempotency Matters ?

### 안정적인 API 설계

네트워크 장애로 인해 동일한 요청이 중복 전송될 수 있다. \
멱등성을 적용하면 이러한 상황에서도 데이터 무결성을 보장할 수 있다.

### 트랜잭션 보장

분산 시스템에서는 동일한 요청이 여러 번 처리될 가능성이 높다. \
멱등성이 적용되면 중복 실행이 되더라도 데이터가 일관된 상태를 유지한다.

### 장애 복구

장애 발생 후 재시도 로직을 구현할 때, 멱등성이 없다면 중복 데이터가 발생할 수 있다. \
이를 방지하기 위해 멱등성이 필요하다.

## Idempotency in HTTP Methods

<table data-header-hidden><thead><tr><th width="128"></th><th width="122"></th><th></th></tr></thead><tbody><tr><td><strong>Method</strong></td><td><strong>Idempotent</strong></td><td><strong>Description</strong></td></tr><tr><td><strong>GET</strong></td><td>✅ Yes</td><td>동일한 요청을 여러 번 해도 같은 응답을 받음</td></tr><tr><td><strong>PUT</strong></td><td>✅ Yes</td><td>같은 데이터를 여러 번 업데이트해도 결과는 동일</td></tr><tr><td><strong>DELETE</strong></td><td>✅ Yes</td><td>같은 리소스를 여러 번 삭제 요청해도 결과는 동일</td></tr><tr><td><strong>POST</strong></td><td>❌ No</td><td>새 리소스 생성의 경우 중복 요청이 발생하면 데이터가 중복 생성될 수 있음</td></tr><tr><td><strong>PATCH</strong></td><td>❌ No</td><td>부분 수정 요청이므로, 중복 요청 시 상태가 계속 변할 수 있음</td></tr></tbody></table>

## How to Ensure Idempotency

### Idempotency Key 사용

클라이언트가 요청마다 **고유한 ID**를 포함하면 서버에서 중복 요청을 방지할 수 있다.

```json
POST /payments
Content-Type: application/json
Idempotency-Key: 123456789abcdef

{
  "user_id": "1001",
  "amount": 50000,
  "currency": "KRW"
}
```

서버는 `Idempotency-Key`를 저장하고 동일한 키로 요청이 오면 기존 응답을 반환하여 중복 처리를 방지한다.

### 상태 기반 처리

* **리소스가 이미 존재하는지 확인 후 업데이트만 수행**
* 예를 들어, 주문 생성 API에서 같은 `order_id`가 이미 존재하면 새로 생성하지 않고 기존 데이터를 반환

### 트랜잭션 활용

* 중복 요청 시 동일한 트랜잭션을 감지하여 롤백하거나 무시하는 방식 적용

## Use Cases

### 결제 시스템

* 동일한 결제 요청이 여러 번 처리되지 않도록 보장
* Idempotency Key를 활용하여 중복 결제를 방지

### 상품 주문 API

* 같은 주문이 여러 번 등록되지 않도록 방지

### 계좌 이체

* 동일한 이체 요청이 반복되지 않도록 방어 로직 적용

## Flow (Idempotency-Key 사용 시)

<figure><img src="../../.gitbook/assets/스크린샷 2025-02-12 오후 8.20.43.png" alt=""><figcaption></figcaption></figure>

## Conclusion

* 멱등성이란 같은 요청을 여러 번 수행해도 결과가 변하지 않는 성질이다.
* `GET`, `PUT`, `DELETE`는 멱등성을 가지지만 `POST`, `PATCH`는 멱등성이 없다.
* Idempotency Key, 상태 기반 처리, 트랜잭션 보상을 활용하면 멱등성을 보장할 수 있다.
* 결제, 주문, 계좌 이체 등의 시스템에서 멱등성이 필수적이다.
* 전 직장 동료의 코드 리뷰 때문에 찾아본 개념. 앞으로 멱등성을 염두하고 설계하는 습관을 가져야겠다.
