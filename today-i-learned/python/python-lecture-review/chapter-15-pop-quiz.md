---
description: 마지막 강의였다. 문제는 쉬웠으나 자바와는 다른 점이 조금 있어 기말고사 볼 때 유의할 필요는 있을 것 같다.
icon: clock-three
---

# Chapter 15 : Pop Quiz

## Problem 1

문제: 유사한 유형의 문제를 해결할 수 있도록 특정 작업을 수행하는 명령문을 모아놓은 집합체는?

1. 함수
2. 인터프리터
3. 파일
4. 명령 블럭

풀이:\
유사한 유형의 문제를 해결하기 위해 특정 작업을 수행하는 명령문을 모아놓은 집합체는 \
함수라고 한다.

답: `1`

***

## Problem 2

문제: `line` 클래스의 인스턴스 `m`의 멤버 `_len`을 `10`으로 수정하기 위한 올바른 표현은?

```python
class line:
    def __init__(self, length):
        self._len = length
    
    def get_length(self):
        return self._len
    
    def set_length(self, length):
        self._len = length
```

1. `m._len = 10`
2. `m.len = 10`
3. `m.set_length(10)`
4. `m.set_length( ) = 10`

풀이:\
클래스의 `private`멤버 `_len`을 직접 수정하려면 `m._len = 10`을 사용할 수 있지만, \
일반적으로 `setter` 메서드`set_length)`를 통해 값을 설정하는 것이 올바른 접근법이다.

답: `3`

***

## Problem 3

문제: `math` 모듈 사용을 위한 빈 칸의 올바른 표현은?

```python
a, b = 10, 20
area = 1 / 2 * a * b * sin(radians(60))
print(area)
```

1. `import math`
2. `import math.sin, math.radians`
3. `from sin, radians import math`
4. `from math import sin, radians`

풀이:\
`Python`에서 `sin()`과 `radians()` 함수를 사용하려면 `math` 모듈에서 해당 함수를 가져와야 한다.

* `import math`만으로는 `math.sin()`과 `math.radians()`를 호출해야 하므로 불편하다.
* `from math import sin, radians`를 사용하면 해당 함수만 직접 가져와서 사용할 수 있다.

답: `4`

***

## Problem 4

문제: 특정 모듈 또는 분야에서 사용되는 함수, 상수 및 클래스를 모아 놓은 집합체는?

1. 모듈
2. 패키지
3. 라이브러리
4. 프레임워크

답: 1

***

## Problem 5

문제: 매개변수로 전달된 숫자를 뒤집어서 출력하는 함수를 위한 올바른 표현은?

```python
def reverse_digit(number):
    while number != 0:
        digit = number % 10
        number = number // 10
        print(digit, end="")
```

1. `==`
2. `/`
3. `%`
4. `//`

풀이:

* `number % 10` → 숫자의 마지막 자리를 구함.
* `number // 10` → 정수 나눗셈으로 숫자의 나머지를 제거.

여기서 `//가` 올바른 연산자이다.

답: `4`

***

## Problem 6

문제: 특정 배열 또는 집합에서 사용되는 함수나 상수를 모아 놓은 집합체는?

1. 복소수
2. 시드
3. 난수
4. 정수

풀이:\
"배열 또는 집합에서 사용되는 함수나 상수"에 대한 설명이 모호하지만, 문맥상 "정수"가 가장 적절한 선택이다.

답: `4`

***

## Problem 7

문제: 딕셔너리의 구성요소와 용어가 올바르게 짝지어진 것은?

```python
"hamlet" 170
"the" 1086
"of" 678
"you" 549
```

1. 값(value)
2. 항목(item)
3. 딕셔너리(dictionary)
4. 키(key)

풀이:\
딕셔너리에서 "hamlet", "the", "of", "you"는 키(key)이고, 170, 1086, 678, 549는 값(value)이다.

답: `4`

***

## Problem 8

문제: 매개변수 numbers에 담긴 숫자를 모두 더하는 함수는?

```python
def summation(*numbers):
    sum = sum + i
    return sum
```

1. `i = numbers`
2. `for i in numbers`
3. `i = i + 1`
4. `if i in numbers`

풀이:

* `sum = sum + i`는 반복문을 통해 `numbers`의 각 요소를 더해야 한다.
* 따라서 `for i in numbers`를 사용해야 한다.

답: `2`

***

## Problem 9

문제: `line` 클래스의 인스턴스 `m`의 멤버 `__len`을 10으로 수정하기 위한 올바른 표현은?

