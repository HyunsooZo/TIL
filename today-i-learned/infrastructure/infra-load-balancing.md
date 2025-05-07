# ⚖️ Infra : Load Balancing

## Load Balancing?

로드 밸런싱이란 하나의 애플리케이션 또는 시스템에 들어오는 네트워크 요청을 \
여러 서버(리소스 풀)에 균등하게 분산시키는 기술을 의미한다.

로드 밸런서는 보통 서버 앞단(Frontend)에 위치하며, 클라이언트의 요청을 받아 적절한 서버로 전달한다. \
이를 통해 다음과 같은 효과를 얻을 수 있다

* 고가용성(High Availability)
* 확장성(Scalability)
* 성능 향상(Performance)
* 보안(Security)

***

## Load Balancing Algorithms

다양한 로드 밸런싱 알고리즘이 존재하며, 각각의 목적과 상황에 따라 선택이 달라진다.

### **1. Round Robin**

현업에서 가장 흔히 사용되는 알고리즘이다. 우리회사도 해당 알고리즘을 사용하는 것으로 알고있다.

모든 요청을 순서대로 서버에 분산한다. 예: A → B → C → A → ...

* 장점: 구현이 단순하고 고른 분산
* 단점: 서버의 부하 상태나 처리 능력을 고려하지 않음

<figure><img src="../../.gitbook/assets/스크린샷 2025-05-07 23.32.51.png" alt=""><figcaption></figcaption></figure>

***

### **2. Weighted Round Robin**

각 서버에 가중치(weight)를 부여하여 처리 능력이 높은 서버에 더 많은 요청을 전달한다.

* 장점: 현실적인 부하 분산 가능
* 단점: 실시간 서버 상태는 고려하지 않음

<figure><img src="../../.gitbook/assets/스크린샷 2025-05-07 23.33.18.png" alt=""><figcaption></figcaption></figure>

***

### **3. Least Connections**

현재 활성 연결 수가 가장 적은 서버로 요청을 전달한다.

* 장점: 연결 수 기반 분산
* 단점: 서버 성능 차이는 고려하지 않음

<figure><img src="../../.gitbook/assets/스크린샷 2025-05-07 23.33.45.png" alt=""><figcaption></figcaption></figure>

***

### **4. Weighted Least Connections**

Least Connections 방식에 가중치 개념을 결합한 방식.

* 장점: 성능과 연결 수를 모두 고려
* 단점: 구현 복잡도 증가

<figure><img src="../../.gitbook/assets/스크린샷 2025-05-07 23.34.49.png" alt=""><figcaption></figcaption></figure>

***

### **5. Least Response Time**

서버의 평균 응답 시간을 기준으로 가장 빠른 서버로 요청을 전달한다.

* 장점: 사용자 응답 속도 최적화
* 단점: 응답 시간 측정 비용 발생

<figure><img src="../../.gitbook/assets/스크린샷 2025-05-07 23.35.17.png" alt=""><figcaption></figcaption></figure>

***

### **6. IP Hash**

클라이언트 IP를 해싱하여 고정된 서버에 요청을 전달한다.

* 장점: 세션 유지에 유리
* 단점: 부하 분산이 고르지 않을 수 있음

<figure><img src="../../.gitbook/assets/스크린샷 2025-05-07 23.35.45.png" alt=""><figcaption></figcaption></figure>

***

## Forward Proxy vs Reverse Proxy vs Load Balancer

<table><thead><tr><th width="158.8671875">종류</th><th width="363.109375">설명</th><th>위치</th></tr></thead><tbody><tr><td>Forward Proxy</td><td>클라이언트가 요청을 보낼 때 프록시가 대신 요청</td><td>클라이언트 앞단</td></tr><tr><td>Reverse Proxy</td><td>클라이언트 요청을 받아 서버로 대신 전달</td><td>서버 앞단</td></tr><tr><td>Load Balancer</td><td>Reverse Proxy 중 하나, 트래픽을 여러 서버에 분산</td><td>서버 앞단</td></tr></tbody></table>

***

## When to Use Load Balancing?

* 트래픽 증가에 유연하게 대응하고 싶을 때
* 무중단 배포(Blue-Green, Canary)를 고려할 때
* 장애 대비 자동 Failover가 필요할 때
