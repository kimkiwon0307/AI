<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/af461c6b-b9a5-46ee-a137-1dcc72ddcf21" />



# 1장 1교시 - HTTP · REST API · JSON 기초

## 1. HTTP란?

HTTP(HyperText Transfer Protocol)는 클라이언트와 서버가 요청(Request)과 응답(Response)을 주고받는 통신 규칙이다.

- Client: 요청을 보내는 프로그램 (브라우저, Python 등)
- Server: 요청을 받아 처리하고 응답하는 프로그램
- HTTPS: HTTP 통신을 TLS로 암호화한 방식

### 동작 원리

Client → HTTP Request → Server → HTTP Response → Client

---

## 2. HTTP 메서드

| 메서드 | 역할 |
|---|---|
| GET | 데이터 조회 |
| POST | 데이터 제출 및 처리 요청 |
| PUT | 리소스 전체 교체 |
| PATCH | 리소스 일부 수정 |
| DELETE | 리소스 삭제 |

**핵심:** GET은 조회, POST는 데이터 전달에 주로 사용한다.

---

## 3. HTTP 상태 코드

| 상태 코드 | 의미 |
|---|---|
| 200 OK | 요청 성공 |
| 201 Created | 리소스 생성 성공 |
| 400 Bad Request | 잘못된 요청 |
| 401 Unauthorized | 인증 필요 또는 실패 |
| 403 Forbidden | 접근 권한 없음 |
| 404 Not Found | 요청 대상 없음 |
| 500 Internal Server Error | 서버 내부 오류 |
| 503 Service Unavailable | 서비스 이용 불가 |

### 상태 코드 분류

- 2xx: 요청 성공
- 4xx: 클라이언트 요청 관련 오류
- 5xx: 서버 측 오류

---

## 4. REST API란?

### API
프로그램끼리 기능이나 데이터를 주고받도록 제공하는 인터페이스이다.

### REST
리소스를 URI로 식별하고 HTTP 메서드 등을 활용하는 API 설계 방식이다.

### REST API 예시

| 요청 | 역할 |
|---|---|
| GET /servers | 서버 목록 조회 |
| GET /servers/1 | 특정 서버 조회 |
| POST /alerts | 장애 알림 전달 |
| PATCH /alerts/10 | 장애 처리 상태 일부 수정 |
| DELETE /alerts/10 | 장애 정보 삭제 |

**핵심:** HTTP는 통신 규칙이고, REST는 API 설계 방식이다.

---

## 5. JSON이란?

JSON(JavaScript Object Notation)은 시스템 간 데이터를 교환할 때 사용하는 텍스트 형식이다.

### JSON 예시

```json
{
  "server": "marble-was01",
  "status": "DOWN",
  "cpu": 95
}
```

- Key: 데이터 이름
- Value: 데이터 값
- `{ }`: 객체
- `[ ]`: 배열
- 문자열, 숫자, 불리언 등의 자료형 지원

**핵심:** AIOps에서는 서버명, 장애 상태, CPU 사용률 등을 JSON으로 전달할 수 있다.

---

## 6. curl 핵심 명령어

### GET 요청

```bash
curl -i "https://httpbin.org/get"
```

### POST 요청

```bash
curl -i -X POST \
  "https://httpbin.org/post" \
  -H "Content-Type: application/json" \
  -d '{"server":"marble-was01","status":"DOWN"}'
```

### 주요 옵션

| 옵션 | 설명 |
|---|---|
| -i | HTTP 응답 헤더 출력 |
| -X POST | POST 메서드 명시 |
| -H | HTTP 요청 헤더 지정 |
| -d | 요청 본문 데이터 전달 |
| --max-time 10 | 요청 전체 시간 제한 |

---

## 7. Python requests 핵심

Python에서 HTTP API를 호출할 때 requests 라이브러리를 사용한다.

```python
import requests

url = "https://httpbin.org/post"

payload = {
    "server": "marble-was01",
    "status": "DOWN"
}

response = requests.post(
    url,
    json=payload,
    timeout=10
)

response.raise_for_status()

print(response.json())
```

### 핵심 문법

| 코드 | 역할 |
|---|---|
| requests.get() | GET 요청 |
| requests.post() | POST 요청 |
| json=payload | JSON 데이터 전송 |
| timeout=10 | 요청 시간 제한 |
| raise_for_status() | HTTP 4xx·5xx 오류 감지 |
| response.json() | JSON 응답을 Python 객체로 변환 |

실제 자동화 코드에서는 try/except로 통신 오류를 처리한다.

---

## 8. AIOps 활용 구조

```text
[WEB / WAS / DB]
       |
       | 장애 발생
       v
    [Zabbix]
       |
       | HTTP POST + JSON
       v
    [FastAPI]
       |
       | 장애 정보 수신
       v
 [Python Script]
       |
       | 장애 분류 및 처리
       v
 [AI 분석 / 자동복구]
```

### 기술별 역할

- HTTP: 시스템 간 통신
- REST API: 데이터 조회 및 작업 요청
- JSON: 장애 데이터 표현 및 전달
- curl: HTTP API 테스트
- Python requests: API 호출 자동화
- FastAPI: HTTP 요청을 수신하는 API 서버 구현

---

## 9. 핵심 정리

1. HTTP는 클라이언트와 서버가 통신하는 규칙이다.
2. GET은 조회, POST는 데이터 제출에 주로 사용한다.
3. HTTP 상태 코드는 요청 처리 결과를 나타낸다.
4. REST는 HTTP의 특성을 활용하는 API 설계 방식이다.
5. JSON은 시스템 간 데이터를 전달하는 형식이다.
6. curl은 명령어로 HTTP 요청을 테스트하는 도구이다.
7. Python requests를 사용하면 API 호출을 자동화할 수 있다.
8. AIOps에서는 HTTP POST와 JSON을 활용해 장애 정보를 전달할 수 있다.
```
