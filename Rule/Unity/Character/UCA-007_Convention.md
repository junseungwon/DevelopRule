---
name: Unity 캐릭터 아키텍처 — 컨벤션·안티패턴·예외
description: 네이밍/폴더/어셈블리 규칙, PR 체크리스트, 안티패턴 9개, 예외 허용 케이스
category: 규칙
priority: high
---

# UCA-007: 컨벤션 · 안티패턴 · 예외

## 네이밍 규칙

| 역할 | 접미사 | 예시 |
|------|--------|------|
| Model | `Stats` / `State` | `HumanStats`, `InventoryState` |
| View | `View` | `HumanView`, `HudView` |
| Feature | `Feature` | `MovementFeature`, `EatingFeature` |
| Strategy | `Mode` / `Strategy` | `WalkMode`, `RunMode` |
| Config (SO) | `Config` | `MovementConfig`, `EatingConfig` |
| Entry | 캐릭터 이름 그대로 | `Human`, `Enemy` |
| Installer | `Installer` | `MovementInstaller` |
| EventBus 메시지 | 과거분사/명사 | `EatingStarted`, `MovementLockRequested` |
| 인터페이스 | `I` 접두사 | `IHumanFeature`, `ITickableFeature` |

---

## 폴더 구조 권장안

```
Assets/Scripts/Human/
├── Model/
│   └── HumanStats.cs
├── View/
│   └── HumanView.cs
├── Features/
│   ├── Core/
│   │   ├── IHumanFeature.cs
│   │   ├── ITickableFeature.cs
│   │   ├── HumanContext.cs
│   │   └── IHumanEventBus.cs
│   ├── Movement/
│   │   ├── MovementFeature.cs
│   │   ├── IMovementMode.cs
│   │   ├── WalkMode.cs
│   │   └── RunMode.cs
│   └── EatingFeature.cs
├── Installers/
├── Events/
├── Configs/
└── Human.cs   (Entry)
```

---

## 어셈블리 분리 (권장)

MVC 분리를 컴파일 타임에 강제하려면:

1. `Game.Human.Domain.asmdef` — Model·Feature·EventBus·Message
   - `UnityEngine` 참조 제거 (`Vector3` 등 필요 시 CoreModule만)
2. `Game.Human.View.asmdef` — View·Entry·Installer
   - `Game.Human.Domain` 참조
3. `Game.Human.Configs.asmdef` — SO 정의들

Domain 어셈블리에서 Unity API를 실수로 쓰면 **컴파일 에러**로 차단됨.

---

## 안티패턴 9개 ❌

### AP-1: God MonoBehaviour
모든 걸 한 스크립트에 — 이동·공격·UI·먹기가 다 섞임.

### AP-2: Feature마다 MVC
Feature 하나를 Model/View/Controller로 찢음.
MVC는 캐릭터 단위이지 Feature 단위가 아님.

### AP-3: View에 로직 숨기기
```csharp
// ❌ View에 계산·분기 섞임
public void UpdateHpBar(int cur, int max)
{
    hpBar.fillAmount = (float)cur / max;
    hpBar.color = cur < max * 0.3f ? Color.red : Color.green;
}
```

### AP-4: Feature가 Unity API 직접 호출
```csharp
// ❌ Feature 안에서 엔진 API 사용
GameObject.Find("Enemy");
_ctx.Transform.GetComponent<Animator>().SetTrigger("X");
```

### AP-5: SO에 런타임 상태 저장
```csharp
[CreateAssetMenu]
public class PlayerStats : ScriptableObject
{
    public float currentHp;  // ❌ 에셋이라 모든 인스턴스가 공유됨
}
```

### AP-6: 전역 static EventBus
```csharp
public static class GlobalBus { }  // ❌ 캐릭터 2명이면 이벤트가 섞임
```

### AP-7: 이벤트 구독 해제 누락
```csharp
public void Initialize(HumanContext ctx) { ctx.Stats.OnChanged += H; }
// Dispose에서 -= 없음 → 씬 전환마다 고스트 구독 누적
```

### AP-8: Model 이벤트를 외부가 직접 구독
```csharp
_human.Stats.OnStaminaChanged += ...;  // ❌ Entry의 도메인 이벤트를 써라
```

### AP-9: 과도한 추상화
단순한 UI 토글에 Model/View/Controller + Interface 4개.
복잡도가 자라기 시작할 때 리팩토링하면 됨.

---

## 예외 허용 케이스

| 케이스 | 내용 |
|--------|------|
| `Vector3`, `Quaternion` in Model | 순수 수학 구조체는 허용. `Transform`은 불가. |
| Feature 5개 이하 | Installer·EventBus 도입 금지. Entry에서 직접 조립으로 충분. |
| 프로토타입/해커톤 | 버려질 코드에 MVC 강요 금지. 상용 브랜치 머지 전 리팩토링 필수. |
| 단순 UI 토글/이펙트 | Model 없이 View 하나로 충분. |
| SO 읽기 전용 데이터 에셋 | Model의 "저장 형태"로 허용. 런타임 변경 상태는 POCO Model에. |

---

## PR 체크리스트 (전체)

**Model**
- [ ] `MonoBehaviour` / `ScriptableObject` 상속 없음
- [ ] `UnityEngine` import 없음 (예외: Vector 계열)
- [ ] 상태 변경이 `event`로 통지됨

**View**
- [ ] 분기·계산 로직 없음
- [ ] 게임 상태 필드 없음
- [ ] Model·Feature 직접 참조 없음

**Feature**
- [ ] `MonoBehaviour` 상속 없음
- [ ] Unity API 직접 호출 없음
- [ ] `Dispose`에서 구독 해제 수행
- [ ] Config(SO) 수정 없음

**SO Config**
- [ ] 런타임 상태 필드 없음
- [ ] 계산 메서드 없음

**Entry**
- [ ] 캐릭터당 1개만 존재
- [ ] `Awake` 조립 / `OnDestroy` 역순 Dispose
- [ ] 자체 게임 로직 없음

**EventBus (도입 시)**
- [ ] Bus가 `static`이 아님
- [ ] 메시지가 `readonly struct`
- [ ] 모든 Subscribe에 대응하는 Unsubscribe 있음
