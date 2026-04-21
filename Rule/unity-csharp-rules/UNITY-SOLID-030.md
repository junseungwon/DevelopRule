---
name: SOLID 원칙
description: 단일 책임, 개방-폐쇄, 리스코프, 인터페이스 분리, 의존성 역전
category: 규칙
priority: high
---

# UNITY-SOLID-030: SOLID 원칙

## 규칙 정의

- **S**: 단일 책임 (Single Responsibility)
- **O**: 개방-폐쇄 (Open/Closed)
- **L**: 리스코프 치환 (Liskov Substitution)
- **I**: 인터페이스 분리 (Interface Segregation)
- **D**: 의존성 역전 (Dependency Inversion)

## 필요성

1. **유지보수성**: 변경 영향 범위 최소화
2. **확장성**: 기존 코드 수정 없이 기능 추가
3. **테스트 가능성**: 작은 책임 단위로 분리

## 좋은 예시 ✓

```csharp
// ✓ S: 단일 책임 - 각 클래스가 하나의 역할
public class PlayerMovement { public void Move(Vector3 dir) { /* ... */ } }
public class PlayerHealth { public void TakeDamage(int d) { /* ... */ } }
public class PlayerInventory { public void AddItem(Item i) { /* ... */ } }

// ✓ O: 개방-폐쇄 - 확장에 열려있고 변경에 닫힘
public abstract class Enemy
{
    public abstract void Attack();
}
public class Zombie : Enemy { public override void Attack() { /* ... */ } }
public class Ghost : Enemy { public override void Attack() { /* ... */ } }

// ✓ I: 인터페이스 분리 - 필요한 기능만 노출
public interface IMovable { void Move(Vector3 dir); }
public interface IDamageable { void TakeDamage(int damage); }

public class Player : MonoBehaviour, IMovable, IDamageable { /* ... */ }
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: S 위반 - God Class
public class PlayerManager : MonoBehaviour
{
    public void Move(Vector3 dir) { /* ... */ }
    public void TakeDamage(int d) { /* ... */ }
    public void AddItem(Item i) { /* ... */ }
    public void PlayAnimation(string name) { /* ... */ }
    public void SaveGame() { /* ... */ }
    // 너무 많은 책임
}

// ✗ Bad: O 위반 - 새 타입 추가마다 수정
public class EnemyController
{
    public void Attack(string enemyType)
    {
        if (enemyType == "Zombie") { /* ... */ }
        else if (enemyType == "Ghost") { /* ... */ }
        // 새 타입마다 여기 수정
    }
}

// ✗ Bad: I 위반 - Fat Interface
public interface IGameEntity
{
    void Move(Vector3 dir);
    void TakeDamage(int d);
    void PlaySound();
    void Animate();
    // 모든 기능 - 구현체가 불필요한 것까지 구현
}
```

## 자동 탐지

```
FOR EACH class
  IF 필드/메서드 수 > 20
  THEN WARNING (S 위반 의심)
  
FOR EACH "if/else if" 타입 분기
  IF 타입별 분기가 3개 이상
  THEN WARNING (O 위반 - 다형성 권장)
  
FOR EACH interface
  IF 메서드 수 > 5
  THEN WARNING (I 위반 의심)
```

## 허용 예외

- DTO/ValueObject 클래스
- 데이터 전용 컴포넌트
- 프로토타입 초기 버전
