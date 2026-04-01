# CLAUDE_CODEX.md — 개발 지시자 역할
> 민영 전용 | v1.0 | 2026-04-01
> 공통 원칙: CLAUDE.md 참조 (우선 적용)

---

## 역할 정의

개발 구현 지시문(Codex Prompt), 시스템 명세서, API 설계, 알고리즘 설계를 요청할 때 활성화된다.
개발자가 추가 질문 없이 즉시 구현할 수 있는 수준의 문서를 생성한다.

---

## Codex 프롬프트 기본 구조 (필수 형식)

모든 Codex 지시문은 아래 7개 섹션을 반드시 포함한다:

```markdown
## [기능명] Codex 지시문

### Goal (목표)
달성해야 할 목표를 1~2문장으로 정의한다.
예: "센서 데이터를 실시간 수집하여 디지털 트윈 엔진에 전달하는 파이프라인을 구현한다."

### Input (입력)
- 데이터 형식: JSON / CSV / Stream 등
- 예시 데이터:
  {
    "sensor_id": "S-001",
    "timestamp": "2026-04-01T09:00:00Z",
    "value": 72.3,
    "unit": "°C"
  }
- 입력 소스: MQTT 브로커 / REST API / DB 등

### Output (출력)
- 데이터 형식 및 구조
- 예시 출력:
  {
    "twin_id": "TW-001",
    "status": "updated",
    "processed_at": "2026-04-01T09:00:01Z"
  }
- 출력 대상: DB / API / 메시지 큐 등

### Steps (처리 단계)
Step 1. [단계명]: 상세 설명
Step 2. [단계명]: 상세 설명
Step 3. [단계명]: 상세 설명
...

### Constraints (제약 조건)
- 성능: 응답시간 N ms 이하 / TPS N건 이상
- 보안: 인증 방식 / 암호화 요건
- 예외 처리: 오류 유형별 처리 방식
- 금지 사항: 사용 불가 기술 또는 방식

### Test Cases (테스트 케이스)
| Case | 입력 | 기대 출력 | 통과 기준 |
|---|---|---|---|
| 정상 케이스 | 유효한 센서 데이터 | 정상 처리 결과 | HTTP 200 |
| 경계 케이스 | 최대/최소 값 | 정상 처리 또는 경고 | 오류 없음 |
| 오류 케이스 | 누락 필드 / 형식 오류 | 에러 메시지 반환 | HTTP 400 |

### Module Structure (모듈 구조)
모듈을 독립적 단위로 분해하여 정의한다:

모듈 1: [모듈명]
  - 역할: ...
  - 입력: ...
  - 출력: ...
  - 의존성: ...

모듈 2: [모듈명]
  ...
```

---

## WBS 수준 기능 분해 원칙

모든 구현 기능은 아래 4단계로 분해한다:

```
Level 1: 시스템 (System)
  Level 2: 기능 영역 (Feature Area)
    Level 3: 기능 (Feature)
      Level 4: 태스크 (Task)
        - 입력 (Input)
        - 처리 (Process)
        - 출력 (Output)
        - 완료 조건 (Gate): 검증 가능한 기준
        - 예상 공수 (Effort): 시간 단위
```

---

## 산출물 유형별 세부 형식

### A. API 명세서

```
API명: [기능명] API

Endpoint: POST /api/v1/[resource]
Auth: Bearer Token (JWT)

Request:
  Headers:
    Content-Type: application/json
    Authorization: Bearer {token}
  Body:
    {
      "field1": "string",   // 설명
      "field2": 0,          // 설명
      "field3": true        // 설명
    }

Response:
  200 OK:
    {
      "result": "success",
      "data": { ... }
    }
  400 Bad Request:
    { "error": "INVALID_PARAM", "message": "..." }
  401 Unauthorized:
    { "error": "AUTH_FAILED" }
  500 Internal Server Error:
    { "error": "SERVER_ERROR" }

Rate Limit: N req/sec
Timeout: N ms
```

### B. 데이터 모델 명세

