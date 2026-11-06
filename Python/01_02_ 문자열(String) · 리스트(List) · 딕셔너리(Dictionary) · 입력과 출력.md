
# Python 프로그래밍 — 1장 2교시
## 문자열(String) · 리스트(List) · 딕셔너리(Dictionary) · 입력과 출력

### 1. 문자열(String)이란?

문자열은 문자나 텍스트를 저장하고 처리하는 자료형이다.

Python에서는 `str`이라고 한다.

```python
server_name = "WAS01"
server_ip = "10.0.21.126"
error_log = "ERROR: Database connection failed"
```

서버 이름, IP 주소, 서비스 이름, 오류 로그 등을 문자열로 관리할 수 있다.

#### 문자열 주요 기능

| 기능 | 의미 | 예시 |
|---|---|---|
| `len()` | 문자 개수 확인 | `len("WAS01")` → 5 |
| `.upper()` | 대문자로 변환 | `"error".upper()` → `"ERROR"` |
| `.lower()` | 소문자로 변환 | `"ERROR".lower()` → `"error"` |
| `.strip()` | 양쪽 공백 제거 | `" WAS01 ".strip()` → `"WAS01"` |
| `.replace()` | 특정 문자열 변경 | `"ERROR".replace("ERROR", "WARN")` |
| `in` | 문자열 포함 여부 | `"ERROR" in error_log` |

#### 실습: 오류 로그에서 ERROR 확인

```python
error_log = "  ERROR: Database Connection Failed  "

# 양쪽 공백 제거
clean_log = error_log.strip()

# 대문자로 변환
upper_log = clean_log.upper()

# ERROR 포함 여부 확인
print("ERROR" in upper_log)
```

실행 결과:

```text
True
```

**핵심:** 문자열 기능을 이용하면 장애 로그를 정리하거나 특정 오류 문구가 포함되어 있는지 확인할 수 있다.

---

### 2. 리스트(List)란?

리스트는 **여러 개의 데이터를 하나의 변수에 순서대로 저장하는 자료형**이다.

대괄호 `[ ]`를 사용한다.

```python
servers = ["WEB01", "WEB02", "WAS01", "WAS02"]
```

#### 리스트의 인덱스(Index)

인덱스는 리스트 안에서 데이터가 위치한 번호이다.

Python의 인덱스는 **0부터 시작한다.**

| 인덱스 | 값 |
|---|---|
| 0 | WEB01 |
| 1 | WEB02 |
| 2 | WAS01 |
| 3 | WAS02 |

```python
print(servers[0])  # WEB01
print(servers[2])  # WAS01
```

#### 리스트의 주요 기능

```python
servers = ["WEB01", "WEB02"]

# 데이터 추가
servers.append("WAS01")

# 데이터 개수
print(len(servers))

# 특정 데이터 변경
servers[1] = "WAS02"

print(servers)
```

실행 결과:

```text
3
['WEB01', 'WAS02', 'WAS01']
```

| 문법 | 역할 |
|---|---|
| `[ ]` | 리스트 생성 |
| `[0]` | 첫 번째 원소 접근 |
| `len()` | 리스트 원소 개수 확인 |
| `.append()` | 리스트 맨 뒤에 원소 추가 |
| `servers[1] = 값` | 지정한 위치의 원소 변경 |

**핵심:** 리스트는 여러 서버 이름이나 장애 서버 목록을 하나의 변수로 관리할 때 사용한다.

---

### 3. 딕셔너리(Dictionary)란?

딕셔너리는 **Key(키)와 Value(값)를 연결하여 데이터를 저장하는 자료형**이다.

중괄호 `{ }`를 사용한다.

#### Marble WAS01 정보 예시

```python
server = {
    "name": "WAS01",
    "ip": "10.0.21.126",
    "service": "marble",
    "port": 8080,
    "status": "NORMAL"
}
```

| Key | Value |
|---|---|
| name | WAS01 |
| ip | 10.0.21.126 |
| service | marble |
| port | 8080 |
| status | NORMAL |

#### 데이터 가져오기

```python
print(server["name"])
print(server["ip"])
```

실행 결과:

```text
WAS01
10.0.21.126
```

#### 데이터 변경

