
# Python 프로그래밍 — 1장 4교시
## 함수(Function)와 모듈(Module)

### 1. 함수(Function)란?

함수는 **특정 작업을 수행하는 코드를 하나로 묶고, 필요할 때 호출하여 재사용할 수 있도록 만든 것**이다.

#### 함수가 필요한 이유

기존에는 서버 상태를 확인할 때마다 같은 코드를 반복 작성해야 했다.

함수를 사용하면 다음과 같은 장점이 있다.

- 중복 코드 감소
- 코드 재사용
- 유지보수 편리
- 기능별 역할 분리
- 프로그램의 가독성 향상

**Marble 적용:** CPU 점검, 서비스 상태 확인, 장애 판단 등의 작업을 각각 함수로 만들어 재사용할 수 있다.

---

### 2. def — 함수 정의와 호출

Python에서는 `def` 키워드로 함수를 정의한다.

#### 기본 문법

```python
def 함수이름():
    실행할_코드
```

#### 예시

```python
def hello():
    print("Hello Python!")
    print("Marble AIOps")

hello()
```

실행 결과:

```text
Hello Python!
Marble AIOps
```

#### 문법 설명

| 문법 | 의미 |
|---|---|
| `def` | 함수 정의 |
| `hello` | 함수 이름 |
| `()` | 매개변수를 작성하는 위치 |
| `:` | 함수 본문 시작 |
| 들여쓰기 | 함수에 속한 코드 |
| `hello()` | 함수 호출 및 실행 |

**주의:** 함수를 정의했다고 바로 실행되는 것은 아니다. 함수 이름과 괄호를 이용해 호출해야 한다.

---

### 3. 매개변수(Parameter)와 인자(Argument)

함수에 외부 데이터를 전달할 때 사용하는 개념이다.

#### 매개변수(Parameter)

함수를 정의할 때 전달받을 값을 저장하는 변수이다.

#### 인자(Argument)

함수를 호출할 때 실제로 전달하는 값이다.

```python
def check_server(server_name):
    print(f"점검 서버: {server_name}")

check_server("WAS01")
check_server("WAS02")
```

실행 결과:

```text
점검 서버: WAS01
점검 서버: WAS02
```

| 구분 | 예시 | 의미 |
|---|---|---|
| Parameter | `server_name` | 함수가 값을 받을 변수 |
| Argument | `"WAS01"` | 실제 전달하는 값 |

#### 여러 매개변수 사용

```python
def check_cpu(server_name, cpu_usage):

    if cpu_usage >= 90:
        print(f"{server_name}: CPU 위험")
    else:
        print(f"{server_name}: CPU 정상")

check_cpu("WAS01", 95)
check_cpu("WAS02", 35)
```

실행 결과:

```text
WAS01: CPU 위험
WAS02: CPU 정상
```

**핵심:** 매개변수를 이용하면 동일한 함수에 서로 다른 서버 정보를 전달할 수 있다.

---

### 4. print()와 return의 차이

이번 교시에서 가장 중요한 개념이다.

- `print()` : 화면에 데이터를 출력한다.
- `return` : 함수의 실행 결과를 호출한 곳에 반환한다.

#### ① print() 사용

```python
def check_cpu(cpu):
    if cpu >= 90:
        print("위험")
    else:
        print("정상")

result = check_cpu(95)

print(result)
```

실행 결과:

```text
위험
None
```

함수가 `"위험"`을 화면에 출력했지만 값을 반환하지 않았으므로 `result`에는 `None`이 저장된다.

`None`은 값이 없음을 나타내는 특별한 값이다.

#### ② return 사용

```python
def check_cpu(cpu):
    if cpu >= 90:
        return "위험"
    else:
        return "정상"

result = check_cpu(95)

print(result)
```

실행 결과:

```text
위험
```

함수가 반환한 `"위험"`이 `result` 변수에 저장되었다.

#### return이 중요한 이유

반환한 결과를 다른 코드에서 사용할 수 있다.

