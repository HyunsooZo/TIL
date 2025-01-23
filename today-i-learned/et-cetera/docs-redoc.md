---
description: >-
  Redoc은 Swagger와 동일한 OpenAPI 문서화 도구 중 하나이다. Swagger보다 좀 더 깔끔한 UI를 제공하며 Swagger
  와 마찬가지로 문서화에 코드를 추가하지 않아도 자동으로 생성해준다.
---

# 📄 Docs : Redoc

## Redoc?

이 포스팅에서는 OpenAPI 명세서 기반 문서 자동 생성 도구인 Redoc을 소개하고, \
Swagger와 함께 사용하는 방법에 대해 설명하고자 한다. \
Swagger UI는 널리 사용되는 API 문서화 도구이지만, \
Redoc은 깔끔하고 가독성이 뛰어난 디자인으로 차별점을 가진다. \
두 도구를 적절히 활용하면 API 문서의 활용도를 더욱 높일 수 있다.

이번에 회사에서 새로운 프로젝트의 api문서를 만들 일이 생겨 산출물로 사용하고자 적용 해보았다.

## Why Redoc?

Redoc은 다음과 같은 장점을 가진다.

* **뛰어난 가독성:** Redoc은 3단 레이아웃을 사용하여 API 정보를 보기 쉽게 제공한다.
* **반응형 디자인:** 다양한 기기에서 최적화된 화면을 제공한다.
* **빠른 렌더링:** 대규모 API 문서도 빠르게 렌더링한다.
* **SEO 친화적:** 검색 엔진 최적화가 용이하다.

## Using Redoc with Swagger

Swagger와 Redoc은 함께 사용할 수 있다. \
Swagger UI는 API를 테스트하고 탐색하는 데 유용하며, Redoc은 API 문서를 보기 좋게 보여주는 데 효과적이다. \
두 도구를 조합하여 API 문서의 기능성과 시각적인 장점을 모두 가져갈 수도 있다!

아래는 Swagger와 Redoc을 함께 사용하는 일반적인 방법이다.

1. **OpenAPI 명세서 작성:** \
   Swagger Editor 또는 다른 도구를 사용하여 OpenAPI (Swagger) 명세서 (YAML 또는 JSON)를 작성한다.
2. **Swagger UI 통합:** \
   Swagger UI를 프로젝트에 통합하여 API를 테스트하고 탐색할 수 있도록 한다.
3. **Redoc 통합:** \
   Redoc을 프로젝트에 통합하여 API 문서를 시각적으로 보기 좋게 제공한다.

## Spring Boot with Redoc

### build.gradle

```gradle
dependencies {
    implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.x.x' 
}
```

### Java Code

```java
@Tag(name = "API 문서", description = "API 문서")
@Controller
public class DocumentController {
    @GetMapping("/api-docs")
    public String getDocument() {
        return "api-docs";
    }
} 

// OpenApiCustomizer 를 통해 /v3/api-docs 의 json 데이터를 커스터 마이징 할 수 있다.
// 나는 메뉴에 노출될 api의 정렬을 내가 지정한 Tag의 이름으로 정렬하고, 
// 본문 또한 같은 이름으로 정렬하는 방식의 커스텀을 진행했다. 
@Configuration 
public class OpenApiConfig {

    @Bean
    public OpenApiCustomizer sortPathsByTags() {
        return openApi -> {
            // 기존 Paths 가져오기
            Paths originalPaths = openApi.getPaths();

            if (originalPaths != null) {
                // 경로를 "tags" 기준으로 정렬
                Map<String, PathItem> sortedPaths = originalPaths.entrySet().stream()
                        .sorted((entry1, entry2) -> {
                            // 첫 번째 경로의 첫 번째 태그
                            String tag1 = getFirstTag(entry1.getValue());
                            // 두 번째 경로의 첫 번째 태그
                            String tag2 = getFirstTag(entry2.getValue());
                            return tag1.compareToIgnoreCase(tag2); // 태그 이름 기준으로 정렬
                        })
                        .collect(Collectors.toMap(
                                Map.Entry::getKey,            // 경로 키 (/v1/ticketing/cancel)
                                Map.Entry::getValue,          // 경로 값 (PathItem)
                                (oldValue, newValue) -> oldValue, // 중복 키 처리 (없어야 함)
                                LinkedHashMap::new            // 순서를 유지하기 위한 LinkedHashMap
                        ));

                Paths newPaths = new Paths();
                sortedPaths.forEach(newPaths::addPathItem);

                openApi.setPaths(newPaths);
            }
        };
    }

    private String getFirstTag(PathItem pathItem) {
        return pathItem.readOperations()
                .stream()
                .flatMap(operation -> operation.getTags().stream()) // 태그 리스트 추출
                .findFirst() // 첫 번째 태그 선택
                .orElse(""); // 태그가 없으면 빈 문자열 반환
    }

}

```

