# unity-developer 평가 기록 (UI Toolkit 전환)

- 날짜: 2026-05-23
- 태스크: 메인 홈 UI를 uGUI → UI Toolkit(UXML/USS/UIDocument)로 전면 교체, HTML 데모 룩 재현
- 코드 규칙 점수: A
- 최적화 점수: A
- 종합 점수: A

## 세부 평가 (jelly-code-reviewer + unity-code-reviewer)
- 구조/통합: A — 기존 uGUI UIManager+ActionBarView+StatHeartView 3클래스를 HomeUIController 단일 컨트롤러로 통합. 이벤트 디커플링(GameEvents 구독) 유지.
- 라이프사이클: A — OnEnable/OnDisable 구독 대칭, Subscribe/Unsubscribe·UpdateCooldowns/Blink/Fades·On*Clicked(5) 본문 로직 함수 분리. RequireComponent(UIDocument).
- 정합성: A — FeedingSystem.TryFeed/GetRemainingCooldown, DanceSystem.TryDance/GetRemainingCooldown, GameEvents.Raise* 시그니처 정확. UXML name↔컨트롤러 Q 매핑 21/21 100% 일치.
- 재현도: A — HTML 데모 룩(파스텔, 3스탯 하트+게이지+%, 구간색 70+/30~69/0~29, 위험 깜빡임, 5버튼 액션바, 토스트/말풍선, CLEAR TOYS/LEVEL) USS 재현. root 투명으로 3D Molly 비침.
- EmitInitial 타이밍: GameManager에서 Awake(Init)→Start(EmitInitial)로 분리, 컨트롤러 OnEnable 구독 후 초기값 반영.

## GUID/fileID 자체검증
- HomeUIController.cs.meta(a1b2c3..)↔씬 m_Script 일치, HomeView.uxml.meta(b2c3d4..)↔씬 sourceAsset 일치.
- 씬 참조 프로젝트 GUID 전수 OK(GameManager/Jelly*/Systems/SO/Mat/uxml/controller). 깨진 참조(2003005/2005002) 0건, Canvas 트리 완전 제거.
- 미발견 GUID 5건은 전부 Unity 내장/패키지(EventSystem/URP/InputModule/내장리소스/UIDocument).

## 리스크 / 미검증 (개선·후속)
- UIDocument 내장 m_Script GUID(e72d6720..) 손작성 — Unity 6 표준값이나 미검증. 틀리면 Missing Script(빌드툴 메뉴가 UIDocument 재추가로 교정).
- PanelSettings.asset/JellyTheme.tss 미생성 → 빌드툴/에디터 메뉴 실행 필요(미연결 시 UI 미표시).
- 실제 컴파일/Play 검증: GUI 에디터 프로젝트 점유로 배치 차단 → 미수행(에디터 강제종료 안 함, 라이선스 정상). 에디터 로그상 최근 error CS 0건이나 새 에셋 미임포트 상태.
- 잔존 uGUI 스크립트(UIManager/ActionBarView/StatHeartView) 미삭제 — 씬 미참조라 무해, 정리는 사용자 판단.
