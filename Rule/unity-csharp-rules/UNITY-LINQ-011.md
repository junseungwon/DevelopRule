---
name: LINQ 사용 규칙
description: LINQ 체인 최적화, 성능 고려, 할당 최소화
category: 규칙
priority: high
---

# UNITY-LINQ-011: LINQ 사용 규칙

## 규칙 정의

LINQ는 **성능이 중요하지 않은 경로**에서 사용한다. Update(), FixedUpdate() 내부, 매 프레임 루프에서는 일반 for/foreach 루프 사용. 체인 최소화 및 할당 감소.

## 필요성

1. **성능**: LINQ는 IEnumerable 할당, GC 압력
2. **프레임 예산**: 매 프레임 코드에서 GC 스파이크 방지
3. **가독성 vs 성능**: 핫패스에서는 성능 우선

## 좋은 예시 ✓

```csharp
// ✓ 초기화/이벤트: LINQ 허용
void Awake()
{
    _enemies = FindObjectsOfType<Enemy>()
        .Where(e => e.IsActive)
        .OrderBy(e => e.Priority)
        .ToList();
}

// ✓ 핫패스: for 루프 사용
void Update()
{
    for (int i = 0; i < _enemies.Count; i++)
    {
        if (_enemies[i].IsDead) continue;
        _enemies[i].Tick();
    }
}

// ✓ LINQ 체인 최소화
var firstActive = enemies.FirstOrDefault(e => e.IsActive);  // 전체 열거 방지
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: Update 내 LINQ 사용
void Update()
{
    var closest = _enemies
        .Where(e => e.IsActive)
        .OrderBy(e => Vector3.Distance(e.transform.position, transform.position))
        .FirstOrDefault();
    // 매 프레임 할당 + GC 압력
}

// ✗ Bad: 불필요한 ToList/ToArray
var count = enemies.Where(e => e.IsDead).ToList().Count;  // Count() 사용
```

## 자동 탐지

```
FOR EACH LINQ 메서드 사용 (Where, Select, OrderBy 등)
  IF 속한 메서드가 Update/FixedUpdate/LateUpdate
  THEN VIOLATION (성능 이슈)
  
  IF ToList() / ToArray() 뒤에 Count/Any()
  THEN WARNING (비효율)
```

## 예외

- 초기화 코드: Awake, Start, OnEnable
- 이벤트 기반 저빈도 호출: UI 상호작용
- 컴파일 타임 분석 확실한 경우
