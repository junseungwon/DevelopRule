# Unity 4축 코드 점검 스킬

## 스킬 정의

| 속성 | 값 |
|------|-----|
| 스킬명 | unity-4axis-inspect |
| 역할 | 4축 점검 기준·프로세스·출력 포맷 정의 |
| 사용 에이전트 | unity-code-inspector |
| 참조 규칙 | `C:\DevelopRule\Rule\unity-csharp-rules\` |

---

## 점검 전 필수 절차

1. `C:\DevelopRule\Rule\unity-csharp-rules\HUB.md` Read
2. 핵심 규칙 파일 Read:

| 규칙 ID | 파일명 | 점검 축 |
|--------|--------|--------|
| UNITY-003 | UNITY-NULL-003.md | 축 1: null 제거 원칙 |
| UNITY-006 | UNITY-EXCEPT-006.md | 축 1: 명시적 실패 vs 조용한 early-return |
| UNITY-041 | UNITY-FUNCTION-041.md | 축 2: 함수 단위 책임 분리 |
| UNITY-021 | UNITY-LIFECYCLE-021.md | 축 2: 라이프사이클 본문 함수화 |
| UNITY-008 | UNITY-COMMENT-008.md | 축 3: 주석 기준 |
| UNITY-040 | UNITY-VARIABLE-040.md | 축 4: 변수 스코프·초기화 |
| UNITY-011 | UNITY-LINQ-011.md | 축 4: LINQ 캐싱 |

---

## 4축 상세 점검 기준

---

### 축 1. null 검사 제거

**핵심 원칙:**
> null이 발생하면 오류다. null 검사로 오류를 숨기지 않는다.
> 내부 코드 경로에서 null은 설계 결함 — 검사 대신 근본 원인을 수정한다.

**제거 대상 시그니처:**

```csharp
// 제거 대상 A — early return null 가드
if (_component == null) return;
if (_component == null) return false;

// 제거 대상 B — null conditional로 조용히 삼키기
_component?.DoSomething();
value = _field?.Property ?? defaultValue;

// 제거 대상 C — LogError로 경고 후 통과
if (_component == null) { Debug.LogError("..."); return; }

// 제거 대상 D — null coalescing 폴백
_reference = _reference ?? GetComponent<T>();

// 제거 대상 E — 삼항 null 검사
var result = _obj != null ? _obj.Value : fallback;
```

**수정 방향:**
- Inspector 직결(`[SerializeField]`)이면 null 검사 자체를 삭제
- `RequireComponent`가 보장하면 검사 삭제
- `Awake`/`Init`에서 주입 후 절대 null이 아닌 참조면 검사 삭제
- null이 발생할 수 있는 구조면 구조를 바꿔 null 자체를 제거

**예외 (유지 가능):**
- 외부 API / 파일 IO / 네트워크 반환값
- 씬 전환 중 파괴될 수 있는 타 MonoBehaviour
- 이벤트 핸들러 `?.Invoke()` (멀티스레드 해제 경쟁)
- 옵셔널로 명시적 설계된 필드

**심각도:** 내부 코드 경로 null 가드 → 필수(Must)

---

### 축 2. 함수 분리

**핵심 원칙:**
> 코드 블록은 의미 단위로 추출해 이름 있는 함수로 만든다.
> 함수 이름이 주석을 대체한다. 함수 1개 = 책임 1개.

**분리 대상 시그니처:**

```csharp
// 대상 A — 라이프사이클 메서드 본문에 로직 직접 나열
private void Awake()
{
    _rb = GetComponent<Rigidbody>();
    _collider = GetComponent<Collider>();
    _animator = GetComponent<Animator>();
    _speed = _baseSpeed * _multiplier;
    // ... 5줄 이상
}

// 대상 B — 10줄 이상의 단일 메서드
private void Update()
{
    // 입력 처리
    var input = ...;
    // 이동 계산
    var move = ...;
    // 애니메이션 갱신
    // ... 길어지는 로직
}

