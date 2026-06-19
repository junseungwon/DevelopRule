# Molly Team — 공용 에이전트 목록
> JellyMooly 프로젝트에서 사용하는 공용·Unity 에이전트 / 최종 업데이트: 2026-05-01

---

## 개요

몰리 팀 전담 에이전트(AGENTS.md) 외에 아래 공용·Unity 에이전트를 함께 활용한다.
CTO가 dev-lead를 통해 적절한 에이전트를 선발하여 배치한다.

---

## 관리 에이전트 (Management)

| 에이전트 | 파일 | 용도 |
|---|---|---|
| CEO | `C:\DevelopRule\agents\management\ceo.md` | 사용자 요청 수신 → CTO/인사계 위임 |
| CTO | `C:\DevelopRule\agents\management\cto.md` | 기술 총괄, 시스템 설계 주도, 팀 배치 |
| dev-lead | `C:\DevelopRule\agents\management\dev-lead.md` | 팀원 인솔, 세부 작업 분배, 진행 추적 |
| code-review-lead | `C:\DevelopRule\agents\management\code-review-lead.md` | 코드 점검 총괄, 리뷰 팀 배치 |

---

## Unity 팀 에이전트 (Unity Team)

| 에이전트 | 파일 | 용도 | 몰리 프로젝트 적용 상황 |
|---|---|---|---|
| unity-architect | `C:\DevelopRule\agents\unity-team\architect.md` | 시스템 구조·의존성·패턴 설계 | UCA 규칙 적용 아키텍처 설계 시 (몰리 에이전트 설계 전 선행 검토) |
| unity-developer | `C:\DevelopRule\agents\unity-team\developer.md` | C# 스크립트 구현, 버그 수정, 리팩터링 | 몰리 에이전트 구현 보조 또는 범용 Unity 코드 작성 시 |
| unity-designer | `C:\DevelopRule\agents\unity-team\designer.md` | 씬 구성, UI 레이아웃, prefab 배치 | Bootstrap/MainHome/Tutorial 씬 초기 배치 |
| unity-code-reviewer | `C:\DevelopRule\agents\unity-team\code-reviewer.md` | Unity C# 코드 품질 점검 | 각 Wave 완료 후 코드 리뷰 |
| unity-optimizer | `C:\DevelopRule\agents\unity-team\optimizer.md` | 성능 프로파일링, GC 최적화, 배칭 | 알파/베타 단계 성능 목표 달성 |
| unity-qa | `C:\DevelopRule\agents\unity-team\qa.md` | 기능 검증, 회귀 테스트 | Wave별 완료 후 기능 검증 |
| unity-documenter | `C:\DevelopRule\agents\unity-team\documenter.md` | API 문서, 주석 가이드 | 시스템 완성 후 문서화 |
| unity-researcher | `C:\DevelopRule\agents\unity-team\researcher.md` | 기술 조사, Unity 패키지 비교 | Spine vs DOTween 등 기술 선택 조사 |
| unity-lead-planner | `C:\DevelopRule\agents\unity-team\lead-planner.md` | 기능 스펙 정의, 우선순위 결정 | 신규 기능 추가 전 스펙 확정 |
| unity-detail-planner | `C:\DevelopRule\agents\unity-team\detail-planner.md` | 세부 동작 기획, 에지케이스 정의 | 구현 전 세부 기획 작성 |
| unity-sample-scene | `C:\DevelopRule\agents\unity-team\sample-scene.md` | 검증용 씬 생성 | 각 시스템 단독 테스트 씬 |
| unity-file-structure-manager | `C:\DevelopRule\agents\unity-team\file-structure-manager.md` | 프로젝트 폴더 구조 관리 | 신규 스크립트 파일 위치 확인 |

---

## 공용 에이전트 (Public Agents)

| 에이전트 | 파일 | 용도 | 몰리 프로젝트 적용 상황 |
|---|---|---|---|
| documenter | `C:\DevelopRule\agents\public-agents\documenter.md` | 범용 문서화 | 설계 문서, API 레퍼런스 생성 |
| code-reviewer | `C:\DevelopRule\agents\public-agents\code-reviewer.md` | 범용 코드 리뷰 | Unity 독립 로직 리뷰 (POCO, 이벤트 구조) |
| visualizer | `C:\DevelopRule\agents\public-agents\visualizer.md` | 다이어그램 생성 (Mermaid 등) | 의존성 그래프, 씬 전환 다이어그램 |
| researcher | `C:\DevelopRule\agents\public-agents\researcher.md` | 기술 조사, 문서 분석 | Unity IAP, Firebase, Spine 최신 버전 조사 |
| file-structure-manager | `C:\DevelopRule\agents\public-agents\file-structure-manager.md` | 파일·폴더 구조 관리 | 프로젝트 폴더 정리, 파일 이동 |
| file-creator | `C:\DevelopRule\agents\public-agents\file-creator.md` | 파일 생성 | 새 스크립트 파일 생성 시 |
| git-push | `C:\DevelopRule\agents\public-agents\git-push.md` | git 커밋·푸시 | 각 Wave 완료 후 커밋 |
| feedback-logger | `C:\DevelopRule\agents\public-agents\feedback-logger.md` | 실수·피드백 자동 기록 | 구현 오류, 누락 항목 기록 |

