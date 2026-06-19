# jelly-feature 실수 기록

## 자주 하는 실수
- Animator 파라미터 접근 시 문자열 리터럴 직접 사용 — Animator.StringToHash로 캐싱해야 함 (GAMEOBJ-013, PROFILE-023)
- MonoBehaviour 라이프사이클 본문(Awake/Start/Update)에 로직·다중 호출 직접 기재 — 1줄 위임 원칙 위반 반복 (UNITY-021)
- Inspector 직결 필드에 [SerializeField] 누락으로 Instantiate 폴백 패턴 사용 — 자동 패씽 위반 (UNITY-020)

## 최근 지적 사항 (최신순)
| 날짜 | 항목 | 등급 |
|------|------|------|
| 2026-05-20 | WallLean.Update() IsGrounded 가드 추가. fallback 패턴 2블록 중복(DRY 권장). 필수 위반 없음. 코드규칙 A / 최적화 S | A |
| 2026-05-14 | Player/Base 브랜치 5파일 점검. PlayerManager Awake/Start 1줄 위임 위반, FootprintDetector Update 1줄 위임 위반, _footprintPoolRig [SerializeField] 누락+Instantiate 폴백, FindObjectsByType 5회. 코드규칙 C / 최적화 B | C |
| 2026-05-14 | FootprintDetector/FootprintFeature에서 "fSpeed" 문자열 리터럴 직접 사용 — static readonly int FSpeedHash 캐싱 권장. FootprintDetector.Init() Awake/Start 혼재. Update() 직접 제어문 포함(단일 위임 권장) | A |
