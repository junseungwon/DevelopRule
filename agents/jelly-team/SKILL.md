# Jelly Molly Code Reviewer Skill — 5축 점검 상세 가이드

## 📋 스킬 정의

| 속성 | 값 |
|------|-----|
| **스킬명** | jelly-code-review-5axis |
| **역할** | 5축 점검 기준·프로세스·출력 포맷 상세 정의 |
| **입력** | 점검 범위 경로 |
| **출력** | 리뷰 보고서 (unity-documenter 위임 후 문서 경로) |

---

## 🔖 점검 전 필수 절차 (생략 금지)

1. `C:\DevelopRule\Rule\unity-csharp-rules\HUB.md` Read
2. 아래 규칙 파일 Read (축 근거로 활용):
   - `UNITY-LIFECYCLE-021.md` — Awake/Start/OnEnable 본문 함수화 근거 (축2)
   - `UNITY-CLASS-036.md` — MonoBehaviour 남용 / POCO 전환 근거 (축1)
   - `UNITY-FUNCTION-041.md` — 함수 단위 책임 분리 근거 (축2)
   - `UNITY-DI-020.md` — 의존성 주입·자동 패씽 대체 근거 (축4)
   - `UNITY-VALIDATE-022.md` — 초기화 1회 검증·불변식 확정 근거 (축4/축5)
   - `UNITY-NULL-003.md` — null 처리 원칙·방어적 가드 제거 근거 (축5)
   - `UNITY-EXCEPT-006.md` — 예외·명시적 실패 vs 조용한 early-return 근거 (축5)
   - 그 외 보고 항목에 해당하는 규칙은 필요 시 추가 Read
3. 보고서 말미에 `검증 규칙: [UNITY-003, UNITY-006, UNITY-020, UNITY-021, UNITY-022, UNITY-036, UNITY-041, ...]` 명시

---

## 🔍 5축 상세 점검 기준

### 축 1. MonoBehaviour 최소화 — POCO 전환/분리 후보 식별

