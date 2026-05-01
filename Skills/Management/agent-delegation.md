# 에이전트 위임·관리 스킬

## 스킬 정의

| 속성 | 값 |
|------|-----|
| 스킬명 | agent-delegation |
| 역할 | 에이전트 위임·분배·관리·보고 절차 정의 |
| 사용 에이전트 | CEO, CTO, dev-lead, code-review-lead |
| 참조 규칙 | `C:\DevelopRule\Rule\Always\Always.md` |

---

## 위임 전 필수 절차

### 1단계 — Always 규칙 확인

반드시 Read: `C:\DevelopRule\Rule\Always\Always.md`

핵심 확인 사항:
- 모든 작업은 CEO → CTO → 팀장 → 팀원 계층 경유
- 에이전트 배치도를 사용자에게 먼저 제시하고 승인 후 진행
- 사용한 에이전트 및 적용 규칙을 작업 완료 후 명시

### 2단계 — 명령 분석

명령을 다음 세 요소로 분해한다:
- WHO: 담당 에이전트 (전문 영역 기준)
- WHAT: 구체적 작업 내용 (산출물 명시)
- WHEN: 의존 관계 및 순서 (병렬/순차)

### 3단계 — 에이전트 선발

| 작업 유형 | 담당 에이전트 |
|----------|-------------|
| 시스템 설계 (JellyMolly) | jelly-architect |
| 시스템 설계 (Unity 일반) | unity-architect |
| Unity 구현 | unity-developer |
| 젤리 기능 | jelly-feature |
| 젤리 물리 | jelly-physics |
| 젤리 시스템 | jelly-system |
| 젤리 비주얼 | jelly-visual |
| 젤리 상태 | jelly-state |
| 젤리 입력 | jelly-input |
| 젤리 상호작용 | jelly-interaction |
| 젤리 최적화 | jelly-optimizer |
| Unity 최적화 | unity-optimizer |
| Unity 코드 리뷰 | unity-code-reviewer |
| 젤리 코드 리뷰 | jelly-code-reviewer |
| 총괄 기획 | unity-lead-planner |
| 세부 기획 | unity-detail-planner |
| UI/Inspector 설계 | unity-designer |
| 자료 조사 | researcher / unity-researcher |
| 문서화 | documenter / unity-documenter |
| 시각화 | visualizer |
| 파일 생성 | file-creator |
| 파일 구조 관리 | file-structure-manager |
| Git 커밋·푸시 | git-push |

### 4단계 — 배치도 제시

에이전트 배치 전 사용자에게 배치도를 제시한다.

```
[요청 내용]
  ↓
[에이전트 1] → 역할 및 산출물
  ↓
[에이전트 2] → 역할 및 산출물
  ↓ (병렬 가능 시)
[에이전트 3a] + [에이전트 3b]
  ↓
[최종 결과]
```

---

## 완료 보고 형식

작업 완료 후 반드시 다음 형식으로 보고:

```markdown
## 작업 완료

**작업**: {작업 설명}
**사용 에이전트**: {에이전트 이름 목록}
**결과물 위치**: {파일 경로}
**적용 규칙**: {규칙 ID 목록}
```

---

## 코드 점검 등급 기준 (code-review-lead 사용)

| 등급 | 의미 |
|------|------|
| S | 완벽. 모든 기준 충족 + 모범 사례 |
| A | 우수. 기준 충족, 소수 개선점 |
| B | 양호. 대부분 충족, 일부 개선 필요 |
| C | 보통. 기본 충족, 다수 개선 필요 |
| D | 미흡. 기준 미달, 수정 필요 |
| E | 불량. 다수 규칙 위반 |
| F | 실패. 전면 재작업 필요 |

---

## 에러 핸들링

| 상황 | 처리 |
|------|------|
| 하위 에이전트 실패 | 원인 분석 후 재위임 또는 대체 에이전트 선정 |
| 범위 초과 명령 | 상위 레이어로 반려 또는 분할 처리 |
| 복합 명령 | CTO + 인사계 동시 위임 |

---

생성: 2026-05-01
