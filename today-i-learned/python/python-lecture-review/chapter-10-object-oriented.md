---
description: >-
  객체지향(aka OOP)은 객체와 객체 사이의 상호작용으로 프로그램을 구성하는 프로그래밍 패러다임이다. 프로그램을 유연하고 변경을 쉽게
  만들어 대규모 소프트웨어 개발에 사용된다.
icon: clock-ten
---

# Chapter 10 : Object Oriented

## Features&#x20;

* **추상화**: 공통의 속성이나 기능을 도출
* **캡슐화**: 데이터 구조와 데이터의 연산을 결합
* **상속**: 상위 개념의 특징이 하위 개념에 전달
* **다형성**: 유사 객체의 사용성을 그대로 유지

## Object and Class

객체는 **추상화와 캡슐화의 결과**이며, 실세계의 사물에 대한 상태(데이터)와 연산(메서드)을 표현한 단위이다.

* **객체의 멤버**: 데이터 필드, 메서드로 구성
* **객체의 데이터와 연산은 클래스에 의해 결정됨**

### Class Definition

클래스는 객체를 정의하는 템플릿이다.

```python
class 클래스이름:
    def __init__(self, 매개변수):
        # 초기자 정의
    def 메서드이름(self, 매개변수):
        # 메서드 정의
```

* **메서드(Method)**: 객체에 대한 행동(연산)을 정의하며, 함수 정의와 사용 방법이 동일함
* **초기자(Initializer)**: 객체의 상태를 초기화하는 특수 메서드 (`__init__` 사용)
* **self 매개변수**: 모든 메서드의 첫 번째 매개변수이며, 객체 자신을 참조

### Class and Instance

#### Object Creation Process

1. 클래스를 사용하여 **객체 생성**
2. `__init__()` **호출**

```python
cone = Cone(20, 30)  # 반지름 20, 높이 30인 원뿔 생성
```

#### Using Objects

객체의 데이터 필드 접근 및 메서드 호출은 \*\*객체 멤버 접근 연산자(`.`)\*\*를 사용한다.

```python
print(cone.r)   # 객체의 반지름 출력
print(cone.get_vol())  # 원뿔의 부피 계산
```

### Applying Object-Oriented Programming

#### Data Type and Object

Python에서 모든 데이터는 객체로 취급된다.

```python
number = 20
print(type(number))  # <class 'int'>
print(id(number))    # 메모리 주소 출력
```

#### String (str) Methods

* `upper(), lower()`: 대/소문자로 변경
* `title()`: 각 단어의 첫 글자를 대문자로 변경
* `replace()`: 특정 부분을 다른 문자열로 대체
* `split()`: 구분자로 분할하여 리스트 반환

```python
text = "I love python"
print(text.upper())       # "I LOVE PYTHON"
print(text.replace("o", "i"))  # "I live pythin"
```

#### Data Field Hiding

데이터 필드의 직접 변경을 방지하기 위해 **데이터 은닉**을 사용한다.

* **private 데이터 필드**는 클래스 내부에서만 접근 가능하며 앞 두 밑줄(`__`)로 정의된다.

```python
class Cone:
    def __init__(self, radius, height):
        self.__r = radius  # private 변수
        self.__h = height
```

#### Accessor and Mutator

* **접근자(Accessor)**: 데이터 필드 반환
* **변경자(Mutator)**: 데이터 필드 설정

```python
class Cone:
    def get_r(self):
        return self.__r
    def set_r(self, radius):
        self.__r = radius
```

#### Utilizing Cone Class

```python
class Cone:
    def __init__(self, radius=20, height=30):
        self.__r = radius
        self.__h = height
    def get_vol(self):
        return 1/3 * 3.14 * self.__r ** 2 * self.__h
    def get_surf(self):
        return 3.14 * self.__r ** 2 + 3.14 * self.__r * self.__h
```

#### BMI Calculation Program

```python
class BMI:
    def __init__(self, name, age, weight, height):
        self.name = name
        self.age = age
        self.weight = weight
        self.height = height
    def get_BMI(self):
        return self.weight / (self.height ** 2)
    def get_status(self):
        bmi = self.get_BMI()
        if bmi < 18.5:
            return "Underweight"
        elif bmi < 25:
            return "Normal weight"
        elif bmi < 30:
            return "Overweight"
        else:
            return "Obese"
```

## Summary

객체지향 프로그래밍(OOP)은 실세계의 개념을 코드로 표현하여 유지보수가 쉬운 프로그램을 만들 수 있도록 한다. \
객체와 클래스의 개념을 이해하고, 캡슐화, 상속, 다형성과 같은 특징을 활용하면 더욱 효율적인 프로그램 개발이 가능하다. \
Python에서는 모든 데이터가 객체로 다뤄지며,\
&#x20;private 변수를 활용한 데이터 은닉과 접근자/변경자 메서드로 데이터 필드를 보호할 수 있다. \
이를 통해 보다 안전하고 재사용성이 높은 소프트웨어를 개발할 수 있다.
