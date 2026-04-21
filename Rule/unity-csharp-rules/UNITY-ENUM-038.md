---
name: Enum 사용
description: Enum 설계, Flags 속성, 타입 안전성
category: 규칙
priority: high
---

# UNITY-ENUM-038: Enum 사용

## 규칙 정의

열거형 값 표현 시 **enum 사용**(int/string 상수 금지). 여러 값 조합 시 `[Flags]` + 2의 거듭제곱 값. 기본값 명시(0번).

## 필요성

1. **타입 안전성**: 잘못된 값 전달 방지
2. **가독성**: 의미있는 이름으로 표현
3. **확장성**: 새 값 추가 용이

## 좋은 예시 ✓

```csharp
// ✓ 일반 enum
public enum WeaponType
{
    None = 0,    // 기본값 명시
    Sword,
    Bow,
    Staff
}

// ✓ Flags enum (조합 가능)
[Flags]
public enum DamageType
{
    None = 0,
    Physical = 1 << 0,  // 1
    Magical = 1 << 1,   // 2
    Fire = 1 << 2,      // 4
    Ice = 1 << 3,       // 8
    All = Physical | Magical | Fire | Ice
}

// ✓ Flags 사용
var damage = DamageType.Physical | DamageType.Fire;
if ((damage & DamageType.Fire) != 0) ApplyBurn();

// ✓ 명시적 기반 타입
public enum Score : byte { Low, Mid, High }  // 메모리 절약
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: int 상수 사용
public const int WEAPON_SWORD = 0;
public const int WEAPON_BOW = 1;
// enum 사용이 더 안전

// ✗ Bad: 문자열 상수
public const string STATE_IDLE = "idle";
public const string STATE_RUNNING = "running";
// 오타 취약, 타입 안전 부재

// ✗ Bad: Flags에 잘못된 값
[Flags]
public enum BadFlags
{
    A = 1,
    B = 2,
    C = 3,  // 잘못됨! (A|B = 3과 충돌)
    D = 4
}

// ✗ Bad: 기본값 없음
public enum State
{
    Running,  // 첫 값이 의미있는 값
    Paused,
    // 기본값 0이 "Running"이 되어버림
}
```

## 자동 탐지

```
FOR EACH public const int/string 그룹
  IF 같은 의미의 값 집합
  THEN WARNING (enum 권장)
  
FOR EACH [Flags] enum
  IF 값이 2의 거듭제곱 아님
  THEN VIOLATION
  
FOR EACH enum
  IF 0 값 정의 없음 / None 없음
  THEN WARNING
```

## 허용 예외

- 기반 라이브러리 상수 (플랫폼 API)
- 동적으로 확장되는 값 (DB 키 등)
