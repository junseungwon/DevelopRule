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

## 에이전트 라우팅 (Agent Routing)

- 모든 작업 요청을 처리하기 전, 반드시 `C:\DevelopRule\agents\hub.md` 파일을 먼저 읽는다.
- 사용자가 에이전트 사용을 명시적으로 지시한 경우, 아주 단순한 기능이나 작업이라도 반드시 에이전트를 분배하여 처리한다.
- `hub.md`에 정의된 에이전트 목록과 라우팅 원칙을 분석하여, 현재 사용자 요청에 가장 적합한 에이전트를 선택하고 해당 에이전트의 역할로 작업을 수행한다.
- 어떤 에이전트를 사용해야 할지 판단하기 어렵거나 복합 작업인 경우, 허브(Hub)의 라우팅 흐름에 따라 작업 순서를 계획한 뒤 실행한다.

---

## 대화 종료 시 적용 규칙 기록

매 대화 마지막 응답 하단에 해당 대화에서 적용된 규칙 목록과 토큰 사용량을 아래 형식으로 작성한다.
파일명과 괄호 안에 한국어 설명을 함께 표기한다.

- 토큰 수는 대화 전체 맥락 기준 추정값이다.
- Claude Sonnet 4.6 컨텍스트 한도는 200,000 토큰이다.

```
---
적용 규칙: always.md (항상 적용 규칙), bash.md (Bash 도구 규칙), code.md (코드 작성 규칙), ...
토큰: 사용 ~X,XXX / 남은 ~XXX,XXX / 한도 200,000
```

---

## 에이전트 생성 시 필수 참조

에이전트를 생성할 때는 반드시 아래 하네스 규칙 파일을 먼저 읽는다.

- `C:\DevelopRule\plugins\harness\skills\harness\SKILL.md` — 에이전트 정의 워크플로우 및 필수 섹션
- `C:\DevelopRule\plugins\harness\skills\harness\references\agent-design-patterns.md` — 에이전트 구조 템플릿 및 팀 아키텍처 패턴
- `C:\DevelopRule\plugins\harness\skills\harness\references\skill-writing-guide.md` — description 작성 원칙

준수 사항:
- frontmatter에 `model: opus` 필수 (documenter 제외)
- `## 팀 통신 프로토콜` 섹션 필수
- 생성 후 `C:\DevelopRule\agents\hub.md` 업데이트 필수

---

## Claude Code 최적화 규칙 (54가지)

필요 시 아래 파일을 참조한다. (`@` 임포트 없이 경로만 기재 — 컨텍스트 절약)

- `C:\DevelopRule\rules\always\01_session-context.md` — 세션 및 컨텍스트 관리 (1–9)
- `C:\DevelopRule\rules\always\02_prompt-engineering.md` — 프롬프트 엔지니어링 (10–18)
- `C:\DevelopRule\rules\always\03_claudemd-optimization.md` — 파일 분산 및 CLAUDE.md 최적화 (19–27)
- `C:\DevelopRule\rules\always\04_tools-control.md` — 명령어 및 외부 도구 제어 (28–36)
- `C:\DevelopRule\rules\always\05_model-cost.md` — 모델 스위칭 및 비용 효율화 (37–45)
- `C:\DevelopRule\rules\always\06_workflow-refactoring.md` — 개발 워크플로우 및 리팩토링 (46–54)
