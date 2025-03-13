---
description: 9강에서는 파이썬의 함수에 대해 알아보았다. 자바와는 다소 다른 (그치만 편한) 특징이 인상깊었다.
icon: '9'
---

# Chapter 9 : Function

## Understanding Functions

함수는 특정 작업을 수행하는 명령문의 집합이다. \
함수의 유무에 따라 반환값이 없는 함수와 반환값이 있는 함수로 구분된다.

> **Java vs Python**
>
> * Java에서는 모든 함수(메서드)가 클래스 내부에 존재해야 하지만, Python에서는 독립적으로 선언할 수 있다.
> * Java의 메서드는 반드시 반환 타입을 지정해야 하지만, Python의 함수는 `return` 없이도 정의할 수 있다.

## Functions with Return Values

반환값이 있는 함수는 실행 후 결과값을 남긴다. \
`return` 명령어를 사용하여 값을 반환하며, 함수 내부에 **여러 개의** `return`을 포함할 수 있다.

> **Java vs Python**
>
> * Java에서는 `return`을 사용할 때, 반드시 선언된 반환 타입과 일치하는 값을 반환해야 한다.
> * Python에서는 동적 타입을 지원하므로, 같은 함수에서 다른 타입의 값을 반환할 수도 있다.

## Simultaneous Assignment

동시에 여러 개의 변수에 값을 할당하는 연산이다. 이를 통해 단일 명령문으로 변수의 값을 맞바꿀 수 있다.

```python
# 동시 할당 예제
x, y = 10, 20
x, y = y, x
print(x, y)  # 20 10
```

> **Java vs Python**
>
> * Java에서는 한 번에 여러 개의 변수를 선언할 수 있지만, 동시에 값을 바꾸려면 별도의 임시 변수가 필요하다.
> * Python에서는 튜플 언패킹을 이용해 간단하게 값을 교환할 수 있다.

## Function Calls with Return Values

반환값이 있는 함수는 호출 후 결과를 변수에 저장하거나 직접 사용할 수 있다.

```python
# 원뿔 부피 계산 함수
def cone_volume(r, h):
    if r > 0 and h > 0:
        return (1/3) * 3.14 * r**2 * h
    else:
        return "양수 값을 입력하세요."

print(cone_volume(10, 50))
```

## Value Passing

함수가 호출될 때, 값이 매개변수로 전달된다. \
이는 함수 내부에서 사용되지만, 원본 변수에는 영향을 주지 않는다.

```python
def inc(x):
    x = x + 1
    print("x의 값은", x)

x = 1
inc(x)
print("x의 값은", x)  # 원본 x 값 유지
```

> **Java vs Python**
>
> * Java는 기본형(primitive type)은 값으로 전달되며, 참조형(reference type)은 주소값이 전달된다.
> * Python에서는 모든 것이 객체이므로, 변경 가능한 객체(mutable)는 참조가 전달되지만, \
>   변경 불가능한 객체(immutable)는 값이 전달된다.

## Variable Scope

변수의 스코프(범위)는 프로그램에서 변수가 접근할 수 있는 영역을 의미한다. \
전역 변수는 프로그램 전체에서 접근 가능하며, 지역 변수는 함수 내부에서만 유효하다.

```python
x = 1  # 전역 변수
def func():
    y = x + 1  # 지역 변수
    print("y의 값은", y)

func()
print("x의 값은", x)
```

> **Java vs Python**
>
> * Java에서는 `static` 키워드를 사용하여 전역 변수처럼 동작하는 변수를 만들 수 있다.
> * Python에서는 `global` 키워드를 사용해야 함수 내부에서 전역 변수를 변경할 수 있다.

## Default Parameters

함수 호출 시 특정 매개변수가 전달되지 않으면 기본값이 사용된다.

```python
# 기본 매개변수 사용 예제
def greet(name="Python"):
    print("Hello,", name)

greet()  # Hello, Python
greet("User")  # Hello, User
```

> **Java vs Python**
>
> * Java에서는 기본 매개변수를 직접 지원하지 않으며, 오버로딩(overloading)으로 같은 기능을 구현해야 한다.
> * Python에서는 기본값을 지정하여 더 간결한 코드를 작성할 수 있다.

## Arbitrary Arguments

가변 매개변수를 사용하면 함수가 전달받을 매개변수 개수를 동적으로 설정할 수 있다.

```python
def sum_all(*args):
    return sum(args)

print(sum_all(1, 2, 3, 4, 5))  # 15
```

> **Java vs Python**
>
> * Java에서는 `varargs`(가변 인자, `...`)를 사용하여 가변 매개변수를 받을 수 있다.
> * Python에서는 `*args`를 사용하여 쉽게 구현할 수 있으며, 리스트나 튜플로 전달 가능하다.

## Summary

* 함수는 특정 작업을 수행하는 코드 블록이다.
* 반환값의 유무에 따라 반환값이 없는 함수와 반환값이 있는 함수로 나뉜다.
* 동시 할당을 통해 변수를 한 줄로 선언하고 값을 바꿀 수 있다.
* 함수 호출 시 매개변수에 값을 전달하면 해당 값이 함수 내부에서 사용된다.
* 변수의 스코프에 따라 전역 변수와 지역 변수로 나뉜다.
* 기본 매개변수를 활용하여 함수 호출 시 기본값을 설정할 수 있다.
* 가변 매개변수를 사용하면 매개변수의 개수를 동적으로 조정할 수 있다.
* Java와 비교하면 Python은 보다 유연하고 간결한 문법을 제공하는 특징이 있다.
