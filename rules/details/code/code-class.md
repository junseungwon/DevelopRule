# 클래스 구조 규칙

## 클래스 내부 순서

```csharp
#region Variables        // 상수 → public → SerializeField → private 순
#region Unity Lifecycle  // Awake, Start, Update, FixedUpdate, LateUpdate 등
#region Public Methods
#region Private Methods
#region Coroutines
#region Event Handlers
```

## 접근 제한자 원칙

- 외부 노출 최소화: 가능하면 `private`, 필요시 `protected`, 불가피할 때만 `public`
- Unity Inspector 노출은 `public` 대신 `[SerializeField] private` 사용

## 네이밍 규칙

| 대상 | 규칙 | 예시 |
|---|---|---|
| 클래스 | PascalCase | `PlayerController` |
| 인터페이스 | `I` + PascalCase | `IDamageable` |
| Enum | `E` + PascalCase | `EGameState` |
| public 필드·프로퍼티 | PascalCase | `MoveSpeed` |
| private 필드 | `_` + camelCase | `_moveSpeed` |
| 메서드 | PascalCase | `TakeDamage()` |
| 매개변수·지역변수 | camelCase | `damageAmount` |

## ⛔ 금지 패턴 목록

- region 순서 임의 변경 → 금지
- Inspector 노출 목적으로 `public` 필드 사용 → 금지
- 인터페이스에 `I` 접두사 누락 → 금지
- Enum에 `E` 접두사 누락 → 금지
