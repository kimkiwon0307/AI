<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/38168a59-75b2-47e4-ad4c-b71602b0eb13" />


# Python 2장 3교시 – 예외 처리(try·except)와 logging

## 1. 핵심 개념

**예외 처리(Exception Handling)** 는 프로그램 실행 중 오류가 발생했을 때 원인을 확인하고 적절하게 대응하는 기능이다.

**logging** 은 프로그램 실행 정보, 경고, 오류 등을 로그 파일에 기록하는 기능이다.

Marble AIOps에서는 JSON 장애 데이터가 잘못되거나 파일이 없어도 오류를 기록하고 안전하게 처리하기 위해 사용한다.

## 2. 핵심 Python 문법

### 예외 처리

| 문법 | 역할 |
|---|---|
| `try` | 오류가 발생할 수 있는 코드 실행 |
| `except` | 예외 발생 시 처리 |
| `else` | 예외 없이 정상 실행되면 수행 |
| `finally` | 정상·오류 여부와 관계없이 마무리 작업 |
| `raise` | 개발자가 직접 예외 발생 |
| `as e` | 발생한 예외 객체를 변수에 저장 |

```python
try:
    cpu = int("ERROR")

except ValueError as e:
    print(f"변환 오류: {e}")

else:
    print("정상 처리")

finally:
    print("작업 종료")
```

### logging

| 로그 레벨 | 의미 |
|---|---|
| `DEBUG` | 상세 디버깅 정보 |
| `INFO` | 정상 실행 정보 |
| `WARNING` | 주의가 필요한 상황 |
| `ERROR` | 오류 발생 |
| `CRITICAL` | 매우 심각한 오류 |

```python
import logging

logging.basicConfig(
    filename="aiops.log",
    level=logging.INFO,
    format="%(asctime)s [%(levelname)s] %(message)s",
    encoding="utf-8"
)

logging.info("점검 시작")
logging.warning("CPU 사용률 높음")
logging.error("JSON 처리 실패")
```

**주요 설정 옵션**

- `filename`: 로그 저장 경로
- `level`: 기록할 최소 로그 레벨
- `format`: 로그 출력 형식
- `encoding`: 문자 인코딩

## 3. 실습 – Marble JSON 장애 처리

**목표:** JSON 파일을 읽고 CPU 상태를 판단하며, 예외 발생 시 로그를 기록한다.

```python
import json
import logging

logging.basicConfig(
    filename="aiops.log",
    level=logging.INFO,
    format="%(asctime)s [%(levelname)s] %(message)s",
    encoding="utf-8"
)

try:
    # JSON 읽기
    with open("alert.json", "r", encoding="utf-8") as file:
        alert = json.load(file)

    # 서버 정보 추출
    host = alert["host"]
    cpu = alert["metrics"]["cpu"]

    # CPU 데이터 검증
    if isinstance(cpu, bool) or not isinstance(cpu, (int, float)) or not 0 <= cpu <= 100:
        raise ValueError("잘못된 CPU 값")

    # 상태 판단 및 로그 기록
    if cpu >= 90:
        logging.error(f"{host} CPU 위험: {cpu}%")
    elif cpu >= 80:
        logging.warning(f"{host} CPU 경고: {cpu}%")
    else:
        logging.info(f"{host} CPU 정상: {cpu}%")

except FileNotFoundError as e:
    logging.error(f"파일 없음: {e}")

except json.JSONDecodeError as e:
    logging.error(f"JSON 문법 오류: {e}")

except (KeyError, TypeError, ValueError) as e:
    logging.error(f"데이터 오류: {e}")

else:
    print("분석 완료")

finally:
    print("작업 종료")
```

### 실행 및 검증

```bash
python3 marble_error_handler.py
cat aiops.log
```

정상적인 CPU 사용률이 91.5%라면 다음과 같은 로그가 기록된다.

```text
2026-11-27 13:00:00 [ERROR] was01 CPU 위험: 91.5%
```

※ 실제 시간은 실행 시점에 따라 달라진다.

## 4. 주요 오류와 해결 방법

| 오류 | 원인 | 해결 방법 |
|---|---|---|
| `FileNotFoundError` | 파일 없음 | `pwd`, `ls -l`로 경로 확인 |
| `JSONDecodeError` | JSON 문법 오류 | `python3 -m json.tool` 검사 |
| `KeyError` | 필수 Key 누락 | JSON 구조 확인 |
| `TypeError` | 잘못된 자료형 | `type()`, `isinstance()` 확인 |
| `ValueError` | 허용되지 않는 값 | 데이터 범위 검증 |
| `PermissionError` | 파일 접근 권한 부족 | 파일 소유자·권한 확인 |

## 5. 실무 활용

- Zabbix Webhook에서 전달받은 장애 데이터 검증
- JSON 형식 오류 및 필수 데이터 누락 처리
- CPU·메모리 사용률 이상 여부 판단
- 자동화 스크립트 실행 기록 저장
- 장애 원인 분석 및 문제 해결
- 향후 LLM 분석·자동복구 시스템의 안전성 강화

**주의:** `except: pass`처럼 모든 예외를 무조건 무시하지 않는다. 오류를 기록하고 필요한 경우 실패 상태를 상위 시스템에 전달해야 한다.

## 핵심 한 줄 정리

**Python의 `try·except`로 장애 데이터 처리 중 발생하는 예외에 대응하고, `logging`으로 실행 기록과 오류 원인을 파일에 저장하여 안정적인 인프라 자동화를 구현할 수 있다.**
