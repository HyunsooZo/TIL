---
description: >-
  Web Server와 WAS(Web Application Server)는 웹 애플리케이션의 동작과 서비스 제공에 필수적인 요소이다. 두
  컴포넌트는 웹 환경에서 중요한 역할을 하며, 각각의 기능과 특성이 다르다
---

# 🖇️ Arch : WebServer & Was

## Web Server?

Web Server는 HTTP 요청을 처리하고 정적 컨텐츠(HTML, CSS, JavaScript 파일 등)를 \
클라이언트(브라우저)에게 제공하는 서버이다. \
Apache HTTP Server, NGINX, Microsoft IIS 등이 대표적인 Web Server이다.

**주요 특징:**

* **정적 컨텐츠 제공**: 정적 파일을 효율적으로 제공함.
* **빠른 응답 속도**: 단순한 요청-응답 구조로 빠른 처리 가능.
* **로드 밸런싱**: 다수의 서버 간 부하를 분산하여 트래픽을 효율적으로 관리함.

## WAS(Web Application Server)?

WAS는 동적 컨텐츠를 생성하고 비즈니스 로직을 처리하는 서버이다. \
Java 기반의 경우 Apache Tomcat, JBoss, WebLogic 등이 대표적이다.

**주요 특징:**

* **동적 컨텐츠 처리**: 사용자 요청에 따라 동적으로 HTML, JSON 등의 응답을 생성.
* **비즈니스 로직 실행**: 애플리케이션의 핵심 비즈니스 로직을 처리.
* **JSP/Servlet 처리**: Java Servlet과 JSP를 실행할 수 있는 환경 제공.
* **데이터베이스 연동**: 백엔드 로직에서 데이터베이스와의 상호작용을 지원.

## Difference Between Web Server  & WAS

<table><thead><tr><th width="137">구분</th><th width="278">Web Server</th><th>Web Application Server</th></tr></thead><tbody><tr><td><strong>주요 기능</strong></td><td>정적 컨텐츠 제공</td><td>동적 컨텐츠 생성 및 비즈니스 로직 실행</td></tr><tr><td><strong>대표 제품</strong></td><td>Apache HTTP Server, NGINX</td><td>Apache Tomcat, JBoss, WebLogic</td></tr><tr><td><strong>성능</strong></td><td>고속의 정적 파일 처리</td><td>비즈니스 로직 실행으로 인해 상대적으로 성능 부담 증가</td></tr><tr><td><strong>역할</strong></td><td>요청을 받아 클라이언트에게 파일 제공</td><td>클라이언트 요청에 따라 동적 응답 생성 및 백엔드 로직 실행</td></tr></tbody></table>

## Cooperation Between Web Server & WAS

현대 웹 애플리케이션 환경에서는 Web Server와 WAS가 함께 사용되는 경우가 많다. \
Web Server는 정적 자원을 처리하고, WAS는 동적 요청을 처리하는 구조로 두 시스템이 협력하여 \
서버의 부하를 분산시키고 성능을 최적화한다.

<figure><img src="../../.gitbook/assets/스크린샷 2024-11-10 오후 4.13.23.png" alt=""><figcaption></figcaption></figure>
