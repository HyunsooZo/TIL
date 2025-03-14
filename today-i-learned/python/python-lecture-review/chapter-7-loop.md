---
description: 이번 포스팅에서는 반복문, 리스트, 문자열 서식화 등을 다뤄보았다.
icon: clock-seven
---

# Chapter 7 : Loop

## Loop Structures

### Conditional Control Loop (while loop)

파이썬에서는 `while` 문을 사용하여 **반복 여부를 조건에 따라 결정**할 수 있다.

```python
count = 0
while count < 5:
    print(count)
    count += 1
```

<figure><img src="../../../.gitbook/assets/스크린샷 2025-03-12 오후 9.06.30.png" alt=""><figcaption></figcaption></figure>

### Counter Control Loop (for loop)

미리 정해진 횟수만큼 반복할 때 `for` 문을 사용한다.

```python
for i in range(1, 6):
    print(i)
```

<figure><img src="../../../.gitbook/assets/스크린샷 2025-03-12 오후 9.03.05.png" alt=""><figcaption></figcaption></figure>

***

### Nested Loop

중첩 반복문을 사용하면 반복문 내부에서 또 다른 반복문을 실행할 수 있다.

```python
for i in range(1, 4):
    for j in range(1, 4):
        print(f"i: {i}, j: {j}")
```

<figure><img src="../../../.gitbook/assets/스크린샷 2025-03-12 오후 9.03.47.png" alt=""><figcaption></figcaption></figure>

***

## List and Indexing

### List Basics

리스트는 여러 개의 값을 저장할 수 있는 **순서가 있는 데이터 타입**이다.

```python
numbers = [1, 2, 3, 4, 5]
print(numbers[0])  # 1 출력
```

### Iterating Over Lists

리스트의 요소를 순차적으로 탐색할 때 `for` 문을 사용한다.

```python
for num in numbers:
    print(num)
```

<figure><img src="../../../.gitbook/assets/스크린샷 2025-03-12 오후 9.04.25.png" alt=""><figcaption></figcaption></figure>

***

## &#x20;`range()` Function

`range()` 함수를 사용하면 일정한 간격의 숫자 리스트를 만들 수 있다.

```python
for i in range(1, 10, 2):
    print(i)
```

***

## String Formatting with `format()`

문자열을 특정 형식으로 정렬하거나 숫자를 조정할 때 사용한다.

```python
print(format("Hello", "^10"))  # 중앙 정렬
```

<figure><img src="../../../.gitbook/assets/스크린샷 2025-03-12 오후 9.04.54.png" alt=""><figcaption></figcaption></figure>

***

