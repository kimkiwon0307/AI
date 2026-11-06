
# AI 기초 이론 — 3장 1교시
## AI API와 Python 연동 원리

### 1. API란 무엇인가?

API(Application Programming Interface)는 **서로 다른 프로그램이 정해진 방법으로 기능이나 데이터를 요청하고 결과를 주고받도록 하는 인터페이스**이다.

예를 들어 Python 프로그램에서 다음 작업을 수행할 수 있다.

- Zabbix API → 장애 이벤트 및 메트릭 조회
- Elasticsearch API → 장애 로그 검색
- Jenkins API → 최근 배포 이력 조회
- Ollama API → AI 장애 분석 요청

#### API 동작 원리

```text
Python 프로그램 (클라이언트)
          ↓
       API 요청
          ↓
Ollama (API 서버)
          ↓
Qwen 모델이 장애 분석
          ↓
       API 응답
          ↓
Python이 분석 결과 수신
```

**핵심:** Python이 AI 모델의 내부 구조를 직접 조작하지 않고도 API를 이용해 분석을 요청할 수 있다.

---

### 2. HTTP란 무엇인가?

HTTP(Hypertext Transfer Protocol)는 클라이언트와 서버가 요청과 응답을 주고받는 통신 프로토콜이다.

웹 브라우저뿐 아니라 Python과 Ollama 같은 프로그램 사이의 통신에도 사용된다.

#### 대표적인 HTTP 메서드

| 메서드 | 주요 용도 | 예시 |
|---|---|---|
| GET | 데이터 조회 | 서버 상태 조회 |
| POST | 데이터 전송 및 처리 요청 | AI 장애 분석 요청 |
| PUT | 리소스 수정·교체 | 설정 변경 |
| DELETE | 리소스 삭제 | 등록된 자원 삭제 |

예를 들어 Python이 Ollama에 장애 로그를 전달하고 분석을 요청할 때는 POST 메서드를 사용한다.

#### REST API란?

REST는 HTTP를 활용하여 리소스 중심으로 API를 설계하는 방식이다.

예시:

- `GET /servers` → 서버 목록 조회
- `GET /servers/1` → 1번 서버 조회

단, 모든 HTTP API가 REST 방식인 것은 아니다.

**핵심:** HTTP는 통신 프로토콜이고, REST는 API를 설계하는 방식 중 하나이다.

---

### 3. JSON이란 무엇인가?

JSON(JavaScript Object Notation)은 프로그램 사이에서 데이터를 주고받을 때 사용하는 구조화된 데이터 표현 형식이다.

#### JSON 예시

```json
{
  "server": "WAS01",
  "service": "marble",
  "status": "ERROR",
  "port": 8080
}
```

#### JSON 주요 문법

| 표현 | 의미 |
|---|---|
| `{ }` | 객체(Object) |
| `"server"` | 키(Key) |
| `"WAS01"` | 값(Value) |
| `:` | 키와 값 구분 |
| `,` | 데이터 항목 구분 |
| `[ ]` | 배열(Array) |

JSON은 문자열, 숫자, 참·거짓, 배열, 객체 등을 표현할 수 있다.

**Marble 적용:** Python은 Zabbix에서 받은 장애 정보와 Ollama에서 반환한 분석 결과를 JSON 형태로 처리할 수 있다.

---

### 4. Ollama API란?

Ollama는 AI 모델을 실행하고 외부 프로그램에서 호출할 수 있는 HTTP API를 제공한다.

기본 로컬 API 주소:

`http://localhost:11434/api`

#### 주소 구성

| 항목 | 의미 |
|---|---|
| `http://` | HTTP 프로토콜 |
| `localhost` | 현재 컴퓨터 자신 |
| `11434` | Ollama 기본 포트 |
| `/api` | API 경로 |

Python과 Ollama가 서로 다른 서버에 있다면 `localhost` 대신 연결 가능한 서버 주소를 사용해야 하며, 안전한 네트워크 접근 제어가 필요하다.

#### AI 분석 요청 예시

Ollama의 `/api/generate`에 POST 요청을 보낸다.

