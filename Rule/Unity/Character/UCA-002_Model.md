---
name: Unity 캐릭터 아키텍처 — Model 규칙
description: POCO Model 클래스의 해야 할 것과 하면 안 되는 것
category: 규칙
priority: critical
---

# UCA-002: Model 규칙 (POCO)

## 규칙 정의

Model은 런타임 상태와 비즈니스 규칙을 담는 **순수 C# 클래스**다.
Unity 엔진에 의존하지 않으며, NUnit 단위 테스트의 1순위 대상이다.

---

## 해야 할 것 ✅

- 런타임 데이터·상태 보관
- 비즈니스 규칙 계산 (데미지 공식·쿨다운 등)
- 상태 변화 시 `event` / `Action`으로 외부에 통지
- 입력값 검증 (Guard clause)
- NUnit 단위 테스트 작성

---

## 하면 안 되는 것 ❌

- `MonoBehaviour`, `ScriptableObject` 상속
- `UnityEngine` import (단, `Vector3` 등 순수 수학 타입은 예외)
- `Transform`, `GameObject`, `Animator` 등 엔진 객체 참조
- `GetComponent<>()`, `FindObjectOfType<>()` 호출
- `Time.deltaTime` 같은 Unity 전역 접근 (파라미터로 받을 것)
- View·Feature 직접 참조

---

## 코드 예시

```csharp
public class HumanStats
{
    public float Stamina { get; private set; }
    public float MaxStamina { get; }

    public event Action<float, float> OnStaminaChanged;
    public event Action OnExhausted;

    // SO를 받아서 초기화. SO 참조는 보관하지 않고 값만 복사
    public HumanStats(HumanStatsConfig config)
    {
        MaxStamina = config.maxStamina;
        Stamina = config.maxStamina;
    }

    public void ConsumeStamina(float amount)
    {
        if (amount < 0) throw new ArgumentException(nameof(amount));
        var prev = Stamina;
        Stamina = Math.Max(0f, Stamina - amount);
        if (prev != Stamina) OnStaminaChanged?.Invoke(Stamina, MaxStamina);
        if (prev > 0f && Stamina == 0f) OnExhausted?.Invoke();
    }
}
```

---

## 핵심 체크

- [ ] `MonoBehaviour` / `ScriptableObject` 상속 없음
- [ ] `UnityEngine` import 없음 (예외: Vector 계열)
- [ ] 상태 변경이 `event`로 통지됨
- [ ] NUnit 테스트 존재
