---
description: 오늘은 개발자라면 반드시 이해하고 넘어가야 할 개념 중 하나인 CORS에 대해 알아보았다.
---

# 🤞 Spring : CORS

## CORS?

CORS(Cross-Origin Resource Sharing)는 브라우저에서 보안을 위해 적용하는 \
동일 출처 정책(Same-Origin Policy, SOP)을 확장하기 위해 만들어진 메커니즘이다.

브라우저는 기본적으로 **출처(origin)가 다른 리소스에 대한 요청을 차단**한다. \
여기서 출처란, **프로토콜 + 호스트 + 포트**를 모두 포함한다. 예를 들어 다음 두 URL은 출처가 다르다.

* `https://example.com:443`
* `http://example.com:80`

만약 웹 애플리케이션이 API 요청을 이런 다른 출처로 보내려 한다면, \
**브라우저는 요청을 차단하거나, 사전 검사를 통해 접근 허용 여부를 확인**하게 된다. \
이 모든 과정을 총칭하여 CORS라고 한다.

***

## Why does CORS exist?

과거에는 보안상 큰 문제였던 **CSRF (Cross-Site Request Forgery)** 공격을 막기 위해 \
**Same-Origin Policy(SOP)** 라는 제약이 브라우저에 기본적으로 적용되었다.\
하지만 현대 웹 애플리케이션은 프론트엔드와 백엔드가 서로 다른 도메인에서 동작하는 SPA 구조가 보편화되었고, \
이러한 환경에서는 **SOP만으로는 융통성 있게 요청을 허용할 수 없었다.**

그래서 등장한 것이 CORS이다. CORS는 SOP를 기반으로, \
서버가 명시적으로 허용한 출처에 대해서만 교차 출처 요청을 허용하는 보안 모델이다. \
즉, **브라우저가 강제하는 것이 아니라 서버가 허용 여부를 제어**한다.

***

## How does CORS work?

CORS는 기본적으로 **브라우저-서버 간의 HTTP 헤더 교환**을 통해 작동한다.

#### 1. Simple Request

단순 요청은 다음 조건을 모두 만족해야 한다.

* 메서드: `GET`, `POST`, `HEAD`
* 헤더: 커스텀 헤더 없이, `Accept`, `Accept-Language`, `Content-Language`, `Content-Type` \
  (단 `Content-Type`은 \
  `application/x-www-form-urlencoded`, \
  `multipart/form-data`, \
  `text/plain` 중 하나여야 함)

이 경우, 브라우저는 요청 시 `Origin` 헤더를 포함하고, 서버는 응답에 다음 헤더를 포함해야 한다.

```http
Access-Control-Allow-Origin: https://your-frontend.com
```

브라우저는 이 헤더 값을 통해 요청이 허용되는지 판단한다.

***

## Preflight Request

단순 요청 조건을 만족하지 않으면, 브라우저는 본 요청을 보내기 전에 **사전 요청(preflight request)** 을 보낸다.\
이 요청은 `OPTIONS` 메서드를 사용하며, 다음과 같은 헤더를 포함한다.

```http
OPTIONS /api/resource
Origin: https://your-frontend.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: X-Custom-Header
```

서버는 응답으로 다음과 같은 헤더를 반환해야 한다.

```http
Access-Control-Allow-Origin: https://your-frontend.com
Access-Control-Allow-Methods: POST
Access-Control-Allow-Headers: X-Custom-Header
Access-Control-Max-Age: 3600
```

이 사전 요청이 성공하면 브라우저는 본 요청을 전송한다.

***

## Credential Request

쿠키, 인증 토큰, 세션 등 인증 정보가 포함된 요청을 **Credential Request**라고 한다. \
이 경우 다음 조건을 반드시 지켜야 한다.

*   클라이언트 요청 시:

    ```js
    fetch("https://api.example.com/data", {
      credentials: "include" // or 'same-origin'
    })
    ```
*   서버 응답 시:

    ```http
    Access-Control-Allow-Credentials: true
    Access-Control-Allow-Origin: https://your-frontend.com
    ```

이 경우에는 절대 `Access-Control-Allow-Origin: *` 와일드카드를 사용할 수 없다.

***

## Practical in Spring

#### Global CORS 설정 예시

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")
                .allowedOrigins("https://your-frontend.com")
                .allowedMethods("GET", "POST", "PUT", "DELETE")
                .allowCredentials(true)
                .maxAge(3600);
    }
}
```

#### 특정 컨트롤러에만 적용 시

```java
@CrossOrigin(origins = "https://your-frontend.com", allowCredentials = "true")
@RestController
@RequestMapping("/api/data")
public class DataController {
    // ...
}
```

***

## Things to Keep in Mind

* CORS는 **보안 기능이라기보단, 브라우저의 정책**이다. Postman, curl, 서버 간 통신에는 적용되지 않는다.
* 서버 설정이 잘못되면 브라우저 콘솔에 `CORS policy has been blocked` 같은 에러가 뜨지만, \
  **응답조차 받지 못한다**.
* CORS 설정은 프론트에서 아무리 조절해도 소용없고, **항상 서버가 허용해줘야 한다.**
* **배포 환경에서 origin은 정확하게 명시**하고, `*` 와일드카드는 최소한으로만 사용하자.

***

## Conclusion

CORS는 단순한 보안 제약이 아니라, 현대의 클라이언트-서버 아키텍처에서 꼭 필요한 메커니즘이다. \
프론트와 백이 분리된 구조에서 **브라우저가 안전하게 교차 출처 요청을 수행할 수 있도록 해주는 핵심 역할**을 한다.

Spring 기반 API 서버를 운영하는 경우라면, **Spring WebMvcConfigurer를 통한 전역 설정**과 \
컨트롤러 단위의 세밀한 설정을 적절히 병행해야 한다.\
또한 인증 토큰을 사용하는 경우에는 `Credential Request`에 대한 이해가 꼭 필요하다.