```python
result = check_cpu(95)

if result == "위험":
    print("장애 분석 필요")
```

실행 결과:

```text
장애 분석 필요
```

**핵심:** 자동화 프로그램에서는 함수가 결과를 `return`하고, 다른 코드가 그 값을 이용해 다음 작업을 수행하는 경우가 많다.

또한 `return`이 실행되면 해당 함수는 즉시 종료된다.

---

### 5. 지역변수(Local Variable)와 전역변수(Global Variable)

#### 지역변수

함수 내부에서 정의한 일반적인 변수이다.

```python
def check_server():
    server_name = "WAS01"
    print(server_name)

check_server()
```

함수 내부에서는 `server_name`을 사용할 수 있다.

하지만 함수 밖에서 다음처럼 사용하면 오류가 발생한다.

```python
print(server_name)
```

일반적으로 `NameError`가 발생한다.

#### 지역변수의 값을 함수 밖에서 사용하려면?

`return`으로 값을 반환한다.

```python
def get_server_name():
    server_name = "WAS01"
    return server_name

result = get_server_name()

print(result)
```

실행 결과:

```text
WAS01
```

#### 전역변수

함수 밖에서 정의한 변수를 전역변수라고 한다.

전역변수를 함수 내부에서 참조할 수 있지만, 유지보수를 위해 필요한 데이터는 매개변수로 전달하고 결과를 반환하는 방식을 우선 사용하는 것이 좋다.

---

### 6. 모듈(Module)이란?

모듈은 **Python 함수와 변수 등의 코드를 담고 있는 파일**이다.

Python 파일은 일반적으로 `.py` 확장자를 사용한다.

#### 모듈이 필요한 이유

모든 기능을 하나의 Python 파일에 작성하면 코드가 길어지고 관리하기 어려워진다.

따라서 역할별로 파일을 분리한다.

예시:

```text
Marble 서버 점검 프로그램
│
├── server_tools.py
│   └── 서버 상태 판단 함수
│
└── marble_monitor.py
    └── 서버 목록 및 점검 실행
```

#### import란?

`import`는 다른 모듈을 불러오는 키워드이다.

```python
import server_tools
```

모듈에 정의된 함수는 다음과 같이 호출할 수 있다.

```python
server_tools.check_server(server)
```

| 개념 | 의미 |
|---|---|
| Function | 특정 작업을 수행하는 코드 묶음 |
| Module | 함수·변수 등을 담는 Python 파일 |
| import | 다른 모듈 불러오기 |
| `모듈명.함수명()` | 모듈에 정의된 함수 호출 |

---

### 7. Python 표준 모듈

Python에는 미리 만들어진 다양한 모듈이 있다.

| 모듈 | 역할 |
|---|---|
| `os` | 운영체제 관련 기능 |
| `sys` | Python 실행 환경 관련 기능 |
| `json` | JSON 데이터 처리 |
| `pathlib` | 파일 및 디렉터리 경로 관리 |
| `subprocess` | Linux 명령어 등 외부 프로그램 실행 |
| `datetime` | 날짜 및 시간 처리 |

예시:

```python
import os

print(os.getcwd())
```

`os.getcwd()`는 현재 작업 디렉터리 경로를 반환한다.

**Marble 적용:** 이후 `subprocess`, `json` 등의 모듈을 사용하여 서버 상태 확인과 API 데이터 처리를 구현한다.

---

### 8. Marble 서버 점검 모듈 실습

#### 실습 목표

서버 상태 판단 기능과 메인 프로그램을 서로 다른 Python 파일로 분리한다.

#### ① 작업 디렉터리 준비

```bash
mkdir -p ~/python-lab/lesson04
cd ~/python-lab/lesson04
```

#### ② server_tools.py 작성

```bash
nano server_tools.py
```