```python
m.set_length(10)
```

답: `3`

***

## Problem 10

문제: 대규모 소프트웨어 개발에 사용한 결과를 하나의 프로젝트로 정의한 것은?

1. 프로그래밍 빌드 과정
2. 소프트웨어 모델링
3. 소프트웨어 개발 라이프사이클
4. DevOps

답: 3

***

## Problem 11-1

#### 파일 전체의 내용이 리스트로 저장되도록 (가)에 들어갈 알맞은 표현은?

```python
khan_fp.readlines()
```

**보기**

1. `readlines(khan_fp)`
2. `readlines()`
3. `readlines(khan_mottos)`
4. `khan_fp.readlines()`

**풀이**

파일 전체의 내용을 리스트로 저장하려면 `readlines()` 메서드를 사용해야 한다. 파일 객체 `khan_fp`를 통해 메서드를 호출해야 하므로 `khan_fp.readlines()`가 올바른 표현이다.

**답: 4**

***

## Problem 11-2

#### 파일 처리 후 프로그램이 종료되기 전 파일에 대한 마무리 작업으로 (나)에 들어갈 올바른 표현은?

```python
khan_fp.close()
```

**보기**

1. `khan_fp.close()`
2. `khan_fp.write('close')`
3. `close(khan_fp)`
4. 없음

**풀이**

파일 처리가 끝난 후에는 파일을 닫아야 한다. `close()` 메서드를 파일 객체를 통해 호출해야 하므로 `khan_fp.close()`가 올바른 표현이다.

**답: 1**

***

## Problem 12

#### 함수의 처리 결과 반환 시 사용하는 키워드는?

**보기**

1. `break`
2. `result`
3. `return`
4. `continue`

**풀이**

함수에서 값을 반환할 때는 `return` 키워드를 사용한다.

**답: 3**

***

## Problem 13

#### 이벤트 루프의 역할에 대한 올바른 설명은?

**보기**

1. 이벤트 생성 여부 확인 및 전달
2. 이벤트 유형에 따른 이벤트 생성
3. 이벤트 저장
4. 이벤트 발생에 따른 코드 실행

**풀이**

이벤트 루프는 발생한 이벤트를 감지하고, 해당 이벤트의 여부를 전달한다.

**답: 1**

***

## Problem 14

#### 소프트웨어 개발 라이프사이클에서 요구 반영, 예상 결과 및 오류 파악이 진행되는 단계는?

**보기**

1. 계획
2. 분석
3. 구현
4. 테스트

**풀이**

테스트 단계에서 소프트웨어의 예상 결과를 검증하고, 오류를 파악하여 수정한다.

**답: 4**

***

## Problem 15

#### 클래스의 인스턴스를 생성하는 표현은?

```python
r1 = robot('android1')
```

**보기**

1. `r1 = robot('android1')`
2. `r1 = __init__('android1', 'cook')`
3. `r1 = create_skill('cook')`
4. `r1 = info()`

**풀이**

클래스의 인스턴스를 생성하려면 클래스의 생성자를 호출해야 한다. `robot('android1')`를 사용하여 객체를 생성하는 것이 올바른 표현이다.

**답: 1**

***

## Problem 16

#### 객체지향 프로그래밍에서 객체의 구성 요소가 아닌 것은?

**보기**

1. 메소드
2. 초기자
3. 데이터 필드
4. 내장 함수

**풀이**

객체는 메서드, 생성자(초기자), 데이터 필드 등의 요소로 구성되지만, 내장 함수는 객체의 구성 요소가 아니다.

**답: 4**

***

## Problem 17

#### 1\~9 사이의 임의의 정수를 생성하는 표현은?

```python
random.randint(1, 9)
```

**보기**

1. `random.randrange(1, 9, 3)`
2. `random.randint(1, 9)`
3. `random.choice(2, 9)`
4. `random.random()`

**풀이**

`randint(a, b)`는 `a`부터 `b` 사이의 정수를 랜덤하게 반환하므로, 1\~9 사이의 정수를 생성할 때 사용할 수 있다.

**답: 2**

***

## Problem 18

#### 2차원 리스트 `board`의 모든 항목을 출력하는 코드의 (가)에 들어갈 표현은?

```python
print()
```

**보기**

1. `print()`
2. `continue`
3. `break`
4. `pass`

**풀이**

2차원 리스트의 각 요소를 출력하는 함수에서 줄바꿈을 위해 `print()`를 사용해야 한다.

**답: 1**