---

## 에이전트 선택 가이드

### 요청별 최적 에이전트

| 요청 상황 | 우선 에이전트 | 보조 에이전트 |
|---|---|---|
| 새 시스템 구조 설계 | unity-architect | molly 전담 에이전트 |
| 기존 시스템 버그 수정 | molly 전담 에이전트 | unity-developer |
| Wave 완료 후 리뷰 | unity-code-reviewer | code-reviewer (공용) |
| 성능 문제 | unity-optimizer | jelly-optimizer |
| 씬 구성/배치 | unity-designer | molly-ui-agent |
| 기술 스택 결정 (라이브러리 선택 등) | unity-researcher | researcher |
| 문서화 | unity-documenter | documenter |
| 기능 스펙 불명확 | unity-lead-planner → unity-detail-planner | - |
| 신규 파일 생성 | file-structure-manager | file-creator |
| Wave 완료 커밋 | git-push | - |
| 실수/누락 기록 | feedback-logger | - |

---

## jelly-team 에이전트 (재사용 가능)

몰리 프로젝트에서 필요 시 jelly-team 에이전트를 추가 활용할 수 있다.

| 에이전트 | 파일 | 몰리 프로젝트 적용 상황 |
|---|---|---|
| jelly-architect | `C:\DevelopRule\agents\jelly-team\architect.md` | UCA Entry/Feature/Bus/POCO 구조 설계 시 |
| jelly-visual | `C:\DevelopRule\agents\jelly-team\visual.md` | WobbleShader 심화 설계 (Wobble 전문성 필요 시) |
| jelly-optimizer | `C:\DevelopRule\agents\jelly-team\optimizer.md` | 몰리 캐릭터 SoftBody·셰이더 특화 최적화 |
| jelly-code-reviewer | `C:\DevelopRule\agents\jelly-team\code-reviewer.md` | MonoBehaviour→POCO 전환 리뷰 |

**주의**: jelly-team은 젤리 캐릭터 전문이므로, 몰리 시스템(Save/Economy/UI)은 molly-team 에이전트를 우선 사용.

---

## 개발 단계별 에이전트 활용 계획

### 프로토타입 단계 (2~3주)

| 목표 | 담당 에이전트 |
|---|---|
| 폴더 구조 초기화 | unity-file-structure-manager |
| Bootstrap/MainHome 씬 배치 | unity-designer |
| SaveSystem 구현 | molly-save-agent |
| StatusSystem 구현 | molly-status-agent |
| MollyCore 구현 | molly-core-agent |
| 리뷰 | unity-code-reviewer |
| 커밋 | git-push |

### 알파 단계 (4~6주)

| 목표 | 담당 에이전트 |
|---|---|
| GrowthSystem 구현 | molly-core-agent |
| EconomySystem/HeartSystem 구현 | molly-economy-agent |
| 버블팡 구현 | minigame-architect → minigame-bubblepop |
| InteractionSystem 구현 | molly-interaction-agent |
| MainHomeUI 구현 | molly-ui-agent |
| WobbleShader + Sprite Atlas | molly-visual-agent |
| 성능 측정 | unity-optimizer |
| 리뷰 | unity-code-reviewer |

### 베타 단계 (4~6주)

| 목표 | 담당 에이전트 |
|---|---|
| MemoryMatch 구현 | minigame-memorymatch |
| JellySlide 구현 | minigame-jellyslide |
| MonetizationSystem (IAP/광고) | molly-economy-agent |
| NotificationManager | molly-save-agent |
| 오프라인 처리 완성 | molly-status-agent + molly-save-agent |
| 전체 QA | unity-qa |
| 문서화 | unity-documenter + documenter |

### 출시 준비 (2주)

| 목표 | 담당 에이전트 |
|---|---|
| 성능 최적화 | unity-optimizer + jelly-optimizer |
| 최종 코드 리뷰 | unity-code-reviewer + jelly-code-reviewer |
| Analytics 설정 | researcher (Firebase 문서 조사) |
| 최종 QA | unity-qa |
