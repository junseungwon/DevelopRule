---
name: Unity 캐릭터 아키텍처 — Feature 규칙
description: ITickableFeature 기반 POCO Feature와 Strategy 패턴 사용 규칙
category: 규칙
priority: critical
---

# UCA-004: Feature 규칙 (Controller 분할 단위)

## 규칙 정의

Feature는 **Controller의 책임을 기능 단위로 쪼갠 순수 C# 컴포넌트**다.
캐릭터의 하나의 "행동"이나 "능력"을 캡슐화하며, MVC가 아니라 **Component + Strategy 패턴**으로 이해한다.

---

## 표준 인터페이스

```csharp
public interface IHumanFeature
{
    void Initialize(HumanContext ctx);   // Awake 대체
    void Dispose();                       // OnDestroy 대체
}

public interface ITickableFeature : IHumanFeature
{
    void Tick(float dt);                  // Update 대체
}

public interface IFixedTickableFeature : IHumanFeature
{
    void FixedTick(float dt);             // FixedUpdate 대체
}

public interface ILateTickableFeature : IHumanFeature
{
    void LateTick(float dt);              // LateUpdate 대체
}
```

---

## 해야 할 것 ✅

- 생성자에서 Config(SO) 및 의존 Feature 주입
- `Initialize`에서 Context의 이벤트 구독
- `Tick`에서 매 프레임 로직 수행
- `Dispose`에서 구독 해제 (누수 방지)
- 상태가 있다면 자기 자신 내부에 보관

---

## 하면 안 되는 것 ❌

- `MonoBehaviour` 상속
- Unity API 직접 호출 (`transform`, `GetComponent` 등)
- 비즈니스 규칙 계산 → Model에 위임
- UI 요소 직접 제어 → View 메서드 호출로 위임
- Config(SO) 값 수정 (읽기 전용)

---

## 코드 예시

```csharp
public class MovementFeature : ITickableFeature
{
    private readonly MovementConfig _cfg;
    private HumanContext _ctx;
    private Vector2 _inputAxis;
    private bool _runRequested;
    private readonly HashSet<string> _lockReasons = new();

    public MovementFeature(MovementConfig cfg) { _cfg = cfg; }

    public void Initialize(HumanContext ctx)
    {
        _ctx = ctx;
        _ctx.View.OnMoveInput += HandleMoveInput;
        _ctx.View.OnRunToggle += HandleRunToggle;
        _ctx.Bus.Subscribe<MovementLockRequested>(OnLock);
        _ctx.Bus.Subscribe<MovementUnlockRequested>(OnUnlock);
    }

    public void Dispose()
    {
        _ctx.View.OnMoveInput -= HandleMoveInput;
        _ctx.View.OnRunToggle -= HandleRunToggle;
        _ctx.Bus.Unsubscribe<MovementLockRequested>(OnLock);
        _ctx.Bus.Unsubscribe<MovementUnlockRequested>(OnUnlock);
    }

    public void Tick(float dt)
    {
        if (_lockReasons.Count > 0) { _ctx.View.SetMoveSpeedNormalized(0f); return; }
        var speed = (_runRequested && _ctx.Stats.Stamina > 0f) ? _cfg.runSpeed : _cfg.walkSpeed;
        if (_inputAxis.sqrMagnitude > 0.001f)
        {
            _ctx.Transform.position += new Vector3(_inputAxis.x, 0, _inputAxis.y) * speed * dt;
            if (speed == _cfg.runSpeed)
                _ctx.Stats.ConsumeStamina(_cfg.runStaminaCostPerSec * dt);
        }
        _ctx.View.SetMoveSpeedNormalized(_inputAxis.magnitude * (speed / _cfg.runSpeed));
    }

    private void HandleMoveInput(Vector2 axis) => _inputAxis = axis;
    private void HandleRunToggle(bool on)      => _runRequested = on;
    private void OnLock(MovementLockRequested m)     => _lockReasons.Add(m.Reason);
    private void OnUnlock(MovementUnlockRequested m) => _lockReasons.Remove(m.Reason);
}
```

---

## Strategy 패턴 (Feature 내부 변형)

### 언제 쓰나

한 Feature 안에서 **같은 목적의 여러 변형**이 있을 때.
서로 독점적(동시에 1개만 활성)이고 상호 교체 가능한 것이 조건.

### Strategy vs 새 Feature 판단 기준

| 판단 항목 | Strategy | 새 Feature |
|-----------|:--------:|:----------:|
| 동시에 여러 개 활성 가능? | 불가 (독점) | 가능 |
| 같은 목적의 변형? | 예 | 아니오 |
| 교체가 잦음? | 예 | 아니오 |
| 독립적 상태/수명? | 아니오 | 예 |

**예**: 걷기/뛰기는 Strategy. 먹기/자기는 별개 Feature.

---

## 핵심 체크

- [ ] `MonoBehaviour` 상속 없음
- [ ] Unity API 직접 호출 없음 (`transform`, `GetComponent`)
- [ ] `IHumanFeature` 또는 하위 인터페이스 구현
- [ ] `Dispose`에서 구독 해제 수행
- [ ] 비즈니스 규칙을 Model에 위임 (Feature는 조정만)
- [ ] Config(SO)를 수정하지 않음 (읽기만)
