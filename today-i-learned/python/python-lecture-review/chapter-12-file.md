---
description: 12강에서는 파이썬을 활용한 파일 입출력, 텍스트 분석, 데이터 구조(리스트, 딕셔너리) 처리 방법을 다뤘다.
icon: clock-twelve
---

# Chapter 12 : File

## Basics of File Input/Output

### **Opening a File**

파일을 열기 위해 `open()` 함수를 사용한다. \
파일 객체를 반환하며, 모드를 지정해 접근 방식을 결정한다.

* **모드**:
  * 'r': 읽기 (기본값).
  * 'w': 쓰기 (파일이 있으면 덮어쓰기).
  * 'a': 추가 (파일 끝에 내용 추가).

```python
with open("python.txt", "a") as a_fp: # 추가 모드로 파일 열기
```

### **Writing to a File**

`write()` 메서드로 데이터를 파일에 기록한다. 작업 후 `close()`로 파일을 닫아 자원을 해제한다.

```python
a_fp.write("nby CS\n") # "python.txt"에 "nby CS"와 개행 문자 기록 
a_fp.close() # 파일 닫기
```

* **컨텍스트 매니저 사용 권장**: with 문을 사용하면 자동으로 파일을 닫아 자원 누수를 방지한다.

```python
with open("python.txt", "a") as a_fp: 
    a_fp.write("nby CS\n") # 파일에 데이터 추가
```

### **Reading from a File**

파일에서 데이터를 읽기 위해 여러 메서드를 사용할 수 있다.

* **메서드**:
  * `read()`: 파일 전체 내용을 문자열로 읽음.
  * `readline()`: 한 줄씩 읽음.
  * `readlines()`: 모든 줄을 리스트로 읽음.
* **예제**: "Hamlet\_by\_Shakespeare.txt"를 읽어 단어 빈도를 분석.

<pre class="language-python"><code class="lang-python"><strong>with open("Hamlet_by_Shakespeare.txt", "r") as f: 
</strong>    text = f.read() # 전체 텍스트 읽기 
    words = text.split() # 단어로 분리 
    word_freq = {} for word in words: 
        word_freq[word] = word_freq.get(word, 0) + 1 # 단어 빈도 계산
</code></pre>

* **분석 결과**:
  * "hamlet": 170회
  * "the": 1086회
  * "of": 678회
  * "you": 549회
  * "that": 384회
  * "apparition": 2회
  * "may": 71회
  * "approve": 2회
  * "eyes": 23회

### Practical File Operations

* **텍스트 파일 분석**:
  * "Hamlet\_by\_Shakespeare.txt"와 "Khan.txt"를 읽어 단어 빈도를 계산.
  * 결과를 파일에 기록하거나 출력.
* **파일 생성 및 추가**:
  * 새로운 파일에 결과를 저장하거나 기존 파일에 추가.
  * **예제**: 단어 빈도 결과를 "result.txt"에 저장

```python
 with open("result.txt", "w") as f: 
      for word, freq in word_freq.items(): 
           f.write(f"{word}: {freq}\n") # 단어와 빈도를 파일에 기록
```

## Advanced File Handling

### **Binary Encoding**

텍스트를 이진 형식으로 변환해 데이터를 효율적으로 저장한다.

* **예제**: 숫자 199를 텍스트와 이진으로 변환.
* 이진 모드로 파일을 열 때는 'rb' (읽기) 또는 'wb' (쓰기) 사용

```python
with open("binary_file.bin", "wb") as f: 
    f.write(b"Binary data") # 이진 데이터 쓰기
```

### **Error Handling**

파일 입출력 시 발생할 수 있는 오류를 처리한다.

* **예제**: 파일이 없거나 권한 문제 발생 시 예외 처리.

```python
try:
    with open("non_existent_file.txt", "r") as f:
        content = f.read()
except FileNotFoundError:
    print("파일을 찾을 수 없습니다.")
except PermissionError:
    print("파일 접근 권한이 없습니다.")
```