### HTML

나는 Html 에서도 앱 로고를 넣고 불필요한 텍스트를 display: none 하는 방식으로 커스텀했다. \
물론 이외에도 css와 script를 활용한 다양한 커스텀이 가능하다!!

```html
<!DOCTYPE html>
<html>
<head>
    <title>API Documentation</title>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/redoc/bundles/redoc.standalone.css" />
    <style>
        .menu-content {
            position: relative;
        }
        .menu-content .logo-container {
            display: flex;
            justify-content: center;
            align-items: center;
            margin-bottom: 20px;
            margin-top: 20px;
        }
        .menu-content .logo-container img {
            width: 100px;
            height: 100px;
            border-radius: 50%;
            object-fit: cover;
        }
        .api-info {
            display: none;
        }
    </style>
</head>
<body>
<!-- Redoc API 문서 -->
<redoc spec-url="/v3/api-docs"></redoc>

<script src="https://cdn.jsdelivr.net/npm/redoc/bundles/redoc.standalone.js"></script>
<script>
    // Redoc DOM 생성 대기 후 로고 삽입 
    document.addEventListener('DOMContentLoaded', function() {
        const observer = new MutationObserver(() => {
            const menuContent = document.querySelector('.menu-content');
            if (menuContent && !document.querySelector('.logo-container')) {
                const logoContainer = document.createElement('div');
                logoContainer.className = 'logo-container';
                logoContainer.innerHTML = '<img src="/logo.png" alt="Company Logo">';
                menuContent.prepend(logoContainer);
                observer.disconnect();
            }
        });
        observer.observe(document.body, { childList: true, subtree: true });
    });
</script>
</body>
</html>
```



## Result&#x20;

<figure><img src="../../.gitbook/assets/스크린샷 2025-01-23 오후 11.25.27.png" alt=""><figcaption></figcaption></figure>

## Conclusion

Redoc은 Swagger UI와 함께 사용하여 API 문서의 품질을 향상시키는 데 유용한 도구이다. \
또한 뛰어난 가독성을 제공하여 개발자들이 API를 더 쉽게 이해하고 사용할 수 있도록 돕는다.

이 외에도 Redoc의 다양한 설정 옵션들을 활용하여 문서의 스타일을 커스터마이징할 수 있다. \
필요에 따라 Redoc의 공식 문서를 참고하여 더 자세한 내용을 확인하면 조금 더 나은 문서를 작설 할 수 있다!

엑셀이나 워드파일을 통한 문서보다 최신화에 훨씬 유리하고 코드 몇 줄을 추가하는 것만으로도\
문서를 생성할 수 있다는 점은 개발자에게 좀 더 개발에만 집중할 수 있게 해주는 생산적인 라이브러리라고 생각한다.

만약 백엔드 개발자로서 api문서 작성을 앞두고 있다면 꼭 사용 해보길 추천한다.
