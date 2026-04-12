# 항상 읽어야 하는 규칙

## 자동 승인

대화 시작 시 사용자에게 자동 승인 여부를 묻는다.

사용자가 자동 승인을 요청한 경우:
- Claude Code를 `--dangerously-skip-permissions` 플래그로 재실행해야 함을 안내한다.
- 재실행 전까지는 기존 allow 창이 계속 나타남을 알린다.

```bash
claude --dangerously-skip-permissions
```

---

## 기본 규칙

- 파일 수정 전 반드시 Read로 먼저 읽는다.
- 요청 범위를 벗어난 추가 변경은 하지 않는다.
- 응답은 간결하게 유지한다.
- 이모지는 사용자가 명시적으로 요청한 경우에만 사용한다.

---

## 대화 종료 시 적용 규칙 기록

매 대화 마지막 응답 하단에 해당 대화에서 적용된 규칙 목록을 아래 형식으로 작성한다.
파일명과 괄호 안에 한국어 설명을 함께 표기한다.

```
---
적용 규칙: always.md (항상 적용 규칙), bash.md (Bash 도구 규칙), code.md (코드 작성 규칙), ...
```

---

## Claude Code 최적화 규칙 (54가지)

필요 시 아래 파일을 참조한다. (`@` 임포트 없이 경로만 기재 — 컨텍스트 절약)

- `C:\DevelopRule\rules\always\01_session-context.md` — 세션 및 컨텍스트 관리 (1–9)
- `C:\DevelopRule\rules\always\02_prompt-engineering.md` — 프롬프트 엔지니어링 (10–18)
- `C:\DevelopRule\rules\always\03_claudemd-optimization.md` — 파일 분산 및 CLAUDE.md 최적화 (19–27)
- `C:\DevelopRule\rules\always\04_tools-control.md` — 명령어 및 외부 도구 제어 (28–36)
- `C:\DevelopRule\rules\always\05_model-cost.md` — 모델 스위칭 및 비용 효율화 (37–45)
- `C:\DevelopRule\rules\always\06_workflow-refactoring.md` — 개발 워크플로우 및 리팩토링 (46–54)
