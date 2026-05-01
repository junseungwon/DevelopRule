# 스킬 인덱스 — 에이전트별 스킬 매핑

> 모든 스킬은 `C:\DevelopRule\Rule\` 하위 규칙 파일들을 참조한다.
> 마지막 업데이트: 2026-05-01

---

## Dev (개발 팀)

| 스킬 파일 | 사용 에이전트 | 역할 | 참조 규칙 |
|---------|------------|------|---------|
| `Dev/unity-development/SKILL.md` | unity-developer, jelly-system, jelly-feature, jelly-physics, jelly-interaction, jelly-input, jelly-state, jelly-visual | Unity C# 코드 작성 절차 | unity-csharp-rules 전체 |
| `Dev/unity-code-writing.md` | (위 에이전트 공용 보조) | 작업 유형별 규칙 ID 매칭 빠른 참조 | unity-csharp-rules HUB |
| `Dev/unity-architecture-design.md` | unity-architect, jelly-architect | 아키텍처 설계 원칙·절차 | UNITY-030, 036, 039, 020, 016, 021, UCA-001, 004, 006 |
| `Design/unity-architecture/SKILL.md` | unity-architect | Unity 일반 아키텍처 설계 | unity-csharp-rules, Character |
| `Design/jelly-architecture/SKILL.md` | jelly-architect | JellyMolly 전용 설계 | Character/UCA-001, 004, 006 |
| `Dev/unity-code-review.md` | unity-code-reviewer, code-reviewer, rules-reviewer | 코드 리뷰 5축 체크리스트·절차 | unity-csharp-rules 전체 |
| `Dev/jelly-5axis-review.md` | jelly-code-reviewer | 젤리 몰리 전용 5축 점검 | UNITY-036, 021, 041, 020, 022, 003, 006 |
| `Dev/unity-optimization.md` | unity-optimizer, jelly-optimizer, optimization-reviewer | 성능 최적화 절차·체크리스트 | UNITY-023, 014, 034, 019, 011, 027, 021, 010, 031 |
| `Dev/unity-qa-testing.md` | unity-qa | Given-When-Then 테스트·버그 리포트 | UNITY-024, 022, 028 |

---

## Design (기획·디자인 팀)

| 스킬 파일 | 사용 에이전트 | 역할 | 참조 규칙 |
|---------|------------|------|---------|
| `Design/unity-planning.md` | unity-lead-planner, unity-detail-planner | 기획서 작성 형식·원칙 | Always.md |
| `Design/unity-inspector-design.md` | unity-designer | Inspector·UI·Animator·Prefab 설계 | UNITY-007, 037, 029, 013, 035 |

---

## Management (관리 팀)

| 스킬 파일 | 사용 에이전트 | 역할 | 참조 규칙 |
|---------|------------|------|---------|
| `Management/agent-delegation.md` | CEO, CTO, dev-lead, code-review-lead | 위임·배치도·완료 보고 절차 | Always.md |
| `Management/code-quality-grading.md` | code-review-lead, rules-reviewer, optimization-reviewer | S/A/B/C/D/E/F 등급 산출 | unity-csharp-rules, Character |
| `Management/system-design/SKILL.md` | CTO | 설계 선행 의사결정 프로세스 | unity-csharp-rules, Character |

---

## ETC (공용 에이전트)

| 스킬 파일 | 사용 에이전트 | 역할 | 참조 규칙 |
|---------|------------|------|---------|
| `ETC/documentation.md` | documenter, unity-documenter | 문서 작성 형식·저장 경로 기준 | Always.md |
| `ETC/research.md` | researcher, unity-researcher | 조사 보고서 형식·출처 기준 | Always.md |
| `ETC/visualization.md` | visualizer | HTML Mermaid 시각화 생성 절차 | Always.md |
| `ETC/feedback-logging.md` | feedback-logger | 일일/주간 피드백 기록 절차 | Always.md |
| `ETC/file-management.md` | file-creator, file-structure-manager, git-push | 파일 생성·구조 문서·Git 절차 | Always.md |

---

## 규칙 파일 경로 색인

| 규칙 범주 | 경로 |
|---------|------|
| 항상 적용 규칙 | `C:\DevelopRule\Rule\Always\Always.md` |
| Unity C# 규칙 허브 | `C:\DevelopRule\Rule\unity-csharp-rules\HUB.md` |
| Unity C# 규칙 개별 | `C:\DevelopRule\Rule\unity-csharp-rules\UNITY-<TOPIC>-<NNN>.md` |
| 캐릭터 아키텍처 허브 | `C:\DevelopRule\Rule\Unity\Character\HUB.md` |
| 캐릭터 아키텍처 개별 | `C:\DevelopRule\Rule\Unity\Character\UCA-00X_*.md` |
| 젤리 5축 점검 상세 | `C:\Users\zkdlm\.claude\agents\jelly-team\SKILL.md` |

---

## 에이전트 → 스킬 빠른 참조

| 에이전트 | 주 스킬 | 보조 스킬 |
|---------|--------|---------|
| CEO | agent-delegation | — |
| CTO | agent-delegation, system-design | — |
| dev-lead | agent-delegation | — |
| code-review-lead | agent-delegation, code-quality-grading | — |
| unity-architect | unity-architecture (SKILL.md), unity-architecture-design | — |
| jelly-architect | jelly-architecture (SKILL.md), unity-architecture-design | — |
| unity-developer | unity-development (SKILL.md), unity-code-writing | — |
| jelly-system | unity-development (SKILL.md), unity-code-writing | — |
| jelly-feature | unity-development (SKILL.md), unity-code-writing | — |
| jelly-physics | unity-development (SKILL.md), unity-code-writing | unity-optimization |
| jelly-visual | unity-development (SKILL.md), unity-code-writing | unity-optimization |
| jelly-state | unity-development (SKILL.md), unity-code-writing | — |
| jelly-input | unity-development (SKILL.md), unity-code-writing | — |
| jelly-interaction | unity-development (SKILL.md), unity-code-writing | — |
| unity-optimizer | unity-optimization | — |
| jelly-optimizer | unity-optimization | — |
| unity-code-reviewer | unity-code-review | — |
| jelly-code-reviewer | jelly-5axis-review, unity-code-review | — |
| rules-reviewer | unity-code-review, code-quality-grading | — |
| optimization-reviewer | unity-optimization, code-quality-grading | — |
| unity-qa | unity-qa-testing | — |
| unity-lead-planner | unity-planning | — |
| unity-detail-planner | unity-planning | — |
| unity-designer | unity-inspector-design | — |
| unity-researcher | research | — |
| researcher | research | — |
| documenter | documentation | — |
| unity-documenter | documentation | — |
| visualizer | visualization | — |
| feedback-logger | feedback-logging | — |
| file-creator | file-management | — |
| file-structure-manager | file-management | — |
| git-push | file-management | — |
