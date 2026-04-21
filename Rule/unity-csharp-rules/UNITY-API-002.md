---
name: 속성 기반 API 캡슐화
description: 공용 상태는 프로퍼티를 통해 캡슐화되어야 하며, 공용 필드는 금지
category: API 설계
priority: high
---

# UNITY-API-002: 속성 기반 API 캡슐화

## 규칙 정의

공용으로 노출되는 모든 상태는 **프로퍼티를 통해 캡슐화**되어야 한다. 공용 필드는 상수(const) 또는 읽기전용(readonly)에만 허용된다.

## 좋은 예시 ✓

```csharp
public class AudioSource
{
    public float Volume { get; set; }
    public bool IsPlaying { get; set; }
}

public class Slider
{
    private float _value;
    public float Value
    {
        get { return _value; }
        set { _value = Mathf.Clamp01(value); }
    }
}
```

## 나쁜 예시 ✗

```csharp
// ✗ Bad: 공용 필드
public class AudioSource
{
    public float volume;
    public AudioClip currentClip;
}
```

## 자동 탐지

```
FOR EACH public member
  IF member is field
     AND member is not const
     AND member is not readonly
  THEN VIOLATION
```
