
# Python 프로그램 — 2장 1교시
## 파일 읽기·쓰기 및 Linux 로그 처리

### 1. 파일 처리(File I/O)란?

파일 처리는 Python 프로그램이 저장장치에 있는 데이터를 읽거나 새로운 데이터를 파일에 저장하는 작업이다.

- 입력(Input): 파일에서 데이터 읽기
- 출력(Output): 파일에 데이터 쓰기

#### 파일 처리가 필요한 이유

기존 Python 실습에서는 서버 상태를 코드에 직접 작성했다.

```python
server = {
    "name": "WAS01",
    "status": "ERROR"
}
```

하지만 실제 운영에서는 서버 상태와 오류 정보가 로그 파일 등에 저장된다.

Python으로 이러한 파일을 읽으면 장애 로그 검색, 오류 집계, 분석 보고서 작성 등을 자동화할 수 있다.

**Marble 활용:**

```text
Marble 애플리케이션 로그
          ↓
Python으로 파일 읽기
          ↓
ERROR / WARN 로그 추출
          ↓
장애 건수 계산
          ↓
분석 결과 파일 저장
          ↓
향후 Ollama + Qwen에 전달
```

---

### 2. 절대경로와 상대경로

파일을 읽으려면 파일의 위치를 지정해야 한다.

| 구분 | 설명 | 예시 |
|---|---|---|
| 절대경로 | `/`부터 시작하는 전체 경로 | `/home/ubuntu/logs/marble.log` |
| 상대경로 | 현재 작업 디렉터리 기준 | `marble.log` |

#### Linux에서 경로 확인

```bash
pwd
ls -l
```

- `pwd`: 현재 작업 디렉터리 확인
- `ls -l`: 파일 목록과 권한 확인

#### Python으로 현재 경로 확인

```python
from pathlib import Path

print(Path.cwd())
```

`Path.cwd()`는 현재 작업 디렉터리를 반환한다.

---

### 3. open() 함수란?

`open()`은 파일을 읽거나 쓰기 위해 파일을 여는 Python 함수이다.

#### 기본 문법

```python
open("파일이름", "모드", encoding="utf-8")
```

#### 주요 파일 모드

| 모드 | 의미 | 특징 |
|---|---|---|
| `r` | Read | 파일 읽기 |
| `w` | Write | 기존 내용 삭제 후 작성 |
| `a` | Append | 기존 내용 유지하고 추가 |
| `x` | Create | 파일이 없을 때만 생성 |

**주의:** `w` 모드는 기존 파일의 내용을 지우므로 실제 서비스 로그나 설정 파일을 실수로 열지 않도록 주의한다.

---

### 4. with 문법이란?

`with`는 파일과 같은 자원을 안전하게 관리할 때 사용하는 문법이다.

```python
with open("marble.log", "r", encoding="utf-8") as file:
    content = file.read()

print(content)
```

#### 문법 해설

| 코드 | 의미 |
|---|---|
| `with` | 파일 자원 관리 |
| `open()` | 파일 열기 |
| `"r"` | 읽기 모드 |
| `encoding="utf-8"` | UTF-8 인코딩 지정 |
| `as file` | 열린 파일을 file 변수로 사용 |
| `file.read()` | 파일 내용 읽기 |

`with` 블록이 종료되면 파일이 자동으로 닫힌다.

**핵심:** 앞으로 파일을 처리할 때는 `with open(...) as file:` 형태를 기본으로 사용한다.

---

### 5. 파일을 읽는 세 가지 방법

#### ① read() — 파일 전체 읽기

```python
with open("marble.log", "r", encoding="utf-8") as file:
    content = file.read()

print(content)
```

파일 전체를 하나의 문자열로 가져온다.

작은 설정 파일이나 텍스트 파일을 읽을 때 편리하다.

#### ② readline() — 한 줄 읽기

```python
with open("marble.log", "r", encoding="utf-8") as file:
    line = file.readline()

print(line.strip())
```

파일에서 한 줄을 읽는다.

#### ③ for line in file — 한 줄씩 반복 처리

```python
with open("marble.log", "r", encoding="utf-8") as file:
    for line in file:
        print(line.strip())
```

파일의 내용을 한 줄씩 읽으면서 반복 처리한다.

| 방식 | 특징 | 주로 사용하는 상황 |
|---|---|---|
| `read()` | 전체 내용 읽기 | 작은 텍스트 파일 |
| `readline()` | 한 줄 읽기 | 특정 줄 확인 |
| `for line in file` | 한 줄씩 순회 | 로그 파일 분석 |

**핵심:** 대용량 로그를 분석할 때는 파일 전체를 한꺼번에 읽는 것보다 한 줄씩 처리하는 방식이 메모리 관리에 유리하다.

---

### 6. 문자열 처리

로그에서 필요한 정보를 추출하기 위해 문자열 메서드를 사용한다.

#### strip() — 공백과 줄바꿈 제거

```python
line = "ERROR Database connection failed\n"

print(line.strip())
```

결과:

```text
ERROR Database connection failed
```

#### in — 문자열 포함 여부 확인

```python
line = "ERROR Database connection failed"

if "ERROR" in line:
    print("오류 발견")
```

