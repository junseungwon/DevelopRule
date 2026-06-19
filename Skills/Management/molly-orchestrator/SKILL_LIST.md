---
name: molly-orchestrator
type: skill
category: Management
status: list-only
created: 2026-05-01
note: 실제 SKILL.md 구현은 추후 작성. 이 파일은 목록/설계 메모 전용.
---

# Molly Orchestrator — 스킬 목록

몰리 키우기(Molly: Raise Your Jelly) 에이전트 팀을 조율하는 오케스트레이터 스킬.
SAD v1.0 기반 Wave 방식 병렬 구현을 관리한다.

## 스킬 식별

| 항목 | 값 |
|------|---|
| 스킬 파일 경로 | `C:\DevelopRule\skills\Management\molly-orchestrator\SKILL.md` (미구현) |
| 트리거 에이전트 | Claude Code 메인 (사용자 요청 수신 후 직접 실행) |
| 사용 에이전트 | 아래 11개 molly-team 에이전트 전체 |

## 담당 에이전트 목록 (11개)

| 에이전트 | 파일 | Wave | 역할 |
|---------|------|------|------|
| molly-save-agent | `C:\DevelopRule\agents\molly-team\molly-save-agent.md` | 1 | SaveSystem, OfflineCareCalc, NotificationManager |
| molly-status-agent | `C:\DevelopRule\agents\molly-team\molly-status-agent.md` | 1 | StatusSystem, CareSystem, 스탯 |
| molly-visual-agent | `C:\DevelopRule\agents\molly-team\molly-visual-agent.md` | 1 | WobbleShader, Sprite Atlas, 파티클 |
| molly-core-agent | `C:\DevelopRule\agents\molly-team\molly-core-agent.md` | 2 | MollyCore, EvoTracker, 진화/감정 |
| molly-interaction-agent | `C:\DevelopRule\agents\molly-team\molly-interaction-agent.md` | 2 | InteractionSystem, 제스처 |
| minigame-architect | `C:\DevelopRule\agents\molly-team\minigame-architect.md` | 2 | MiniGameManager, IMiniGame |
| molly-economy-agent | `C:\DevelopRule\agents\molly-team\molly-economy-agent.md` | 2 | EconomySystem, RewardSystem, IAP/광고 |
| minigame-bubblepop | `C:\DevelopRule\agents\molly-team\minigame-bubblepop.md` | 3 | BubblePop 전체 |
| minigame-memorymatch | `C:\DevelopRule\agents\molly-team\minigame-memorymatch.md` | 3 | MemoryMatch 전체 |
| minigame-jellyslide | `C:\DevelopRule\agents\molly-team\minigame-jellyslide.md` | 3 | JellySliding 전체 |
| molly-ui-agent | `C:\DevelopRule\agents\molly-team\molly-ui-agent.md` | 3 | MainHomeUI, MiniGameUI, ShopUI, HUD |

## 워크플로우 개요

```
Phase 0: 컨텍스트 확인 (_workspace/ 존재 여부)
Phase 1: 준비 — SAD 문서 참조, _workspace/ 생성
Phase 2: Wave 1 병렬 — save / status / visual (기반 인터페이스 생성)
Phase 3: Wave 2 병렬 — core / interaction / minigame-arch / economy
Phase 4: Wave 3 병렬 — bubblepop / memorymatch / jellyslide / ui
Phase 5: 통합 검토 — 이벤트 버스 14개 연결, 네임스페이스 확인, 보고
```

## 공용 에이전트 (선택적 추가 호출)

| 에이전트 | 경로 | 용도 |
|---------|------|------|
| unity-architect | `C:\DevelopRule\agents\unity-team\architect.md` | 씬 구조, asmdef |
| unity-optimizer | `C:\DevelopRule\agents\unity-team\optimizer.md` | FPS/메모리 최적화 |
| documenter | `C:\DevelopRule\agents\public-agents\documenter.md` | 코드 문서화 |
| visualizer | `C:\DevelopRule\agents\public-agents\visualizer.md` | 아키텍처 다이어그램 |
| git-push | `C:\DevelopRule\agents\public-agents\git-push.md` | 커밋·푸시 |

## SAD 참조

원본 문서: `D:\ProjectFiles\Mobile_JellyMooly\docs\molly_SAD.pdf`