```json
{
  "model": "qwen2.5-coder:7b",
  "prompt": "MySQL 연결 오류의 원인을 설명해주세요.",
  "stream": false
}
```

| 항목 | 역할 |
|---|---|
| model | 사용할 AI 모델 |
| prompt | AI에게 전달하는 질문 |
| stream | 응답을 나눠 받을지 설정 |

`stream: false`는 완성된 답변을 하나의 응답으로 받겠다는 뜻이다.

#### AI 응답 예시

```json
{
  "model": "qwen2.5-coder:7b",
  "response": "MySQL 서비스 상태와 3306 포트 연결을 확인하세요.",
  "done": true
}
```

설명을 위해 일부 필드만 표시한 예시이다.

- `model`: 사용한 모델
- `response`: AI가 생성한 답변
- `done`: 응답 생성 완료 여부

**핵심:** Python은 JSON으로 AI 분석을 요청하고, Ollama는 JSON으로 분석 결과를 반환한다.

---

### 5. Python과 Ollama는 어떻게 연결되는가?

Python에서는 `requests` 라이브러리를 이용해 HTTP 요청을 보낼 수 있다.

#### 기본 예시

```python
import requests

# 분석할 오류 로그
error_log = "Communications link failure"

# Ollama API 호출
response = requests.post(
    "http://localhost:11434/api/generate",
    json={
        "model": "qwen2.5-coder:7b",
        "prompt": f"이 오류의 가능한 원인을 설명해줘: {error_log}",
        "stream": False
    },
    timeout=120
)

response.raise_for_status()

# AI 분석 결과 출력
print(response.json()["response"])
```

이 코드는 requests가 설치되어 있고, Ollama와 해당 모델이 준비되어 있다는 전제에서 동작한다.

#### 코드의 핵심 의미

| 코드 | 설명 |
|---|---|
| `import requests` | HTTP 통신 라이브러리 불러오기 |
| `error_log = ...` | 오류 로그를 변수에 저장 |
| `requests.post()` | HTTP POST 요청 |
| `json={}` | JSON 요청 데이터 |
| `timeout=120` | 응답 대기 시간 제한 |
| `raise_for_status()` | HTTP 오류 확인 |
| `response.json()` | JSON 응답 읽기 |
| `["response"]` | AI가 생성한 답변 추출 |

실제 운영에서는 API 연결 실패, 타임아웃, JSON 파싱 오류 등에 대한 예외 처리도 필요하다.

**핵심:** Python은 장애 데이터를 AI에 전달하고 결과를 받아 다음 자동화 작업으로 연결하는 역할을 한다.

---

### 6. API와 Webhook의 차이

두 기술은 모두 시스템 간 통신에 사용할 수 있지만 동작 방식에 차이가 있다.

#### API 조회 방식

Python이 Zabbix에 데이터를 요청한다.

```text
Python
   ↓ 장애 정보 조회 요청
Zabbix API
   ↓ 장애 정보 반환
Python
```

#### Webhook 방식

Zabbix에서 장애가 발생하면 Python으로 이벤트를 전달한다.

```text
WAS01 장애 발생
       ↓
Zabbix 장애 감지
       ↓
Webhook 자동 전송
       ↓
Python 수신 서버
```

| 구분 | API 조회 | Webhook |
|---|---|---|
| 통신 시작 | Python 등 요청 프로그램 | 이벤트가 발생한 시스템 |
| 목적 | 필요한 데이터 조회 | 이벤트 자동 통지 |
| Marble 예시 | Zabbix 장애 목록 조회 | 장애 발생 즉시 Python에 전달 |

Webhook도 HTTP API를 호출하는 방식으로 구현할 수 있다.

Python에서는 FastAPI 같은 프레임워크를 이용해 Webhook을 수신하는 서버를 만들 수 있다.

---

### 7. Marble AIOps 적용

최종적으로 구축할 시스템은 다음과 같다.

