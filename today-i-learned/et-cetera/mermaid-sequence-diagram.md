---
description: Mark Down 기반의 코드 블럭 다이어그램 툴
---

# 🧜 Mermaid Sequence Diagram



## Github 및 LiveEditor

[Mermaid Official Github](https://github.com/mermaid-js/mermaid)     [Mermiad Live Editor](https://mermaid.live/)

## **Mermaid란**

* **정의**: 코드를 기반으로 여러 종류의 다이어그램을 그릴 수 있게 해주는 오픈 소스 도구.
* **장점**: 직관적인 문법, 빠른 다이어그램 생성, 다양한 다이어그램 지원.
* **적용 분야**: 기술 문서화, 아키텍처 다이어그램, 워크플로우 시각화 등.

## **적용하게 된 이유**

* 회사에서 별도 워크플로우 문서 없이 작업을 하다보니 헷갈리는 경우가 많았다. \
  검색 하다가 코드로 워크플로우를 작성할 수 있다고 하여 실제 업무에 적용해보았다.&#x20;
* 실제로 어느정도의 복잡도 조차 커버할 정도로 뛰어났으며, 문법은 굉장히 간단했다.
* 다만 플로우가 "많이" 복잡하다면 다른 툴을 고려해보는 것이 나을 것 같다.

## **Mermaid 기본문법**

### 1. **플로우차트 (Flowchart)**

```mermaid
graph TD
  A[시작] --> B[프로세스]
  B --> C{조건}
  C -->|예| D[종료]
  C -->|아니오| E[다시 처리]
```

* **방향**:
  * `TD` (위에서 아래로)
  * `LR` (왼쪽에서 오른쪽으로)
  * `BT` (아래에서 위로)
  * `RL` (오른쪽에서 왼쪽으로)
* **노드 모양**:
  * `A[텍스트]` - 사각형
  * `A((텍스트))` - 원형
  * `A{"텍스트"}` - 다이아몬드 (조건)

***

### 2. **시퀀스 다이어그램 (Sequence Diagram)**

```mermaid
sequenceDiagram
  participant Alice
  participant Bob
  Alice->>Bob: 안녕 Bob, 잘 지내?
  Bob-->>Alice: 잘 지내, 고마워!
  Alice->>Bob: 프로젝트는 어때?
  Bob-->>Alice: 잘 진행 중이야
```

* **참여자 정의**: `participant`로 참여자를 정의
* **화살표 종류**:
  * `->>`: 실선 화살표 (메시지)
  * `-->>`: 점선 화살표 (응답)



* &#x20;**조건문 (alt, opt, loop)**
  * **alt/else/end**: 조건문 블록을 구성해 시퀀스 흐름을 제어.
  * **opt**: 조건이 있을 때만 실행되는 선택적 블록.
  * **loop**: 반복되는 동작을 나타낼 때 사용.

```mermaid
sequenceDiagram
  participant User
  participant Server

  alt 성공적인 로그인
    User->>Server: 로그인 요청
    Server-->>User: 로그인 성공
  else 로그인 실패
    Server-->>User: 로그인 실패 메시지
  end
```



* **`->>+`** 와 **`->>-`**: 강조하여 특정 액션 시작/종료 표시.
* **Note**: 특정 구간에 설명을 추가.

```mermaid
sequenceDiagram
  participant User
  participant Server

  User->>Server: 데이터 요청
  Server-->>User: 응답

  Note over User: 요청이 완료됨

  Server->>+Database: 데이터 저장
  Server-->>-Database: 저장 완료
```



* **activate/deactivate**: 객체가 활성화/비활성화 상태임을 나타냄.

```mermaid
sequenceDiagram
  participant User
  participant Server

  activate User
  User->>Server: 데이터 요청
  deactivate User

  activate Server
  Server-->>User: 응답
  deactivate Server
```

***

### 3. **간트 차트 (Gantt Chart)**

```mermaid
gantt
  title 프로젝트 일정
  dateFormat  YYYY-MM-DD
  section 디자인
  작업 1           :done,    des1, 2024-01-01, 2024-01-10
  작업 2           :active,  des2, 2024-01-11, 2024-01-20
  section 개발
  작업 3           :des3,    2024-01-21, 2024-02-01
```

* **작업 상태**:
  * `done`: 완료된 작업
  * `active`: 진행 중인 작업
  * `des3`: 상태 없이 정의된 작업

***

### 4. **클래스 다이어그램 (Class Diagram)**

```mermaid
classDiagram
  class Animal {
    +String name
    +int age
    +makeSound()
  }
  class Dog {
    +String breed
    +bark()
  }
  Animal <|-- Dog
```

* **클래스 문법**:
  * `+`: Public 멤버
  * `-`: Private 멤버
  * `#`: Protected 멤버
* **상속 관계**:
  * `<|--`: 상속(일반화)

***

### 5. **ER 다이어그램 (ERD)**

```mermaid
erDiagram
  CUSTOMER ||--o{ ORDER : 주문한다
  ORDER ||--|{ LINE-ITEM : 포함한다
  CUSTOMER {
    string name
    string address
  }
  ORDER {
    int orderNumber
    date orderDate
  }
  LINE-ITEM {
    int quantity
    string productCode
  }
```

* **관계 유형**:
  * `||--||`: 일대일 관계
  * `||--o{`: 일대다 관계
  * `|{--o{`: 다대다 관계

***

### 6. **상태 다이어그램 (State Diagram)**

```mermaid
stateDiagram
  [*] --> Active
  Active --> Inactive
  Active --> Terminated
  Inactive --> [*]
```

* **상태 전환**:
  * `[Start] --> State`로 상태 전환을 정의.

***

### 7. **파이 차트 (Pie Chart)**

```mermaid
pie
  title 브라우저 사용량
  "Chrome" : 60
  "Firefox" : 30
  "Safari" : 10
```

* **제목**: `title`로 차트 제목을 설정.