```python
# 서버 상태 판단 함수

def check_server(server):

    if server["status"] == "ERROR":
        return "서비스 장애"

    elif server["status"] != "NORMAL":
        return "상태 확인 필요"

    elif server["cpu"] >= 90:
        return "CPU 위험"

    elif server["cpu"] >= 70:
        return "CPU 경고"

    else:
        return "정상"
```

이 함수는 서버 정보를 딕셔너리로 전달받아 상태를 판단한 후 결과를 반환한다.

#### ③ marble_monitor.py 작성

```bash
nano marble_monitor.py
```

```python
import server_tools

# 서버 목록
servers = [
    {"name": "WEB01", "status": "NORMAL", "cpu": 35},
    {"name": "WEB02", "status": "NORMAL", "cpu": 92},
    {"name": "WAS01", "status": "ERROR", "cpu": 45},
    {"name": "WAS02", "status": "NORMAL", "cpu": 55},
    {"name": "DB01", "status": "NORMAL", "cpu": 30}
]

incidents = []

print("===== Marble Server Monitor =====")

for server in servers:

    # 서버 점검 함수 호출
    result = server_tools.check_server(server)

    print(f"{server['name']} : {result}")

    # 점검 필요 서버 저장
    if result != "정상":
        incidents.append({
            "name": server["name"],
            "reason": result
        })

print("\n===== 점검 결과 =====")

print(f"전체 서버: {len(servers)}대")
print(f"점검 필요 서버: {len(incidents)}대")

for incident in incidents:
    print(f"- {incident['name']}: {incident['reason']}")
```

#### ④ 실행

```bash
python3 marble_monitor.py
```

#### 예상 결과

```text
===== Marble Server Monitor =====
WEB01 : 정상
WEB02 : CPU 위험
WAS01 : 서비스 장애
WAS02 : 정상
DB01 : 정상

===== 점검 결과 =====
전체 서버: 5대
점검 필요 서버: 2대
- WEB02: CPU 위험
- WAS01: 서비스 장애
```

#### 코드 동작 원리

```text
marble_monitor.py 실행
          ↓
server_tools 모듈 불러오기
          ↓
서버 목록을 for 반복문으로 순회
          ↓
check_server(server) 함수 호출
          ↓
if / elif / else로 상태 판단
          ↓
return으로 결과 반환
          ↓
점검 필요 서버 리스트에 추가
          ↓
최종 결과 출력
```

**중요:** 지금은 직접 입력한 가상 서버 상태를 확인하는 프로그램이다. 실제 서버에 접속하여 CPU나 서비스 상태를 수집하는 기능은 이후 과정에서 구현한다.

---

### 9. 자주 발생하는 오류

| 오류 | 주요 원인 | 해결 방법 |
|---|---|---|
| SyntaxError | `def` 뒤 콜론 누락 등 | 함수 문법 확인 |
| IndentationError | 함수 내부 들여쓰기 오류 | 공백 4칸 등으로 통일 |
| NameError | 정의되지 않은 함수·변수 사용 | 이름과 정의 위치 확인 |
| TypeError | 함수에 필요한 인자 누락 | 매개변수 개수 확인 |
| ModuleNotFoundError | 모듈을 찾지 못함 | 파일 이름 및 경로 확인 |
| KeyError | 딕셔너리에 없는 키 사용 | 데이터 구조 확인 |

#### 자주 하는 실수: return 누락

```python
def check_status():
    print("ERROR")

result = check_status()

print(result)
```

결과:

```text
ERROR
None
```

다른 코드에서 결과를 활용하려면 `return`을 사용한다.

```python
def check_status():
    return "ERROR"

result = check_status()

print(result)
```

결과:

```text
ERROR
```

---

### 10. Marble AIOps 적용

앞으로 개발할 Marble AIOps에서도 함수와 모듈을 사용해 기능을 분리할 예정이다.

```text
marble-aiops/
│
├── main.py
│   └── 전체 프로그램 실행
│
├── zabbix_client.py
│   └── Zabbix 장애 정보 조회
│
├── elk_client.py
│   └── 장애 로그 검색
│
├── jenkins_client.py
│   └── 최근 배포 이력 조회
│
├── ai_analyzer.py
│   └── Ollama + Qwen 장애 분석
│
├── recovery.py
│   └── 안전한 복구 정책 처리
│
└── notifier.py
    └── Discord 알림
```