// 대상 C — 여러 책임을 동시에 처리하는 함수
private void ProcessAll()
{
    HandleInput();
    MoveCharacter();
    UpdateAnimation();
    CheckCollisions();
    // 4가지 이상의 책임
}
```

**수정 방향:**

```csharp
// 라이프사이클 → 1줄 위임
private void Awake() => Init();

private void Init()
{
    CacheComponents();
    InitializeState();
}

private void CacheComponents()
{
    _rb = GetComponent<Rigidbody>();
    _collider = GetComponent<Collider>();
    _animator = GetComponent<Animator>();
}

// Update → 단일 위임
private void Update() => Tick();

private void Tick()
{
    HandleInput();
    ApplyMovement();
    UpdateVisuals();
}
```

**분리 기준:**
- 라이프사이클 메서드 본문 2줄 이상 → 위임 메서드 1개로 추출
- 일반 함수 10줄 이상 → 책임 단위로 분리 검토
- 주석으로 구획 나누기(`// --- 이동 ---`) → 함수 분리 신호

**심각도:** 라이프사이클 본문 직접 나열 → 필수(Must) / 긴 일반 함수 → 권장(Should)

---

### 축 3. 불필요 주석 제거

**핵심 원칙:**
> 코드 자체가 의도를 전달한다. 주석은 WHY가 비자명한 경우에만 존재한다.
> 코드를 설명하는 주석은 코드로 대체한다.

**제거 대상:**

```csharp
// 제거 대상 A — 코드를 그대로 반복하는 주석
// speed를 계산한다
float speed = distance / time;

// 제거 대상 B — 함수 이름으로 충분한 설명
// 플레이어를 이동시킨다
MovePlayer();

// 제거 대상 C — 주석 처리된 코드 블록
// _oldMethod();
// if (legacyMode) { ... }

// 제거 대상 D — 구획 구분 주석 (함수로 대체해야 함)
// ========== 이동 로직 ==========
// ========== 초기화 ==========

// 제거 대상 E — 의미 없는 태그
// TODO: 나중에 수정
// FIXME: 임시 코드 (기한 없음)
// 임시 처리

// 제거 대상 F — 닫는 중괄호 주석
} // end Update
} // end class
```

**유지 가능한 주석:**
- 숨겨진 제약사항 설명 (`// Unity 특정 버전 버그 우회`)
- 반직관적 동작 이유 (`// 물리엔진 연산 순서상 1프레임 지연 의도적`)
- 복잡한 알고리즘 개요 (단, 1줄)

**심각도:** 주석 처리된 코드 블록 → 필수(Must) / 반복 설명 주석 → 권장(Should)

---

### 축 4. 루프 내 변수 캐싱

**핵심 원칙:**
> Update/FixedUpdate/LateUpdate 등 루프에서 반복 조회·계산하는 참조는 필드로 캐싱한다.
> 프레임마다 GC 할당하거나 컴포넌트 조회하는 코드는 성능 문제다.

**캐싱 대상 시그니처:**

```csharp
// 대상 A — Update 내 GetComponent
private void Update()
{
    var rb = GetComponent<Rigidbody>();  // 매 프레임 조회
    rb.velocity = Vector3.zero;
}

// 대상 B — Camera.main 반복 접근
private void Update()
{
    var ray = Camera.main.ScreenPointToRay(Input.mousePosition);  // 매 프레임
}

// 대상 C — transform 반복 접근 (자기 자신이 아닌 타겟)
private void Update()
{
    var pos = _target.transform.position;  // _target.transform → 캐싱
}

// 대상 D — LINQ in Update
private void Update()
{
    var active = _enemies.Where(e => e.IsAlive).ToList();  // 매 프레임 할당
}

// 대상 E — Find 계열 반복 호출
private void Update()
{
    var player = GameObject.FindWithTag("Player");  // 매 프레임 탐색
}

// 대상 F — string 변환 반복
private void Update()
{
    _text.text = _score.ToString();  // 값 변경 시에만 갱신해야 함
}
```

