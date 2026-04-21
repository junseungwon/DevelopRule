---
name: 제네릭 타입 제약
description: 제네릭 타입 매개변수는 필요한 제약 조건을 명시해야 함
category: 규칙
priority: high
---

# UNITY-GENERIC-004: 제네릭 타입 제약

## 규칙 정의

모든 제네릭 타입 매개변수는 **필요한 제약 조건(constraint)을 명시**해야 한다. 제약 없는 제네릭은 타입 안전성을 감소시킨다.

```csharp
패턴: where T : [BaseClass/Interface/struct/class]
```

## 필요성

1. **타입 안전성**: 컴파일 타임에 유효한 타입 검증
2. **의도 명확화**: 어떤 타입을 기대하는지 명시
3. **성능 최적화**: 박싱/언박싱 회피 가능

## 좋은 예시 ✓

```csharp
// where T : class - 참조 타입만 허용
public class Pool<T> where T : class
{
    private List<T> _items = new();
    
    public T Get() => _items.Count > 0 ? _items[0] : null;
}

// where T : Component - Unity Component만 허용
public T GetComponent<T>() where T : Component
{
    return GetComponent<T>();
}

// 복합 제약
public class Repository<T> where T : IEntity, new()
{
    public T Create() => new();
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 제약 없음 - 타입 안전성 부재
public class Pool<T>
{
    private List<T> _items = new();
}

// ✗ Bad: 의도 모호 - 어떤 타입인지 불명확
public void Serialize<T>(T data)
{
    // T가 무엇인지 알 수 없음
}
```

## 자동 탐지

```
FOR EACH generic type parameter T
  IF T used in method body
     AND T has no constraint
  THEN WARNING (context에 따라 필수)
```

## 예외

- 제약 불가능: 진정한 범용 메서드
- 위임: 인터페이스 구현 시 부모 정의 따름
