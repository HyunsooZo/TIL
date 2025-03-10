---
description: 파이썬 프로그래밍 3강 내용 정리
icon: '3'
---

# Chapter 3 : Features

## Human-friendly & Intuitive

* 실행할 수 있는 의사 코드(Executable pseudocode) 수준의 문법을 가지고 있다.

```python
if 3 in [1,3,5,7]: print("3이 들어있습니다")
```

## Productivity & Efficiency

* C / Java 와 비교했을 때 간결한 문법으로 생산성이 높다.
  *   C Language:

      ```c
      int i, n;
      int sum = 0;

      printf("입력:");
      scanf("%d", &n);

      for(i = 0; i < n; i++) {
          sum += i;
      }

      printf("합은 %d", sum);
      ```
  *   Python:

      ```python
      n = int(input("입력: "))
      sum = 0
      for i in range(1, n+1):
          sum += i
      print("합은" + sum)
      ```

## Large Developer Community

* Python은 세계적으로 인기 있는 프로그래밍 언어 중 하나이다.
* JavaScript, Java, C/C++과 함께 많은 개발자들이 사용하고 있다.
* 다양한 분야에서 활용되며, 특히 **데이터 과학(DS), 머신러닝(ML), 웹 개발**에서 강세를 보인다.

## Disadvantages of Python

* C나 Java로 작성된 프로그램보다 **실행 속도가 느리다.**
* 단독 애플리케이션 개발이 어렵다.
  * 주로 **스크립트 언어** 용도로 활용됨.
  * 모바일 앱 등 응용 애플리케이션 개발에는 적합하지 않음.
  * 대안으로 **Rust 또는 Go**를 고려할 수 있다.

## Python Execution Environment

* 플랫폼 독립적인 **인터프리터** 기반 언어이다.
* **동적 타이핑(dynamically typed)**&#xC744; 지원한다.
* 다양한 운영체제에서 실행 가능하다.&#x20;
  * Windows, Linux, macOS, Unix 등
* 다양한 인터프리터 환경이 존재한다.
  * CPython, PyPy, Cython, Jython 등
* **객체 지향적** 모델링을 지원한다.

### CPython

* **C 언어로 개발된 Python 인터프리터**
* C 라이브러리와의 연동을 통해 확장이 용이하다.
* **Types of Compilers**
  1. Self-hosting Compiler: 자신의 언어로 작성된 컴파일러
  2. Source-to-source Compiler: 타 언어로 작성된 컴파일러\


## Python Program Execution Process

* Python 소스 코드는 바이트 코드(.pyc 파일)로 변환된다.
* Python 가상 머신(PVM)이 바이트 코드를 실행한다.

<figure><img src="../../.gitbook/assets/스크린샷 2025-03-10 오후 9.29.45.png" alt=""><figcaption></figcaption></figure>

## IDE For Python

### IDLE

* Python 기본 포함 통합 개발 환경(IDE)
* Tkinter GUI 기반 개발 가능
* 기본적인 코드 자동 완성, 디버깅 기능 제공

### Jupyter Notebook

* **Open-source Web-based Platform**
* Python을 포함한 40여 개의 언어를 지원한다.
* 웹 기반 대화형 개발 및 실행

### PyCharm

* **JetBrains에서 개발한 Python 전용 IDE** (IntelliJ에 익숙한 나에게는 가장 친숙한 IDE일 듯 싶다.)
* 코드 자동 완성, 강력한 디버깅 기능 제공
* 프로젝트 관리 및 가상 환경 지원
* 대규모 Python 애플리케이션 개발에 최적화됨

### VS Code

* **Microsoft에서 개발한 경량 IDE**
* Python 확장을 설치하여 강력한 기능 활용 가능
* 다양한 프로그래밍 언어 지원
* 풍부한 플러그인과 확장 기능 제공
