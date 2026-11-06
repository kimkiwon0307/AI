
# Python 프로그래밍 — 1장 1교시
## Python 개념·설치·변수·자료형

### 1. Python이란?

Python은 **사람이 작성한 명령을 컴퓨터가 실행하도록 만드는 프로그래밍 언어**이다.

문법이 비교적 간결하고 읽기 쉬워 다양한 분야에서 사용된다.

대표적인 활용 분야:
- Linux 서버 운영 자동화
- AWS 클라우드 자원 관리
- 로그 및 JSON 데이터 처리
- API 연동
- AI 모델 활용
- AIOps 장애 분석 및 자동 복구

#### Python이 필요한 이유

Linux에서는 명령어를 직접 실행해 서버 상태를 확인할 수 있다.

```bash
df -h
free -h
systemctl status marble
```

하지만 서버가 많아지고 반복 작업이 증가하면 수동 관리가 번거로워진다.

Python을 사용하면 여러 작업을 하나의 프로그램으로 묶어 자동화할 수 있다.

**Marble 적용:** Python으로 Zabbix·ELK·Jenkins·Ollama를 연결하여 장애 정보 수집과 AI 분석을 자동화할 예정이다.

---

### 2. Shell Script와 Python의 차이

| 구분 | Shell Script | Python |
|---|---|---|
| 주된 강점 | Linux 명령어 실행 및 연결 | 데이터 처리와 복잡한 자동화 |
| 문법 | Bash 등 Shell 문법 | Python 문법 |
| Linux 명령 실행 | 직접 실행하기 편리 | subprocess 등 활용 |
| JSON 처리 | jq 등 사용 가능 | 표준 라이브러리 지원 |
| API 연동 | curl 등 활용 | requests 등 활용 |
| Marble 활용 | 백업·서비스 제어 | 장애 수집·AI 연동 |

Shell Script와 Python은 서로 대체하는 기술이 아니라 함께 사용할 수 있다.

---

### 3. Python 설치 및 실행

#### ① Python 설치 확인

```bash
python3 --version
which python3
```

| 명령어 | 의미 |
|---|---|
| python3 | Python 3 실행 프로그램 |
| --version | 설치된 버전 확인 |
| which python3 | 실행 파일 경로 확인 |

Ubuntu 24.04에서는 일반적으로 Python 3.12 계열을 사용한다.

#### ② 설치되지 않은 경우

```bash
sudo apt update
sudo apt install -y python3
```

#### ③ Python 파일 실행

Python 소스 파일은 일반적으로 `.py` 확장자를 사용한다.

```bash
python3 hello.py
```

Python 인터프리터가 파일의 코드를 처리하고 실행한다.

**핵심:** `python3 파일명.py` 명령어로 Python 프로그램을 실행한다.

---

### 4. 첫 번째 Python 프로그램

#### 작업 디렉터리 생성

```bash
mkdir -p ~/python-lab/lesson01
cd ~/python-lab/lesson01
```

#### Python 파일 작성

```bash
nano hello.py
```

다음 코드를 입력한다.

```python
print("Hello Python!")
print("Marble AIOps 학습 시작")
```

#### 실행

```bash
python3 hello.py
```

#### 실행 결과

```text
Hello Python!
Marble AIOps 학습 시작
```

#### print()란?

`print()`는 데이터를 화면에 출력하는 Python 내장 함수이다.

```python
print("Hello Python!")
```

- `print`: 출력 기능을 수행하는 함수
- `()`: 함수에 전달할 값을 감싸는 괄호
- `"Hello Python!"`: 출력할 문자열

---

### 5. 변수(Variable)란?

변수는 **프로그램에서 값을 저장하고 참조하기 위해 사용하는 이름**이다.

예시:

```python
server_name = "WAS01"
server_ip = "10.0.21.126"
server_port = 8080
```

여기서:
- `server_name`: 변수 이름
- `=`: 값을 할당하는 연산자
- `"WAS01"`: 변수에 할당된 문자열

변수를 사용하면 값을 여러 곳에서 재사용할 수 있다.

```python
server_name = "WAS01"

print(server_name)
print(server_name)
```

출력 결과:

```text
WAS01
WAS01
```

변수에 저장된 값을 변경하면 이후 해당 변수를 사용하는 부분에 새로운 값이 반영된다.

**주의:** Python에서 `=`는 값의 할당이고, 값이 같은지 비교할 때는 `==`를 사용한다.

---

### 6. 자료형(Data Type)이란?

자료형은 데이터가 어떤 종류인지 나타낸다.

Python의 대표적인 기본 자료형은 다음과 같다.

| 자료형 | 의미 | 예시 |
|---|---|---|
| str | 문자열 | `"WAS01"` |
| int | 정수 | `8080` |
| float | 실수 | `23.5` |
| bool | 참 또는 거짓 | `True`, `False` |

#### ① 문자열(str)

```python
server_name = "WAS01"
server_ip = "10.0.21.126"
```

문자나 텍스트를 저장한다.

IP 주소도 일반적으로 문자열로 다룬다.

#### ② 정수(int)

```python
server_port = 8080
```

소수점이 없는 정수를 저장한다.

#### ③ 실수(float)

```python
cpu_usage = 23.5
```

소수점이 있는 숫자를 표현한다.

#### ④ 불리언(bool)

```python
is_running = True
```

참 또는 거짓을 나타낸다.

Python에서는 `True`, `False`처럼 첫 글자를 대문자로 작성한다.

---

### 7. f-string이란?

f-string은 문자열 안에 변수 값을 삽입하는 Python 문법이다.

#### 사용 예시

```python
server_name = "WAS01"

print(f"서버 이름: {server_name}")
```

실행 결과:

```text
서버 이름: WAS01
```

