# Plan Orchestration 스킬

## 스킬 정의

| 속성 | 값 |
|------|-----|
| 스킬명 | plan-orchestration |
| 역할 | plan-team 내 에이전트 위임·조율·파이프라인 실행 절차 정의 |
| 사용 에이전트 | plan-hub |
| 참조 규칙 | `C:\DevelopRule\Rule\Always\Always.md` |

---

## plan-team 파이프라인

### 신규 기획 파이프라인

```
CPO (요청 수신)
  ↓
plan-hub (분석 + 배치도 제시)
  ↓
plan-researcher (레퍼런스 수집 + 시장 조사)
  ↓
plan-designer (GDD / 컨셉 설계)  ← researcher 결과 활용
  ↓
plan-spec-writer (기능 스펙 문서화)  ← designer 결과 기반
  ↓
plan-visualizer (다이어그램 + HTML 시각화)  ← spec 결과 시각화
  ↓
CPO 보고
```

### 부분 기획 파이프라인 (단계별 선택 실행 가능)

| 요청 유형 | 실행 에이전트 |
|----------|-------------|
| 레퍼런스만 조사 | plan-researcher 단독 |
| GDD 작성 | plan-researcher → plan-designer |
| 스펙만 작성 | plan-spec-writer 단독 (기존 GDD 입력) |
| 시각화만 | plan-visualizer 단독 (기존 문서 입력) |
| 전체 파이프라인 | 위 순서 전체 |

---

## 위임 절차

### 1단계 — 요청 분석

CPO 또는 사용자 요청을 다음으로 분해:
- **WHO**: 담당 에이전트 (plan-team 멤버)
- **WHAT**: 구체적 작업 내용 (산출물 명시)
- **WHEN**: 의존 관계 및 순서 (병렬/순차)

### 2단계 — 배치도 제시

```
[요청 내용]
  ↓
[plan-researcher] → 레퍼런스 문서
  ↓
[plan-designer] → GDD 초안
  ↓
[plan-spec-writer] → 기능 스펙
  ↓
[plan-visualizer] → HTML 다이어그램
```

### 3단계 — 순차 위임 실행

- 각 에이전트 위임 시 이전 단계 산출물 경로와 핵심 컨텍스트 명시
- 직접 기획/설계/집필 금지 — 위임만 수행

### 4단계 — 통합 보고

CPO에게 보고 형식:
```markdown
## plan-team 작업 완료

**요청**: {내용}
**실행 파이프라인**: researcher → designer → spec-writer → visualizer
**산출물**:
- 레퍼런스 조사: `docs/research/{파일명}`
- GDD: `docs/design/{파일명}`
- 스펙: `docs/design/{파일명}`
- 시각화: `docs/visualization/{파일명}`
```

---

## 에러 핸들링

| 상황 | 처리 |
|------|------|
| 하위 에이전트 실패 | 원인 분석 후 재위임 또는 단계 건너뜀 |
| 기획 범위 초과 | CPO에게 범위 확인 요청 |
| 기술 구현 가능 여부 불명 | CTO에게 기술 검토 의뢰 요청을 CPO에 보고 |

---

생성: 2026-05-01
