---
description: 파이썬 프로그래밍 4강 내용 정리
icon: '4'
---

# Chapter 4 : Basics

## Numbers & Strings

### Number

* 정수(Integer): 소수점이 없는 숫자
* 실수(Floating Point): 소수점이 포함되는 숫자

**Java vs Python**

* Java: `int`, `double` 등 명시적 타입 선언 필요
* Python: `int`, `float` 자동 타입 지정 (동적 타이핑)

### String

* 유니코드(Unicode) 기반 문자 또는 문자열
* 인용 부호 `"` 또는 `'` 사용 가능

**Java vs Python**

* Java: `String`은 불변(immutable), `char` 타입 존재
* Python: 문자열은 불변(immutable), `char` 타입 없음 (모든 문자는 문자열로 취급)

## Basic Operators & Expressions

* 피연산자와 연산자를 이용한 표현식은 자동 계산됨

| 연산자  | 기능       |
| ---- | -------- |
| `+`  | 더하기      |
| `-`  | 빼기       |
| `*`  | 곱하기      |
| `/`  | 나누기      |
| `**` | 지수(거듭제곱) |

**Java vs Python**

* Java: `/` 연산자는 정수 나눗셈 시 정수 반환, `Math.pow()` 사용해 지수 연산
* Python: `/`는 항상 실수 반환, `**` 연산자로 지수 연산 가능

## Functions

* 특정 작업을 수행하는 코드의 집합
* 함수 이름만으로 실행할 수 있는 단위
* `print()` 함수는 화면에 데이터를 출력하는 역할을 함

```python
print("Hello World!")
```

**Java vs Python**

* Java: 모든 함수는 클래스 내에서 선언해야 함 (`public static void main` 등)
* Python: 독립적인 함수 선언 가능 (`def` 사용)

## Indentation

* 파이썬은 들여쓰기에 의존적인 언어
* 코드의 논리적 집합을 블록으로 표현
* PEP 8에서는 **스페이스 4칸** 들여쓰기를 권장

```python
print("Hello World!")
  print("Python is fun")  # 잘못된 들여쓰기
```

**Java vs Python**

* Java: `{}` 중괄호로 블록 구분
* Python: 들여쓰기로 코드 블록 구분

## Documentation

* 주석(Comment) 사용
* 코드 가독성을 높이고 유지보수에 도움을 줌
* 한 줄 주석: `#` 사용
* 여러 줄 주석: `"""` 또는 `'''` 사용

```python
# 이 부분은 주석
print("Hello Python")

"""
이것도 주석
여러 줄에 걸쳐 사용 가능
"""
```

**Java vs Python**

* Java: 한 줄 주석 `//`, 여러 줄 주석 `/* ... */`
* Python: `#` 및 `"""` 사용

## Variables

* 데이터를 저장하는 공간
* 변수는 할당 연산자 `=` 를 이용해 값을 저장

```python
rad = 20  # 반지름
hei = 30  # 높이
```

**Java vs Python**

* Java: 변수 선언 시 타입 명시 필수 (`int rad = 20;`)
* Python: 변수 타입 자동 지정 (동적 타이핑)

## Reserved Words

* 파이썬에서 특정한 문법적인 용도로 이미 사용되고 있어 식별자로 사용할 수 없는 단어

| 예약어                                     |
| --------------------------------------- |
| False, await, else, import, pass, None  |
| break, except, in, True, class, finally |
| is, return, and, continue, for, lambda  |
| try, as, def, global, not, with         |
| async, elif, if, yield, raise, or       |

## Operator Precedence

* 연산자의 우선순위는 다음과 같음

1. 괄호 내부 수식
2. 지수(`**`)
3. 곱셈, 실수 나눗셈, 정수 나눗셈, 나머지 (`*, /, //, %`)
4. 덧셈, 뺄셈 (`+, -`)
5. 할당 연산자 (`=`)

```python
result = 3 + 2 * 5  # 3 + (2 * 5) → 13
```

**Java vs Python**

* 연산자 우선순위는 유사하나, Java에서는 `Math.pow()` 사용해 지수 연산 수행

## Built-in Functions

* 파이썬이 기본적으로 제공하는 내장 함수
* 별도의 모듈이나 패키지 없이 사용 가능

```python
max(2, 3, 4)   # 4
min(2, 3, 4)   # 2
round(3.141592)  # 3
abs(-3)  # 3
pow(2, 3)  # 8
```

**Java vs Python**

* Java: `Math.max()`, `Math.min()`, `Math.abs()` 등 `Math` 클래스를 사용해야 함
* Python: 내장 함수로 제공되므로 바로 사용 가능

## Summary

* 프로그래밍의 기초 개념을 익히고, 변수, 연산자, 함수, 주석 등을 활용하는 방법을 학습했다.
* 파이썬과 자바의 문법적 차이점도 비교하여 이해해보려했다.
* 아직 기초단계이다보니 조금 지루한 감이 있다..
