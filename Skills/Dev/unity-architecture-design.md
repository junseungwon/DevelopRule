# Unity 아키텍처 설계 스킬

## 스킬 정의

| 속성 | 값 |
|------|-----|
| 스킬명 | unity-architecture-design |
| 역할 | Unity 시스템 아키텍처 설계 전 규칙 참조·설계 원칙 적용 절차 정의 |
| 사용 에이전트 | unity-architect, jelly-architect |
| 참조 규칙 | `C:\DevelopRule\Rule\unity-csharp-rules\`, `C:\DevelopRule\Rule\Unity\Character\` |

---

## 설계 전 필수 절차 (생략 금지)

### 1단계 — 허브 로드

반드시 Read (순서대로):
1. `C:\DevelopRule\Rule\unity-csharp-rules\HUB.md`
2. `C:\DevelopRule\Rule\Unity\Character\HUB.md` (캐릭터/Feature 시스템 설계 시)

### 2단계 — 설계 핵심 규칙 Read

아키텍처 설계 시 반드시 참조할 규칙:

| 규칙 ID | 파일명 | 설계 결정에서의 역할 |
|--------|--------|-------------------|
| UNITY-030 | UNITY-SOLID-030.md | SOLID 원칙 전반 — 모든 설계의 근거 |
| UNITY-036 | UNITY-CLASS-036.md | MonoBehaviour vs POCO 판단 기준 |
| UNITY-039 | UNITY-INTERFACE-039.md | 인터페이스 경계 정의 |
| UNITY-020 | UNITY-DI-020.md | 의존성 주입 전략 |
| UNITY-016 | UNITY-SINGLETON-016.md | 싱글톤 사용 제한 기준 |
| UNITY-021 | UNITY-LIFECYCLE-021.md | 라이프사이클 설계 원칙 |
| UCA-001 | UCA-001_Architecture.md | MVC+Feature 전체 원칙 (캐릭터) |
| UCA-004 | UCA-004_Feature.md | POCO Feature / ITickableFeature 기준 |
| UCA-006 | UCA-006_Composition.md | Entry 조립 / Bus / 도메인 이벤트 계층 |

### 3단계 — 작업 유형별 추가 규칙

| 설계 상황 | 추가 참조 규칙 |
|----------|-------------|
| 성능 크리티컬 경로 포함 | UNITY-023, UNITY-014, UNITY-034 |
| 비동기 설계 | UNITY-001, UNITY-031, UNITY-010 |
| 이벤트 버스 설계 | UNITY-017, UNITY-020 |
| 데이터 구조 설계 | UNITY-042, UNITY-038, UNITY-019 |

### 4단계 — 설계서 작성

설계서 말미에 반드시 명시한다:
```
적용 규칙: [UNITY-030, UNITY-036, UCA-001, UCA-006, ...]
```

---

## 설계 원칙 (규칙 기반)

### 계층 분리 원칙

```
Entry (MonoBehaviour, Composition Root)
  ↓ 주입
Feature (POCO, 도메인 로직)
  ↓ 호출
View (MonoBehaviour, Unity API 연결)
```

- Entry: BuildFeatures()에서 모든 의존성 조립
- Feature: Unity API 직접 사용 금지. View 메서드 호출로 위임
- View: 표현·연결 전용. 도메인 로직 포함 금지

### MB vs POCO 판단 기준

```
Unity 라이프사이클 콜백 필요? → MB 필수
Inspector 직접 참조 필요?     → MB 유지 검토
위 두 조건 모두 없음?         → POCO 전환
```

### 의존성 방향 원칙

- 단방향 의존성 유지. 순환 참조 금지
- Feature 간 직접 참조 금지 → EventBus 경유
- 외부 이벤트와 내부 메시지 계층 분리

### asmdef 경계 (JellyMolly 기준)

```
Core asmdef (MessageBus, 도메인 이벤트)
    ↑
View asmdef (MonoBehaviour View)
    ↑
Jelly asmdef (IJellyFeature, Feature 구현체)
    ↑
Editor asmdef (SceneBuilder, 에디터 툴)
```

---

## 설계 산출물 형식

설계 문서는 다음 항목을 포함해야 한다:

1. 전환 전/후 구조 비교표 (신규 시스템이면 설계 근거)
2. 새 클래스 목록 + 역할 한 줄 설명
3. 의존성 그래프 (텍스트 다이어그램)
4. 인터페이스·생성자 시그니처 (뼈대만, 구현 코드 금지)
5. 주입 체인 (BuildFeatures 또는 팩토리 변경 포인트)
6. 적용 규칙 목록

---

## 에러 핸들링

| 상황 | 처리 |
|------|------|
| 기존 코드와 구조 충돌 | 영향 범위 보고 후 단계적 전환 경로 제안 |
| asmdef 순환 발견 | 파일 이동 대상 명시 + 대안 네임스페이스 제시 |
| 기획 모순 발견 | detail-planner에 반려 |
| 규칙 파일 접근 불가 | 경고 후 내장 지식 기반으로 진행, "규칙 미참조" 표기 |

---

생성: 2026-05-01