```python
server["status"] = "ERROR"
```

`status`라는 키에 저장된 값을 `NORMAL`에서 `ERROR`로 변경한다.

#### 새로운 데이터 추가

```python
server["cpu"] = 85.5
```

`cpu`라는 새로운 키와 값이 추가된다.

**핵심:** 딕셔너리는 서버의 이름, IP, 포트, 서비스 상태처럼 서로 관련된 여러 정보를 관리할 때 사용한다.

---

### 4. 리스트와 딕셔너리의 차이

| 구분 | List | Dictionary |
|---|---|---|
| 목적 | 여러 데이터 저장 | Key와 Value로 데이터 관리 |
| 기호 | `[ ]` | `{ }` |
| 접근 방법 | 인덱스 | 키 |
| 예시 | 서버 목록 | 서버 상세 정보 |
| Marble 활용 | 장애 서버 목록 | 장애 이벤트 상세 정보 |

#### 함께 사용하는 방법

```python
servers = [
    {
        "name": "WAS01",
        "ip": "10.0.21.126",
        "status": "NORMAL"
    },
    {
        "name": "WAS02",
        "ip": "10.0.22.9",
        "status": "ERROR"
    }
]

print(servers[0]["name"])
print(servers[1]["status"])
```

실행 결과:

```text
WAS01
ERROR
```

#### 코드 동작 원리

`servers[0]["name"]`

1. `servers` → 서버 목록 리스트
2. `[0]` → 첫 번째 서버 딕셔너리 선택
3. `["name"]` → 해당 서버의 이름 가져오기
4. 결과 → `WAS01`

**핵심:** 리스트 안에 딕셔너리를 저장하면 여러 서버의 상세 정보를 체계적으로 관리할 수 있다.

---

### 5. input() 함수란?

`input()`은 **사용자가 키보드로 입력한 값을 받아오는 함수**이다.

```python
server_name = input("서버 이름: ")

print(f"선택한 서버: {server_name}")
```

실행 예시:

```text
서버 이름: WAS01
선택한 서버: WAS01
```

#### input()의 주의사항

`input()`으로 받은 값은 기본적으로 문자열(`str`)이다.

```python
port = input("포트 번호: ")

print(type(port))
```

8080을 입력하더라도 결과는 다음과 같다.

```text
<class 'str'>
```

정수로 사용하려면 `int()`를 이용한다.

```python
port = int(input("포트 번호: "))
```

단, 숫자가 아닌 문자를 입력하면 `ValueError`가 발생할 수 있다.

---

### 6. Marble 서버 관리 통합 실습

#### 목표

WAS01과 WAS02의 정보를 리스트와 딕셔너리로 저장하고, 사용자가 선택한 서버의 정보를 출력한다.

#### 파일 생성

```bash
mkdir -p ~/python-lab/lesson02
cd ~/python-lab/lesson02
nano marble_servers.py
```

#### Python 코드

```python
# Marble 서버 목록

servers = [
    {
        "name": "WAS01",
        "ip": "10.0.21.126",
        "port": 8080
    },
    {
        "name": "WAS02",
        "ip": "10.0.22.9",
        "port": 8080
    }
]

print("===== Marble 서버 선택 =====")

print("0: WAS01")
print("1: WAS02")

# 서버 번호 입력
number = int(input("서버 번호: "))

# 선택한 서버 정보
server = servers[number]

print("===== 서버 정보 =====")

print(f"서버 이름: {server['name']}")
print(f"서버 IP: {server['ip']}")
print(f"서버 포트: {server['port']}")
```

#### 실행

```bash
python3 marble_servers.py
```

#### 예상 결과

```text
===== Marble 서버 선택 =====
0: WAS01
1: WAS02
서버 번호: 1

===== 서버 정보 =====
서버 이름: WAS02
서버 IP: 10.0.22.9
서버 포트: 8080
```

**주의:** 현재 프로그램은 올바른 번호 0 또는 1을 입력한다는 전제이며, 실제 서버에 접속하거나 상태를 확인하는 프로그램은 아니다.

---

### 7. 자주 발생하는 오류