### Text Processing with Files

* **단어 빈도 분석**:
  * 파일을 읽고 단어를 분리한 뒤 빈도를 계산.
  * 결과를 딕셔너리로 저장.

```python
word_freq = {} 
with open("Hamlet_by_Shakespeare.txt", "r") as f: 
    text = f.read()  # 전체 텍스트 읽기 
    words = text.split()  # 단어로 분리 

    for word in words: 
        word_freq[word] = word_freq.get(word, 0) + 1  # 단어 빈도 계산
```

* **데이터 정제**:
  * 불필요한 공백, 특수문자 제거.
  * 대소문자 통일 (예: "The"와 "the"를 동일하게 처리)

```python
word = word.lower().strip(".,!?") # 소문자 변환 및 특수문자 제거
```

## Data Structures for File Processing

### Dictionary Operations

* **생성 및 초기화**:

```python
word_freq = {} # 빈 딕셔너리 생성 # 또는 word_freq = dict() # dict()로 생성
```

* **조작**:
  * 키-값 쌍 추가: `word_freq["new_word"] = 1 # 새로운 단어 추가`
* 삭제:
  * `del word_freq["new_word"] # 단어 삭제`
* **순회**:
  * `for key in word_freq:`&#x20;
    * `print(key + ": " + str(word_freq[key])) # 키와 값 출력`
* **메서드**:
  * `keys()`: 키를 튜플로 반환.
  * `values()`: 값을 튜플로 반환.
  * `items()`: 키-값 쌍을 튜플로 반환.
  * `clear()`: 모든 항목 삭제.
  * `get(키)`: 지정된 키의 값 반환.
  * `pop(키)`: 지정된 키와 값 제거 후 값 반환.
  * `popitem()`: 마지막 키-값 쌍 제거 후 튜플로 반환.

```python
print(word_freq.keys()) # 키 출력 
print(word_freq.values()) # 값 출력 
print(word_freq.items()) # 키-값 쌍 출력
```

### List Operations

* **생성**:`hei_list = [1, 5, 14, 31] # 리스트 생성`
* **조작**:
  * 요소 추가:\
    `hei_list.append(100) # 리스트 끝에 추가` \
    `hei_list.insert(2, 50) # 인덱스 2에 50 삽입`
  *   요소 제거:

      `hei_list.remove(5) # 값 5 제거` \
      `hei_list.pop(0) # 인덱스 0의 요소 제거`
  * 전체 삭제:\
    `hei_list.clear() # 모든 요소 삭제`
* **순회**: `for element in hei_list: print(element) # 요소 출력`

### Practical Examples

* **텍스트 파일 처리**:

```python
word_freq = {} 
with open("Hamlet_by_Shakespeare.txt", "r") as f: 
    text = f.read() 
    words = text.split() 
    
    for word in words: 
        word = word.lower().strip(".,!?") 
        word_freq[word] = word_freq.get(word, 0) + 1 
        
with open("result.txt", "w") as f: 
    for word, freq in word_freq.items(): 
        f.write(f"{word}: {freq}\n")
```

* **데이터 구조 활용**:
  * 딕셔너리로 단어 빈도 저장 후 순회 출력.
  * 리스트로 단어 목록 관리 (예: 고유 단어 리스트 생성).

```python
unique_words = list(word_freq.keys()) # 고유 단어 리스트 생성 
print(unique_words)
```

### Summary

파일 입출력은 open(), read(), write()를 통해 수행되며, with 문을 사용해 자원 관리를 간편하게 한다. 텍스트 파일을 읽고 단어 빈도를 분석한 뒤 결과를 파일에 기록하는 과정은 데이터 처리의 핵심이다. 딕셔너리와 리스트를 활용해 데이터를 구조화하고, 오류 처리를 추가하면 안정적이고 효율적인 프로그램을 작성할 수 있다.
