---
name: 상수 vs 읽기전용
description: const와 readonly의 차이를 이해하고 올바르게 선택
category: 규칙
priority: high
---

# UNITY-CONST-015: 상수 vs 읽기전용

## 규칙 정의

- **const**: 컴파일 타임에 확정되는 값 (primitive, string)
- **readonly**: 런타임에 결정되나 생성 이후 변경 불가
- **static readonly**: 참조 타입 상수

## 필요성

1. **정확한 의도**: 각 키워드의 의미가 다름
2. **버전 호환성**: const는 참조 어셈블리에 인라인됨
3. **런타임 유연성**: readonly는 생성자에서 초기화 가능

## 좋은 예시 ✓

```csharp
public class GameConfig
{
    // ✓ 컴파일 타임 상수
    public const int MAX_PLAYERS = 4;
    public const string VERSION = "1.0.0";
    
    // ✓ 참조 타입은 static readonly
    public static readonly Vector3 SPAWN_POINT = new Vector3(0, 1, 0);
    public static readonly string[] LEVEL_NAMES = { "Forest", "Desert" };
    
    // ✓ 인스턴스별 readonly
    public readonly int Id;
    public readonly DateTime CreatedAt;
    
    public GameConfig(int id)
    {
        Id = id;
        CreatedAt = DateTime.UtcNow;
    }
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 참조 타입에 const 시도 (컴파일 오류)
public const Vector3 SPAWN = new Vector3(0, 1, 0);

// ✗ Bad: 어셈블리 간 const 사용 (버전 호환성)
public const int API_VERSION = 2;
// 호출 측은 재컴파일 필요

// ✗ Bad: 변경 불가한 값을 일반 필드로
public static int MaxHealth = 100;  // const/readonly여야 함
```

## 자동 탐지

```
FOR EACH field
  IF value is literal primitive/string
     AND never reassigned
     AND NOT const
  THEN WARNING (const 권장)
  
  IF value is readonly reference type
     AND NOT static readonly
  THEN WARNING
```

## 허용 예외

- 유닛 테스트용 필드 (수정 필요)
- 빌드 플래그로 조건부 초기화
