---
name: Unity 캐릭터 아키텍처 규칙 허브
description: 작업 유형으로 적용할 UCA 규칙 ID를 찾는 인덱스
category: 허브
priority: critical
---

# UCA-HUB-000: Unity 캐릭터 아키텍처 규칙 허브

**경로**: `./UCA-00X_*.md` (총 7개) · **사용법**: 작업 → ID 매칭 → 해당 파일 Read → 적용 → 보고에 `적용 규칙: [UCA-...]` 명시

## 작업 상황 → 규칙 ID

| 상황 | IDs |
|------|-----|
| 새 캐릭터 시스템 설계 / 클래스 배치 결정 | 001, 006 |
| MB인지 POCO인지 판단 | 001 |
| Model 클래스 작성 | 002 |
| View(MonoBehaviour) 작성 | 003 |
| Feature(기능 모듈) 작성 | 004 |
| Strategy 패턴 도입 여부 결정 | 004 |
| ScriptableObject 작성 | 005 |
| Entry(Composition Root) 작성 | 006 |
| Installer / EventBus 도입 여부 결정 | 006 |
| Feature 간 통신 설계 | 006 |
| 외부 시스템(HUD·Save·SFX)에 이벤트 노출 | 006 |
| 네이밍 / 폴더 / 어셈블리 결정 | 007 |
| PR 체크리스트 확인 | 007 |
| 안티패턴 점검 | 007 |

## 규칙 ID 색인

```
001 ARCHITECTURE   002 MODEL   003 VIEW    004 FEATURE
005 CONFIG         006 COMPOSITION         007 CONVENTION
```

> 신규 규칙 추가 시 본 허브의 **상황 표** · **ID 색인** 두 곳 동시 갱신.
