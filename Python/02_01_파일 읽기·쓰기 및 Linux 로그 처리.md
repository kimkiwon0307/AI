<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/93773374-4cef-41ec-93f6-cbadb92d10a6" />


# Python 2장 1교시 – 파일 읽기·쓰기 및 서버 로그 분석

## 1. 핵심 개념

Python은 파일을 읽고 수정하거나 새로운 파일을 생성할 수 있다.

서버 운영에서는 로그 파일을 분석하여 장애 메시지를 추출하고 결과를 저장하는 자동화에 활용한다.

### 파일 열기 모드

| 모드 | 의미 | 특징 |
|---|---|---|
| `r` | Read | 파일 읽기 |
| `w` | Write | 기존 내용 덮어쓰기 |
| `a` | Append | 기존 내용 뒤에 추가 |
| `x` | Create | 새 파일 생성, 이미 있으면 오류 |

## 2. 핵심 Python 문법

| 문법 | 역할 |
|---|---|
| `open()` | 파일 열기 |
| `with` | 파일 사용 후 자동으로 닫기 |
| `read()` | 파일 전체 읽기 |
| `write()` | 파일에 문자열 쓰기 |
| `writelines()` | 여러 문자열 기록 |
| `for line in file` | 파일 한 줄씩 읽기 |
| `append()` | 리스트에 데이터 추가 |
| `len()` | 데이터 개수 계산 |

### 파일 읽기

```python
with open("sample.log", "r", encoding="utf-8") as file:
    for line in file:
        print(line, end="")
```

- `with`: 파일을 안전하게 관리
- `"r"`: 읽기 모드
- `encoding="utf-8"`: 문자 인코딩 지정
- `for`: 로그를 한 줄씩 처리

## 3. 실습 – Marble ERROR 로그 분석

**목표:** 서버 로그에서 ERROR 메시지만 추출하여 보고서 생성

```python
error_lines = []

# 로그 읽기 및 ERROR 추출
with open("sample.log", "r", encoding="utf-8") as file:
    for line in file:
        if " ERROR " in line:
            error_lines.append(line)

# 오류 건수 계산
error_count = len(error_lines)

# 분석 결과 저장
with open("error_report.txt", "w", encoding="utf-8") as file:
    file.writelines(error_lines)
    file.write(f"Total ERROR: {error_count}\n")

print(f"ERROR 발생 건수: {error_count}")
```

### 동작 원리

1. `sample.log` 파일 열기
2. `for` 반복문으로 로그 한 줄씩 읽기
3. `if` 조건문으로 ERROR 탐색
4. `append()`로 오류 로그 저장
5. `len()`으로 오류 건수 계산
6. `error_report.txt` 파일 생성

## 4. 주요 오류와 해결 방법

| 오류 | 원인 | 해결 방법 |
|---|---|---|
| `FileNotFoundError` | 파일이 없음 | `pwd`, `ls`로 경로 확인 |
| `PermissionError` | 접근 권한 부족 | `ls -l`로 권한 확인 |
| `UnicodeDecodeError` | 인코딩 불일치 | 파일 인코딩 확인 |
| `IndentationError` | 들여쓰기 오류 | 공백 4칸 기준 정렬 |

## 5. 실무 활용

- Nginx·Spring Boot 로그 분석
- ERROR 메시지 자동 추출
- 장애 발생 건수 집계
- 장애 분석 보고서 생성
- 향후 Zabbix·Webhook·LLM 연동의 기초

## 핵심 한 줄 정리

**Python의 `with open()`으로 서버 로그를 읽고, `for`와 `if`로 오류를 추출한 뒤, 분석 결과를 파일에 저장할 수 있다.**
