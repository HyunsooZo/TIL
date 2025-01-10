---
description: >-
  GraphQL은 2012년 Facebook에서 설계되었으며, 2015년에 오픈소스로 공개되었다. 클라이언트 중심의 유연한 데이터 요청을
  지원하는 도구로 널리 사용되고 있다.
---

# 🍡 Spring : GraphQL

## GraphQL?

GraphQL은 데이터 요청 방식에 있어 REST API의 한계를 극복하고자 설계된 언어로,\
클라이언트가 필요한 데이터를 정확히 정의하여 요청할 수 있는 유연성을 제공한다. \
이를 통해 Over-fetching, Under-fetching과 같은 REST API의 문제를 해결할 수 있다.

***

## GraphQL vs REST API

GraphQL과 REST API는 서로 다른 데이터 요청 방식과 철학을 가진다. \
아래는 주요 차이점을 정리한 비교 표이다.

<table data-header-hidden><thead><tr><th width="192"></th><th></th><th></th></tr></thead><tbody><tr><td><strong>Feature</strong></td><td><strong>GraphQL</strong></td><td><strong>REST API</strong></td></tr><tr><td>데이터 요청</td><td>클라이언트가 필요한 데이터만 요청</td><td>서버가 정한 고정된 데이터 반환</td></tr><tr><td>Over-fetching 문제</td><td>해결 가능</td><td>고정된 엔드포인트로 인해 불가</td></tr><tr><td>Under-fetching 문제</td><td>하나의 요청으로 여러 데이터 조합 가능</td><td>여러 엔드포인트를 호출해야 함</td></tr><tr><td>실시간 데이터 처리</td><td>Subscription으로 기본 지원</td><td>WebSocket 등의 추가 구현 필요</td></tr><tr><td>버전 관리</td><td>필요 없음 (스키마로 관리)</td><td>API 버전 관리 필요 (v1, v2 등)</td></tr><tr><td>캐싱</td><td>복잡 (쿼리 기반 캐싱)</td><td>간단 (URL 기반 캐싱 가능)</td></tr><tr><td>개발 난이도</td><td>초기 설정 및 학습 곡선 높음</td><td>비교적 단순</td></tr></tbody></table>

***

## When to Use GraphQL

GraphQL은 다음과 같은 상황에서 사용하면 효과적이다.

1.  **Admin Dashboard 또는 복잡한 조회 화면**

    * 대량의 데이터를 효율적으로 조회해야 하는 경우.
    * 클라이언트가 데이터를 조합하여 요청해야 하는 경우.
    * 예: 여러 데이터 테이블을 조합하여 어드민 대시보드에 표시.

    ```graphql
    query {
        getUserById(id: "1") {
            name
            orders {
                id
                productName
                totalPrice
            }
        }
    }
    ```
2. **Mobile 및 Frontend Application**
   * 데이터 소비가 제한적이고, 네트워크 요청 횟수를 최소화해야 하는 경우.
   * 예: 모바일 앱에서 홈 화면에 필요한 여러 데이터를 한 번에 요청.
3. **Dynamic or Evolving APIs**
   * 데이터 구조가 자주 변경되는 경우.
   * 예: 신제품이 추가되거나, 데이터 요구 사항이 변경되는 스타트업 환경.
4. **Real-Time Updates**
   * 실시간 데이터가 필요한 경우.
   * 예: 채팅 애플리케이션, 알림 시스템.

***

## Flow

<figure><img src="../../.gitbook/assets/스크린샷 2025-01-10 오후 10.50.17.png" alt=""><figcaption></figcaption></figure>

***

## Example

### Problem

어드민 대시보드에서 사용자, 주문, 결제 정보를 조회해야 하는 상황이다. \
REST API를 사용하면 다음과 같은 문제가 발생할 수 있다.

* 여러 엔드포인트 호출 필요 (Over-fetching).
* 불필요한 데이터 포함.

### Solution

GraphQL을 사용하면 단일 요청으로 필요한 데이터를 조합할 수 있다.

* **Schema :** `src/main/resources/graphql` 디렉토리 아래에 작성

```graphql
type Query {
    getUsers: [UserDetail]
}

type UserDetail {
    id: ID!
    name: String!
    orders: [OrderDetail]
}

type OrderDetail {
    id: ID!
    totalPrice: Float!
    payment: Payment
}

type Payment {
    status: String!
}

```

* **데이터 가공 로직**: Resolver에서 데이터를 결합 및 가공.

```java
@Component
public class UserResolver implements GraphQLQueryResolver {

    private final UserService userService;
    private final OrderService orderService;
    private final PaymentService paymentService;

    public UserResolver(
        UserService userService, 
        OrderService orderService, 
        PaymentService paymentService
    ) {
        this.userService = userService;
        this.orderService = orderService;
        this.paymentService = paymentService;
    }

    public List<UserDetail> getUsers() {
        // 1. 사용자 목록 조회
        List<User> users = userService.findAll();

        // 2. 각 사용자별 주문 및 결제 정보 가공
        return users.stream()
            .map(user -> {
                List<Order> orders = orderService.findOrdersByUserId(user.getId());

                List<OrderDetail> orderDetails = orders.stream()
                    .map(order -> new OrderDetail(
                        order.getId(),
                        order.getTotalPrice(),
                        new Payment(paymentService.getPaymentStatus(order.getPaymentId()))
                    ))
                    .toList();

                return new UserDetail(user.getId(), user.getName(), orderDetails);
            })
            .toList();
    }
}
```

***

## Data Caching in GraphQL

GraphQL은 REST API와 달리 쿼리 기반 캐싱을 사용해야 한다. 캐싱 전략은 다음과 같다.

1. **Persisted Queries**
   * 쿼리를 사전에 저장해 캐싱 성능을 높임.
2. **Response Caching**
   * 클라이언트의 동일한 요청에 대해 응답을 캐싱.

***

## Conclusion

* GraphQL은 복잡한 데이터 요구 사항이 있는 클라이언트 중심 애플리케이션에 적합하다.
* REST API는 간단한 데이터 요청 및 캐싱이 중요한 공용 API에 적합하다.
* 두 기술은 상호 배타적이지 않으며, 필요에 따라 혼합하여 사용할 수 있다.
* 위 기술을 적용해 볼 기회가 있으면 정말 재밌 것 같다.&#x20;
