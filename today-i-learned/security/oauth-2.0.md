---
description: a.k.a Open Authorization 2.0, OAuth2
---

# 🚪 OAuth 2.0

## 머릿말

직장 동료들과 진행하는 토이프로젝트에서 인증/인가 에 대한 작업을 맡게되었다.\
작업에 들어가기 전 사용하게 될 OAuth 2.0 에 대해 알아보았다.\
결과적으로 우리 프로젝트에서는 외부 소셜 미디어 앱(예: Google, Apple 등) \
로그인을 구현 해야하므로 **Authorization Code Grant (인가 코드 승인 방식)**을 사용하려 한다.

> **이유**\
> 외부 애플리케이션이 액세스 토큰을 안전하게 받을 수 있도록 설계되어있다. \
> 이 방식에서는 사용자가 직접 애플리케이션에 비밀번호를 입력하는 대신, \
> **소셜 미디어의 인증 페이지**를 통해 로그인하고, \
> 그 결과로 받은 인가 코드를 클라이언트가 사용해 액세스 토큰을 받아오는 구조이다.

~~**Implicit Grant**:~~\
~~주로 클라이언트가 공개된 환경에 있을 때(예: 브라우저 기반 앱) 사용되며,~~ \
~~보안성이 낮아 로그인과 같은 민감한 데이터 처리에는 적합하지 않다.~~

~~**Resource Owner Password Credentials Grant**:~~ \
~~클라이언트가 사용자의 사용자명/비밀번호를 직접 받는 방식이라,~~ \
~~소셜 로그인과 같은 시나리오에서는 적합하지 않다.~~

~~**Client Credentials Grant**:~~ \
~~애플리케이션 자체의 권한으로 API에 접근하는 방식이므로,~~ \
~~주로 사용자 권한 승인이 필요 없는 내부 서버 간 통신에 사용된다.~~

## OAuth 2.0 **개념**

인증을 위한 개방형 표준 프로토콜

Third-Party 프로그램에게 리소스 소유자를 대신하여 리소스 서버에서 제공하는 자원에 대한 \
접근 권한을 위임하는 방식을 제공한다. 구글, 페이스북, 카카오, 네이버 등에서 제공하는 간편 로그인 기능도 \
OAuth2 프로토콜 기반의 사용자 인증 기능을 제공한다고 한다.

## **역할**&#x20;

### **Resource Owner**

본인의 정보에 접근할 수 있는 자격을 승인하는 주체. (예:구글 로그인을 할 사용자) \
Resource Owner는 클라이언트를 인증(Authorize)하는 역할을 수행하고, \
인증이 완료되면 동의를 통해 권한 획득 자격(Authorization Grant)을 클라이언트에게 부여하는 서버

### **Resource Server**

Resource Owner 의 정보가 저장되어 있는 서버

### &#xD;**Authorization Server**

인증/인가를 수행하는 서버로 클라이언트의 접근 자격을 확인하고 \
Access Token을 발급하여 권한을 부여하는 역할을 수행하는 서버

## **인증 과 인가**

### **Authentication**&#x20;

접근 권한이 있는지 검증

### **Authorization**&#x20;

자원에 접근 할 권한을 부여하고 리소스 접근 권한이 담긴 Access Token을 제공

### **인증과 인가에 대한 간단한 비교**

<table><thead><tr><th width="94">구분</th><th width="226">인증 (Authentication)</th><th>인가 (Authorization)</th></tr></thead><tbody><tr><td>목적</td><td>사용자의 신원을 확인</td><td>사용자가 특정 작업을 수행할 수 있는지 확인</td></tr><tr><td>질문</td><td>"이 사람이 정말 본인인가?"</td><td>"이 사람이 이 작업을 할 수 있는가?"</td></tr><tr><td>예시</td><td>로그인, 2단계 인증, 생체인증</td><td>관리자 페이지 접근 권한, 파일 읽기/쓰기 권한</td></tr><tr><td>순서</td><td>시스템 접근의 첫 단계</td><td>인증 후 사용자의 권한을 확인하는 단계</td></tr><tr><td>결과</td><td>사용자가 시스템에 접근 가능해짐</td><td>사용자가 특정 리소스에 대한 접근 가능 여부 결정</td></tr></tbody></table>