```text
① Marble WEB/WAS/DB 장애 발생
              ↓
② Zabbix 장애 감지
              ↓
③ Webhook으로 Python에 이벤트 전달
              ↓
④ Python이 API를 이용해 정보 수집
   ├── Zabbix → 서버 상태 및 메트릭
   ├── ELK → 관련 장애 로그
   └── Jenkins → 최근 배포 이력
              ↓
⑤ Python이 Ollama API에 분석 요청
              ↓
⑥ Qwen이 장애 원인 가설 생성
              ↓
⑦ Python이 AI 분석 결과 수신
              ↓
⑧ 복구 정책 및 안전 조건 검증
              ↓
⑨ 허용된 단순 장애 자동 복구
              ↓
⑩ Health Check 및 Discord 알림
```

#### 각 기술의 역할

| 기술 | 역할 |
|---|---|
| Zabbix | 장애 감지 |
| Webhook | 장애 이벤트 자동 전달 |
| Python | 장애 정보 수집 및 자동화 |
| ELK | 장애 로그 검색 |
| Jenkins API | 배포 이력 조회 |
| Ollama | AI 모델 실행 및 API 제공 |
| Qwen | 장애 원인 분석 |
| 복구 정책 엔진 | 허용된 복구 작업 실행 |
| Discord | 장애 및 복구 결과 알림 |

#### 자동 복구 시 주의사항

AI가 "MySQL을 재시작하세요"라고 답변했다고 해서 바로 실행하면 안 된다.

실제 서버 상태를 확인하고 사전에 허용한 복구 조건을 충족해야 실행하도록 구성한다.

또한 Ollama API를 외부 인터넷에 인증이나 접근 제한 없이 노출하지 않아야 한다.

---

### 8. 핵심 개념 최종 정리

| 용어 | 의미 |
|---|---|
| API | 프로그램끼리 데이터와 기능을 주고받는 인터페이스 |
| HTTP | 클라이언트·서버 요청 및 응답 프로토콜 |
| GET | 주로 데이터 조회 |
| POST | 주로 데이터 전송·처리 요청 |
| REST API | HTTP 기반 API 설계 방식 |
| JSON | 구조화된 데이터 표현 형식 |
| Webhook | 이벤트 발생 시 자동으로 정보 전달 |
| FastAPI | Python 기반 API 서버 개발 프레임워크 |
| Ollama API | AI 모델 호출을 위한 인터페이스 |

### 반드시 기억할 핵심 5가지

1. **API는 서로 다른 프로그램을 연결하는 인터페이스이다.**
2. **HTTP는 요청과 응답을 주고받는 프로토콜이다.**
3. **JSON은 API 통신에서 데이터를 표현하는 대표적인 형식이다.**
4. **Python은 Ollama API를 호출해 Qwen의 분석 결과를 받을 수 있다.**
5. **Marble AIOps에서는 Python이 Zabbix·ELK·Jenkins·Ollama를 연결하는 중심 역할을 한다.**

---

### 9. 복습 확인문제 및 정답

**Q1. API의 가장 중요한 역할은?**

정답: 서로 다른 프로그램이 기능과 데이터를 주고받도록 하는 것이다.

**Q2. Python에서 Ollama에 장애 분석을 요청할 때 사용하는 HTTP 메서드는?**

정답: POST

**Q3. JSON이란 무엇인가?**

정답: 프로그램 간에 데이터를 주고받을 때 사용하는 구조화된 데이터 표현 형식이다.

**Q4. Zabbix에서 장애 발생 시 Python 서버로 자동 이벤트를 전달하는 방식은?**

정답: Webhook

**Q5. Marble AIOps에서 Python이 수행하는 역할은?**

정답: 장애 정보를 수집하고 Ollama API에 AI 분석을 요청하며, 복구 자동화 및 검증을 연결하는 역할이다.

---

### 다음 교시

**3장 2교시 — AIOps의 개념과 Marble 프로젝트 적용 구조**

- AIOps란 무엇인가?
- 기존 모니터링과 AIOps의 차이
- AI 기반 장애 분석 원리
- 자동 복구 시스템의 동작 구조
- 자동 복구의 위험성과 안전장치
- Marble AIOps 최종 아키텍처


<img width="1055" height="1491" alt="image" src="https://github.com/user-attachments/assets/8d5957c1-65c1-45e1-b582-6417a15fffb3" />
