# 젤리 몰리 5축 코드 점검 스킬

## 스킬 정의

| 속성 | 값 |
|------|-----|
| 스킬명 | jelly-5axis-review |
| 역할 | 젤리 몰리 전용 5축 코드 점검 기준·절차·출력 포맷 정의 |
| 사용 에이전트 | jelly-code-reviewer |
| 참조 규칙 | `C:\DevelopRule\Rule\unity-csharp-rules\` |

> 참고: 상세 5축 기준은 `C:\Users\zkdlm\.claude\agents\jelly-team\SKILL.md` 참조.
> 본 파일은 해당 스킬의 규칙 참조 경로 및 절차를 표준화한 진입점이다.

---

## 점검 전 필수 절차 (생략 금지)

### 1단계 — 규칙 허브 로드

순서대로 Read:
1. `C:\DevelopRule\Rule\unity-csharp-rules\HUB.md`
2. 아래 핵심 규칙 파일 Read (5축 근거):

| 규칙 ID | 파일명 | 점검 축 |
|--------|--------|--------|
| UNITY-036 | UNITY-CLASS-036.md | 축 1: MonoBehaviour 남용 / POCO 전환 |
| UNITY-021 | UNITY-LIFECYCLE-021.md | 축 2: 라이프사이클 본문 비대화 |
| UNITY-041 | UNITY-FUNCTION-041.md | 축 2: 함수 단위 책임 분리 |
| UNITY-020 | UNITY-DI-020.md | 축 4: 자동 패씽 대체 |
| UNITY-022 | UNITY-VALIDATE-022.md | 축 4/5: 초기화 1회 검증·불변식 |
| UNITY-003 | UNITY-NULL-003.md | 축 5: null 처리 원칙 |
| UNITY-006 | UNITY-EXCEPT-006.md | 축 5: 명시적 실패 vs 조용한 early-return |

### 2단계 — 상세 기준 파일 로드

반드시 Read: `C:\Users\zkdlm\.claude\agents\jelly-team\SKILL.md`

5축 상세 점검 기준, 출력 포맷, 리뷰 프로세스를 로드한다.

### 3단계 — 점검 수행

1. 범위 확정 (사용자 지정 경로 → 없으면 `Assets/02_Scripts/` 전체 최근 변경 파일 우선)
2. Glob/Grep으로 후보 파일 나열 → Read로 본문 확인
3. 5축 체크리스트 적용
4. 심각도 분류: 필수(Must) / 권장(Should) / 선택(Could)

### 4단계 — 보고서 작성 및 문서화 위임

- 보고서 말미에 명시: `검증 규칙: [UNITY-003, UNITY-006, UNITY-020, UNITY-021, UNITY-022, UNITY-036, UNITY-041, ...]`
- 보고서 작성 직후 `unity-documenter`를 호출해 수정 가이드 문서로 저장
- 저장 경로: `docs/code-review/{YYYY-MM-DD}-jelly-molly-code-review.md`

---

## 5축 요약

| 축 | 점검 항목 | 핵심 규칙 |
|----|---------|---------|
| 1 | MonoBehaviour → POCO 전환/분리 후보 | UNITY-036, UCA-001 |
| 2 | 라이프사이클 본문 1줄 위임 위반 | UNITY-021, UNITY-041 |
| 3 | 불필요 static 멤버 | — |
| 4 | 의미 없는 자동 패씽 (AddComponent/GetComponent/Find 폴백) | UNITY-020 |
| 5 | 방어적·불필요 null 검사 | UNITY-003, UNITY-006, UNITY-022 |

---

## 에러 핸들링

| 상황 | 처리 |
|------|------|
| 규칙 파일 접근 불가 | 경로 재확인 후 1회 재시도. 실패 시 체크리스트만으로 진행, "규칙 미참조" 경고 |
| 대상 파일 과다 | 폴더 단위로 분할, 보고서를 파트로 분리 |
| unity-documenter 위임 실패 | 보고서만 반환하고 사용자에게 문서화 재시도 의사 확인 |

---

생성: 2026-05-01
