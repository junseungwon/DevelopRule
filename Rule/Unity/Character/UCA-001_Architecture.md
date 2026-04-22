---
name: Unity 캐릭터 아키텍처 — 핵심 원칙
description: POCO vs MonoBehaviour 판단 기준 및 MVC+Feature 전체 구조 원칙
category: 규칙
priority: critical
---

# UCA-001: 핵심 아키텍처 원칙

## 규칙 정의

캐릭터 시스템은 **MVC + Feature** 구조로 설계한다.
Unity 엔진 종속은 View에만 허용하고, 나머지는 순수 C# (POCO)으로 작성한다.

---

## 판단의 단일 기준

> **"이 코드가 Unity 엔진 API 없이 동작할 수 있는가?"**
> - YES → 순수 C# (POCO)
> - NO → MonoBehaviour

---

## 구성 요소 역할

| 구성 요소 | 구현 방식 | 역할 |
|-----------|-----------|------|
| **Model** | POCO | 런타임 상태 + 규칙 + 이벤트 발행 |
| **View** | MonoBehaviour | Unity API 조작 (애니·UI·입력·물리) |
| **Feature** | POCO | Controller 책임을 기능별로 분할한 컴포넌트 |
| **Config** | ScriptableObject | 밸런싱 수치 등 설계 시점 데이터 |
| **Entry** | MonoBehaviour | 위 모두를 조립 (캐릭터당 1개) |
| **Context** | POCO | Feature들이 공유하는 참조 꾸러미 |

---

## 3대 목표

1. **테스트 가능성**: 로직을 Unity 없이 NUnit에서 검증할 수 있어야 한다.
2. **관심사의 분리**: 데이터 / 로직 / 표현 / 설정을 명확히 구분한다.
3. **재사용성**: 로직은 서버·다른 캐릭터·다른 씬에서도 그대로 쓸 수 있어야 한다.

---

## MVC 적용 범위

- **캐릭터 전체 = 하나의 MVC 단위**
- Feature 각각을 MVC로 쪼개지 않는다. (오버엔지니어링 금지)
- UI 스크린(메뉴·HUD)은 별도 MVC로 구성 가능

---

## MonoBehaviour로 둘 것 ✅

엔진에 본질적으로 종속된 기능.

- 물리 콜백 수신 (`OnCollisionEnter`, `OnTriggerEnter`, `FixedUpdate`)
- 렌더링·애니메이션 조작 (`Animator`, `SpriteRenderer`, `ParticleSystem`)
- 입력 수신 (`Input`, 새 Input System 콜백)
- `Transform` 매 프레임 조작
- `Coroutine` 사용 (UniTask 사용 시 POCO도 가능)
- 카메라, UI 컴포넌트 제어

## POCO로 빼야 할 것 ✅

엔진 없이 성립하는 로직.

- 스탯·데미지·확률 계산 공식
- 쿨다운·버프/디버프 타이머 관리
- 인벤토리 규칙·아이템 정렬/스택
- AI 의사결정 (어떤 행동을 할지)
- 경제 시스템·퀘스트 조건·업적
- 데이터 직렬화/파싱 (JSON·세이브)
- 상태 머신 전이 규칙

---

## 실무 비율 가이드

| 영역 | 비율 |
|------|------|
| POCO (Model + Feature + 유틸) | 60~70% |
| MonoBehaviour (View + Entry) | 20~30% |
| SO (Config) | ~10% |

---

## 새 기능 배치 결정 플로차트

```
1. 이 코드가 Unity API 없이 돌아가는가?
   ├─ NO  → View (MonoBehaviour)
   └─ YES → 2번

2. 런타임 상태/수치 데이터를 보관하는가?
   ├─ YES, 변하는 값   → Model (POCO)
   ├─ YES, 고정 설정값 → SO Config
   └─ NO, 계산/규칙만 → 3번

3. 캐릭터의 '행동'에 해당하는가?
   ├─ YES → Feature (POCO, ITickableFeature)
   └─ NO  → POCO Service/Calculator

4. 이 Feature가 다른 Feature의 동일 목적 변형인가?
   ├─ YES, 독점적 전환 → Strategy (Feature 내부)
   └─ NO               → 별개 Feature
```