각 기능을 별도 모듈로 분리하면 다음과 같은 장점이 있다.

- Zabbix 연동 부분만 수정 가능
- AI 모델을 변경할 때 관련 코드만 수정 가능
- 장애 분석과 복구 실행 로직 분리
- 코드 테스트 및 유지보수 편리

---

### 11. 핵심 개념 최종 정리

| 개념 | 의미 |
|---|---|
| 함수(Function) | 특정 작업을 수행하는 코드 묶음 |
| def | 함수 정의 |
| 함수 호출 | 정의된 함수 실행 |
| Parameter | 함수 정의 시 값을 받는 변수 |
| Argument | 함수 호출 시 전달하는 실제 값 |
| return | 함수 결과 반환 및 실행 종료 |
| print() | 화면에 데이터 출력 |
| 지역변수 | 함수 내부 지역 범위의 변수 |
| 전역변수 | 함수 밖에서 정의한 변수 |
| Module | Python 코드를 담은 파일 |
| import | 다른 모듈 불러오기 |
| None | 값이 없음을 나타내는 특별한 값 |

### 12. 반드시 기억할 핵심 5가지

1. **함수는 반복되는 코드를 묶어 재사용하는 기능이다.**
2. **def로 함수를 정의하고 함수 이름과 괄호로 호출한다.**
3. **매개변수는 값을 받는 변수이고, 인자는 실제 전달하는 값이다.**
4. **print()는 화면 출력이고 return은 함수 결과 반환이다.**
5. **모듈은 Python 파일이며 import를 통해 다른 파일의 기능을 재사용할 수 있다.**

---

### 13. 복습 확인문제 및 정답

**Q1. Python에서 함수를 정의할 때 사용하는 키워드는?**

정답: `def`

**Q2. 함수 정의에서 전달받는 값을 나타내는 변수는?**

정답: Parameter(매개변수)

**Q3. 함수 결과를 호출한 곳으로 반환할 때 사용하는 문법은?**

정답: `return`

**Q4. 함수에서 return을 작성하지 않으면 기본적으로 반환하는 값은?**

정답: `None`

**Q5. 다른 Python 모듈을 불러오는 키워드는?**

정답: `import`

**Q6. 함수 내부의 일반적인 지역변수를 함수 밖에서 사용하려면?**

정답: 필요한 값을 return으로 반환하여 호출한 곳에서 받아 사용할 수 있다.

**Q7. 함수와 모듈을 사용하는 가장 큰 이유는?**

정답: 중복 코드를 줄이고, 기능을 분리하여 코드의 재사용성과 유지보수성을 높이기 위해서이다.

---

### 14. Python 1장 전체 학습 완료

| 교시 | 학습 내용 |
|---|---|
| 1교시 | Python 설치·변수·자료형 |
| 2교시 | 문자열·리스트·딕셔너리·입출력 |
| 3교시 | 조건문·반복문·연산자 |
| 4교시 | 함수·모듈 |

#### 1장 전체 기술의 연결

```text
변수와 자료형
      ↓
리스트와 딕셔너리
      ↓
조건문과 반복문
      ↓
함수 정의 및 호출
      ↓
모듈 분리와 import
      ↓
Marble 서버 점검 자동화 프로그램
```

### 다음 교시

**Python 2장 1교시 — 파일 읽기·쓰기 및 Linux 로그 처리**

- 파일 읽기와 쓰기가 필요한 이유
- open()과 with 문법
- 파일 경로 이해
- 로그에서 필요한 문자열 추출
- Marble 애플리케이션 로그 읽기 실습


<img width="1122" height="1402" alt="image" src="https://github.com/user-attachments/assets/77b4111d-549c-4300-bae2-d84ffa57acf4" />