| 오류 | 원인 | 해결 방법 |
|---|---|---|
| IndexError | 리스트에 없는 인덱스 접근 | 인덱스 범위 확인 |
| KeyError | 딕셔너리에 없는 키 사용 | 키 이름 확인 |
| ValueError | 변환할 수 없는 값을 숫자로 변환 | 입력값 확인 |
| SyntaxError | 괄호·따옴표 등 문법 오류 | 문법 확인 |
| NameError | 정의되지 않은 변수 사용 | 변수 이름 확인 |

#### IndexError 예시

```python
servers = ["WAS01", "WAS02"]

print(servers[2])
```

리스트의 인덱스는 0과 1까지만 존재하므로 오류가 발생한다.

#### KeyError 예시

```python
server = {"name": "WAS01"}

print(server["ip"])
```

`ip` 키가 존재하지 않아 오류가 발생한다.

---

### 8. Marble AIOps 적용

앞으로 Python 프로그램에서 다음과 같이 활용할 예정이다.

| 학습 개념 | AIOps 활용 |
|---|---|
| 문자열 | 장애 로그에서 오류 문구 확인 |
| 리스트 | 장애 서버 목록 저장 |
| 딕셔너리 | 장애 서버·시각·원인 등 정보 관리 |
| input() | 사용자가 점검할 서버 선택 |
| 리스트 + 딕셔너리 | 여러 서버의 상세 정보 처리 |

예를 들어 Zabbix에서 받은 장애 정보를 다음과 같은 형태로 관리할 수 있다.

```python
incident = {
    "server": "WAS01",
    "service": "marble",
    "status": "ERROR",
    "message": "Connection refused"
}
```

이 데이터를 나중에 JSON으로 변환해 Ollama API에 전달하는 자동화 프로그램으로 발전시킬 수 있다.

---

### 9. 핵심 개념 최종 정리

| 개념 | 설명 |
|---|---|
| str | 문자열 자료형 |
| len() | 문자열 길이·리스트 원소 개수 |
| upper() | 대문자 변환 |
| lower() | 소문자 변환 |
| strip() | 양쪽 공백 제거 |
| in | 포함 여부 확인 |
| list | 여러 데이터를 순서대로 저장 |
| index | 리스트의 위치 번호 |
| append() | 리스트에 원소 추가 |
| dict | Key와 Value로 데이터 저장 |
| Key | 값을 찾기 위한 이름 |
| Value | 키에 연결된 데이터 |
| input() | 사용자 입력받기 |
| int() | 정수 변환 |

### 10. 반드시 기억할 핵심 5가지

1. **문자열은 서버 이름과 로그 같은 텍스트를 처리한다.**
2. **리스트는 여러 데이터를 순서대로 저장하며 인덱스는 0부터 시작한다.**
3. **딕셔너리는 Key와 Value로 데이터를 저장한다.**
4. **input()은 사용자 입력을 문자열로 반환한다.**
5. **리스트와 딕셔너리를 함께 사용하면 여러 Marble 서버의 정보를 관리할 수 있다.**

---

### 11. 복습 확인문제 및 정답

**Q1. 문자열에 ERROR가 포함되어 있는지 확인하는 방법은?**

정답:

```python
"ERROR" in error_log
```

**Q2. 다음 리스트에서 WAS01을 가져오는 코드는?**

```python
servers = ["WEB01", "WEB02", "WAS01"]
```

정답:

```python
servers[2]
```

**Q3. 딕셔너리에서 ip 키에 저장된 값을 가져오는 방법은?**

정답:

```python
server["ip"]
```

**Q4. input() 함수가 기본적으로 반환하는 자료형은?**

정답: `str`(문자열)

**Q5. 여러 서버의 이름·IP·포트를 관리하기에 적합한 자료구조는?**

정답: 리스트 안에 서버별 딕셔너리를 저장하는 구조이다.

---

### 12. 다음 교시

**Python 1장 3교시 — 조건문·반복문·연산자**

학습 내용:
- 비교 연산자와 논리 연산자
- if / elif / else 조건문
- for / while 반복문
- break / continue
- 서버 목록 반복 처리
- Marble 서버의 장애 상태 자동 판단 실습

<img width="1122" height="1402" alt="image" src="https://github.com/user-attachments/assets/6859e3dc-5b88-414c-93c8-f10c08fecf35" />

