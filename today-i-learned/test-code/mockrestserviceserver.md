---
description: >-
  MockRestServiceServer는 RestTemplate이 실제로 HTTP 요청을 보내는 대신에 테스트에서 응답을 제어할 수 있도록
  해준다. 즉, RestTemplate을 모킹하여 실제 외부 서비스에 요청을 보내지 않고도 다양한 시나리오를 테스트할 수 있게 돕는 도구.
---

# ❕ MockRestServiceServer

#### RestTemplate Mocking하는 방법

테스트에서 `RestTemplate`을 모킹하여 원하는 응답을 반환하게 하려면, \
\``` MockRestServiceServer` ``를 사용해야 해야 한다.

1. **MockRestServiceServer 생성**:
   * `` `MockRestServiceServer.createServer(restTemplate);` ``을 통해 \
     \``` RestTemplate` ``과 연결된 모킹 서버를 생성해. 이 서버는 `restTemplate`이 HTTP 요청을 보낼 때 가로채서 미리 설정한 응답을 반환하도록 한다.
2. **expect 설정**:
   * `` `mockServer.expect(MockRestRequestMatchers.anything())` ``는 `restTemplate`이 어떤 요청이든 보내면 이를 가로채겠다는 의미이다.
   * `` `andRespond(MockRestResponseCreators.withSuccess(...))` ``는 가로챈 요청에 대해 어떤 응답을 반환할지 설정하는 부분이다. 아래 예시코드에서는 HTTP 200 상태 코드와 함께 JSON 형식의 응답을 반환하도록 설정하고 있다.

```java
class ExampleTest {

RestTemplate restTemplate;
MockRestServiceServer mockServer;
ExampleService exampleService;

@BeforeEach
void setUp() {
    restTemplate = new RestTemplate();
    mockServer = MockRestServiceServer.createServer(restTemplate);
}

@Test
@DisplayName("깃북 업로드용 테스트")
void test() {
        // 요청값은 헤더 등을 설정하기 까다로우므로 아래와 같이 .anyThing()으로
    mockServer.expect(MockRestRequestMatchers.anything())
            .andRespond(MockRestResponseCreators.withSuccess(
                    """
                            {
                            "id":1,
                            "resultCode":"0",
                            "resultMessage":"",
                            "body":{
                                "shortLink":"https://test.com"
                                 }
                            }
                            """.trim(), APPLICATION_JSON));

    assertThatCode(() -> {
        Optional<GetRedirectResponse> response = exampleService.getLink("test");
        assertThat(response).isNotNull();
        response.ifPresent(
                r -> {
                    assertThat(r.getBody().getShortLink())
                    .isEqualTo("https://test.com");
                }
        );
    }).doesNotThrowAnyException();
}
```
