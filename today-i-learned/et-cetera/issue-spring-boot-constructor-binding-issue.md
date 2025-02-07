---
description: >-
  작년에 회사 프로젝트의 스프링부트 버전 업그레이드를 진행하던 도중 마주쳤던 작은 이슈를 정리해보았다. 이제는 습관처럼 파라미터 명을
  선언하지만 (컴파일 옵션을 통해 해결할 수도 있다.) 혹시라도 다시 마주쳤을 때 조금 더 빠르게 대처할 수 있도록 기록 해 본다.
---

# 🤯 Issue : Spring Boot Constructor Binding Issue

## Issue

### Symptoms

생성자 바인딩(Constructor binding)이 매개변수 이름을 가져오지 못함.

### Conditions

* 스프링 부트 3.2 또는 스프링 프레임워크 6.1 이상 사용
* 인텔리제이에서 빌드 수행
* 인텔리제이 내장 빌드 도구 사용

### Console Error Message

```plaintext
Cannot resolve parameter names for constructor ㅇㅇㅇ.class
```

## Solution

### Explicitly Defining Parameter Names

개인적으로는`@RequestParam("key")`를 사용하여 \
요청 매개변수를 명시적으로 지정하는 방식이 가장 확실한 해결책이라고 본다.

(아래 `-parameters` 옵션 추가도 좋지만 명시적인게 제일 좋다고 생각한다.)

```java
@PostMapping("/articles")
public ResponseEntity<String> createArticle(
    @RequestParam("title") String title, // 명시적으로 파라미터 명 선언
    @RequestParam("content") String content
) {
    return ResponseEntity.ok("Success");
}
```

이 방식은 추가적인 컴파일러 설정 없이도 매개변수 이름을 명확하게 지정할 수 있다.

### Alternative: Adding `-parameters` Compiler Option

컴파일러 옵션을 추가하여 바이트코드에 매개변수 이름을 포함하는 방법도 가능하다.

**IntelliJ IDEA**

1. `Settings` → `Java Compiler` 검색
2. `Additional command line parameters` 입력란에 `-parameters` 추가

**Gradle (Kotlin DSL)**

```kotlin
tasks.withType<JavaCompile> {
    options.compilerArgs.add("-parameters")
}
```

**Gradle (Groovy DSL)**

```groovy
tasks.withType(JavaCompile).configureEach {
    options.compilerArgs.add("-parameters")
}
```

## If It Still Doesn't Work

빌드를 다시 수행해야 할 수도 있다. \
인텔리제이의 내장 빌드 도구는 기본적으로 증분 빌드를 수행하는데, \
증분 빌드는 변경된 부분만 다시 빌드하기 때문에 특정 설정이 반영되지 않을 가능성이 있다.

## Cause

### Class Removal in Spring Framework

스프링 부트 3.2부터 더 이상 바이트코드를 파싱하여 매개변수 이름을 추론하지 않는다.\
아래 스프링 릴리즈 노트를 참고하자!

### **Spring Boot 3.2 Release Notes**

> The version of Spring Framework used by Spring Boot 3.2 no longer attempts to deduce parameter names by parsing bytecode. If you experience issues with dependency injection or property binding, you should double check that you are compiling with the -parameters option.

### **Spring Framework 6.1 Release Notes**

> LocalVariableTableParameterNameDiscoverer has been removed in 6.1.

## Conclusion

스프링 부트 3.2 및 스프링 프레임워크 6.1 이상에서는 생성자 바인딩 시 `-parameters` 옵션이 필수로 요구된다. \
코딩 스타일에 따라 갈리겠지만, 명확한 매개변수 바인딩을 원한다면 \
`@RequestParam("key")` 방식이 더 직관적이며, 추가적인 설정 없이도 안정적인 동작을 보장한다고 생각한다.\
또한 빌드 도구의 특성상 설정이 반영되지 않을 수도 있으므로, 수정 후 전체 리빌드를 수행하는 것이 좋다!
