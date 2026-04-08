# 변수 주석 규칙

## 적용 대상
| 대상 | 작성 방식 |
|---|---|
| public 변수 | /// <summary> 필수 |
| [SerializeField] 변수 | [Tooltip] 필수, XML 생략 |
| private 변수 | 생략 (복잡한 경우 // 인라인) |

## XML 변수 주석 예시
```csharp
/// <summary>플레이어의 현재 체력입니다.</summary>
public int health;

/// <summary>플레이어의 이동 속도입니다. (단위: m/s)</summary>
public float moveSpeed;
```

## Inspector 어트리뷰트 예시
```csharp
[Header("Movement Settings")]
[Tooltip("이동 속도 (단위: m/s)")]
[SerializeField] private float moveSpeed = 5f;

[Tooltip("달리기 속도 배율")]
[SerializeField] private float sprintMultiplier = 1.5f;
```

## 어트리뷰트 규칙
- 연관 변수 3개 이상 → [Header] 필수
- 물리량 → 단위 명시 필수 (m/s, mm, kg, 초, degree 등)
- 그룹 간 분리 필요 시에만 [Space] 사용

## 금지 패턴 목록 (실수 발생 시 누적 추가)
- public 변수에 <summary> 누락 → 금지
- [SerializeField]에 [Tooltip] 누락 → 금지
- [SerializeField] 변수에 XML과 [Tooltip] 중복 작성 → 금지
- 물리량 변수에 단위 미표기 → 금지
