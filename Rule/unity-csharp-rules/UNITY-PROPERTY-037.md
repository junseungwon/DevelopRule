---
name: 속성 제작
description: 자동 속성, getter/setter 검증, 프라이빗 setter
category: 규칙
priority: high
---

# UNITY-PROPERTY-037: 속성 제작

## 규칙 정의

간단한 경우 **자동 속성**(`{ get; set; }`) 사용. 외부 변경 불필요하면 **`private set`**. 검증/계산 필요 시 **전체 속성** 구현. 부작용 큰 연산은 메서드로.

## 필요성

1. **캡슐화**: 내부 상태 보호
2. **불변성**: `private set`/`init`으로 변경 제한
3. **명확성**: 프로퍼티는 값, 메서드는 동작

## 좋은 예시 ✓

```csharp
public class Player
{
    // ✓ 자동 속성 (간단한 경우)
    public string Name { get; set; }
    
    // ✓ Private set (외부 수정 불가)
    public int Health { get; private set; }
    public int Score { get; private set; }
    
    // ✓ 전체 속성 (검증 필요)
    private float _volume;
    public float Volume
    {
        get => _volume;
        set => _volume = Mathf.Clamp01(value);
    }
    
    // ✓ 계산 속성 (가벼운 연산)
    public bool IsAlive => Health > 0;
    
    // ✓ Init-only (불변)
    public DateTime CreatedAt { get; init; }
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: getter/setter만 있는 private 필드 + 공개 메서드
private int _health;
public int GetHealth() => _health;
public void SetHealth(int value) => _health = value;
// → 자동 속성으로 대체

// ✗ Bad: 프로퍼티에 무거운 연산
public List<Enemy> ActiveEnemies
{
    get => FindObjectsOfType<Enemy>().Where(e => e.IsActive).ToList();
    // 매 호출 시 비용 - 메서드로 하거나 캐싱 필요
}

// ✗ Bad: Setter에 부작용
public int Level
{
    set
    {
        _level = value;
        SaveToDisk();       // 부작용
        NotifyServer();     // 부작용
        RebuildUI();        // 부작용
    }
}
// → SetLevel 메서드가 더 명확
```

## 자동 탐지

```
FOR EACH "Get/Set + private field" 패턴
  THEN VIOLATION (자동 속성 권장)
  
FOR EACH property getter
  IF 무거운 연산 (루프, FindObject)
  THEN WARNING (메서드 권장)
  
FOR EACH property setter
  IF 여러 부작용 (I/O, 이벤트, 상태 변경)
  THEN WARNING (메서드 권장)
```

## 허용 예외

- Unity Inspector용 백킹 필드
- WPF/UI 바인딩용 프로퍼티
- 지연 초기화 패턴
