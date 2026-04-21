---
name: 이벤트/델리게이트
description: 이벤트 구독 해제, 메모리 누수 방지, 안전한 사용
category: 규칙
priority: high
---

# UNITY-EVENT-017: 이벤트/델리게이트

## 규칙 정의

이벤트는 **OnEnable에서 구독하고 OnDisable에서 해제**한다. `?.Invoke()` null-조건부 연산자로 안전 호출. 익명 메서드/람다 캡처는 해제 어려움.

## 필요성

1. **메모리 누수 방지**: 구독자 해제 실패 시 GC 방해
2. **Null 안전**: 구독자 없을 때 예외 방지
3. **생명주기 일치**: 객체 파괴 후 이벤트 호출 방지

## 좋은 예시 ✓

```csharp
public class Player : MonoBehaviour
{
    public event Action<int> OnHealthChanged;
    
    // ✓ 안전한 이벤트 발생
    public void TakeDamage(int amount)
    {
        _health -= amount;
        OnHealthChanged?.Invoke(_health);  // null-safe
    }
}

public class HealthUI : MonoBehaviour
{
    [SerializeField] private Player _player;
    
    // ✓ OnEnable 구독
    void OnEnable()
    {
        _player.OnHealthChanged += UpdateHealthBar;
    }
    
    // ✓ OnDisable 해제
    void OnDisable()
    {
        _player.OnHealthChanged -= UpdateHealthBar;
    }
    
    private void UpdateHealthBar(int health) { /* ... */ }
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 구독만 하고 해제 안함
void Start()
{
    _player.OnHealthChanged += UpdateHealthBar;
    // OnDisable/OnDestroy 해제 없음 -> 메모리 누수
}

// ✗ Bad: null 체크 없이 호출
public void TakeDamage(int amount)
{
    OnHealthChanged.Invoke(_health);  // 구독자 없으면 예외
}

// ✗ Bad: 익명 람다 구독 (해제 불가)
void Start()
{
    _player.OnHealthChanged += (h) => UpdateUI(h);
    // 참조를 저장하지 않아 해제 불가능
}
```

## 자동 탐지

```
FOR EACH event subscription (+=)
  IF 대응하는 해제(-=)가 없음
  THEN VIOLATION
  
FOR EACH event invocation
  IF NOT using ?.Invoke() 또는 null check
  THEN VIOLATION
  
FOR EACH anonymous lambda subscription
  IF 참조 저장 없이 +=
  THEN WARNING
```

## 허용 예외

- 스태틱 이벤트 (수명 = 어플리케이션)
- WeakEventPattern 사용 시
- 일회성 구독 (self-unsubscribing)
