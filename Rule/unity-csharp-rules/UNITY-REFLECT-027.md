---
name: Reflection 사용
description: 성능 캐싱, 안전한 호출, 메타데이터 접근
category: 규칙
priority: high
---

# UNITY-REFLECT-027: Reflection 사용

## 규칙 정의

Reflection은 **초기화 단계에서만** 사용하고, 결과를 **캐싱**한다. 런타임 핫패스에서는 델리게이트 또는 Expression 컴파일로 대체.

## 필요성

1. **성능**: Reflection 호출은 매우 비용 높음
2. **AOT 호환성**: IL2CPP 빌드 고려
3. **타입 안전성**: 컴파일 검증 부재 보완

## 좋은 예시 ✓

```csharp
public class AttributeCache
{
    // ✓ 정적 캐싱
    private static Dictionary<Type, MyAttribute> _cache = new();
    
    public static MyAttribute GetAttribute(Type type)
    {
        if (!_cache.TryGetValue(type, out var attr))
        {
            attr = type.GetCustomAttribute<MyAttribute>();
            _cache[type] = attr;
        }
        return attr;
    }
}

// ✓ 델리게이트 캐싱 (핫패스용)
public class DynamicInvoker
{
    private readonly Action<object, object> _setter;
    
    public DynamicInvoker(PropertyInfo property)
    {
        _setter = (obj, val) => property.SetValue(obj, val);
        // 또는 Expression.Compile로 최적화
    }
    
    public void Invoke(object target, object value) => _setter(target, value);
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: Update에서 Reflection
void Update()
{
    var method = GetType().GetMethod("Tick");
    method.Invoke(this, null);  // 매 프레임 리플렉션
}

// ✗ Bad: 캐싱 없이 반복 접근
public void SetField(object obj, string fieldName, object value)
{
    var field = obj.GetType().GetField(fieldName);  // 매번 검색
    field.SetValue(obj, value);
}
```

## 자동 탐지

```
FOR EACH 리플렉션 API 호출 (GetMethod, GetField 등)
  IF 속한 메서드가 Update/FixedUpdate
  THEN VIOLATION
  
  IF 캐싱 없이 반복 호출
  THEN WARNING
```

## 허용 예외

- 에디터 전용 도구 (#if UNITY_EDITOR)
- 초기화 시 한 번만 호출
- 테스트 프레임워크 내부
