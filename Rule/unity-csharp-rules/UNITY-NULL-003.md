---
name: Null 안전성 처리
description: 참조 타입 매개변수는 입력 시 Null 체크를 수행해야 함
category: 규칙
priority: high
---

# UNITY-NULL-003: Null 안전성 처리

## 규칙 정의

모든 참조 타입 매개변수는 사용 전에 **Null 체크를 수행**해야 한다. 특히 GameObject, Component, 배열, 컬렉션 등에서 필수.

```csharp
매개변수 사용 전: if (parameter != null)
또는 null-safe operator: parameter?.Method()
```

## 필요성

1. **런타임 안전성**: NullReferenceException 방지
2. **예기치 않은 동작 방지**: null 상태에서 메서드 호출로 인한 버그
3. **디버깅 용이**: 명시적 null 체크로 의도 명확화

## 좋은 예시 ✓

```csharp
public void ProcessGameObject(GameObject target)
{
    if (target == null)
    {
        Debug.LogError("Target cannot be null");
        return;
    }
    target.SetActive(false);
}

public void InitializeComponent(Component component)
{
    if (component != null)
    {
        component.enabled = true;
    }
}

public string SafeGetName(Transform transform)
{
    return transform?.name ?? "Unknown";
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: Null 체크 없음
public void ProcessGameObject(GameObject target)
{
    target.SetActive(false);  // target이 null이면 NullReferenceException
}

// ✗ Bad: 배열 null 체크 누락
public void ProcessItems(Item[] items)
{
    foreach (var item in items)
    {
        item.Use();  // items가 null이면 실패
    }
}
```

## 자동 탐지

```
FOR EACH method parameter (참조 타입)
  IF parameter used in body
     AND no null check before first use
  THEN VIOLATION
```

## 예외

- 제약: [NotNull] 속성이 있는 경우
- 계약: 호출자 책임이 명시된 경우 (문서화 필수)
- 컨테이너: LINQ Where() 이후 null 필터링된 경우
