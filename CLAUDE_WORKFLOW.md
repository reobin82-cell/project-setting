# CLAUDE_WORKFLOW.md — 버전 관리 운영 워크플로우
> 민영 전용 | v1.0 | 2026-04-01

---

## 파일 수정 후 커밋 절차

```bash
# 파일 수정 후
git add .
git commit -m "feat: CLAUDE_STRATEGY.md 디지털트윈 수익모델 추가"
git push
```

---

## 커밋 메시지 규칙

| 접두사 | 용도 | 예시 |
|---|---|---|
| `init:` | 최초 생성 | `init: 5개 파일 초기 세팅` |
| `feat:` | 내용 추가 | `feat: 논문 SLR 방법론 추가` |
| `fix:` | 오류 수정 | `fix: 키워드 오판 예외 규칙 수정` |
| `update:` | 기존 내용 갱신 | `update: 기술스택 2026 버전 반영` |

---

## 핵심 운영 원칙

```
수정은 claude-config\ 에서
    ↓
테스트 후 홈 디렉터리로 복사
    ↓
git commit으로 버전 기록
```