**수정 방향:**

```csharp
public sealed class EnemyController : MonoBehaviour
{
    // 캐싱된 필드
    private Rigidbody _rb;
    private Camera _mainCamera;
    private Transform _targetTransform;

    private void Awake() => Init();

    private void Init()
    {
        _rb = GetComponent<Rigidbody>();
        _mainCamera = Camera.main;
    }

    public void SetTarget(Transform target)
    {
        _targetTransform = target;
    }

    private void Update() => Tick();

    private void Tick()
    {
        ApplyVelocity();
        TrackTarget();
    }

    private void ApplyVelocity() => _rb.velocity = Vector3.zero;
    private void TrackTarget() { /* _targetTransform 사용 */ }
}
```

**캐싱 기준:**
- `GetComponent<T>()` in 루프 → Awake에서 캐싱
- `Camera.main` in 루프 → 필드 캐싱
- 타 오브젝트의 `.transform` in 루프 → 참조 캐싱
- LINQ in 루프 → 조건 변경 시에만 갱신하는 캐시 도입 또는 리팩터링
- `Find*` in 루프 → 완전 금지, 주입/캐싱으로 교체

**심각도:** `GetComponent`/`Find*`/LINQ in Update → 필수(Must) / `Camera.main`/타겟 transform → 권장(Should)

---

## 리뷰 프로세스

1. **범위 확정:** 사용자 지정 경로 → 없으면 `Assets/02_Scripts/` 최근 변경 파일 우선
2. **수집:** Glob으로 `.cs` 파일 → Grep으로 각 축 패턴 탐색 → Read로 본문 확인
3. **4축 점검:** 각 파일에 축1~축4 체크리스트 적용
4. **심각도 분류:**
   - 필수(Must): 성능 문제·버그·오류 은폐
   - 권장(Should): 가독성·유지보수성 저하
   - 선택(Could): 스타일·미미한 중복
5. **모드 분기:**
   - 수정 모드 → 직접 파일 수정 후 변경 요약
   - 보고 모드 → 보고서 작성 후 `unity-documenter` 위임

---

## Grep 패턴 (빠른 탐지)

```
# 축 1: null 검사
== null.*return
!= null.*return
\?\. 
?? 
Debug\.LogError.*null

# 축 2: 라이프사이클 본문 길이
private void Awake\(\)
private void Start\(\)
private void Update\(\)

# 축 3: 불필요 주석
^[\s]*//.+
// TODO
// FIXME
// end

# 축 4: 루프 내 조회
GetComponent.*Update
Camera\.main
FindWithTag
FindObjectOfType
\.Where\(
\.ToList\(
```

---

## 출력 포맷

```
# Unity 코드 점검 보고서 — {YYYY-MM-DD}

## 축 1. null 검사 제거
[필수/권장] {파일:라인} — {가드 종류}
  현재: {null 검사 코드}
  문제: {null이 발생하는 근본 원인}
  수정: {검사 제거 + 구조 수정 방향}

## 축 2. 함수 분리
[필수/권장] {파일:라인} — {메서드명}
  현재: {긴 본문 요약}
  수정: {추출할 함수 목록}

## 축 3. 불필요 주석 제거
[필수/권장] {파일:라인} — {주석 종류}
  현재: {주석 내용}
  수정: 제거 / 함수명으로 대체

## 축 4. 루프 내 변수 캐싱
[필수/권장] {파일:라인} — {반복 접근 대상}
  현재: {루프 내 조회 코드}
  수정: {캐싱 필드명 + 초기화 위치}

## 요약
- 필수 {N}건 / 권장 {N}건 / 선택 {N}건
- 검증 규칙: [UNITY-003, UNITY-006, UNITY-008, UNITY-011, UNITY-021, UNITY-040, UNITY-041]
```

---

생성: 2026-06-05
