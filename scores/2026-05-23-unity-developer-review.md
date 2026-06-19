# unity-developer 평가 기록

- 날짜: 2026-05-23
- 태스크: Jelly Molly MVP(미니게임 없는 버전) Unity C# 구현 — Data/Stat/Time/Jelly FSM/Interaction/Feeding/Dance/UI/GameManager (31 파일, 네임스페이스 JellyMolly)
- 코드 규칙 점수: A
- 최적화 점수: A
- 종합 점수: A

## 세부 평가 (jelly-code-reviewer + unity-code-reviewer)
- 데이터-로직 분리: A — 핵심 로직(Stat/Time/Feeding/Save) 순수 클래스, MonoBehaviour는 라이프사이클 필요 시에만(9개 모두 정당).
- 이벤트 디커플링: A — GameEvents 정적 허브, UI↔System 단방향, Reset() 제공(플레이모드 재진입 안전).
- 최적화/틱: A — decay 1초 코루틴 틱(매 프레임 X), FoodThrower 풀링, 오프라인 일괄 보정.
- 확정 설계 정합성: S — 게이팅 Hunger만(🔧TEMP 주석 명시), 춤 Hunger -8 보상없음, 미니게임 코드 0참조(주석만), Joy 회복 없음.

## 실제 배치 컴파일 검증 (2차)
- 실제 Unity 배치 컴파일(에디터 로그)에서 정적 분석이 놓친 네임스페이스 충돌 발견:
  `JellyMolly.Systems.Time` 네임스페이스가 `UnityEngine.Time` 식별자를 가려 동일 `Systems.*` 계층의
  DanceSystem/InteractionSystem/FoodThrower 에서 `Time.time`/`Time.deltaTime` → CS0234 컴파일 에러(10건).
- (수정완료) 3개 파일에 `using UTime = UnityEngine.Time;` 별칭 추가 후 `UTime.` 로 교체. 폴더-네임스페이스 일치 유지.
- 재컴파일 검증은 GUI 에디터의 프로젝트 점유로 배치 차단 → 보류(사용자 환경 안전상 에디터 강제 종료 안 함). 라이선스는 정상.
- 교훈: 도메인 네임스페이스에 UnityEngine 흔한 타입명(Time/Random/Object 등) 재사용 금지 — 향후 GameTime 등으로 명명 권장.

## 주요 지적 (개선 여지)
- (수정완료) activeInputHandler=1(New Input System 전용) 발견 → InteractionSystem 을 UnityEngine.InputSystem Pointer API 로 전환(레거시 Input 런타임 예외 회피).
- (경미) ActionBarView.Update 매 프레임 쿨타임 3회 폴링 → 이벤트/타이머 기반 갱신 권장.
- (경미) JellyVisual.ApplyMood 의 material.color 접근(머티리얼 인스턴스화) — 무드 변경 시에만 호출되어 빈도 낮으나 sharedMaterial/MPB 고려 가능.
- (후속) SO 인스턴스 에셋(GameBalanceConfig/FoodData/FoodDatabase) 및 Home 씬 구성은 에디터 작업 — CreateAssetMenu 만 제공, ASSUMPTION 으로 보고.
