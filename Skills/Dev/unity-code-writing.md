# Unity C# 코드 작성 스킬

## 스킬 정의

| 속성 | 값 |
|------|-----|
| 스킬명 | unity-code-writing |
| 역할 | Unity C# 스크립트 작성 전 규칙 참조·적용 절차 정의 |
| 사용 에이전트 | unity-developer, jelly-system, jelly-feature, jelly-physics, jelly-interaction, jelly-input, jelly-state, jelly-visual |
| 참조 규칙 | `C:\DevelopRule\Rule\unity-csharp-rules\` |

---

## 코드 작성 전 필수 절차 (생략 금지)

### 1단계 — 허브 로드

반드시 Read: `C:\DevelopRule\Rule\unity-csharp-rules\HUB.md`

허브의 "작업 상황 → 규칙 ID" 매핑 테이블을 확인한다.

### 2단계 — 작업 유형별 규칙 ID 매칭

아래 표를 기준으로 현재 작업에 해당하는 규칙 ID를 선택한다.

| 작업 상황 | 필수 규칙 IDs |
|----------|-------------|
| 새 클래스/구조체/Enum 작성 | 036, 042, 038, 030, 005 |
| MonoBehaviour 작성 | 036, 021, 013, 007, 017 |
| 공용 API·프로퍼티 작성 | 002, 037, 039, 008, 003 |
| 함수·변수·네이밍 | 041, 040, 005, 015 |
| 비동기·스레드 코드 | 001, 031, 010, 006 |
| 예외·입력·복구 처리 | 006, 003, 022, 028, 018 |
| 컬렉션·LINQ·메모리 | 019, 011, 014, 004 |
| 성능·풀링·프로파일 | 034, 023, 014, 027 |
| GameObject·Tag/Layer | 013, 035, 021 |
| 싱글톤·DI·이벤트 | 016, 020, 017 |
| 리소스·파일 I/O | 009, 006, 001 |
| Editor·플랫폼 분기 | 033, 026, 007, 025 |
| 테스트 코드 | 024, 020, 036 |
| 로그·주석 | 012, 008, 029 |
| 네트워크 | 032, 010, 006, 001 |
| Reflection | 027, 014, 023 |

### 3단계 — 해당 규칙 파일 Read

선택된 각 ID에 대응하는 파일을 Read한다.

경로 패턴: `C:\DevelopRule\Rule\unity-csharp-rules\UNITY-<TOPIC>-<NNN>.md`

규칙 ID 색인:
```
001 ASYNC       002 API         003 NULL        004 GENERIC     005 NAMING
006 EXCEPT      007 SERIAL      008 COMMENT     009 RESOURCE    010 THREAD
011 LINQ        012 LOG         013 GAMEOBJ     014 MEMORY      015 CONST
016 SINGLETON   017 EVENT       018 CAST        019 COLLECTION  020 DI
021 LIFECYCLE   022 VALIDATE    023 PROFILE     024 TEST        025 CONFIG
026 CONDITIONAL 027 REFLECT     028 RECOVERY    029 ANNOTATION  030 SOLID
031 TASK        032 NETWORK     033 EDITOR      034 POOL        035 MARKER
036 CLASS       037 PROPERTY    038 ENUM        039 INTERFACE   040 VARIABLE
041 FUNCTION    042 STRUCT
```

### 4단계 — 캐릭터 코드 작업 시 추가 절차

캐릭터·Feature 관련 코드 작성 시 아래 절차를 추가로 수행한다.

반드시 Read: `C:\DevelopRule\Rule\Unity\Character\HUB.md`

| 작업 상황 | 필수 UCA 규칙 IDs |
|----------|-----------------|
| 새 캐릭터 시스템 설계·클래스 배치 | UCA-001, UCA-006 |
| MB인지 POCO인지 판단 | UCA-001 |
| Model 클래스 작성 | UCA-002 |
| View(MonoBehaviour) 작성 | UCA-003 |
| Feature(기능 모듈) 작성 | UCA-004 |
| ScriptableObject 작성 | UCA-005 |
| Entry(Composition Root) 작성 | UCA-006 |
| Feature 간 통신 설계 | UCA-006 |
| 네이밍·폴더·어셈블리 결정 | UCA-007 |

경로 패턴: `C:\DevelopRule\Rule\Unity\Character\UCA-00X_*.md`

### 5단계 — 구현 완료 후 보고

구현 완료 후 결과 보고 말미에 반드시 명시한다:
```
적용 규칙: [UNITY-021, UNITY-036, UCA-004, ...]
```

---

## 핵심 코드 원칙 (규칙 요약)

- MonoBehaviour는 얇게 유지. 도메인 로직은 Plain C#(POCO)으로 분리 (036)
- 라이프사이클 메서드 본문은 1줄 위임 원칙 (021)
- null 체크는 경계에서만, 방어적 null 가드 금지 (003)
- 싱글톤은 최후의 수단, DI 컨테이너·매니저 경유 우선 (016, 020)
- 핫패스에서 LINQ·string 연결·new·Reflection 금지 (011, 027)
- 주석은 Why만 기재, What은 금지 (008)
- 수치는 ScriptableObject 또는 Config로 관리, 하드코딩 금지 (025)
- GameObject·Component 접근은 캐싱 (013, 014)

---

## 에러 핸들링

| 상황 | 처리 |
|------|------|
| 규칙 파일 접근 불가 | 경로 재확인 후 1회 재시도. 실패 시 내장 지식 기반 진행, 보고서 말미에 "규칙 미참조" 표기 |
| 규칙 간 충돌 | 우선순위(critical > high > medium) 기준 적용 후 충돌 사실 보고 |
| 설계와 규칙 충돌 | 규칙 우선. 충돌 사실을 architect에 보고 |

---

생성: 2026-05-01
