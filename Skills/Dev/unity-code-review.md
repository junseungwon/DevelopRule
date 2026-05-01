# Unity 코드 리뷰 스킬

## 스킬 정의

| 속성 | 값 |
|------|-----|
| 스킬명 | unity-code-review |
| 역할 | Unity C# 코드 리뷰 전 규칙 참조·리뷰 절차 정의 |
| 사용 에이전트 | unity-code-reviewer, code-reviewer, rules-reviewer |
| 참조 규칙 | `C:\DevelopRule\Rule\unity-csharp-rules\`, `C:\DevelopRule\Rule\Unity\Character\` |

---

## 리뷰 전 필수 절차 (생략 금지)

### 1단계 — 허브 로드

반드시 Read: `C:\DevelopRule\Rule\unity-csharp-rules\HUB.md`

### 2단계 — 리뷰 대상 유형별 규칙 ID 매칭

리뷰 대상 코드의 작업 유형에 따라 규칙 ID를 매칭한다.

| 리뷰 대상 유형 | 필수 규칙 IDs |
|-------------|-------------|
| MonoBehaviour 클래스 | 036, 021, 013, 007, 017 |
| POCO / 도메인 로직 | 036, 030, 039, 020 |
| 공용 API·인터페이스 | 002, 037, 039, 003 |
| 비동기·코루틴 | 001, 031, 010, 006 |
| 성능 핫패스 | 023, 014, 034, 011, 027 |
| 상태·데이터 구조 | 042, 038, 017, 007 |
| 싱글톤·의존성 | 016, 020, 030 |
| null 처리·예외 | 003, 006, 022, 028 |
| 네이밍·주석 | 005, 008, 040, 041 |

### 3단계 — 해당 규칙 파일 Read

매칭된 각 규칙 ID의 파일을 Read한다.
경로: `C:\DevelopRule\Rule\unity-csharp-rules\UNITY-<TOPIC>-<NNN>.md`

### 4단계 — 캐릭터 코드 리뷰 시 추가 절차

캐릭터·Feature 코드 리뷰 시:
반드시 Read: `C:\DevelopRule\Rule\Unity\Character\HUB.md` → 해당 UCA-*.md Read

### 5단계 — 리뷰 보고서 작성

보고서 말미에 반드시 명시:
```
검증 규칙: [UNITY-003, UNITY-021, UCA-004, ...]
```

---

## 리뷰 체크리스트

### 5축 점검 기준

#### 축 1. MonoBehaviour 최소화 (UNITY-036, UCA-001)
- [ ] Unity API 없이 도메인 로직만 있는 MB → POCO 전환 후보
- [ ] 도메인 로직이 MB에 섞인 경우 → Fat MB → Thin MB + POCO 분리 후보
- [ ] Unity 메시지 수신 필수, Inspector 직결 필수인 경우만 MB 유지

#### 축 2. 라이프사이클 본문 1줄 위임 (UNITY-021)
- [ ] Awake/Start/OnEnable/OnDisable/OnDestroy/Update/FixedUpdate 본문 2줄 이상
- [ ] 단일 위임 메서드 없이 여러 함수 직접 나열
- [ ] if/for 등 제어문 직접 포함

#### 축 3. 불필요 static 정리
- [ ] 상태를 가진 static 필드 (싱글톤 위장 전역 상태)
- [ ] 한 곳에서만 쓰이는 static 메서드
- [ ] 테스트 격리를 막는 static 상태

#### 축 4. 자동 패씽 식별 (UNITY-020)
- [ ] `if (field == null) field = GetComponent<T>();` 폴백
- [ ] `if (field == null) field = AddComponent<T>();` (최강 위반)
- [ ] `FindObjectOfType<T>()` 계열 사용
- [ ] `if (field == null) field = new T();` 무조건 기본 생성

#### 축 5. 불필요 null 검사 (UNITY-003, UNITY-006)
- [ ] 생성자·Init 주입 후 읽기 전용 필드에 반복 null 가드
- [ ] RequireComponent 보장 컴포넌트에 null 검사
- [ ] new로 직접 생성한 POCO에 `?.` / null 가드
- [ ] 조용한 early-return으로 버그 은닉

---

## 리뷰 보고서 형식

```
[필수/권장/선택] {규칙ID} | {파일명:라인}
  이유: {위반 근거}
  개선: {구체적 수정 방법 또는 코드 예시}
```

심각도 분류:
- 필수(Must): 버그·메모리 누수·라이프사이클 위반·POCO 전환 대상·AddComponent/Find 폴백·상태 은닉 null 가드
- 권장(Should): 유지보수성·테스트 용이성 저하·GetComponent 폴백·반복 방어 null 가드
- 선택(Could): 스타일·미미한 중복

---

## 에러 핸들링

| 상황 | 처리 |
|------|------|
| 규칙 파일 접근 불가 | 경로 재확인 후 1회 재시도. 실패 시 체크리스트 기반으로만 진행, "규칙 미참조" 경고 |
| 설계서 없이 리뷰 요청 | 코드 품질 기준만 리뷰하고 한계 명시 |
| 리뷰 범위 초과 | 섹션별 분할 실행 |

---

생성: 2026-05-01
