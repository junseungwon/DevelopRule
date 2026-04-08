# 코드 패턴 규칙

## GetComponent
- `Awake()`에서만 호출 후 필드 캐싱, 매 프레임 호출 금지

## 코루틴 관리
```csharp
if (_fadeCoroutine != null) StopCoroutine(_fadeCoroutine);
_fadeCoroutine = StartCoroutine(FadeOutCoroutine());
```

## null 체크
- Unity 오브젝트: `== null` 사용 / 일반 C#: `?.` 또는 `is null`
- Unity 오브젝트에 `is null` 금지

## 매직 넘버
- 숫자 리터럴 직접 사용 금지 → 상수 또는 `[SerializeField]`로 추출

## 싱글톤
```csharp
private void Awake() {
    if (Instance != null && Instance != this) { Destroy(gameObject); return; }
    Instance = this;
    DontDestroyOnLoad(gameObject);
}
```

## 이벤트
- 호출 시 `?.Invoke()` 필수 / 구독 해제는 `OnDestroy`에서 처리

## ⛔ 금지 패턴 목록
- Update()에서 GetComponent 호출 → 금지
- 코루틴 중복 방어 없이 StartCoroutine 호출 → 금지
- 매직 넘버 직접 사용 → 금지
- Unity 오브젝트에 is null 사용 → 금지
- 이벤트 호출 시 ?.Invoke() 미사용 → 금지
