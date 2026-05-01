# 코드 품질 등급 평가 스킬

## 스킬 정의

| 속성 | 값 |
|------|-----|
| 스킬명 | code-quality-grading |
| 역할 | 코드 규칙 준수 및 최적화 영역 S/A/B/C/D/E/F 등급 산출 절차 정의 |
| 사용 에이전트 | code-review-lead, rules-reviewer, optimization-reviewer |
| 참조 규칙 | `C:\DevelopRule\Rule\unity-csharp-rules\`, `C:\DevelopRule\Rule\Unity\Character\` |

---

## 점검 전 필수 절차

### 1단계 — 규칙 로드

반드시 Read: `C:\DevelopRule\Rule\unity-csharp-rules\HUB.md`

### 2단계 — 점검 영역별 규칙 Read

#### 코드 규칙 점검 (rules-reviewer)

| 점검 항목 | 참조 규칙 |
|---------|---------|
| 네이밍 컨벤션 | UNITY-NAMING-005, UNITY-VARIABLE-040, UNITY-FUNCTION-041 |
| MonoBehaviour 남용 | UNITY-CLASS-036, UNITY-LIFECYCLE-021 |
| 의존성 방향 | UNITY-DI-020, UNITY-SOLID-030 |
| null 처리 | UNITY-NULL-003, UNITY-EXCEPT-006 |
| static 남용 | UNITY-SINGLETON-016 |
| 주석·문서화 | UNITY-COMMENT-008, UNITY-ANNOTATION-029 |
| 자동 패씽 | UNITY-DI-020, UNITY-VALIDATE-022 |
| Feature 경계 | UCA-004, UCA-006 |

#### 최적화 점검 (optimization-reviewer)

| 점검 항목 | 참조 규칙 |
|---------|---------|
| Update 병목 | UNITY-LIFECYCLE-021 |
| GC Allocation | UNITY-MEMORY-014, UNITY-LINQ-011, UNITY-COLLECTION-019 |
| 드로우콜·배칭 | UNITY-PROFILE-023 |
| 메모리 관리 | UNITY-POOL-034, UNITY-MEMORY-014 |
| 비동기 처리 | UNITY-ASYNC-001, UNITY-TASK-031 |
| 캐싱 패턴 | UNITY-GAMEOBJ-013 |
| Reflection | UNITY-REFLECT-027 |

---

## 등급 산출 기준

### 등급 정의

| 등급 | 설명 | 필수 위반 | 권장 위반 |
|------|------|---------|---------|
| S | 완벽. 모범 사례 포함 | 0건 | 0건 |
| A | 우수. 소수 개선점 | 0건 | 1~2건 |
| B | 양호. 일부 개선 필요 | 0건 | 3~5건 |
| C | 보통. 다수 개선 필요 | 1~2건 | 다수 |
| D | 미흡. 수정 필요 | 3~5건 | — |
| E | 불량. 다수 규칙 위반 | 6~10건 | — |
| F | 실패. 전면 재작업 필요 | 11건+ | — |

### 가중치

다음 위반은 심각도를 한 단계 올린다:
- AddComponent/FindObjectOfType 폴백 사용
- 상태를 가진 public static 필드
- 메모리 누수 패턴 (이벤트 구독 미해제)
- 라이프사이클 본문 직접 로직 다수 포함

---

## 점검 보고서 형식

### 코드 규칙 점검 결과

```markdown
## 코드 규칙 점검 결과

### 등급: {S/A/B/C/D/E/F}

### 위반 항목
| 심각도 | 규칙 | 위치 | 내용 |
|-------|------|------|------|
| 필수 | UNITY-036 | Player.cs:42 | MB에 도메인 로직 혼재 |

### 개선 권고
- {구체적 수정 방법}

검증 규칙: [UNITY-003, UNITY-036, ...]
```

### 최적화 점검 결과

```markdown
## 최적화 점검 결과

### 등급: {S/A/B/C/D/E/F}

### 성능 이슈
| 심각도 | 위치 | 영향도 | 내용 |
|-------|------|--------|------|
| 필수 | Movement.cs:88 | GC 고빈도 | LINQ in Update |

### 개선 권고
- {구체적 최적화 방법}

적용 규칙: [UNITY-023, UNITY-014, ...]
```

---

## 에러 핸들링

| 상황 | 처리 |
|------|------|
| 규칙 파일 접근 불가 | 경로 재확인 후 1회 재시도 |
| 점검 범위 과다 | 파일 단위로 분할하여 순차 점검 |

---

생성: 2026-05-01
