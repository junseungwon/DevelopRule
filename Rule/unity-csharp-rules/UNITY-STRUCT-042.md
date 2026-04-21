---
name: Struct 제작
description: 구조체 설계, 불변성, 크기 제한
category: 규칙
priority: high
---

# UNITY-STRUCT-042: Struct 제작

## 규칙 정의

구조체는 **작은 값 타입**(16바이트 이하)에 사용. **불변(readonly)**으로 설계. 논리적으로 단일 값 표현. 상속/가상 메서드 없음.

## 필요성

1. **성능**: 스택 할당, 박싱 회피
2. **의미론**: 값 타입 복사 의도 명시
3. **불변성**: 버그 예방 (복사본 수정은 원본 영향 없음)

## 좋은 예시 ✓

```csharp
// ✓ 작은 불변 구조체
public readonly struct Damage
{
    public int Amount { get; }
    public DamageType Type { get; }
    public Vector3 Source { get; }
    
    public Damage(int amount, DamageType type, Vector3 source)
    {
        Amount = amount;
        Type = type;
        Source = source;
    }
}

// ✓ IEquatable 구현
public readonly struct GridPosition : IEquatable<GridPosition>
{
    public int X { get; }
    public int Y { get; }
    
    public GridPosition(int x, int y) { X = x; Y = y; }
    
    public bool Equals(GridPosition other) => X == other.X && Y == other.Y;
    public override bool Equals(object obj) => obj is GridPosition p && Equals(p);
    public override int GetHashCode() => HashCode.Combine(X, Y);
}

// ✓ 읽기전용 메서드 (값 복사 최소화)
public readonly struct Point3D
{
    public float X { get; }
    public float Y { get; }
    public float Z { get; }
    
    public float DistanceTo(Point3D other) { /* ... */ }
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 거대한 struct (복사 비용)
public struct BigData  // 100 바이트 넘음
{
    public float A, B, C, D, E, F, G, H, I, J;
    public Vector3 V1, V2, V3, V4, V5;
    public string Name;  // 참조 타입 포함
    // 매 전달마다 100+ 바이트 복사
}

// ✗ Bad: 가변 struct (혼란 원인)
public struct Point
{
    public int X;
    public int Y;
    public void Move(int dx, int dy) { X += dx; Y += dy; }  // 복사본 수정
}
var p = new Point();
GetPoint().Move(1, 1);  // 원본 변경 안됨 - 버그

// ✗ Bad: 엔티티 같은 큰 객체
public struct Player  // 많은 필드, 식별자 있음
{
    public string Name;
    public int Health;
    public Inventory Inventory;
    // class여야 함
}
```

## 자동 탐지

```
FOR EACH struct declaration
  IF 크기 > 16 바이트 (추정)
  THEN WARNING (class 권장)
  
  IF 필드가 readonly 아님
     AND public field
  THEN WARNING (불변 권장)
  
  IF 참조 타입 필드 포함
  THEN WARNING (복사 시 얕은 복사)
```

## 허용 예외

- Unity 내장 구조체 (Vector3, Quaternion)
- Interop/네이티브 구조체
- 성능 크리티컬 버퍼 구조체
