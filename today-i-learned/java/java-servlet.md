---
description: 자바 웹 개발의 핵심 기술 중 하나
---

# ☕ Servlet

## Servlet ?

`Servlet` 은 자바를 사용하여 웹 요청을 처리하고 동적인 웹 콘텐츠를 생성하는 서버 사이드 프로그램이다. \
서블릿은 자바로 작성된 클래스이며, **HTTP 프로토콜**을 기반으로 클라이언트의 요청을 받아 처리한 후 응답을 생성한다.

* **주요 역할**:
  * 클라이언트의 요청(Request) 수신
  * 요청 처리 및 비즈니스 로직 수행
  * 응답(Response) 생성 및 전송

## Servlet 아키텍처

서블릿 아키텍처는 `Servlet Container` 를 중심으로 동작한다.

* **서블릿 컨테이너**:
  * 서블릿의 생명주기 관리
  * 네트워크 통신 처리
  * 멀티스레딩 지원
* **서블릿**:
  * 클라이언트 요청 처리
  * 비즈니스 로직 수행
  * 응답 생성

## Servlet 의 동작 원리

### 서블릿 컨테이너

**서블릿 컨테이너**는 웹 서버 내에서 동작하며, 서블릿의 실행 환경을 제공한다.

* **주요 기능**:
  * 서블릿 생명주기 관리 (`init`, `service`, `destroy`)
  * 요청과 응답 객체 생성 (`HttpServletRequest`, `HttpServletResponse`)
  * 스레드 관리 및 동시성 처리

**대표적인 서블릿 컨테이너**:

* **Apache Tomcat**
* **Jetty**
* **WildFly (JBoss)**
* **GlassFish**

### 서블릿의 라이프사이클

서블릿의 생명주기는 다음과 같이 네 단계로 구성된다:

1. **로딩 및 인스턴스화**: 컨테이너가 서블릿 클래스를 메모리에 로딩하고 인스턴스를 생성한다.
2. **초기화 (`init` )**: 서블릿이 초기화되어 사용할 준비를 한다. 초기화 매개변수를 읽거나 리소스를 로딩할 수 있다.
3. **요청 처리 (`service` )**: 클라이언트의 각 요청마다 `service` 메서드가 호출되어 요청을 처리한다.
4.  **종료 (`destroy` )**: 컨테이너가 서블릿을 메모리에서 언로드하기 전에 `destroy` 메서드를 호출한다.

    <figure><img src="../../.gitbook/assets/스크린샷 2024-10-29 오후 9.03.57.png" alt=""><figcaption></figcaption></figure>

### 서블릿의 주요 메서드와 인터페이스

서블릿은 여러 인터페이스와 클래스를 통해 다양한 기능을 제공한다.

#### Servlet 인터페이스

* **주요 메서드**:
  * `init(ServletConfig config)`: 서블릿 초기화
  * `service(ServletRequest req, ServletResponse res)`: 요청 처리
  * `destroy()`: 서블릿 종료
  * `getServletConfig()`: 서블릿 설정 정보 반환
  * `getServletInfo()`: 서블릿 정보 반환

#### GenericServlet 클래스

* `Servlet` 인터페이스를 구현한 추상 클래스이다.
* 프로토콜에 독립적인 서블릿을 만들 때 사용한다.
* `service()` 메서드를 추상 메서드로 선언한다.

#### HttpServlet 클래스

* `GenericServlet`을 상속받아 HTTP 프로토콜을 지원하는 서블릿이다.
* **주요 메서드**:
  * `doGet()`: GET 요청 처리
  * `doPost()`: POST 요청 처리
  * `doPut()`: PUT 요청 처리
  * `doDelete()`: DELETE 요청 처리
  * `doHead()`, `doOptions()`, `doTrace()` 등

## 서블릿의 활용 사례

* **MVC 패턴의 컨트롤러**:
  * 서블릿을 컨트롤러로 사용하여 모델과 뷰를 연결한다.
* **RESTful API 엔드포인트**:
  * 서블릿을 통해 API 요청을 처리한다.
* **필터 및 리스너**:
  * 공통 로직 처리 및 이벤트를 감지한다.
* **파일 업로드/다운로드 처리**
* **웹소켓 통신 지원**
