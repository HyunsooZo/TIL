---
description: >-
  파이썬 모듈은 코드의 구조화를 돕고 재사용성을 높이는 중요한 개념이다. 모듈은 함수, 변수, 클래스 등을 포함하는 하나의 파일이며, 이를
  통해 다양한 프로그램에서 같은 기능을 재사용할 수 있다. 10강 에서는 파이썬 모듈, 패키지, 라이브러리의 개념과 사용법을 살펴봤다.
icon: clock-eleven
---

# Chapter 11 : Module

## Library vs Framework vs Module

**라이브러리**는 특정 기능을 제공하는 모듈의 모음이다. \
**프레임워크**는 프로그램의 실행 흐름을 규정하는 구조이다.\
**모듈**은 함수, 변수, 클래스 등을 모아 놓은 집합체이다.

### 대표적인 파이썬 라이브러리 및 프레임워크

* **과학 계산:** NumPy, SciPy, pandas
* **데이터 시각화:** Matplotlib, Bokeh
* **머신러닝:** TensorFlow, PyTorch, Scikit-learn
* **웹 개발:** Flask, Django
* **인터랙티브 컴퓨팅:** Jupyter

### 모듈의 구성 요소

* **함수:** 특정 작업을 수행하는 코드 블록
* **변수(상수):** 변경되지 않는 값 저장
* **클래스:** 객체 지향 프로그래밍을 위한 설계도

### Modules, Packages, and Libraries

| 용어        | 정의                 |
| --------- | ------------------ |
| **모듈**    | 파이썬 코드가 포함된 단일 파일. |
| **패키지**   | 모듈을 포함하는 디렉터리.     |
| **라이브러리** | 여러 패키지와 모듈의 집합.    |

```
라이브러리
├── 패키지 (폴더)
│   ├── 모듈 1 (파이썬 파일)
│   ├── 모듈 2 (파이썬 파일)
│   └── 서브 패키지
```

## Importing Modules

파이썬에서 모듈을 불러오는 방법은 여러 가지가 있다.

```python
import 모듈이름 # 기본 임포트
```

```python
import 모듈이름 as 별칭 # 별칭 사용
```

```python
from 모듈이름 import 함수이름, 클래스이름 # 특정 요소만 불러오기
```

```python
from 모듈이름 import * #모든 요소 불러오기
```

### Checking Module Registration

파이썬은 모듈을 확인하는 내장 함수를 제공한다.

* `dir()`: 객체의 속성과 메서드를 리스트로 반환
* `help()`: 모듈, 함수, 클래스의 설명 출력

```python
dir(math)
help(math.sqrt)
```

### Understanding Namespaces

네임스페이스는 변수나 함수의 범위를 정의하는 개념이다.

1. **지역 네임스페이스:** 함수 또는 메서드 내부에서만 유효한 이름 공간
2. **전역 네임스페이스:** 모듈 전체에서 사용되는 이름 공간
3. **빌트인 네임스페이스:** 파이썬이 기본 제공하는 함수(`print`, `len` 등)

### Using the `math` Module

파이썬 `math` 모듈은 다양한 수학적 기능을 제공한다.

```python
import math
print(math.pi)  # 3.141592653589793
print(math.sqrt(16))  # 4.0
```

#### 예제: 원뿔 부피 및 표면적 계산

```python
class Cone:
    def __init__(self, radius=20, height=30):
        self.r = radius
        self.h = height

    def get_volume(self):
        return (1/3) * math.pi * self.r ** 2 * self.h

    def get_surface_area(self):
        return math.pi * self.r ** 2 + math.pi * self.r * self.h
```

### Example: Triangle Area Calculation

삼각형의 넓이를 삼각법을 이용해 구하는 프로그램:

```python
import math

def triangle_area(a, b, alpha):
    return 0.5 * a * b * math.sin(math.radians(alpha))
```

### Using the `random` Module

`random` 모듈은 난수를 생성하는 기능을 제공한다.

#### 주요 함수

| 함수                  | 설명                 |
| ------------------- | ------------------ |
| `random()`          | 0\~1 사이의 난수 발생     |
| `randint(a, b)`     | a부터 b 사이의 정수 난수 발생 |
| `choice(sequence)`  | 시퀀스에서 랜덤 항목 선택     |
| `shuffle(sequence)` | 리스트 항목을 무작위로 섞음    |

#### Example: Rock-Paper-Scissors Game

```python
import random

options = ["가위", "바위", "보"]
user_choice = input("가위, 바위, 보 중 하나를 입력하세요: ")
computer_choice = random.choice(options)

if user_choice == computer_choice:
    print("비겼습니다!")
elif (user_choice == "가위" and computer_choice == "보") or \
     (user_choice == "바위" and computer_choice == "가위") or \
     (user_choice == "보" and computer_choice == "바위"):
    print("이겼습니다!")
else:
    print("졌습니다!")
```

### Summary

* **모듈**: 파이썬 코드 파일 (함수, 클래스 포함)
* **패키지**: 여러 모듈을 포함하는 디렉터리
* **라이브러리**: 패키지와 모듈의 집합
* **모듈 불러오기**: `import` 문 사용
* **유용한 모듈**: `math`, `random`, `time`

모듈을 효과적으로 활용하면 코드의 재사용성과 유지보수성이 향상된다.
