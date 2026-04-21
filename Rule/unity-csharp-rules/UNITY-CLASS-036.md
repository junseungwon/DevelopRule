---
name: Class 제작
description: POCO 우선, MonoBehaviour 최소화, 조합 선호
category: 규칙
priority: high
---

# UNITY-CLASS-036: Class 제작

## 규칙 정의

도메인 로직은 **POCO(순수 C# 클래스)**. MonoBehaviour는 Unity API/메시지 연결에만 사용. 상속보다 조합. 기본 sealed.

## 좋은 예시 ✓

```csharp
// POCO - 도메인 로직
public class Health
{
    public int Current { get; private set; }
    public event Action OnDeath;
    public Health(int max) => Current = max;
    public void TakeDamage(int amount)
    {
        Current = Mathf.Max(0, Current - amount);
        if (Current == 0) OnDeath?.Invoke();
    }
}

// 얇은 MonoBehaviour - Unity 연결
public sealed class PlayerHealthView : MonoBehaviour
{
    [SerializeField] private int _maxHealth = 100;
    private Health _health;
    void Awake() => _health = new Health(_maxHealth);
    public void TakeDamage(int n) => _health.TakeDamage(n);
}
```

## 나쁜 예시 ✗

```csharp
// 로직을 MonoBehaviour에 직접 구현
public class HealthMono : MonoBehaviour
{
    private int _current = 100;
    public void TakeDamage(int n) => _current -= n;
}

// Unity API 없는 MonoBehaviour
public class Calculator : MonoBehaviour
{
    public int Add(int a, int b) => a + b;
}
```

## 자동 탐지

```
FOR EACH MonoBehaviour class
  IF Unity API/메시지 미사용
  THEN VIOLATION (POCO 권장)
  IF 도메인 로직 직접 구현
  THEN WARNING (POCO 분리)
```

## 예외

- Unity 메시지/GameObject 접근 필수 시
- ScriptableObject
- 프레임워크 베이스 클래스
