---
name: 의존성 주입
description: 느슨한 결합, 테스트 가능성, 유연한 아키텍처
category: 규칙
priority: high
---

# UNITY-DI-020: 의존성 주입

## 규칙 정의

클래스는 **인터페이스를 통해 의존성을 받아야** 한다. 구체 클래스에 직접 의존하지 않고, 생성자/인스펙터/메서드를 통한 주입.

## 필요성

1. **테스트 가능성**: 모의 객체로 대체 가능
2. **느슨한 결합**: 구현 교체 용이
3. **유연한 아키텍처**: 의존성 방향 역전

## 좋은 예시 ✓

```csharp
public interface IHealthService
{
    void Heal(int amount);
}

public class Player : MonoBehaviour
{
    private IHealthService _healthService;
    
    // ✓ 메서드 주입
    public void Initialize(IHealthService healthService)
    {
        _healthService = healthService;
    }
    
    void OnPickup()
    {
        _healthService?.Heal(10);
    }
}

// ✓ Inspector 주입 (ScriptableObject)
public class Enemy : MonoBehaviour
{
    [SerializeField] private EnemyConfig _config;  // 구체 대신 설정
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 구체 클래스 직접 의존
public class Player : MonoBehaviour
{
    private HealthService _healthService = new HealthService();  // 강한 결합
    
    void OnPickup()
    {
        _healthService.Heal(10);
    }
}

// ✗ Bad: Singleton 직접 참조
public class Enemy : MonoBehaviour
{
    void Attack()
    {
        GameManager.Instance.DealDamage(10);  // 테스트 불가
    }
}
```

## 자동 탐지

```
FOR EACH class field
  IF field type is concrete class (인터페이스 아님)
     AND 인터페이스로 대체 가능
  THEN WARNING
  
FOR EACH new ConcreteClass() in 메서드
  IF 의존성으로 사용됨
  THEN VIOLATION
```

## 허용 예외

- Unity MonoBehaviour 생명주기 관리 객체
- 값 객체 (DTO, ValueObject)
- 유틸리티 정적 클래스
