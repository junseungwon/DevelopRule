---
name: 명명 규칙
description: 클래스, 메서드, 프로퍼티는 PascalCase, 변수/매개변수는 camelCase
category: 규칙
priority: high
---

# UNITY-NAMING-005: 명명 규칙

## 규칙 정의

C# 표준 명명 규칙 준수:
- **클래스, 인터페이스, 메서드, 프로퍼티**: PascalCase
- **변수, 매개변수, 로컬 상수**: camelCase
- **상수**: UPPER_CASE
- **Private 필드**: _camelCase (언더스코어 접두사)

## 필요성

1. **가독성**: 식별자 타입을 이름만으로 파악 가능
2. **일관성**: 팀 표준 따름으로 코드 통일
3. **도구 호환성**: IDE 자동완성, 리팩토링 지원

## 좋은 예시 ✓

```csharp
public class PlayerController    // ✓ 클래스: PascalCase
{
    private float _moveSpeed;    // ✓ Private 필드: _camelCase
    public int Health { get; set; }  // ✓ 프로퍼티: PascalCase
    public const int MaxHealth = 100;  // ✓ 상수: UPPER_CASE
    
    public void Move(Vector3 direction)  // ✓ 메서드: PascalCase
    {
        float magnitude = direction.magnitude;  // ✓ 로컬 변수: camelCase
    }
}

public interface IDamager  // ✓ 인터페이스: I + PascalCase
{
    void DealDamage(int amount);
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 클래스 소문자
public class playerController
{
    // ✗ Bad: Private 필드에 언더스코어 없음
    public float moveSpeed;
    
    // ✗ Bad: 프로퍼티 camelCase
    public int health { get; set; }
    
    // ✗ Bad: 메서드 camelCase
    public void move(Vector3 direction)
    {
        // ✗ Bad: 상수 소문자
        int maxhealth = 100;
    }
}
```

## 자동 탐지

```
FOR EACH identifier (클래스, 메서드, 프로퍼티, 변수)
  IF identifier type is "Class/Method/Property"
     AND NOT matches "^[A-Z][a-zA-Z0-9]*$"  // PascalCase
  THEN VIOLATION
  
  IF identifier type is "Variable/Parameter"
     AND NOT matches "^[a-z][a-zA-Z0-9]*$"  // camelCase
  THEN VIOLATION
  
  IF identifier is "Constant"
     AND NOT matches "^[A-Z][A-Z0-9_]*$"  // UPPER_CASE
  THEN VIOLATION
```

## 예외

- 재정의: 부모 클래스 규칙 따름
- 외부 라이브러리: 원본 네이밍 유지 가능
- 약자: 약자 전체를 대문자 (HTTPSConnection 아닌 HttpsConnection)