**기본 원칙 (UNITY-CLASS-036):**
> 도메인 로직은 **POCO(순수 C#)**. MonoBehaviour는 **Unity API/메시지 연결 전용**. 상속보다 조합. 기본 `sealed`.

**중요:** "Unity API를 쓰고 있어서 MonoBehaviour를 유지해야 한다"는 **잘못된 판단**이다. Unity API가 사용되고 있더라도, 그 안에 섞인 **도메인 로직은 POCO로 분리**해야 하며 MonoBehaviour는 얇은 **뷰/어댑터**로 축소한다.

**점검 카테고리는 3가지. 하나라도 해당하면 보고 대상.**

#### 카테고리 A. 완전 전환 (MonoBehaviour → POCO)

**판별 체크리스트 — "없음" 또는 "대체 가능"이면 정리 대상:**
- [ ] Unity 라이프사이클 메시지 호출 없음 / 대체 가능
- [ ] `[SerializeField]` / Inspector 노출 없음 / 대체 가능
- [ ] `StartCoroutine` / `InvokeRepeating` / `Invoke` 없음 / 대체 가능
- [ ] `gameObject` / `transform` / `GetComponent` 등 Unity 런타임 API 없음 / 대체 가능
- [ ] Inspector 드래그 참조 / `AddComponent` 없음 / 대체 가능

→ 모두 "없음"이면 **즉시 POCO 전환 필수(Must)**.
→ 일부 "대체 가능"이면 **카테고리 A로 분류, 대체 경로 명시**. 대체 불가 항목 1개 이상이면 카테고리 B로 재분류.

#### 카테고리 B. 분리 (Fat MonoBehaviour → Thin MonoBehaviour + POCO)

**도메인 로직 판별 신호 (하나라도 해당하면 분리 후보):**
- [ ] 상태 값 계산/변환(체력·점수·확률·타이머 등)을 직접 수행
- [ ] 규칙·정책·판정 로직(데미지 공식, 조건 분기, 상태 머신 전이 등) 포함
- [ ] 이벤트/옵저버 발행 로직이 Unity 메시지 본문에 직접 섞임
- [ ] `UnityEngine` 외부에서도 의미를 갖는 데이터/행동 소유
- [ ] 유닛 테스트 대상이 될 법한 순수 로직이 Unity 의존 코드와 뒤섞임

**권장 분리 패턴:**
```csharp
// POCO — 도메인 (순수 C#, 테스트 가능)
public class Health
{
    public int Current { get; private set; }
    public event Action OnDeath;
    public Health(int max) => Current = max;
    public void TakeDamage(int amount) { /* 규칙/계산 */ }
}

// Thin MonoBehaviour — Unity 연결만
public sealed class PlayerHealthView : MonoBehaviour
{
    [SerializeField] private int _maxHealth = 100;
    private Health _health;
    private void Awake() => Init();
    private void Init() => _health = new Health(_maxHealth);
    public void TakeDamage(int n) => _health.TakeDamage(n);
}
```

#### 카테고리 C. MonoBehaviour 유지가 타당 (예외)
- 다른 MonoBehaviour/GameObject 수명에 직접 묶인 순수 연결 코드
- `ScriptableObject` 전용 데이터 컨테이너
- 프레임워크 베이스 클래스
- Inspector 드래그 참조가 본질적 요구인 순수 세팅 컴포넌트

→ 이 경우에도 **내부에 도메인 로직이 있으면 카테고리 B로 재분류**.

---

### 축 2. 라이프사이클 본문 — 1줄 위임 원칙

**문제 패턴:** `Awake`, `Start`, `OnEnable`, `OnDisable`, `OnDestroy`, `Update`, `FixedUpdate` 본문에 로직·함수 호출이 직접 나열되어 있어 외부 호출이 불가능하고 재초기화가 어려움.

**판별 기준 — 하나라도 해당하면 지적 대상:**
- 라이프사이클 메서드 본문이 **2줄 이상**이거나
- 단일 위임 메서드 없이 **여러 함수를 직접 나열**하거나
- `if`/`for` 등 제어문을 **직접 포함**하거나
- 한 메서드가 **여러 시스템**의 책임을 동시에 수행

**권장 패턴 — 라이프사이클 1줄 위임 원칙:**

각 라이프사이클 메서드는 **단일 위임 메서드 호출 1줄만** 남기고, 실제 로직은 위임 메서드 안에 집약한다.
이렇게 하면 외부(다른 컴포넌트·테스트·재초기화 흐름)에서 위임 메서드를 직접 호출할 수 있다.

```csharp
private void Awake()     => Init();
private void Start()     => LateInit();
private void OnEnable()  => EnableInternal();
private void OnDisable() => DisableInternal();
private void OnDestroy() => DisposeInternal();
private void Update()    => Tick();
private void FixedUpdate() => FixedTick();
```

위임 메서드 안에서 세부 함수들을 호출한다:

```csharp
private void Init()
{
    CacheReferences();
    InitializeState();
    BuildSubsystems();
}

private void EnableInternal()
{
    SubscribeEvents();
    BindInput();
    StartRoutines();
}
```

- `Init`, `LateInit`, `EnableInternal`, `DisableInternal`, `DisposeInternal`, `Tick`, `FixedTick` 등은 재초기화·테스트·HSM 연동 등 이후 사용 목적이 있을 때 `internal` 또는 `public`으로 열어 외부 호출 허용 검토.

---

### 축 3. 불필요 static 식별 및 정리

**판별 체크리스트 — 해당 시 정리 후보:**
- [ ] 상태를 가진 static 필드 — 싱글턴으로 위장된 전역 상태
- [ ] 한 곳에서만 쓰이는 static 메서드 — 인스턴스 메서드 또는 지역 함수로 이전 가능
- [ ] 캐시 없이 매번 계산만 하는 static 메서드 중 DI 대상이 될 수 있는 것
- [ ] `static readonly` 불필요 — `const`로 충분한 값
- [ ] 테스트 격리를 막는 static 상태 — `Domain Reload Disabled` 충돌 위험
- [ ] 순수 유틸리티가 아닌 static 클래스 — 협력 객체와 혼재

**유지가 타당한 static (제외):**
- 순수 함수 유틸리티 / `const` / 확장 메서드 / MonoBehaviour static 이벤트 허브(명시적 설계)

---

### 축 4. 의미 없는 자동 패씽(Auto-Wiring) 식별

**대표적인 자동 패씽 시그니처 — 해당하면 축4 후보:**
- [ ] `if (field == null) field = GetComponent<T>();`
- [ ] `if (field == null) field = gameObject.AddComponent<T>();` (**가장 강한 위반**)
- [ ] `if (field == null) field = FindObjectOfType<T>();` / `FindAnyObjectByType` / `FindFirstObjectByType`
- [ ] `if (field == null) field = GetComponentInParent<T>();` / `GetComponentInChildren<T>()` 폴백
- [ ] `Singleton/Context.Instance.GetFeature<T>()` 실패 시 `GetComponent<T>()` 폴백
- [ ] `if (field == null) field = new T();` — POCO를 "혹시 몰라서" 기본 생성
- [ ] `Awake`/`Start`에서 아무 조건 없이 `GetComponent<T>()` / `AddComponent<T>()` 호출 (Inspector 직결로 충분한 경우)

**유지가 타당한 패씽 (예외):**
- `RequireComponent` + `GetComponent<T>()` 1회
- 명시적 팩토리/빌더 내부 생성 책임 집중
- 설계 의도인 풀링/스포너

**정리 원칙:**
1. Inspector 직결 가능한 참조는 Inspector 드래그 단일 경로로 고정 — 폴백 제거
2. POCO로 바꿀 수 있는 참조는 생성자/Init 주입으로 단일화
3. `Find*` 계열은 원칙적 금지 — 서비스 로케이터/컨텍스트 주입으로 교체
4. "비어 있으면 넣어준다"는 코드는 재설계 대상 1순위

---

### 축 5. 방어적·불필요 null 검사 제거

**제거 대상 시그니처 — 해당하면 축5 후보:**
- [ ] 생성자·Init에서 주입되고 읽기 전용인 필드에 대한 반복 `if (field == null) return;` / `field?.X`
- [ ] `RequireComponent`가 보장하는 컴포넌트에 대한 null 검사
- [ ] `Awake`/`Start`에서 캐시된 직후 절대 교체되지 않는 참조에 대한 반복 null 검사
- [ ] `new`로 직접 생성해 절대 null일 수 없는 POCO 필드에 대한 `?.` / null 가드
- [ ] 메서드 반환값이 계약상 null이 아닌 API에 대한 null 가드
- [ ] 동일 스코프에서 이미 null 아님을 확인한 뒤 재확인하는 중복 검사
- [ ] Null Coalescing(`??`) / Null Conditional(`?.`)로 상태 이상을 조용히 삼키는 케이스

**유지가 타당한 null 검사 (예외):**
- 외부 경계 입력(사용자 입력, 파일 I/O, 네트워크, 외부 패키지 API)
- 수명이 다른 오브젝트(파괴될 수 있는 타 MonoBehaviour / 씬 전환 교차 참조)
- 옵셔널로 문서화된 필드
- 구독 해제 전 이벤트 핸들러의 `?.Invoke()` (멀티스레드/해제 경쟁 방지)
- 초기화 실패 감지 목적의 단 1회 가드

**정리 원칙:**
1. 불변식으로 보장되는 참조는 검사하지 않는다
2. null을 기본값으로 쓰지 않는다 — 설계를 바꿔 null 자체를 제거
3. 방어적 early-return은 버그를 숨긴다 — 실패하게 두거나 명시적 예외를 던진다
4. `?.` 남발 금지 — null일 수 없는 경로에서 사용하면 의도가 왜곡된다

---

## ⚙️ 리뷰 프로세스

1. **범위 확정:** 사용자 지정 경로 → 없으면 `Assets/02_Scripts/` 전체 중 최근 변경 파일 우선
2. **수집:** Glob/Grep으로 후보 파일 나열 → Read로 본문 확인
3. **5축 점검:** 각 파일에 대해 축1~축5 체크리스트 적용 (축1/축4/축5는 서로 동반 발생 빈번 — 교차 참조)
4. **심각도 분류:**
   - **필수(Must):** 버그·잠재 누수·라이프사이클 위반 / 카테고리 A / 카테고리 B 도메인 로직 비중 큰 케이스 / 축4 `AddComponent` 폴백·`Find*` 폴백 / 축5 상태 이상 은닉형 null 가드
   - **권장(Should):** 유지보수성·테스트 용이성 저하 / 카테고리 B 소량·참조 영향 큰 케이스 / 축4 `GetComponent` 폴백·기본 new / 축5 반복·방어적 가드
   - **선택(Could):** 스타일·미미한 중복
5. **보고서 작성** → **문서화 위임**

---

## 📤 출력 포맷

### 출력 1 — 리뷰 보고서

```
# 젤리 몰리 코드 점검 보고서 — {YYYY-MM-DD}

## 축 1. MonoBehaviour 최소화 (POCO 전환/분리)
[필수/권장/선택] [카테고리 A/B/C] {파일:라인} — {클래스명}
  근거: {실패 체크리스트 또는 섞여 있는 도메인 로직 위치}
  영향: {참조 위치, prefab/씬 연결 여부, 테스트 가능 범위}
  제안:
    - A(완전 전환): MonoBehaviour 제거 → POCO + 생성자 DI/팩토리
    - B(분리): 분리할 POCO {이름} + Thin MonoBehaviour {이름}
    - C(유지): 유지 근거 + 도메인 로직 혼입 없음 확인

## 축 2. 라이프사이클 본문 1줄 위임
[필수/권장/선택] {파일:라인} — {메서드명}
  현재: {본문 요약 — 여러 함수 직접 나열 또는 로직 직접 포함}
  제안: {라이프사이클 → 위임 메서드 1줄, 위임 메서드 내 세부 함수 목록}

## 축 3. 불필요 static 정리
[필수/권장/선택] {파일:라인} — {멤버명}
  근거: {체크리스트 실패 항목}
  제안: {대체 설계}

## 축 4. 자동 패씽 정리
[필수/권장/선택] {파일:라인} — {패턴: AddComponent폴백/GetComponent폴백/Find폴백/Context폴백/기본new}
  근거: {자동 패씽 시그니처 + 참조 실제 소유자}
  제안: {Inspector 직결/생성자 주입/컨텍스트 주입으로 단일화}

## 축 5. 불필요 null 검사 정리
[필수/권장/선택] {파일:라인} — {가드 종류: 생성 보장/RequireComponent/캐시 직후/POCO new/관성 ?./중복/상태 은닉}
  근거: {null일 수 없음을 보장하는 불변식}
  제안: {가드 삭제 / 초기화 1회 검증으로 대체 / 설계 정정}

## 요약
- 필수 {N}건 / 권장 {N}건 / 선택 {N}건
- 검증 규칙: [UNITY-021, UNITY-036, UNITY-041, ...]
```

### 출력 2 — unity-documenter 위임

보고서 작성 직후 반드시 `unity-documenter`를 호출한다:

```
Agent(
  subagent_type: "unity-documenter",
  model: "opus",
  description: "젤리 코드 점검 수정 가이드 문서화",
  prompt: |
    첨부된 젤리 몰리 코드 점검 보고서를 프로젝트 문서로 정리해 저장하라.

    - 저장 경로: d:/ProjectFiles/JellyMolly/docs/code-review/{YYYY-MM-DD}-jelly-molly-code-review.md
    - 구조: 개요 → 축별 결과 → 각 항목 수정 가이드(현재→목표→절차) → 검증 규칙 인용
    - 각 항목은 반드시 "수정 절차 체크박스"를 포함
    - 참고 규칙: UNITY-NULL-003, UNITY-EXCEPT-006, UNITY-DI-020,
                UNITY-LIFECYCLE-021, UNITY-VALIDATE-022, UNITY-CLASS-036, UNITY-FUNCTION-041

    [여기에 리뷰 보고서 본문을 붙여 넣는다]
)
```

위임 완료 후 문서 저장 경로를 사용자에게 최종 보고한다.

---

## 🔧 사용 도구

| 도구 | 목적 |
|------|------|
| **Glob** | 대상 파일 목록 수집 |
| **Grep** | 패턴별 후보 라인 탐색 |
| **Read** | 파일 본문 확인 |
| **Agent(unity-documenter)** | 보고서 → 문서화 위임 |

---

## ❗ 에러 핸들링

- 규칙 파일 접근 불가 → 경로 재확인 후 1회 재시도, 실패 시 "규칙 미참조 리뷰" 경고와 함께 체크리스트만으로 진행
- 대상 파일 너무 많음 → 폴더 단위로 분할 실행, 보고서를 파트로 분리 출력
- `unity-documenter` 위임 실패 → 보고서만 반환하고 사용자에게 문서화 재시도 의사 확인
- 규칙과 사용자 지시 충돌 → 사용자 지시 우선, 충돌 사실을 보고서 말미에 명시

---

## 🤝 협업

- **입력:** 사용자 / `jelly-hub` / `unity-hub`로부터 점검 요청 수신
- **출력:** 리뷰 보고서 + `unity-documenter`로 위임한 문서 경로
- 필수 수정 항목 포함 시 → 보고서 요약을 `unity-developer` 또는 `jelly-*` 담당에게 인계(선택)
- 성능 관련 지적은 `jelly-optimizer` / `unity-optimizer`로 라벨링

---

## 🔁 후속 작업 행동

- 이전 보고서(`docs/code-review/` 하위)가 존재하면 먼저 Read하여 **미해결 항목**을 이어서 다룬다
- 사용자가 "부분 재점검"(예: "축 2만 다시")을 요청하면 해당 축만 재수행
- 새 입력이 명시되면 이전 보고서를 `_workspace_prev/`로 이동하고 새 점검 실행

---

**생성:** 2026-04-22
**상태:** ✅ 스킬 정의 완료
