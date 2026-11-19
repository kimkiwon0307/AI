
# Ollama + Qwen 프로젝트식 학습

## 1장 1교시 — LLM·Ollama·Qwen 기초 및 Ubuntu 설치

### 1. 핵심 개념

| 기술 | 설명 |
|---|---|
| LLM | 대규모 언어 모델. 입력된 문맥을 바탕으로 텍스트를 생성하는 AI |
| Ollama | 로컬 서버에서 AI 모델을 다운로드·실행하고 API를 제공하는 프로그램 |
| Qwen | Ollama에서 실행할 수 있는 AI 언어 모델 |
| Token | AI가 텍스트를 처리하는 기본 단위 |
| Inference(추론) | 학습된 모델에 입력을 전달하여 답변을 생성하는 과정 |
| AIOps | AI 기술을 활용하여 IT 운영·장애 분석·자동화를 지원하는 방식 |

### 2. Ollama와 Qwen의 관계

```text
사용자 / Python
      ↓ 질문·장애 로그
Ollama (AI 실행 환경 / API 서버)
      ↓ 모델 실행
Qwen (LLM)
      ↓ 분석 결과 생성
Ollama
      ↓ 응답 반환
사용자 / Python
```

- Ollama는 AI 모델을 실행하는 도구이다.
- Qwen은 실제 분석과 답변을 생성하는 AI 모델이다.
- Ollama 기본 로컬 API 주소: `http://127.0.0.1:11434`

### 3. LLM 동작 원리

1. 입력: 질문 및 장애 로그 전달
2. 토큰화: 텍스트를 토큰 단위로 변환
3. 추론: 학습된 모델이 입력 문맥을 처리
4. 생성: 다음 토큰을 반복적으로 예측
5. 출력: 분석 결과를 텍스트로 반환

**주의:** LLM은 로그만으로 실제 장애 원인을 확정할 수 없다. AI 분석 결과는 Linux 명령어, 로그 및 모니터링 정보로 검증해야 한다.

### 4. Ubuntu 24.04 실습

#### 4-1. 서버 환경 확인

```bash
nproc         # CPU 논리 프로세서 개수
free -h       # RAM 사용량
df -h /       # 디스크 여유 공간
uname -m      # CPU 아키텍처
```

#### 4-2. Ollama 설치

```bash
sudo apt update
sudo apt install -y curl ca-certificates

curl -fsSLo /tmp/ollama-install.sh https://ollama.com/install.sh
less /tmp/ollama-install.sh
sh /tmp/ollama-install.sh
```

- `curl -f`: HTTP 오류 시 실패 처리
- `-sS`: 진행 표시 숨김, 오류 메시지 표시
- `-L`: 리다이렉션 따라가기
- `-o`: 저장 파일 지정

#### 4-3. Ollama 서비스 확인

```bash
ollama --version
sudo systemctl status ollama --no-pager
sudo ss -lntp | grep 11434
curl -s http://127.0.0.1:11434/api/tags
```

정상 기준:
- Ollama 버전이 표시됨
- 서비스 상태가 `active (running)`
- 기본 로컬 포트 `127.0.0.1:11434` 확인
- API가 JSON 형태로 응답

#### 4-4. Qwen 모델 설치 및 실행

```bash
ollama pull qwen3:1.7b
ollama list
ollama show qwen3:1.7b
ollama run qwen3:1.7b
```

- `pull`: AI 모델 다운로드
- `list`: 다운로드된 모델 목록
- `show`: 모델 상세 정보
- `run`: 모델 실행 및 질문 입력
- `/bye`: 대화형 실행 종료
- `ps`: 메모리에 로딩된 모델 확인

### 5. AIOps 장애 로그 분석 예시

입력 로그:

```text
ERROR java.sql.SQLNonTransientConnectionException:
Could not create connection to database server
```

AI에 요청할 내용:
1. 장애 증상 설명
2. 가능한 원인 3가지 제시
3. Linux 점검 명령어 제시
4. 로그만으로 확정할 수 없는 사항 구분

예상 원인 후보:
- MySQL 서비스 중단
- 네트워크 또는 방화벽 문제
- DB 연결 설정 오류

검증 명령어 예시:

```bash
sudo systemctl status mysql
sudo ss -lntp | grep 3306
nc -vz DB_IP 3306
```

※ MySQL 서비스/포트 명령은 DB 서버에서, `nc`는 애플리케이션 서버에서 실행한다. `DB_IP`는 실제 DB 주소로 대체한다.

### 6. 운영 검증

```bash
systemctl is-active ollama
ollama list
ollama ps
sudo ss -lntp | grep 11434
sudo journalctl -u ollama -n 30 --no-pager
```

**`ollama list`와 `ollama ps` 차이**
- `list`: 디스크에 저장된 모델
- `ps`: 현재 메모리에 로딩된 모델

### 7. 자주 발생하는 장애 및 대응

| 장애 | 확인 방법 |
|---|---|
| ollama: command not found | `command -v ollama` 및 설치 확인 |
| 서비스 실행 실패 | `systemctl status ollama`, `journalctl -u ollama` |
| 모델 다운로드 실패 | `df -h /`, `getent hosts ollama.com` |
| 모델 실행이 느림 | `free -h`, `top`, `ollama ps` |
| 메모리 부족 | 작은 모델 `qwen3:0.6b` 사용 검토 |
| AI의 잘못된 답변 | 로그·시스템 상태·모니터링 데이터로 재검증 |

### 8. 핵심 정리

- LLM은 입력 문맥을 기반으로 답변을 생성하는 AI 모델이다.
- Ollama는 AI 모델을 실행하고 로컬 API를 제공한다.
- Qwen은 장애 로그를 분석하는 데 활용할 수 있는 언어 모델이다.
- Ollama의 기본 로컬 API 포트는 11434이다.
- Ollama가 설치되어 있어도 Qwen 모델은 별도로 다운로드해야 한다.
- 모델 다운로드 여부와 메모리 로딩 여부는 서로 다르다.
- AI가 제안한 장애 원인은 확정 진단이 아니며 반드시 검증해야 한다.
- 향후 Python에서 Ollama API를 호출하여 AIOps 자동화로 확장한다.

### 9. 학습 목표

- [ ] LLM·Ollama·Qwen의 차이를 설명할 수 있다.
- [ ] Ubuntu에 Ollama를 직접 설치할 수 있다.
- [ ] Qwen 모델을 다운로드하고 실행할 수 있다.
- [ ] 서비스·포트·모델 상태를 확인할 수 있다.
- [ ] 장애 로그를 입력하여 원인 후보와 점검 방법을 받을 수 있다.
- [ ] AI의 분석 결과를 검증해야 하는 이유를 설명할 수 있다.
