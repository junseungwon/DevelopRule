---
name: 타입 캐스팅
description: as vs (T), is vs as, 안전한 타입 변환
category: 규칙
priority: high
---

# UNITY-CAST-018: 타입 캐스팅

## 규칙 정의

- **(T)value**: 실패 시 예외 - 확신할 때만
- **as T**: 실패 시 null - 참조 타입에 사용
- **is T t**: 패턴 매칭 - 가장 권장

## 필요성

1. **예외 방지**: 안전한 변환 실패 처리
2. **성능**: 이중 캐스팅 회피 (`is` 후 `as`)
3. **코드 명확성**: 의도 표현

## 좋은 예시 ✓

```csharp
// ✓ is 패턴 매칭 (C# 7+)
public void ProcessObject(object obj)
{
    if (obj is Enemy enemy)
    {
        enemy.Attack();  // 안전하게 사용
    }
}

// ✓ as 연산자 + null 체크
public void DamageTarget(Component target)
{
    var damageable = target as IDamageable;
    if (damageable != null)
    {
        damageable.TakeDamage(10);
    }
}

// ✓ 명시적 캐스트 (확신 있을 때)
float value = 3.14f;
int intValue = (int)value;  // 의도적 소실
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: as + 명시적 캐스트 혼용
if (obj is Enemy)
{
    var enemy = (Enemy)obj;  // 이중 캐스트
}

// ✗ Bad: 예외 가능한 캐스트 (확인 없이)
public void ProcessComponent(object obj)
{
    var enemy = (Enemy)obj;  // 타입 불일치 시 예외
    enemy.Attack();
}

// ✗ Bad: as 후 null 체크 없음
var enemy = obj as Enemy;
enemy.Attack();  // null 참조 예외 가능
```

## 자동 탐지

```
FOR EACH "is T" 후 "(T)obj" 패턴
  THEN VIOLATION (is T t 사용)
  
FOR EACH "as T" 사용
  IF 후속 null 체크 없음
  THEN VIOLATION
  
FOR EACH "(T)value" 명시적 캐스트
  IF T가 참조 타입이고 의심 가능
  THEN WARNING
```

## 허용 예외

- 제네릭 제약으로 타입 보장된 경우
- 값 타입 간 명시적 변환 (int ↔ float)
- 컴파일러가 검증한 경로