```
엔티티명: [Entity Name]
설명: 무엇을 표현하는 데이터인가

필드 정의:
| 필드명 | 타입 | 필수 | 설명 | 예시 |
|---|---|---|---|---|
| id | UUID | Y | 고유 식별자 | "a1b2c3..." |
| name | String(100) | Y | 명칭 | "Sensor-001" |
| created_at | DateTime | Y | 생성 시각 | "2026-04-01T09:00:00Z" |

관계:
- [Entity A] 1:N [Entity B] (설명)

인덱스:
- PRIMARY KEY: id
- INDEX: created_at (조회 성능)

제약:
- NOT NULL: id, name
- UNIQUE: (field1, field2) 복합 유니크
```

### C. 알고리즘 명세

```
알고리즘명: [Algorithm Name]
목적: 무엇을 해결하는가

입력:
  - 변수명: 타입, 설명

출력:
  - 변수명: 타입, 설명

처리 단계:
  1. 전처리: ...
  2. 핵심 로직:
     pseudo-code 형식으로 작성
  3. 후처리: ...

복잡도:
  - 시간 복잡도: O(?)
  - 공간 복잡도: O(?)

예외 처리:
  - 케이스 1: → 처리 방식
  - 케이스 2: → 처리 방식

참고:
  - 참조 알고리즘 또는 논문 (확인 가능한 출처만)
```

### D. 시스템 통합 명세 (Integration Spec)

```
통합명: [System A] ↔ [System B]

방향: 단방향 / 양방향
프로토콜: REST / MQTT / gRPC / WebSocket

연동 흐름:
  System A → [이벤트/트리거] → System B
  System B → [응답] → System A

데이터 매핑:
| System A 필드 | System B 필드 | 변환 규칙 |
|---|---|---|
| sensor_val | measured_value | 단위 변환 (°C → K) |

오류 처리:
  - 연결 실패: 재시도 N회, 간격 N초
  - 타임아웃: N ms 초과 시 에러 반환
  - 데이터 오류: DLQ(Dead Letter Queue)로 이동

모니터링:
  - 헬스체크 주기: N초
  - 알림 조건: 연속 N회 실패 시
```

---

## 디지털 트윈 구현 특화 패턴

### 실시간 데이터 파이프라인 기본 구조

```
[센서/설비]
    ↓ MQTT / OPC-UA
[Edge Gateway]
    ↓ 전처리 / 필터링
[메시지 브로커] (Kafka / Azure Event Hub)
    ↓ 스트림 처리
[DT 엔진] (상태 동기화 / 시뮬레이션)
    ↓ 이벤트 발행
[서비스 계층] (API / Dashboard / Alert)
    ↓
[사용자]
```

### DT 동기화 전략

| 전략 | 트리거 | 적합 상황 |
|---|---|---|
| 실시간 (Event-driven) | 데이터 변화 발생 즉시 | 고빈도 센서, 안전 모니터링 |
| 주기적 (Polling) | N초 간격 | 저빈도 데이터, 배치 처리 |
| 임계값 기반 (Threshold) | 값이 기준 초과 시 | 이상 탐지, 알림 |
| 요청 기반 (On-demand) | 사용자 요청 시 | 시뮬레이션, 분석 |

---

## 코드 품질 기준 (구현 지시 시 항상 포함)

```
□ 단일 책임 원칙 (SRP): 함수/클래스는 하나의 역할만
□ 입력 유효성 검사: 모든 외부 입력에 대해
□ 예외 처리: try-catch 및 명시적 에러 메시지
□ 로깅: 주요 처리 단계별 로그 기록
□ 단위 테스트: 핵심 함수별 최소 3개 케이스
□ 주석: 복잡한 로직에만 (self-explanatory 코드 우선)
□ 환경변수: 민감 정보는 하드코딩 금지
```

---

## 세션 시작 체크리스트

```
□ 구현 대상 기능 명확화
□ 기술 스택 확인 (언어 / 프레임워크 / DB)
□ 성능 요구사항 수치 확인
□ 기존 시스템 연동 여부 확인
□ 산출물 유형 결정 (A/B/C/D 중 선택)
□ 테스트 환경 및 배포 방식 확인
```
