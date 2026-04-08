# #region 작성 규칙

## 사용 기준
- MonoBehaviour 클래스 레벨에서만 사용
- 코드가 없는 빈 region 작성 금지
- 메서드 내부 사용 금지

## 표준 순서 및 네이밍 (순서·이름 고정)
```csharp
#region Variables        // 상수 → public → SerializeField → private 순
#region Unity Lifecycle  // Awake, Start, Update, FixedUpdate, LateUpdate 등
#region Public Methods
#region Private Methods
#region Coroutines
#region Event Handlers
```

## 금지 패턴 목록 (실수 발생 시 누적 추가)
- 메서드 내부 #region 사용 → 금지
- 표준 목록 외 임의 region 이름 사용 → 금지
- 빈 #region ~ #endregion 작성 → 금지
