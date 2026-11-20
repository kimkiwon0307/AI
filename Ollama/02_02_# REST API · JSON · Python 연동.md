
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/b4109285-cfb2-4a6d-9888-7fed0f82d100" />

# Ollama + Qwen — 2장 2교시
## REST API · JSON · Python 연동

### 1. 핵심 개념

| 개념 | 설명 |
|---|---|
| API | 프로그램끼리 기능과 데이터를 주고받는 인터페이스 |
| REST API | HTTP를 활용하는 API 설계 방식 |
| HTTP POST | 서버에 데이터를 전달하여 처리 요청 |
| JSON | 프로그램 간 데이터 교환 형식 |
| requests | Python에서 HTTP 요청을 보내는 라이브러리 |
| payload | API에 전달할 요청 데이터 |
| Response | API 서버가 반환하는 응답 |

### 2. Python + Ollama 동작 원리

```text
장애 로그 (incident.log)
        ↓
Python이 로그 읽기
        ↓
payload 생성 (JSON 요청 데이터)
        ↓
requests.post() → Ollama API
        ↓
Qwen이 장애 로그 분석
        ↓
response.json() → AI 응답 처리
        ↓
analysis.json 파일 저장
```

### 3. 핵심 Python 코드

**① 라이브러리 및 API 주소**

```python
import requests

API_URL = "http://127.0.0.1:11434/api/chat"
```

**② API 요청 데이터(payload) 생성**

```python
payload = {
    "model": "qwen3:1.7b",
    "messages": [
        {
            "role": "system",
            "content": "당신은 Linux 장애 분석 전문가입니다."
        },
        {
            "role": "user",
            "content": "Nginx 502 오류를 분석해주세요."
        }
    ],
    "stream": False
}
```

**③ API 호출 및 응답 처리**

```python
response = requests.post(
    API_URL,
    json=payload,
    timeout=(5, 300)
)

response.raise_for_status()

result = response.json()
answer = result["message"]["content"]

print(answer)
```

### 4. 주요 Python 문법

| 코드 | 역할 |
|---|---|
| `import requests` | HTTP 라이브러리 가져오기 |
| `payload = {}` | 딕셔너리로 요청 데이터 작성 |
| `messages = []` | 리스트에 메시지 저장 |
| `requests.post()` | Ollama에 HTTP POST 요청 |
| `response.json()` | HTTP 응답 JSON을 Python 객체로 변환 |
| `json.loads()` | JSON 문자열을 Python 객체로 변환 |
| `Path().read_text()` | 파일 내용 읽기 |
| `Path().write_text()` | 파일에 내용 저장 |
| `try/except` | 오류 발생 시 예외 처리 |

### 5. 주요 오류 및 대응

| 오류 | 확인 사항 |
|---|---|
| Connection refused | Ollama 서비스·포트 확인 |
| ModuleNotFoundError | requests 설치 및 가상환경 확인 |
| Model not found | `ollama list`로 모델 확인 |
| Read timed out | 모델 실행 상태·서버 자원 확인 |
| JSON 처리 오류 | AI 응답 형식과 필수 항목 확인 |

### 6. AIOps 활용

이번 실습에서는 Python이 장애 로그를 읽고 Ollama API로 Qwen을 호출하여 분석 결과를 `analysis.json` 파일로 저장하는 프로그램을 구현했다.

향후 Zabbix Webhook과 연동하면 장애 이벤트를 자동으로 받아 AI 분석을 수행하도록 확장할 수 있다.

### 7. 핵심 정리

- Python의 `requests.post()`로 Ollama API를 호출한다.
- `payload`에 모델 이름과 System/User Prompt를 담는다.
- `response.json()`으로 서버 응답을 처리한다.
- AI 분석 결과를 JSON 파일로 저장할 수 있다.
- `try/except`로 API 통신 오류를 처리한다.
- AI가 제시한 원인은 실제 서버에서 검증해야 한다.

**핵심 흐름: Python → Ollama API → Qwen 분석 → Python 응답 처리 → JSON 저장**