## **권한 부여 방식**

OAuth 2.0 프로토콜에서는 다양한 클라이언트 환경에 적합하도록 \
권한 부여 방식에 따른 프로토콜을 아래 4가지 종류로 구분하여 제공한다.\
참고로 하단의 모든 이미지는 이전에 알아봤던 \
[Mermaid Diagram Live Editor](../et-cetera/mermaid-sequence-diagram.md) 를 사용해 만들어보았다.

### Authorization Code Grant <a href="#id-1-authorization-code-grant" id="id-1-authorization-code-grant"></a>

권한 부여 승인을 위해 자체 생성한 Authorization Code를 전달하는 방식으로 많이 쓰이고 \
기본이 되는 방식이다. 간편 로그인 기능에서 사용되는 방식으로 클라이언트가 사용자를 대신하여 \
특정 자원에 접근을 요청할 때 사용되는 방식이며 보통 타사의 클라이언트에게 보호된 자원을 \
제공하기 위한 인증에 사용된다. Refresh Token의 사용이 가능한 방식이다.

<figure><img src="../../.gitbook/assets/스크린샷 2024-10-26 오후 10.04.09.png" alt=""><figcaption><p>권한 부여 승인 코드 방식</p></figcaption></figure>

### Implicit Grant  <a href="#id-2-implicit-grant" id="id-2-implicit-grant"></a>

자격증명을 안전하게 저장하기 힘든 클라이언트(예: JavaScript등의 스크립트 언어를 사용한 브라우저)에게 \
최적화된 방식이다.

암시적 승인 방식에서는 권한 부여 승인 코드 없이 바로 `Access Token`이 발급 된다. \
`Access Token`이 바로 전달되므로 누출의 위험을 방지하기 위해 만료기간을 짧게 설정하여 전달한다.\
`Refresh Token` 사용이 불가능한 방식이며, 이 방식에서 권한 서버는 `client_secret`을 사용해 \
클라이언트를 인증하지 않는다. \
즉 `Access Token`을 획득하기 위한 절차가 간소화되기에 응답성과 효율성은 높아지지만 \
`Access Token`이 URL로 전달된다는 단점이 존재하는 방식이다.

<figure><img src="../../.gitbook/assets/스크린샷 2024-10-26 오후 10.08.50.png" alt=""><figcaption><p>암묵적 승인 방식</p></figcaption></figure>

### Resource Owner Password Credentials Grant <a href="#id-3-resource-owner-password-credentials-grant" id="id-3-resource-owner-password-credentials-grant"></a>

간단히 username, password로 `Access Token`을 받는 방식

클라이언트가 타사의 외부 프로그램일 경우에는 _**당연히**_ 이 방식을 적용하면 안된다. \
내부 서비스에서 제공하는 어플리케이션일 경우에만 사용되는 인증 방식이라고 한다. \
`Refresh Token`의 사용도 물론 가능하다.

<figure><img src="../../.gitbook/assets/스크린샷 2024-10-26 오후 10.15.18.png" alt=""><figcaption><p>자원소유자 자격증명 승인 방식</p></figcaption></figure>

### Client Credentials Grant <a href="#id-4-client-credentials-grant" id="id-4-client-credentials-grant"></a>

OAuth2의 권한 부여 방식 중 가장 간단한 방식으로 \
클라이언트 자신이 관리하는 리소스 혹은 권한 서버에 해당 클라이언트를 위한 \
제한된 리소스 접근 권한이 설정되어 있는 경우 사용된다. \
이 방식은 자격증명을 안전하게 보관할 수 있는 클라이언트에서만 사용되어야 하며, \
`Refresh Token`은 사용할 수 없다.

<figure><img src="../../.gitbook/assets/스크린샷 2024-10-26 오후 10.17.37.png" alt=""><figcaption></figcaption></figure>
