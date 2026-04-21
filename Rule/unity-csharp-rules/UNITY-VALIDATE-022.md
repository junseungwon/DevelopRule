---
name: 데이터 검증
description: 입력값 검증, 경계값 확인, 예상 범위 벗어남 감지
category: 규칙
priority: high
---

# UNITY-VALIDATE-022: 데이터 검증

## 규칙 정의

**Public 메서드의 모든 매개변수**는 사용 전에 검증해야 한다. 경계값 체크, 범위 확인, 예상 범위 벗어남 감지. 에디터에서는 `OnValidate()`로 인스펙터 값 검증.

## 필요성

1. **방어적 프로그래밍**: 잘못된 입력 조기 감지
2. **디버깅 용이**: 버그 원인 명확화
3. **API 계약**: 명시적 사전 조건

## 좋은 예시 ✓

```csharp
public class Character
{
    public void SetHealth(int health)
    {
        if (health < 0)
            throw new ArgumentException("Health cannot be negative", nameof(health));
        if (health > _maxHealth)
            throw new ArgumentException($"Health exceeds max ({_maxHealth})");
        
        _health = health;
    }
    
    public void ApplyDamage(int damage)
    {
        if (damage < 0) return;  // 가벼운 검증
        _health = Mathf.Max(0, _health - damage);
    }
}

// ✓ 에디터 인스펙터 값 검증
public class EnemyConfig : MonoBehaviour
{
    [SerializeField] private float _speed = 5f;
    
    void OnValidate()
    {
        if (_speed < 0) _speed = 0;
    }
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 검증 없이 사용
public void SetHealth(int health)
{
    _health = health;  // 음수 가능, MaxHealth 초과 가능
}

// ✗ Bad: 배열/컬렉션 인덱스 검증 없음
public Item GetItem(int index)
{
    return _items[index];  // IndexOutOfRangeException
}
```

## 자동 탐지

```
FOR EACH public method parameter
  IF 값 타입이 range-sensitive (int, float)
     AND 사용 전 검증 없음
  THEN WARNING
  
FOR EACH 배열/리스트 인덱스 접근
  IF 인덱스가 매개변수
     AND 범위 체크 없음
  THEN VIOLATION
```

## 허용 예외

- private/internal 메서드 (신뢰 가능 호출자)
- 성능 크리티컬 핫패스 (프로파일링 확인)
- [NotNull] 등 계약 속성 적용 시
