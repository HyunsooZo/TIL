---
description: written by Joshua Bloch
---

# 🦾 Effective Java 3/E

### 짧은 리뷰

읽는 중.. (2024/08/18\~)

### 책 정리

<details>

<summary>Item 1 : 생성자 대신 정적 팩토리 메서드를 고려하라</summary>

* 핵심: \
  \- 객체 생성 시 생성자 대신 정적 팩토리 메서드를 사용하면 유연성이 증가하고, \
  &#x20;  명확한 이름을 통해 객체 생성 과정이 더 명확해질 수 있음.
* 이점:\
  \- 명명 가능: 생성자보다 명확한 이름을 가질 수 있ㅁ.\
  \- 불필요한 객체 생성 방지: 동일한 인스턴스를 재사용 가ㅇ.\
  \- 반환 타입의 하위 클래스 객체를 반환할 수 있음.
* 예시:

```java
public class User {
    private String name;
    private int age;

    // 생성자 대신 사용되는 정적 팩토리 메서드
    public static User createWithNameAndAge(String name, int age) {
        User user = new User();
        user.name = name;
        user.age = age;
        return user;
    }

    // private 생성자
    private User() {}
}
```

</details>

<details>

<summary>Item 2 : 생성자에 매개변수가 많다면 빌더를 고려하라</summary>

* 핵심: \
  \- 매개변수가 많을 때는 생성자 대신 빌더 패턴을 사용해 가독성과 유지보수성을 높일 수 있음.
* 이점:\
  \- 매개변수가 많은 경우에도 코드가 깔끔하게 유지됨.\
  \- 선택적 매개변수 처리에 유용함.\
  \- 코드의 가독성을 크게 향상시킬 수 있음.
* 예시:

```java

public class NutritionFacts {
    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        // 필수 매개변수
        private final int servingSize;
        private final int servings;

        // 선택적 매개변수 - 기본값으로 초기화
        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        public Builder calories(int val) {
            calories = val;
            return this;
        }

        public Builder fat(int val) {
            fat = val;
            return this;
        }

        public Builder sodium(int val) {
            sodium = val;
            return this;
        }

        public Builder carbohydrate(int val) {
            carbohydrate = val;
            return this;
        }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }
}
```

</details>
