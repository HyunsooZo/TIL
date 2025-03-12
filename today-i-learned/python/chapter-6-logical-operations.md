---
icon: '6'
---

# Chapter 6 : Logical Operations

## Selection Structure and Syntax Format

파이썬에서는 특정 조건에 따라 코드 실행 흐름을 제어할 수 있다. \
선택 구조를 사용하면 불리언 값에 따라 코드 블록이 실행될지 결정된다.

### Key Concepts

* `if` 문을 사용해 조건을 평가하고 실행할 코드 블록을 들여쓰기로 구분한다.
* 코드 블록을 만들 때는 **스페이스 4칸** 들여쓰기를 권장 (PEP-8 스타일 가이드)
* 조건이 `True`이면 코드 블록이 실행되고, `False`이면 실행되지 않는다.

```python
x = 10
if x > 5:
    print("x는 5보다 크다")
```

<figure><img src="../../.gitbook/assets/스크린샷 2025-03-12 오후 8.36.17 (1).png" alt=""><figcaption></figcaption></figure>

***

## Boolean Expressions and Comparison Operators

### What is a Boolean Expression?

불리언식은 **비교 연산자**를 사용하여 `True` 또는 `False` 값을 반환하는 식이다.

### Main Comparison Operators

| Operator | Meaning |
| -------- | ------- |
| `<`      | 작다      |
| `<=`     | 작거나 같다  |
| `>`      | 크다      |
| `>=`     | 크거나 같다  |
| `==`     | 같다      |
| `!=`     | 같지 않다   |

```python
print(3 < 6)   # True
print(10 >= 20) # False
print(5 == 5)  # True
print(4 != 2)  # True
```

<figure><img src="../../.gitbook/assets/스크린샷 2025-03-12 오후 8.37.18.png" alt=""><figcaption></figcaption></figure>

***

## Boolean Type

#### What is Boolean Type?

파이썬에서는 `True`와 `False` 두 가지 값만을 가지는 **불리언 타입**이 존재한다.

* 비교 연산자의 결과로 불리언 값이 생성된다.
* `True`와 `False`는 대소문자를 구분하며 반드시 첫 글자가 대문자여야 한다.

```python
is_active = True
print(type(is_active))  # <class 'bool'>
print(3 > 6)  # False
```

***

## Logical Operators (and, or, not)

불리언 값들을 조합할 때 사용하는 연산자가 있다. \
논리 연산자를 사용하면 조건을 더 세밀하게 설정할 수 있다.

#### `and` (Logical AND)

* 두 조건이 **모두 `True`일 때**만 `True` 반환한다.
* 하나라도 `False`이면 `False` 반환한다.

```python
print(True and True)   # True
print(True and False)  # False
```



#### `or` (Logical OR)

* **하나라도 `True`면 `True` 반환**한다.
* 둘 다 `False`일 때만 `False` 반환한다.

```python
print(True or False)  # True
print(False or False) # False
```



#### `not` (Logical NOT)

* `True` → `False`, `False` → `True` 변환한다.

```python
print(not True)   # False
print(not False)  # True
```

<figure><img src="../../.gitbook/assets/스크린샷 2025-03-12 오후 8.40.10.png" alt=""><figcaption></figcaption></figure>

***

## Short-Circuit Evaluation

#### What is Short-Circuit Evaluation?

파이썬의 논리 연산자는 **단락 평가**(short-circuit evaluation) 기법을 사용한다.

* 논리 연산의 첫 번째 값만으로 결과가 판별될 수 있으면, **두 번째 값은 평가하지 않는다.**
* `and` 연산에서 첫 번째 값이 `False`이면 **두 번째 조건을 평가하지 않는다.**
* `or` 연산에서 첫 번째 값이 `True`이면 **두 번째 조건을 평가하지 않는다.**

```python
x = 10
y = 5
print(x > 5 and y < 10)  # True (두 번째 조건도 평가됨)
print(x < 5 and y > 0)   # False (첫 번째 조건이 False이므로 두 번째 조건 평가 안 함)
print(x > 5 or y > 10)   # True (첫 번째 조건이 True이므로 두 번째 조건 평가 안 함)
```



***

## Summary

이번 포스팅에서는 **불리언 값과 논리 연산자, 제어 구조**에 대해 정리했다.

* `if` 문과 들여쓰기를 사용해 코드 블록을 제어한다.
* 불리언 값은 `True` 또는 `False`이며, 비교 연산자로 생성된다.
* `and`, `or`, `not` 연산자를 사용해 논리적으로 조건을 결합한다.
* **단락 평가**를 이용하면 불필요한 연산을 줄일 수 있다.
