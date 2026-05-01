---
name: jelly-architecture
description: >
  JellyMolly 프로젝트의 Entry/Feature/Bus/POCO 패턴 기반 아키텍처 설계 스킬.
  신규 Feature·시스템 설계, MonoBehaviour→POCO 전환 설계, 의존성 경계 정의,
  Bus 메시지 설계, asmdef 재설계 요청 시 사용.
  '젤리 구조 잡아줘', '젤리 Feature 설계', 'MB 전환 설계', '젤리 아키텍처' 요청에 반드시 이 스킬을 사용할 것.
---

# Jelly Architecture — JellyMolly 설계 프로세스

JellyMolly Entry/Feature/Bus/POCO 패턴을 기반으로 구조를 설계하고 설계서를 산출한다.

## 참조 규칙

설계 전 반드시 Read한다. 규칙 파일이 정의이고, 이 스킬은 그 정의를 적용하는 절차다.

| 규칙 ID | 파일 | 적용 시점 |
|---------|------|---------|
| HUB | `C:\DevelopRule\Rule\Unity\Character\HUB.md` | 항상 (캐릭터 설계 진입점) |
| UCA-001 | `UCA-001_Architecture.md` | MVC+Feature 전체 원칙 — 항상 |
| UCA-004 | `UCA-004_Feature.md` | Feature/POCO 기준 — 항상 |
| UCA-006 | `UCA-006_Composition.md` | Entry 조립·Bus·이벤트 계층 — 항상 |
| UCA-002 | `UCA-002_Model.md` | 데이터 모델 설계 시 |
| UCA-003 | `UCA-003_View.md` | View(MonoBehaviour) 설계 시 |
| UCA-005 | `UCA-005_Config.md` | ScriptableObject Config 설계 시 |
| UCA-007 | `UCA-007_Convention.md` | 네이밍·파일 배치 결정 시 |
| UNITY-DI-020 | `unity-csharp-rules\UNITY-DI-020.md` | 의존성 주입 경로 설계 시 |
| UNITY-CLASS-036 | `unity-csharp-rules\UNITY-CLASS-036.md` | POCO 전환 판단 시 |
| UNITY-LIFECYCLE-021 | `unity-csharp-rules\UNITY-LIFECYCLE-021.md` | 라이프사이클 설계 시 |
| UNITY-SOLID-030 | `unity-csharp-rules\UNITY-SOLID-030.md` | 전반적 원칙 검증 시 |

## 실행 흐름

### Step 1: 현황 파악

1. 설계 대상 파일·폴더 Read
2. 기존 Feature 구성·Bus 메시지·asmdef 경계 파악
3. 충돌·순환 참조 위험 지점 식별

### Step 2: 규칙 Read

HUB.md → UCA-001, UCA-004, UCA-006은 항상 Read. 이후 상황별 추가 규칙 Read.

### Step 3: 설계 결정

1. **POCO vs MB 판단** — POCO Feature 전환 or Thin MB 유지
2. **Feature 책임 정의** — 1 Feature = 1 책임 (SRP)
3. **의존성 방향** — 직접 참조 vs Bus 메시지 판단
4. **Bus 메시지 설계** — 필요한 `readonly struct` 메시지 타입 정의
5. **주입 체인** — `JellyPlayer.BuildFeatures()` 변경 포인트 결정
6. **asmdef 영향** — 변경이 asmdef 경계를 넘는지 확인

### Step 4: 설계서 작성 → 저장

`docs/design/{YYYY-MM-DD}-{feature-name}.md`에 저장한다.

## 설계서 포맷

```markdown
# {기능명} 아키텍처 설계

**작성일**: {YYYY-MM-DD}  **설계자**: jelly-architect

## 설계 요약
## 전환 전/후 구조 비교
| 항목 | 전환 전 | 전환 후 |

## 새 클래스 목록
| 클래스명 | 타입 | 책임 |

## 인터페이스·생성자 시그니처
```csharp
// 시그니처만 — 구현 본문 금지
```

## 의존성 그래프
## Bus 메시지 (신규)
## BuildFeatures() 변경 포인트
## asmdef 영향
## 적용 규칙
```

## 에러 핸들링

| 상황 | 처리 |
|------|------|
| 기존 코드와 구조 충돌 | 영향 범위 보고 후 단계적 전환 경로 제안 |
| asmdef 순환 발견 | 파일 이동 대상 명시 + 대안 네임스페이스 제시 |
| SerializeField 대체 불가 | Thin MB 유지 설계로 전환, 이유 명시 |
| 규칙 파일 접근 불가 | 경고 후 내장 지식 기반 진행, 설계서 말미에 "규칙 미참조" 표시 |