#### 문법 설명

| 표현 | 의미 |
|---|---|
| f | 변수나 표현식을 삽입하는 문자열 |
| `" "` | 문자열 |
| `{server_name}` | 변수 값을 문자열에 삽입 |
| print() | 결과 출력 |

**핵심:** f-string을 사용하면 변수 값을 포함한 문장을 쉽게 만들 수 있다.

---

### 8. type()과 주석(Comment)

#### ① type()

`type()`은 값의 자료형을 확인하는 함수이다.

```python
server_name = "WAS01"
server_port = 8080
cpu_usage = 23.5
is_running = True

print(type(server_name))
print(type(server_port))
print(type(cpu_usage))
print(type(is_running))
```

실행 결과:

```text
<class 'str'>
<class 'int'>
<class 'float'>
<class 'bool'>
```

Python은 변수에 할당된 값에 따라 자료형이 결정되는 동적 타이핑 언어이다.

#### ② 주석(Comment)

주석은 코드에 설명을 남기기 위한 문법이다.

```python
# Marble 서버 정보
server_name = "WAS01"
```

`#` 이후의 내용은 해당 줄에서 실행되지 않는다.

---

### 9. Marble 서버 정보 출력 실습

#### 파일 작성

```bash
nano server_info.py
```

#### Python 코드

```python
# Marble WAS01 서버 정보

server_name = "WAS01"
server_ip = "10.0.21.126"
server_port = 8080

cpu_usage = 23.5
is_running = True

# 출력

print("===== Marble Server Info =====")

print(f"서버 이름: {server_name}")
print(f"서버 IP: {server_ip}")
print(f"서버 포트: {server_port}")
print(f"CPU 사용률: {cpu_usage}%")
print(f"서비스 실행 상태: {is_running}")

print("==============================")
```

#### 실행

```bash
python3 server_info.py
```

#### 예상 결과

```text
===== Marble Server Info =====
서버 이름: WAS01
서버 IP: 10.0.21.126
서버 포트: 8080
CPU 사용률: 23.5%
서비스 실행 상태: True
==============================
```

**주의:** 현재 코드는 실제 WAS01 서버에 접속하여 상태를 확인하는 프로그램이 아니다.

변수에 직접 입력한 정보를 출력하는 기초 실습이다.

실제 서버 상태를 자동으로 수집하는 기능은 이후 Linux 운영 자동화 과정에서 학습한다.

---

### 10. 자주 발생하는 오류

| 오류 | 원인 | 해결 방법 |
|---|---|---|
| SyntaxError | Python 문법 오류 | 괄호·따옴표 확인 |
| NameError | 정의되지 않은 변수 사용 | 변수 이름 확인 |
| IndentationError | 잘못된 들여쓰기 | 공백과 들여쓰기 확인 |
| FileNotFoundError | 파일을 찾지 못함 | 파일 경로 확인 |
| python3: command not found | 실행 프로그램을 찾지 못함 | Python 설치·경로 확인 |

#### 변수 이름 오타 예시

```python
server_name = "WAS01"

print(server_nam)
```

`server_name`과 `server_nam`은 서로 다른 변수 이름이므로 NameError가 발생한다.

올바른 코드:

```python
print(server_name)
```

Python은 변수 이름의 대문자와 소문자를 구분한다.

---

### 11. 핵심 개념 정리

| 개념 | 설명 |
|---|---|
| Python | 자동화와 데이터 처리에 사용하는 프로그래밍 언어 |
| Interpreter | Python 코드를 처리·실행하는 프로그램 |
| .py | Python 소스 파일 확장자 |
| print() | 화면 출력 |
| 변수 | 값을 저장하고 참조하는 이름 |
| = | 값 할당 |
| str | 문자열 |
| int | 정수 |
| float | 실수 |
| bool | 참·거짓 |
| type() | 자료형 확인 |
| f-string | 문자열 안에 변수 값 삽입 |
| # | 주석 |

---

### 12. 반드시 기억할 핵심 5가지

1. **Python은 인프라 운영 자동화와 AI 연동에 활용할 수 있는 프로그래밍 언어이다.**
2. **`python3 파일명.py` 명령어로 Python 프로그램을 실행한다.**
3. **변수는 값을 저장하고 참조하기 위한 이름이다.**
4. **str, int, float, bool은 기본 자료형이다.**
5. **print()와 f-string으로 변수에 저장된 서버 정보를 화면에 출력할 수 있다.**

---

### 13. 복습 확인문제 및 정답

**Q1. Python이란 무엇인가?**

정답: 서버 운영 자동화, 데이터 처리, API 연동 등에 사용할 수 있는 프로그래밍 언어이다.

**Q2. Python 파일을 실행하는 명령어는?**

정답:

```bash
python3 server_info.py
```

**Q3. 다음 코드에서 server_name은 무엇인가?**

```python
server_name = "WAS01"
```

정답: 변수 이름이다.

**Q4. 다음 코드의 자료형은 무엇인가?**

```python
cpu_usage = 23.5
```

정답: float(실수)

**Q5. 변수에 저장된 서버 이름을 문자열 안에 넣어 출력하는 방법은?**

정답:

```python
print(f"서버 이름: {server_name}")
```

---

### 14. 다음 교시

**Python 1장 2교시 — 문자열·List·Dictionary·입출력**

학습 내용:
- 문자열 처리
- List의 개념과 사용법
- Dictionary의 개념과 사용법
- 여러 서버 정보를 하나의 자료구조로 관리
- Python 입력과 출력
- Marble WEB01/02·WAS01/02 서버 정보 관리 실습


<img width="1122" height="1402" alt="image" src="https://github.com/user-attachments/assets/a844f34b-64e2-4636-9e3d-5f39697df53a" />
