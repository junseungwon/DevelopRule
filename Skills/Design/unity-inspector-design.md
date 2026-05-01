# Unity Inspector·UI·Animator 설계 스킬

## 스킬 정의

| 속성 | 값 |
|------|-----|
| 스킬명 | unity-inspector-design |
| 역할 | Unity Inspector 구성·UI 레이아웃·Animator 파라미터·Prefab 구조 설계 절차 정의 |
| 사용 에이전트 | unity-designer |
| 참조 규칙 | `C:\DevelopRule\Rule\unity-csharp-rules\` |

---

## 설계 전 필수 절차 (생략 금지)

### 1단계 — 허브 로드

반드시 Read: `C:\DevelopRule\Rule\unity-csharp-rules\HUB.md`

### 2단계 — 설계 관련 핵심 규칙 Read

| 규칙 ID | 파일명 | 설계에서의 역할 |
|--------|--------|--------------|
| UNITY-007 | UNITY-SERIAL-007.md | SerializeField 사용 원칙 |
| UNITY-037 | UNITY-PROPERTY-037.md | 프로퍼티 노출 기준 |
| UNITY-029 | UNITY-ANNOTATION-029.md | Header·Tooltip·Range 활용 |
| UNITY-013 | UNITY-GAMEOBJ-013.md | GameObject 계층 구성 |
| UNITY-035 | UNITY-MARKER-035.md | Tag/Layer 활용 |

### 3단계 — 설계서 말미 명시

```
적용 규칙: [UNITY-007, UNITY-029, UNITY-037, UNITY-013, UNITY-035]
```

---

## 설계 원칙

### Inspector 구성 원칙 (UNITY-007, UNITY-029)
- Public 필드 금지. `[SerializeField] private` 조합 필수
- Header로 섹션 구분, Tooltip으로 각 필드 설명 필수
- Range로 수치 범위 제한
- 불필요한 Inspector 노출 금지

```csharp
[Header("이동 설정")]
[SerializeField, Range(0f, 20f), Tooltip("최대 이동 속도 (m/s)")]
private float _maxSpeed = 10f;
```

### UI 설계 원칙
- 해상도 독립적 설계. Anchor·Pivot·Canvas Scaler 기준
- 절대 픽셀 좌표 금지 → 앵커 기반 상대 좌표 사용

### Animator 설계 원칙
- 상태 수 최소화
- 파라미터는 명확한 네이밍 (Bool: is-, Float: speed-, Trigger: on-)
- 서브 스테이트 머신으로 복잡도 관리

---

## 설계 산출물 형식

### Inspector 설정표

| 필드명 | 타입 | 기본값 | Tooltip | 범위 |
|-------|------|--------|---------|------|
| _maxSpeed | float | 10f | 최대 이동 속도 (m/s) | 0~20 |

### Animator 파라미터표

| 파라미터명 | 타입 | 기본값 | 용도 |
|-----------|------|--------|------|
| Speed | Float | 0 | 이동 속도 기반 Blend |
| IsGrounded | Bool | true | 지면 접지 여부 |
| OnJump | Trigger | — | 점프 트리거 |

### Prefab 구조 트리

```
{Root GameObject}
  ├── Visual          — 렌더러·메시
  ├── Colliders       — 충돌체
  ├── Effects         — 파티클
  └── UI              — 월드 스페이스 UI
```

---

## 에러 핸들링

| 상황 | 처리 |
|------|------|
| UX 시나리오 부족 | detail-planner에 보완 요청 |
| 기술 제약 충돌 | architect와 협의 후 수정안 제시 |

---

생성: 2026-05-01