결과:

```text
오류 발견
```

#### split() — 문자열 나누기

```python
message = "WAS01,ERROR,95"

result = message.split(",")

print(result)
```

결과:

```text
['WAS01', 'ERROR', '95']
```

특정 구분자를 기준으로 문자열을 나누고 리스트로 반환한다.

```python
print(result[0])  # WAS01
print(result[1])  # ERROR
print(result[2])  # 95
```

#### split(" ", 3)

```python
line = "2026-11-06 10:02:00 ERROR Database connection failed"

parts = line.split(" ", 3)

print(parts)
```

결과:

```text
['2026-11-06', '10:02:00', 'ERROR', 'Database connection failed']
```

최대 세 번 나누어 날짜, 시간, 로그 수준, 메시지를 분리한다.

단, 실제 로그 형식에 따라 파싱 방법을 달리해야 한다.

---

### 7. 파일 쓰기 — write()

Python에서 파일에 내용을 기록할 때 `write()`를 사용한다.

#### w 모드: 새 보고서 작성

```python
with open("report.txt", "w", encoding="utf-8") as file:
    file.write("===== Marble 점검 결과 =====\n")
    file.write("WAS01 : 장애\n")
```

`w`는 기존 파일이 있으면 내용을 삭제하고 새로 작성한다.

#### a 모드: 기존 보고서에 내용 추가

```python
with open("report.txt", "a", encoding="utf-8") as file:
    file.write("WAS02 : 정상\n")
```

`a`는 기존 내용을 유지하면서 파일 끝에 데이터를 추가한다.

#### \n의 의미

`\n`은 줄바꿈 문자이다.

`write()`는 자동으로 줄바꿈을 추가하지 않으므로 필요한 경우 직접 작성한다.

---

### 8. Marble 로그 분석 실습

#### 실습 목표

1. 로그 파일 생성
2. Python으로 파일 읽기
3. 전체 로그 건수 계산
4. ERROR와 WARN 건수 계산
5. ERROR 로그 별도 추출
6. 분석 결과를 보고서 파일로 저장

#### ① 실습 디렉터리 준비

```bash
mkdir -p ~/python-lab/lesson05
cd ~/python-lab/lesson05
nano marble.log
```

#### ② 샘플 로그 작성

```text
2026-11-06 10:00:00 INFO Marble started
2026-11-06 10:01:00 INFO Database connected
2026-11-06 10:02:00 ERROR Database connection failed
2026-11-06 10:03:00 WARN Response time increased
2026-11-06 10:04:00 ERROR Connection refused
2026-11-06 10:05:00 INFO Health check successful
```

실습을 위한 가상의 Marble 로그이다.

#### ③ Python 코드 작성

```bash
nano marble_log_analyzer.py
```

```python
from pathlib import Path

# Python 소스 파일이 있는 디렉터리
BASE_DIR = Path(__file__).resolve().parent

# 로그 파일과 보고서 파일 경로
LOG_FILE = BASE_DIR / "marble.log"
REPORT_FILE = BASE_DIR / "marble_report.txt"

# 카운터 초기화
total_count = 0
error_count = 0
warn_count = 0

error_logs = []

# 로그 파일 읽기
with open(LOG_FILE, "r", encoding="utf-8") as file:

    for line in file:

        clean_line = line.strip()

        total_count += 1

        if "ERROR" in clean_line:
            error_count += 1
            error_logs.append(clean_line)

        elif "WARN" in clean_line:
            warn_count += 1

# 분석 결과 출력
print("===== Marble Log Analysis =====")

print(f"전체 로그: {total_count}건")
print(f"ERROR 로그: {error_count}건")
print(f"WARN 로그: {warn_count}건")

print("\n===== ERROR 상세 =====")

for error in error_logs:
    print(error)

# 분석 결과 파일 저장
with open(REPORT_FILE, "w", encoding="utf-8") as file:

    file.write("===== Marble 로그 분석 보고서 =====\n")
    file.write(f"전체 로그: {total_count}건\n")
    file.write(f"ERROR 로그: {error_count}건\n")
    file.write(f"WARN 로그: {warn_count}건\n")

    file.write("\n===== ERROR 상세 =====\n")

    for error in error_logs:
        file.write(error + "\n")

print("\n분석 보고서 저장 완료")
```

#### ④ 실행

```bash
python3 marble_log_analyzer.py
cat marble_report.txt
```

#### 예상 결과

```text
===== Marble 로그 분석 보고서 =====
전체 로그: 6건
ERROR 로그: 2건
WARN 로그: 1건

===== ERROR 상세 =====
2026-11-06 10:02:00 ERROR Database connection failed
2026-11-06 10:04:00 ERROR Connection refused
```

#### 핵심 코드 해설

| 코드 | 역할 |
|---|---|
| `Path(__file__).resolve().parent` | Python 소스 파일의 디렉터리 확인 |
| `for line in file` | 로그를 한 줄씩 처리 |
| `line.strip()` | 줄바꿈과 양쪽 공백 제거 |
| `total_count += 1` | 전체 로그 건수 증가 |
| `if "ERROR" in clean_line` | 오류 문구 포함 여부 확인 |
| `error_logs.append()` | 오류 로그를 리스트에 저장 |
| `file.write()` | 분석 결과를 파일에 기록 |

