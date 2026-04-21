---
name: 코드 주석 및 문서화
description: 모든 공용 타입, 메서드, 프로퍼티는 XML 문서 주석으로 설명
category: 규칙
priority: high
---

# UNITY-COMMENT-008: 코드 주석 및 문서화

## 규칙 정의

모든 **public 타입, 메서드, 프로퍼티**에는 XML 문서 주석(`///`)을 작성한다. `<summary>`, `<param>`, `<returns>` 태그를 사용하여 의도를 명확화한다.

## 필요성

1. **API 문서화**: IntelliSense에서 설명 표시
2. **계약 명시**: 매개변수, 반환값, 예외 조건 명확화
3. **유지보수**: 다른 개발자의 이해 용이

## 좋은 예시 ✓

```csharp
/// <summary>
/// 플레이어에게 지정된 양의 데미지를 적용합니다.
/// </summary>
/// <param name="amount">적용할 데미지 양 (0 이상)</param>
/// <returns>데미지 적용 후 남은 HP</returns>
/// <exception cref="ArgumentException">amount가 음수일 때</exception>
public int ApplyDamage(int amount)
{
    if (amount < 0)
        throw new ArgumentException("Damage cannot be negative");
    
    _health -= amount;
    return _health;
}

/// <summary>현재 플레이어의 체력.</summary>
public int Health { get; private set; }
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: XML 주석 없음
public int ApplyDamage(int amount)
{
    _health -= amount;
    return _health;
}

// ✗ Bad: 의미 없는 주석 (코드 반복)
/// <summary>Health를 반환합니다.</summary>
public int Health { get; private set; }

// ✗ Bad: 일반 주석으로 문서화 시도
// 플레이어에게 데미지 적용
public int ApplyDamage(int amount) { ... }
```

## 자동 탐지

```
FOR EACH public member (클래스/메서드/프로퍼티)
  IF member has no /// comment preceding
  THEN VIOLATION
  
  IF method has parameters
     AND XML has no <param> tag for each
  THEN VIOLATION
```

## 예외

- private/internal 멤버: 선택사항
- 재정의 메서드: `<inheritdoc/>` 사용 가능
- 테스트 메서드: 주석 생략 가능
