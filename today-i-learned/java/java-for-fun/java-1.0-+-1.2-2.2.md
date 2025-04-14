---
description: >-
  1.0 + 1.2 = 2.2 일까? 프로그래밍을 배운 사람이라면 당당하게 아니오 라고 말하고 그 이유를 설명할 수 있어야한다!!  왜 그런지
  살펴보자.
---

# ➕ Java : 1.0 + 1.2 = 2.2 ?

## Introduction

프로그래밍을 하다 보면 간단한 소수 덧셈조차 예상과 다르게 동작하는 경우가 있다.\
그 대표적인 예가 `1.0 + 1.2 == 2.2` 가 `false`가 되는 현상이다.\
이 글에서는 해당 현상의 원인을 부동소수점 표현 방식의 한계 관점에서 설명하고, \
개발자가 이를 어떻게 다뤄야 하는지 알아보았다.

***

## Floating-point Representation

컴퓨터는 숫자를 이진수로 저장하며, 실수는 `IEEE 754` 표준을 따른다.\
이 표준에서는 `float`와 `double` 자료형을 사용하며, 실수를 2진수로 근사하여 표현한다.

문제는 10진수로는 간단한 `1.2`조차도 2진수로는 무한 소수가 되어 정확히 표현할 수 없다는 점이다.

```
1.2 (10진수) ≈ 1.001100110011001100... (2진수)
```

이처럼 무한 반복되는 소수는 컴퓨터 내부에서 잘려나가고, 결국 **근사값**으로 저장된다.\
이로 인해 `1.0 + 1.2` 연산 결과는 정확한 `2.2`가 아닌, **아주 가까운 다른 값**이 된다.

***

## The Problem in Code

다음 자바 코드를 보자

```java
System.out.println(1.0 + 1.2); // 출력: 2.2로 보일 수 있음
System.out.println(1.0 + 1.2 == 2.2); // false
```

이유는 `1.0 + 1.2`의 결과가 `2.2`와 같지 않기 때문이다. 내부적으로 다음과 같이 보인다

```java
double sum = 1.0 + 1.2;
System.out.printf("%.20f\n", sum);
```

출력 결과

```
2.19999999999999995559
```

이는 부동소수점 근사 오차에 의한 정확한 수 비교 실패다.

***

## Solutions

### **1. Epsilon**

작은 오차 범위를 허용하는 방식으로 비교하면 문제를 피할 수 있다.

```java
double a = 1.0 + 1.2;
double b = 2.2;
double epsilon = 0.0000001;

if (Math.abs(a - b) < epsilon) {
    System.out.println("같다고 봄");
}
```

### **2. BigDecimal**

정확한 소수 계산이 필요한 경우 `BigDecimal` 클래스를 사용한다.

```java
BigDecimal a = new BigDecimal("1.0");
BigDecimal b = new BigDecimal("1.2");
BigDecimal result = a.add(b);

System.out.println(result); // 정확하게 2.2
```

문자열로 생성하는 것이 핵심이다. `new BigDecimal(1.2)`처럼 하면 \
이미 부정확한 double이 들어가므로 주의해야 한다.

***

## Conclusion

<table><thead><tr><th width="117.9140625">항목</th><th>설명</th></tr></thead><tbody><tr><td>원인</td><td>IEEE 754 부동소수점 근사값</td></tr><tr><td>현상</td><td><code>1.0 + 1.2 ≠ 2.2</code></td></tr><tr><td>해결책</td><td>오차 허용 비교, BigDecimal 사용</td></tr></tbody></table>

개발자라면 부동소수점 연산의 불안정성에 대해 항상 인지하고 있어야 하며,\
정밀한 수치 계산이 요구되는 도메인(예: 금융/핀테크 등등..)에서는 반드시 적절한 대책을 세워야 한다..!
