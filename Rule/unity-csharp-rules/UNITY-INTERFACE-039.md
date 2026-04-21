---
name: Interface 제작 및 사용
description: 인터페이스 설계, 명명, 역할 정의
category: 규칙
priority: high
---

# UNITY-INTERFACE-039: Interface 제작 및 사용

## 규칙 정의

인터페이스 이름은 **I 접두사 + PascalCase**. **단일 책임 원칙**(ISP) 준수, 메서드 5개 이하 권장. 구현체는 인터페이스 통해 의존.

## 필요성

1. **명명 표준**: I 접두사로 인터페이스 식별
2. **작은 인터페이스**: 구현 부담 감소
3. **추상화**: 테스트 가능성과 교체 용이성

## 좋은 예시 ✓

```csharp
// ✓ I 접두사, 단일 책임
public interface IDamageable
{
    int Health { get; }
    void TakeDamage(int amount);
}

public interface IMovable
{
    void Move(Vector3 direction);
    float Speed { get; }
}

public interface IHealable
{
    void Heal(int amount);
}

// ✓ 작은 인터페이스 조합
public class Player : MonoBehaviour, IDamageable, IMovable, IHealable
{
    public int Health { get; private set; }
    public float Speed => 5f;
    
    public void TakeDamage(int amount) => Health -= amount;
    public void Move(Vector3 dir) { /* ... */ }
    public void Heal(int amount) => Health += amount;
}

// ✓ 의존은 인터페이스에
public class CombatSystem
{
    public void Attack(IDamageable target, int damage)
    {
        target.TakeDamage(damage);
    }
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: I 접두사 없음
public interface Damageable  // I 필요
{
    void TakeDamage(int amount);
}

// ✗ Bad: 거대 인터페이스 (ISP 위반)
public interface IGameEntity
{
    void Move(Vector3 dir);
    void TakeDamage(int damage);
    void Heal(int amount);
    void PlaySound(string name);
    void PlayAnimation(string name);
    void SaveState();
    void LoadState();
    // 10+ 메서드 - 모든 구현체가 부담
}

// ✗ Bad: 구체 클래스에 의존
public class CombatSystem
{
    public void Attack(Player target, int damage)  // Player 직접 의존
    {
        target.TakeDamage(damage);
    }
}
```

## 자동 탐지

```
FOR EACH interface declaration
  IF 이름이 "I" 로 시작하지 않음
  THEN VIOLATION
  
  IF 메서드 수 > 5
  THEN WARNING (분리 권장)
  
FOR EACH public method parameter
  IF 타입이 구체 클래스
     AND 인터페이스로 대체 가능
  THEN WARNING
```

## 허용 예외

- 프레임워크 인터페이스 (Unity: IEnumerator 등)
- 마커 인터페이스 (메서드 없음)
- Generic 제약 인터페이스
