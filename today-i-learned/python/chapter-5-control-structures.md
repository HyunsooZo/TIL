---
icon: '5'
---

# Chapter 5 : Control Structures

## Control Structures

* 프로그램의 흐름을 제어하는 구조를 의미함.
* Python에서는 **순차(Sequence), 선택(Selection), 반복(Iteration)** 구조로 구성됨.

## Structured Programming Paradigm

* 절차적 프로그래밍 패러다임의 하위 개념이다.
* `goto` 문을 사용하지 않고 프로그램을 3가지 제어 구조만으로 구성한다.
* 프로그램의 실행 흐름을 간결하게 유지하고 작은 규모로 조직화하기 쉬움.

### 주요 제어 구조

* **Sequence (순차 구조)**: 명령이 위에서 아래로 순서대로 실행됨.
* **Selection (선택 구조)**: 특정 조건에 따라 실행 여부가 결정됨.
* **Iteration (반복 구조)**: 특정 조건이 충족될 때까지 명령을 반복 실행함.

### Sequence Structure&#x20;

* 가장 기본적인 구조로, **코드가 작성된 순서대로 실행**되는 방식이다.
* 한 단계가 끝나면 다음 단계가 자동으로 실행됨.

```python
print("설계 단계")
print("생산 단계")
print("조립 단계")
```

**Java vs Python**

* Java에서는 `main` 메서드 내에서 명령어가 순서대로 실행됨.
* Python에서는 코드가 순서대로 실행되며, 블록 지정 없이 들여쓰기로 흐름을 구분함.

### Selection Structure&#x20;

* 특정 조건에 따라 실행 여부가 결정되는 구조이다.
* `if`, `if-else`, `if-elif-else` 문을 사용함.

```python
status = "정상"
if status == "정상":
    print("배송 진행")
else:
    print("검사 필요")
```

**Java vs Python**

* Java: `{}` 중괄호를 사용하여 블록 지정.
* Python: 들여쓰기를 사용하여 코드 블록을 구분.

### Iteration Structure

* 특정 영역의 명령문을 여러 번 반복 실행하는 구조이다.
* `for` 문과 `while` 문을 사용한다.

```python
for i in range(3):
    print("반복 실행")

i = 0
while i < 3:
    print("반복 실행")
    i += 1  # i 값을 증가시켜 종료 조건을 만족하도록 함
```

**Java vs Python**

* Java: `for` 루프에서 초기화, 조건, 증감식을 한 줄에 작성.
* Python: `range()` 함수를 사용하여 반복 횟수를 지정.

## User Input&#x20;

* 사용자의 입력을 받을 때 `input()` 함수를 사용한다.
* 입력된 데이터는 기본적으로 **문자열(str) 타입**이다.

```python
rad = input("반지름을 입력하세요: ")
```

**Java vs Python**

* Java: `Scanner` 클래스를 사용하여 입력을 받음.
* Python: `input()` 함수 하나로 간단하게 입력 가능.

## Programming Errors&#x20;

* 프로그램에서 발생할 수 있는 대표적인 오류 유형.

#### 주요 오류 유형

* **Syntax Error (구문 오류)**: 문법 체계에 맞지 않는 명령문 입력 시 발생.
* **Runtime Error (실행 오류)**: 논리적으로 실행 불가능한 명령문 작성 시 발생.
* **Semantic Error (의미 오류)**: 의미적으로 잘못 해석되는 명령문 작성 시 발생.

```python
# Syntax Error 예제
print("Hello World!"  # 닫는 괄호가 없음

# Runtime Error 예제
print(10 / 0)  # 0으로 나누는 오류 발생

# Semantic Error 예제
x = 10
y = 20
if x > y:
    print("x가 y보다 크다")  # 실제로는 y가 크지만 코드가 잘못 작성됨
```

### Summary

* Python에서 제어 구조는 **순차, 선택, 반복** 구조로 이루어짐.
* 사용자 입력은 `input()`을 사용하며, Java보다 간결한 문법을 제공함.
* 프로그래밍 오류는 **구문 오류, 실행 오류, 의미 오류**로 구분됨.
