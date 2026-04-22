---
name: Unity 캐릭터 아키텍처 — Composition 규칙
description: Entry 조립·Installer·EventBus·도메인 이벤트·통신 3계층 규칙
category: 규칙
priority: critical
---

# UCA-006: Composition 규칙 (Entry · 확장 패턴 · 통신)

## Entry (Composition Root) 규칙

### 역할

- 캐릭터/시스템 단위로 오직 **1개**의 MonoBehaviour
- Model, View, Feature를 `new`로 생성하고 조립
- Unity 생명주기(`Awake`/`Update`/`OnDestroy`)를 Feature들에게 배포

### 해야 할 것 ✅

- `[SerializeField]` SO Config 참조들
- `[SerializeField]` View 참조
- Awake에서 조립 로직
- Update/FixedUpdate/LateUpdate를 Feature Tick으로 위임
- OnDestroy에서 **역순** Dispose
- 외부에 공개할 `public event` (도메인 이벤트)

### 하면 안 되는 것 ❌

- 게임 로직 (Feature로 분리)
- 수치 계산 (Model/Config로 분리)
- UI 조작 (View로 분리)
- 다른 Entry 직접 참조 (서비스 레이어 경유)

### 기본 구현 (Feature 5개 이하)

```csharp
public class Human : MonoBehaviour
{
    [SerializeField] private HumanView view;
    [SerializeField] private HumanStatsConfig statsConfig;
    [SerializeField] private MovementConfig movementConfig;

    public event Action<float> OnStaminaRatioChanged;
    public event Action OnMealEaten;

    private HumanContext _ctx;
    private readonly List<IHumanFeature> _features = new();
    private readonly List<ITickableFeature> _tickables = new();

    private void Awake()
    {
        var stats = new HumanStats(statsConfig);
        var bus = new HumanEventBus();
        _ctx = new HumanContext(transform, stats, view, bus);

        Register(new MovementFeature(movementConfig));
        Register(new EatingFeature(eatingConfig));

        foreach (var f in _features) f.Initialize(_ctx);

        stats.OnStaminaChanged += (cur, max) => OnStaminaRatioChanged?.Invoke(cur / max);
        bus.Subscribe<EatingFinished>(_ => OnMealEaten?.Invoke());
    }

    private void Update()
    {
        var dt = Time.deltaTime;
        for (int i = 0; i < _tickables.Count; i++) _tickables[i].Tick(dt);
    }

    private void OnDestroy()
    {
        for (int i = _features.Count - 1; i >= 0; i--) _features[i].Dispose();
    }

    private void Register(IHumanFeature f)
    {
        _features.Add(f);
        if (f is ITickableFeature t) _tickables.Add(t);
    }
}
```

---

## 확장 패턴 도입 기준

> 아래 증상이 나타날 때만 도입. 처음부터 전부 적용하면 오버엔지니어링.

| 증상 | 해법 |
|------|------|
| Feature가 5개 이하 | **도입하지 말 것.** 기본 Entry로 충분 |
| Feature가 5~10개, Awake가 길어짐 | Installer 패턴 |
| Feature 간 생성자 참조가 3개 이상 | 내부 EventBus |
| 외부(HUD·Save·Achievement)가 캐릭터 상태 필요 | 도메인 이벤트 |

---

## Installer 패턴 (Feature 5~10개 시)

```csharp
public interface IHumanInstaller
{
    void Install(HumanFeatureRegistry registry, HumanContext ctx);
}

public class MovementInstaller : IHumanInstaller
{
    private readonly MovementConfig _cfg;
    public MovementInstaller(MovementConfig cfg) { _cfg = cfg; }
    public void Install(HumanFeatureRegistry reg, HumanContext ctx)
        => reg.Add(new MovementFeature(_cfg));
}
```

---

## 내부 EventBus (Feature 간 참조 3개 이상 시)

```csharp
public interface IHumanEventBus
{
    void Publish<T>(T message) where T : struct;
    void Subscribe<T>(Action<T> handler) where T : struct;
    void Unsubscribe<T>(Action<T> handler) where T : struct;
}
```

**Bus 사용 원칙**
- Human 인스턴스마다 Bus 하나. `static` 금지.
- 메시지는 `readonly struct` (GC 방지)
- Feature Dispose에서 반드시 `Unsubscribe`
- Bus를 외부(다른 캐릭터·UI)에 노출 금지

---

## 도메인 이벤트 (외부 노출)

**Entry가 정제된 public event를 외부에 노출**

```csharp
// Entry가 내부 이벤트를 도메인 이벤트로 승격
stats.OnStaminaChanged += (cur, max) => OnStaminaRatioChanged?.Invoke(cur / max);
bus.Subscribe<EatingFinished>(_ => OnMealEaten?.Invoke());

// 외부 시스템이 구독
player.OnStaminaRatioChanged += UpdateStamina;
player.OnDied += ShowGameOver;
```

**도메인 이벤트 설계 원칙**
- 외부가 정말로 알아야 할 것만 노출 (3~10개)
- 이름이 의미 중심이어야 함 (`OnMealEaten` ✅ / `OnEatingFeature_RemainingChanged` ❌)
- `HumanStats.OnStaminaChanged` 같은 Model 이벤트를 HUD가 직접 구독 금지 → 반드시 Entry 경유

---

## 이벤트 3계층

```
[외부 계층] Entry.public event (도메인 이벤트)
  Human.OnDied, Human.OnMealEaten
  → HUD, Save, Achievement 등이 구독

[내부 계층] IHumanEventBus (Human 범위 전용)
  MovementLockRequested, EatingStarted
  → Feature 간 통신

[최하위 계층] Model의 event
  HumanStats.OnStaminaChanged
  → Feature들이 상태 변화 감지
```

**원칙**
- 계층을 건너뛰지 않는다 (HUD가 Model 이벤트 직접 구독 ❌)
- 바깥 계층일수록 의미 중심, 안쪽 계층일수록 구현 중심
- `static event` 전역 버스 금지

---

## 구독 해제 규칙 (엄수)

- Feature의 `Dispose`에서 반드시 `-=` / `Unsubscribe`
- Entry의 `OnDestroy`에서 Feature들을 **역순** Dispose
- 미해제 시 씬 전환마다 메모리 누수 + MissingReferenceException

---

## 핵심 체크

- [ ] 캐릭터당 Entry 1개만 존재
- [ ] `Awake`에서 조립, `OnDestroy`에서 역순 Dispose
- [ ] 자체 게임 로직 없음 ("조립"만 담당)
- [ ] 외부 공개 event는 도메인 의미로 네이밍
- [ ] Bus가 `static`이 아님 (Human 인스턴스마다 1개)
- [ ] 메시지가 `readonly struct`
- [ ] 모든 Subscribe에 대응하는 Unsubscribe 있음