**핵심:** 1장에서 배운 변수·리스트·조건문·반복문을 이용하여 실제 텍스트 파일을 처리하는 프로그램을 만들었다.

---

### 9. 실제 Linux / Marble 로그 연동

Marble 서비스가 systemd로 실행되는 경우 `journalctl`로 로그를 확인할 수 있다.

#### 서비스 로그 조회

```bash
sudo journalctl -u marble.service -n 50 --no-pager
```

| 옵션 | 역할 |
|---|---|
| `journalctl` | systemd 저널 로그 조회 |
| `-u marble.service` | Marble 서비스 로그만 조회 |
| `-n 50` | 최근 50줄 |
| `--no-pager` | 페이지 구분 없이 출력 |

#### 로그를 파일에 저장

```bash
sudo journalctl -u marble.service -n 100 --no-pager \
  > ~/python-lab/lesson05/marble_journal.log
```

이 명령어는 실제 Marble WAS 서버에서 실행해야 한다.

`>`는 Linux 셸의 출력 리다이렉션 기호이며, 기존 파일이 있으면 덮어쓴다.

**주의:** 실제 로그에는 비밀번호, 토큰, 개인정보 등 민감한 값이 포함될 수 있으므로 AI 분석에 전달하기 전에 필요한 정보만 추출하고 민감한 값을 제거해야 한다.

---

### 10. 자주 발생하는 오류

| 오류 | 원인 | 해결 방법 |
|---|---|---|
| FileNotFoundError | 파일이 존재하지 않음 | 경로·파일 이름 확인 |
| PermissionError | 파일 접근 권한 부족 | 소유자·권한 확인 |
| UnicodeDecodeError | 텍스트 인코딩 불일치 | 실제 인코딩 확인 |
| IsADirectoryError | 디렉터리를 파일처럼 열려고 함 | 대상 경로 확인 |
| 파일 내용이 사라짐 | `w` 모드로 덮어씀 | `w`와 `a` 구분 |
| ERROR가 검색되지 않음 | 문자열 대소문자·로그 형식 차이 | 검색 조건 확인 |

#### 오류 발생 시 Linux 점검 명령

```bash
pwd
ls -l
ls -l marble.log
cat marble.log
```

권한 문제를 해결한다고 무조건 `chmod 777`을 사용하지 않는다.

---

### 11. 핵심 개념 최종 정리

| 개념 | 의미 |
|---|---|
| File I/O | 파일 입출력 |
| open() | 파일 열기 |
| r | 읽기 모드 |
| w | 덮어쓰기 모드 |
| a | 추가 쓰기 모드 |
| with | 파일 자원 안전 관리 |
| read() | 파일 전체 내용 읽기 |
| readline() | 파일 한 줄 읽기 |
| for line in file | 한 줄씩 반복 처리 |
| strip() | 양쪽 공백·줄바꿈 제거 |
| split() | 문자열 분리 |
| in | 문자열 포함 여부 확인 |
| write() | 파일에 문자열 기록 |
| Path | 파일·디렉터리 경로 관리 |
| \n | 줄바꿈 문자 |

### 12. 반드시 기억할 핵심 5가지

1. **Python의 파일 처리는 로그를 읽고 분석하는 운영 자동화의 기본이다.**
2. **`with open()`으로 파일을 안전하게 열고 닫을 수 있다.**
3. **`r`은 읽기, `w`는 덮어쓰기, `a`는 추가 쓰기이다.**
4. **`for line in file`과 `if`를 결합하면 원하는 로그만 추출할 수 있다.**
5. **분석 결과를 파일로 저장하면 이후 AIOps 장애 분석 데이터로 활용할 수 있다.**

---

### 13. 복습 확인문제 및 정답

**Q1. Python에서 파일을 열 때 사용하는 함수는?**

정답: `open()`

**Q2. 기존 파일 내용을 유지하면서 새 내용을 추가하는 모드는?**

정답: `a`(Append)

**Q3. with open()을 사용하는 이유는?**

정답: 파일 사용이 끝나면 자동으로 닫는 등 자원을 안전하게 관리하기 위해서이다.

**Q4. 로그 파일을 한 줄씩 처리하는 방법은?**

정답:

```python
for line in file:
    print(line.strip())
```

**Q5. ERROR 문자열이 포함된 로그만 추출하는 조건은?**

정답:

```python
if "ERROR" in line:
    print(line)
```

---

### 14. 다음 교시

**Python 2장 2교시 — JSON 데이터 처리**

학습 내용:

- JSON이란 무엇인가?
- JSON과 Python 딕셔너리의 차이
- json 모듈과 import
- json.load() / json.dump()
- json.loads() / json.dumps()
- Marble 장애 정보를 JSON 파일로 저장
- 이후 Zabbix API 및 Ollama API 연동 기초


<img width="1122" height="1402" alt="image" src="https://github.com/user-attachments/assets/ce3431fd-6542-4afc-bf9b-c46cd5c2a355" />

